---
title: 编译数据库简介
description: 
published: true
date: 2023-01-31T16:24:22.133Z
tags: 
editor: markdown
dateCreated: 2023-01-29T14:39:23.226Z
---

[编译数据库](https://clang.llvm.org/docs/JSONCompilationDatabase.html)是一个json格式，它提供了编译源文件时的命令、参数、源文件、工作目录等信息，可以基于编译数据库对C/C++代码进行分析。一般的生成文件名为`compile_commands.json`。

这里包含对[Compilation database — Sarcasm notebook](https://sarcasm.github.io/notes/dev/compilation-database.html)的整理。

主流的支持基于编译数据库分析的工具有：[clang-check](http://clang.llvm.org/docs/ClangCheck.html)，[clang-doc](https://clang.llvm.org/extra/clang-doc.html)，[clang-include-fixer](http://clang.llvm.org/extra/clang-include-fixer.html)，[clang-rename](http://clang.llvm.org/extra/clang-rename.html)，[clang-tidy](http://clang.llvm.org/extra/clang-tidy)，[clangd](https://clangd.llvm.org)。

## 格式简介 <a href="#lodyz" id="lodyz"></a>

生成的编译数据库形如：

```json
{ "directory": "/home/user/llvm/build",
  "arguments": ["/usr/bin/clang++", "-Irelative", "-DSOMEDEF=With spaces, quotes and \\-es.", "-c", "-o", "file.o", "file.cc"],
  "file": "file.cc" },

{ "directory": "/home/user/llvm/build",
  "command": "/usr/bin/clang++ -Irelative -DSOMEDEF=\"With spaces, quotes and \\-es.\" -c -o file.o file.cc",
  "file": "file2.cc" },

]
```



注意到这里有`arguments`和`command`两种输出形式，一般认为`arguments`结果要好。

TODO

## 如何获得项目的编译数据库 <a href="#b9c51365" id="b9c51365"></a>

### 使用cmake构建项目 <a href="#jxjtb" id="jxjtb"></a>

原来是`cmake`的地方替换成`cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=1`，另一种方案是在`CMakeLists.txt`文件中添加

```
set(CMAKE_EXPORT_COMPILE_COMMANDS 1)
```

### 使用bazel构建项目 <a href="#77b58e45" id="77b58e45"></a>



* [github.com/google/kythe: tools/cpp/generate\_compilation\_database.sh](https://github.com/kythe/kythe/blob/f215df07e18d1d99535a2839b197a81130fcfd90/tools/cpp/generate\_compilation\_database.sh) -- google出品
* [github.com/hedronvision/bazel-compile-commands-extractor: Hedron’s Compile Commands Extractor for Bazel](https://github.com/hedronvision/bazel-compile-commands-extractor) -- clangd推荐
* [grailbio/bazel-compilation-database: Tool to generate compile\_commands.json from the Bazel build system (github.com)](https://github.com/grailbio/bazel-compilation-database)



### 使用ninja构建项目 <a href="#424a9f37" id="424a9f37"></a>

使用如下格式生成。

```
ninja -t compdb > compile_commands.json
```



### 使用clang编译单个文件 <a href="#cddb4d3e" id="cddb4d3e"></a>

使用类似`clang -MJ c_struct.o.json c_struct.c -o c_struct.o`的选项生成。生成的格式形如下。注意如下几个细节：

* 生成文件的末尾的`,`，这并不是标准的json；同时缺乏通常的编译数据库生成的`[]`。因此，基于这种方案生成的编译数据库文件需要手动调整（[这里](https://github.com/hongxuchen/dotfiles/blob/master/\_bin/llvm/wrap\_cdb.py)提供一个自动化脚本）。
* `arguments`中`clang`的命令被替换成非软链接的绝对路径，且添加上了一些选项如`-xc`，`--target=x86_64-pc-linux-gnu`。这样的编译数据库，是使用了clang编译器而不是driver。
* 除了`arguments`还有`output`项，且`/tmp/c_struct-d6903c.o`和`c_struct.o`并不一样。

```
{ "directory": "/root", "file": "c_struct.c", "output": "/tmp/c_struct-3faa5e.o", "arguments": ["/usr/lib/llvm-14/bin/clang", "-xc", "c_struct.c", "--target=x86_64-pc-linux-gnu"]},
```

### 使用bear进行wrap <a href="#41024d6f" id="41024d6f"></a>

使用[bear build](https://github.com/rizsotto/Bear)。[这个回复](https://github.com/rizsotto/Bear/issues/196#issuecomment-359655816)中提到不建议使用`scan-build`中的`intercept-build`。

#### 优势 <a href="#f094dd87" id="f094dd87"></a>

* 可以搞定任何构建命令的编译数据库生成问题。
* 生成的编译数据库使用`arguments`而不是`command`，避免了转义的问题（但很多对编译数据库的分析工具可能仅支持`command`项）。

#### 不足 <a href="#7f56244b" id="7f56244b"></a>

* 由于bear仅监控本次_实际_构建的命令，因此如果是项目的增量式构建则只会输出部分编译数据库结果，但全量构建可能会非常耗时。经常会出现，"全版本动辄近一个小时，很影响效率，大部分时间都浪费在等待编译结束"。

### 使用compiledb对make进行wrap（推荐） <a href="#91704bc8" id="91704bc8"></a>

[compiledb](https://github.com/nickdiego/compiledb)有所有bear的优势，且弥补了bear的不足；性能方面也有优势。

改进之处包括：

1. 可以跳过时间构建而只生成`compile_commands.json`，如`compiledb -n make`。
2. 可以基于build-log来生成编译数据库。

```
make -Bnwk > build-log.txt
compiledb --parse build-log.txt # 或 compiledb < build-log.txt
# 使用管道的方式也行
make -Bnwk | compiledb -o-
```

1. 使用`command`风格的编译数据库：

```
compiledb --command-style make # 根据我的实验，在已经构建完成之后使用该命令，仍然能够得到编译数据库
```

不足：

* 仅能够对`make`指令进行wrap。

```
# 可编译
> gcc testnum.c main.c
# bear可wrap
> bear -- gcc testnum.c main.c
#compiledb不可wrap
> compiledb  gcc testnum.c main.c
Usage: compiledb [OPTIONS] COMMAND [ARGS]...
Try 'compiledb -h' for help.

Error: No such command 'gcc'.
```

## TODO： <a href="#spqut" id="spqut"></a>

* 补全`citnames`及`intercept`命令行的使用（bear相关）。
* 补全交叉编译时的问题。
* 补全编译数据库供基于clang的分析工具时的局限性。