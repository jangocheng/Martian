<div align=center>
<img width="200px;" src="http://mars-framework.com/img/logo-github.png"/>
</div>

<br/>

<div align=center>

<img src="https://img.shields.io/badge/licenes-MIT-brightgreen.svg"/>
<img src="https://img.shields.io/badge/jdk-1.8+-brightgreen.svg"/>
<img src="https://img.shields.io/badge/maven-3.5.4+-brightgreen.svg"/>
<img src="https://img.shields.io/badge/release-master-brightgreen.svg"/>

</div>

<br/>

<div align=center>

声明式API编程 (DAP) 框架

</div>

## 项目简介

Martian 是一个声明式API编程 (DAP) 的Java开发框架

- 以HttpServer作为 http服务，彻底脱离Tomcat这一类的Web容器和Servlet，同时也让项目减少了几个依赖
- 声明式API，让Controller变成了一个interface，降低了开发的工作量
- 拥有其他web框架 拥有的大部分功能，比如AOP,IOC,声明式事务,异常监听等
- 拥有自主开发的 半ORM持久层框架，并天然的集成到了Martian中
- 如果你不喜欢HttpServer，我们也提供了Tomcat启动器
- 支持分布式开发

## 项目生态
- 【分布式组件】[Martian-cloud](https://github.com/yuyenews/Martian-Cloud)
- 【网关及其他组件】[Martian-gateway](https://github.com/yuyenews/Martian-Component)
- 【更多组件】筹划中

## 使用示例

[https://github.com/yuyenews/Mars-Example](https://github.com/yuyenews/Mars-Example)

## 项目特性
### 一、声明式API
只需要在你的interface上加上一个注解，即可对外提供一个接口，并且我们还支持传统的Controller写法
```java
@MarsApi(refBean="要引用的bean的name")
public interface TestApi {

   返回类型 selectList(TestDTO testDTO);
}
```
### 二、单表增删改查无sql
```java
// 根据主键查询一条数据
@MarsGet(tableName = "userinfo",primaryKey = "id")
public abstract 要返回的实体类 selectById(int id);

// 单表新增
@MarsUpdate(tableName = "userinfo",operType = OperType.INSERT)
public abstract int insert(实体对象参数);

// 单表根据主键删除
@MarsUpdate(tableName = "userinfo",operType = OperType.DELETE,primaryKey = "id")
public abstract int delete(int id);

// 单表根据主键修改
@MarsUpdate(tableName = "userinfo",operType = OperType.UPDATE,primaryKey = "id")
public abstract int update(实体对象参数);
```

### 三、参数校验只需一个注解
在API接口的参数对象里的字段上加上一个注解即可（VO的字段上加注解）
```java
// 不可为空，且长度在2-3位
@MarsDataCheck(notNull = true,maxLength = 3L,minLength = 2L, msg = "id不可为空且长度必须在2-3位之间")
private Integer id;

// 正则校验
@MarsDataCheck(reg = "^(?![0-9]+$)(?![a-zA-Z]+$)[0-9A-Za-z]{6,12}$",msg = "密码不可以为空且必须是6-12位数字字母组合")
private String password;
```

前端如何得到提示?

正常请求API就好了，如果校验不通过会得到这样一个json
```json
{"error_code":1128,"error_info":"提示文字"}
```

### 四、异常监听器
通常我们在写代码的时候，需要给每个Controller的方法加上try{}catch(){},用来在异常的时候，能够正常的返回 json串

spring是有一个叫ExecptionHandler 来解决这个问题，而Martion也提供了对应的解决方案

解决方案就是什么都不用管，如果出了异常，会自动给前端返回如下json串
```json
{"error_code":500,"error_info":"异常提示"}
```

### 五、一行注解，解决分布式锁
在要加锁的方法上添加RedisLock注解
```java
@RedisLock(key = "自己定义一个key")
public int insert(){
  return 1;
}
```