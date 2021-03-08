springboot学习
=======

## 1.主程序入口类

```java
@SpringBootApplication
public class SpringbootApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringbootApplication.class, args);
	}

}
```
@**SpringBootApplication**： 该注解标注在某个类上，这说明这个类是springboot的主配置类。springboot就应该运行这个类中的主方法来启动springboot应用。该注解的代码如下：
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
```
@**SpringBootConfiguration** ：spirngboot的配置类：
   标注在某个类上，标识是一个spirngboot的配置类；
   @**Configuration**:配置类上标注这个注解
      配置类-----配置文件;配置类也是容器中的组件
@**EnableAutoConfiguration**：开启自动配置功能
   以前需要配置的东西，springboot会帮我们自动配置；该注解会告诉spirngboot开启自动配置功能，这样使spirngboot生效。

```java
    @AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
```
   @**AutoConfigurationPackage**:自动配置包
      @Import({Registrar.class}):
      spring的底层注解@Import,给容器中导入一个组件，导入的组件是Registrar.class提供。<u>该类中的registerBeanDefinitions方法将主配置类所在包以及子包中的所有组件扫描到spring容器。</u>

  @**@Import({AutoConfigurationImportSelector.class})**：导入组件的选择器
      会给容器中导入自动配置类(XXXAutoConfiguration);
配置文件
=========
##1、配置文件
springboot使用全局的配置文件，配置文件名是固定的
* application.propertie
* application.yml
**yaml**:
   以数据为中心，比json、xml更适合做配置文件
```yaml
server:
  port: 8081


```

XML:

## 2、**yaml**语法

### 1.基本语法

k:(空格)v；

以空格缩进来控制层级关系；只要是对齐的一列数据，都是同一层级的；

```yaml
server:
    port: 8081
    path: /hello
```

属性和值是大小写敏感的

### 2.值的写法

**字母量：普通值（数字，字符串，布尔）**

  字符串默认不会加上单引号或者双引号；

  “”：双引号：不会转义字符串里的特殊字符；特殊字符会作为本身想代表的意思

​       例如： name: "zhangsan \n list"------->输出： zhangsan 换行 list

  ‘’：单引号：会转义特殊字符

​      例如：name: 'zhangsan \n list'--------->输出：zhangsan \n list

**对象、Map**

还是k: v的格式:

列格式：

```yaml
friends: 
  lastname: zhangsan
  age: 18
```

行格式：

```yaml
friends: {lastname: zhangsan,age: 18}
```



**数组（list、set）**

用-值来标识数组中的一个元素

列格式：

```yaml
pets:
 - cat
 - dog
```

行格式：

```yaml
pets: [cat,dog,pig]
```

## 3、YAML基本语法

## 4、@PropertySource&@ImportResource

@PropertySource:加载指定的配置文件：

```java
@PropertySource(value = "classpath:person.properties")
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private String lastname;
    private Integer age;
    private Boolean boss;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> list;
    private Dog dog;
```



@**ImportResource**:导入spring的配置文件，让配置文件的内容生效

​    在工程中添加一个beans.xml文件，在service路径下创建一个HelloService.java文件，并配置一个helloService的Bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean name="helloService" class="com.learn.springboot.service.HelloService"></bean>
</beans>
```

在测试类中添加测试方法，查看spirng容器中是否加载了该实例：

```java
	@Autowired
	ApplicationContext context;

	@Test
	public void  testservice(){
		boolean b = context.containsBean("helloService");
		System.out.println(b);
	}
```

结果为false，发现springboot不会自动帮我们配置spring的配置文件。

将@**ImportResource**加入到主类上

```java
@ImportResource(locations = {"classpath:beans.xml"})
@SpringBootApplication
public class SpringbootApplication {
```

此时结果为true，说明spirng容器已成功配置

springboot推荐给容器添加组件的方式：推荐使用全注解的方式

使用@Bean

```java
/**
 * @Author 
 * @Date 2020/7/9
 * Configuration指明当前类是一个配置类，用来代替之前的配置文件
 * 之前使用<bean></bean>来添加组件
 */
@Configuration
public class MyAppConfig {
//    将方法的返回值添加到容器中，容器中的组件默认的id就是方法名
    @Bean
    public HelloService helloService(){
        return new HelloService();
    }
}
```

## 5、资源文件中的占位符使用

1.随机数

```java
${random.value}、${random.uuid}、${random.long}、
${random.int(10)}、${random.value}、
```

2.占位符获取之前配置的值，如果没有可以用:来指定默认值

```properties
person.lastname=张三${random.uuid}
person.age=${random.int}
person.boss=true
person.birth=2011/5/02
person.maps.k1=v1
person.maps.k2=v2
person.list=a,b,c
person.dog.name=${person.lastname：hello}的旺财
person.dog.age=3
```

## 6、profile

## 7、配置文件的加载位置

## 8、日志

![](D:\学习笔记\image\日志.png)

左边选一个门面接口，右边选一个实现。

日志门面：slf4j;

日志实现：logback;



springboot:底层是spring框架，默认使用的是jcl;

​    springboot选用slf4j和logback。

### 2、日志系统使用slf4j

```java
public class Log4jTest {

        private static final Logger logger = LoggerFactory.getLogger(Log4jTest.class);

        public static void main(String[] args) {
            logger.debug("debug");
            logger.warn("warm");
            logger.error("error");
        }
 }
```



​     ![](D:\学习笔记\image\日志slf4j与实现.png)

### 3.springboot日志结构



![](D:\学习笔记\image\springboot日志jar包关系图.png)

1.springboot使用的是slf4j+logback

2.其他类型的日志做了适配转化

3.在引入其他框架时要注意移出其自带的日志关系

### 3.日志使用





## 9、web模块学习

1.springboot对静态资源的映射规则

1)webjars路径

用c+s+a+n查找WebMvcAutoConfiguration,找到addResourceHandlers资源映射规则如下：

```java
        public void addResourceHandlers(ResourceHandlerRegistry registry) {
            if (!this.resourceProperties.isAddMappings()) {
                logger.debug("Default resource handling disabled");
            } else {
                Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
                CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
                if (!registry.hasMappingForPattern("/webjars/**")) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }

                String staticPathPattern = this.mvcProperties.getStaticPathPattern();
                if (!registry.hasMappingForPattern(staticPathPattern)) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }

            }
        }
```

所有的/webjars/**,都会去classpath:/META-INF/resources/webjars/找资源.

webjars:以Jar包的形式引入静态资源

![](D:\学习笔记\image\静态资源加载.png)

2）“/**”

```java
"classpath:/META-INF/resources/", "classpath:/resources/", 
"classpath:/static/", 
"classpath:/public/
"/"   //当前项目根目录
```

3）welcomePageHandlerMapping 欢迎页（首页映射）

```java
        public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext, FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
            WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(new TemplateAvailabilityProviders(applicationContext), applicationContext, this.getWelcomePage(), this.mvcProperties.getStaticPathPattern());
            welcomePageHandlerMapping.setInterceptors(this.getInterceptors(mvcConversionService, mvcResourceUrlProvider));
            welcomePageHandlerMapping.setCorsConfigurations(this.getCorsConfigurations());
            return welcomePageHandlerMapping;
        }
```

2.模板引擎Themleaf



![](D:\学习笔记\image\thymeleaf图.png)



## 10、resultful接口使用

- **RequestMapping**

```java
@RequestMapping(value="welcome",method=RequestMethod.POST)
```



- ant风格的请求

 ？ 表示任意字符

```java
@RequestMapping(value="welcome/a?c",method=RequestMethod.POST)
```

请求：localhost:8080/welcome/abc

 *表示任意字符串

```java
@RequestMapping(value="welcome/*/test",method=RequestMethod.POST)
```

请求： localhost:8080/welcome/abc/test

 **表示任意目录

```java
@RequestMapping(value="welcome/**/test",method=RequestMethod.POST)
```

请求：  localhost:8080/welcome/abc/b/c/test

- 传参：访问请求时带入路径/后面

  ```java
  	@RequestMapping(value = "welcome/{username}",method = RequestMethod.GET)
  	public String Welcome1(@PathVariable("username") String username)
  	{
  		return null;
  	}
  ```

  使用@PathVariable（）注解标注，并同时在路径上表上返回参数。

## 11、自制starters(场景启动器)

- starter制作条件:
  1.需要用那些依赖
  2.如何编写自动配置
  
  ```java
  @Configuration //指定这个类是配置类
  @ConditionalOnxxx //判断条件，当条件满足时自动配置类生效
  @AutoConfigureOrder //指定自动配置类顺序
  
  
  @Bean //给容器中添加组件
  @ConfigurationProperties //结合xxxProperties类来绑定相关配置
  @EnableConfigurationProperties({WebMvcProperties.class, ResourceProperties.class}) //让properties生效，并加入到容器中
  
  
  自动配置类要能加载，
  将需要启动就自动加载的自动配置类，配置在meta-inf/spring.factories
  org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
  
  ```
  
  

![](D:\学习笔记\image\autoconfigure.png)
  3.模式

 ![](D:\学习笔记\image\启动器以及命名规范.png)

启动器只用来导入依赖；

专门写一个自动配置的模块做为依赖；

启动器引入该模块做自动配置；

别人只需要用启动器；

实例参考：D:\springboot学习\08starter +demo

## 12、缓存

![](D:\学习笔记\image\缓存概念.png)

![](D:\学习笔记\image\缓存概念1.png)

![](D:\学习笔记\image\spring缓存概念.png)



cacheable的几个参数：



| cacheNames/value | 指定缓存组件的名字，将方法的返回结果放在哪个缓存中，是数组方式，可以指定多个缓存 |
| ---------------- | ------------------------------------------------------------ |
| key              | 缓存数据使用key（理解为缓存数据的唯一标识），默认是方法参数的值 |
| keyGenerator     | 与key类似，二选一使用                                        |
| condition        | 符合条件下才缓存；condition = "#id>0"                        |
| unless           | 符合条件不缓存；unless = "#result == null"                   |
| sync             | 是否使用异步（异步下不支持unless）                           |

类公共注解：@CacheConfig()，指定方法的公共cache属性

方法注解： @Caching 复杂缓存规则使用该注解编写

1.创建数据库

```mysql

CREATE TABLE
IF NOT EXISTS `department` (
	`id` INT UNSIGNED AUTO_INCREMENT,
	`departmentName` VARCHAR (50) NOT NULL,
	PRIMARY KEY (`id`)
) ENGINE = INNODB DEFAULT CHARSET = utf8;

CREATE TABLE
IF NOT EXISTS `employee` (
	`id` INT UNSIGNED AUTO_INCREMENT,
	`lastName` VARCHAR (50) NOT NULL,
	`did` INT UNSIGNED NOT NULL ,
	PRIMARY KEY (`id`),
	FOREIGN KEY (`did`) REFERENCES `department` (id)
) ENGINE = INNODB DEFAULT CHARSET = utf8;

```

2.缓存配置：

  1）开启基于注解的缓存@EnableCaching

   2)需要配置缓存的方法上配置缓存注解：

- ​      @Cacheable

- ​      @CacheEvict

- ​       @CachePut

3）Maven引入redis-start

4）配置redis缓存

pom添加data-redis依赖

```xml

```



  

3.redis缓存：

springboot 1.0默认创建的 RedisCacheManager

```java
//缓存管理器
    @Bean
    public CacheManager cacheManager(@SuppressWarnings("rawtypes") RedisTemplate redisTemplate) {
        RedisCacheManager cacheManager = new RedisCacheManager(redisTemplate);
        //设置缓存过期时间
        cacheManager.setDefaultExpiration(10000);
        return cacheManager;
    }    //缓存管理器
```

springboot 2.0默认创建的 RedisCacheManager

```java
@Bean
	public RedisCacheManager cacheManager(RedisConnectionFactory redisConnectionFactory,
			ResourceLoader resourceLoader) {
		RedisCacheManagerBuilder builder = RedisCacheManager.builder(redisConnectionFactory)
				.cacheDefaults(determineConfiguration(resourceLoader.getClassLoader()));
		List<String> cacheNames = this.cacheProperties.getCacheNames();
		if (!cacheNames.isEmpty()) {
			builder.initialCacheNames(new LinkedHashSet<>(cacheNames));
		}
		return this.customizerInvoker.customize(builder.build());
    }
```

## 13、消息路由



使用场景：异步处理、应用解耦、秒杀系统（流量削峰）

https://www.cnblogs.com/javastack/archive/2020/04/14/12699213.html



![](D:\学习笔记\image\消息中间件的概述.png)

![](D:\学习笔记\image\消息中间件概述2.png)

![](D:\学习笔记\image\消息中间件概述3.png)



![](D:\学习笔记\image\rabbitMQ运行机制.png)

交换器类型：
![](D:\学习笔记\image\exchange类型.png)



![](D:\学习笔记\image\exchange2.png)



![](D:\学习笔记\image\消息实例.png)

maven中引入

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
```

原理：

   自动配置：

  1.RabbitAutoConfiguration

  2.在RabbitAutoConfiguration配置了连接工厂CachingConnectionFactory

  3.配置信息：RabbitProperties

  4.配置rabbitmq

```properties
spring.rabbitmq.host=192.168.8.196
spring.rabbitmq.username=admin
spring.rabbitmq.password=admin
```

5.在RabbitAutoConfiguration有RabbitTemplate用于rabbitmq操作

6.amqpadmin:系统管理构件

```java
default T getIfUnique(java.util.function.Supplier<T> defaultSupplier)  throws BeansException
//defaultSupplier参数是一个回调，用于在容器中不存在唯一候选者的情况下提供默认对象
```

7.rabbitmq初级使用

```java
    @Autowired
    RabbitTemplate rabbitTemplate;
    @Test
//将信息推送到atguigu.news信道中
    void contextLoads() {
        Map<String,Object> map = new HashMap();
        map.put("name","张三");
        map.put("gener","男");
        map.put("email","8@qq.com");
//        rabbitTemplate.setMessageConverter(new Jackson2JsonMessageConverter());
//        rabbitTemplate.set
//        对象被默认序列化为jdk序列化方式
        rabbitTemplate.convertAndSend("exchange.direct","atguigu.news",map);
    }

//从atguigu.news获取信息
    @Test
    void receive(){
        Object o = rabbitTemplate.receiveAndConvert("atguigu.news");
        System.out.println(o.getClass());
        System.out.println(o);
    }
```

```java
@Configuration
public class MyAmqpConfig {
    @Bean
    MessageConverter myMessageConverter(){
        return     new Jackson2JsonMessageConverter();
    }
}
//将rabbitTemplate序列化方式改为Jackson2JsonMessageConverter
```

8.rabbitlistener 和 enablerabbit

```java
@SpringBootApplication
@EnableRabbit
public class SpringBoot12RamqApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBoot12RamqApplication.class, args);
    }

}
```

```java
@Service
public class BookService {
    @RabbitListener(queues = "atguigu.news")
    public void receive(Book book){
        System.out.println("收到消息0"+book);
    }
}
```





![image-20200723170434767](C:\Users\ljf\AppData\Roaming\Typora\typora-user-images\image-20200723170434767.png)

## 14、thymeleaf使用

1.引入thymeleaf依赖

```xml
        <!--thymeleaf-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```

2.ThymeleafAutoConfiguration中的ThymeleafProperties中设置了thymeleaf的配置

```java
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    public static final String DEFAULT_SUFFIX = ".html";
    private boolean checkTemplate = true;
    private boolean checkTemplateLocation = true;
    private String prefix = "classpath:/templates/";
    private String suffix = ".html";
    private String mode = "HTML";
```

意味着thymeleaf会拼串到classpath:/templates/xxx.html

3.导入名称空间

```html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

4.开发过程中注意事项

1）禁用thymeleaf缓存

```xml
# 关闭thmleaf缓存
spring.thymeleaf.cache=false
```

2)修改完html后 使用ctrl+F9进行刷新



## 15、spring mvc使用

1.springboot在官网中的介绍： 

2.扩展spring mvc

3.ctrl+o打开可以实现的方法

4.WebMvcConfigurationSupport中的addViewController

5.全面接管spring mvc： 在配置类中添加@EnableWebMvc，所有的mvc模块的自动配置会全部失效。不推荐使用全面接管

6.国际化

 1）编写国际化配置文件：

![](D:\学习笔记\image\国际化文件.png)

创建



  2）原理：使用ResourceBundleMessageSource管理国际化文件 messageSourceAutoConfiguration

  3）yml文件中添加：spring.messages.basename=i18n.login  匹配文件

  4）页面获取国际化的值：thymeleaf中用#{}取国际化的值

7.web mvc扩展:

建立配置类实现springmvc扩展内容

```java
@Configuration
public class MyMvcConfig  implements WebMvcConfigurer {
    @Override
    //设置视图映射
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/atguigu").setViewName("success");
        registry.addViewController("/").setViewName("login");
        registry.addViewController("/index.html").setViewName("login");
        registry.addViewController("/main.html").setViewName("dashboard");
    }

    @Override
    //设置拦截器
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**").excludePathPatterns("/index.html"
                ,"/","/user/login","/asserts/**", "/webjars/**");
    }

    @Override
    //设置资源映射
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
//        不过真的是不建议我们修改静态资源文件的访问目录为classpath:/，因为如果我们改了静态资源的访问目录为classpath:/，
// 这会带来一个隐患，就是classpath下的所有文件都是可以被访问到的。其实我们直接使用springboot的默认设置的静态资源目录
// （classpath:/statc/）j就可以了，不管是打war包还是jar包都是可以被访问的，同时也会杀死别的目录下的文件被访问的机会。
        registry.addResourceHandler("/**").addResourceLocations("classpath:/static/",
                "classpath:/META-INF/resources/",
                "classpath:/static/",
                "classpath:/public/");
    }


}

```





  

## 16、Servlet容器

### 1.配置嵌入式Servlet容器

![](D:\学习笔记\image\默认嵌入的servlet容器.png)

SpringBoot默认使用Tomcat作为嵌入式的servlet容器

## 2.3大组件servlet/filter/listener

https://www.cnblogs.com/meizhoulqp/p/11172937.html



## 17、 springboot启动原理

```java
//META-INF/spring.factories
ApplicationContextInitializer

SpringApplicationRunListener
//Ioc
ApplicationRunner

CommandLineRunner
```



```java
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
```

启动后执行两步

1.创建springapplication对象

```java
    public SpringApplication(Class... primarySources) {
        this((ResourceLoader)null, primarySources);
    }
   //上面的this调用下面的构造方法，来初始化对象

    public SpringApplication(ResourceLoader resourceLoader, Class... primarySources) {
        this.sources = new LinkedHashSet();
        this.bannerMode = Mode.CONSOLE;
        this.logStartupInfo = true;
        this.addCommandLineProperties = true;
        this.addConversionService = true;
        this.headless = true;
        this.registerShutdownHook = true;
        this.additionalProfiles = new HashSet();
        this.isCustomEnvironment = false;
        this.lazyInitialization = false;
        this.resourceLoader = resourceLoader;
        Assert.notNull(primarySources, "PrimarySources must not be null");
        this.primarySources = new LinkedHashSet(Arrays.asList(primarySources));
        //deduceFromClasspath判断是否是WEB环境
        this.webApplicationType = WebApplicationType.deduceFromClasspath();
        //从META-INF/spring.factories下找到所有的ApplicationContextInitializer.class，保存起来
        this.setInitializers(this.getSpringFactoriesInstances(ApplicationContextInitializer.class));
        //类似上面，从从META-INF/spring.factories下找到所有的ApplicationListener保存起来
        this.setListeners(this.getSpringFactoriesInstances(ApplicationListener.class));
        //从多个主配置类中找到main方法的主配置类
        this.mainApplicationClass = this.deduceMainApplicationClass();
    }
```

```java
//核心步骤
this.setInitializers(this.getSpringFactoriesInstances(ApplicationContextInitializer.class));
//展开getSpringFactoriesInstances
    private <T> Collection<T> getSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes, Object... args) {
        ClassLoader classLoader = this.getClassLoader();
        Set<String> names = new LinkedHashSet(SpringFactoriesLoader.loadFactoryNames(type, classLoader));
        List<T> instances = this.createSpringFactoriesInstances(type, parameterTypes, classLoader, args, names);
        AnnotationAwareOrderComparator.sort(instances);
        return instances;
    }
//展开loadFactoryNames
    public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
        String factoryTypeName = factoryType.getName();
        return (List)loadSpringFactories(classLoader).getOrDefault(factoryTypeName, Collections.emptyList());
    }
//展开loadSpringFactories
    private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
        MultiValueMap<String, String> result = (MultiValueMap)cache.get(classLoader);
        if (result != null) {
            return result;
        } else {
            try {
                Enumeration<URL> urls = classLoader != null ? classLoader.getResources("META-INF/spring.factories") : ClassLoader.getSystemResources("META-INF/spring.factories");
                LinkedMultiValueMap result = new LinkedMultiValueMap();

                while(urls.hasMoreElements()) {
                    URL url = (URL)urls.nextElement();
                    UrlResource resource = new UrlResource(url);
                    Properties properties = PropertiesLoaderUtils.loadProperties(resource);
                    Iterator var6 = properties.entrySet().iterator();

                    while(var6.hasNext()) {
                        Entry<?, ?> entry = (Entry)var6.next();
                        String factoryTypeName = ((String)entry.getKey()).trim();
                        String[] var9 = StringUtils.commaDelimitedListToStringArray((String)entry.getValue());
                        int var10 = var9.length;

                        for(int var11 = 0; var11 < var10; ++var11) {
                            String factoryImplementationName = var9[var11];
                            result.add(factoryTypeName, factoryImplementationName.trim());
                        }
                    }
                }

                cache.put(classLoader, result);
                return result;
            } catch (IOException var13) {
                throw new IllegalArgumentException("Unable to load factories from location [META-INF/spring.factories]", var13);
            }
        }
    }
//从META-INF/spring.factories下找到所有的ApplicationContextInitializer.class，保存起来
```

![](D:\学习笔记\image\initializers.png)

![](D:\学习笔记\image\Listeners.png)



2.执行run方法

```java
//run方法    
public ConfigurableApplicationContext run(String... args) {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        ConfigurableApplicationContext context = null;
        Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList();
        this.configureHeadlessProperty();
    //获取SpringApplicationRunListeners，从类路径下从META-INF/spring.factories下找到所有的ApplicationListener
        SpringApplicationRunListeners listeners = this.getRunListeners(args);
        listeners.starting();

        Collection exceptionReporters;
        try {
            //封装命令行参数args
            ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
            //回调SpringApplicationRunListeners的environmentPrepared方法，表示环境准备完成
            ConfigurableEnvironment environment = this.prepareEnvironment(listeners, applicationArguments);
            this.configureIgnoreBeanInfo(environment);
            //打印控制台spring的图标
            Banner printedBanner = this.printBanner(environment);
            //创建ioc容器
            context = this.createApplicationContext();
            exceptionReporters = this.getSpringFactoriesInstances(SpringBootExceptionReporter.class, new Class[]{ConfigurableApplicationContext.class}, context);
            //准备上下文环境；将environment保存到ioc中；applyInitializers
            //applyInitializers（）
            this.prepareContext(context, environment, listeners, applicationArguments, printedBanner);
            //刷新容器，ioc容器初始化
            this.refreshContext(context);
            //从容器中获取。。。。。
            this.afterRefresh(context, applicationArguments);
            stopWatch.stop();
            if (this.logStartupInfo) {
                (new StartupInfoLogger(this.mainApplicationClass)).logStarted(this.getApplicationLog(), stopWatch);
            }

            listeners.started(context);
            this.callRunners(context, applicationArguments);
        } catch (Throwable var10) {
            this.handleRunFailure(context, var10, exceptionReporters, listeners);
            throw new IllegalStateException(var10);
        }

        try {
            listeners.running(context);
            return context;
        } catch (Throwable var9) {
            this.handleRunFailure(context, var9, exceptionReporters, (SpringApplicationRunListeners)null);
            throw new IllegalStateException(var9);
        }
    }
```

### 1.java函数回调和引用

https://blog.csdn.net/iteen/article/details/82183596

### 2.this关键字的几种用法

### 3.java8中的default关键字

https://blog.csdn.net/qq_35835624/article/details/80196932

## 18、检索

![](D:\学习笔记\image\Elasticsearch检索.png)

注意：elasticsearch预占2G内存

```dockerfile
sudo docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9200:9200 -p 9300:9300 --name myES01 5a
//创建搜索引擎容器
```

访问9200端口

![](D:\学习笔记\image\搜索引擎安装成功.png)

![](D:\学习笔记\image\ES集群图.png)

es检索学习官网：https://www.elastic.co/guide/cn/elasticsearch/guide/current/_search_lite.html

1.springboot2.0 + jest的elasticsearch搭建以及基本使用

```xml
        <dependency>
            <groupId>io.searchbox</groupId>
            <artifactId>jest</artifactId>
            <version>6.3.0</version>
        </dependency>
<-- 引入jset依赖 -->
```

2.配置JestClient

```java
//配置JestClient（2.3需要手动配置，1.0则不需要该步骤）
@Configuration
public class MyConfig {

    @Bean
    public JestClient jestCline() {
        JestClientFactory factory = new JestClientFactory();
        factory.setHttpClientConfig(new HttpClientConfig
                .Builder("http://localhost:9200")
                .multiThreaded(true)
                .build());
        return factory.getObject();
    }
}
```

3.测试以及入门使用

```java
    @Autowired
    JestClient jestClient;
    @Test
    void contextLoads() {
        Article article = new Article();
        article.setId(1);
        article.setAuthor("罗贯中");
        article.setTitle("三国演义");
        article.setContent("砍曹操");
        Index index = new Index.Builder(article).index("jinfeng").type("book").build();
        try {
            jestClient.execute(index);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    @Test
    void  search(){
        String json = "{\n" +
                "    \"query\" : {\n" +
                "        \"match\" : {\n" +
                "            \"content\" : \"砍\"\n" +
                "        }\n" +
                "    }\n" +
                "\n" +
                "}";
        Search search = new Search.Builder(json).addIndex("jinfeng").addType("news").build();
        try {
            SearchResult result = jestClient.execute(search);
            System.out.println(result.getJsonString());
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
```



https://blog.csdn.net/weixin_45566322/article/details/107644082

新版本学习Elasticsearch+springboot2.3:

注意问题：1.版本适配问题：elasticsearch和spring data elsticsearch 版本适配（elasticsearch4.0以上不在有type）

引入依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
        </dependency>
```

添加配置AbstractElasticsearchConfiguration

```java
@Configuration
public class RestClientConfig extends AbstractElasticsearchConfiguration {
    @Override
    public RestHighLevelClient elasticsearchClient() {
        ClientConfiguration configuration = ClientConfiguration.builder(
        )
                .connectedTo("localhost:9201")//9300会报错
                //.withConnectTimeout(Duration.ofSeconds(5))
                //.withSocketTimeout(Duration.ofSeconds(3))
                //.useSsl()
                //.withDefaultHeaders(defaultHeaders)
                //.withBasicAuth(username, password)
                // ... other options
                .build();
        RestHighLevelClient client = RestClients.create(configuration).rest();
        return client;
    }
}
```

```java
@Document(indexName = "jinfeng")
public class Book {
    private Integer id;
    private String author;
    private String bookName;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public String getBookName() {
        return bookName;
    }

    public void setBookName(String bookName) {
        this.bookName = bookName;
    }

    @Override
    public String toString() {
        return "Book{" +
                "id=" + id +
                ", author='" + author + '\'' +
                ", bookName='" + bookName + '\'' +
                '}';
    }

```

配置ElasticsearchRepository

```java
public interface BookRepository extends ElasticsearchRepository<Book,Integer> {
    public List<Book> findBookById(Integer id);
}
```

```java
//添加自动配置注解
```





测试

```java
@Autowired
    BookRepository bookRepository;    

@Test
    void elasticsearch(){
        Book book =new Book();
        book.setId(1);
        book.setAuthor("吴承恩");
        book.setBookName("西游记");
        bookRepository.save(book);
    }
    @Test
    void elasticsearchFind(){
        List<Book> bookList = bookRepository.findBookById(1);
        System.out.println(bookList);
    }
```

测试elasticsearchOperations

```java
    @Autowired
    ElasticsearchOperations elasticsearchOperations;
    @Test
    void testSaveByOperations(){
        Book book=new Book();
        book.setId(2);
        book.setBookName("西游记2");
        book.setAuthor("吴承恩二世");
        IndexQuery indexQuery= new IndexQueryBuilder()
                .withId(book.getId().toString())
                .withObject(book)
                .build();
        String documentId = elasticsearchOperations.index(indexQuery, IndexCoordinates.of("jinfeng"));//返回_id(并非javabean中的ID，而是hits中的)
        System.out.println(documentId);
    }

    @Test
    void testFindByOperations(){
        Book book = elasticsearchOperations.get("2",Book.class,IndexCoordinates.of("jinfeng"));//IndexCoordinates的参数为Index
        System.out.println(book);
    }
```

## 19、task

### 1）异步任务

在方法上添加Async注解，在主程序上添加EnableAsync注解

```java
@Service
public class AsyncService {

//    异步方法
    @Async
    public void hello(){
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("hello+数据处理中。。。。。");

    }
}
```

```java
@RestController
public class HelloController {

    @Autowired
    AsyncService asyncService;

    @GetMapping("/hello")
    public String hello(){
        asyncService.hello();
        return "success";
    }

}
```

```java
@EnableAsync
@SpringBootApplication
public class SpringBootTaskApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootTaskApplication.class, args);
    }

}
```

### 2）任务调度task

```java
@Service
public class ScheduleService {
    /**
     *  秒，分，时，日，月，周
     *  * * * * * *
     */
    @Scheduled(cron = "0 * * * * *")
    public void  hello(){
        System.out.println("hello0");
    }
}
```



```java
@EnableScheduling
@EnableAsync
@SpringBootApplication
public class SpringBootTaskApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootTaskApplication.class, args);
    }

}
```

![](D:\学习笔记\image\定时任务设置.png)

## 20、安全

1.引入依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.thymeleaf.extras</groupId>
            <artifactId>thymeleaf-extras-springsecurity5</artifactId>
            <version>3.0.4.RELEASE</version>
        </dependency>
<- 注意版本适配问题->
```

2.引入config

```java
@EnableWebSecurity
public class MySecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
//    制定授权规则
    protected void configure(HttpSecurity http) throws Exception {
//        super.configure(http);
//        配置Http
        http.authorizeRequests().antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasAnyRole("vip1")
                .antMatchers("/level2/**").hasAnyRole("vip2")
                .antMatchers("/level3/**").hasAnyRole("vip3");
//        开启自动配置的登录功能，若未登录则进入默认登录页面
        http.formLogin().usernameParameter("user").passwordParameter("pwd").loginPage("/userlogin");
        //1. /login进入登录页面
        //2. /重定向到login/error页面
        //3.更多详细规则
        //4.默认post /login 代表处理登录
        //5. 一旦自定义loginPage，那么该配置的post请求即是登录的处理逻辑
        //6. 自定义处理登录请求追加.loginProcessingUrl()来做登录请求自定义处理

//        开启自动配置的注销功能
        http.logout().logoutSuccessUrl("/");//注销成功后进入首页
        //用户注销，清空session
        //返回/login?logout页面

        http.rememberMe().rememberMeParameter("remeber");
        //登录成功后，将cookie发给浏览器保存，下次登录时后会实现免登录功能
        //点击注销会清空cookie

    }

//    定义认证规则
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
//        super.configure(auth);
//        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder()).withUser("admin").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1");
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder()).withUser("jack").password(new BCryptPasswordEncoder().encode("fang")).roles("vip2");
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder()).withUser("zhangsan").password(new BCryptPasswordEncoder().encode("123456")).roles("vip3");

    }
}
```

3.结合thymeleaf进行权限控制

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
	  xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity5">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
<h1 align="center">欢迎光临武林秘籍管理系统</h1>
<div sec:authorize="!isAuthenticated()">
	<h2 align="center">游客您好，如果想查看武林秘籍 <a th:href="@{/userlogin}">请登录</a></h2>
</div>
<div sec:authorize="isAuthenticated()">
	<h2><span sec:authentication="name"></span>，您好,您的角色有：
		<span sec:authentication="principal.authorities"></span></h2>
	<form th:action="@{/logout}" method="post">
		<input type="submit" value="注销"/>
	</form>
</div>

<hr>

<div sec:authorize="hasRole('vip1')">
	<h3>普通武功秘籍</h3>
	<ul>
		<li><a th:href="@{/level1/1}">罗汉拳</a></li>
		<li><a th:href="@{/level1/2}">武当长拳</a></li>
		<li><a th:href="@{/level1/3}">全真剑法</a></li>
	</ul>

</div>

<div sec:authorize="hasRole('vip2')">
	<h3>高级武功秘籍</h3>
	<ul>
		<li><a th:href="@{/level2/1}">太极拳</a></li>
		<li><a th:href="@{/level2/2}">七伤拳</a></li>
		<li><a th:href="@{/level2/3}">梯云纵</a></li>
	</ul>

</div>

<div sec:authorize="hasRole('vip3')">
	<h3>绝世武功秘籍</h3>
	<ul>
		<li><a th:href="@{/level3/1}">葵花宝典</a></li>
		<li><a th:href="@{/level3/2}">龟派气功</a></li>
		<li><a th:href="@{/level3/3}">独孤九剑</a></li>
	</ul>
</div>


</body>
</html>
```

