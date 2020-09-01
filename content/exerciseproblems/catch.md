---
title: "Try/catch"
date: 2020-09-01T07:07:27+08:00
draft: false
toc: false
images:
tags: [try,catch]
---
## Try/catch
1. 
```java
try {
    int i = 10 / 0;
} finally {
    System.out.println("last");
}
```
- 能够成功执行，并且会返回last，同时也会返回Exeception 提示：: / by zero 
2. finally 不一定都会被执行
- try里面包含`System.exit()`
- finally 里包含异常就会抛出异常并终止执行
```java
try {
    System.out.println("try");
} catch (Exception e) {
    System.out.println("catch");
} finally {
    int k = 3 / 0;
    System.out.println("finally");
}
System.out.println("main");
```


