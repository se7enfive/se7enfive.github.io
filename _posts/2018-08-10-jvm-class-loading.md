---
layout: post
title: JVM类加载机制
subtitle: 类加载过程、类加载器与双亲委派模型
date: 2018-08-10
categories: 技术
tags: java jvm 类加载
cover: 
---

JVM类加载机制是Java程序运行的基础，理解类加载过程、类加载器和双亲委派模型对于Java开发至关重要。

## 类加载概述

### 什么是类加载
类加载是JVM将字节码文件加载到内存中，并转换为可以被JVM直接使用的Java类的过程。

### 类加载的时机
1. **遇到new、getstatic、putstatic或invokestatic指令**
2. **使用java.lang.reflect包的方法对类进行反射调用**
3. **初始化一个类时，如果其父类还没有被初始化**
4. **虚拟机启动时，用户指定的主类**
5. **使用JDK1.7的动态语言支持时**

### 类加载的过程
类加载过程分为五个阶段：加载、验证、准备、解析、初始化。

## 加载（Loading）

### 加载阶段的任务
1. **获取字节码**：通过类的全限定名获取字节码文件
2. **创建Class对象**：在内存中创建一个代表这个类的java.lang.Class对象
3. **存储到方法区**：将字节码存储在方法区中

### 加载方式
- **从文件系统加载**：从.class文件加载
- **从JAR包加载**：从JAR包中加载
- **从网络加载**：从网络URL加载
- **动态生成**：运行时动态生成

### 加载示例
```java
public class ClassLoadingExample {
    public void loadClass() throws ClassNotFoundException {
        // 通过Class.forName加载类
        Class<?> clazz = Class.forName("java.lang.String");
        
        // 通过类加载器加载类
        ClassLoader loader = ClassLoader.getSystemClassLoader();
        Class<?> clazz2 = loader.loadClass("java.lang.String");
        
        // 通过类字面量加载类
        Class<?> clazz3 = String.class;
    }
}
```

## 验证（Verification）

### 验证阶段的任务
验证阶段确保字节码文件的正确性，防止恶意代码的执行。

### 验证内容
1. **文件格式验证**：验证字节码文件格式
2. **元数据验证**：验证类的元数据信息
3. **字节码验证**：验证字节码指令
4. **符号引用验证**：验证符号引用的正确性

### 验证示例
```java
public class VerificationExample {
    public void verifyClass() {
        // 验证类文件格式
        if (!isValidClassFile()) {
            throw new ClassFormatError("Invalid class file format");
        }
        
        // 验证元数据
        if (!isValidMetadata()) {
            throw new NoSuchFieldError("Invalid metadata");
        }
        
        // 验证字节码
        if (!isValidBytecode()) {
            throw new VerifyError("Invalid bytecode");
        }
    }
}
```

## 准备（Preparation）

### 准备阶段的任务
为类的静态变量分配内存并设置初始值。

### 准备内容
1. **分配内存**：为静态变量分配内存
2. **设置初始值**：设置静态变量的初始值
3. **常量池解析**：解析常量池中的符号引用

### 准备示例
```java
public class PreparationExample {
    // 准备阶段：为这些静态变量分配内存并设置初始值
    public static int value = 123;        // 初始值为0
    public static final int CONSTANT = 456; // 初始值为456
    public static String str = "hello";   // 初始值为null
    
    public static void main(String[] args) {
        System.out.println("value: " + value);     // 输出: 0
        System.out.println("CONSTANT: " + CONSTANT); // 输出: 456
        System.out.println("str: " + str);         // 输出: null
    }
}
```

## 解析（Resolution）

### 解析阶段的任务
将常量池中的符号引用替换为直接引用。

### 解析内容
1. **类或接口解析**：解析类或接口的符号引用
2. **字段解析**：解析字段的符号引用
3. **方法解析**：解析方法的符号引用
4. **接口方法解析**：解析接口方法的符号引用

### 解析示例
```java
public class ResolutionExample {
    public void resolveSymbols() {
        // 解析类引用
        Class<?> clazz = String.class;
        
        // 解析字段引用
        try {
            Field field = clazz.getDeclaredField("value");
        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        }
        
        // 解析方法引用
        try {
            Method method = clazz.getDeclaredMethod("length");
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        }
    }
}
```

## 初始化（Initialization）

### 初始化阶段的任务
执行类的初始化代码，包括静态变量赋值和静态代码块。

### 初始化内容
1. **静态变量赋值**：为静态变量赋值
2. **静态代码块执行**：执行静态代码块
3. **类构造器执行**：执行类构造器<clinit>方法

### 初始化示例
```java
public class InitializationExample {
    // 静态变量
    public static int value = 123;
    
    // 静态代码块
    static {
        System.out.println("静态代码块执行");
        value = 456;
    }
    
    // 类构造器
    static {
        System.out.println("类构造器执行");
    }
    
    public static void main(String[] args) {
        System.out.println("value: " + value); // 输出: 456
    }
}
```

## 类加载器

### 类加载器层次结构
1. **启动类加载器（Bootstrap ClassLoader）**
2. **扩展类加载器（Extension ClassLoader）**
3. **应用程序类加载器（Application ClassLoader）**

### 启动类加载器
- **作用**：加载核心类库
- **路径**：JAVA_HOME/lib目录
- **特点**：无法被Java程序直接引用

### 扩展类加载器
- **作用**：加载扩展类库
- **路径**：JAVA_HOME/lib/ext目录
- **特点**：由启动类加载器加载

### 应用程序类加载器
- **作用**：加载应用程序类
- **路径**：classpath路径
- **特点**：由扩展类加载器加载

### 类加载器示例
```java
public class ClassLoaderExample {
    public void demonstrateClassLoaders() {
        // 获取系统类加载器
        ClassLoader systemLoader = ClassLoader.getSystemClassLoader();
        System.out.println("系统类加载器: " + systemLoader);
        
        // 获取扩展类加载器
        ClassLoader extLoader = systemLoader.getParent();
        System.out.println("扩展类加载器: " + extLoader);
        
        // 获取启动类加载器
        ClassLoader bootstrapLoader = extLoader.getParent();
        System.out.println("启动类加载器: " + bootstrapLoader);
        
        // 获取当前类的类加载器
        ClassLoader currentLoader = this.getClass().getClassLoader();
        System.out.println("当前类加载器: " + currentLoader);
    }
}
```

## 双亲委派模型

### 双亲委派模型原理
双亲委派模型要求除了顶层的启动类加载器外，其余的类加载器都应当有自己的父类加载器。

### 委派过程
1. **向上委派**：子类加载器将加载请求委派给父类加载器
2. **向下委派**：父类加载器无法加载时，子类加载器尝试加载
3. **缓存机制**：已加载的类会被缓存

### 双亲委派模型示例
```java
public class ParentDelegationExample {
    public void demonstrateDelegation() {
        // 创建自定义类加载器
        ClassLoader customLoader = new ClassLoader() {
            @Override
            public Class<?> loadClass(String name) throws ClassNotFoundException {
                // 先委派给父类加载器
                try {
                    return super.loadClass(name);
                } catch (ClassNotFoundException e) {
                    // 父类加载器无法加载时，自己尝试加载
                    return findClass(name);
                }
            }
            
            @Override
            protected Class<?> findClass(String name) throws ClassNotFoundException {
                // 自定义加载逻辑
                return super.findClass(name);
            }
        };
    }
}
```

### 双亲委派模型的优点
1. **避免重复加载**：防止同一个类被多次加载
2. **保证安全性**：防止恶意代码替换核心类
3. **提高效率**：利用缓存机制提高加载效率

## 自定义类加载器

### 自定义类加载器示例
```java
public class CustomClassLoader extends ClassLoader {
    private String classPath;
    
    public CustomClassLoader(String classPath) {
        this.classPath = classPath;
    }
    
    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        try {
            // 读取字节码文件
            byte[] classData = loadClassData(name);
            if (classData != null) {
                // 定义类
                return defineClass(name, classData, 0, classData.length);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return super.findClass(name);
    }
    
    private byte[] loadClassData(String name) throws IOException {
        String fileName = name.replace('.', '/') + ".class";
        String fullPath = classPath + "/" + fileName;
        
        try (FileInputStream fis = new FileInputStream(fullPath);
             ByteArrayOutputStream baos = new ByteArrayOutputStream()) {
            
            byte[] buffer = new byte[1024];
            int bytesRead;
            while ((bytesRead = fis.read(buffer)) != -1) {
                baos.write(buffer, 0, bytesRead);
            }
            return baos.toByteArray();
        }
    }
}
```

## 类加载器应用

### 热部署
```java
public class HotDeploymentExample {
    private CustomClassLoader classLoader;
    
    public void hotDeploy() throws Exception {
        // 创建新的类加载器
        classLoader = new CustomClassLoader("target/classes");
        
        // 加载新版本的类
        Class<?> clazz = classLoader.loadClass("com.example.MyClass");
        Object instance = clazz.newInstance();
        
        // 调用新版本的方法
        Method method = clazz.getDeclaredMethod("doSomething");
        method.invoke(instance);
    }
}
```

### 插件系统
```java
public class PluginSystemExample {
    private Map<String, ClassLoader> pluginLoaders = new HashMap<>();
    
    public void loadPlugin(String pluginName, String pluginPath) throws Exception {
        // 为每个插件创建独立的类加载器
        ClassLoader pluginLoader = new CustomClassLoader(pluginPath);
        pluginLoaders.put(pluginName, pluginLoader);
        
        // 加载插件主类
        Class<?> pluginClass = pluginLoader.loadClass("com.plugin.Main");
        Object plugin = pluginClass.newInstance();
        
        // 注册插件
        registerPlugin(pluginName, plugin);
    }
}
```

## 性能优化

### 优化策略
1. **预加载类**：提前加载常用的类
2. **缓存机制**：利用类加载器的缓存机制
3. **并行加载**：使用并行类加载器
4. **减少类加载**：减少不必要的类加载

### 配置参数
```bash
# 设置类加载器并行度
-Djava.util.concurrent.ForkJoinPool.common.parallelism=4

# 启用类加载器并行加载
-XX:+UseParallelClassLoading
```

## 总结

JVM类加载机制是Java程序运行的基础，理解类加载过程、类加载器和双亲委派模型对于Java开发至关重要。在实际开发中，应该根据应用的特点和需求来选择合适的类加载策略，并进行相应的性能优化。

—— 完 ——
