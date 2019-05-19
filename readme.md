慕课网SpringBoot构建电商基础秒杀项目，课程地址https://www.imooc.com/learn/1079
##	第一章 课程介绍

**电商秒杀应用简介**

> * 商品列表页获取秒杀商品列表
> * 进入商品详情页获取秒杀商品详情
> * 秒杀开始后进入下单确认页下单并支付成功

##	第二章 应用SpringBoot完成基础项目搭建

###	2.1 使用IDEA创建maven项目

1.new->project->maven项目->选择maven-archetype-quickstart

以jar包方式对外输出

​	稍等一会，可能会有点慢

2.新建一个resources目录，作为资源文件目录，指定为Resource root

###	2.2 引入SpringBoot依赖包实现简单的Web项目

进入官方文档https://spring.io/guides/gs/rest-service/

**Building a RESTful Web Service**

1.引入父pom

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.1.4.RELEASE</version>
</parent>
```

2.引入依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

3.maven Reimport刷新一下，会自动下载相应jar包（注：可以把idea设定为自动导入maven依赖）

4.SpringBoot的Web项目

```java
@EnableAutoConfiguration
@RestController
public class App 
{

    @RequestMapping("/")
    public String home() {
        return "hello World!";
    }
    public static void main( String[] args )
    {
        System.out.println("Hello World!");
        SpringApplication.run(App.class,args);
    }
}
```

再次启动App，访问localhost:8080

###	2.3 Mybatis接入SpringBoot项目

1.SpringBoot的默认配置

在resources目录下新建SpringBoot的默认配置文件application.properties

通过一行简单的属性就能更改tomcat的端口

```xml
server.port=8090
```

2.配置pom文件

```xml
<!--数据库-->
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
</dependency>
<!--数据库连接池-->
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>druid</artifactId>
  <version>1.1.3</version>
</dependency>
<!--Mybatis依赖-->
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>1.3.1</version>
</dependency>
```

3.配置文件application.properties，设置

`mybatis.mapper-locations=classpath:mapping/*.xml`

然后在resources目录下新建mapping目录

4.自动生成工具，生成数据库文件的映射

引入插件

```xml
<!--自动生成工具，生成数据库文件的映射-->
<plugin>
  <groupId>org.mybatis.generator</groupId>
  <artifactId>mybatis-generator-maven-plugin</artifactId>
  <version>1.3.5</version>
  <dependencies>
    <dependency>
      <groupId>org.mybatis.generator</groupId>
      <artifactId>mybatis-generator-core</artifactId>
      <version>1.3.5</version>
    </dependency>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.41</version>
    </dependency>
  </dependencies>
  <executions>
    <execution>
      <id>mybatis generator</id>
      <phase>package</phase>
      <goals>
        <goal>generate</goal>
      </goals>
    </execution>
  </executions>
  <configuration>
    <!--允许移动生成的文件-->
    <verbose>true</verbose>
    <!--允许自动覆盖文件（生产环境中千万不要这样做）-->
    <overwrite>true</overwrite>
    <configurationFile>
      src/main/resources/mybatis-generator.xml
    </configurationFile>
  </configuration>
</plugin>
```

###	2.4 Mybatis自动生成器的使用方式

1.新建文件src/main/resources/mybatis-generator.xml，从官网下载xml配置文件

http://www.mybatis.org/generator/configreference/xmlconfig.html

2.新建数据库

新建一个miaosha的数据库，并建立两张表，分别是user_info和user_password

3.修改配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>

    <context id="DB2Tables" targetRuntime="MyBatis3">
        <!--数据库链接地址账号密码-->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/miaosha"
                        userId="root"
                        password="123456">
        </jdbcConnection>

        <!--生成DataObject类存放位置-->
        <javaModelGenerator targetPackage="com.miaoshaproject.dataobject" targetProject="src/main/java">
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
        </javaModelGenerator>

        <!--生成映射文件存放位置-->
        <sqlMapGenerator targetPackage="mapping"  targetProject="src/main/resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>

        <!--生成Dao类存放位置-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.miaoshaproject.dao"  targetProject="src/main/java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>

        <!--生成对应表及类名-->
        <!--  enableCountByExample="false"
               enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false"
               selectByExampleQueryId="false"
               这些属性是为了使得只生成简单查询的对应文件，去掉复杂查询的生成文件，因为一般开发中不太用的到
        -->
        <table tableName="user_info" domainObjectName="UserDO"
               enableCountByExample="false"
               enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false"
               selectByExampleQueryId="false"></table>
        <table tableName="user_password" domainObjectName="userPasswordDO"
               enableCountByExample="false"
               enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false"
               selectByExampleQueryId="false" ></table>

    </context>
</generatorConfiguration>
```

4.生成文件

在终端运行``` mvn mybatis-generator:generate```命令

5.接入mysql数据源

```xml
spring.datasource.name=miaosha
spring.datasource.url=jdbc:mysql://localhost:3306/miaosha
spring.datasource.username=root
spring.datasource.password=123456

#使用druid数据源
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

6.测试数据库

修改App类

```java
@SpringBootApplication(scanBasePackages = {"com.miaoshaproject"})
@RestController
@MapperScan("com.miaoshaproject.dao")
public class App {

    @Autowired
    private UserDOMapper userDOMapper;

    @RequestMapping("/")
    public String home() {
        UserDO userDO = userDOMapper.selectByPrimaryKey(1);
        if (userDO == null) {
            return "用户对象不存在";
        } else {
            return userDO.getName();
        }
    }
}
```

启动测试

##	第三章 用户模块开发

###	3.1 使用SpringMVC方式开发用户信息

1.增加controller层、dao层

创建UserController

```java
@Controller("user")
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;

    @RequestMapping("/get")
    @ResponseBody
    public UserModel getUser(@RequestParam(name = "id") Integer id) {
        //调用service服务获取对应id的用户对象并返回给前端
        UserModel userModel = userService.getUserById(id);
        return userModel;
    }
}
```

userController需要UserModel

2.在service层增加UserModel

```java
package com.miaoshaproject.service.model;

/**
 * @author KiroScarlet
 * @date 2019-05-15  -16:50
 */
public class UserModel {
    private Integer id;
    private String name;
    private Byte gender;
    private Integer age;
    private String telphone;
    private String regisitMode;
    private Integer thirdPartyId;
    private String encrptPassword;
}
```

UserModel需要增加 用户的密码，其通过userPasswordDOMapper从userPasswordDO得到

3.修改userPasswordDOMapper.xml和.java文件

增加方法

```xml
<select id="selectByUserId" parameterType="java.lang.Integer" resultMap="BaseResultMap">
  select
  <include refid="Base_Column_List" />
  from user_password
  where user_id = #{userId,jdbcType=INTEGER}
</select>
```

```java
userPasswordDO selectByUserId(Integer UserId);
```

4.编写UserService

```java
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserDOMapper userDOMapper;

    @Autowired
    private userPasswordDOMapper userPasswordDOMapper;

    @Override
    public UserModel getUserById(Integer id) {
        //调用UserDOMapper获取到对应的用户dataobject
        UserDO userDO = userDOMapper.selectByPrimaryKey(id);
        if (userDO == null) {
            return null;
        }

        //通过用户id获取对应的用户加密密码信息
        userPasswordDO userPasswordDO = userPasswordDOMapper.selectByUserId(userDO.getId());

        return convertFromDataObject(userDO, userPasswordDO);
    }

    private UserModel convertFromDataObject(UserDO userDO,userPasswordDO userPasswordDO) {
        if (userDO == null) {
            return null;
        }

        UserModel userModel = new UserModel();
        BeanUtils.copyProperties(userDO, userModel);

        if (userPasswordDO != null) {
            userModel.setEncrptPassword(userPasswordDO.getEncrptPassword());
        }
        return userModel;

    }
}
```

5.这种方式存在的问题

> 直接给前端用户返回了UserModel，使得攻击者可以直接看到密码
>
> 需要在controller层增加一个viewobject模型对象

只需要这些信息：

```java
public class UserVO {
    private Integer id;
    private String name;
    private Byte gender;
    private Integer age;
    private String telphone;
}
```

6.改造controller

```java
public UserVO getUser(@RequestParam(name = "id") Integer id) {
    //调用service服务获取对应id的用户对象并返回给前端
    UserModel userModel = userService.getUserById(id);

    //将核心领域模型用户对象转化为可供UI使用的viewobject
    return convertFromModel(userModel);
}

private UserVO convertFromModel(UserModel userModel) {
    if (userModel == null) {
        return null;
    }
    UserVO userVO = new UserVO();
    BeanUtils.copyProperties(userModel, userVO);
    return userVO;

}
```

### 3.2 定义通用的返回对象——返回正确信息

之前的程序一旦出错，只会返回一个白页，并没有错误信息，需要返回一个有意义的错误信息。

1.增加一个response包。创建CommonReturnType类

```java
public class CommonReturnType {

    //表明对应请求的返回处理结果“success”或“fail”
    private String status;

    //若status=success，则data内返回前端需要的json数据
    //若status=fail，则data内使用通用的错误码格式
    private Object data;

    //定义一个通用的创建方法
    public static CommonReturnType create(Object result) {
        return CommonReturnType.create(result, "success");
    }

    public static CommonReturnType create(Object result,String status) {
        CommonReturnType type = new CommonReturnType();
        type.setStatus(status);
        type.setData(result);
        return type;
    }
}
```

2.改造返回值

```java
public CommonReturnType getUser(@RequestParam(name = "id") Integer id) {
    //调用service服务获取对应id的用户对象并返回给前端
    UserModel userModel = userService.getUserById(id);

    //将核心领域模型用户对象转化为可供UI使用的viewobject
    UserVO userVO = convertFromModel(userModel);
    
    //返回通用对象
    return CommonReturnType.create(userVO);
}
```

### 3.3 定义通用的返回对象——返回错误信息

1.创建error包

2.创建commonError接口

```
public interface CommonError {
    public int getErrCode();

    public String getErrMsg();

    public CommonError setErrMsg(String errMs);
}
```

3.创建实现类

```java
public enum EmBusinessError implements CommonError {
    //通用错误类型00001
    PARAMETER_VALIDATION_ERROR(00001, "参数不合法"),


    //10000开头为用户信息相关错误定义
    USER_NOT_EXIST(10001, "用户不存在")
    ;

    private EmBusinessError(int errCode, String errMsg) {
        this.errCode = errCode;
        this.errMsg = errMsg;
    }

    private int errCode;
    private String errMsg;

    @Override
    public int getErrCode() {
        return this.errCode;
    }

    @Override
    public String getErrMsg() {
        return this.errMsg;
    }

    @Override
    public CommonError setErrMsg(String errMsg) {
        this.errMsg = errMsg;
        return this;
    }
}
```

4.包装器模式实现BusinessException类

```java
/包装器业务异常实现
public class BusinessException extends Exception implements CommonError {

    private CommonError commonError;

    //直接接受EmBusinessError的传参用于构造业务异常
    public BusinessException(CommonError commonError) {
        super();
        this.commonError = commonError;
    }

    //接收自定义errMsg的方式构造业务异常
    public BusinessException(CommonError commonError, String errMsg) {
        super();
        this.commonError = commonError;
        this.commonError.setErrMsg(errMsg);
    }

    @Override
    public int getErrCode() {
        return this.commonError.getErrCode();
    }

    @Override
    public String getErrMsg() {
        return this.commonError.getErrMsg();
    }

    @Override
    public CommonError setErrMsg(String errMsg) {
        this.commonError.setErrMsg(errMsg);
        return this;
    }
}
```

5.抛出异常类

```java
public CommonReturnType getUser(@RequestParam(name = "id") Integer id) throws BusinessException {
    //调用service服务获取对应id的用户对象并返回给前端
    UserModel userModel = userService.getUserById(id);

    //若获取的对应用户信息不存在
    if (userModel == null) {
        throw new BusinessException(EmBusinessError.USER_NOT_EXIST);
    }
    //将核心领域模型用户对象转化为可供UI使用的viewobject
    UserVO userVO = convertFromModel(userModel);

    //返回通用对象
    return CommonReturnType.create(userVO);
}
```

### 3.4 定义通用的返回对象——异常处理

1.定义exceptionHandler解决未被controller层吸收的exception

```java
public class BaseController {

    //定义exceptionHandler解决未被controller层吸收的exception
    @ExceptionHandler(Exception.class)
    @ResponseStatus(HttpStatus.OK)
    @ResponseBody
    public Object handlerException(HttpServletRequest request, Exception ex) {
        Map<String, Object> responseData = new HashMap<>();
        if (ex instanceof BusinessException) {
            BusinessException businessException = (BusinessException) ex;
            responseData.put("errCode", businessException.getErrCode());
            responseData.put("errMsg", businessException.getErrMsg());
        } else {
            responseData.put("errCode", EmBusinessError.UNKNOWN_ERROR.getErrCode());
            responseData.put("errMsg", EmBusinessError.UNKNOWN_ERROR.getErrMsg());
        }
        return CommonReturnType.create(responseData, "fail");
    }

}
```

### 3.5 用户模型管理——otp验证码获取

```java
public class UserController extends BaseController{

    @Autowired
    private UserService userService;

    @Autowired
    private HttpServletRequest httpServletRequest;

    //用户获取otp短信接口
    @RequestMapping("/getotp")
    @ResponseBody
    public CommonReturnType getOtp(@RequestParam(name = "telphone") String telphone) {
        //需要按照一定的规则生成OTP验证码
        Random random = new Random();
        int randomInt = random.nextInt(99999);
        randomInt += 10000;
        String otpCode = String.valueOf(randomInt);

        //将OTP验证码同对应用户的手机号关联，使用httpsession的方式绑定手机号与OTPCDOE
        httpServletRequest.getSession().setAttribute(telphone, otpCode);

        //将OTP验证码通过短信通道发送给用户，省略
        System.out.println("telphone=" + telphone + "&otpCode=" + otpCode);

        return CommonReturnType.create(null);
    }
```

测试，在控制台打印数据

### 3.6 用户模型管理——Metronic模板简介

采用前后端分离的思想，建立一个html文件夹，引入static文件夹

前端文件保存在本地的哪个盘下都可以，因为是通过ajax来异步获取接口

###	3.7 用户模型管理——getotp页面实现

1.getotp.html：

```xml
<html>
<head>
    <meta charset="UTF-8">
    <script src="static/assets/global/plugins/jquery-1.11.0.min.js" type="text/javascript"></script>
    <title>Title</title>
</head>
<body>
    <div>
        <h3>获取otp信息</h3>
        <div>
            <label>手机号</label>
            <div>
                <input type="text" placeholder="手机号" name="telphone" id="telphone"/>
            </div>
        </div>
        <div>
            <button id="getotp" type="submit">
                获取otp短信
            </button>
        </div>
    </div>

</body>

<script>
    jQuery(document).ready(function () {

        //绑定otp的click事件用于向后端发送获取手机验证码的请求
        $("#getotp").on("click",function () {

            var telphone=$("#telphone").val();
            if (telphone==null || telphone=="") {
                alert("手机号不能为空");
                return false;
            }


            //映射到后端@RequestMapping(value = "/getotp", method = {RequestMethod.POST}, consumes = {CONTENT_TYPE_FORMED})
            $.ajax({
                type:"POST",
                contentType:"application/x-www-form-urlencoded",
                url:"http://localhost:8080/user/getotp",
                data:{
                    "telphone":$("#telphone").val(),
                },
                success:function (data) {
                    if (data.status=="success") {
                        alert("otp已经发送到了您的手机，请注意查收");
                    }else {
                        alert("otp发送失败，原因为" + data.data.errMsg);
                    }
                },
                error:function (data) {
                    alert("otp发送失败，原因为"+data.responseText);
                }
            });
        });
    });
</script>
</html>
```

2.指定controller的method

```java
@RequestMapping(value = "/getotp", method = {RequestMethod.POST}, consumes = {CONTENT_TYPE_FORMED})
```

3.提示发送失败，使用chrome调试，发现报错为

```
getotp.html?_ijt=cqdae6hmhq9069c9s4muooakju:1 Access to XMLHttpRequest at 'http://localhost:8080/user/getotp' from origin 'http://localhost:63342' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

跨域请求错误，只需要在UserController类上加一个注解`@CrossOrigin`即可

###	3.8 用户模型管理——getotp页面美化

1.引入样式表

```html
<link href="static/assets/global/plugins/bootstrap/css/bootstrap.min.css" rel="stylesheet" type="text/css"/>
<link href="static/assets/global/plugins/css/component.css" rel="stylesheet" type="text/css"/>
<link href="static/assets/admin/pages/css/login.css" rel="stylesheet" type="text/css"/>
```

2.使用样式

```html
<body class="login">
    <div class="content">
        <h3 class="form-title">获取otp信息</h3>
        <div class="form-group">
            <label class="control-label">手机号</label>
            <div>
                <input class="form-control" type="text" placeholder="手机号" name="telphone" id="telphone"/>
            </div>
        </div>
        <div class="form-actions">
            <button class="btn blue" id="getotp" type="submit">
                获取otp短信
            </button>
        </div>
    </div>

</body>
```

###	3.9 用户模型管理——用户注册功能实现

1.实现方法：用户注册接口

```java
     //用户注册接口
    @RequestMapping(value = "/register", method = {RequestMethod.POST}, consumes = {CONTENT_TYPE_FORMED})
    @ResponseBody
    public CommonReturnType register(@RequestParam(name = "telphone") String telphone,
                                     @RequestParam(name = "otpCode") String otpCode,
                                     @RequestParam(name = "name") String name,
                                     @RequestParam(name = "gender") String gender,
                                     @RequestParam(name = "age") String age,
                                     @RequestParam(name = "password") String password) throws BusinessException, UnsupportedEncodingException, NoSuchAlgorithmException {

        //验证手机号和对应的otpCode相符合
        String inSessionOtpCode = (String) this.httpServletRequest.getSession().getAttribute(telphone);
        if (!com.alibaba.druid.util.StringUtils.equals(otpCode, inSessionOtpCode)) {
            throw new BusinessException(EmBusinessError.PARAMETER_VALIDATION_ERROR, "短信验证码不符合");
        }
        //用户的注册流程
        UserModel userModel = new UserModel();
        userModel.setName(name);
        userModel.setAge(Integer.valueOf(age));
        userModel.setGender(Byte.valueOf(gender));
        userModel.setTelphone(telphone);
        userModel.setRegisitMode("byphone");

        //密码加密
        userModel.setEncrptPassword(this.EncodeByMd5(password));

        userService.register(userModel);
        return CommonReturnType.create(null);

    }

    //密码加密
    public String EncodeByMd5(String str) throws NoSuchAlgorithmException, UnsupportedEncodingException {
        //确定计算方法
        MessageDigest md5 = MessageDigest.getInstance("MD5");
        BASE64Encoder base64en = new BASE64Encoder();
        //加密字符串
        String newstr = base64en.encode(md5.digest(str.getBytes("utf-8")));
        return newstr;
    }
```

2.引入做输入校验的依赖

```xml
<!-- https://mvnrepository.com/artifact/org.apache.commons/commons-lang3 -->
<dependency>
  <groupId>org.apache.commons</groupId>
  <artifactId>commons-lang3</artifactId>
  <version>3.7</version>
</dependency>
```

3.UserServiceImpl的register方法

```java
    @Override
    @Transactional//声明事务
    public void register(UserModel userModel) throws BusinessException {
        //校验
        if (userModel == null) {
            throw new BusinessException(EmBusinessError.PARAMETER_VALIDATION_ERROR);
        }
        if (StringUtils.isEmpty(userModel.getName())
                || userModel.getGender() == null
                || userModel.getAge() == null
                || StringUtils.isEmpty(userModel.getTelphone())) {
            throw new BusinessException(EmBusinessError.PARAMETER_VALIDATION_ERROR);
        }

        //实现model->dataobject方法
        UserDO userDO = convertFromModel(userModel);
        //insertSelective相对于insert方法，不会覆盖掉数据库的默认值
        userDOMapper.insertSelective(userDO);

        userModel.setId(userDO.getId());

        userPasswordDO userPasswordDO = convertPasswordFromModel(userModel);
        userPasswordDOMapper.insertSelective(userPasswordDO);

        return;
    }

    private userPasswordDO convertPasswordFromModel(UserModel userModel) {
        if (userModel == null) {
            return null;
        }
        userPasswordDO userPasswordDO = new userPasswordDO();
        userPasswordDO.setEncrptPassword(userModel.getEncrptPassword());
        userPasswordDO.setUserId(userModel.getId());

        return userPasswordDO;
    }

    private UserDO convertFromModel(UserModel userModel) {
        if (userModel == null) {
            return null;
        }
        UserDO userDO = new UserDO();
        BeanUtils.copyProperties(userModel, userDO);
        return userDO;
    }

```

4.前端界面

首先在getotp界面添加注册成功的跳转界面

```javascript
success:function (data) {
    if (data.status=="success") {
        alert("otp已经发送到了您的手机，请注意查收");
        window.location.href="register.html";
    }else {
        alert("otp发送失败，原因为" + data.data.errMsg);
    }
},
```

模仿之前写的界面，新建一个register.html

```html
<body class="login">
    <div class="content">
        <h3 class="form-title">用户注册</h3>
        <div class="form-group">
            <label class="control-label">手机号</label>
            <div>
                <input class="form-control" type="text" placeholder="手机号" name="telphone" id="telphone"/>
            </div>
        </div>
        <div class="form-group">
            <label class="control-label">验证码</label>
            <div>
                <input class="form-control" type="text" placeholder="验证码" name="otpCode" id="otpCode"/>
            </div>
        </div>
        <div class="form-group">
            <label class="control-label">用户昵称</label>
            <div>
                <input class="form-control" type="text" placeholder="用户昵称" name="name" id="name"/>
            </div>
        </div>
        <div class="form-group">
            <label class="control-label">性别</label>
            <div>
                <input class="form-control" type="text" placeholder="性别" name="gender" id="gender"/>
            </div>
        </div>
        <div class="form-group">
            <label class="control-label">年龄</label>
            <div>
                <input class="form-control" type="text" placeholder="年龄" name="age" id="age"/>
            </div>
        </div>
        <div class="form-group">
            <label class="control-label">密码</label>
            <div>
                <input class="form-control" type="password" placeholder="密码" name="password" id="password"/>
            </div>
        </div>
        <div class="form-actions">
            <button class="btn blue" id="register" type="submit">
                提交注册
            </button>
        </div>
    </div>

</body>

<script>
    jQuery(document).ready(function () {

        //绑定otp的click事件用于向后端发送获取手机验证码的请求
        $("#register").on("click",function () {

            var telphone=$("#telphone").val();
            var otpCode=$("#otpCode").val();
            var password=$("#password").val();
            var age=$("#age").val();
            var gender=$("#gender").val();
            var name=$("#name").val();
            if (telphone==null || telphone=="") {
                alert("手机号不能为空");
                return false;
            }
            if (otpCode==null || otpCode=="") {
                alert("验证码不能为空");
                return false;
            }
            if (name==null || name=="") {
                alert("用户名不能为空");
                return false;
            }
            if (gender==null || gender=="") {
                alert("性别不能为空");
                return false;
            }
            if (age==null || age=="") {
                alert("年龄不能为空");
                return false;
            }
            if (password==null || password=="") {
                alert("密码不能为空");
                return false;
            }

            //映射到后端@RequestMapping(value = "/register", method = {RequestMethod.POST}, consumes = {CONTENT_TYPE_FORMED})
            $.ajax({
                type:"POST",
                contentType:"application/x-www-form-urlencoded",
                url:"http://localhost:8080/user/register",
                data:{
                    "telphone":telphone,
                    "otpCode":otpCode,
                    "password":password,
                    "age":age,
                    "gender":gender,
                    "name":name
                },
                //允许跨域请求
                xhrFields:{withCredentials:true},
                success:function (data) {
                    if (data.status=="success") {
                        alert("注册成功");
                    }else {
                        alert("注册失败，原因为" + data.data.errMsg);
                    }
                },
                error:function (data) {
                    alert("注册失败，原因为"+data.responseText);
                }
            });
            return false;
        });
    });
</script>
```

5.调试

发现报错，获取不到验证码

跨域请求问题

在UserController上添加如下注解：

```java
//跨域请求中，不能做到session共享
@CrossOrigin(allowCredentials = "true",allowedHeaders = "*")
```

6.注册成功，但是查看数据库，发现password表中并没有user_id

在UserDOMapper的insertSelective方法中添加如下代码：

```xml
 <insert id="insertSelective" parameterType="com.miaoshaproject.dataobject.UserDO" keyProperty="id" useGeneratedKeys="true">
```

通过这样的方式将自增id取出之后复制给对应的UserDO

7.修改UserServiceImpl

```java
UserDO userDO = convertFromModel(userModel);
//insertSelective相对于insert方法，不会覆盖掉数据库的默认值
userDOMapper.insertSelective(userDO);

userModel.setId(userDO.getId());

userPasswordDO userPasswordDO = convertPasswordFromModel(userModel);
userPasswordDOMapper.insertSelective(userPasswordDO);

return;
```

重新测试成功

8.上面并没有做手机号的唯一性验证

首先，在数据库中添加索引：

索引名称为：telphone_unique_index，索引字段选择telphone，索引类型为UNIQUE，索引方法为BTREE

然后修改以下代码：

```java
try {
    userDOMapper.insertSelective(userDO);
} catch (DuplicateKeyException ex) {
    throw new BusinessException(EmBusinessError.PARAMETER_VALIDATION_ERROR, "手机号已注册");
}
```

###	3.9 用户模型管理——用户登录功能实现

1.UserController中的用户登录接口

```java

    //用户登录接口
    @RequestMapping(value = "/login", method = {RequestMethod.POST}, consumes = {CONTENT_TYPE_FORMED})
    @ResponseBody
    public CommonReturnType login(@RequestParam(name = "telphone") String telphone,
                                  @RequestParam(name = "password") String password) throws BusinessException, UnsupportedEncodingException, NoSuchAlgorithmException {
        //入参校验
        if (StringUtils.isEmpty(telphone) || StringUtils.isEmpty(password)) {
            throw new BusinessException(EmBusinessError.PARAMETER_VALIDATION_ERROR);
        }

        //用户登录服务，用来校验用户登录是否合法
        //用户加密后的密码
        UserModel userModel = userService.validateLogin(telphone, this.EncodeByMd5(password));

        //将登陆凭证加入到用户登录成功的session内
        this.httpServletRequest.getSession().setAttribute("IS_LOGIN", true);
        this.httpServletRequest.getSession().setAttribute("LOGIN_USER", userModel);

        return CommonReturnType.create(null);

    }
```

2.UserService中的校验登录方法

```java
    /*
    telphone:用户注册手机
    encrptPassowrd:用户加密后的密码
     */
    UserModel validateLogin(String telphone, String encrptPassword) throws BusinessException;
```

3.UserServiceImpl的登录方法实现

```java
    @Override
    public UserModel validateLogin(String telphone, String encrptPassword) throws BusinessException {
        //通过用户手机获取用户信息
        UserDO userDO = userDOMapper.selectByTelphone(telphone);
        if (userDO == null) {
            throw new BusinessException(EmBusinessError.USER_LOOGIN_FAIL);
        }
        userPasswordDO userPasswordDO = userPasswordDOMapper.selectByUserId(userDO.getId());
        UserModel userModel = convertFromDataObject(userDO, userPasswordDO);

        //比对用户信息内加密的密码是否和传输进来的密码相匹配
        if (StringUtils.equals(encrptPassword, userModel.getEncrptPassword())) {
            throw new BusinessException(EmBusinessError.USER_LOOGIN_FAIL);
        }

        return userModel;
    }
```

4.UserDOMapper.xml中的新建方法

```xml
<select id="selectByTelphone" resultMap="BaseResultMap">
    select
    <include refid="Base_Column_List"/>
    from user_info
    where telphone = #{telphone,jdbcType=VARCHAR}
</select>
```

5.UserDOMapper中建立映射

```java
//根据电话号码取得用户对象
UserDO selectByTelphone(String telphone);
```

6.新建前端界面：login.html

```html
<body class="login">
    <div class="content">
        <h3 class="form-title">用户登录</h3>
        <div class="form-group">
            <label class="control-label">手机号</label>
            <div>
                <input class="form-control" type="text" placeholder="手机号" name="telphone" id="telphone"/>
            </div>
        </div>
        <div class="form-group">
            <label class="control-label">密码</label>
            <div>
                <input class="form-control" type="password" placeholder="密码" name="password" id="password"/>
            </div>
        </div>
        <div class="form-actions">
            <button class="btn blue" id="login" type="submit">
                登录
            </button>
            <button class="btn green" id="register" type="submit">
                注册
            </button>
        </div>
    </div>

</body>

<script>
    jQuery(document).ready(function () {

        //绑定注册按钮的click事件用于跳转到注册页面
        $("#register").on("click",function () {
            window.location.href = "getotp.html";
        });

        //绑定登录按钮的click事件用于登录
        $("#login").on("click",function () {

            var telphone=$("#telphone").val();
            var password=$("#password").val();
            if (telphone==null || telphone=="") {
                alert("手机号不能为空");
                return false;
            }
            if (password==null || password=="") {
                alert("密码不能为空");
                return false;
            }

            //映射到后端@RequestMapping(value = "/login", method = {RequestMethod.POST}, consumes = {CONTENT_TYPE_FORMED})
            $.ajax({
                type:"POST",
                contentType:"application/x-www-form-urlencoded",
                url:"http://localhost:8080/user/login",
                data:{
                    "telphone":telphone,
                    "password":password
                },
                //允许跨域请求
                xhrFields:{withCredentials:true},
                success:function (data) {
                    if (data.status=="success") {
                        alert("登录成功");
                    }else {
                        alert("登录失败，原因为" + data.data.errMsg);
                    }
                },
                error:function (data) {
                    alert("登录失败，原因为"+data.responseText);
                }
            });
            return false;
        });
    });
```

###	3.10 优化校验规则

1.查询maven仓库中是否由可用类库

```xml
<!--校验-->
<dependency>
  <groupId>org.hibernate</groupId>
  <artifactId>hibernate-validator</artifactId>
  <version>5.2.4.Final</version>
</dependency>
```

2.对validator进行一个简单的封装

新建validator的目录

新建一个ValidationResult的类

```java
public class ValidationResult {
    //校验结果是否有错
    private boolean hasErrors = false;

    //存放错误信息的map
    private Map<String, String> errorMsgMap = new HashMap<>();

    public boolean isHasErrors() {
        return hasErrors;
    }

    public void setHasErrors(boolean hasErrors) {
        this.hasErrors = hasErrors;
    }

    public Map<String, String> getErrorMsgMap() {
        return errorMsgMap;
    }

    public void setErrorMsgMap(Map<String, String> errorMsgMap) {
        this.errorMsgMap = errorMsgMap;
    }

    //实现通用的通过格式化字符串信息获取错误结果的msg方法
    public String getErrMsg() {
        return StringUtils.join(errorMsgMap.values().toArray(), ",");
    }
}
```

新建一个ValidatiorImpl的类

```java
@Component
public class ValidatorImpl implements InitializingBean {

    private Validator validator;

    //实现校验方法并返回校验结果
    public ValidationResult validate(Object bean) {
        final ValidationResult result = new ValidationResult();
        Set<ConstraintViolation<Object>> constraintViolationSet = validator.validate(bean);
        if (constraintViolationSet.size() > 0) {
            //有错误
            result.setHasErrors(true);
            constraintViolationSet.forEach(constraintViolation ->{
                String errMsg = constraintViolation.getMessage();
                String propertyName = constraintViolation.getPropertyPath().toString();
                result.getErrorMsgMap().put(propertyName, errMsg);
            });
        }
        return result;
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        //将hibernate validator通过工厂的初始化方式使其实例化
        this.validator = Validation.buildDefaultValidatorFactory().getValidator();
    }
}
```

3.修改UserModel，基于注解的校验方式

```java
@NotBlank(message = "用户名不能为空")
private String name;

@NotNull(message = "性别不能填写")
private Byte gender;

@NotNull(message = "年龄不能不填写")
@Min(value = 0, message = "年龄必须大于0岁")
@Max(value = 150, message = "年龄必须小于150岁")
private Integer age;

@NotBlank(message = "手机号不能为空")
private String telphone;
private String regisitMode;
private Integer thirdPartyId;

@NotBlank(message = "密码不能为空")
private String encrptPassword;
```

4.在UserServiceImpl中使用validator

引入bean

```java
@Autowired
private ValidatorImpl validator;
```



```java
        //校验
//        if (userModel == null) {
//            throw new BusinessException(EmBusinessError.PARAMETER_VALIDATION_ERROR);
//        }
//        if (StringUtils.isEmpty(userModel.getName())
//                || userModel.getGender() == null
//                || userModel.getAge() == null
//                || StringUtils.isEmpty(userModel.getTelphone())) {
//            throw new BusinessException(EmBusinessError.PARAMETER_VALIDATION_ERROR);
//        }

        ValidationResult result = validator.validate(userModel);
        if (result.isHasErrors()) {
            throw new BusinessException(EmBusinessError.PARAMETER_VALIDATION_ERROR, result.getErrMsg());
        }
```

以后做校验时只需要在model的属性上做注解即可