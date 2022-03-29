---
layout: post
title: Android Sunflower Java版本
category: Android
tabs: [android, android jetpack]
bigimg: /assets/images/2019-06-17/jetpack_donut.png
gh-repo: leno23/sunflower-java
no-post-nav: true
---

&#160; &#160; &#160; &#160;Sunflower App是展示Android Jetpack使用的最佳实践。

&#160; &#160; &#160; &#160;Sunflower原来是[Google用Kotlin写的一个Android Jetpack最佳实践](https://github.com/googlesamples/android-sunflower)；而这个[Java版本](https://github.com/leno23/sunflowr-by-java)是我很早以前还在上一家公司工作时写的。  
&#160; &#160; &#160; &#160;因为上一家公司都是用Java开发，所以为了体验Android Jetpack和MVVM模式，以便后续应用到项目中，就先用Java覆写了一遍Sunflower App。当时，没有上传代码。现在，想要用Dagger做di框架，就在这个项目上做个Demo。为了保存版本做版本管理，就上传到Github了。  

&#160; &#160; &#160; &#160;**关于选用Kotlin还是Java？** 这个问题我在上一家公司的内部分享会上问过一个Java技术专家，”如何评价Kotlin，有没有必要转Kotlin？“。他说”Java已经是很成熟强大的语言了，没必要再学一门类JVM语言。可以学习其他编程思想的语言，比如lisp啊“。  
&#160; &#160; &#160; &#160;学不同思想的编程语言肯定没错，但他对Kotlin的评价，我不能苟同。从目前的使用体验，Kotlin更加高效。相比Java的优势也不仅仅是解决了Null的问题或某些语法糖，很多新的特性是Java不具备或者说暂时不具备的。  
&#160; &#160; &#160; &#160;学习Kotlin，对所有人来说都有帮助，可以打开很多新的思路；对于Android开发来说，我认为是必选项。毕竟Kotlin已经被Google选为Android开发的官方语言，新出的AndroidX库有的也只支持Kotlin。而且各大神也都用Kotlin做开发了。Follow自己的男神就对了~~~

&#160; &#160; &#160; &#160;好了，后面会发一篇介绍[Sunflower + Dagger2](https://leno23.github.io/android/2019/06/24/android-sunflower-java-2.html)的文章。  
&#160; &#160; &#160; &#160;有时间也会结合自己使用整理一下关于Android Jetpack组件的介绍。

&#160; &#160; &#160; &#160;Ok，欢迎Star&Follow！