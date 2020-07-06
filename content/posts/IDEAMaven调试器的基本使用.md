---
title: "IDEAMaven调试器的基本使用"
date: 2020-05-06T17:06:17+08:00
draft: false
---


#### SpotBugs
- Maven 生命周期  
设置生命周期后的Maven会在相应的阶段执行
Picture 设置
- 生命周期对应执行的工作需要绑定插件进行设置
1. complie —— goal ——> maven-compilen-plugin
2. test —— goal ——> surefire 测试插件
3. verlfy —— goal ——> checkstyle（非核心插件可以绑定到任何阶段