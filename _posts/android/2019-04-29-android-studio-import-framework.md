---
layout: post
title: "Android Studio导入Framework.jar"
categories: Android
tags: 'Android Studio'
---

* content
{:toc}

## 描述

很多时候在开发系统 App 的时候需要用到 Android Framework 中的接口，此时就需要导入 `framework.jar`。
由于默认情况下，Android Studio 在解析类依赖的时候会将 Android Sdk 放到最前面。如果 Sdk
中也有你要用到的类，但是你要用到的方法却是隐藏的，这就会导致 Android Studio 报找不到方法的错误。

## 解决办法

打开 Android Studio 工程模块目录下的 `<module-name>.iml`，如果模块名为 `app`，默认情况下
该文件为 `app.iml`。 打开该文件，找到以 `<orderEntry type="library"` 开头的行，可以发现
第一个就是 Android Sdk。我们只需要将该行移动到最后面即可。但是这样做等你清理项目之后改动应该
会丢失，我们可以更改 build.gradle 使系统自动在build之前完成该任务。

### 1. 更改模块级的配置文件

更改模块级配置文件，让 gradle 在开始编译之前，自动将 Android Sdk 移动到最后面。在 `app/build.gradle`
的最外层添加如下代码：

```groovy
preBuild {
    doLast {
        def imlFile = file(project.name + ".iml")
        println 'Change ' + project.name + '.iml order'
        try {
            def parsedXml = (new XmlParser()).parse(imlFile)
            def jdkNode = parsedXml.component[1].orderEntry.find { it.'@type' == 'jdk' }
            parsedXml.component[1].remove(jdkNode)
            def sdkString = "Android API " + android.compileSdkVersion.substring("android-".length()) + " Platform"
            new Node(parsedXml.component[1], 'orderEntry', ['type': 'jdk', 'jdkName': sdkString, 'jdkType': 'Android SDK'])
            groovy.xml.XmlUtil.serialize(parsedXml, new FileOutputStream(imlFile))
            } catch (FileNotFoundException e) {
            // nop, iml not found
            }
        }
}
```

截图如下：
![截图](/asserts/images/android/android-studio-import-framework/prebuild.png)

### 2. 更改项目级别配置文件

在项目级别的 build.gradle 里面配置提高编译时 lib 库的引用优先级，如果有多个 jar 包就用分号隔开。

```groovy
  gradle.projectsEvaluated {
        tasks.withType(JavaCompile) {
            options.compilerArgs.add('-Xbootclasspath/p:app\\libs\\framework.jar')
        }
    }
```
截图如下：

![截图](/asserts/images/android/android-studio-import-framework/improve-framework.png)


更改之后再次编译，所有标红的地方都会自动消失。