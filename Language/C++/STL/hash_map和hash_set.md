# hash_map

```C++
template <class Value, class Key, class HashFcn,
          class ExtractKey, class EqualKey,
          class Alloc>
class hash_map
{
private:
  typedef hashtable<pair<const Key, T>, Key, HashFcn,
                    select1st<pair<const Key, T> >, EqualKey, Alloc> ht;
  ht rep;

public:
  typedef typename ht::key_type key_type;
  typedef T data_type;
  typedef T mapped_type;
  typedef typename ht::value_type value_type;
  typedef typename ht::hasher hasher;
  typedef typename ht::key_equal key_equal;

  typedef typename ht::size_type size_type;
  typedef typename ht::difference_type difference_type;
  typedef typename ht::pointer pointer;
  typedef typename ht::const_pointer const_pointer;
  typedef typename ht::reference reference;
  typedef typename ht::const_reference const_reference;

  typedef typename ht::iterator iterator;
  typedef typename ht::const_iterator const_iterator;

  hasher hash_funct() const { return rep.hash_funct(); }
  key_equal key_eq() const { return rep.key_eq(); }

public:
  hash_map() : rep(100, hasher(), key_equal()) {}
  explicit hash_map(size_type n) : rep(n, hasher(), key_equal()) {}
  hash_map(size_type n, const hasher& hf) : rep(n, hf, key_equal()) {}
  hash_map(size_type n, const hasher& hf, const key_equal& eql)
    : rep(n, hf, eql) {}

#ifdef __STL_MEMBER_TEMPLATES
  template <class InputIterator>
  hash_map(InputIterator f, InputIterator l)
    : rep(100, hasher(), key_equal()) { rep.insert_unique(f, l); }
  template <class InputIterator>
  hash_map(InputIterator f, InputIterator l, size_type n)
    : rep(n, hasher(), key_equal()) { rep.insert_unique(f, l); }
  template <class InputIterator>
  hash_map(InputIterator f, InputIterator l, size_type n,
           const hasher& hf)
    : rep(n, hf, key_equal()) { rep.insert_unique(f, l); }
  template <class InputIterator>
  hash_map(InputIterator f, InputIterator l, size_type n,
           const hasher& hf, const key_equal& eql)
    : rep(n, hf, eql) { rep.insert_unique(f, l); }

#else
  hash_map(const value_type* f, const value_type* l)
    : rep(100, hasher(), key_equal()) { rep.insert_unique(f, l); }
  hash_map(const value_type* f, const value_type* l, size_type n)
    : rep(n, hasher(), key_equal()) { rep.insert_unique(f, l); }
  hash_map(const value_type* f, const value_type* l, size_type n,
           const hasher& hf)
    : rep(n, hf, key_equal()) { rep.insert_unique(f, l); }
  hash_map(const value_type* f, const value_type* l, size_type n,
           const hasher& hf, const key_equal& eql)
    : rep(n, hf, eql) { rep.insert_unique(f, l); }

  hash_map(const_iterator f, const_iterator l)
    : rep(100, hasher(), key_equal()) { rep.insert_unique(f, l); }
  hash_map(const_iterator f, const_iterator l, size_type n)
    : rep(n, hasher(), key_equal()) { rep.insert_unique(f, l); }
  hash_map(const_iterator f, const_iterator l, size_type n,
           const hasher& hf)
    : rep(n, hf, key_equal()) { rep.insert_unique(f, l); }
  hash_map(const_iterator f, const_iterator l, size_type n,
           const hasher& hf, const key_equal& eql)
    : rep(n, hf, eql) { rep.insert_unique(f, l); }
#endif /*__STL_MEMBER_TEMPLATES */

public:
  size_type size() const { return rep.size(); }
  size_type max_size() const { return rep.max_size(); }
  bool empty() const { return rep.empty(); }
  void swap(hash_map& hs) { rep.swap(hs.rep); }
  friend bool
  operator== __STL_NULL_TMPL_ARGS (const hash_map&, const hash_map&);

  iterator begin() { return rep.begin(); }
  iterator end() { return rep.end(); }
  const_iterator begin() const { return rep.begin(); }
  const_iterator end() const { return rep.end(); }

public:
  pair<iterator, bool> insert(const value_type& obj)
    { return rep.insert_unique(obj); }
#ifdef __STL_MEMBER_TEMPLATES
  template <class InputIterator>
  void insert(InputIterator f, InputIterator l) { rep.insert_unique(f,l); }
#else
  void insert(const value_type* f, const value_type* l) {
    rep.insert_unique(f,l);
  }
  void insert(const_iterator f, const_iterator l) { rep.insert_unique(f, l); }
#endif /*__STL_MEMBER_TEMPLATES */
  pair<iterator, bool> insert_noresize(const value_type& obj)
    { return rep.insert_unique_noresize(obj); }    

  iterator find(const key_type& key) { return rep.find(key); }
  const_iterator find(const key_type& key) const { return rep.find(key); }

  T& operator[](const key_type& key) {
    return rep.find_or_insert(value_type(key, T())).second;
  }

  size_type count(const key_type& key) const { return rep.count(key); }
  
  pair<iterator, iterator> equal_range(const key_type& key)
    { return rep.equal_range(key); }
  pair<const_iterator, const_iterator> equal_range(const key_type& key) const
    { return rep.equal_range(key); }

  size_type erase(const key_type& key) {return rep.erase(key); }
  void erase(iterator it) { rep.erase(it); }
  void erase(iterator f, iterator l) { rep.erase(f, l); }
  void clear() { rep.clear(); }

public:
  void resize(size_type hint) { rep.resize(hint); }
  size_type bucket_count() const { return rep.bucket_count(); }
  size_type max_bucket_count() const { return rep.max_bucket_count(); }
  size_type elems_in_bucket(size_type n) const
    { return rep.elems_in_bucket(n); }
};
```

## hash_map简介

hash_map不属于C++ STL标准库，它是SGI额外提供的一个以hashtable为底层实现结构的容器。hashtable提供了大量接口，因此几乎所有hash_map的接口都是掉用hashtable的操作。hash_multimap的底层也是通过hashtable来实现的，具体的实现下面来讲。

hash_map 和 map 两者不论底层是 hashtable 还是 RB-tree，都能够根据键值快速查找元素，但是 RB-tree有自动排序功能，而 hashtable 没有。 还有一点需要注意，那就是 hashtable 存储的类型应该具有 hash function，因此某些情况在使用hashtable 时，我们需要为存储的类型实现 hash function。

## hashtable的剖析

在SGI STL中，hashtable的实现：

> - 使用vector管理桶结点；
> - 使用除留余数法作为哈希函数；
> - 采用链地址法解决哈希冲突;
> - 在SGI hashtable实现过程中，hashtable的长度就是用来通过除留余数法计算地址的被除数。

![](../../../Algorithm/images/散列表/hashtable.png)

```C++
template <class Value>
struct __hashtable_node
{
  __hashtable_node* next;
  Value val;
}; 
...
...
template <class Value, class Key, class HashFcn,
          class ExtractKey, class EqualKey,
          class Alloc>
class hashtable {
public:
  typedef Key key_type;
  typedef Value value_type;
  typedef HashFcn hasher;
  typedef EqualKey key_equal;

  typedef size_t            size_type;
  typedef ptrdiff_t         difference_type;
  typedef value_type*       pointer;
  typedef const value_type* const_pointer;
  typedef value_type&       reference;
  typedef const value_type& const_reference;

  hasher hash_funct() const { return hash; }
  key_equal key_eq() const { return equals; }

private:
  hasher hash;			//求hashcode的函数
  key_equal equals;		//用来判断key是否相等
  ExtractKey get_key;	//从value中提取key值

  typedef __hashtable_node<Value> node;
  typedef simple_alloc<node, Alloc> node_allocator;

  vector<node*,Alloc> buckets;
  size_type num_elements;  //元素个数
  ...
  ...
};
```



### **hashtable插入算法**

inset_unique()和insert_equal()用于不同的场景，比如hash_map和hash_multimap，使用insert_unique()函数插入value时，key时独一无二的。而使用insert_euqal()时key可以相同。

```c++
pair<iterator, bool> insert_unique(const value_type& obj)
{
	resize(num_elements + 1);
    return insert_unique_noresize(obj);
}

iterator insert_equal(const value_type& obj)
{
    resize(num_elements + 1);
	return insert_equal_noresize(obj);
}
```

```c++
template <class V, class K, class HF, class Ex, class Eq, class A>
pair<typename hashtable<V, K, HF, Ex, Eq, A>::iterator, bool> 
hashtable<V, K, HF, Ex, Eq, A>::insert_unique_noresize(const value_type& obj)
{
  const size_type n = bkt_num(obj);
  node* first = buckets[n];

  for (node* cur = first; cur; cur = cur->next) 
    if (equals(get_key(cur->val), get_key(obj))) //两个key相等直接退出
      return pair<iterator, bool>(iterator(cur, this), false);

  //插入元素，采用头插的方式
  node* tmp = new_node(obj);
  tmp->next = first;
  buckets[n] = tmp;
  ++num_elements;
  return pair<iterator, bool>(iterator(tmp, this), true);
}
```

```C++
template <class V, class K, class HF, class Ex, class Eq, class A>
typename hashtable<V, K, HF, Ex, Eq, A>::iterator 
hashtable<V, K, HF, Ex, Eq, A>::insert_equal_noresize(const value_type& obj)
{
  const size_type n = bkt_num(obj);
  node* first = buckets[n];

  for (node* cur = first; cur; cur = cur->next) 
    if (equals(get_key(cur->val), get_key(obj))) {
      //元素key值已存在，则将新的元素插入至第一个key相等的元素的后面
      node* tmp = new_node(obj);
      tmp->next = cur->next;
      cur->next = tmp;
      ++num_elements;
      return iterator(tmp, this);
    }
  //元素还未存在，采用头插的方式插入元素
  node* tmp = new_node(obj);
  tmp->next = first;
  buckets[n] = tmp;
  ++num_elements;
  return iterator(tmp, this);
}
```

### **hashtable的resize算法**

关于散列表容量选取问题，SGI STL中hashtable内置了一张素数表，为了随着hashtable的所有元素个数的增加来调整桶的数量，桶的数量就是根据这张表确定的，目的是在元素个数到达一定程度时，通过增加桶的数量使得元素分布更加均匀（为什么这样就会均匀分布？）。

```c++
// Note: assumes long is at least 32 bits.
static const int __stl_num_primes = 28;
static const unsigned long __stl_prime_list[__stl_num_primes] =
{
  53,         97,           193,         389,       769,
  1543,       3079,         6151,        12289,     24593,
  49157,      98317,        196613,      393241,    786433,
  1572869,    3145739,      6291469,     12582917,  25165843,
  50331653,   100663319,    201326611,   402653189, 805306457, 
  1610612741, 3221225473ul, 4294967291ul
};

inline unsigned long __stl_next_prime(unsigned long n)
{
  const unsigned long* first = __stl_prime_list;
  const unsigned long* last = __stl_prime_list + __stl_num_primes;
  const unsigned long* pos = lower_bound(first, last, n);
  return pos == last ? *(last - 1) : *pos;
}
```

resize()用来处理hashtable元素个数到达一定程度时增加桶的数量并且调整元素的分布。num_elements表示当前hashtable中的元素个数，buckets.size()表示用来管理桶结点的vector的容量，即桶的个数。当num_elements+1 > buckets.size()时，会调用上面__stl_next_prime()方法在素数表中取合适的素数，更新桶的数目。

```flow
st=>start: start
op1=>operation: insert element
cond=>condition: num_elements+1 
> buckets.size()
op2=>operation: get next prime number
op3=>operation: resize and adjust
io=>inputoutput: input element
e=>end: end

st(right)->io(right)->op1->cond
cond(yes)(left)->op2(right)->op3(right)->e
cond(no)->e
```

hashtable中的桶vector在resize时，通过创建一个新的vector，然后将old vector里面的元素遍历并重新计算它们在new vector中的地址，此时它们采用的hash函数是一样的，只是new vector的长度有了变化，也就是hashtable的长度发生了变化。

```c++
template <class V, class K, class HF, class Ex, class Eq, class A>
void hashtable<V, K, HF, Ex, Eq, A>::resize(size_type num_elements_hint)
{
  const size_type old_n = buckets.size();
  if (num_elements_hint > old_n) {
    const size_type n = next_size(num_elements_hint);
    if (n > old_n) {
      	vector<node*, A> tmp(n, (node*) 0);
      	__STL_TRY {
        for (size_type bucket = 0; bucket < old_n; ++bucket) {
          node* first = buckets[bucket];
          while (first) {
            size_type new_bucket = bkt_num(first->val, n);
            buckets[bucket] = first->next;
            first->next = tmp[new_bucket];
            tmp[new_bucket] = first;
            first = buckets[bucket];          
          }
        }
        buckets.swap(tmp);
      }
#ifdef __STL_USE_EXCEPTIONS
      catch(...) {
        for (size_type bucket = 0; bucket < tmp.size(); ++bucket) {
          while (tmp[bucket]) {
            node* next = tmp[bucket]->next;
            delete_node(tmp[bucket]);
            tmp[bucket] = next;
          }
        }
        throw;
      }
#endif /* __STL_USE_EXCEPTIONS */
    }
  }
}
```

### **hashtable内置的hash函数**

在hashtable的实现中，想要hashtable支持多种类型，首先要保证这些类型有自己的hash函数，还有equalkey的方法，有时候还需要extractkey的方法。这里简单介绍一下hashtable的内置的一些hash函数，equalkey的方法和etractkey方法，这些方法都是通过仿函数（主要是重载了() 运算符）和全特化来实现的。

这是一个关于字符串和整型的求hashcode实现，（字符串采用的应该是BKDHRHash算法，seed取5），公式如下。
$$
hash = seed * hash + (*pStr)(pStr为指向字符串的指针)
$$
而int直接返回原值即可，即整型的hashcode就是其本身。

```C++
// BKDR Hash Function
unsigned int BKDRHash(char *str)
{
    unsigned int seed = 131; // 31 131 1313 13131 131313 etc..
    unsigned int hash = 0;

    while (*str)
    {
        hash = hash * seed + (*str++);
    }

    return (hash & 0x7FFFFFFF);
}
```

```c++
template <class Key> struct hash { };

inline size_t __stl_hash_string(const char* s)
{
  unsigned long h = 0; 
  for ( ; *s; ++s)
    h = 5*h + *s;
  
  return size_t(h);
}

//string
__STL_TEMPLATE_NULL struct hash<char*>
{
  size_t operator()(const char* s) const { return __stl_hash_string(s); }
};

//int
__STL_TEMPLATE_NULL struct hash<int> {
  size_t operator()(int x) const { return x; }
};
```

```C++
size_type bkt_num_key(const key_type& key) const
{
    return bkt_num_key(key, buckets.size());
}

size_type bkt_num_key(const key_type& key, size_t n) const
{
    //计算在hashtable中的地址，使用除留余数法
    return hash(key) % n;
}  

size_type bkt_num(const value_type& obj, size_t n) const
{
    return bkt_num_key(get_key(obj), n);
}

size_type bkt_num(const value_type& obj) const
{
    return bkt_num_key(get_key(obj));
}
```

equalkey主要是用来比较两个元素key值是否相等，STL中实现了二元仿函数equal_to来比较两个值否否相等。而extractkey用来从value中提取key，比如上面的getkey方法，对于int，char*等这样的类型，直接返回原值就可以，STL中也实现了一元仿函数identify直接返回原值。这些只是针对比较简单的类型来说，一旦我们需要存储比较复杂的类型或者key值是比较复杂类型时，就需要自定义这些复杂类型求hashcode的方法、extractkey和equalkey的方法，定义这些方法都应该通过仿函数的方式实现。

```C++
template <class T>
struct equal_to : public binary_function<T, T, bool> {
    bool operator()(const T& x, const T& y) const { return x == y; }
};
```

```C++
template <class T>
struct identity : public unary_function<T, T> {
  	const T& operator()(const T& x) const { return x; }
};
```



