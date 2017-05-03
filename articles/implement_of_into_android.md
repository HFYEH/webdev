

# 手動導入 Openframeworks 至現有的 APP (Android版)

## 簡述

[Openframeworks](openframeworks.cc)(OF)是一個開源的C++工具組，有跨平台的特性。團隊有專案在Openframeworks上開發，先前主要都是在Windows上開發，現在要將功能移植到APP上。

這篇主要是講移植到Android系統。Openframeworks提供給Android原生的函式庫，以Java Native Interface調用。並且還提供addon，為一個OFActivity，可以在這裡面使用OF的各種功能。

官方提供的方案只能從它們寫好的sample app開始一個專案，由於不想被OF綁死，所以我開始很痛苦的去看原生的各部份是如何組在一起的（只玩React native果然是要還的），以便將來可以再次拆出OF來。這篇文章就以一個Android空白專案為開始，最後可以用intent呼叫OFActivity。中間許多原理可能會跳過，僅說明步驟。

## 環境設定

基本的Android Studio和SDK、JDK的設定就略過，直接從NDK開始。NDK是讓Android專案可以調用C++或其他語言的工具。首先到[Android NDK頁面](https://developer.android.com/ndk/downloads/index.html)下載。要注意，因為OF官方說明，完整測過的NDK版本只有r10，所以要拉到最下面的archive下載r10版本來用。使用Android Studio雖然可以透過SDK Manager直接裝，但是無法裝舊版的。

下載完解壓後，設定環境變數`NDK_HOME=path_to_ndk_folder`並將此變數加入PATH。Windows下要設定的是`NDK_ROOT`。

打開SDK Manager，安裝LLDB和CMake。

再到OF官網下載OF for Android。

## 開新專案

保持簡單就好，這邊使用OF開一個UseOpenframeworks專案。

圖1

Mininum SDK不要選太高，確保可以向下相容。接下來因為我們會在Android原生的acitivity中intent給OFActivity，所以創一個初始的Activity，使用EmptyActivity即可。其他就照預設按Next。

圖2

再來把Project的結構改成從Android改成Project，結構會變成下圖。

圖3

因為沒有使用SDK Manager安裝NDK，所以須要手動設定NDK路徑。在 File > Project Structure ... 的SDK Location中，修改Android NDK Location欄位。

圖4

至此，專案完成。


## 引入OF

在settings.gradle中加入

```gradle
def ofRoot = 'path_to_your_of_folder'

include ':openFrameworks'
project(':openFrameworks').projectDir = new File(ofRoot + 'libs/openFrameworks')
```

並且sync，可以看到openFrameworks的原始碼被引入了。

圖5

繼續加入

```gradle
include ':ofAndroidLib'
project(':ofAndroidLib').projectDir = new File(ofRoot + 'addons/ofxAndroid/ofAndroidLib')
```

這是OF的Android package，之後可以直接將之實例化，放入AndroidManifest.xml中當作一個activity來用。如果是SDK Platform要更新，就點擊錯誤訊息更新即可。加入成功後如下圖

圖6

繼續加入

```gradle
include ':openFrameworksProject'
project(':openFrameworksProject').projectDir = new File(ofRoot + 'libs/openFrameworksCompiled/project/android')
```

這個是重點。引入並用gradle build時，會將OF原始碼建成可執行檔，並且會進入專案，將project_folder/src內的native code也建成.o檔。

圖7

上面`def ofRoot`要小心，windows和linux路徑表示方式不同，而最後一定要加`/`或`\\`才會正確進入該資料夾。

如果build失敗，OF提供一個技巧，在失敗的地方println xxx，然後在project folder將gradlew改成可執行檔，然後手動build，可以看到印出的訊息。這個就是對gradle不熟的我第一次習

沒意外的話，前兩步應該都可以正常運作。但是第三步因為牽涉到整個OF和本身專案的build，因此有許多問題。我們要改一下其中的一些設定，讓gradle可以成功build。再來就是痛苦的debug過程了。
得的技能。

首先在會先碰到的是找不到NDK路徑，雖然我們已經設定好了，但是它就是吃不到。為此，我們要改寫OF的內容。照官方說明，在libs/openFrameworksCompiled/project/android/paths.make裡改寫android NDK path。

在windows須要安裝MinGW的GCC編譯器才能compile，所以接著安裝MinGW，看[教學](http://blog.jex.tw/blog/2013/12/17/windows-install-gcc-compiler-mingw/)。

然後還要安裝[Make for Windows](http://gnuwin32.sourceforge.net/packages/make.htm)，讓windows看得懂Makefile。跟MinGW一樣，也要設定環境變數。
















在app/build.gradle加入compile project(':ofAndroidLib')

改openframeworkProject的gradle version

把下面換掉

```gradle
//    assembleDebug.dependsOn(":openFrameworksProject:compileDebugOF")

//    assembleRelease.dependsOn(":openFrameworksProject:compileReleaseOF")
tasks.whenTaskAdded { task ->
    if (task.name == 'assembleDebug') {
        task.dependsOn ':openFrameworksProject:compileDebugOF'
    }
}

tasks.whenTaskAdded { task ->
    if (task.name == 'assembleDebug') {
        task.dependsOn ':openFrameworksProject:compileReleaseOF'
    }
}
```


add a makefile and cmake

看起來openframworkProject的用意在建起一個類似虛擬機的動作，作完後會在libs裡新增一個armeabi-v7a


把Makefile和config.make新增過去，在config.make內

```
ifndef OF_ROOT
    OF_ROOT=$(realpath ../../..)
endif
```


