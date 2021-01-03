## go build 和 go install

### 作用

go build: 用于生成可执行文件(有main包)，生成的可执行文件放在当前目录下

go install: 用于生成库或者可执行文件，

            1、用来生成静态库(无main包)，编译后的包文件放在$GOPATH/pkg/目录下

            2、生成可执行文件(有main包)，编译后的可执行文件放在$PATH/bin/目录下

### 相同点

都可以生成可执行文件

### 不同点

+ go build不可以生成静态库文件

+ go build生成的可执行文件在当前目录下，go install生成的可执行文件在$PATH/bin目录下

### 用法(当前目录为$PATH目录)

bin/ pkg/ src/三个目录

src/目录下有main.go和pack/目录， pack1/目录下有pack1.go

```
cd src/
go install pack1
go install main.go
```

或者：

```
cd src/
go install pack1
go build main.go

```

