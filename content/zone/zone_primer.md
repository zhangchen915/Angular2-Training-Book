# 概述

## What?

A JavaScript VM is embedded in a host environment, such as a browser or Node, which is responsible for scheduling JavaScript execution through tasks. A Zone is an execution context that persists across async tasks, and allows the creator of the zone to observe and control execution of the code within the zone. 

JavaScript VM嵌入在主机环境中，例如浏览器或节点，负责通过任务调度JavaScript执行。 Zone 是跨越异步任务持续的执行上下文，并且允许 zone 的创建者观察和控制区域内的代码的执行。

zone 负责：

- 在异步任务执行中保持zone。
- 公开主机环境的任务调度和处理的可见性（范围限制到当前 zone）

## Why?

- Zones 可用于调试，测试，分析
- Zones 可以用来知道框架什么时候 render
- Zones 可用于跟踪跨异步操作持续的资源，并可自动释放/清除资源
- Zones 是可组合的