---
layout:     post
title:      （转）十大Intellij IDEA快捷键
subtitle:   非常好用的快捷键，你需要更加熟悉
date:       2016-08-23 01:09:00
catalog:    true
tags:       [转载, Intellij, ]
---

# （转）十大Intellij IDEA快捷键

> **Intellij IDEA**中有很多快捷键让人爱不释手，**stackoverflow**上也有一些有趣的讨论。每个人都有自己的最爱，想排出个理想的榜单还真是困难。以前也整理过**Intellij**的快捷键，这次就按照我日常开发时的使用频率，简单分类列一下我最喜欢的十大快捷-神-键吧。

##  智能提示

Intellij首当其冲的当然就是Intelligence智能！**基本的代码提示用Ctrl+Space**，还有更智能地按类型信息提示**Ctrl+Shift+Space**，但因为Intellij总是随着我们敲击而自动提示，所以很多时候都不会手动敲这两个快捷键(除非提示框消失了)。用**F2/ Shift+F2移动到有错误的代码**，**Alt+Enter快速修复**(即Eclipse中的Quick Fix功能)。当智能提示为我们自动补全方法名时，我们通常要自己补上行尾的反括号和分号，当括号嵌套很多层时会很麻烦，这时我们只需敲**Ctrl+Shift+Enter就能自动补全末尾的字符**。而且不只是括号，例如敲完if/for时也可以自动补上{}花括号。
最后要说一点，Intellij能够智能感知Spring、Hibernate等主流框架的配置文件和类，以静制动，在看似“静态”的外表下，智能地扫描理解你的项目是如何构造和配置的。

##  重构

Intellij重构是另一完爆Eclipse的功能，其智能程度令人瞠目结舌，比如提取变量时自动检查到所有匹配同时提取成一个变量等。尤其看过《重构-改善既有代码设计》之后，有了Intellij的配合简直是令人大呼过瘾！也正是强大的智能和重构功能，使Intellij下的TDD开发非常顺畅。
切入正题，先说一个无敌的重构功能大汇总快捷键**Ctrl+Shift+Alt+T**，叫做Refactor This。按法有点复杂，但也符合Intellij的风格，很多快捷键都要双手完成，而不像Eclipse不少最有用的快捷键可以潇洒地单手完成(不知道算不算Eclipse的一大优点)，但各位用过Emacs的话就会觉得也没什么了(非Emacs黑)。此外，还有些最常用的重构技巧，因为太常用了，若每次都在Refactor This菜单里选的话效率有些低。比如Shift+F6直接就是改名，Ctrl+Alt+V则是提取变量。

##  代码生成

这一点类似Eclipse，虽不是独到之处，但因为日常使用频率极高，所以还是罗列在榜单前面。常用的有fori/sout/psvm+Tab即可生成循环、System.out、main方法等boilerplate样板代码，用Ctrl+J可以查看所有模板。后面“辅助”一节中将会讲到Alt+Insert，在编辑窗口中点击可以生成构造函数、toString、getter/setter、重写父类方法等。这两个技巧实在太常用了，几乎每天都要生成一堆main、System.out和getter/setter。
另外，Intellij IDEA 13中加入了后缀自动补全功能(Postfix Completion)，比模板生成更加灵活和强大。例如要输入for(User user : users)只需输入user.for+Tab。再比如，要输入Date birthday = user.getBirthday();只需输入user.getBirthday().var+Tab即可。

## 编辑

编辑中不得不说的一大神键就是能够自动按语法选中代码的Ctrl+W以及反向的Ctrl+Shift+W了。此外，**Ctrl+Left/Right移动光标到前/后单词，Ctrl+[/]移动到前/后代码块**，这些类Vim风格的光标移动也是一大亮点。以上Ctrl+Left/Right/[]加上Shift的话就能选中跳跃范围内的代码。Alt+Forward/Backward移动到前/后方法。还有些非常普通的像Ctrl+Y删除行、Ctrl+D复制行、Ctrl+</>折叠代码就不多说了。
关于光标移动再多扩展一点，除了Intellij本身已提供的功能外，我们还可以安装ideaVim或者emacsIDEAs享受到Vim的快速移动和Emacs的AceJump功能(超爽！)。另外，Intellij的书签功能也是不错的，用Ctrl+Shift+Num定义1-10书签(再次按这组快捷键则是删除书签)，然后通过Ctrl+Num跳转。这避免了多次使用前/下一编辑位置Ctrl+Left/Right来回跳转的麻烦，而且此快捷键默认与Windows热键冲突(默认多了Alt，与Windows改变显示器显示方向冲突，一不小心显示器就变成倒着显式的了，冏啊)。

##  查找打开

类似Eclipse，Intellij的**Ctrl+N/Ctrl+Shift+N可以打开类或资源**，但Intellij更加智能一些，我们输入的任何字符都将看作模糊匹配，省却了Eclipse中还有输入*的麻烦。最新版本的IDEA还加入了Search Everywhere功能，只需按Shift+Shift即可在一个弹出框中搜索任何东西，包括类、资源、配置项、方法等等。
类的继承关系则可用**Ctrl+H打开类层次窗口**，在继承层次上跳转则用Ctrl+B/Ctrl+Alt+B分别对应父类或父方法定义和子类或子方法实现，查看**当前类的所有方法用Ctrl+F12**。
要找类或方法的使用也很简单，Alt+F7。要查找文本的出现位置就用Ctrl+F/Ctrl+Shift+F在当前窗口或全工程中查找，再配合**F3/Shift+F3前后移动**到下一匹配处。
Intellij更加智能的又一佐证是在任意菜单或显示窗口，都可以直接输入你要找的单词，Intellij就会自动为你过滤。

##  其他辅助

以上这些神键配上一些辅助快捷键，即可让你的双手90%以上的时间摆脱鼠标，专注于键盘仿佛在进行钢琴表演。这些不起眼却是至关重要的最后一块拼图有：
Ø  命令：**Ctrl+Shift+A**可以查找所有Intellij的命令，并且每个命令后面还有其快捷键。所以它不仅是一大神键，也是查找学习快捷键的工具。
Ø  新建：**Alt+Insert**可以新建类、方法等任何东西。
Ø  格式化代码：格式化import列表Ctrl+Alt+O，**格式化代码Ctrl+Alt+L**。
Ø  切换窗口：Alt+Num，常用的有1-项目结构，3-搜索结果，4/5-运行调试。Ctrl+Tab切换标签页，Ctrl+E/Ctrl+Shift+E打开最近打开过的或编辑过的文件。
Ø  单元测试：**Ctrl+Alt+T创建单元测试用例**。
Ø  运行：Alt+Shift+F10运行程序，Shift+F9启动调试，Ctrl+F2停止。
Ø  调试：F7/F8/F9分别对应Step into，Step over，Continue。
此外还有些我自定义的，例如水平分屏Ctrl+|等，和一些神奇的小功能Ctrl+Shift+V粘贴很早以前拷贝过的，Alt+Shift+Insert进入到列模式进行按列选中。

##  最终榜单

这榜单阵容太豪华了，后几名都是如此有用，毫不示弱。

Ø  Top#10切来切去：**Ctrl+Tab**

Ø  Top #9  选你所想：**Ctrl+W**

Ø  Top #8  代码生成：**Template/Postfix +Tab**

Ø  Top #7  发号施令：**Ctrl+Shift+A**

Ø  Top #6  无处藏身：**Shift+Shift**

Ø  Top #5  自动完成：**Ctrl+Shift+Enter**

Ø  Top #4  创造万物：**Alt+Insert**

Ø  Top #3  智能补全：**Ctrl+Shift+Space**

Ø  Top #2  自我修复：**Alt+Enter**

Ø  Top #1  重构一切：**Ctrl+Shift+Alt+T**

[原文地址](http://blog.csdn.net/dc_726/article/details/42784275)


