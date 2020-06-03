# SpringBoot

## 1.SprintBoot与SpringCloud

1. 历史

2. 第一个SpringBoot程序

- 网站新建 [Spring官网](https://start.spring.io/)
- IDEA新建 。因为IDEA集成了这个网站

> 1. 建立过程和SSM一样的初始信息
> 2. 端口修改
> 3. banner

>1. 建立过程和SSM一样的配置信息
>
>2. 端口修改
>
>3. banner

3. 自动装配原理初探

**pom.xml**:

- spring-boot-dependencies:核心的依赖都在父工程中
- 写入依赖不需要指定版本，因为有版本库



**启动器**

- ```xml
   <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starte</artifactId>
  </dependency>
  ```

- 启动器：就是SpringBoot 的启动场景

- 比如spring-boot-starte-web就会帮我们自动导入web环境的所有依赖

- springBoot会将一个个功能变成一个个启动器

- 我们需要使用什么功能，只需要找到对应的启动器 `starte`





**自动装配**

总结：SpringBoot 自动装配就是在启动的时候，扫描和加载：spring.factories里面的配置类。但并一定生效，需要经过判断是否需要。如果判断条件成立，就导入对应的starter，于是有了启动器。有了启动器，自动配置就会生效，然后配置成功。



1. SpringBoot从类路径spring.factories中，获取指定的值
2. 将这些自动配置类导入容器，自动配置生效。
3. 整合JavaEE，解决方案和自动配置都在spring-boot-autoconifgure-2.2.0.RELEASE.jar包下
4. 它将所需要的组件，以类名的方式返回，这些组件都会加载到容器中
5. 容器中有很多xxxAutoConfigure的类（@Bean),就是这些类导入了场景所需要的组件，并自动配置 
6. 有了自动配置类，免去我们手动编写配置文件的工作





Javaconfig    @Config (配置类)   @Bean(组件)

Docker: 进程  （以前以容器启动）





关于SpringBoot的理解

- 自动装配
- Run()   做了四个事情



SpringBoot配置了什么？，能不能修改

- XXXAutoConfiguration   向容器中自动配置组件
- XXXProperties    自动配置类，配置配置文件的自定义内容

## 2.yaml语法

### 1.基本语法

1. key-value

2. 对象

   ```yaml
   studnet: 
    name: hcn 
    age: 21
   #对空格要求严格
   #行内写法
   student: {name:hcn,age: 21}
   ```

   

3. 数组

   ```yaml
   pets:
    -dog
    -cat
    -pig
    #
    pets: [dog,cat,pig]
   ```

   >可以注入到配置类中

### 2.绑定属性

以前的：@Value("value")

现在: 

1. yaml

- 添加依赖（非必须）

```java
//springboot配置注解处理器
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-configuration-processor</artifactId>
</dependency>
```

- @ConfigurationProperties(prefix="person")

- EL表达式-占位符

  ```java
  person:
      name: qinjiang${random.uuid} # 随机uuid
      age: ${random.int}  # 随机int
      happy: false
      birth: 2000/01/01
      maps: {k1: v1,k2: v2}
      lists:
        - code
        - girl
        - music
      dog:
        name: ${person.hello:other}_旺财
        age: 1
  ```

2. properties文件

- @PropertySource(value = "classpath:person.properties")   				//加载指定的配置文件

​       @Value(${name})

3. 对比

@Value这个使用起来并不友好！我们需要为每个属性单独注解赋值，比较麻烦；我们来看个功能对比图

| @ConfigurationProperties | @Value               |          |
| ------------------------ | -------------------- | -------- |
| 功能                     | 批量注入配置文件属性 | 单个指定 |
| 松散绑定(语法)           | 支持                 | 不支持   |
| spEL                     | 不支持               | 支持     |
| JSR303校验               | 支持                 | 不支持   |
| 复杂类型                 | 支持                 | 不支持   |

1、@ConfigurationProperties只需要写一次即可 ， @Value则需要每个字段都添加

2、松散绑定：这个什么意思呢? 比如我的yml中写的last-name，这个和lastName是一样的， - 后面跟着的字母默认是大写的。这就是松散绑定。可以测试一下

3、JSR303数据校验 ， 这个就是我们可以在字段是增加一层过滤器验证 ， 可以保证数据的合法性

4、复杂类型封装，yml中可以封装对象 ， 使用value就不支持

**结论：**

配置yml和配置properties都可以获取到值 ， 强烈推荐 yml；

如果我们在某个业务中，只需要获取配置文件中的某个值，可以使用一下 @value；

如果说，我们专门编写了一个JavaBean来和配置文件进行一一映射，就直接@configurationProperties，不要犹豫！

### 3.JSR303与多环境切换

**JSR303**:

```java
@Validate		//开启验证
//有其他注解
@Email			//加在段上，是否为Email格式。还可以设置错误提示的值@Email(message="邮箱格式错误")
```

其他Constrain：

[IBM-JSR303](https://www.ibm.com/developerworks/cn/java/j-lo-jsr303/index.html)

**多环境**

1. 配置文件位置

   ```java
   优先级1：项目路径下的config文件夹配置文件
   优先级2：项目路径下配置文件
   优先级3：资源路径下的config文件夹配置文件
   优先级4：资源路径下配置文件
   ```

   

2. 环境配置

   - 新建多个配置文件（yaml可以不用，直接使用分隔符）
   - Spring.profile.active调用

[官方外部配置说明文档](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-external-config)

## 3.SpringBoot项目

### 静态资源

1. ```Java
   public void addResourceHandlers(ResourceHandlerRegistry registry) {
       if (!this.resourceProperties.isAddMappings()) {
           logger.debug("Default resource handling disabled");
       } else {
           Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
           CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
           //1.
           if (!registry.hasMappingForPattern("/webjars/**")) {
               this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
           }
   
           //2.
           
                   String staticPathPattern = this.mvcProperties.getStaticPathPattern();
                   if (!registry.hasMappingForPattern(staticPathPattern)) {
                       this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                   }
   
               }
           }
   
   ```



(1).SpringBoot处理静态资源

+ webjar       `localhost:8080/webjar/`

- public 、static、/**、resource        `localhost:8080/`

(2)优先级：resources>static>public

### 首页如何定制

```java
"classpath:/META-INF/resources/", "classpath:/resources/", "classpath:/static/", "classpath:/public/"
```

### 模板引擎Thymeleaf

**前提**：要使用Thymeleaf，只需导入对应的依赖，并将html放入template

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

```Java
public static final String DEFAULT_PREFIX = "classpath:/templates/";
public static final String DEFAULT_SUFFIX = ".html";
```

**使用**：导入约束，th可代替HTML==所有==的元素,  th:元素名

```xml
#约束
xmlns:th="http://www.thymeleaf.org"
```

```Java
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org" >
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <div th:text="${msg}"></div>
</body>
</html>
```

**语法**

取变量和遍历

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org" >
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <!--<div th:text="${msg}"></div>-->
    <div th:utext="${msg}"></div>  <!--转义-->
    <hr>
    <!--<h3 th:each="user:${users}" th:text="${user}"></h3>-->
    <h3 th:each="user:${users}" >[[${user}]]</h3>
</body>
</body>
</html>
```

