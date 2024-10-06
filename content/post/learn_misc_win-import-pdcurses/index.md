# 一.配置C++环境

## 1.下载MinGW

选用mingw。

首先，打开[官方网站](https://winlibs.com/#download-release)，选择最新的适合自己的版本下载。

如果不知道下什么，可以点此[链接](https://github.com/brechtsanders/winlibs_mingw/releases/download/14.1.0posix-18.1.5-11.0.1-ucrt-r1/winlibs-x86_64-posix-seh-gcc-14.1.0-llvm-18.1.5-mingw-w64ucrt-11.0.1-r1.zip)进行下载。
下载后，解压到非中文路径。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/01.avif)

在例子中，我是直接解压到c盘下面。

## 2.添加环境变量

打开window设置。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/02.avif)

点击关于(about)

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/03.avif)

点击高级系统设置(Advanced system settings)。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/04.avif)

点击环境变量。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/05.avif)

选中path那一行后，点击编辑。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/06.avif)

点击新建，把你的mingw64\bin全部路径输进去。由于我的路径是`C:\mingw64\bin`，所以我添加的是C:\mingw64\bin。如果你的路径是`C:\Progra Files\mingw64\bin`，那你就添加C:\Progra Files\mingw64\bin。以此类推。

添加完成后点击确定。关闭剩下的窗口。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/07.avif)

按`Win`+`R`键，输入cmd，点击确定，打开命令提示符窗口。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/08.avif)

在命令提示符窗口中输入：

```cmd
g++ --version
```

若又输出版本号，即成功配置c++环境。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/09.avif)

# 二.导入PDCurses库

## 1.下载源码

访问[官方下载网站]([Releases · wmcbrine/PDCurses · GitHub](https://github.com/wmcbrine/PDCurses/releases))，找到最新的版本下载，不知道下什么可以点此[链接](https://github.com/wmcbrine/PDCurses/archive/refs/tags/3.9.zip)下载。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/10.avif)

下载后解压出来。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/11.avif)

## 2.编译源码

进入`wincon`文件夹。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/12.avif)

在此位置打开终端，如果不会，直接在上面的文件路径栏输`cmd`然后回车。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/13.avif)

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/14.avif)

成功打开，且路径在这个文件夹下面。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/15.avif)

输入：

```cmd
mingw32-make -f Makefile
```

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/16.avif)

回车，等待代码滚完。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/17.avif)
[learn/pve/pve-hackintosh](../blog/learn_pve_pve-hackintosh.md)
## 3.将编译出来的东西添加到MinGW的库中

打开`PDCurses-3.9\wincon`这个文件夹，里面将里面一个叫`pdcurses.a`的文件复制到`mingw64\lib`目录下面，并重命名为名叫`libpdcurses.a`

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/18.avif)

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/19.avif)

打开`PDCurses-3.9`这个文件夹，把`curses.h`、`curspriv.h`和`panel.h`这三个头文件复制到`mingw64\include`目录下面。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/20.avif)

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/21.avif)

至此导入成功，下面进行测试。

## 4.测试

找个地方创建`test.cpp`。

```test.cpp
#include <cstring>
#include <curses.h>

int main(){
    initscr();
    raw();
    noecho();
    curs_set(0);

    char* c = "Hello, World!";

    mvprintw(LINES/2, (COLS-strlen(c))/2,c);
    refresh();

    getch();
    endwin();
    
    return 0;
}

```

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/22.avif)

然后在该目录下打开命令提示符。并输入：

```cmd
g++ -o test test.cpp -lpdcurses
```

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/23.avif)

这里警告不要管他，已经编译成功了。在该文件夹下面生成了`test.exe`。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/24.avif)

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/25.avif)

点击运行这个`test.exe`。

看到hello world，测试成功。PDCurses库可以正常使用。

![](https://img.0pt.icu/learn/misc/win-import-pdcurses/26.avif)
