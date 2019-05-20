# Windows 编译- [Windows 编译](#windows-%E7%BC%96%E8%AF%91)

- [Windows 编译- Windows 编译](#windows-%E7%BC%96%E8%AF%91--windows-%E7%BC%96%E8%AF%91)
  - [1 准备工作](#1-%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C)
    - [1.1 关于源码](#11-%E5%85%B3%E4%BA%8E%E6%BA%90%E7%A0%81)
    - [1.2 编译准备](#12-%E7%BC%96%E8%AF%91%E5%87%86%E5%A4%87)
      - [1.2.1 生成 config_site.h 文件](#121-%E7%94%9F%E6%88%90-configsiteh-%E6%96%87%E4%BB%B6)
      - [1.2.2 关于 config_site.h 文件](#122-%E5%85%B3%E4%BA%8E-configsiteh-%E6%96%87%E4%BB%B6)
      - [1.2.3 生成 config_site.h 文件的方式](#123-%E7%94%9F%E6%88%90-configsiteh-%E6%96%87%E4%BB%B6%E7%9A%84%E6%96%B9%E5%BC%8F)
    - [1.3 安装](#13-%E5%AE%89%E8%A3%85)
      - [1.3.1 工具和 SDK](#131-%E5%B7%A5%E5%85%B7%E5%92%8C-sdk)
      - [1.3.2 支持视频(2.0及以上版本支持)](#132-%E6%94%AF%E6%8C%81%E8%A7%86%E9%A2%9120%E5%8F%8A%E4%BB%A5%E4%B8%8A%E7%89%88%E6%9C%AC%E6%94%AF%E6%8C%81)
        - [1.3.2.1 其他必要条件](#1321-%E5%85%B6%E4%BB%96%E5%BF%85%E8%A6%81%E6%9D%A1%E4%BB%B6)
        - [1.3.2.2 其他配置](#1322-%E5%85%B6%E4%BB%96%E9%85%8D%E7%BD%AE)
        - [1.3.2.3 主机必要条件](#1323-%E4%B8%BB%E6%9C%BA%E5%BF%85%E8%A6%81%E6%9D%A1%E4%BB%B6)
  - [2 编译工程](#2-%E7%BC%96%E8%AF%91%E5%B7%A5%E7%A8%8B)
    - [2.1 编译示例](#21-%E7%BC%96%E8%AF%91%E7%A4%BA%E4%BE%8B)
    - [2.2 编译遇到的问题](#22-%E7%BC%96%E8%AF%91%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98)
      - [2.2.1 FF_INPUT_BUFFER_PADDING_SIZE undefined identifier](#221-ffinputbufferpaddingsize-undefined-identifier)
      - [2.2.2 avcodec.lib(dplrs00034.o) : error LNK2026: 模块对于 SAFESEH 映像是不安全的](#222-avcodeclibdplrs00034o--error-lnk2026-%E6%A8%A1%E5%9D%97%E5%AF%B9%E4%BA%8E-safeseh-%E6%98%A0%E5%83%8F%E6%98%AF%E4%B8%8D%E5%AE%89%E5%85%A8%E7%9A%84)
      - [2.2.3 msys.bat cannot find the rxvt.exe or sh.exe binary -- aborting](#223-msysbat-cannot-find-the-rxvtexe-or-shexe-binary----aborting)
  - [3 调试示例应用](#3-%E8%B0%83%E8%AF%95%E7%A4%BA%E4%BE%8B%E5%BA%94%E7%94%A8)
  - [参考](#%E5%8F%82%E8%80%83)

## 1 准备工作

### 1.1 关于源码

- 获取[源码](https://www.pjsip.org/download.htm)
- 顶层目录($TOP)包含下面的子目录
  - $TOP/build: 包含 Makefile 文件，适用于所有工程
  - $TOP/build.symbian: 包含编译 Symbian 目标的 MMP 文件
  - $TOP/doc:
  - $TOP/lib:
  - $TOP/pjlib: 包含 PJLIB 的头文件和源文件。PJLIB 是基本的可移植框架库，被其他库使用
  - $TOP/pjlib-util: 包含 PJLIB-UTIL 头文件和源文件。PJLIB-UTIL 是一个辅助库，包含了一些工具函数，比如 scanner/xml/STUN/MD5/getopt 等
  - $TOP/pjmedia: 包含 PJMEDIA 和 PJMEDIA-CODEC 的头文件和源文件。不同的编解码封装(比如 GSM/Speex/iLBC)可在此目录中找到。编解码库通常会放在 `$TOP/third_party` 下面
  - $TOP/pjnath:包含 PJNATH 的头文件和源文件。PJNATH 包含 STUN/TURN/ICE 实现
  - $TOP/pjsip: 包含 PJSIP 的头文件和源文件。是 SIP 协议栈实现
  - $TOP/pjsip-apps: 包含 PJSUA 和一些示例应用的源码，包括 Python 封装
  - $TOP/tests: 包括库的一些单元测试
  - $TOP/third_party: 包含第三方库的源码(比如 GSM/Speex/iLBC 编解码)
- 每个工程的文件目录
  - bin: 编译生成的二进制文件
  - build: 包含编译过程的编译脚本、Makefile 文件、工程文件、工程工作区。它包括一个 Makefile 文件在 GNU 系统下编译工程，一个 *.dsw 工作区文件来编译 Visual Studio 6 或之后的版本
  - build/output: 包含编译生成的目标文件和其他文件。为了支持在同一个源文件树下编译生成不同的目标文件，每个编译目标会占据当前目录下的一个子目录
  - docs: 包含 Doxygen 配置文件(doxygen.cfg)通过源码文件生成在线文档。输出文件将放在这个目录下(比如 docs/html 包含 HTML 文件)
    - 为源码树生成 Doxygen 文档，只需要在自己的工程目录下运行 `doxygen docs/doxygen.cgf`，生成的文件会在 docs 目录
  - include: 包含项目头文件
  - lib: 包含编译生成的库
  - src: 包含项目源码文件

### 1.2 编译准备

#### 1.2.1 生成 config_site.h 文件

- 编译源码之前，需要创建 `pjlib/include/pj/config_site.h` 文件
- 可以是空文件
- **注意**：基于 Makefile 的编译系统，这个步骤会由 Makefile 完成

#### 1.2.2 关于 config_site.h 文件

- 文件包含了对库的本地定制
- 所有的定制应该放在这个文件而不是修改 PJ 的文件。因为一旦 PJ 的文件被修改，这些修改的文件在下次源码同步的时候将不会更新。或者 SVN 的刷新会覆盖本地修改
- 文件在版本控制中，因此不会被`svn update`覆盖
- 可以覆盖的配置宏包含在下面的文件中
  - PJLIB 配置 `pjlib/config.h`
  - PJLIB-UTIL 配置 `pjlib-util/config.h`
  - PJMEDIA 配置 `pjmedia/config.h`
  - PJNATH 配置 `pjnath/config.h`
  - PJSIP 配置 `pjsip/config.h`
- 示例的文件可以参考 `pjlib/include/config_site_sample.h`

#### 1.2.3 生成 config_site.h 文件的方式

- 最简单的方式是创建一个空文件，即使用库的默认值
- 创建一个文件，写入类似下面的内容

```c
// Uncomment to get minimum footprint (suitable for 1-2 concurrent calls only)
//#define PJ_CONFIG_MINIMAL_SIZE

// Uncomment to get maximum performance
//#define PJ_CONFIG_MAXIMUM_SPEED

#include <pj/config_site_sample.h>
```

### 1.3 安装

#### 1.3.1 工具和 SDK

- 安装 Microsoft Visual Studio 2015
- 安装 [DirectX SDK](https://www.microsoft.com/en-us/download/details.aspx?id=6812)
- 可选：安装 [OpenSSL](https://trac.pjsip.org/repos/wiki/Getting-Started/Installing-OpenSSL-Windows)以支持 TLS，暂且跳过

#### 1.3.2 支持视频(2.0及以上版本支持)

##### 1.3.2.1 其他必要条件

- DirectShow SDK
  - 不支持 Windows 7 特性，使用[Windows SDK Update for Windows Vista](https://www.microsoft.com/en-us/download/details.aspx?id=14477)
- [SDL v.2.0](http://www.libsdl.org/download-2.0.php)
- 建议：libyuv，跳过
- 建议：OpenH264，跳过
- [FFmpeg](http://ffmpeg.org/)
  - 如果支持 H.264，需要 2011/10 之后的版本，且需要 libz
  - 可以使用二进制包(比如 [Zeranoe](https://ffmpeg.zeranoe.com/builds/) 的 dev 包)
  - 使用 [MSYS|MinGW](http://www.mingw.org/wiki/MSYS) 编译 libx264 和 FFmpeg 以及它的 [gcc 编译包](http://www.mingw.org/wiki/InstallationHOWTOforMinGW)
    - 建议使用 gcc 4 及以上版本
    - 为了避免问题，把 MSYS，libx264 和 FFmpeg 放在不含空格的文件夹
    - VS 使用 FFmpeg，需要 `inttypes.h` 和 `stdint.h`，[参考](https://code.google.com/p/msinttypes/downloads/detail?name=msinttypes-r26.zip&can=2&q=)
    - MSYS 编译最少参数

    ```sh
    ./configure --enable-shared --disable-static --enable-memalign-hack
    # add other options if needed, e.g: optimization, install dir, search path
    # particularly CFLAGS and LDFLAGS for x264
    # to enable H264, add "--enable-gpl --enable-libx264"
    make && make install
    ```

- 可选：libx264，跳过
- 可选：[Qt development SDK](https://www.qt.io/download)，用于编译 [video GUI 示例](https://trac.pjsip.org/repos/wiki/Video_Users_Guide#vidgui)，跳过

##### 1.3.2.2 其他配置

- 为组件添加 include 和 library 路径：DirectShow SDK，SDL，OpenH264，libyuv
  - libyuv 只需要为 PJSIP 2.5.1 或更旧的版本设置，之后的版本已经包含了 libyuv
- 添加下面的内容到 `config_site.h`

```c
#define PJMEDIA_HAS_VIDEO           1
#define PJMEDIA_HAS_OPENH264_CODEC  1
#define PJMEDIA_HAS_LIBYUV          1
#define PJMEDIA_VIDEO_DEV_HAS_SDL   1
#define PJMEDIA_VIDEO_DEV_HAS_DSHOW 1
```

- 可选：为 FFmpeg 添加 include 和 library 路径，并添加下面的内容到 `config_site.h`

```c
#define PJMEDIA_HAS_FFMPEG          1
```

##### 1.3.2.3 主机必要条件

- Windows NT, 2000, XP, 2003, Vista, Windows 7, Windows 10, or later
- Windows 95/98 应该可以工作，但是没有测试
- 足够的内存用于编译

## 2 编译工程

- 对于 VS2015：打开 `pjproject-vs14.sln` 解决方案文件
- 设置 `pjsua` 作为活动或启动项目
- 选择 [Debug/Release](https://trac.pjsip.org/repos/wiki/Getting-Started/Visual-Studio-Build-Configuration)
- 配置头文件和库目录
  - 工程添加 include 和 lib 目录，分别保存 FFmpeg 和 SDL 的头文件和库文件
  - 设置 `x64` 作为平台
    - 对项目 pjmedia 的 `VC++目录 -> 包含目录` 添加 `;$(SolutionDir)\include\ffmpeg;`
    - 对项目 pjmedia_codec 的 `VC++目录 -> 包含目录` 添加 `;$(SolutionDir)\include\ffmpeg;`
    - 对项目 pjmedia_videodev 的 `VC++目录 -> 包含目录` 添加 `;$(SolutionDir)\include\sdl;`
    - 对项目 pjsua 的 `VC++目录 -> 库目录` 添加 `;$(SolutionDir)\lib\sdl\x64;$(SolutionDir)\lib\ffmpeg\x64;`
  - 设置 `Win32` 作为平台
    - 对项目 pjmedia 的 `VC++目录 -> 包含目录` 添加 `;$(SolutionDir)\include\ffmpeg;`
    - 对项目 pjmedia_codec 的 `VC++目录 -> 包含目录` 添加 `;$(SolutionDir)\include\ffmpeg;`
    - 对项目 pjmedia_videodev 的 `VC++目录 -> 包含目录` 添加 `;$(SolutionDir)\include\sdl;`
    - 对项目 pjsua 的 `VC++目录 -> 库目录` 添加 `;$(SolutionDir)\lib\sdl\x86;$(SolutionDir)\lib\ffmpeg\x86;`
- 编译项目：会编译 pjsua 应用以及 pjsua 所需的所有库
- 编译成功之后，pjsua 应用会放在 `pjsua-apps/bin` 目录下，库会放在每个工程的 `lib` 目录

### 2.1 编译示例

- 使用相同的工作空间
- 设置 `samples` 作为活动项目
- 选择 `Debug/Release`
- 编译项目：会编译所有的示例应用和所需的库
- 编译成功之后，示例应用会放在 `pjsip-apps/bin/samples` 目录，库会放在每个工程的 `lib` 目录

### 2.2 编译遇到的问题

- 配置 vs2015，安装对应的 SDK

#### 2.2.1 FF_INPUT_BUFFER_PADDING_SIZE undefined identifier

- [参考](https://github.com/twitter/vireo/issues/11)
- 文件 `FFmpeg/doc/APIchanges` 指出
  > 29d147c / 059a9348 - Rename FF_INPUT_BUFFER_PADDING_SIZE and FF_MIN_BUFFER_SIZE to AV_INPUT_BUFFER_PADDING_SIZE and AV_INPUT_BUFFER_MIN_SIZE.
- 将 `pjmedia\src\pjmedia-codec\ffmpeg_vid_codecs.c` 中的 `FF_INPUT_BUFFER_PADDING_SIZE` 改成 `AV_INPUT_BUFFER_PADDING_SIZE`

#### 2.2.2 avcodec.lib(dplrs00034.o) : error LNK2026: 模块对于 SAFESEH 映像是不安全的

- 设置 Debug、Win32 编译 pjsua 时，报错
- 解决方法：对项目 pjsua 的 `链接器 -> 命令行` 添加 `/SAFESEH:NO`

#### 2.2.3 msys.bat cannot find the rxvt.exe or sh.exe binary -- aborting

- [安装指南](https://www.ics.uci.edu/~pattis/common/handouts/mingweclipse/mingw.html)
- 使用 MinGW 编译时，启动 msys.bat 报错
- 手动下载安装 [MSYS](https://sourceforge.net/projects/mingwbuilds/files/external-binary-packages/)
  - 将下载的包解压，然后将 msys 文件夹及子文件夹拷贝到 MinGW 安装文件夹，替换

## 3 调试示例应用

## 参考

- [Getting Started: Building for Microsoft Windows](https://trac.pjsip.org/repos/wiki/Getting-Started/Windows)