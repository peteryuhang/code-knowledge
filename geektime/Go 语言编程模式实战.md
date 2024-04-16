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
