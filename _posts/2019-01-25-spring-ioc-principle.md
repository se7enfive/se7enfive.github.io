---
layout: post
title: Spring IOC原理详解
subtitle: IOC容器、Bean生命周期、依赖注入与作用域
date: 2019-01-25
categories: 技术
tags: spring ioc 依赖注入 容器 生命周期
cover: 
---

Spring IOC（控制反转）是Spring框架的核心特性，理解IOC的原理、容器实现和Bean生命周期对于掌握Spring框架至关重要。

## IOC概念

### 什么是IOC
IOC（Inversion of Control）控制反转，是一种设计原则，它将对象的创建和依赖关系的管理交给外部容器来处理，而不是由对象自己创建依赖。

### 传统方式 vs IOC方式
```java
// 传统方式：对象自己创建依赖
public class UserService {
    private UserDao userDao = new UserDaoImpl();
    
    public void saveUser(User user) {
        userDao.save(user);
    }
}

// IOC方式：依赖由容器注入
@Service
public class UserService {
    @Autowired
    private UserDao userDao;
    
    public void saveUser(User user) {
        userDao.save(user);
    }
}
```

### IOC的优势
- **降低耦合度**：对象不需要知道依赖的具体实现
- **提高可测试性**：可以轻松注入模拟对象进行测试
- **提高可维护性**：修改依赖实现不需要修改使用方代码
- **提高灵活性**：可以在运行时决定使用哪个实现

## Spring容器高层视图

### 容器架构
```
ApplicationContext
├── BeanFactory (核心容器)
├── 国际化支持
├── 事件发布
├── 资源访问
└── 应用上下文
```

### 容器功能
- **Bean管理**：创建、配置、管理Bean的生命周期
- **依赖注入**：自动解析和注入依赖关系
- **配置管理**：管理应用配置信息
- **事件处理**：发布和处理应用事件

## IOC容器实现

### 1. BeanFactory接口
BeanFactory是Spring IoC容器的顶层接口，提供了基本的Bean管理功能。

```java
public interface BeanFactory {
    Object getBean(String name) throws BeansException;
    <T> T getBean(String name, Class<T> requiredType) throws BeansException;
    <T> T getBean(Class<T> requiredType) throws BeansException;
    boolean containsBean(String name);
    boolean isSingleton(String name) throws NoSuchBeanDefinitionException;
    Class<?> getType(String name) throws NoSuchBeanDefinitionException;
    String[] getAliases(String name);
}
```

### 2. ApplicationContext接口
ApplicationContext继承自BeanFactory，提供了更多企业级功能。

```java
public interface ApplicationContext extends BeanFactory {
    String getId();
    String getApplicationName();
    String getDisplayName();
    long getStartupDate();
    ApplicationContext getParent();
    AutowireCapableBeanFactory getAutowireCapableBeanFactory();
}
```

### 3. 容器实现类
```java
// 基于XML配置的容器
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

// 基于注解配置的容器
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

// Web应用容器
ApplicationContext context = new XmlWebApplicationContext();
```

## Spring Bean作用域

### 1. singleton（单例）
默认作用域，整个应用中只有一个Bean实例。

```java
@Service
@Scope("singleton")
public class UserService {
    // 单例Bean
}
```

### 2. prototype（原型）
每次获取Bean时都创建新的实例。

```java
@Service
@Scope("prototype")
public class UserService {
    // 原型Bean
}
```

### 3. request（请求）
每个HTTP请求创建一个Bean实例。

```java
@Component
@Scope("request")
public class UserService {
    // 请求作用域Bean
}
```

### 4. session（会话）
每个HTTP会话创建一个Bean实例。

```java
@Component
@Scope("session")
public class UserService {
    // 会话作用域Bean
}
```

### 5. globalSession（全局会话）
全局HTTP会话创建一个Bean实例。

```java
@Component
@Scope("globalSession")
public class UserService {
    // 全局会话作用域Bean
}
```

## Spring Bean生命周期

### 生命周期阶段
1. **实例化**：创建Bean实例
2. **属性设置**：设置Bean属性
3. **初始化**：调用初始化方法
4. **使用**：Bean可以被使用
5. **销毁**：调用销毁方法

### 生命周期接口
```java
@Component
public class MyBean implements InitializingBean, DisposableBean {
    
    @PostConstruct
    public void init() {
        System.out.println("Bean初始化");
    }
    
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("属性设置后");
    }
    
    @PreDestroy
    public void cleanup() {
        System.out.println("Bean清理");
    }
    
    @Override
    public void destroy() throws Exception {
        System.out.println("Bean销毁");
    }
}
```

### 生命周期回调
```java
@Configuration
public class AppConfig {
    
    @Bean(initMethod = "init", destroyMethod = "cleanup")
    public UserService userService() {
        return new UserService();
    }
}
```

## Spring依赖注入四种方式

### 1. 构造器注入
通过构造器参数注入依赖。

```java
@Service
public class UserService {
    private final UserDao userDao;
    
    public UserService(UserDao userDao) {
        this.userDao = userDao;
    }
}
```

### 2. Setter方法注入
通过Setter方法注入依赖。

```java
@Service
public class UserService {
    private UserDao userDao;
    
    @Autowired
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
}
```

### 3. 字段注入
直接在字段上注入依赖。

```java
@Service
public class UserService {
    @Autowired
    private UserDao userDao;
}
```

### 4. 方法注入
通过方法参数注入依赖。

```java
@Service
public class UserService {
    
    @Autowired
    public void configure(UserDao userDao, UserValidator validator) {
        // 配置依赖
    }
}
```

## 自动装配方式

### 1. no（默认）
不进行自动装配，需要手动指定依赖。

### 2. byName
根据属性名称自动装配。

```java
@Configuration
public class AppConfig {
    
    @Bean
    public UserDao userDao() {
        return new UserDaoImpl();
    }
    
    @Bean
    @Autowired(required = false)
    public UserService userService() {
        return new UserService();
    }
}
```

### 3. byType
根据属性类型自动装配。

```java
@Service
public class UserService {
    @Autowired
    private UserDao userDao; // 根据类型自动装配
}
```

### 4. constructor
根据构造器参数类型自动装配。

```java
@Service
public class UserService {
    private final UserDao userDao;
    
    public UserService(UserDao userDao) {
        this.userDao = userDao;
    }
}
```

### 5. autodetect
自动检测使用构造器还是Setter方法。

## IOC容器的工作流程

### 1. 容器启动
```java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
```

### 2. 扫描组件
容器扫描配置的包，找到所有需要管理的Bean。

### 3. 创建Bean定义
为每个Bean创建BeanDefinition对象。

### 4. 实例化Bean
根据Bean定义创建Bean实例。

### 5. 依赖注入
解析并注入Bean的依赖关系。

### 6. 初始化Bean
调用Bean的初始化方法。

### 7. 使用Bean
Bean可以被应用程序使用。

### 8. 销毁Bean
容器关闭时销毁Bean。

## 总结

Spring IOC是Spring框架的核心特性，它通过控制反转和依赖注入简化了企业级应用的开发。理解IOC的原理、容器实现和Bean生命周期对于掌握Spring框架至关重要。

—— 完 ——
