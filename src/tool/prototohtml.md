# proto2html

## step1: go 安装 

linux下安装go参看：[go安装](https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/01.1.md)

## step2: 安装protoc-gen-doc

下载protoc-gen-doc release 版本就可以，[protoc-gen-doc release 版本](https://github.com/pseudomuto/protoc-gen-doc/releases/download/v1.3.2/protoc-gen-doc-1.3.2.linux-amd64.go1.12.6.tar.gz)
下载放在/usr/local/go/bin/目录下

## step3: 安装sphinx (事先安装python和pip)

```
    #pip install -U sphinx
```

## step4: 安装插件

```
    #pip install recommonmark
    #pip install sphinx_markdown_tables
```

## step5: 设置临时环境变量

```
    $export SRC_DIR=/media/sf_workspace/ppf_libpub/rg-protobuf/trunk/code/rg_protobuf_rt/IDL/
    $export OUT_DIR=/media/sf_workspace/sphinx/sphinx_proto/source/
```

## step6: 使用protoc和protoc-gen-doc工具通过proto文件生成.md文档

```
    $find $SRC_DIR -type f -name "*.proto" | xargs protoc --doc_out=$OUT_DIR --doc_opt=markdown,proto.md -I$SRC_DIR
```

## step7: 利用sphinx将md文档转换成html

```
    $make html
```

