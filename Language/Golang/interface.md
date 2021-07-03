# interface分析

## 编译器如何检查“ duck ”类型

```go
// type.Type
// A Type represents a Go type.
type Type struct {
	// Extra contains extra etype-specific fields.
	// As an optimization, those etype-specific structs which contain exactly
	// one pointer-shaped field are stored as values rather than pointers when possible.
	//
	// TMAP: *Map
	// TFORW: *Forward
	// TFUNC: *Func
	// TSTRUCT: *Struct
	// TINTER: *Interface
	// TFUNCARGS: FuncArgs
	// TCHANARGS: ChanArgs
	// TCHAN: *Chan
	// TPTR: Ptr
	// TARRAY: *Array
	// TSLICE: Slice
	Extra interface{}  

	// Width is the width of this Type in bytes.
	Width int64 // valid if Align > 0

	methods    Fields
	allMethods Fields

	Nod  *Node // canonical OTYPE node  OTYPE节点
	Orig *Type // original type (type literal or predefined type)

	// Cache of composite types, with this type being the element type.
	Cache struct {
		ptr   *Type // *T, or nil
		slice *Type // []T, or nil
	}

	Sym    *Sym  // symbol containing name, for named types 包含名称的符号，用于命名类型
	Vargen int32 // unique name for OTYPE/ONAME 

	Etype EType // kind of type  类型
	Align uint8 // the required alignment of this type, in bytes (0 means Width and Align have not yet been computed)

	flags bitset8
}

// EType:
// TMAP: *Map
// TFORW: *Forward
// TFUNC: *Func
// TSTRUCT: *Struct
// TINTER: *Interface
// TFUNCARGS: FuncArgs
// TCHANARGS: ChanArgs
// TCHAN: *Chan
// TPTR: Ptr
// TARRAY: *Array
// TSLICE: Slice
```

```go
// type.Sym
type Sym struct {
	Importdef *Pkg   // 保存导入包定义
	Linkname  string // link name

	Pkg  *Pkg
	Name string // 对象名

	// saved and restored by dcopy
	Def        *Node    // definition: ONAME OTYPE OPACK or OLITERAL
	Block      int32    // blocknumber to catch redeclaration
	Lastlineno src.XPos // last declaration for diagnostic

	flags   bitset8
	Label   *Node // corresponding label (ephemeral)
	Origpkg *Pkg  // original package for . import
}
```

```go
// gc.Node
// A Node is a single node in the syntax tree.
// Actually the syntax tree is a syntax DAG, because there is only one
// node with Op=ONAME for a given instance of a variable x.
// The same is true for Op=OTYPE and Op=OLITERAL. See Node.mayBeShared.
/*
Node是语法树中的单个节点。实际上，语法树是一个语法 DAG，因为对于变量 x 的给定实例，只有一个具有 Op=ONAME 的节点。 Op=OTYPE 和 Op=OLITERAL 也是如此。请参阅 Node.mayBeShared
*/
type Node struct {
	// Tree structure.
	// Generic recursive walks should follow these fields. 
	//! 通用递归遍历应该遵循这些字段
	Left  *Node         //! 左节点
	Right *Node			//!	右节点 
	Ninit Nodes		    //! 
	Nbody Nodes			//!	
	List  Nodes         //!
	Rlist Nodes         //! 

	// most nodes
	Type *types.Type
	Orig *Node // original form, for printing, and tracking copies of ONAMEs

	// func
	Func *Func

	// ONAME, OTYPE, OPACK, OLABEL, some OLITERAL
	Name *Name

	Sym *types.Sym  // various
	E   interface{} // Opt or Val, see methods below

	// Various. Usually an offset into a struct. For example:
	// - ONAME nodes that refer to local variables use it to identify their stack frame position.
	// - ODOT, ODOTPTR, and ORESULT use it to indicate offset relative to their base address.
	// - OSTRUCTKEY uses it to store the named field's offset.
	// - Named OLITERALs use it to store their ambient iota value.
	// - OINLMARK stores an index into the inlTree data structure.
	// - OCLOSURE uses it to store ambient iota value, if any.
	// Possibly still more uses. If you find any, document them.
	Xoffset int64

	Pos src.XPos

	flags bitset32

	Esc uint16 // EscXXX

	Op  Op
	aux uint8
}

// op:
// Node ops.
	// names
	ONAME    // var or func name
	ONONAME  // unnamed arg or return value: f(int, string) (int, error) { etc }
	OTYPE    // type name

	// expressions
	OAS           // Left = Right or (if Colas=true) Left := Right
	OAS1          // List = Rlist (x, y, z = a, b, c)
	OASOP         // Left Etype= Right (x += y)
	OCALL         // Left(List) (function call, method call or type conversion)

	
	OCOMPLIT   // Right{List} (composite literal, not yet lowered to specific form)
	OCONV      // Type(Left) (type conversion)
	OCONVIFACE // Type(Left) (type conversion, to interface)
	OCONVNOP   // Type(Left) (type conversion, no effect)
	ODCL       // var Left (declares Left of type Left.Type)

	// Used during parsing but don't last.
	ODCLFUNC  // func f() or func (r) f()
	ODCLFIELD // struct field, interface field, or func/method argument/return value.
	ODCLCONST // const pi = 2.14
	ODCLTYPE  // type Int int or type Int = int
	OXDOT        // Left.Sym (before rewrite to one of the preceding)
	OINDEX       // Left[Right] (index of array or slice

	// types
	OTCHAN   // chan int
	OTMAP    // map[string]int
	OTSTRUCT // struct{}
	OTINTER  // interface{}
	OTFUNC   // func()
	OTARRAY  // []int, [7]int, [N]int or [...]int
```





