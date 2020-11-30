# codeviz工具生成函数调用图

CodeViz是《Understanding The Linux Virtual Memory Manager》的作者 Mel Gorman 写的一款分析C/C++源代码中函数调用关系的open source工具（类似的open source软件有 egypt、ncc）。其基本原理是给 GCC 打个补丁（如果你的gcc版本不符合它的要求还得先下载正确的gcc版本），让它在编译每个源文件时 dump 出其中函数的 call graph，然后用 Perl 脚本收集并整理调用关系，转交给Graphviz绘制图形（Graphviz属于后端，CodeViz属于前端）。

## Graphviz + CodeViz编译安装

### graphviz安装
```
apt install  graphviz
```

### codeviz安装

+ 1、下载codeviz源码：[codeviz-1.0.12](https://fossies.org/linux/privat/old/codeviz-1.0.12.tar.gz/)

+ 2、解压 ```tar -xzf codeviz-1.0.12.tar.gz```

+ 3、codeviz-1.0.12依赖于gcc-4.6.2版本 下载gcc-4.6.2源码 [gcc-4.6.2](https://ftp.gnu.org/gnu/gcc/gcc-4.6.2/gcc-4.6.2.tar.gz)

+ 4、将压缩包放到codeviz-1.0.12/compilers/目录下

+ 5、编译
```
cd codeviz-1.0.12/compilers/
rm -rf gcc-graph/objdir
mkdir -p gcc-graph/objdir

解压gcc并打patch:
```
tar -zxf gcc-4.6.2.tar.gz -C gcc-graph
cd gcc-graph/gcc-4.6.2
patch -p1 < ../../gcc-patches/gcc-4.6.2-cdepn.diff
```
修改gcc的依赖mpfr,gmp,mpc的软连接方式为移动目录
```
cd contrib
vim download_prerequisites
```
修改项：
```
wget ftp://gcc.gnu.org/pub/gcc/infrastructure/$MPFR.tar.bz2 || exit 1
tar xjf $MPFR.tar.bz2 || exit 1
#ln -sf $MPFR mpfr || exit 1
mv $MPFR ../mpfr

wget ftp://gcc.gnu.org/pub/gcc/infrastructure/$GMP.tar.bz2 || exit 1
tar xjf $GMP.tar.bz2  || exit 1
#ln -sf $GMP gmp || exit 1
mv $GMP ../gmp

wget ftp://gcc.gnu.org/pub/gcc/infrastructure/$MPC.tar.gz || exit 1
tar xzf $MPC.tar.gz || exit 1
#ln -sf $MPC mpc || exit 1
mv $MPC ../mpc
```

##进入objdir目录，configure
``` 
cd ../../objdir
../gcc-4.6.2/configure --prefix=/home/yuyaojiang/gcc-graph --enable-shared --enable-languages=c,c++

make make bootstrap CXXFLAGS=-fPIC CFLAGS=-fPIC -j4
```

## 待补充




