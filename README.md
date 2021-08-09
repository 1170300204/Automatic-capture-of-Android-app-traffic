# Automatic-capture-of-Android-app-traffic
自动控制Android应用产生对应流量并进行捕获和保存。<br><br>
想要在Android平台下实现自动流量捕获，就需要找到一种方法使得我们可以控制Android设备自动的进行一些操作，进而控制相应的软件完成一系列预定的动作以产生对应的流量。<br><br>
## ADB
ADB的全名为Android debug bridge（Android调试桥），它是一种Android官方提供的功能十分强大的命令行工具，以帮助PC和Android设备之间建立连接进行通信。通过ADB我们可以实现很多功能，例如访问Android设备的Unix shell，并通过Unix shell在设备上运行各种各样的指令。<br><br>
其中我们重点关注 `adb shell input` 指令。 `adb shell input` 指令能够模拟按键输入，通过给定按键事件和坐标，即可以完成屏幕点击、滑动和键盘输入等行为来达成自动操作应用的目的。例如，我们可以通过以下指令完成“点击屏幕上(200,400)坐标点”动作：
```
# adb shell input touchscreen tap 200 400
```
也可以通过以下指令完成“以300ms时间从屏幕上(500,1800)点处滑动到(500,800)点处”动作
```
# adb shell input swipe 500 1800 500 800 300
```
而对于现在的很多Android应用来说其界面是会动态变化的，因此其用户界面所包含的各个元素的位置并不是固定在某个位置上，而可能会随着内容的不同而发生变化。<br><br>
例如，对于一些社交软件，用户可以浏览各种推送给自己的推文并对其进行点赞、评论、转发等操作。但是用户接收到的推文的文字长度并不是固定的，有的推文也可能会有图片内容等，所以对应推文所占用的空间长度并不是固定的，其点赞、评论、转发按钮等的位置也会随之改变。<br><br>
因此，如果我们想要通过ADB来点击某一个推文的点赞按钮，就需要能够实时获取该按钮在屏幕上的位置。UiAutomator工具可以帮助我们实现这一功能。
## UiAutomator
UiAutomator是Android 4.3发布的时候包含的一种测试工具，可以用于Android UI自动化测试。其主要功能包括用于检查手机布局层次结构、检索状态信息以及在当前设备上执行相关操作等。<br><br>
UiAutomator的`dump`命令可以帮助我们抓取当前手机目前屏幕信息形成一个xml文档，当中包含着当前手机屏幕状态下各个页面元素的各项属性信息，如坐标、名称等。
```
# adb shell uiautomator dump 2>&1 
```
由此我们可以考虑如下操作：<br><br>
通过ADB访问Android设备的Unix shell执行UiAutomator的`dump`指令，此时会在手机的SD存储卡中生成一个名为`windows_dump.xml`的可扩展标记语言文件，其中储存着当前屏幕上页面元素的位置信息。然后就可以从`windows_dump.xml`文件中提取出所需元素在屏幕上的实际位置。有了位置信息则能够通过`adb shell input`指令来完成相应的动作。

