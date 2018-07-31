### 源文件

每个源文件都属于包的一部分，在文件头部用 `package` 声明所属的包名称。以 “.go” 作为文件扩展名，语句结尾分号会被省略，入口函数 main 没有参数，且必须放在 `main` 包下。

**test.go**

```
package main

// import "fmt"

func main(){
	// fmt.Println("hello world")
	println("hello world")
}
```

**运行**

```
go run test.go
hello world
```

### 变量

使用 `var` 定义变量，支持类型推断。基础数据类型划分清晰明确，有助于编写跨平台应用。编译器确保变量总是被初始化为零值。

```
// 设置变量 x 类型为 int32 初始化零值
var x int32
// 设置变量 s 推断类型为 string
var s = "hello world"
// 使用更简单的定义模式 省略 var := 中间不允许出现空格
z := 12
// 一次定义多个变量
a, b, c := 1, 2.0, "c"
```

> 编译器将未被使用的局部变量定义当作错误。

### 表达式

GO 仅有三种流控制语句

**if**

```
func main() {
	x := 100
	if x > 0 {
		println("> 0")
	} else if x < 0 {
		println("< 0")
	} else {
		println("=0")
	}
}
```

**switch**

```
package main

func main() {
	x := 100
	switch {
	case x > 0:
		println("> 0")
	case x< 0:
		println("< 0")
	default:
		println("0")
	}
}
```

**for**

```
package main

func main() {
	// 普通循环
	for i := 0; i < 5; i++ {
		println(i)
	}
	for i := 4; i >= 0; i-- {
		println(i)
	}

	// while (x < 5) { ...}
	x := 0
	for x < 5 {
		println(x)
		x++
	}

	// while (true) { ... }
	y := 4
	for {
		println(y)
		y--
		if y < 0 {
			break
		}
	}
	// 迭代遍历 还可以返回索引 i 是索引

	z := []int{1, 2, 3}
	for i, n := range z {
		println(i, ":", n)
	}
}
```

### 函数

函数可以定义多个返回值，并对其命名。函数是第一类型，可作为参数或返回值。用 `defer` 定义延迟调用，无论函数是否出错，都会确保结束前被调用。

```
package main

import "github.com/pkg/errors"

func main() {
	a, b := 1, 2
	// 接收多个返回值
	c, err := test1(a, b)
	println(c, err)

	// 接受函数类型
	f := test2(4)
	// 执行函数方法
	f()

	test3(1, 0)
}

/*
func 为函数声明
test1 为函数名
a, b 为函数参数列表
int, error 为函数返回值
 */

func test1(a, b int) (int, error) {
	if b == 0 {
		return 0, errors.New("b by zero")
	}
	return a / b, nil
}

// 返回值为函数类型
func test2(x int) func() {
	return func() { // 匿名函数
		println(x) //闭包
	}
}

// 无返回值的函数
func test3(a, b int) {
	/*
	使用关键字 defer 定义延迟调用
	始终会在结束前被调用
	多个 defer 执行顺序为 FILO（先定的后执行）
	 */
	defer println("defer 1")
	defer println("defer 2")
	println(a / b)
}

```