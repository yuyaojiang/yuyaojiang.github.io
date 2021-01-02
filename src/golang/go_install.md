# go 1.5版本以上的安装：

编译GO1.5版本以上的需要依赖GO1.4版本的二进制，并且需要把GOROOT_BOOTSTRAP的路径设置为1.4版本GO的根目录，这样它的bin目录就可以

先安装go1.4版本，设置GOROOT_BOOTSTRAP，再安装go1.5以上版本


## 设置环境变量

```
vim ~/.bashrc

//添加下面内容
export GOROOT=$HOME/go
export PATH=$PATH:$GOROOT/bin
export GOPATH=$HOME/workspace/go
export GOROOT_BOOTSTRAP=$HOME/go1.4

```

## 编译go1.4版本

```
//下载go源码
git clone git@github.com:golang/go.git

cd go
//创建一个本地分支1.4，用来跟踪远程分支origin/release-branch.go1.4，并且切换到该分支
git checkout -b 1.4 origin/release-branch.go1.4

cd src/

./all.bash

```

## 拷贝~/go/目录下的文件到 ~/go1.4/目录下（默认的$GOROOT_BOOTSTRAT）

```
cd ~

mkdir go1.4

cp go/* go1.4/ -r

```

## 编译go1.9版本

```
cd go

//清楚之前的编译产出物
git clean -dfx
//创建一个本地分支1.9，用来跟踪远程分支origin/release-branch.go1.9，并且切换到该分支
git checkout -b 1.9 origin/release-branch.go1.9

cd src/

./all.bash

```

## 安装完成，查看go版本 ``` go version ```

