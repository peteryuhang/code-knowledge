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

## 错误处理

- Go 语言的错误处理的方式，本质上是类似 C 语言的返回值检查，但是它也兼顾了异常的一些好处——对错误的扩展
- Error Check Hell：

```go
func parse(r io.Reader) (*Point, error) {
  var p Point

  if err := binary.Read(r, binary.BigEndian, &p.Longitude); err != nil {
    return nil, err
  }
  if err := binary.Read(r, binary.BigEndian, &p.Latitude); err != nil {
    return nil, err
  }
  if err := binary.Read(r, binary.BigEndian, &p.Distance); err != nil {
    return nil, err
  }
  if err := binary.Read(r, binary.BigEndian, &p.ElevationGain); err != nil {
    return nil, err
  }
  if err := binary.Read(r, binary.BigEndian, &p.ElevationLoss); err != nil {
    return nil, err
  }
}
```

- 可以通过函数式编程的方式将代码改成下面这样：

```go
func parse(r io.Reader) (*Point, error) {
  var p Point
  var err error
  read := func(data interface{}) {
    if err != nil {
      return
    }
    err = binary.Read(r, binary.BigEndian, data)
  }

  read(&p.Longitude)
  read(&p.Latitude)
  read(&p.Distance)
  read(&p.ElevationGain)
  read(&p.ElevationLoss)

  if err != nil {
    return &p, err
  }
  return &p, nil
}
```

- 进一步，我们可以模仿 `bufio.Scanner()` 的方式，这样业务逻辑会更加 “干净”：

```go
type Reader struct {
  r   io.Reader
  err error
}

func (r *Reader) read(data interface{}) {
  if r.err == nil {
    r.err = binary.Read(r.r, binary.BigEndian, data)
  }
}

func parse(input io.Reader) (*Point, error) {
  var p Point
  r := Reader{r: input}

  r.read(&p.Longitude)
  r.read(&p.Latitude)
  r.read(&p.Distance)
  r.read(&p.ElevationGain)
  r.read(&p.ElevationLoss)

  if r.err != nil {
    return nil, r.err
  }

  return &p, nil
}
```

- 有了这些，**流式接口（Fluent Interface）** 也就很容易处理了：

```go
package main

import (
  "bytes"
  "encoding/binary"
  "fmt"
)

// 长度不够，少一个Weight
var b = []byte {0x48, 0x61, 0x6f, 0x20, 0x43, 0x68, 0x65, 0x6e, 0x00, 0x00, 0x2c} 
var r = bytes.NewReader(b)

type Person struct {
  Name [10]byte
  Age uint8
  Weight uint8
  err error
}
func (p *Person) read(data interface{}) {
  if p.err == nil {
    p.err = binary.Read(r, binary.BigEndian, data)
  }
}

func (p *Person) ReadName() *Person {
  p.read(&p.Name) 
  return p
}
func (p *Person) ReadAge() *Person {
  p.read(&p.Age) 
  return p
}
func (p *Person) ReadWeight() *Person {
  p.read(&p.Weight) 
  return p
}
func (p *Person) Print() *Person {
  if p.err == nil {
    fmt.Printf("Name=%s, Age=%d, Weight=%d\n",p.Name, p.Age, p.Weight)
  }
  return p
}

func main() {   
  p := Person{}
  p.ReadName().ReadAge().ReadWeight().Print()
  fmt.Println(p.err)  // EOF 错误
}
```

- 不过这个错误处理的技巧的使用条件是有限的，只能对同一个业务对象的不断操作进行简化，而多个业务对象则还是需要 `if err != nil`

### 包装错误

- 通常会在接收到错误后，添加些上下文再返回到上层，一般的做法如下：

```go
if err != nil {
  return fmt.Errorf("something failed: %v", err)
}
```

- 另外一个普遍的做法是将错误包装在另一个错误中，同时保留原始内容

```go
type authorizationError struct {
  operation string
  err error   // 原始错误
}

func (e *authorizationError) Error() string {
  return fmt.Sprintf("authorization failed during %s: %v", e.operation, e.err)
}
```

- 更好的方式是通过一种标准的访问方法，我们最好使用一个接口：

```go
type causer interface {
  Cause() error
}

func (e *authorizationError) Cause() error {
  return e.err
}
```

- 有一个第三方错误库专门负责这事，由于使用很广，基本上是事实的标准：

```go
import "github.com/pkg/errors"

//错误包装
if err != nil {
  return errors.Wrap(err, "read failed")
}

// Cause 接口
switch err := errors.Cause(err).(type) {
case *MyError:
  // handle specifically
default:
  // unknown error
}
```

## Go 编程模式：Functional Options

- 在编程中我们会遇到配置选项问题，比如下面的 `Addr` 和 `Port` 为必选，其他可选：

```go
type Server struct {
  Addr     string
  Port     int
  Protocol string
  Timeout  time.Duration
  MaxConns int
  TLS      *tls.Config
}
```

- 针对这样的配置，我们需要有不同的创建函数：

```go
func NewDefaultServer(addr string, port int) (*Server, error) {
  return &Server{addr, port, "tcp", 30 * time.Second, 100, nil}, nil
}

func NewTLSServer(addr string, port int, tls *tls.Config) (*Server, error) {
  return &Server{addr, port, "tcp", 30 * time.Second, 100, tls}, nil
}

func NewServerWithTimeout(addr string, port int, timeout time.Duration) (*Server, error) {
  return &Server{addr, port, "tcp", timeout, 100, nil}, nil
}

func NewTLSServerWithMaxConnAndTimeout(addr string, port int, maxconns int, timeout time.Duration, tls *tls.Config) (*Server, error) {
  return &Server{addr, port, "tcp", 30 * time.Second, maxconns, tls}, nil
}
```

### 组合嵌套

- 这及其不灵活，也不利于管理

- 我们可以考虑配置对象的方案，将非必需的选项都放到一个结构中，然后使用嵌套的方式来组合：

```go
type Config struct {
  Protocol string
  Timeout  time.Duration
  Maxconns int
  TLS      *tls.Config
}

type Server struct {
  Addr string
  Port int
  Conf *Config
}
```

- 这样我们就仅需要一个 `NewServer()` 函数来构造对象：

```go
func NewServer(addr string, port int, conf *Config) (*Server, error) {
    //...
}

//Using the default configuratrion
srv1, _ := NewServer("localhost", 9000, nil) 

conf := ServerConfig{Protocol:"tcp", Timeout: 60*time.Duration}
srv2, _ := NewServer("locahost", 9000, &conf)
```

### Builder 模式

- 上面的代码中，我们还需要对 `Config` 进行判空，代码还是不太 “干净”

- 可以考虑仿照 Java 中的 Builder 设计模式：

```go
// 使用一个 builder 类来做包装
type ServerBuilder struct {
  Server
}

func (sb *ServerBuilder) Create(addr string, port int) *ServerBuilder {
  sb.Server.Addr = addr
  sb.Server.Port = port
  // 其它代码设置其它成员的默认值
  return sb
}

func (sb *ServerBuilder) WithProtocol(protocol string) *ServerBuilder {
  sb.Server.Protocol = protocol 
  return sb
}

func (sb *ServerBuilder) WithMaxConn( maxconn int) *ServerBuilder {
  sb.Server.MaxConns = maxconn
  return sb
}

func (sb *ServerBuilder) WithTimeOut( timeout time.Duration) *ServerBuilder {
  sb.Server.Timeout = timeout
  return sb
}

func (sb *ServerBuilder) WithTLS( tls *tls.Config) *ServerBuilder {
  sb.Server.TLS = tls
  return sb
}

func (sb *ServerBuilder) Build() (Server) {
  return  sb.Server
}
```

- 这样一来就可以用 **链式函数调用的方式** 来构建了

```go
sb := ServerBuilder{}
server, err := sb.Create("127.0.0.1", 8080).
  WithProtocol("udp").
  WithMaxConn(1024).
  WithTimeOut(30*time.Second).
  Build()
```

### Functional Options

- 使用 Builder 的方式需要多加一个 Builder 类，不然错误处理会比较麻烦，如果我们想要省去这个包装结构体，就需要考虑 **函数式编程** 了

- 定义一个函数类型：

```go
type Option func(*Server)
```

- 然后使用函数式的方式定义一组函数：

```go
func Protocol(p string) Option {
  return func(s *Server) {
    s.Protocol = p
  }
}
func Timeout(timeout time.Duration) Option {
  return func(s *Server) {
    s.Timeout = timeout
  }
}
func MaxConns(maxconns int) Option {
  return func(s *Server) {
    s.MaxConns = maxconns
  }
}
func TLS(tls *tls.Config) Option {
  return func(s *Server) {
    s.TLS = tls
  }
}
```

- 在数学上，这样的函数被称作 **高阶函数**，就是对原有函数进行包装后返回
- 我们可以基于此定义类似前面的 `NewServer()` 函数，进行对象的创建：

```go
func NewServer(addr string, port int, options ...func(*Server)) (*Server, error) {
  srv := Server{
    Addr:     addr,
    Port:     port,
    Protocol: "tcp",
    Timeout:  30 * time.Second,
    MaxConns: 1000,
    TLS:      nil,
  }
  for _, option := range options {
    option(&srv)
  }
  //...
  return &srv, nil
}
```

- 就可以像如下这样进行对象的创建：

```go
s1, _ := NewServer("localhost", 1024)
s2, _ := NewServer("localhost", 2048, Protocol("udp"))
s3, _ := NewServer("0.0.0.0", 8080, Timeout(300*time.Second), MaxConns(1000))
```

- 这种方式的 6 大好处
  - 直觉式的编程
  - 高度的可配置化
  - 很容易维护和扩展
  - 自文档
  - 新来的人很容易上手
  - 没有什么令人困惑的事（是 `nil` 还是空）

## Go 编程模式：委托和反转控制

### 嵌套

- 在 Go 中我们可以把一个结构体嵌入到另一个结构体中：

```go
type Widget struct {
  X, Y int
}

type Label struct {
  Widget        // Embedding (delegation)
  Text   string // Aggregation
}
```

- 可以类似如下使用：

```go
label := Label{Widget{10, 10}, "State:"}

label.X = 11
label.Y = 12
```

- 可以层层嵌套：

```go
type Button struct {
  Label // Embedding (delegation)
}

type ListBox struct {
  Widget          // Embedding (delegation)
  Texts  []string // Aggregation
  Index  int      // Aggregation
}
```

### 委托

- 定义如下两个接口

```go
type Painter interface {
  Paint()
}
 
type Clicker interface {
  Click()
}
```

- 之前的结构体实现这两个接口，对于 `Label` 来说，只有 `Clicker`

```go
func (label Label) Paint() {
  fmt.Printf("%p:Label.Paint(%q)\n", &label, label.Text)
}

// 因为这个接口可以通过 Label 的嵌入带到新的结构体，
// 所以，可以在 Button 中重载这个接口方法
func (button Button) Paint() { // Override
  fmt.Printf("Button.Paint(%s)\n", button.Text)
}
func (button Button) Click() {
  fmt.Printf("Button.Click(%s)\n", button.Text)
}

func (listBox ListBox) Paint() {
  fmt.Printf("ListBox.Paint(%q)\n", listBox.Texts)
}
func (listBox ListBox) Click() {
  fmt.Printf("ListBox.Click(%q)\n", listBox.Texts)
}
```

- 从下面的程序中，我们可以看到多态是怎么执行的

```go
button1 := Button{Label{Widget{10, 70}, "OK"}}
button2 := NewButton(50, 70, "Cancel")
listBox := ListBox{
  Widget{10, 40}, 
  []string{"AL", "AK", "AZ", "AR"},
  0
}

for _, painter := range []Painter{label, listBox, button1, button2} {
  painter.Paint()
}
 
for _, widget := range []interface{}{label, listBox, button1, button2} {
  widget.(Painter).Paint()
  if clicker, ok := widget.(Clicker); ok {
    clicker.Click()
  }
  fmt.Println() // print a empty line 
}
```

- 可以使用接口（比如 `Painter` 和 `Clicker`）来多态，也可以使用泛型 `interface{}` 来多态

### 反转控制

- 我们有一个存放整数的数据结构，实现了 `Add`，`Delete`，`Contains` 三个操作：

```go
type IntSet struct {
  data map[int]bool
}

func NewIntSet() IntSet {
  return IntSet{make(map[int]bool)}
}

func (set *IntSet) Add(x int) {
  set.data[x] = true
}

func (set *IntSet) Delete(x int) {
  delete(set.data, x)
}

func (set *IntSet) Contains(x int) bool {
  return set.data[x]
}
```

- 我们想实现一个 `Undo` 的功能，可以对 `IntSet` 再包装一下，变成 `UndoableIntSet`

```go
type UndoableIntSet struct { // Poor style
  IntSet    // Embedding (delegation)
  functions []func()
}
 
func NewUndoableIntSet() UndoableIntSet {
  return UndoableIntSet{NewIntSet(), nil}
}

func (set *UndoableIntSet) Add(x int) { // Override
  if !set.Contains(x) {
    set.data[x] = true
    set.functions = append(set.functions, func() { set.Delete(x) })
  } else {
    set.functions = append(set.functions, nil)
  }
}

func (set *UndoableIntSet) Delete(x int) { // Override
  if set.Contains(x) {
    delete(set.data, x)
    set.functions = append(set.functions, func() { set.Add(x) })
  } else {
    set.functions = append(set.functions, nil)
  }
}

func (set *UndoableIntSet) Undo() error {
  if len(set.functions) == 0 {
    return errors.New("No functions to undo")
  }
  index := len(set.functions) - 1
  if function := set.functions[index]; function != nil {
    function()
    set.functions[index] = nil // For garbage collection
  }
  set.functions = set.functions[:index]
  return nil
}
```

- 这里的 `UndoableIntSet` 中嵌入了 `IntSet`，Override 了它的 `Add()` 和 `Delete()` 方法
- `UndoableIntSet` 通过将每次操作的逆操作保存到一个函数数组中来实现 Undo 逻辑
- **问题在于这里的 Undo 是控制逻辑，并不是业务逻辑，但是 Undo 里面加入了大量的跟 `IntSet` 相关的业务逻辑**

- 另外一种方法，将 Undo 这个控制逻辑定义成如下所示

```go
type Undo []func()

func (undo *Undo) Add(function func()) {
  *undo = append(*undo, function)
}

func (undo *Undo) Undo() error {
  functions := *undo
  if len(functions) == 0 {
    return errors.New("No functions to undo")
  }
  index := len(functions) - 1
  if function := functions[index]; function != nil {
    function()
    functions[index] = nil // For garbage collection
  }
  *undo = functions[:index]
  return nil
}
```

- 然后，我们在 IntSet 里嵌入 Undo，然后在之前的 `Add()` 和 `Delete()` 里使用上面的方法，就可以完成功能了

```go
type IntSet struct {
  data map[int]bool
  undo Undo
}
 
func NewIntSet() IntSet {
  return IntSet{data: make(map[int]bool)}
}

func (set *IntSet) Undo() error {
  return set.undo.Undo()
}
 
func (set *IntSet) Contains(x int) bool {
  return set.data[x]
}

func (set *IntSet) Add(x int) {
  if !set.Contains(x) {
    set.data[x] = true
    set.undo.Add(func() { set.Delete(x) })
  } else {
    set.undo.Add(nil)
  }
}
 
func (set *IntSet) Delete(x int) {
  if set.Contains(x) {
    delete(set.data, x)
    set.undo.Add(func() { set.Add(x) })
  } else {
    set.undo.Add(nil)
  }
}
```

- **控制反转**：不是由控制逻辑 Undo 依赖 IntSet，而是由业务逻辑 IntSet 依赖 Undo
- 这里依赖的只是一个协议，**这个协议是一个没有参数的函数数组**

## Go 编程模式 - Map-Reduce

- Map Reduce 的含义：

![](/assets/geektime/go_map_reduce.png)

- `Map`，`Reduce`，`Filter` 只是一种控制逻辑，真正的业务逻辑是传给它们的函数和数据
- 这个模式也是经典的 “业务逻辑” 和 “控制逻辑” 分离解耦的编程模式

- 我们有一个员工对象和一些数据：

```go
type Employee struct {
  Name     string
  Age      int
  Vacation int
  Salary   int
}

var list = []Employee{
  {"Feier", 44, 0, 8000},
  {"Doupao", 34, 10, 5000},
  {"Doubao", 23, 5, 9000},
  {"Jack", 26, 0, 4000},
  {"Tom", 48, 9, 7500},
  {"Marry", 29, 0, 6000},
  {"Mike", 32, 8, 4000},
}
```

- 以及一些函数 (Reduce 和 Filter)：

```go
func EmployeeCountIf(list []Employee, fn func(e *Employee) bool) int {
  count := 0
  for i, _ := range list {
    if fn(&list[i]) {
      count += 1
    }
  }
  return count
}

func EmployeeFilterIn(list []Employee, fn func(e *Employee) bool) []Employee {
  var newList []Employee
  for i, _ := range list {
    if fn(&list[i]) {
      newList = append(newList, list[i])
    }
  }
  return newList
}

func EmployeeSumIf(list []Employee, fn func(e *Employee) int) int {
  var sum = 0
  for i, _ := range list {
    sum += fn(&list[i])
  }
  return sum
}
```

- 我们利用上面这些函数可以做一些统计的工作：

```go
// 统计有多少员工大于 40 岁
old := EmployeeCountIf(list, func(e *Employee) bool {
  return e.Age > 40
})
fmt.Printf("old people: %d\n", old)
//old people: 2

// 列出有没有休假的员工
no_vacation := EmployeeFilterIn(list, func(e *Employee) bool {
  return e.Vacation == 0
})
fmt.Printf("People no vacation: %v\n", no_vacation)
//People no vacation: [{Feier 44 0 8000} {Jack 26 0 4000} {Marry 29 0 6000}]

// 统计所有员工的薪资总和
total_pay := EmployeeSumIf(list, func(e *Employee) int {
  return e.Salary
})
fmt.Printf("Total Salary: %d\n", total_pay)
//Total Salary: 43500
```

- 上面的 Map-Reduce 都要因为处理数据的不同，写出不同的版本，我们需要将其 **泛型化**，我们可以利用 `interface{} + reflect` 来完成

```go
func Map(data interface{}, fn interface{}) []interface{} {
  vfn := reflect.ValueOf(fn)
  vdata := reflect.ValueOf(data)
  result := make([]interface{}, vdata.Len())

  for i := 0; i < vdata.Len(); i++ {
    result[i] = vfn.Call([]reflect.Value{vdata.Index(i)})[0].Interface()
  }
  return result
}
```

- 基于此，不同类型的数据可以使用相同的逻辑：

```go
square := func(x int) int { return x * x }
nums := []int{1, 2, 3, 4}
squared_arr := Map(nums, square)
fmt.Println(squared_arr)
//[1 4 9 16]

upcase := func(s string) string { return strings.ToUpper(s) }
strs := []string{"Hao", "Chen", "MegaEase"}
upstrs := Map(strs, upcase);
fmt.Println(upstrs)
//[HAO CHEN MEGAEASE]
```

- 但是这里面并没有错误处理机制，而且反射是运行时的事，如果类型出问题，就会 panic
- 因此我们需要自己来做类型检查

```go
func Transform(slice, function interface{}) interface{} {
  return transform(slice, function, false)
}

func TransformInPlace(slice, function interface{}) interface{} {
  return transform(slice, function, true)
}

func transform(slice, function interface{}, inPlace bool) interface{} {
  //check the `slice` type is Slice
  sliceInType := reflect.ValueOf(slice)
  if sliceInType.Kind() != reflect.Slice {
    panic("transform: not slice")
  }

  //check the function signature
  fn := reflect.ValueOf(function)
  elemType := sliceInType.Type().Elem()
  if !verifyFuncSignature(fn, elemType, nil) {
    panic("trasform: function must be of type func(" + sliceInType.Type().Elem().String() + ") outputElemType")
  }

  sliceOutType := sliceInType
  if !inPlace {
    sliceOutType = reflect.MakeSlice(reflect.SliceOf(fn.Type().Out(0)), sliceInType.Len(), sliceInType.Len())
  }
  for i := 0; i < sliceInType.Len(); i++ {
    sliceOutType.Index(i).Set(fn.Call([]reflect.Value{sliceInType.Index(i)})[0])
  }

  return sliceOutType.Interface()
}

func verifyFuncSignature(fn reflect.Value, types ...reflect.Type) bool {
  //Check it is a funciton
  if fn.Kind() != reflect.Func {
    return false
  }
  // NumIn() - returns a function type's input parameter count.
  // NumOut() - returns a function type's output parameter count.
  if (fn.Type().NumIn() != len(types)-1) || (fn.Type().NumOut() != 1) {
    return false
  }
  // In() - returns the type of a function type's i'th input parameter.
  for i := 0; i < len(types)-1; i++ {
    if fn.Type().In(i) != types[i] {
      return false
    }
  }
  // Out() - returns the type of a function type's i'th output parameter.
  outType := types[len(types)-1]
  if outType != nil && fn.Type().Out(0) != outType {
    return false
  }
  return true
}
```

- 类似地，我们也可以得到 Reduce 和 Filter：

```go
func Reduce(slice, pairFunc, zero interface{}) interface{} {
  sliceInType := reflect.ValueOf(slice)
  if sliceInType.Kind() != reflect.Slice {
    panic("reduce: wrong type, not slice")
  }

  len := sliceInType.Len()
  if len == 0 {
    return zero
  } else if len == 1 {
    return sliceInType.Index(0)
  }

  elemType := sliceInType.Type().Elem()
  fn := reflect.ValueOf(pairFunc)
  if !verifyFuncSignature(fn, elemType, elemType, elemType) {
    t := elemType.String()
    panic("reduce: function must be of type func(" + t + ", " + t + ") " + t)
  }

  var ins [2]reflect.Value
  ins[0] = sliceInType.Index(0)
  ins[1] = sliceInType.Index(1)
  out := fn.Call(ins[:])[0]

  for i := 2; i < len; i++ {
    ins[0] = out
    ins[1] = sliceInType.Index(i)
    out = fn.Call(ins[:])[0]
  }
  return out.Interface()
}

func Filter(slice, function interface{}) interface{} {
  result, _ := filter(slice, function, false)
  return result
}

func FilterInPlace(slicePtr, function interface{}) {
  in := reflect.ValueOf(slicePtr)
  if in.Kind() != reflect.Ptr {
    panic("FilterInPlace: wrong type, " +
      "not a pointer to slice")
  }
  _, n := filter(in.Elem().Interface(), function, true)
  in.Elem().SetLen(n)
}

var boolType = reflect.ValueOf(true).Type()

func filter(slice, function interface{}, inPlace bool) (interface{}, int) {

  sliceInType := reflect.ValueOf(slice)
  if sliceInType.Kind() != reflect.Slice {
    panic("filter: wrong type, not a slice")
  }

  fn := reflect.ValueOf(function)
  elemType := sliceInType.Type().Elem()
  if !verifyFuncSignature(fn, elemType, boolType) {
    panic("filter: function must be of type func(" + elemType.String() + ") bool")
  }

  var which []int
  for i := 0; i < sliceInType.Len(); i++ {
    if fn.Call([]reflect.Value{sliceInType.Index(i)})[0].Bool() {
      which = append(which, i)
    }
  }

  out := sliceInType

  if !inPlace {
    out = reflect.MakeSlice(sliceInType.Type(), len(which), len(which))
  }
  for i := range which {
    out.Index(i).Set(sliceInType.Index(which[i]))
  }

  return out.Interface(), len(which)
}
```

- 使用反射来做这些东西会有一个问题，那就是代码的性能会很差。所以，上面的代码不能用在需要高性能的地方

## Go 编程模式 - 修饰器

- 修饰器模式的简单示例

```go
package main

import "fmt"

func decorator(f func(s string)) func(s string) {
  return func(s string) {
    fmt.Println("Started")
    f(s)
    fmt.Println("Done")
  }
}

func Hello(s string) {
  fmt.Println(s)
}

func main() {
  decorator(Hello)("Hello, World!")
}
```

- 计算函数运行时间的例子：

```go
package main

import (
  "fmt"
  "reflect"
  "runtime"
  "time"
)

type SumFunc func(int64, int64) int64

func getFunctionName(i interface{}) string {
  return runtime.FuncForPC(reflect.ValueOf(i).Pointer()).Name()
}

// 修饰器函数
func timedSumFunc(f SumFunc) SumFunc {
  return func(start, end int64) int64 {

    defer func(t time.Time) {
      fmt.Printf("--- Time Elapsed (%s): %v ---\n", 
          getFunctionName(f), time.Since(t))
    }(time.Now())

    return f(start, end)
  }
}

func Sum1(start, end int64) int64 {
  var sum int64
  sum = 0
  if start > end {
    start, end = end, start
  }
  for i := start; i <= end; i++ {
    sum += i
  }
  return sum
}

func Sum2(start, end int64) int64 {
  if start > end {
    start, end = end, start
  }
  return (end - start + 1) * (end + start) / 2
}

func main() {

  sum1 := timedSumFunc(Sum1)
  sum2 := timedSumFunc(Sum2)

  fmt.Printf("%d, %d\n", sum1(-10000, 10000000), sum2(-10000, 10000000))
}
```

- 运行结果如下：

```txt
$ go run time.sum.go
--- Time Elapsed (main.Sum1): 3.557469ms ---
--- Time Elapsed (main.Sum2): 291ns ---
49999954995000, 49999954995000
```

- 简单的 HTTP Server 的代码：

```go
package main

import (
  "fmt"
  "log"
  "net/http"
  "strings"
)

// 修饰器函数 - set header
func WithServerHeader(h http.HandlerFunc) http.HandlerFunc {
  return func(w http.ResponseWriter, r *http.Request) {
    log.Println("--->WithServerHeader()")
    w.Header().Set("Server", "HelloServer v0.0.1")
    h(w, r)
  }
}

// 修饰器函数 - set cookie
func WithAuthCookie(h http.HandlerFunc) http.HandlerFunc {
  return func(w http.ResponseWriter, r *http.Request) {
    log.Println("--->WithAuthCookie()")
    cookie := &http.Cookie{Name: "Auth", Value: "Pass", Path: "/"}
    http.SetCookie(w, cookie)
    h(w, r) 
  }
}

// 修饰器函数 - cookie auth check
func WithBasicAuth(h http.HandlerFunc) http.HandlerFunc {
  return func(w http.ResponseWriter, r *http.Request) {
    log.Println("--->WithBasicAuth()")
    cookie, err := r.Cookie("Auth")
    if err != nil || cookie.Value != "Pass" {
      w.WriteHeader(http.StatusForbidden)
      return
    }
    h(w, r)
  }
}

// 修饰器函数 - 打印日志
func WithDebugLog(h http.HandlerFunc) http.HandlerFunc {
  return func(w http.ResponseWriter, r *http.Request) {
    log.Println("--->WithDebugLog")
    r.ParseForm()
    log.Println(r.Form)
    log.Println("path", r.URL.Path)
    log.Println("scheme", r.URL.Scheme)
    log.Println(r.Form["url_long"])
    for k, v := range r.Form {
      log.Println("key:", k)
      log.Println("val:", strings.Join(v, ""))
    } 
    h(w, r)
  }
}

func hello(w http.ResponseWriter, r *http.Request) {
  log.Printf("Recieved Request %s from %s\n", r.URL.Path, r.RemoteAddr)
  fmt.Fprintf(w, "Hello, World! "+r.URL.Path)
}

func main() {
  http.HandleFunc("/v0/hello", WithServerHeader(hello))
  http.HandleFunc("/v1/hello", WithServerHeader(WithAuthCookie(hello)))
  http.HandleFunc("/v2/hello", WithServerHeader(WithBasicAuth(hello)))
  http.HandleFunc("/v3/hello", WithServerHeader(WithBasicAuth(WithDebugLog(hello))))
  err := http.ListenAndServe(":8080", nil)
  if err != nil {
    log.Fatal("ListenAndServe: ", err)
  }
}
```

- 如果多个修饰器套在一起，看上去不怎么好看，可以重构一下：

```go
type HttpHandlerDecorator func(http.HandlerFunc) http.HandlerFunc

func Handler(h http.HandlerFunc, decors ...HttpHandlerDecorator) http.HandlerFunc {
  for i := range decors {
    d := decors[len(decors)-1-i] // iterate in reverse
    h = d(h)
  }
  return h
}
```

- 于是下面两种写法就等效了：

```go
http.HandleFunc("/v3/hello", WithServerHeader(WithBasicAuth(WithDebugLog(hello))))
http.HandleFunc("/v3/hello", Handler(hello, WithDebugLog, WithBasicAuth, WithServerHeader))
```

- 简易的基于 Reflection 机制的通用修饰器

```go
// 出参：decoPrt 完成修饰的函数
// 入参：fn 需要修饰的函数
func Decorator(decoPtr, fn interface{}) (err error) {
  var decoratedFunc, targetFunc reflect.Value

  decoratedFunc = reflect.ValueOf(decoPtr).Elem()
  targetFunc = reflect.ValueOf(fn)

  v := reflect.MakeFunc(
    targetFunc.Type(),
    func(in []reflect.Value) (out []reflect.Value) {
      fmt.Println("before")
      out = targetFunc.Call(in) // 调用被修饰的函数
      fmt.Println("after")
      return
    })

  decoratedFunc.Set(v)
  return
}

func foo(a, b, c int) int {
  fmt.Printf("%d, %d, %d \n", a, b, c)
  return a + b + c
}

func bar(a, b string) string {
  fmt.Printf("%s, %s \n", a, b)
  return a + b
}
```

- 如果要对 `foo` 和 `bar` 修饰，可以像如下这样：

```go
type MyFoo func(int, int, int) int
var myfoo MyFoo
Decorator(&myfoo, foo)
myfoo(1, 2, 3)
```

- 不想申明函数签名的话，也可以这样：

```go
mybar := bar
Decorator(&mybar, bar)
mybar("hello,", "world!")
```

## Go 编程模式 - Pipeline

- Pipeline 是一种把各种命令拼接起来完成一个更强功能的技术方法
- Pipeline 可以很容易地把代码按单一职责的原则拆分成多个高内聚低耦合的小模块，然后轻松地把它们拼装起来，去完成比较复杂的功能
- 在上面修饰器例子中提到的 http handler，最后就是一个 Pipeline
- 我们可以基于 Go Routine 和 Channel 来构建 Pipeline，首先创建一个 echo 函数

```go
// 把一个整型数组放到一个 Channel 中
func echo(nums []int) <-chan int {
  out := make(chan int)
  go func() {
    for _, n := range nums {
      out <- n
    }
    close(out)
  }()
  return out
}
```

- 然后依照上面的模式，类似地可以创建不同功能的函数：

```go
// 求平方的函数
func sq(in <-chan int) <-chan int {
  out := make(chan int)
  go func() {
    for n := range in {
      out <- n * n
    }
    close(out)
  }()
  return out
}

// 过滤奇数的函数
func odd(in <-chan int) <-chan int {
  out := make(chan int)
  go func() {
    for n := range in {
      if n%2 != 0 {
        out <- n
      }
    }
    close(out)
  }()
  return out
}

// 求和函数
func sum(in <-chan int) <-chan int {
  out := make(chan int)
  go func() {
    var sum = 0
    for n := range in {
      sum += n
    }
    out <- sum
    close(out)
  }()
  return out
}
```

- 我们就可以将上面的函数拼接起来，这其实就相当于命令行中的 `echo $nums | odd | sq | sum`：

```go
var nums = []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
for n := range sum(sq(odd(echo(nums)))) {
  fmt.Println(n)
}
```

- 如果不想要有这么多的嵌套，可以通过代理函数完成

```go
type EchoFunc func ([]int) (<- chan int) 
type PipeFunc func (<- chan int) (<- chan int) 

func pipeline(nums []int, echo EchoFunc, pipeFns ... PipeFunc) <- chan int {
  ch  := echo(nums)
  for i := range pipeFns {
    ch = pipeFns[i](ch)
  }
  return ch
}
```

- 有了上面的代理函数，使用起来就会更加地简洁：

```go
var nums = []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}    
for n := range pipeline(nums, echo, odd, sq, sum) {
  fmt.Println(n)
}
```

### Fan in/out

- 假设我们需要用并发的方式对一个很长的数组中的质数进行求和运算

```go
func makeRange(min, max int) []int {
  a := make([]int, max-min+1)
  for i := range a {
    a[i] = min + i
  }
  return a
}

func is_prime(value int) bool {
  for i := 2; i <= int(math.Floor(float64(value) / 2)); i++ {
    if value%i == 0 {
      return false
    }
  }
  return value > 1
}

func prime(in <-chan int) <-chan int {
  out := make(chan int)
  go func ()  {
    for n := range in {
      if is_prime(n) {
        out <- n
      }
    }
    close(out)
  }()
  return out
}

func merge(cs []<-chan int) <-chan int {
  var wg sync.WaitGroup
  out := make(chan int)

  wg.Add(len(cs))
  for _, c := range cs {
    go func(c <-chan int) {
      for n := range c {
        out <- n
      }
      wg.Done()
    }(c)
  }
  go func() {
    wg.Wait()
    close(out)
  }()
  return out
}

func main() {
  // 制造一个从 1 到 10000 的数组
  nums := makeRange(1, 10000)

  // 把这堆数放到 channel 里
  in := echo(nums)

  // 生成 5 个 channel，并同时执行 sum(prime(in))
  // 每个 Sum 的 Go Routine 都会开始计算和
  const nProcess = 5
  var chans [nProcess]<-chan int
  for i := range chans {
    chans[i] = sum(prime(in))
  }

  // 把所有的结果再次求和，得到最终的结果
  for n := range sum(merge(chans[:])) {
    fmt.Println(n)
  }
}
```

- 整个程序的结构图如下：

![](/assets/geektime/go_pipeline_prime_sum.png)


## Go 编程模式 - Kubernetes Visitor

- Visitor 模式是将算法与操作对象的结构分离的一种方法
- 这种分离的实际结果是能够在不修改结构的情况下向现有对象结构添加新操作，是遵循开放 / 封闭原则的一种方法

- 简单的 visitor 示例：
```go
package main

import (
  "encoding/json"
  "encoding/xml"
  "fmt"
)

type Visitor func(shape Shape)

type Shape interface {
  accept(Visitor)
}

type Circle struct {
  Radius int
}

func (c Circle) accept(v Visitor) {
  v(c)
}

type Rectangle struct {
  Width, Heigh int
}

func (r Rectangle) accept(v Visitor) {
  v(r)
}
```

- 实现两个 Visitor，一个用来做 JSON 序列化；另一个用来做 XML 序列化：

```go
func JsonVisitor(shape Shape) {
  bytes, err := json.Marshal(shape)
  if err != nil {
    panic(err)
  }
  fmt.Println(string(bytes))
}

func XmlVisitor(shape Shape) {
  bytes, err := xml.Marshal(shape)
  if err != nil {
    panic(err)
  }
  fmt.Println(string(bytes))
}
```

- 使用如下：

```go
func main() {
  c := Circle{10}
  r :=  Rectangle{100, 200}
  shapes := []Shape{c, r}

  for _, s := range shapes {
    s.accept(JsonVisitor)
    s.accept(XmlVisitor)
  }
}
```

- 主要目的就是解耦数据结构（`Circle`，`Rectangle`）和算法（`Json`，`xml`）
- 上面的逻辑也可以用 Strategy 模式完成，但是有些时候，多个 Visitor 是来访问一个数据结构的不同部分，这种情况下，数据结构有点像一个数据库，而各个 Visitor 会成为一个个的小应用，kubectl 就是这样

- kubectl 的主要工作是处理用户提交的东西（包括命令行参数、YAML 文件等），接着会把用户提交的这些东西组织成一个数据结构体，发送给 API Server
- 基本原理就是它从命令行和 YAML 文件中获取信息，通过 Builder 模式并把其转成一系列的资源，最后用 Visitor 模式来迭代处理这些 Reources

- kubectl 的简单实现方法，kubectl 主要是用来处理 Info 结构体，相关定义：

```go
type VisitorFunc func(*Info, error) error

type Visitor interface {
  Visit(VisitorFunc) error
}

type Info struct {
  Namespace   string
  Name        string
  OtherThings string
}

func (info *Info) Visit(fn VisitorFunc) error {
  return fn(info, nil)
}
```

- 几种不同类型的 Visitor

```go
// 用来访问 Info 结构中的 Name 和 NameSpace 成员
type NameVisitor struct {
  visitor Visitor
}

func (v NameVisitor) Visit(fn VisitorFunc) error {
  return v.visitor.Visit(func(info *Info, err error) error {
    fmt.Println("NameVisitor() before call function")
    err = fn(info, err)
    if err == nil {
      fmt.Printf("==> Name=%s, NameSpace=%s\n", info.Name, info.Namespace)
    }
    fmt.Println("NameVisitor() after call function")
    return err
  })
}

// 用来访问 Info 结构中的 OtherThings 成员
type OtherThingsVisitor struct {
  visitor Visitor
}

func (v OtherThingsVisitor) Visit(fn VisitorFunc) error {
  return v.visitor.Visit(func(info *Info, err error) error {
    fmt.Println("OtherThingsVisitor() before call function")
    err = fn(info, err)
    if err == nil {
      fmt.Printf("==> OtherThings=%s\n", info.OtherThings)
    }
    fmt.Println("OtherThingsVisitor() after call function")
    return err
  })
}

// 用来输出 log
type LogVisitor struct {
  visitor Visitor
}

func (v LogVisitor) Visit(fn VisitorFunc) error {
  return v.visitor.Visit(func(info *Info, err error) error {
    fmt.Println("LogVisitor() before call function")
    err = fn(info, err)
    fmt.Println("LogVisitor() after call function")
    return err
  })
}
```

- 使用方代码：

```go
func main() {
  info := Info{}
  var v Visitor = &info
  v = LogVisitor{v}
  v = NameVisitor{v}
  v = OtherThingsVisitor{v}

  loadFile := func(info *Info, err error) error {
    info.Name = "Hao Chen"
    info.Namespace = "MegaEase"
    info.OtherThings = "We are running as remote team."
    return nil
  }
  v.Visit(loadFile)
}
```

- 代码的输出如下：

```
LogVisitor() before call function
NameVisitor() before call function
OtherThingsVisitor() before call function
==> OtherThings=We are running as remote team.
OtherThingsVisitor() after call function
==> Name=Hao Chen, NameSpace=MegaEase
NameVisitor() after call function
LogVisitor() after call function
```

- 上面的代码的功效：
  - 解耦了数据和程序
  - 使用了修饰器模式
  - 做出了 Pipeline 的模式

- 我们可以用修饰器模式来重构一下上面的代码

```go
type DecoratedVisitor struct {
  visitor    Visitor
  decorators []VisitorFunc
}

func NewDecoratedVisitor(v Visitor, fn ...VisitorFunc) Visitor {
  if len(fn) == 0 {
    return v
  }
  return DecoratedVisitor{v, fn}
}

// Visit implements Visitor
func (v DecoratedVisitor) Visit(fn VisitorFunc) error {
  return v.visitor.Visit(func(info *Info, err error) error {
    if err != nil {
      return err
    }
    if err := fn(info, nil); err != nil {
      return err
    }
    for i := range v.decorators {
      if err := v.decorators[i](info, nil); err != nil {
        return err
      }
    }
    return nil
  })
}
```

- 代码就可以这样运作了：

```go
func main() {
  info := Info{}
  var v Visitor = &info
  v = NewDecoratedVisitor(v, NameVisitor, OtherVisitor)

  loadFile := func(info *Info, err error) error {
    info.Name = "Hao Chen"
    info.Namespace = "MegaEase"
    info.OtherThings = "We are running as remote team."
    return nil
  }
  v.Visit(loadFile)
}
```
