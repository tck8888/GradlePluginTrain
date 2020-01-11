[toc]
# Gradle Plugin学习笔记

## 创建插件工程
### 创建工程
![image](8F5A52847EE04C50AB338822E92A85B5)

> gradle插件不一使用groovy语言开发，只要是基于jvm环境的语言都可以，比如java和kotlin

### 添加依赖

![image](06DD261523904F1B8F521809059B304C)


在build.gradle添加依赖
```groovy
dependencies {
    compile 'org.codehaus.groovy:groovy-all:2.3.11'
    testCompile group: 'junit', name: 'junit', version: '4.12'

    //依赖
    compile localGroovy()
    compile gradleApi()

}
```

### 创建我们的自定义plugin
![image](E513D29552C048FC9377D6E00DFF2134)


```java
public class MyPlugin implements Plugin<Project> {

    @Override
    public void apply(Project target) {
        System.out.println("----hello gradle plugin.......");
        System.out.println("----hello gradle plugin.......");
        System.out.println("----hello gradle plugin.......");
    }
}

```

### 导出插件

![image](105F22BC7BFB4F41AE64EE496AA96850)

#### 创建插件

    src/main/resources/META-INF/gradle-plugins/{插件ID}.properties

#### {插件ID}.properties内容

```
//com.tck.plugin.MyPlugin为插件的全路径类名
implementation-class=com.tck.plugin.MyPlugin
```

### 插件的发布

#### 发布到远程仓库或者本地文件夹

##### 发布到本地文件夹

```groovy
//build.gradle下配置

plugins {
    id 'groovy'
    id 'java'
    id 'maven'
}

uploadArchives {
    repositories {
        mavenDeployer {
            repository(url: uri('./repo'))
            pom.project {
                groupId = 'com.tck'
                artifactId = 'MyPluginTrain'
                version = '1.0.0'
                description '学习自定义plugi'
            }
        }
    }
}
```
![image](C2BF7E2D1BC34DAC81922FC2B2ED3B23)
点击uploadArchives就可以发布到当前文件夹

##### 发布到远程仓库

这里我使用的nexus3

```groovy
uploadArchives {
    repositories {
        mavenDeployer {
            repository(url: '远程仓库地址') {
                authentication(userName: '用户名xxx', password: '密码xxx')
            }
            pom.project {
                groupId = 'com.tck'
                artifactId = 'MyPluginTrain'
                version = '1.0.0'
                description '学习自定义plugi'
            }
        }
    }
}
```
点击uploadArchives就可以发布到远程仓库

### 安装插件到本地仓库

```
//控制台输入
 gradlew clean install
 //位置在
 C:\Users\tck88\.m2\repository
```

   

==遇到乱码添加如下内容==

```
// java控制台输出中文乱码
tasks.withType(JavaCompile) {
    options.encoding = "UTF-8"
}
```
## 使用插件

### 创建Android工程
![image](9348B442B8A84F4DA60B69DF4654A02F)
### 修改根目录build.gradle
#### 使用插件的规则

    classpath 'groupId:artifactId:version'
    
```groovy

buildscript {
    repositories {
        google()
        jcenter()
        mavenLocal()
        
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.5.3'
        //这里就是使用自定义插件
        classpath 'com.tck:MyPluginTrain:1.0'
    }
}
```

### app目录下的build.gradle使用插件

    apply plugin: 'com.tck.helloplugin'

#### 使用规则

```
//插件工程下
src/main/resources/META-INF/gradle-plugins/{插件ID}.properties
    
apply plugin: '{插件ID}' 
```
![image](47D00ABD708C49B58DB08DD3A607849B)

构建一下
![image](1C12F8F2A06B4397A72A80B086E81F01)

![image](00A16BC710F941478E1136ECB2B67ABC)

## 调试插件

1. 插件工程
![image](4732746CE3E442DA8A8D667D7A8F71AC)
2. android工程

```
gradlew clean assembleDebug -Dorg.gradle.debug=true --no-daemon
```
![image](DA617E2B53904207AF20C6C0DD5046F3)

3. idea打上断点，开启调试
![image](36D18BFE0D9045BF93D5468C3E576664)

结果如下
![image](33D692F6510A4650BE6A245DCC7D6A61)






