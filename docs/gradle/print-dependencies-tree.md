---
title: 打印依赖树
date: 2022-10-09
tags:
  - Gradle
---
考虑以下场景：
* 查看哪个依赖是由哪个模块/组件/依赖引入。
* 查看某个（些）依赖的具体版本，排查版本不符合预期的问题。

这时，就需要查看项目/模块的依赖。

Gradle 提供了**查询依赖**的能力。

## 方法

### 打印模块的所有依赖
```bash
./gradlew :app:dependencies
```
其中，`app` 需要替换成实际的模块名。

这样会把模块的所有 `configuration` 的依赖打印出来。

这种方式打印出的信息很多。通常，我们仅关心部分配置的依赖，可以通过下述方式打印特定 `configureation` 的依赖。

### 打印某个 `configuration` 的依赖
```bash
./gradlew dependencies --configuration prodDebugRuntimeClasspath
```
其中，`prodDebugRuntimeClasspath` 需要替换成实际想查看依赖的 `configuration` 名字。

注意，`configuration` 的组成格式如下：
```bash
<variant-name><Debug-or-Release><Runtime-or-Compile>Classpath
```
其中，`Runtime` 表示运行时所需要的依赖，`Compile` 表示编译时所需的依赖。

### 查看特定依赖的使用情况
如果知道依赖的名字，可以通过 `dependencyInsight` 打印出它在项目中的使用情况：用了哪个版本、如何被引入的等等。
```bash
./gradlew :app:dependencyInsight --configuration prodDebugRuntimeClasspath --dependency androidx.activity
```
上述命令是在 `prodDebugRuntimeClasspath` 这个 `configuration` 中查询依赖 `androidx.activity` 的使用情况。

### 使用 Build Scan 进行可视化查看
```bash
./gradlew build --scan
```
这种方式会把编译的过程信息，上传到 gradle 官网。缺点如下：
* 存在安全风险
* 依赖网络
* 需要邮箱验证才能查看编译信息

## 技巧
如果打印的依赖树信息非常多，或者希望可视化查看依赖信息，参考如下解决方案。

### 将结果重定向输出到文件
```bash
./gradlew :app:dependencies 2>&1 > dep.txt
```
上述命令的含义是将 `./gradlew :app:dependencies` 执行过程中的输出（包括**标准输出**和**标准错误输出**），重定向到文件 `dep.txt` 中。

## 参考资料
* [Viewing and debugging dependencies](https://docs.gradle.org/current/userguide/viewing_debugging_dependencies.html)