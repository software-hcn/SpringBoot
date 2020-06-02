# SpringBoot

## 1.SprintBoot与SpringCloud

## 2.第一个SpringBoot程序

1. 网站新建 [Spring官网](https://start.spring.io/)
2. IDEA新建 。因为IDEA集成了这个网站

>1. 建立过程和SSM一样的配置信息
>
>2. 端口修改
>
>3. banner

## 3.自动装配原理

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



## 4.yaml语法

**基本语法**

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

   **设置属性**

   以前的：@Value("value")

   现在: 

   1. yaml

   - @ConfigurationProperties(prefix="person")

   ```java
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
   </dependency>
   ```

   - EL表达式

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

   2. properties

   - @PropertySource(value = "classpath:person.properties")

   ​       @Value(${name})

   3. 对比

   @Value这个使用起来并不友好！我们需要为每个属性单独注解赋值，比较麻烦；我们来看个功能对比图

   ![](E:\MarkDown\SpringBoot-Note\img\640.png)

   1、@ConfigurationProperties只需要写一次即可 ， @Value则需要每个字段都添加

   2、松散绑定：这个什么意思呢? 比如我的yml中写的last-name，这个和lastName是一样的， - 后面跟着的字母默认是大写的。这就是松散绑定。可以测试一下

   3、JSR303数据校验 ， 这个就是我们可以在字段是增加一层过滤器验证 ， 可以保证数据的合法性

   4、复杂类型封装，yml中可以封装对象 ， 使用value就不支持

**结论：**

配置yml和配置properties都可以获取到值 ， 强烈推荐 yml；

如果我们在某个业务中，只需要获取配置文件中的某个值，可以使用一下 @value；

如果说，我们专门编写了一个JavaBean来和配置文件进行一一映射，就直接@configurationProperties，不要犹豫！