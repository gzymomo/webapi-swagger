# webapi-swagger
dot.net core  swagger
### .Net Core mvc 中 FromURL、FromHeader、FromBody、区别

#### [httpget]
1.  无参get
```C
GET /api/TestDemo HTTP/1.1
Host: localhost:5001
Cache-Control: no-cache
```
> 服务端  直接返回数据，没什么好说的
```Java

[HttpGet]
public List<DomainUser> GetsData()
{
    List<DomainUser> list = new List<DomainUser>();
    list.Add(new DomainUser(){Name="gzyMomo",Age=12,UserSex=Sex.female});
    list.Add(new DomainUser(){Name="gzyMomo",Age=12,UserSex=Sex.female});
    return list;
}
```
2. 有参
> **get请求的参数 只能加在url后面，这种方式参数容易暴露且别修改，**
>   * javascript 中Ajax 请求方法的 data:{"name":"urlname","age":"1"} 
>   * Form 表单提交 key/value
>   * Url 中?name=formname&age=1  
>    
> **以上3种方式 执行效果相同**  

![66663c3f400c11328022401818baba41.png](en-resource://database/542:1)


> 服务端代码
> 默认情况 name和age 如果不加修饰 从url 中获取 如果换成[FromHeader] name 或者 [FromBody] name 时，服务点name 则接收不到url 传过来的参数。
```java

[HttpGet]
public List<DomainUser> GetsDataPara(string name,int age)
{
    List<DomainUser> list = new List<DomainUser>();
    list.Add(new DomainUser(){Name=name, Age= age,UserSex=Sex.female});
    list.Add(new DomainUser(){Name="gzyMomo",Age=12,UserSex=Sex.female});
    return list;
}
```
#### [httppost]
1. 无参
> POST 请求无参时，除了请求method 与get不同 其他的都相同，无参时一般很少用Post 求情，get足以满足要求
2. 有参
* **[FormUrl]** 可以缺省 同get
* **form 表单 [FromForm]**
> html代码 **下面两种方式提交等效**
```html
<form action="https://localhost:5001/api/TestDemo" method="post"> 
    name: <input type="text" id="txt1" name='name' />
    age:   <input type="text" id="txt1" name='age' />
            <input type="submit" value="submit">
</form
//javascript
$.ajax({
	type: "POST",
	url: "https://localhost:5001/api/TestDemo",
	data: {"name":"test","age":"1"},
	success: function (data) {
	    console.log(data);
	}
});

```
> 服务端代码 此时 参数前必须要以[FromForm] 修饰 否则接受不到参数。
```java
[HttpPost]
  public ActionResult<List<DomainUser>>PostdataFromForm([FromForm]string name,[FromForm]int age)
  {
            Console.WriteLine(name);
            Console.WriteLine(age);
            List<DomainUser> list = new List<DomainUser>();
            list.Add(new DomainUser(){Name=name,Age=age,UserSex=Sex.female});
             list.Add(new DomainUser(){Name="sdf",Age=12,UserSex=Sex.male});
            return list;
 }
```

*  **[FormBody]**
> 在测试 frombody 是出现跨域问题 在服务端添加cors 支持 ，在这里不做深入介绍
> 前端代码， 在使用Ajax 调用时出现问题 参数 data为 **json** 对象时 后台无法解析 报400错误
> 使用 JSON.stringify( data ) 将data 转成字符串
```java
$("#btnAjax").click(function(){
	var data = JSON.stringify({name:'momo',age:12,userSex:1});
	$.ajax({
		type: "POST",
		url: "https://localhost:5001/api/TestDemo",
		data: data,
		contentType:"application/json",
		success: function (data) {
		console.log(data);
		}
	});
})
```
> 后端代码 参数为实体，实体钱加标记 **[FromBody]** 标记
```java

[HttpPost]
public ActionResult<List<DomainUser>>PostdataFromForm([FromBody]DomainUser user)
{
    Console.WriteLine(user);
    String value = JsonConvert.SerializeObject(user);
    Console.WriteLine(value);
    List<DomainUser> list = new List<DomainUser>();
    list.Add(new DomainUser(){Name=user.Name,Age=12,UserSex=Sex.male});
    return list;
}
```


 



  

