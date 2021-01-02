## go log 包学习笔记

### log.SetFlags() 设置Log格式
```
const (
	Ldate         = 1 << iota     //日期示例： 2009/01/23
	Ltime                         //时间示例: 01:23:23
	Lmicroseconds                 //毫秒示例: 01:23:23.123123.
	Llongfile                     //绝对路径和行号: /a/b/c/d.go:23
	Lshortfile                    //文件和行号: d.go:23.
	LUTC                          //日期时间转为0时区的
	LstdFlags     = Ldate | Ltime //Go提供的标准抬头信息
)
```

其中Lshortfile和Llongfile如果同时设置，Lshortfile生效；Ltime和Lmicroseconds同时设置，Lmicroseconds生效

示例：

```
package main

import (
    "log"
)

func init() {
    log.SetFlags(log.Ldate | log.Lmicroseconds | log.Lshortfile)
}
func main() {
    log.Println("yuyaojiang", "learning go log package.")
    log.Println("huangli", "learning go log package.")
    log.Fatalln("yuzuqiqi") 
}

```

输出：
```
2021/01/01 16:06:03.726212 main.go:11: yuyaojiang learning go log package.
2021/01/01 16:06:03.726629 main.go:12: huangli learning go log package.
```

### log.SetPrefix 设置log前缀

```log.SetPrefix([yuyaojiang-go])```

示例：

```
func init() {
    log.SetPrefix("[yuyaojiang-go]")
    log.SetFlags(log.Ldate | log.Lmicroseconds | log.Lshortfile)
}
```

输出：

```
[yuyaojiang-go]2021/01/01 16:09:06.832690 main.go:12: yuyaojiang learning go log package.
[yuyaojiang-go]2021/01/01 16:09:06.833079 main.go:13: huangli learning go log package.
```

### 定制log

日志记录的根本就在于一个日志记录器Logger，所以我们定制自己的日志，其实就是创建不同的Logger

```
package main

import (
	"io"
	"log"
	"os"
)

var (
	Info  *log.Logger
	Warn  *log.Logger
	Error *log.Logger
)

func init() {
	errFile, err := os.OpenFile("/tmp/error.log", os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0666)
	if err != nil {
		log.Fatalln("open file error:", err)
	}
	Info = log.New(os.Stdout, "Info: ", log.Ldate|log.Lmicroseconds|log.Lshortfile)
	Warn = log.New(os.Stdout, "Warn: ", log.Ldate|log.Lmicroseconds|log.Lshortfile)
	Error = log.New(io.MultiWriter(os.Stderr, errFile), "Error: ", log.Ldate|log.Lmicroseconds|log.Lshortfile)

	Info.SetPrefix("[yuyaojiang-go]")
	Warn.SetPrefix("[yuyaojiang-go]")
	Error.SetPrefix("[yuyaojiang-go]")
}
func main() {
	Info.Println("yuyaojiang", "learning go log package.")
	Warn.Println("huangli", "learning go log package.")
	Error.Println("yuzuqiqi")
}
```

