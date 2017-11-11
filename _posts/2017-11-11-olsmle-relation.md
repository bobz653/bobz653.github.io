---
layout: post
title: "从最小二乘（OLS）和最大似然估计（MLE）的关系谈起"
description: "OLS MLE的关系"
author: "张波"
category: 机器学习
tags: [machine learning]
---

最近一段时间一直在忙碌工程项目上的事情，虽琐碎但也比较充实。了解到了大公司产研测的流转。使用devops类似的工具（具体内部工具就不说了）来去管理需求、开发、测试、上线部署、监控流程，实现进度和代码review等质量一系列的监管。开源的大家可以参考 **Gitlab + Phabircator**. 开发规范可以参考**Git-flow**。这不是本篇文章的重点，以后有时间可以详细说，还是回到算法模型上吧。

## 机器学习三要素
* 模型：这里我们可以定义为非条件概率模型和条件概率模型
  非条件概率 $$ F = \lbrace f|Y=f_\theta(X) \} $$
* 目标
* 算法


