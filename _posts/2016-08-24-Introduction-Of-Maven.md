---
layout:     post
title:      （译）Maven简介
subtitle:   个人翻译的Maven官方介绍文档
date:       2016-08-24 23:48:00
catalog:    true
tags:       [Maven, 译文, 官方文档, ]
---


#Maven简介

> **Maven**在意第绪语（属于日耳曼语族）中是积累知识的意思，最早是在**Jakarta Turbine**项目中为了简化项目构建过程而提出的，该项目中用Ant管理的好几个项目都大同小异，项目依赖的Jar包在CSV中统一管理。但我们想要一种标准的方式来构建项目，一个项目构成的明确的定义，一种简单的方式来发布项目信息和在不同的项目中共享Jar包。最终有这样一个可以用来构建和管理所有Java工程的工具也就是Maven。我们希望我们可以让Java工程师们每天都要进行的工作能够更简单，而且可以帮助Java工程更易于理解。

## Maven的目标

Maven最初的目标是让开发者在最短的时间里理解开发过程中的所有步骤。为了达到这个目标Maven做了以下方面的努力：

- 让构建过程更轻松
- 提供统一的构建系统
- 提高大量的项目信息
- 提供最好的开发指南
- Allowing transparent migration to new features（这个怎么译？）

## 让构建过程更轻松
虽然使用Maven并不代表你完全不需要去理解其实现的原理，但Maven确实提供了大量对于细节的包装

## 提供统一的构建系统
Maven允许项目通过其对象模型（POM）来构建并且项目中可以通过Maven来共享各类组件，提供一个统一的构建系统。
Once you familiarize yourself with how one Maven project builds you automatically know how all Maven projects build saving you immense amounts of time when trying to navigate many projects.（这个又怎么译？）

ps:个人翻译的Maven官方介绍文档，翻译不好，还望见谅:)
原文地址：[what-is-maven](http://maven.apache.org/what-is-maven.html)