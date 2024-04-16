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

