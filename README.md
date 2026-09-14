# 实验一 Android 开发基础

## 一、实验目的

1. 掌握 Android Studio 的安装与配置方法，熟悉开发环境；
2. 学会创建第一个 Android 工程（HelloWorld），理解工程目录结构；
3. 掌握 Gradle 构建体系的基本概念，能够解决同步（Sync）过程中的常见问题；
4. 掌握使用 Git / GitHub Desktop 将本地工程同步至 GitHub 的完整流程；
5. 了解 Android 工程忽略文件（.gitignore）的作用。

## 二、实验环境

| 项目                        | 版本/说明               |
| --------------------------- | ----------------------- |
| 操作系统                    | Windows 11              |
| Android Studio              | Quail 3 (2026.1.3)      |
| Android Gradle Plugin (AGP) | 9.3.1                   |
| Gradle                      | 9.5.0                   |
| Kotlin                      | 2.2.10                  |
| JDK                         | Android Studio 内置 JBR |
| Git 工具                    | GitHub Desktop          |
| 代码托管平台                | GitHub                  |

## 三、实验内容与步骤

### 3.1 安装 Android Studio

从官网下载最新版 Android Studio（Quail 系列），按向导完成安装。首次启动时会下载 SDK 组件，需要保持网络畅通。

![image-20260914171215635](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20260914171215635.png)

### 3.2 创建第一个 Android 工程

1. 打开 Android Studio，选择 ***\*New Project\****；
2. 选择 ***\*Empty Activity\**** 模板；
3. 配置项目信息：

- Name：`HelloAndroid`
- Package name：`com.example.helloandroid`
- Language：Kotlin
- Minimum SDK：API 24

1. 点击 ***\*Finish\****，等待工程创建完成。

![image-20260914171236883](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20260914171236883.png)

### 3.3 解决 Gradle 同步失败问题

新建工程后首次 Sync 时遇到了一系列报错，通过排查逐一解决：

***\*问题1：Gradle 发行包下载超时\****

> Could not install Gradle distribution from 'https://services.gradle.org/distributions/gradle-9.5.0-bin.zip'
> Reason: java.net.SocketTimeoutException: Read timed out

***\*解决\****：修改 `gradle/wrapper/gradle-wrapper.properties`，将 `distributionUrl` 改为国内腾讯镜像：

```
distributionUrl=https\://mirrors.cloud.tencent.com/gradle/gradle-9.5.0-bin.zip
```

![image-20260914171304329](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20260914171304329.png)

***\*问题2：AGP 插件无法解析\****

> Plugin [id: 'com.android.application', version: '9.3.x', apply: false] was not found

***\*分析\****：报错的直接原因是无法访问 Google Maven 仓库（网络不通），Gradle 统一报"找不到插件"。

***\*解决\****：在项目根目录 `settings.gradle.kts` 的 `pluginManagement` 和 `dependencyResolutionManagement` 中配置阿里云镜像（置于仓库列表最前）：

```
pluginManagement {
    repositories {
        maven { url = uri("https://maven.aliyun.com/repository/google") }
        maven { url = uri("https://maven.aliyun.com/repository/central") }
        maven { url = uri("https://maven.aliyun.com/repository/gradle-plugin") }
        google()
        mavenCentral()
        gradlePluginPortal()
    }
}
```

![image-20260914171323496](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20260914171323496.png)

同步成功后，界面不再显示 "Gradle project sync failed" 提示。

![image-20260914171405686](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20260914171405686.png)

### 3.4 运行 HelloWorld 程序

MainActivity.kt 中使用 Jetpack Compose 显示 "Hello Android!" 文本：

```
setContent {
    HelloAndroidTheme {
        Scaffold(modifier = Modifier.fillMaxSize()) { innerPadding ->
            Greeting(
                name = "Android",
                modifier = Modifier.padding(innerPadding)
            )
        }
    }
}
```

![image-20260914171718747](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20260914171718747.png)

### 3.5 注册 GitHub 账号并安装 Git 工具

1. 在 GitHub 官网注册个人账号；
2. 安装 GitHub Desktop（图形化 Git 客户端），首次启动时登录 GitHub 账号；
3. 首次使用时客户端会自动配置提交身份（Name 与 Email，使用 Gitee/GitHub 的 noreply 隐私邮箱避免真实邮箱泄露）。

【截图7：GitHub 账号主页 / GitHub Desktop 登录界面】

### 3.6 将工程同步至 GitHub

1. 打开 GitHub Desktop，选择 ***\*Add an Existing Repository from your local drive\****，选择工程目录 `D:\Javacode\FJNU\Android\HelloAndroid`；
2. 提示目录还不是 Git 仓库时，点击 ***\*create a repository here\**** 完成初始化（沿用工程已有的 `.gitignore`，避免把 build 产物等提交上去）；
3. 在 Summary 中填写 `Initial commit`，点击 ***\*Commit to main\****；
4. 点击顶部 ***\*Publish repository\****，选择公开/私有后发布到 GitHub；
5. 发布完成后点击 ***\*View on GitHub\**** 在浏览器中确认仓库内容。

![image-20260914171908884](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20260914171908884.png)

![image-20260914171809768](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20260914171809768.png)

### 3.7 Android 工程忽略文件

Android 工程的 `.gitignore` 用于排除不需要版本控制的文件，例如：

- `build/`：编译生成的中间产物和 APK；
- `.idea/`：IDE 的本地配置文件；
- `local.properties`：本机 SDK 路径等本地配置；
- `*.iml`：模块配置文件。

参考官方模板：https://github.com/github/gitignore/blob/main/Android.gitignore

【截图11：工程中的 .gitignore 文件内容】

![image-20260914170819880](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20260914170819880.png)

## 四、实验结果

1. 成功安装并配置 Android Studio（Quail 3），创建了第一个 Kotlin + Compose 工程 HelloAndroid；
2. 通过配置国内镜像（腾讯 Gradle 镜像、阿里云 Maven 镜像）解决了 Gradle 下载超时和 AGP 插件解析失败的问题；
3. 程序在模拟器上成功运行，显示 "Hello Android!"；
4. 工程已通过 GitHub Desktop 成功发布到 GitHub。

仓库地址：https://github.com/lll-ssbb/HelloAndroid

## 五、实验总结

1. 掌握了 Android Studio 新建工程的基本流程，理解了 build.gradle.kts、settings.gradle.kts、gradle-wrapper.properties 等核心配置文件的作用；
2. 理解了 Gradle、AGP、版本目录（libs.versions.toml）之间的关系：Gradle 是构建系统，AGP 是适配 Android 的插件，插件版本由版本目录统一管理；
3. 遇到 "plugin not found" 报错时，要学会区分是"版本号不存在"还是"仓库访问不通"，两种问题的排查思路不同；
4. 掌握了 Git 的基本使用：init → add → commit → publish，以及 .gitignore 的作用；
5. 国内网络环境下开发 Android 需要善用国内镜像源，可以显著提高依赖下载成功率。