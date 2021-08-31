## GoLang 初识

### 可见性规则

**使用`大小写`来决定常量、变量、类型、接口、结构或函数是否可以被外部所调用：**

- 函数名首字母小写，即为private
- 函数名首字母大写，即为public

### 数据类型

[参考源一](https://www.jianshu.com/p/ea17fa167585)

#### 基础数据类型

**布尔型、整型、浮点型、复数型、字符型、字符串型、错误类型**

##### 布尔类型 (bool)

值：true 和 false，默认值为 false

```go
package main
import "fmt"

func main() {
    var v1, v2 bool         // 声明变量，默认值为 false
    v1 = true               // 赋值
    v3, v4 := false, true   // 声明并赋值

    fmt.Print("v1:", v1)   // v1 输出 true
    fmt.Print("\nv2:", v2) // v2 没有重新赋值，显示默认值：false
    fmt.Print("\nv3:", v3) // v3 false
    fmt.Print("\nv4:", v4) // v4 true
}
```

##### 整型

有符号 （int8、int16、int32、int64）

无符号 （uint8、uint16、uint32、uint64）

特殊整型 （可能在不同平台上存在差异）

- uint，32位操作系统就是uint32，64位操作系统就是uint64
- int，32位操作系统就是int32，64位操作系统就是int64
- uintptr，无符号整型，用于存放指针

```go
func main() {
    // 无符号整形，默认值都是0
    var u8 uint8
    var u16 uint16
    var u32 uint32
    var u64 uint64
    fmt.Printf("u8: %d, u16: %d, u32: %d, u64: %d\n", u8, u16, u32, u64) // 默认值都为0
    u8 = 255
    u16 = 65535
    u32 = 4294967295
    u64 = 18446744073709551615
    fmt.Printf("u8: %d, u16: %d, u32: %d, u64: %d\n", u8, u16, u32, u64)

    // 整型
    var i8 int8
    var i16 int16
    var i32 int32
    var i64 int64
    fmt.Printf("i8: %d, i16: %d, i32: %d, i64: %d\n", i8, i16, i32, i64) // 默认值都为0
    i8 = 127
    i16 = 32767
    i32 = 2147483647
    i64 = 9223372036854775807
    fmt.Printf("i8: %d, i16: %d, i32: %d, i64: %d\n", i8, i16, i32, i64)

    // int 型，取值范围32位系统为 int32，64位系统为 int64，取值相同但为不同类型
    var i int
    //i = i32 // 报错，编译不通过，类型不同
    //i = i64 // 报错，编译不通过，类型不同
    i = -9223372036854775808
    fmt.Println("i: ", i)
    
    // 指针地址
    var p uintptr
    fmt.Printf("p: %v\n", p) // 默认值为0
    p = 18446744073709551615 // 64位系统最大值
    //p = 18446744073709551616 // 报错：超出最大值
    fmt.Printf("p: %v\n", p)
}
```

##### 浮点型

（float32、float64）

```go
func main() {
    // 浮点型，f32精度6位小数，f64位精度15位小数
    var f32 float32
    var f64 float64
    fmt.Printf("f32: %f, f64: %f\n", f32, f64) // 默认值都为 0.000000
    f32 = 1.12345678
    f64 = 1.12345678901234567
    fmt.Printf("f32: %v, f64: %v\n", f32, f64) // 末位四舍五入，输出：f32: 1.1234568, f64: 1.1234567890123457   
}
```

##### 复数型

（复数有实部和虚部，complex64的实部和虚部为32位，complex128的实部和虚部为64位）

```go
func main() {
    // 复数型
    var c64 complex64
    var c128 complex128
    fmt.Printf("c64: %v, c128: %v\n", c64, c128) // 实数、虚数的默认值都为0
    c64 = 1.12345678 + 1.12345678i
    c128 = 2.1234567890123456 + 2.1234567890123456i
    fmt.Printf("c64: %v, c128: %v\n", c64, c128) // 输出：c64: (1.1234568+1.1234568i), c128: (2.1234567890123457+2.1234567890123457i)
}
```

##### 字符型

uint8类型，或者叫byte型，代表`ASCII` 码的一个符号

rune类型，代表一个`UTF-8` 符号

```go
func main() {
    // 字符型
    var b byte                                       // uint8 别名
    var r1, r2 rune                                  // uint16 别名
    fmt.Printf("b: %v, r1: %v, r2: %v\n", b, r1, r2) // 默认值为0
    b = 'a'
    r1 = 'b'
    r2 = '字'
    fmt.Printf("b: %v, r1: %v, r2: %v\n", b, r1, r2) // 输出：b: 97(ASCII表示的数), r1: 98(utf-8表示的数), r2: 23383 (utf-8表示的数)

    b = u8
    r1 = i32
    fmt.Printf("b: %v, r1: %v\n", b, r1) // 输出：b: 255, r1: 2147483647
}
```

##### 字符串型

字符串以原生数据类型，出现内部实现使用`UTF-8`编码

```go
func main() {
    var str1 string // 默认值为空字符串 ""
    str1 = `hello world`
    str2 := "你好世界"

    str := str1 + " " + str2 // 字符串连接
    fmt.Println(str1)
    fmt.Println(str2)
    fmt.Println(str) // 输出：hello world 你好世界
    // 遍历字符串
    l := len(str)
    for i := 0; i < l; i++ {
        chr := str[i]
        fmt.Println(i, chr) // 输出字符对应的编码数字
    }
}
```



#### 复合数据类型

**指针、数组、切片、字典、通道、结构体、接口**

