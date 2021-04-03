---
title: "Maven"
date: 2020-09-15T23:02:35+08:00
draft: true
tags: ["",""]
---
## Maven lifeCycle
1. verify 运行检查确保代码有效
2. integration-test (继承测试) 相比于 verify位 于maven生命周期的前几个（运行maven 的范围比 verify少）
>- 如有必要，将包处理和部署到可以运行集成测试的环境中。