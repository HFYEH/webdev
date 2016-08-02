# 轉載文章

[adb shell 命令詳解](http://www.open-open.com/lib/view/1327557366686)


SDK的Tools文件夹下包含着Android模拟器操作的重要命令adb，adb的全称为(Android Debug Bridge就是调试桥的作用。通过adb我们可以在Eclipse中方面通过DDMS来调试Android程序。借助这个工具，我们可以管理设备或手机模拟器的状态。还可以进行以下的操作： 

1. 快速更新设备或手机模拟器中的代码，如应用或Android 系统升级； 
2. 在设备上运行shell命令； 
3. 管理设备或手机模拟器上的预定端口； 
4. 在设备或手机模拟器上复制或粘贴文件；


### adb在集成开发环境中的工作

adb的工作方式比较特殊采用监听Socket TCP 5554等端口的方式让IDE和Qemu通讯，默认情况下adb会daemon相关的网络端口，所以当我们运行Eclipse时adb进程就会自动运行。

1. 通过adb可以轻松的执行Linux Shell命令，如adb shell dir 就是列举目录，在Linux中根目录为/而不是Windows上的C盘、D盘。
2. 安装apk程序到模拟器则执行adb install android123.apk，这样名为android123的安装包就会安装到Android模拟器中，前提是android123.apk文件需要放到SDK/Tools目录下。
3. 向emulator传送文件， 使用adb push android123.txt /tmp/android123.txt命令可以把SDK/Tools下的android123.txt文件传输到模拟器的/tmp/文件夹中，需要注意的是/tmp/文件夹中内容会在Android模拟器重新启动时清空。
4. 从Android仿真器中回传文件到电脑  通过adb pull /tmp/android123.txt android123.txt命令就会把仿真器的tmp文件夹下android123.txt文件回传到电脑SDK/Tools目录下。

### adb 常用命令大全

1. 显示系统中全部Android平台：

 android list targets

2. 显示系统中全部AVD（模拟器）：

 android list avd

3. 创建AVD（模拟器）：

 android create avd --name 名称 --target 平台编号

4. 启动模拟器：

 emulator -avd 名称 -sdcard ~/名称.img (-skin 1280x800)

5. 删除AVD（模拟器）：

 android delete avd --name 名称

6. 创建SDCard：

 mksdcard 1024M ~/名称.img

7. AVD(模拟器)所在位置：

 Linux(~/.android/avd) Windows(C:\Documents and Settings\Administrator\.android\avd)

8. 启动DDMS：

 ddms

9. 显示当前运行的全部模拟器：

 adb devices

10. 对某一模拟器执行命令：

 abd -s 模拟器编号 命令

11. 安装应用程序：

 adb install -r 应用程序.apk

12. 获取模拟器中的文件：

 adb pull <remote> <local>

13. 向模拟器中写文件：

 adb push <local> <remote>

14. 进入模拟器的shell模式：

 adb shell

15. 启动SDK，文档，实例下载管理器：

 android

16. 缷载apk包：

 adb shell

 cd data/app

 rm apk包

 exit

 adb uninstall apk包的主包名

 adb install -r apk包

17. 查看adb命令帮助信息：

 adb help

18. 在命令行中查看LOG信息：

 adb logcat -s 标签名

19. adb shell后面跟的命令主要来自：

 源码\system\core\toolbox目录和源码\frameworks\base\cmds目录。

20. 删除系统应用：

 adb remount （重新挂载系统分区，使系统分区重新可写）。

 adb shell

 cd system/app

 rm *.apk

21. 获取管理员权限：

 adb root

22. 启动Activity：

 adb shell am start -n 包名/包名＋类名（-n 类名,-a action,-d date,-m MIME-TYPE,-c category,-e 扩展数据,等）。

23、发布端口：

 你可以设置任意的端口号，做为主机向模拟器或设备的请求端口。如： adb forward tcp:5555 tcp:8000

24、复制文件：

 你可向一个设备或从一个设备中复制文件，  复制一个文件或目录到设备或模拟器上：  adb push <source> <destination></destination></source>  如：adb push test.txt /tmp/test.txt  从设备或模拟器上复制一个文件或目录：  adb pull <source> <destination></destination></source>  如：adb pull /addroid/lib/libwebcore.so .

25、搜索模拟器/设备的实例：

 取得当前运行的模拟器/设备的实例的列表及每个实例的状态：  adb devices

26、查看bug报告： adb bugreport 27、记录无线通讯日志：

 一般来说，无线通讯的日志非常多，在运行时没必要去记录，但我们还是可以通过命令，设置记录：  adb shell  logcat -b radio

28、获取设备的ID和序列号：

 adb get-product  adb get-serialno

29、访问数据库SQLite3

 adb shell sqlite3