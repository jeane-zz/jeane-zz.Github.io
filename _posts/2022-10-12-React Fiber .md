---
layout:     post
title:      React Fiber
subtitle:
date:       2022-10-12
author:     Jeane
header-img: img/post-bg-swift2.jpg
catalog: true
tags:
    - react fiber
    - 学习笔记
---
·

# React Fiber

## Fiber 是什么

从架构角度来看，Fiber 是对 React核心算法（即调和过程）的重写

从编码角度来看，Fiber是 React内部所定义的一种数据结构，它是 Fiber树结构的节点单位，也就是 React 16 新架构下的虚拟DOM

##### 参考
https://vue3js.cn/interview/React/Fiber.html#%E4%BA%8C%E3%80%81%E6%98%AF%E4%BB%80%E4%B9%88

## Fiber解决了什么问题

### js渲染特征

JavaScript引擎和页面渲染引擎两个线程是互斥的，当其中一个线程执行时，另一个线程只能挂起等待

如果 JavaScript 线程长时间地占用了主线程，那么渲染层面的更新就不得不长时间地等待，界面长时间不更新，会导致页面响应度变差，用户可能会感觉到卡顿

这就是React 15版本 Stack Reconciler 面临的问题，如果组件较大，那么js线程会一直执行，然后等到整棵VDOM树计算完成后，才会交给渲染的线程

### 需要解决的问题：
reconciler 在协调的时候能否被打断暂停
进行 DOM diff 时，如何在 16ms 时间窗内不阻塞浏览器渲染

### 解决方案
将运算进行切割，切分为多个 work unit（工作单元），分批完成。
在完成一个 work unit 之后，将主线程控制权交回给浏览器，如果浏览器有 UI 渲染工作要做的话，能让其在 16ms 的窗口期内，占用主线程有时间去做，而不像之前主线程被 stack 递归栈一直霸占而不得释放。在浏览器使用主线程完成渲染工作，有空闲时间后，再回到之前未完成的任务点继续完成剩余的 work unit。

pause work and come back to it later（暂停工作，并且能之后回到暂停的地方）
assign priority to different types of work（安排不同类型工作的优先级）
reuse previously completed work（之前已经处理完的工作单元，可以得到重用）
abort work if it's no longer needed（如果后续的工作不再需要做，工作可以直接被终止）

##### 参考
https://juejin.cn/post/7066456735015239716