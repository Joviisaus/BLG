---
title: 宏定义小馋猫
date: 2024-10-12 14:47:00
tags: [报错, 经验]
catagories: 记录
keywords: Macro, Debug , Compiler
top_img: "/image/blog-placeholder-4.jpg"
comments: true
cover: "/image/blog-placeholder-4.jpg"
ai: true
swiper_index: 6
top_group_index: 6
---

# 问题的出现

基于对样条的突然感兴趣和样条函数编码经验的匮乏之间的矛盾，最近突发奇想想做一个比较通用的可视化工具，能加到系统设置里随时方便的查看常用的 ’.stp','.m','.hex' 等文件。于是重新拿起**GLFW**来做可视化界面。最开始是neovim + CMake + Mingw的工作流。报一个静态lib库的问题，初步判断是编译器的原因。于是转到visual studio 2022 进一步调试。可奇怪的是原本能编译能汇编的项目显示大量函数未在正确的地方定义😅,于是开始了极具戏剧感的排查过程。

# 排查时出现的意外

本身在命令行使用 **Clang** 编译是没有太大的问题的，可能真就只是缺了一个 lib 的问题，后来实在是排查完没有缺的 lib 才决定使用一个大型 IDE 来查看详细报告。

![MinGw_CMAKE](/image/mh/MinGw_CMAKE.png)

此时的报错如下,如果稍微熟悉一点 **OpenGL** 的话应该知道是其中一个 lib 库依赖于系统内部自带的 **OpenGL32.lib** 库,但这不是重点。

```bash
lld-link: error: undefined symbol: __declspec(dllimport) wglGetProcAddress
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:262
>>>               soil2-debug.lib(SOIL2.obj):($LN5)

lld-link: error: undefined symbol: __declspec(dllimport) glBindTexture
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2252
>>>               soil2-debug.lib(SOIL2.obj):($LN92)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2599
>>>               soil2-debug.lib(SOIL2.obj):($LN43)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2799
>>>               soil2-debug.lib(SOIL2.obj):($LN14)
>>> referenced 1 more times

lld-link: error: undefined symbol: __declspec(dllimport) glDeleteTextures
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2356
>>>               soil2-debug.lib(SOIL2.obj):($LN92)

lld-link: error: undefined symbol: __declspec(dllimport) glGenTextures
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2249
>>>               soil2-debug.lib(SOIL2.obj):($LN92)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2596
>>>               soil2-debug.lib(SOIL2.obj):($LN43)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2796
>>>               soil2-debug.lib(SOIL2.obj):($LN14)
>>> referenced 1 more times

lld-link: error: undefined symbol: __declspec(dllimport) glGetError
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2601
>>>               soil2-debug.lib(SOIL2.obj):($LN43)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2655
>>>               soil2-debug.lib(SOIL2.obj):($LN43)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2801
>>>               soil2-debug.lib(SOIL2.obj):($LN14)
>>> referenced 1 more times

lld-link: error: undefined symbol: __declspec(dllimport) glGetIntegerv
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:1849
>>>               soil2-debug.lib(SOIL2.obj):($LN16)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:340
>>>               soil2-debug.lib(SOIL2.obj):($LN21)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2606
>>>               soil2-debug.lib(SOIL2.obj):($LN43)
>>> referenced 3 more times

lld-link: error: undefined symbol: __declspec(dllimport) glGetString
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:356
>>>               soil2-debug.lib(SOIL2.obj):($LN21)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:205
>>>               soil2-debug.lib(SOIL2.obj):(int __cdecl isAtLeastGL3(void))

lld-link: error: undefined symbol: __declspec(dllimport) glPixelStorei
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:1852
>>>               soil2-debug.lib(SOIL2.obj):($LN16)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:1861
>>>               soil2-debug.lib(SOIL2.obj):($LN16)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2609
>>>               soil2-debug.lib(SOIL2.obj):($LN43)
>>> referenced 7 more times

lld-link: error: undefined symbol: __declspec(dllimport) glReadPixels
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:1857
>>>               soil2-debug.lib(SOIL2.obj):($LN16)

lld-link: error: undefined symbol: __declspec(dllimport) glTexImage2D
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2308
>>>               soil2-debug.lib(SOIL2.obj):($LN92)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2337
>>>               soil2-debug.lib(SOIL2.obj):($LN92)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2649
>>>               soil2-debug.lib(SOIL2.obj):($LN43)
>>> referenced 5 more times

lld-link: error: undefined symbol: __declspec(dllimport) glTexParameteri
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2369
>>>               soil2-debug.lib(SOIL2.obj):($LN92)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2370
>>>               soil2-debug.lib(SOIL2.obj):($LN92)
>>> referenced by C:\Users\ouyan\Downloads\SOIL2-release-1.20\SOIL2-release-1.20\src\SOIL2\SOIL2.c:2374
>>>               soil2-debug.lib(SOIL2.obj):($LN92)
>>> referenced 35 more times
clang++: error: linker command failed with exit code 1 (use -v to see invocation)
make[2]: *** [CMakeFiles\Viewer.dir\build.make:133: Viewer.exe] Error 1
make[1]: *** [CMakeFiles\Makefile2:82: CMakeFiles/Viewer.dir/all] Error 2
make: *** [Makefile:90: all] Error 2
```

更换开发环境为 **visual studio** 后，出现了一个很奇怪的报错，指的是某个函数在不该定义的地方被定义。

![ERROR](/image/mh/Error.png)

# 新问题的排查

由于这个问题过于奇怪，因此只针对这个报错排查，在 **main.cpp** 里依次删除头文件, 发现注释掉 **<glad.h>** 后可以避免这个错误，由此作为突破口进行排查。

## glad.h 内部被编译出一个左括号

这是最直白的也是最容易被想到的思路，**C++** 里 **#include** 语句的作用就是把一个文件的内容复制粘贴过来而已，除此之外没有其他的功能。因此怀疑是一些误操作导致 **glad.h** 的一个回括号被删除了。

于是花了很多时间在检查这个头文件，但结果似乎不尽如人意，因为这个头文件在语言服务协议下括号是齐全的，而注释掉这个头文件里的头文件依然不能解决问题。最最明显的一点，那就是如果结果是这个头文件的原因导致的括号无法匹配，那没有任何理由在 **clang** 下可以运行。

## 关键的一步

发现去掉一些中文注释，这个报错莫名其妙就没了，于是这个问题也很突然也很莫名其妙的就解决了。但为什么已经被注释掉的内容会导致半个括号的出现或者消失，这和 **glad.h** 又有什么联系呢。

把去掉的中文注释依次恢复，发现是 “定” 这个字符会导致问题，将预编译好的文件输出，可以发现这个 “定” 字吃掉了一个右括号...

![preprocessing](/image/mh/preprocess.png)

![main](/image/mh/main.png)

由此基本可以得出结论了，默认的编译器（应该是 **MinGW** ) 会先进行宏替换，然后再去除注释，这会导致一个问题就是注释内的内容也被宏替换了，因此注释内的内容有可能会和注释外的内容相互影响，最后会导致右括号被吃这样的惨剧。

# 后续

后来排查问题发现是系统自带的 OpenGL32 没有被包括进来，包括其 **lib** 和 **dll** 文件，链接上之后就没有什么问题可以正常运行显示网格了。

![result](/image/mh/result.png)
