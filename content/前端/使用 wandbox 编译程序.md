---
tags:
  - web
---

有时我们需要创建一个在线编程应用，可以编译、运行不同的编程语言，但是，又没有一个服务器用于配置编程环境；有时我们需要在无编程环境的前提下，测试运行代码；这时可以选择 `WandBox`。

[WandBox](https://wandbox.org/) 是一个在线编译项目，支持绝大多数语言。不仅可以在其官网上进行编程测试，还可以通过 Web API，进行访问。不过，其 Web API 一直没有一个比较完善的官方文档。最好的办法是，在官网上运行一个程序，然后在开发者面板中，查看新生成的 `POST` 请求，分析其内容。

## Web API 解析

我们先来探索最基本的情形：只有一个文件，以 `C++` 语言为例，其 `POST` 请求头为：

```
{"compiler":"gcc-13.2.0","title":"Test","description":"","code":"#include <iostream>\n\nint main() {\n    std::cout << \"Hello, world\" << std::endl;\n    return 0;\n}","codes":[],"options":"warning,boost-1.83.0-gcc-13.2.0,gnu++2b,cpp-no-pedantic","stdin":"","compiler-option-raw":"","runtime-option-raw":""}
```

我们逐个分析：

1. `compiler` 选项：当前的编译器版本
2. `title` 选项：当前项目的标题，可忽略
3. `code` 选项：代码内容
4. `codes` 选项：如果有多个文件，则需要写这个选项
5. `options` 选项：语言相关，对于 `C++` 而言，需要关注的是 `warning` 和 `boost` 两个选项
6. `stdin` 选项：标准输入（模拟程序运行时用户的输入）
7. `compiler-option-raw` 选项：自定义的编译选项
8. `runtime-option-raw` 选项：未知，也没有用到过。

请求的地址为：https://wandbox.org/api/compile.ndjson

## 回复内容解析

回复内容有一个标准的 `response`，关键在于其内容，其内容为一个 `ReadableStream`，典型包含以下内容：

```json
{"type":"Control","data":"Start"}
{"type":"StdOut","data":"Hello, world\n"}
{"type":"ExitCode","data":"0"}
{"type":"Control","data":"Finish"}

{"type":"Control","data":"Start"}
{"type":"StdErr","data":"prog.exe: prog.cc:5: int main(): Assertion `1 == 2' failed.\n"}
{"type":"ExitCode","data":"134"}
{"type":"Control","data":"Finish"}


{"type":"Control","data":"Start"}
{"type":"CompilerMessageE","data":"prog.cc: In function 'int main()':\nprog.cc:7:9: warning: unused variable 'x' [-Wunused-variable]\n    7 |     int x;\n      |         ^\n"}
{"type":"ExitCode","data":"0"}
{"type":"Control","data":"Finish"}


{"type":"Control","data":"Start"}
{"type":"CompilerMessageE","data":"prog.cc: In function 'int main()':\nprog.cc:8:5: error: expected initializer before 'return'\n    8 |     return 0;\n      |     ^~~~~~\n"}
{"type":"ExitCode","data":"1"}
```

可以看到，主要以下几种类型：

1. `Control`：用于展示任务状态，任务的开始与结束
2. `StdOut`: 标准输出的内容
3. `ExitCode`: 程序运行退出状态码
4. `CompilerMessageE`: 包含编译器的错误信息和警告信息

## 多文件支持

`Wandbox` 会将 `POST` 请求中的 `code` 字段的内容，放入到一个名为 `prog` 的主程序当中，后缀名会根据语言进行变换。如果 `codes` 字段有其他内容，则会将其写入到文件系统中进行编译。`codes` 字段是一个数组，其中每个元素都是一个 `Object`，其内容为：

```json
{
  "file": "File Title",
  "code": "File content"
}
```

其中，`file` 字段是文件名称，`code` 字段是文件内容。文件名称支持空格以及目录。例如，如果想在 `include` 文件夹中创建一个 `header.hpp` 文件，则需要将文件名称写为：`include/header.hpp`

注意，多文件编译需要在 `compiler-option-raw` 字段中进行设置。`compiler-option-raw` 会将每一行看作一个参数，并合并在一起。因此，如果想要编译 `prog.cc` 和 `lib.cpp`、`extra.cpp` 这三个文件，则需要将 `compiler-option-raw` 设置为：

```
lib.cpp
extra.cpp
```

注意，是两行内容，不要放到一行，不然会出错。
