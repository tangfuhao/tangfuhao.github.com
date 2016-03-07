---
title: android studio 配置ndk环境 利用jni创建demo
date: 2016-03-04 14:59:43
tags:
---


本篇文章用来介绍最新的android studio使用ndk工程的转换
所有操作设置来自官方文档，如有更新请参考官方文档
http://tools.android.com/tech-docs/new-build-system/gradle-experimental


## 环境说明

* mac os 10.11.2
* android studio 2.0 Beta5
* ndk r9d
* gradle-2.10-all.zip
* gradle-experimental:0.6.0-beta5

* android studio（以后简称as） 在1.3 Beta1版本引入NDK支持，
请确保你的as已有安装ndk support,验证方法 Preference...-> 搜索ndk 
结果如下图所示


## Step 1
创建一个empty工程备用，用来加入ndk支持, 并将项目视图Android切换为Project.
如果已经下载ndk，直接指定目录，否则下载ndk环境

* 下载设置ndk环境
／／图片

* 指定ndk目录
／／图片




## Step 2
* 修改gradle版本
修改在项目文件夹下的gradle/wrapper/gradle-wrapper.properties
{% codeblock %}
#Mon Dec 28 10:00:20 PST 2015
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-2.10-all.zip
{% endcodeblock %}
我这里用的是目前最新版的 https\://services.gradle.org/distributions/gradle-2.10-all.zip 


* 修改gradle编译工具的版本
在项目下的build.gradle文件中更改gradle版本，**注意是项目下的build.gradle**
{% codeblock %}
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle-experimental:0.6.0-beta5'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
{% endcodeblock %}

这里的build工具使用gradle-experimental，因为目前ndk加入还是比较不方便的实验版本，但功能其实是可用的。
我这里用的同样是最新的gradle-experimental:0.6.0-beta5

* gradle 和 gradle编译工具 版本依赖的问题
注意 gradle编译工具的版本对 gradle版本有兼容依赖的关系, 反则没有。
也就是说 比如最新的gradle-experimental:0.6.0-beta5 只有gradle-2.10-all.zip才能适用。
但我用最新的gradle-2.10-all.zip，使用低版本的gradle-experimental:0.2.0没有影响，所以这点需要注意。


**更新gradle设置可能需要下载新的文件，天朝网络可能被墙，请确保可以科学上网**


## Step 3

* 修改app的build.gradle 或者 library的build.gradle

目前只有app工程，修改它的build.gradle文件DSL写法
不同版本gradle编译工具的DSL写法不同，这里用的是最新版本的0.6.0-beta5
如果是以前版本写法可以参考官方 https://sites.google.com/a/android.com/tools/tech-docs/new-build-system/gradle-experimental/0-4-0

修改之前的代码：
{% codeblock %}
apply plugin: 'com.android.application'

android {
    compileSdkVersion 23
    buildToolsVersion "23.0.2"

    defaultConfig {
        applicationId "com.example.android.tang.androidstudiojnidemoforndk10"
        minSdkVersion 15
        targetSdkVersion 23
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'
    compile 'com.android.support:appcompat-v7:23.1.1'
}
{% endcodeblock %}


修改后的代码：
{% codeblock %}
apply plugin: "com.android.model.application"

model {
    android {
        compileSdkVersion 23
        buildToolsVersion "23.0.2"

        defaultConfig {
            applicationId "com.example.android.tang.androidstudiojnidemoforndk10"
            minSdkVersion.apiLevel 15
            targetSdkVersion.apiLevel 23
            versionCode 1
            versionName "1.0"
        }
        buildTypes {
            release {
                minifyEnabled false
                proguardFiles.add(file("proguard-rules.pro"))
            }
        }

        sources {
            main {
                java {
                    source {
                        srcDir "src"
                    }
                }
            }
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'
    compile 'com.android.support:appcompat-v7:23.1.1'
}
{% endcodeblock %}


* DSL的改变:
1. 用com.android.model.application代替com.android.application
2. 如果创建aarlibrary用com.android.model.library代替com.android.library 
3. 配置用model { } 块包装起来
4. 添加元素用add方法

* DSL的目前的局限性
1. 属性List只能接受唯一属性类型，比如你可以设置一个File使用String类型,但List<File>的类型列表只接受File对象。
2. 创建一个buildType或productFlavor需要调用create方法,只需要换一个名字，改变一个现有的Relese和Debug的buildType，
3. DSL修改变量和任务功能很一般

## Step 4 引入NDK

* 加入ndk编译module
在app目录下的build.gradle 加入
{% codeblock %}
ndk {                       
    moduleName "native"         //指定生成的lib，比如此时生成native.so
}
{% endcodeblock %}
之后为：
{% codeblock %}
model {
    android {
        compileSdkVersion 23
        buildToolsVersion "23.0.2"
        ndk {                       
            moduleName "native"         
        }
    }
}
{% endcodeblock %}

* 加入JNI目录

在目录app/src/main下创建jni目录，然后修改build.gradle 新增Source Set：
{% codeblock %}
sources {
    main {
        jni {
            source {
                srcDir "src"
            }
        }
    }
}
{% endcodeblock %}

## Step 5 代码中生成JNI代码

在activity中加载module native，加入本地方法test()

{% codeblock %}
public class MainActivity extends AppCompatActivity implements View.OnClickListener{
    static {
        System.loadLibrary("native");
    }

    public native String test();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        findViewById(R.id.button).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        Log.i("####",test());
    }
}
{% endcodeblock %}

代码提示test()方法，自动生成jni代码

／／图片

编译运行，搞定！







