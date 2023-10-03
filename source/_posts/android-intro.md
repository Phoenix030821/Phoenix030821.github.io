---
title: Android Studio 入门笔记
date: 2023-05-11 15:30:55
categories:
- 学习
tags:
- android
- 学习
description: 为了完成大作业，学了点连皮毛都算不上的东西。
---

创建新项目之后，代码在`src/main/java`中，其对应布局在 `src/main/res/layout`中。

`drawable`是用来设置背景

res中新建新目录`drawable-xhdpi`，然后将图片放入其中，然后在布局中设置背景为`@drawable/图片名`即可。

`values`中有colors,strings,styles,themes等，可以在其中设置颜色、字符串、样式、主题等。

`AndroidManifest.xml`中可以设置应用的名称、图标、版本号、权限等。

## LinearLayout和RelativeLayout

LinearLayout是线性布局，可以设置水平或垂直排列，可以设置权重。

LinearLayout中要设置排列是vertical还是horizontal，可以设置gravity属性，设置居中、居左、居右等。

layout_width或者height可以设置match_parent或者wrap_content，也可以设置具体的数值。

RelativeLayout是相对布局，可以设置控件相对于其他控件的位置。

在每个控件中要单独设置layout，相对布局

建议开始使用Linear Layout

padding, background等属性可以先记下。

## TextView, Button, 
Text控件可以设置width, height, color, size, textSize一般用sp单位，如"18sp"

maxEms可以设每一行最多字数

maxLines是最多多少行

注意密码要设置inputType为textPassword

