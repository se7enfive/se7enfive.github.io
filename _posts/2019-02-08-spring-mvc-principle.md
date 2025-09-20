---
layout: post
title: Spring MVC原理详解
subtitle: MVC架构、请求处理流程、核心组件与配置
date: 2019-02-08
categories: 技术
tags: spring mvc web框架 请求处理 控制器
cover: 
---

Spring MVC是Spring框架的Web模块，它基于MVC架构模式构建Web应用程序。理解Spring MVC的原理、请求处理流程和核心组件对于开发Web应用至关重要。

## Spring MVC概述

### MVC架构模式
MVC（Model-View-Controller）是一种软件架构模式，将应用程序分为三个核心部分：
- **Model（模型）**：处理业务逻辑和数据
- **View（视图）**：处理用户界面和展示
- **Controller（控制器）**：处理用户输入和协调模型与视图

### Spring MVC特点
- **松耦合**：各组件之间松耦合，易于测试和维护
- **可配置**：支持多种配置方式
- **可扩展**：支持多种视图技术
- **国际化**：内置国际化支持
- **表单处理**：强大的表单处理能力

## Spring MVC核心组件

### 1. DispatcherServlet
前端控制器，负责接收所有请求并分发给相应的处理器。

```java
@WebServlet(name = "dispatcher", urlPatterns = "/", loadOnStartup = 1)
public class DispatcherServlet extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // 处理GET请求
        processRequest(request, response);
    }
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // 处理POST请求
        processRequest(request, response);
    }
}
```

### 2. HandlerMapping
处理器映射器，负责根据请求URL找到对应的处理器。

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
        registry.addViewController("/login").setViewName("login");
    }
}
```

### 3. HandlerAdapter
处理器适配器，负责调用具体的处理器方法。

```java
@Controller
@RequestMapping("/user")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping("/{id}")
    public String getUser(@PathVariable Long id, Model model) {
        User user = userService.findById(id);
        model.addAttribute("user", user);
        return "user/detail";
    }
}
```

### 4. ViewResolver
视图解析器，负责根据视图名称解析出具体的视图对象。

```java
@Configuration
public class ViewConfig {
    
    @Bean
    public ViewResolver viewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        return resolver;
    }
}
```

## Spring MVC请求处理流程

### 1. 请求接收
用户发送HTTP请求到Web服务器，请求被DispatcherServlet接收。

### 2. 处理器映射
DispatcherServlet通过HandlerMapping找到对应的处理器。

```java
@Controller
public class HomeController {
    
    @RequestMapping(value = "/", method = RequestMethod.GET)
    public String home(Model model) {
        model.addAttribute("message", "Welcome to Spring MVC!");
        return "home";
    }
}
```

### 3. 处理器适配
HandlerAdapter调用具体的处理器方法。

### 4. 处理器执行
处理器执行业务逻辑并返回ModelAndView。

```java
@Controller
public class UserController {
    
    @GetMapping("/users")
    public ModelAndView getUsers() {
        List<User> users = userService.findAll();
        ModelAndView modelAndView = new ModelAndView("user/list");
        modelAndView.addObject("users", users);
        return modelAndView;
    }
}
```

### 5. 视图解析
ViewResolver根据视图名称解析出具体的视图对象。

### 6. 视图渲染
视图对象渲染响应并返回给用户。

## 控制器开发

### 1. 基本控制器
```java
@Controller
public class BasicController {
    
    @RequestMapping("/hello")
    public String hello() {
        return "hello";
    }
}
```

### 2. RESTful控制器
```java
@RestController
@RequestMapping("/api/users")
public class UserRestController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping
    public List<User> getUsers() {
        return userService.findAll();
    }
    
    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }
    
    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.save(user);
    }
    
    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        return userService.update(id, user);
    }
    
    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userService.delete(id);
    }
}
```

### 3. 参数绑定
```java
@Controller
public class ParameterController {
    
    @GetMapping("/user")
    public String getUser(@RequestParam String name, 
                         @RequestParam(defaultValue = "0") int age,
                         Model model) {
        model.addAttribute("name", name);
        model.addAttribute("age", age);
        return "user/info";
    }
    
    @PostMapping("/user")
    public String createUser(@ModelAttribute User user) {
        userService.save(user);
        return "redirect:/users";
    }
}
```

## 视图技术

### 1. JSP视图
```java
@Configuration
public class JspConfig {
    
    @Bean
    public ViewResolver jspViewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        return resolver;
    }
}
```

### 2. Thymeleaf视图
```java
@Configuration
public class ThymeleafConfig {
    
    @Bean
    public ViewResolver thymeleafViewResolver() {
        ThymeleafViewResolver resolver = new ThymeleafViewResolver();
        resolver.setTemplateEngine(templateEngine());
        return resolver;
    }
    
    @Bean
    public SpringTemplateEngine templateEngine() {
        SpringTemplateEngine engine = new SpringTemplateEngine();
        engine.setTemplateResolver(templateResolver());
        return engine;
    }
}
```

### 3. JSON视图
```java
@RestController
public class JsonController {
    
    @GetMapping("/api/data")
    public ResponseEntity<Map<String, Object>> getData() {
        Map<String, Object> data = new HashMap<>();
        data.put("message", "Hello World");
        data.put("timestamp", System.currentTimeMillis());
        return ResponseEntity.ok(data);
    }
}
```

## 数据绑定和验证

### 1. 表单数据绑定
```java
@Controller
public class FormController {
    
    @GetMapping("/user/form")
    public String showForm(Model model) {
        model.addAttribute("user", new User());
        return "user/form";
    }
    
    @PostMapping("/user/save")
    public String saveUser(@ModelAttribute @Valid User user, 
                          BindingResult result) {
        if (result.hasErrors()) {
            return "user/form";
        }
        userService.save(user);
        return "redirect:/users";
    }
}
```

### 2. 数据验证
```java
public class User {
    
    @NotNull
    @Size(min = 2, max = 50)
    private String name;
    
    @NotNull
    @Email
    private String email;
    
    @Min(18)
    @Max(100)
    private Integer age;
    
    // getter和setter方法
}
```

### 3. 异常处理
```java
@ControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(UserNotFoundException.class)
    public String handleUserNotFound(UserNotFoundException ex, Model model) {
        model.addAttribute("error", ex.getMessage());
        return "error/user-not-found";
    }
    
    @ExceptionHandler(Exception.class)
    public String handleGenericException(Exception ex, Model model) {
        model.addAttribute("error", "系统错误");
        return "error/generic";
    }
}
```

## 拦截器

### 1. 自定义拦截器
```java
public class LoggingInterceptor implements HandlerInterceptor {
    
    @Override
    public boolean preHandle(HttpServletRequest request, 
                           HttpServletResponse response, 
                           Object handler) throws Exception {
        System.out.println("请求开始: " + request.getRequestURI());
        return true;
    }
    
    @Override
    public void postHandle(HttpServletRequest request, 
                          HttpServletResponse response, 
                          Object handler, 
                          ModelAndView modelAndView) throws Exception {
        System.out.println("请求处理完成: " + request.getRequestURI());
    }
    
    @Override
    public void afterCompletion(HttpServletRequest request, 
                               HttpServletResponse response, 
                               Object handler, 
                               Exception ex) throws Exception {
        System.out.println("请求结束: " + request.getRequestURI());
    }
}
```

### 2. 拦截器配置
```java
@Configuration
public class InterceptorConfig implements WebMvcConfigurer {
    
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoggingInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns("/static/**");
    }
}
```

## Spring MVC配置

### 1. 基于注解配置
```java
@Configuration
@EnableWebMvc
@ComponentScan("com.example.controller")
public class WebConfig implements WebMvcConfigurer {
    
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        registry.jsp("/WEB-INF/views/", ".jsp");
    }
    
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**")
                .addResourceLocations("/static/");
    }
}
```

### 2. 基于XML配置
```xml
<beans>
    <mvc:annotation-driven/>
    <mvc:resources mapping="/static/**" location="/static/"/>
    
    <context:component-scan base-package="com.example.controller"/>
    
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```

## 总结

Spring MVC是构建Web应用的强大框架，它基于MVC架构模式，提供了完整的Web开发解决方案。理解Spring MVC的核心组件、请求处理流程和配置方式对于开发高质量的Web应用至关重要。

—— 完 ——
