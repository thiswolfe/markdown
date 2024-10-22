---
title: meson 的基础使用
description: meson是一个构建系统，类似于 CMake 或者GNU Autotools. meson只是负责配置构建，后台默认是用ninja来编译的(当然也支持其它后台)。ninja是一个小型的致力于编译速度优化的编译系统，相当于make的替代物。所以meson+ninja相当于Cmake+make。 
tag:
 - meson
categories:
 - code
---

# MESON 构建程序-最简单的使用

## **meson 的基本介绍**

Meson 是基于python3实现，至少需要 python3.5+ 才能运行，默认采用ninja作为后端。

    sudo apt-get install python3 python3-pip ninja-build
    sudo pip3 install meson ninja

# 查看meson内置的选项、默认值及可选值
    meson configure
    meson build

# 进入到meson的构建目录执行:
    meson compile 或者 ninja

# 在生成编译配置时，可以通过 -D 指定编译选项
    meson builddir -Dprefix=/usr -Dgtk_doc=disabled -Dtests=disabled
    cd builddir && ninja -j8
    meson install

# 在源码根目录通过 configure更新编译选项
    meson configure builddir -Dprefix=/home/dev/tmp

可以通过以下命令取得一个工程示例。该示例会生成一个 testproject.c、meson.build：

    meson init --name testproject

# **构建执行程序**

**main.c**

    #include<stdio.h>

    int main(int argc, char **argv) {
        printf("Hello meson！.\n");
        return 0;
    }

**meson.build**

    project('tutorial', 'c')
    executable('demo', 'main.c')

**files tree**

    .
    ├── main.c
    └── meson.build


## **构建库文件**

**test.c**

    #include <stdio.h>
    #include "test.h"

    void info_print()
    {
        printf("hello! this library.\n");
    }

**test.h**

    #ifndef _TEST_LIB_
    #define _TEST_LIB_

    void info_print();

    #endif

**meson.build**

    project('testlib', 'c')

    shared_library('testlib', 'test.c')
    # static_library('testlib', 'test.c')

**files tree**

    .
    ├── meson.build
    ├── test.c
    └── test.h


## **使用系统库文件**

**main.c**

    #include <stdio.h>
    #include <pthread.h>

    int main(int argc, char * argv[]) {
        pthread_t a = pthread_self();;
        printf("demo using pthread, id[%ld].\n", a);
        return a;
    }

**meson.build**

    project('uselib', 'c')

    cc = meson.get_compiler('c')
    # cxx = meson.get_compiler('cpp')

    libpthread = cc.find_library('pthread', required : true)

    executable(
        'main',
        'main.c',
        dependencies: [libpthread],
        )

**files tree**

    .
    ├── main.c
    └── meson.build


## **使用第三方库文件**

### **方案1**

根据文件位置引用。注意：需要先生成lib文件。

**main.cpp**

	#include <stdio.h>
	#include "test.h"
	
	int main(void)
	{
	    printf("hello project main lib\n");
	    info_print();
	    return 0;
	}

**test.cpp**

    #include <stdio.h>
    #include "test.h"

    void info_print()
    {
        printf("hello! this library.\n");
    }

**test.h**

    #ifndef _TEST_LIB_
    #define _TEST_LIB_

    void info_print();

    #endif

**lib/meson.build**

    project('testlib', 'c')

    # 这个没测试过呢
    # pkg = import('pkgconfig')
    # pkg.generate(libraries : testlib,
    #              subdirs : ['lib'],
    #              version : '1.0',
    #              name : 'testlib',
    #              filebase : 'testlib',
    #              description : 'testlib info')

    testlib = shared_library('testlib', 'test.c')
    # testlib = static_library('testlib', 'test.c')

    database_app_dep = declare_dependency(
      link_with : [
        testlib,
      ],
      include_directories : [
        '.'
      ]
    )

**meson.build**

    project('main', 'c')

    cc = meson.get_compiler('c')
    testlib = cc.find_library('testlib',
                              dirs : [ '/home/zero/Desktop/test/meson_demo/extlib/origin/lib/' ])
    testinc = include_directories('./lib/')

    executable('main', 'main.c',
               dependencies: [testlib],
               include_directories:testinc)

**files tree**

    .
    ├── lib
    │       ├── meson.build
    │       ├── test.c
    │       └── test.h
    ├── main.c
    └── meson.build


## **方案二**

通过 pkg-config 使用第三方库，主要步骤参考 run.sh 。
注意：需要先生成库文件，并安装到 pkg-config 中。

**main.cpp**

    #include <stdio.h>
    #include "test.h"

    int main(void)
    {
        printf("hello project main lib\n");
        info_print();
        return 0;
    }

**test.cpp**

    #include <stdio.h>
    #include "test.h"

    void info_print()
    {
        printf("hello! this library.\n");
    }

**test.h**

    #ifndef _TEST_LIB_
    #define _TEST_LIB_

    void info_print();

    #endif

**lib/meson.build**

    project('testlib', 'c')

    # 没测试过
    # Create a pkg file
    # pkg = import('pkgconfig')
    # pkg.generate(libraries : testlib,
    #              subdirs : ['lib'],
    #              version : '1.0',
    #              name : 'testlib',
    #              filebase : 'testlib',
    #              description : 'testlib info')

    # install dir
    # meson build --prefix=/usr

    testlib = shared_library(
    	      'testlib', 'test.c',
    	      install : true
    	      )
    # testlib = static_library('testlib', 'test.c', install : true)

    database_app_dep = declare_dependency(
      link_with : [
        testlib,
      ],
      include_directories : [
        '.'
      ]
    )

**libtestlib.pc**

    #libevent pkg-config source file

    prefix=/home/zero/Desktop/test/meson_demo/extlib/upgrade/lib/
    exec_prefix=${prefix}
    libdir=${exec_prefix}/build
    includedir=${prefix}

    Name: libtestlib
    Description: Test library files to use
    Version: 1.1.0-stable
    Requires: libtestlib
    Conflicts:
    Libs: -L${libdir} -ltestlib
    Libs.private:
    Cflags: -I${includedir}

**meson.build**

    project('main', 'c')

    dep = dependency('libtestlib')

    executable('main', 'main.c',
               dependencies: [dep],
    )

**run.sh**

    #!/bin/bash
    #显示每一步执行的内容
    set -x

    # 设置当前路径为变量
    CURPATH=`pwd`

    # 设置环境变量并增加查找位置
    export PKG_CONFIG_PATH=${CURPATH}/lib/:$PKG_CONFIG_PATH
    # 查找pkg的包内容
    pkg-config --list-all | grep testlib

    # meson- 构建
    meson build

    cd build

    # meson 编译
    meson compile

    # 执行测试
    ./main

**files tree**

    .
    ├── lib
    │   ├── libtestlib.pc
    │   ├── meson.build
    │   ├── test.c
    │   └── test.h
    ├── main.c
    ├── meson.build
    └── run.sh
