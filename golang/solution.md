# 岗前培训	

## slice和map

### Slice的实现

**难点**

> * 如何支持泛型 （interface{} + reflect）
> * 如何分配一块连续的内存（reflect.ArrayOf  + reflect.New）
> * 如何去访问reflect.Value值（Go语言圣经 第十二章）
> * slice的扩容（参考标准库）

```go
package slice

import (
	"reflect"
	"unsafe"
)

type T interface{}

type MySlice struct {
	array unsafe.Pointer
	len   int
	cap   int
}

func IMake(et T, params ...int) MySlice {
	if len(params) > 2 || len(params) < 1 {
		panic("Params is not enough!")
	}

	if len(params) == 1 {
		if params[0] <= 0 {
			panic("error input param: len and cap <= 0")
		}

		typ := reflect.ArrayOf(params[0], reflect.TypeOf(et).Elem())
		value := reflect.New(typ)

		return MySlice{unsafe.Pointer(&value), params[0], params[0]}
	} else {
		if params[0] > params[1] && params[2] > 0 {
			panic("error input param: len > cap or cap <= 0")
		}

		tp := reflect.ArrayOf(params[1], reflect.TypeOf(et).Elem())
		v := reflect.New(tp)

		//fmt.Println(v.Type(),v.Kind())  // v.kind()为reflect.Ptr, v.Type()为*[8]int

		return MySlice{unsafe.Pointer(&v), params[0], params[1]}
	}
}

func ILen(slice MySlice) int {
	return slice.len
}

func ICap(slice MySlice) int {
	return slice.cap
}

func IAppend(slice MySlice, elems ...T) MySlice {
	var sli = slice

	if sli.len+len(elems) > slice.cap {
		sli = growSlice(slice, len(elems))
	}

	v := *(*reflect.Value)(sli.array)
	typ := v.Type().Elem().Elem()
	arr := v.Elem()

	//fmt.Println(v.Type(),v.Kind())		//*[8]int   reflect.Ptr
	//fmt.Println(v.Type().Elem(), typ) 	//[8]int	int
	//fmt.Println(arr.Type())   			//[8]int

	len := sli.len

	for _, value := range elems {
		if reflect.TypeOf(value) == typ {
			arr.Index(len).Set(reflect.ValueOf(value))
			len++
		} else {
			panic("type is incompatible!")
		}
	}

	return MySlice{sli.array, len, sli.cap}
}

//len表示IAppend参数elems的长度
func growSlice(old MySlice, len int) MySlice {
	newCap := old.cap
	doubleCap := 2 * old.cap

	if old.cap+len > doubleCap {
		newCap = old.cap + len
	} else {
		if old.len < 1024 {
			newCap = doubleCap
		} else {
			for 0 < newCap && newCap < old.len+len {
				newCap += newCap / 4
			}

			//newCap溢出
			if newCap <= 0 {
				newCap = old.len + len
			}
		}
	}

	value := *(*reflect.Value)(old.array)
	arrTyp := value.Type().Elem()
	//fmt.Println(arrTyp)    //[8]int

	tp := reflect.ArrayOf(newCap, arrTyp.Elem())
	v := reflect.New(tp)

	return MySlice{unsafe.Pointer(&v), old.len, newCap}
}
```



### map并发安全问题

go语言官方博客中（https://blog.golang.org/go-maps-in-action）有说明：

**Maps are not safe for concurrent use:** it's not defined what happens when you read and write to them simultaneously. If you need to read from and write to a map from concurrently executing goroutines, the accesses must be mediated by some kind of synchronization mechanism. One common way to protect maps is with sync.RWMutex.

**map类型不是并发安全的：**它没有定义当你同时读取和写入map时发生的情况，如果你需要在并发执行的goroutine中读取和写入map，那么访问时必须通过某种同步机制来调解。读写锁（sync.RWMutex）是用来保护map的一种常用方法。

因此，对于需要在并发执行的程序中使用map时，需要通过同步机制对map的访问进行控制。通常情况下会通过将Sync.RWMutex和map封装在一起，实现一个并发安全的结构。

```go
package safemap

import "sync"

type SafeMap struct {
	sync.RWMutex
	mp map[interface{}]interface{}
}

func Initial() *SafeMap {
	newMap := new(SafeMap)
	newMap.mp = make(map[interface{}]interface{})
	return newMap
}

func (smp *SafeMap) Read(key interface{}) interface{} {
	smp.RLock()
	value := smp.mp[key]
	smp.RUnlock()

	return value
}

func (smp *SafeMap) Write(key, value interface{}) {
	smp.Lock()
	smp.mp[key] = value
	smp.Unlock()
}

//Can not use delete and write operation by callback.
func (smp *SafeMap) Range(callback func(key, value interface{}) bool) {
	smp.RLock()
	for k, v := range smp.mp {
		if !callback(k, v) {
			break
		}
	}
	smp.RUnlock()
}
```

除此之外，Go语言也提供了并发安全的sync.Map，详细信息查看https://golang.org/pkg/sync/#Map。



## 海量数据处理问题

**题目：**

这里有2018年广东省75.8万高考考生的成绩（成绩存放在一个文件中，文件格式见score.txt），请提供接口使我能查到考666分的有哪些人；能查到准考证号为20180601526考生当前是多少名？（前提条件，内存空间容纳不下75.8万考生的数据，磁盘空间充足。）

**主要思路：**

题目中数据量大，内存少不够用

通过对海量数据分块处理，即对大文件进行分割，保证分割后的小文件能够放在内存中处理，针对每一小块进行处理，然后统计每一个小文件的处理结果。

**需要注意的问题：**

> * 文件分割时，要保证每个小文件的内容能够完全放在内存中处理，分割算法尽量有利于后面的操作
> * 选择合适的数据结构和算法，使效率尽可能的高

遇到问题主要有：

> * 大文件分块存储时的效率问题
> * 查找数据结构的选择
> * workerpool的使用
> * 数据量不断增长时，HASHSIZE的取值问题
> * 随着HASHZIE的变化，文件个数太多时，如何去分配worker数量（分割文件时和数据处理时）
> * sorter包是如何选择排序算法的
> * 对大文件排序—小文件内部先排序，然后通过归并排序整合为大文件

### 文件分割

文件分割时，要保证每个小文件的内容能够完全放在内存中处理，分割算法尽量有利于后续的操作。按照学号hash的算法来分割文件则是一个很好的选择，学号不会重复，能够保证每个文件的数据量很均匀，而且可以很方便的按照学号查找某个人的成绩。

**文件分块一**

直接读入大文件，然后通过对学号的hash来实现分块，对于758000条数据，耗时5s左右。

```GO
func Split(fileName string) bool {
	var files [HASHSIZE]*os.File
	for i := 0; i < HASHSIZE; i++ {
		files[i]= Openfile(OUTPUTDIR + strconv.Itoa(i) + ".txt")
	}

	inputFile, err := os.Open(fileName)
	if err != nil {
		fmt.Println("open file error : ", err.Error())
		return false
	}
	defer inputFile.Close()

	removeAllFile(OUTPUTDIR)
	reader := bufio.NewReader(inputFile)
	_, err = reader.ReadString('\n')
	if err != nil {
		return false
	}

	for {
		str, err := reader.ReadString('\n')
		if err != nil {
			break
		}

		s := strings.Fields(str)
		addr := Hash(s[0]) % HASHSIZE

		outputFile := files[addr]

		writer := bufio.NewWriter(outputFile)
		writer.WriteString(str)
		writer.Flush()
	}

	for i := 0; i < HASHSIZE; i++ {
		files[i].Close()
	}

	return true
}
```

**文件分块二**

将从大文件中的数据读取出来先放入带缓冲的chan，然后创建worker池去处理，开100个goroutine，同样的数据量耗时2.5s左右。

```go
package fileop

import (
	"bufio"
	"fmt"
	"os"
	"strings"
	"sync"
	"strconv"
)

var files [HASHSIZE]*os.File
var data = make(chan string, 2048)

const (
	HASHSIZE  = 53
	POOLSIZE  = 100
	OUTPUTDIR = "E:\\CodeBase\\Solution\\data\\"
)

func Split(fileName string) bool {
	removeAllFile(OUTPUTDIR)
	for i := 0; i < HASHSIZE; i++ {
		outputFile := Openfile(OUTPUTDIR + strconv.Itoa(i) + ".txt")
		files[i] = outputFile
	}

	go readFromFile(fileName)
	createWorkerPool(POOLSIZE)

	for i := 0; i < HASHSIZE; i++ {
		files[i].Close()
	}

	return true
}

func readFromFile(fileName string) {
	inputFile, err := os.Open(fileName)
	if err != nil {
		fmt.Println("open file error : ", err.Error())
		return
	}

	defer inputFile.Close()

	reader := bufio.NewReader(inputFile)
	_, err = reader.ReadString('\n')
	if err != nil {
		return
	}

	for {
		str, err := reader.ReadString('\n')
		if err != nil {
			break
		}

		data <- str
	}
	close(data)
}

func worker(wg *sync.WaitGroup) {
	for str := range data {
		handler(str)
	}
	wg.Done()
}

func createWorkerPool(size int) {
	var wg sync.WaitGroup

	for i := 0; i < size; i++ {
		wg.Add(1)
		go worker(&wg)
	}

	wg.Wait()
}

func handler(str string) {
	s := strings.Fields(str)

	addr := Hash(s[0]) % HASHSIZE
	outputFile := files[addr]

	writer := bufio.NewWriter(outputFile)
	writer.WriteString(str)
	writer.Flush()
}
```

**文件分块三**

采用工作池的方式实现，实现以缓冲的方式写入文件，同样的数据量时间缩减至1秒内。

```go
func Split(fileName string) bool {
   hashSize = getHashSize(fileName)
   removeAllFile(OUTPUTDIR)

   files := make([]*os.File, 0, hashSize)
   writers := make([]*bufio.Writer, 0, hashSize)

   for i := 0; i < hashSize; i++ {
      outputFile := Openfile(OUTPUTDIR + strconv.Itoa(i) + ".txt")
      files = append(files, outputFile)
   }

   for i := 0; i < len(files); i++ {
      writers = append(writers, bufio.NewWriter(files[i]))
   }

   pool := workers.New(POOLSIZE)

   inputFile, err := os.Open(fileName)
   if err != nil {
      fmt.Println("open file error : ", err.Error())
      return false
   }

   defer inputFile.Close()

   reader := bufio.NewReader(inputFile)

   //去除第一行
   _, err = reader.ReadString('\n')
   if err != nil {
      return false
   }

   var mutex sync.Mutex

   //循环按行读取内容，然后交给workerpool去处理
   for {
      str, err := reader.ReadString('\n')
      if err != nil {
         break
      }

      pool.Run(workers.Job{
         Data: str,
         Proc: func(data interface{}) {
            if str, ok := data.(string); ok {
               s := strings.Fields(str)

               addr := hash(s[0]) % hashSize
               writer := writers[addr]
               mutex.Lock()
               writer.WriteString(str)
               if writer.Buffered() >= BUFFERSIZE {
                  writer.Flush()
               }
               mutex.Unlock()
            }
         },
      })
   }

   pool.Shutdown()

   //将缓冲区剩余的内容写入文件
   for i := 0; i < len(writers); i++ {
      writers[i].Flush()
   }

   for i := 0; i < len(files); i++ {
      files[i].Close()
   }

   return true
}
```

### 按成绩查找记录

**两种方法：**

（1）从这些数据来看，所有的成绩均为整型，将大文件按照不同的成绩写入不同的文件，生成的文件多，而且每个文件的数据量不均匀，但是对于查找某个成绩，可以快速的定位到某个文件，但是文件分割比较费时，考虑到其他需求，因此需要采用一种更合理的方法，使得所有需求在实现上都能有较高的效率。

（2）对文件进行分割，使得每个文件的数据都能一次性加载到内存，然后采用并发的方式在每个文件中遍历生成hashmap的结构，然后再查找，做相应的处理。

```go
func FindScoresByGrade(grade int) int {
   files := fileop.GetAllFile(fileop.OUTPUTDIR)
   result := fileop.Openfile(RESULT_DIR + strconv.Itoa(grade) + ".txt")
   writer := bufio.NewWriter(result)

   defer result.Close()

   var stuCount, size int
   if len(files) > POOLSIZE {
      size = POOLSIZE
   } else {
      size = len(files)
   }

   //开启WorkerPool
   pool := workers.New(size)
   var mutex sync.Mutex

   for _, info := range files {
      pool.Run(workers.Job{
         Data: info,
         Proc: func(data interface{}) {
            if file, ok := data.(os.FileInfo); ok {
               scoreMap := fileop.GetScoreMap(fileop.OUTPUTDIR + file.Name())

               for _, v := range (*scoreMap)[grade] {
                  mutex.Lock()
                  writer.WriteString(v.ToString())
                  if writer.Buffered() >= BUFFERSIZE {
                     writer.Flush()
                  }
                  mutex.Unlock()
               }

               mutex.Lock()
               writer.Flush()
               stuCount += len((*scoreMap)[grade]) //统计所有文件中该成绩的人数
               mutex.Unlock()
            }
         },
      })
   }
   pool.Shutdown() //关闭通道，等待goroutine全部执行完

   return stuCount
}
```

具体实现流程图如下：

```flow
st=>start: 开始
op1=>operation: 获取所有文件信息并打开
op3=>operation: 建立工作池
op4=>operation: 遍历文件生成map再查找
cond1=>condition: 是否存在与grade相等的记录
cond2=>condition: 所有文件是否遍历完
op5=>operation: 写入文件并统计个数
io=>inputoutput: 输入成绩grade
io1=>inputoutput: 输出统计的结果
e=>end: 结束

st(right)->io->op1->op3->op4->cond1
cond1(yes)(right)->op5(right)->cond2
cond1(no)->cond2
cond2(yes)->io1->e
cond2(no)->op4
```

### 根据学号获取排名

​	获取排名就需要对文件内容进行排序，一般采用先对多个小文件进行排序，然后再进行归并排序将所有小文件合并，然后再遍历求排名。

​	但是对于成绩而言，总成绩大小不会变，我们可以通过对每个成绩进行统计，统计每个成绩有多少人，并保存下来，然后求某个人的排名时，可以先得到他的成绩，并获取到所有与他成绩相同的人并按照学号排序得到他在这些人里的排名，最后他在所有人里面的排名 = 比他成绩高的所有人的人数 + 他在与他成绩相同的人里面的排名。这样的方法虽然可行，但是需要考虑内存大小，与他成绩相同的人数太多的话，无法一次加载到内存进行排序。

​	综上所述，求某个人的排名时，我们可以先对与他的成绩相同的人数进行统计，如果内存可以容纳，则采用第二种方法，如果内存不足，则采用第一种方法。

```go
func GetRankingByStats(stuId string) int {
   var rank = 0

   grade := FindGradeByStuId(stuId)
   if grade != -1 {
      counts := doStats()

      if counts[grade] > fileop.FILESIZE {
         return GetRankingByMerge(stuId)
      }

      //成绩相同的排序再查找
      scores := GetScoresByGrade(grade)
      sort.Sort(scores)

      index := sort.Search(len(scores), func(i int) bool {
         return strings.Compare(scores[i].StuID, stuId) >= 0 && grade == scores[i].Grade
      })

      for i := grade + 1; i <= MAXGRADE; i++ {
         rank += counts[i]
      }
      //fmt.Println(index, " ", scores[index])
      rank += index + 1
      return rank
   } else {
      fmt.Println("please input right StuID!")
      return 0
   }
}
```

整体处理流程图如下：

```flow
st=>start: 开始
io1=>inputoutput: 输入学号
op1=>operation: 获取对应的成绩grade
cond1=>condition: grade == -1
op2=>operation: 统计所有成绩对应的人数counts
io2=>inputoutput: 输出排名
op3=>operation: 获取成绩与grade相同的所有记录
op4=>operation: 按照学号局部排序得到他的排名
op5=>operation: 统计所有比他成绩高的人数并得
sub=>subroutine: 通过归并排序获取排名
cond2=>condition: counts[grade] > 内存能容纳的大小

e=>end: 结束

st->io1->op1->cond1
cond1(yes)->op2->cond2
cond1(no)->io2->e
cond2(yes)->op3->op4->op5(right)->io2
cond2(no)->sub->io2
```

通过对小文件排序和n路归并排序生成排好序的文件，然后遍历文件获取排名。

```go
func GetRankingByMerge(stuId string) int {
   Sort()  //对每个小文件进行排序
   Merge() //对小文件进行n路归并

   inputFile, err := os.Open(RESULT_FILE)
   if err != nil {
      fmt.Println("open file error : ", err.Error())
      return 0
   }

   defer inputFile.Close()

   reader := bufio.NewReader(inputFile)
   var count = 0

   for {
      input, err := reader.ReadString('\n')
      if err != nil {
         break
      }

      count++

      str := strings.Trim(input, "\n")
      s := strings.Fields(str)
      if strings.Compare(stuId, s[0]) == 0 {
         break
      }
   }

   return count
}
```

排序算法的实现如下：

```go
//对每个小文件排序
func Sort() {
   files := fileop.GetAllFile(fileop.OUTPUTDIR)

   var size int
   if len(files) > POOLSIZE {
      size = POOLSIZE
   } else {
      size = len(files)
   }

   //开启WorkerPool
   pool := workers.New(size)
   for _, info := range files {
      pool.Run(workers.Job{
         Data: info,
         Proc: func(data interface{}) {
            if info, ok := data.(os.FileInfo); ok {
               scores := fileop.GetScores(fileop.OUTPUTDIR + info.Name())
               sort.Sort(scores)

               inputFile, err := os.OpenFile(fileop.OUTPUTDIR+info.Name(), os.O_WRONLY, 0600)
               if err != nil {
                  fmt.Println("open file error !")
                  return
               }

               defer inputFile.Close()

               writer := bufio.NewWriter(inputFile)

               for _, v := range scores {
                  writer.WriteString(v.ToString())
                  if writer.Buffered() > BUFFERSIZE {
                     writer.Flush()
                  }
               }
               writer.Flush()
            }
         },
      })
   }
   pool.Shutdown() //关闭通道，等待goroutine全部执行完
}
```

```
func Merge() {
   files := fileop.GetAllFile(fileop.OUTPUTDIR)
   result := fileop.Openfile(RESULT_FILE)
   writer := bufio.NewWriter(result)

   defer result.Close()

   fileList := make([]*os.File, 0, len(files))
   for _, info := range files {
      file, err := os.Open(fileop.OUTPUTDIR + info.Name())
      if err != nil {
         fmt.Println("open file error!")
         return
      }

      fileList = append(fileList, file)
   }

   readerList := make([]*bufio.Reader, 0, len(files))
   for _, f := range fileList {
      readerList = append(readerList, bufio.NewReader(f))
   }

   records := make(defs.Records, 0, len(fileList))
   //先从已排序好文件中取出第一条记录
   for i := 0; i < len(fileList); i++ {
      str, err := readerList[i].ReadString('\n')
      if err != nil {
         continue
      }

      s := strings.Fields(str)
      grade, _ := strconv.Atoi(s[1])
      score := defs.Score{
         StuID: s[0],
         Grade: grade,
      }

      records = append(records, defs.Record{Score: score, FileIndex: i})
   }

   //然后建堆，每次取出堆定元素，再调整
   heap.Init(&records)
   for len(records) != 0 {
      record := heap.Pop(&records) //取出堆顶元素

      if rec, ok := record.(defs.Record); ok {
         writer.WriteString(rec.Score.ToString())
         if writer.Buffered() >= BUFFERSIZE {
            writer.Flush()
         }

         //从该记录所在的文件中再取下一条记录
         input, err := readerList[rec.FileIndex].ReadString('\n')
         if err != nil {
            continue
         }
         str := strings.Trim(input, "\n")

         s := strings.Fields(str)
         grade, _ := strconv.Atoi(s[1])
         score := defs.Score{
            StuID: s[0],
            Grade: grade,
         }

         heap.Push(&records, defs.Record{Score: score, FileIndex: rec.FileIndex})
      }
   }

   writer.Flush()

   for _, f := range fileList {
      f.Close()
   }
}
```

**遗留问题**

> * n路归并带来的问题（随着n的增长可能会导致 a. 效率问题 b. 内存不足）

