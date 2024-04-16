## Slice

- Slice 在 Go 中是一个结构体

```go
type slice struct {
    array unsafe.Pointer // 指向存放数据的数组指针
    len   int            // 长度有多大
    cap   int            // 容量有多大
}
```

- slice 存在 **数据共享**，比如下面这样：

```go
foo = make([]int, 5)
foo[3] = 42
foo[4] = 100

bar := foo[1:4]
bar[1] = 99
```

![](/assets/geektime/go_slice_1.png)

- slice 的会动态扩容，指向新的数组：

```go
a := make([]int, 32)
b := a[1:16]
a = append(a, 1)
a[2] = 42
```

![](/assets/geektime/go_slice_2.png)

- 由于数据共享，slice 存在数据被覆盖的问题：

```go
func main() {
  path := []byte("AAAA/BBBBBBBBB")
  sepIndex := bytes.IndexByte(path,'/')

  dir1 := path[:sepIndex]
  dir2 := path[sepIndex+1:]

  fmt.Println("dir1 =>", string(dir1)) // prints: dir1 => AAAA
  fmt.Println("dir2 =>", string(dir2)) // prints: dir2 => BBBBBBBBB

  dir1 = append(dir1, "suffix"...)

  fmt.Println("dir1 =>", string(dir1)) // prints: dir1 => AAAAsuffix
  fmt.Println("dir2 =>", string(dir2)) // prints: dir2 => uffixBBBB
}
```

![](/assets/geektime/go_slice_3.png)

- 解决上面的问题，只需将 `dir1 := path[:sepIndex]` 更改为 `dir1 := path[:sepIndex:sepIndex]`，最后一个参数是 Limited Capacity，它会让后续的 `append()` 操作重新分配内存

## 深度比较

- 比较两个结构体中的数据是否相同，需要使用 `reflect.DeepEqual()`

```go
import (
    "fmt"
    "reflect"
)

func main() {
  v1 := data{}
  v2 := data{}
  fmt.Println("v1 == v2:", reflect.DeepEqual(v1,v2))
  //prints: v1 == v2: true

  m1 := map[string]string{"one": "a","two": "b"}
  m2 := map[string]string{"two": "b", "one": "a"}
  fmt.Println("m1 == m2:", reflect.DeepEqual(m1, m2))
  //prints: m1 == m2: true

  s1 := []int{1, 2, 3}
  s2 := []int{1, 2, 3}
  fmt.Println("s1 == s2:", reflect.DeepEqual(s1, s2))
  //prints: s1 == s2: true
}
```

### 接口编程

- 下面为初始代码：

```go
type Country struct {
  Name string
}

type City struct {
  Name string
}

type Printable interface {
  PrintStr()
}

func (c Country) PrintStr() {
  fmt.Println(c.Name)
}
func (c City) PrintStr() {
  fmt.Println(c.Name)
}

c1 := Country {"China"}
c2 := City {"Beijing"}
c1.PrintStr()
c2.PrintStr()
```

- 这里定义了接口 `Printable` 但是并没有很好地利用，可以通过 **结构体嵌入** 来完善：

```go
type WithName struct {
  Name string
}

type Country struct {
  WithName
}

type City struct {
  WithName
}

type Printable interface {
  PrintStr()
}

func (w WithName) PrintStr() {
  fmt.Println(w.Name)
}

c1 := Country { WithName{"China"} }
c2 := City { WithName{"Beijing"} }
c1.PrintStr()
c2.PrintStr()
```

- 代码依然比较乱，继续完善：

```go
type Country struct {
  Name string
}

type City struct {
  Name string
}

type Stringable interface {
  ToString() string
}

func (c Country) ToString() string {
  return "Country = " + c.Name
}

func (c City) ToString() string{
  return "City = " + c.Name
}

func PrintStr(p Stringable) {
  fmt.Println(p.ToString())
}

d1 := Country {"USA"}
d2 := City{"Los Angeles"}
PrintStr(d1)
PrintStr(d2)
```

- `Stringable` 的接口将 “业务类型” `Country` 和 `City`，以及 “控制逻辑” `Print()` 解耦了，只要实现了 `Stringable`，都可传给 `PrintStr()` 使用
- 核心思想 —— **Program to an interface not an implementation**

### 接口完整性检查

- Go 语言的编译器并没有严格检查一个对象是否实现了某接口的所有方法

```go
type Shape interface {
  Sides() int
  Area() int
}

type Square struct {
  len int
}

func (s* Square) Sides() int {
  return 4
}

func main() {
  s := Square{len: 5}
  fmt.Printf("%d\n",s.Sides())
}
```

- 可以使用 `var _ Shape = (*Square)(nil)` 来进行强验证，如果没有实现完全相关的接口方法，编译器就会报错

## 性能提示

- 如果需要把数字转换成字符串，使用 `strconv.Itoa()` 比 `fmt.Sprintf()` 要快一倍左右
- 尽可能避免把 `String` 转成 `[]Byte`，这个转换会导致性能下降
- 如果在 for-loop 里对某个 Slice 使用 `append()`，请先把 Slice 的容量扩充到位，这样可以避免内存重新分配以及系统自动按 2 的 N 次方幂进行扩展但又用不到的情况，从而避免浪费内存
- 使用 `StringBuffer` 或是 `StringBuild` 来拼接字符串，性能会比使用 `+` 或 `+=` 高三到四个数量级
- 尽可能使用并发的 `goroutine`，然后使用 `sync.WaitGroup` 来同步分片操作
- 避免在热代码中进行内存分配，这样会导致 gc 很忙。尽可能使用 `sync.Pool` 来重用对象
- 使用 lock-free 的操作，避免使用 `mutex`，尽可能使用 `sync/Atomic` 包
- 使用 I/O 缓冲，I/O 是个非常非常慢的操作，使用 `bufio.NewWrite()` 和 `bufio.NewReader()` 可以带来更高的性能
- 对于在 for-loop 里的固定的正则表达式，一定要使用 `regexp.Compile()` 编译正则表达式。性能会提升两个数量级
- 如果你需要更高性能的协议，就要考虑使用 `protobuf` 或 `msgp` 而不是 JSON，因为 JSON 的序列化和反序列化里使用了反射
- 你在使用 Map 的时候，使用整型的 key 会比字符串的要快，因为整型比较比字符串比较要快
