# 编译为C语言可用的.so文件

go1.5后可编译动态链接库，在C中调用。

## demo
```go
package main

// <preamble>
import "C"

import "fmt"

//export hello
func hello(s *C.char) {
	fmt.Println("hello,", C.GoString(s))
}

func main() {}
```

```C++
#include "libhello.h"

int main() {
    hello("world");
}
```

## `//export xxx`
导出 Go 函数供 C 代码使用，注意`//` 与 `export` 间不包含空格。


## preamble
`import "C"` 前的注释称为 *preamble*，使用C编写，编译时将放入头文件。

包含 `//export` 的文件将被复制到两个不同的 C 输出文件中，因此 preamble 中不能包含定义，只能包含声明。必须将定义放在其他文件或 C 源文件的前导码中。

**Refrence**: 
- [cgo 命令](https://pkg.go.dev/cmd/cgo)
- [C? Go? Cgo!](https://go.dev/blog/cgo)