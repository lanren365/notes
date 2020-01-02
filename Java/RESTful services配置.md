# RESTful services配置
  `Root Resource And Sub-Resource` 资源是组成RESTful服务的关键部分，可以使用HTTP方法（如：`GET`、`POST`、`PUT`和`DELETE`）操作资源。在JAX-RX中，资源通过POJO实现，使用`@Path` 注释组成其标识符。资源可以有子资源，父资源是资源集合，子资源是成员资源。  

  `Resources`是”/services” URI组成是集合资源，`UserResource`是“/services/user” URI组成的成员资源;   
    
## HTTP Methods 
  HTTP方法映射到资源的CRUD（创建、读取、更新和删除）操作，基本模式如下：
```md
> HTTP GET: 读取/列出/检索单个资源

> HTTP POST: 新建资源

> HTTP PUT: 更新现有资源或资源集合

> HTTP DELETE: 删除资源或资源集合
```
## @Produces 
`@Produces`注释用来指定将要返回给client端的数据标识类型（MIME）。`@Produces`可以作为class注释，也可以作为方法注释，方法的`@Produces`注释将会覆盖class的注释。 覆盖的意思是假如方法声明了自己的Produce，那么以方法的为准，class的仅供参考:
1. 指定MIME类型 
```java
@Produces(“application/json”) 
```
2. 指定多个MIME类型 
```java
@Produces({“application/json”,“application/xml”})
```
## @Consumes 
`@Consumes`与@`Produces`相反，用来指定可以接受client发送过来的MIME类型，同样可以用于class或者method，也可以指定多个MIME类型，一般用于@PUT，@POST 。参数（ParaAnnotations） meter Parameter Annotations用于获取client发送的数据。本文只介绍常用的注解，更多详见Jersey用户手册 。
## @PathParam
使用`@PathParam`可以获取URI中指定规则的参数
## @QueryParam 
使用`@QueryParam`用于获取GET请求中的查询参数
## @FormParam 
使用`@FormParam`从POST请求的表单参数中获取数据。
##  @BeanParam
当请求参数很多时，比如客户端提交一个修改用户的PUT请求，请求中包含很多项用户信息。