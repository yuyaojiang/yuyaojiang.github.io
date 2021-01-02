## os package 总结

### sys.go

提供接口os.Hostname(),返回系统主机名，linux系统下的/proc/sys/kernel/Hostname

```
package main

import (
    "fmt"
    "os"
)

func main() {
    fmt.Println("hostname is", os.Hostname)
}

```

结果为：

hostname is yuyaojiang-ubuntu

![](./picture/os.hostname.png)

