---
layout: post
title: Android Studio你不知道的快捷键(一)
category: Android
tabs: [android, android studio]
bigimg: /assets/images/2019-06-04/studio-feature.png
# no-post-nav: true
---

&#160; &#160; &#160; &#160;一般来说键盘用的越多鼠标用的越少，那么写起代码来效率就越高；常见的快捷键想必大家都已经掌握，接下来我就分享一些你可能不知道的但确非常实用的快捷键。

相关文章：  
[Android Studio你不知道的快捷键(二)](https://leno23.github.io/android/2019/06/05/android-studio-hotkey-2.html)  
[Android Studio你不知道的快捷键(三)](https://leno23.github.io/android/2019/06/10/android-studio-hotkey-3.html)

>文章转载于[Weishu](http://weishu.me/2015/12/11/shortcut-of-android-studio-you-may-not-know/)

>下文所有快捷键基于如下keymap  
>Windows: Default  
>Linux: Default  
>OSX: Mac OSX 10.5+

## 自动补全的时候是Enter还是Tab？

![自动补全enter和tab区别](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-04/blog-1.gif "自动补全enter和tab区别")

&#160; &#160; &#160; &#160;在使用自动补全的时候`Enter`和`Tab`的行为还是有一些细微的区别的：  
1. 使用`Enter`会补全你选择的语句  
2. 使用`Tab`的话，会替换掉你之前在这里的内容（删除后面的语句直到遇到点号，逗号，分号）  

&#160; &#160; &#160; &#160;这种情况我们还是会经常遇到的，比如要替换一个资源的ID（R.id.a_xxx_xxx)，想必大多数人都是先选择a.xxx_xxx删除，然后输入新的内容，或者相反；其实这时候，用Tab才是最优雅的方式。

&#160; &#160; &#160; &#160;**快捷键：（在补全的时候)`Enter/Tab`**

## 返回编辑器窗口

![返回编辑器窗口](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-04/blog-2.gif "返回编辑器窗口")

&#160; &#160; &#160; &#160;正在写代码的时候，很多操作会让焦点脱离编辑器；比如*Find Usage, Logcat,* 切换到项目结构视图，类型继承树等；如果视图切换了如何快速切回编辑器继续写代码呢？简单的鼠标点一下编辑器就可以了，但其实还有两种选择：

1. `Esc`: 让编辑器窗口获取焦点，这时候就可以输入代码了  
2. `Shift + Esc`: 这个会让编辑器获取焦点，并且顺手帮你把刚刚打开的窗口关闭了。

&#160; &#160; &#160; &#160;个人喜欢第二种；Find Usage完毕了，`Shift + Esc`, 优雅～

&#160; &#160; &#160; &#160;**`Esc`: 返回编辑器  
&#160; &#160; &#160; &#160;`Shift + Esc`: 返回编辑器并关闭当前窗口**

## 返回上次打开的工具窗口

![返回最后打开的工具窗口](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-04/blog-3.gif "返回最后打开的工具窗口")

&#160; &#160; &#160; &#160;接上面那个功能，如果你`Shift + Esc` 写了一会儿代码，发现又需要打开刚刚的窗口怎么办？这种场景通常发生在*Logcat*这个Tol Window上，看完了日志，写代码，写完代码看日志；如何快速切换？

&#160; &#160; &#160; &#160;**快捷键：`F12`**

## 快捷打开窗口

![使用数字快捷打开窗口](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-04/blog-4.gif "使用数字快捷打开窗口")

&#160; &#160; &#160; &#160;有木有发现有的窗口上面有个数字？这样的窗口（工具窗）我们可以快捷打开！

&#160; &#160; &#160; &#160;**Mac: `Cmd + 数字`  
&#160; &#160; &#160; &#160;windows/Linux: `Alt + 数字`**

## 任意窗口切换

![窗口切换](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-04/blog-5.gif "窗口切换")

&#160; &#160; &#160; &#160;上面的切换还是无法满足你的要求？记得Mac的`Cmd + Tab`，Windows的`Alt/Win + Tab`吗？Android Studio也有这个类似的功能，可以让你切换到任意窗口！

&#160; &#160; &#160; &#160;在这个切换窗口打开的时候，你可以直接按数字切换到对应的工具窗口，或者输入字母搜索右边的编辑器窗口，如果你需要关闭某个窗口，在上面按`BackSpace`即可。

&#160; &#160; &#160; &#160;**快捷键：`Ctrl + Tab`**

## 隐藏所有窗口

![隐藏所有窗口](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-04/blog-6.gif "隐藏所有窗口")

&#160; &#160; &#160; &#160;好了学了那么多打开窗口的技能，如果你想关闭那些乱七八糟的窗口，安安静静写代码应该怎么办？

&#160; &#160; &#160; &#160;**Mac: `CMD + Shift + F12`  
&#160; &#160; &#160; &#160;windows/Linux: `Ctrl + shift + F12`**  

&#160; &#160; &#160; &#160;如果需要恢复所有窗口，再按一次这个快捷键即可。

## 参数提示

![参数提示](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-04/blog-7.gif "参数提示")

&#160; &#160; &#160; &#160;这个功能估计很多人知道了，但是还是提一下。在自动补全以后，如果某个方法参数超级长，你不知道参数是什么怎么办？可以试试这个。

&#160; &#160; &#160; &#160;**Mac: `CMD + P`  
&#160; &#160; &#160; &#160;win/Linux: `Ctrl + P`**