 # Android开发环境搭建

##下载TI源代码

下载PROCESSOR-SDK-ANDROID-AUTOMOTIVE代码
参考 [<font color="red">Ti代码下载地址</font>](http://processors.wiki.ti.com/index.php/6AO.1.0_Release_Notescon)


远峰svn代码同步命令：
> svn checkout svn://182.254.215.90/co-dev --username=HuangLeo --password=p0RPn7IS

下载android源代码后进行编译。

```bash
source build/envsetup.sh
lunch
make -j8
```

单模块编译
以SystemUI为例，有两种方式编译:

+ 在根目录运行mm  SystemUI
+ cd /home/lyq/src/android/frameworks/base/packages/SystemUI; mma

编译不过可以尝试清除 /out/target/common/obj/APPS/SystemUI_intermediates
编译完之后记得`make snod`重新写入system.img，然后重启模拟器

##导入android studio

+ 首先编译idegen模块
mmm development/tools/idegen/
这个命令是为了生成`idegen.jar`文件，默认情况下aosp编译并不会生成该文件。
该文件的路径一般在aosp-root/out/host/linux-x86/framework/idegen.jar

+ 然后执行下面命令，生成IDE项目文件
```bash
development/tools/idegen/idegen.sh
```
如果你查看该脚本的源码，会发现它做了2件事情：
在out目录下查找，是否已经生成了idegen.jar文件

+ 执行java -cp idegen.sh Main

Gradle
> + Android projects are Gradle multi-project builds, with a top-level build.gradle file and a subdirectory called app, with its own build.gradle file. The top-level build file is noted as (Project: HelloWorldGradle) in the figure, and the app build file has (Module: app) appended to it.
> + There may be two files called gradle.properties. One is local to the project. The other, optional file of the same name exists only if you have a global gradle.properties file in the .gradle sub-directory of your home directory.
> + The file settings.gradle is used by Gradle to configure the multi-project build. It should consist of a single line:
include ':app'

repo 切换分之
```bash
repo forall -c git reset --hard
repo init -u https://android.googlesource.com/platform/manifest -b android-6.0.1_r1
repo sync
```

附录（摘自王胜明邮件）
+ [<font color="red">Android 6.1.3 链接</font>](http://software-dl.ti.com/infotainment/jacinto6/android/6AM_1_3_Release/index_FDS.html)

这个链接今天好像有问题，你们明天再试试。我先把System UI和launcher原生代码放到以下svn目录：svn://182.254.215.90/co-dev/ajax1_hu/ti

+ WM编译好的APK可以提交到svn上的wmrelease目录，YF的Jenkins可以自动打包后再上传到svn的yfrelease目录，省掉你们的打包过程。

+ WM的systemui代码可以放到trunk目录，YF的Jenkins自动将其编译进系统，发布到yfrelease目录。

##ubuntu切换Java版本
```bash
update-alternatives --config java
update-alternatives --config javac
```

dex2oat --runtime-arg -Xms64m --runtime-arg -Xmx512m --dex-file=/data/Launcher3.dex --dex-location=/system/priv-app/Launcher3/Launcher3.apk --oat-file=/data/Launcher3.odex --android-root=/system --instruction-set=x86 --instruction-set-variant=x86 --instruction-set-features=default --include-patch-information --runtime-arg -Xnorelocate --no-generate-debug-info --abort-on-hard-verifier-error
