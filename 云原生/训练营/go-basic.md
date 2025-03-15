# 1.Go 基础回顾

## 1.0 前言

> 微服务架构 12 元素

## 1.1 Go语言特性

### 1.1.1 Go 语言原则

Less is More

- Go 语言是一个可以'编译高效'、‘支持高并发’、‘面向垃圾回收的’ 语言。
  - 秒级完成大型程序的单节点编译
  - 依赖管理清晰
  - 不支持继承、程序员无需花费精力定义不同类型之间的关系
  - 支持垃圾回收、支持并发执行，支持多线程通讯
  - 对多核计算机支持友好

- Go 语言不支持的特性
  - 不支持函数重载和操作符重载
  - 不支持隐士转换
  - 支持接口抽象，不支持继承
  - 不支持动态加载代码
  - 不支持动态链接库
  - 通过recover 和panic 代替异常机制

### 1.1.2 Go 环境搭建

- Go 安装以及源代码位置

- 二进制文件安装

- 环境变量
  - GOROOT
    - go 的安装目录

  - GOPATH
    - src: 存放源代码
    - pkg: 存放依赖包
    - bin: 存放可执行文件

  - 其他常用环境变量
    - GOOS、GOMODULE、GOPROXY
    - 国内用户建议设置 goproxy:export GOPROXY=https://goproxy.cn

- IDE 设置(VS CODE)

    - 下载并安装VisualStudioCode https://code.visualstudio.com/download    

    - 安装Go语言插件 https://marketplace.visualstudio.com/items?itemName=golang.go

### 1.1.3 Go 基本命令

#### 1.基本命令

bug       start a bug report
build     compile packages and dependencies
clean     remove object files and cached files
doc       show documentation for package or symbol 
env       print Go environment information
fix       update packages to use new APIs
fmt       gofmt (reformat) package sources
generate  generate Go files by processing source
get       add dependencies to current module and install them

#### 2.常用命令

install   compile and install packages and dependencies
list      list packages or modules
mod       module maintenance
run       compile and run Go program
test      test packages
tool      run specified go tool
verison   print Go version
vet       report likely mistakes in packages

可以使用 'go help' 查看所有命令基本使用.

#### 3. go build

- go 语言不支持动态链接，因此编译时会将所有依赖编译到二进制文件。

- 指定输出目录
  - go build -o main .

- 常用环境变量设置编译操作系统和CPU架构
  - GOOS=linux GOARCH=amd64 go build

#### 4. go test

Go 语言原生自带单元测试包.

```

```