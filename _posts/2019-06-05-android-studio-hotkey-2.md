---
layout: post
title: Android Studio你不知道的快捷键(二)
category: Android
tabs: [android, android studio]
bigimg: /assets/images/2019-06-04/studio-feature.png
# no-post-nav: true
---

&#160; &#160; &#160; &#160;在[Android Studio你不知道的快捷键(一)](https://leno23.github.io/android/2019/06/04/android-studio-hotkey-1.html)里面，主要讲述了一些窗口操作的快捷键还有补全参数提示等，这一篇会分享一些代码代码编辑的快捷键。

相关文章：  
[Android Studio你不知道的快捷键(一)](https://leno23.github.io/android/2019/06/04/android-studio-hotkey-1.html)  
[Android Studio你不知道的快捷键(三)](https://leno23.github.io/android/2019/06/10/android-studio-hotkey-3.html)

>文章转载于[Weishu](http://weishu.me/2015/12/12/shortcut-of-android-studio-you-may-not-know-2/)

## 自动生成变量

![自动生成变量](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-05/blog-1.gif "自动生成变量")

&#160; &#160; &#160; &#160;作为一门静态类型语言，Java是有一定的类型推导能力的；那么你是否经常书写:

```java
String testStr = "testStr";
List<String> testStrings = new ArrayList<String>();
```
&#160; &#160; &#160; &#160;其实大可不必写那些恼火的类型声明的，一看就知道`testStr`, `testStrings`就知道是什么类型，再这么干不就是废话么！好在IDEA给了我们这个能力。尝试一下这个快捷键吧，会给你惊喜。

&#160; &#160; &#160; &#160;**Mac: `Cmd + Alt + V`  
&#160; &#160; &#160; &#160;Win/Linux: `Ctrl + Alt + V`**  

&#160; &#160; &#160; &#160;有的童鞋可能会问了：我使用`ArrayList`, `HashMap`的时候，习惯类型声明为`List`,`Map`等接口，这个自动生成的类型声明还是具体的实现啊，怎么办？这一点IDE已经帮你想到了，试试`shift + tab`,他会给你一个可以选择的类型列表～

## 自动提取参数

![自动提取参数](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-05/blog-2.gif "自动提取参数")

&#160; &#160; &#160; &#160;有时候你正在写一个方法的时候发现。哎哟，这个变量最好是当作参数传递进来啊；要做成这么一件事，你必须把这个方法内部所有使用这个局部变量的地方替换，把所有调用这个函数的地方添加参数，繁琐至极！好了有了这个你可以随便玩了：

&#160; &#160; &#160; &#160;**Mac: `Cmd + Alt + P`  
&#160; &#160; &#160; &#160;Win/Linux: `Ctrl + Alt + P`**  

&#160; &#160; &#160; &#160;当然，如果你想保留原来的方法，只是搞一个参数不同的方法（重载）出来，可以在弹出的那个对话框里面打勾。

## 自动提取方法

![自动提取方法](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-05/blog-3.gif "自动提取方法")

&#160; &#160; &#160; &#160;写代码的时候是否会发现不知不觉的这个方法已经太长了，适合分解然后提取出一个个子方法；或者是重构的时候看到一个一两千的函数，你是不是头都大了？一般情况下，我们都是把要提取的代码copy出来，然后写一个方法（还要什么该死的方法签名）然后把这段代码复制进来；其实这个过程是机械的，完全可以由IDE完成：

&#160; &#160; &#160; &#160;**Mac: `Cmd + Alt + M`  
&#160; &#160; &#160; &#160;Win/Linux: `Ctrl + Alt + M`**  

&#160; &#160; &#160; &#160;如果想改变方法的签名，在对话框里面选择你需要的就可以了～

>上面提到了三个快捷键其实是比较类似的，如何记忆呢？  
>&#160; &#160; &#160; &#160;首先组合键都是`Cmd/Ctrl + Alt`  
>&#160; &#160; &#160; &#160;然后提取变量**V**ariable=**V**，参数**P**arameters=**P**，方法**M**ethod=**M**

## 内联变量/参数/方法

![内联](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-05/blog-4.gif "内联")

&#160; &#160; &#160; &#160;好了学会了上面那几个快捷键，万一玩high了，比如提取了太多的方法，想“弄回去”，该怎么办呢？这个操作叫他`Inline..`：

&#160; &#160; &#160; &#160;**Mac: `Cmd + Alt + N`  
&#160; &#160; &#160; &#160;Win/Linux: `Ctrl + Alt + N`**

&#160; &#160; &#160; &#160;上面那个图只是参考，其实不仅可以作用于变量，还可以是方法/参数，个人觉得方法inline比较有用。

## 万能重构键

![万能重构键](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-05/blog-5.png "万能重构键")

&#160; &#160; &#160; &#160;静态类型语言重构起来相对容易的，但是通常修改一个地方会牵扯到很多别的地方，我们只有一处一处找到这些编译错误然后手动修复。其实有好多工作是可以自动完成的，比如删除某个方法；先确认有没有人调用（Alt ＋ F7），没有的话把方法体删了，有的话去看看调用的地方再决定怎么办。

&#160; &#160; &#160; &#160;但是重构的操作实在是太多了！我们没有办法也没有必要一个个记住，知道这个快捷键即可，我叫他万能重构键:

&#160; &#160; &#160; &#160;**Mac : `Ctrl + T`  
&#160; &#160; &#160; &#160;Win/Linux: `Ctrl + Alt + Shift + T`**

&#160; &#160; &#160; &#160;在Win/Linux上可以考虑把这个快捷键改一下键，一下按四个键臣妾很难做到啊。。  
&#160; &#160; &#160; &#160;这个重构菜单每一个功能都可以自己去尝试一下，使用之后不好用你来打我。

## 重命名

![重命名](https://raw.githubusercontent.com/leno23/leno23.github.io.assets/master/images/2019-06-05/blog-6.gif "重命名")

&#160; &#160; &#160; &#160;好了介绍了那么多貌似很高端的玩意，来个大部分人都知道的吧。有时候你发现有个变量名字取得有问题，或者没文化的队友/自己单词拼错了咋办？需要把所有用到这个变量的地方重新命名，小case！

&#160; &#160; &#160; &#160;**快捷键：`shift + F6`**