###### Springboot

### 一.基础

1. 在pom.xml中添加相关依赖：

```
 <parent>  

​	<groupId>org.springframework.boot</groupId>   

​	<artifactId>spring-boot-starter-parent</artifactId>  

​	<version>1.5.9.RELEASE</version>

 </parent> 
```



```
<dependencies>

​	<dependency>

​		<groupId>org.springframework.boot</groupId>

​		<artifactId>spring-boot-starter-web</artifactId>

​	</dependency>

</dependencies>
```



2. 在主函数添加注解：`@SpringBootApplication` 

告诉函数此函数为SpringBoot函数

### 二.Spring Boot四大核心

1.自动配置

2.起步依赖

3.Actuator

4.命令行界面

### 三. Spring Boot项目结构

1.`@SpringBootApplication`     此注解标注放到有主函数main的类上，没有这个注解，只是一个普通函数

2./src/main/resources 下的static、templates、application.properties三个文件夹

static: 用于放置一些静态资源，如图片，js等

templates: 用于放置前端的模板引擎。templates和static类似于web项目的webapp目录

application.properties: 核心配置文件

3.Spring Boot项目的代码，都必须写在和main函数所在类的同级或下一级中

4.application.properties 修改配置方法：

`server.port=8080`    //设置端口

`server.servlet.context-path=/xxxx`    //设置上下文，可以省略，但要有 /

5.多环境application.properties的配置方法：

​	新建一个文件application-xxx.properties，xxx为环境的名称，可以任意取，放到application.properties同一目录下。在application.properties添加

`spring.profiles.active=/xxx`  表示激活这个配置文件

### 四.SpringBoot 对jsp的支持依赖

1.SpringBoot必须添加相关依赖才可以解析jsp。在src目录下新建webapp文件夹，一般把jsp文件放到这个webapp文件夹中

2.pom.xml中解析jsp的依赖为：

```java
<dependency> 
	<groupId>org.apache.tomcat.embed</groupId>
	 <artifactId>tomcat-embed-jasper</artifactId> 
</dependency>
```

3.SpringBoot 要求 jsp 文件必须编译到指定的 META-INF/resources 目录下才能访问，否则访问 不到， 写在<build></build>中

```Java
<resources> 
	<resource>
		 <directory>src/main/webapp</directory> 
					<!--指定编译到 META-INF/resources，该目录不能随便写--> 		<targetPath>META-INF/resources</targetPath> 
<!--指定要把哪些文件编译进去， **表示 webapp 目录及子目录， *.*表示所有文件--> 
		<includes> 
			<include>**/*.*</include>
	 	</includes>
	 </resource> 
</resources>
```

4.application.properties 文件配置 Spring MVC 的视图展示为jsp  

```
spring.mvc.view.prefix=/ 
spring.mvc.view.suffix=.jsp
```

### 五.SpringBoot 与 Mybatis

1.新建SpringBoot项目，pom.xml中添加相关依赖

```java
<!-- Mysql驱动 -->
	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
	</dependency>
```
		<!-- MyBatis整合SpringBoot框架的起步依赖 -->
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.2.0</version>
		</dependency>
2.使用MyBatis提供的逆向工程生成实体bean，映射文件，DAO接口，配置为：

pom.xml中：

			<plugin>
				 <groupId>org.mybatis.generator</groupId>
				 <artifactId>mybatis-generator-maven-plugin</artifactId>
				 <version>1.3.6</version>
				 <configuration>
				 <!--配置文件的位置-->
				 	<configurationFile>GeneratorMapper.xml</configurationFile>
				 	<verbose>true</verbose>
				 	<overwrite>true</overwrite>
				 </configuration>
			</plugin>
复制GeneratorMapper.xml，并进行相应配置中进行

3.application.properties文件中需要进行数据库的配置：

```java
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/springboot?useUnicode=true&characterEncoding=UTF-8&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=GMT%2B8
spring.datasource.username=root
spring.datasource.password=123456
```

4.SpringBoot的事务：

在实现类上，添加注解  @Transactional

5.<build>中进行配置：

```java
	<resources>
		<resource>
			<directory>src/main/java</directory>
			<includes>
				<include>**/*.xml</include>
			</includes>
		</resource>
	</resources>
```
### 六.SpringBoot下的Spring MVC

1.@RestController :   @Controller 与@ResponseBody 的组合注解，如果一个 Controller 类添加了@RestController，那么该 Controller 类下的所有方法都相当于添加了@ResponseBody 注解。这个类下面的所有方法返回的都是 json 数据。

### 七.SpringBoot集成Redis

1.SpringBoot集成Redis的起步依赖

​	

```java
	<dependency>

 		<groupId>org.springframework.boot</groupId>

 		<artifactId>spring-boot-starter-data-redis</artifactId>

​		</dependency>
```

2.application.properties中进行相关配置

```
spring.redis.host=localhost
spring.redis.port=6379
spring.redis.password=123456
```

### 八.SpringBoot过滤器

1.在过滤器类上添加注解

此函数需要implements Filter，然后添加注解@WebFilter(urlPatterns="/myFilter")

2.在main函数上添加注解
@ServletComponentScan(basePackages="xxxx")

### 九.SpringBoot字符编码设置

方法一：

1.在public class MyServlet extends HttpServlet{}类中添加
resp.setContentType("text/html;character=utf-8");

2.新建类

​	

	@Configuration
	public class SystemConfig {
		@Bean
		public FilterRegistrationBean chraEncoding() {
		
		//创建字符编码过滤器
		CharacterEncodingFilter cef = new 			  CharacterEncodingFilter();
		//设置强制使用指定字符编码
		cef.setForceEncoding(true);
		//设置指定字符编码
		cef.setEncoding("utf-8");
		
		FilterRegistrationBean frb = new FilterRegistrationBean();
		
		//设置字符编码过滤器
		frb.setFilter(cef);
		//设置字符编码过滤器路径
		frb.addUrlPatterns("/*");
			return frb;
		
	}

3.application.properties中添加
spring.http.encoding.enabled=false

方法二：

1.在public class MyServlet extends HttpServlet{}类中添加
resp.setContentType("text/html;character=utf-8");

2.application.properties中添加

```
spring.http.encoding.enabled=true
spring.http.encoding.force=true
spring.http.encoding.charset=utf-8
```

### 十.SpringBoot打包为war

1.pom.xml添加内容：

<packaging>war</packaging>

确保有打包插件，一般SpringBoot项目创建时就有

```java
<plugin>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```
<bulid>标签中添加<finalName>这里填项目名称</finalName>

### 十一.SpringBoot日志log管理

1.resource文件夹下新建.xml文件名字需要有-spring ，如logback-spring.xml ，并写入相关内容。

2.pom.xml中添加依赖

```
<dependency>
     <groupId>org.projectlombok</groupId>
     <artifactId>lombok</artifactId>
</dependency>
```

3.在需要打印日志的类上添加注解@Slf4j，比如Contorll类上，在类中的要输出日志的代码上写：log.info("xxx日志为")或log.debug() 等。

### 十二.Thymeleaf

1.起步依赖

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
2.html中设置<html xmlns:th="http://www.thymeleaf.org">

### 十三.Vue

1.vue的安装：

1）CDN引入：

<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

2）下载和引入

<html xmlns:th="http://www.thymeleaf.org">

<script th:src="@{/script/vue.js}" type="text/javascript"></script>

3) NPM安装

2.

<div id="app">
    {{message}}
</div>
<script>
    var app = new Vue({
        el:"#app",   //el:用于指定标签并挂载  #后面跟id属性
        data:{
            message:"Hello, vue"
        }
    });
</script>


3.标签常用的属性：

| 指令                          | 含义                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| v-html="rawHtml"              | 说明{{rawHtml}}的内容是html代码，而不是普通文本。此时不需要再写{{}} |
| v-for="item in movie"         | item为自定义的变量，movie是已经定义的数组等，用以执行循环  <li v-for="item in movie">{{item}}</li> |
| v-for="(item,index) in movie" | 获取要变量对象的值和索引值，索引值从0开始。当对象是k-v的集合时，(item,index)分别获取value和key |
| v-one                         | 限制只执行一次，后面没有表达式，只是一个指令                 |
| v-pre                         | 不解析{{}}，按文本进行输出                                   |
| v-bind:src="url"              | v-bind 指定src动态绑定，url的值在data{} 中进行定义。简写：：，一个冒号即可代表v-bind |
| v-model:xxx                   | 双向绑定，比如绑定一个data中的数据，当一个地方的这个数据更改，其他引用这个数据的地方也会改变 |
| v-text='msg'                  | 类似于{{msg}}，会覆盖原本的内容                              |
|                               |                                                              |

4.Vue Mustache注意事项：

{{}} 智能放到标签之间作为内容，不能给属性进行赋值，两端不能加"   "

5.v-bind 绑定style:

使用方法：:style="{key1:value1,key2:value2}"  
value1和value2如果不加引号则表示变量，需要在data中进行定义

```
<h3 id="app" :style="{fontSize:font,color:col}">{{message}}</h3>
<script>
  var app = new Vue({
​    el:"#app",
​    data:{
​      message:'你好啊',
​      font:'50px',
​      col:"red"
​    }
  });
```

v-bind会把等号右边的内容当成一个变量。

6.在vue中，建议定义变量的var改为let 或者const. let 在定义变量时使用，const在定义常量时使用。const定义的常量，不可以修改指向的对象，但可以修改对象的属性值。

7.v-on: 事件监听 缩写: @

如： @click="aa"  表示监听click事件，会执行一个aa函数，在methods中定义。
$event: 获得浏览器参数的event对象

@click.stop ='"func" 表示点击后只执行这个标签的函数，不会执行父级的事件函数。

@keyup="keyUp"  监听键盘敲击事件，@keyup.enter="keyUp"  只监听回车键敲击事件

@click.prevent  阻止默认行为。比如，一个链接设置了click.prevent，则点击后只会执行这个事件，不会跳转链接

8.Vue组件化开发步骤：
1）创建组件构造器对象；2）注册组件；3）使用组件

<div id="app">
  <mycpn></mycpn>  <!--3.使用组件-->
</div>
<script>
  //1.创建组件构造器对象
    const cpn = Vue.extend({
    template:`
    <div>
      <h3>标题来一个</h3>
      <p>内容来一个</p>
    </div>`，
       components:{   //局部注册
        //这里也可以进行组件注册，可以注册其他已经定义的组件，这样可以使用其他组件。
      } 
  })
  //2.注册组件
  Vue.component('mycpn',cpn);  //此方法为全局注册
  var app = new Vue({
      el:"#app",
      data:{          
      },
      methods:{
      },
      components:{   //局部注册
        mycpn:cpn
      }
  });

 //使用语法糖，创建和定义合并

```html
 Vue.component('cpn',{
  template:`
    <div>
   <h3>标题来一个</h3>
      <p>内容来一个</p>
  </div>`
 });
```

注意：组件不可以访问Vue实例中的数据。组件在注册时可以设置data, 但这个data必须是一个函数。 

```
Vue.component('cpn1',{
  template:'#cpn1',
  data(){
  } 
 });
```

9.组件间通信：

1）父传子：props. 在子类组件注册中，添加props属性，如props:[]或者props:{}, 中间填入父组件的一些变量

2）子传父：自定义事件

父组件访问子组件：$children或$refs. 使用$refs之前必须给组件添加ref属性ref="xxx"
子组件访问父组件：$parent

10.自定义全局指令：使用Vue.directive("这里是指令的名称，不需要加v-，但是使用时必须加v-前缀",{})

```
 Vue.directive("color",{
  bind:function(el){
  },
  inserted:function(el){
  },
  updated:function(el){
  }
 })
```

11.Vue生命周期

beforeCreate() 该函数执行在data，methods之前；data，methods初始化之后，执行created() 函数。之后，Vue会渲染el 和template，但只是在内存中编译，并未到页面上，此时会执行beforeMount() 函数，页面中的元素还没有被替换。

接着页面被渲染，页面完成渲染后，会执行mounted() 函数，表示实例已经被完全创建好了。

当数据更新后，执行beforeUpdate(), 此时只是数据被更新，而页面没有更新。当页面也更新完后，执行updated() 函数

12.axios网络连接

get：

axios.get(地址).then(function(response){},function(err){})

当有参数时：

axios.get(地址?key1=value1&key2=value2).then(function(response){},function(err){})

post：

axios.post(地址,{key:value,key2:value2}).then(function(response){},function(err){})