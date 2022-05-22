---
layout: post
title: 自建java.lang.String可否被加载
# subtitle: 关于Java的知识点
cover-img: /assets/img/java/bg.png
thumbnail-img: /assets/img/java/java-logo.png
# full-width: true
tags: [整理总结,Java]
---

## 可不可以自己实现一个```java.lang.String```？
先说结论：**你可以写一个这样的类，但是无法加载**。下面来解释一下原因：  

首先，JVM类装载时使用```全盘负责委托机制```，其中，```全盘负责```指的是当一个ClassLoader装载一个类时，除非显示指定了其余的类加载器，否则涉及到的依赖类也全都由该加载器负责加载；```委托机制```指的是，当一个ClassLoader加载一个类时，首先会委托父类加载器去加载，只有父类加载器尚未加载该类，且该类也不在父类加载器的加载目录中时，当前类加载器才会去加载该类。

然后，Java自带了三个类加载器，分别是  

|加载器|负责范围|
|---|---|
|Bootstrap ClassLoader|最顶层的加载类，主要加载核心类库，也就是我们环境变量下面%JRE_HOME%\lib下的rt.jar、resources.jar、charsets.jar和class等。可以通过启动jvm时指定-Xbootclasspath和路径来改变Bootstrap ClassLoader的加载目录。|
|Extention ClassLoader|扩展的类加载器，加载目录%JRE_HOME%\lib\ext目录下的jar包和class文件。还可以加载-D java.ext.dirs选项指定的目录。|
|Appclass Loader|也称为SystemAppClass，加载当前应用的classpath的所有类。|

这三种类加载器的加载顺序：
Bootstrap ClassLoader > Extention ClassLoader > Appclass Loader  
所以，我们自定义的```java.lang.String```最终无法加载，因为Bootsrap类加载器始终会先将Java类库中是```java.lang.String```加载进去。  

再进一步说，如果我们建一个```java.lang.String```的class文件，然后使用自定义类加载器去加载，最终结果是会报错，因为在```java.lang.ClassLoader#preDefineClass```方法中可以看到，加载时对类名进行了校验：
{% highlight Java%}
private ProtectionDomain preDefineClass(String name,
                                            ProtectionDomain pd)
    {
        if (!checkName(name))
            throw new NoClassDefFoundError("IllegalName: " + name);

        // Note:  Checking logic in java.lang.invoke.MemberName.checkForTypeAlias
        // relies on the fact that spoofing is impossible if a class has a name
        // of the form "java.*"
        if ((name != null) && name.startsWith("java.")
                && this != getBuiltinPlatformClassLoader()) {
            throw new SecurityException
                ("Prohibited package name: " +
                 name.substring(0, name.lastIndexOf('.')));
        }
        if (pd == null) {
            pd = defaultDomain;
        }

        if (name != null) {
            checkCerts(name, pd.getCodeSource());
        }

        return pd;
    }
{% endhighlight%}


