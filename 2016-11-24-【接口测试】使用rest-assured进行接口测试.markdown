---
layout:     post
title:      "使用rest-assured进行接口测试"
subtitle:   ""
date:       2016-11-24 17:32:33
author:     "小白"
tags:
    - rest-assured
    - Java后端
    - 接口测试
---

# 前言 #
第一次在社区发主题帖，还是挺~紧张的（雾）。</br>
说一下发帖的原因，由于偶然机会，得知了rest-assured这个项目，据说很棒的样子，我就屁颠屁颠到Github上，先把项目fork过来再说，不过研究源码可能是有生之年了(〃ω〃)。</br>
正好我们组在使用Java脚本进行接口测试，会需要写一些公共方法来验证接口返回的json节点神马的，当然保证状态码200肯定是前提。之前使用的是net.sf.json，随便贴几行代码，Object、Array、String、JSONObject神马的互相转换取值，讲道理挺容易被绕晕的。。。</br>
![](/uploads/photo/2016/bc2d11f5cba3ce9a057777c360d72267.png!large)

而且，给同事讲解的时候也比较尴尬（抛出一个异常的眼神你们自己体会），有时候我们为了提升效率，还是不喜欢这种绕的做法。看rest-assured的[用户手册](https://github.com/rest-assured/rest-assured/wiki/Usage)时，首先看的就是节点取值验证部分，然后我o゜▽゜)o，总之非常高兴就是了。
# 正文 #
接下来我就依据文档写了一些demo，在这里分享下自己的经验，没啥厉害的，看看热闹就行~</br>
首先，我使用idea新建了一个maven项目，添加了以下依赖:

       

```yml
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <version>3.0.1</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>json-schema-validator</artifactId>
            <version>3.0.1</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>spring-mock-mvc</artifactId>
            <version>3.0.1</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.codehaus.groovy</groupId>
            <artifactId>groovy-all</artifactId>
            <version>1.8.1</version>
            <scope>provided</scope>
        </dependency>
```
然后，写一个测试类，官方推荐我们在使用的时候，静态导入以下：

```java
import static io.restassured.RestAssured.*;
import static io.restassured.matcher.RestAssuredMatchers.*;
import static io.restassured.path.json.JsonPath.from;
import static org.hamcrest.Matchers.*;
import static io.restassured.module.jsv.JsonSchemaValidator.*;
```
接下来就是我在接口测试中验证的常见逻辑了，实战time，Go！</br>
1.HTTP状态码
----------
```java
    @Test
    public void test1() {
        Response response = get("url");//发起get请求，并获取响应
        if (response.getStatusCode() == 200) {//如果响应码等于200
         ///
        }
     }
```
先使用get方法发起请求（支持HTTP和HTTPS，不需要我们手动调用不同的方法类啦），获得响应后就需要我们做进一步的处理。</br>



2.节点值验证</br>
----------

比如对这样一串json，
```js
{
    "result": {
        "cardtablist": [
            {
                "id": 1,
                "name": "服务",
                "taburl": "",
                "type": "0"
            },
            {
                "id": 2,
                "name": "金融",
                "taburl": "https://demo/",
                "type": "1"
            }
        ]
    },
    "returncode": 0,
    "message": "ok"
}
```
我们可以简单写一下：
```java
response.then().body("returncode", equalTo(0));//判断returncode是否等于
response.then().body("result.cardtablist", hasSize(2));//判断cardtablist数组的长度是否等于2
response.then().body("result.cardtablist[0].id", equalTo(1));//判断cardtablist数组第一个元素下，id字段值是否等于1
response.then().body("result.cardtablist[0].id", lessThan(2));//判断cardtablist数组第一个元素下，id字段值是否小于2
response.then().body("result.cardtablist[0].id", greaterThan(0));//判断cardtablist数组第一个元素下，id字段值是否大于0
response.then().body("result.cardtablist[0].name", not(""));//判断cardtablist第一个数组元素中name字段值不为“”
response.then().body("result.cardtablist[0].name.length()", greaterThan(10));//判断cardtablist第一个数组元素中name字段值长度大于10
List<String> dspnameStr = from(jsonStr).getList("result.adlist.findAll { it.ishavead == 1 }.dspname");//得到所有的ishavead=1的adlist数组元素，并获取其中dspname的值,放在一个集合里
```


3.格式化打印json</br>
----------
我觉得这个功能挺赞啊，省得打开浏览器去http://json.cn/做格式化。
```java
response.getBody().prettyPrint();//格式化打印JSON数据
```
当然，控制台打印的json字符串不带颜色，这就不要挑剔了。

4.参数化</br>
----------
```java
given().param("p1", "0").param("p2", "1").get("http://server/demo");
```
这里把参数拿出来单独赋值，最终会拼接成形如http://server/demo?p1=0&p2=1的链接并进行访问。

5.响应时间</br>
----------
```java
when().get("https://server/demo?p1=0&p2=1").then().time(lessThan(100L),TimeUnit.MILLISECONDS);//判断响应时间是否少于预期值。
```
 
6.JSON Schema Validation
----------
这个功能我也挺喜欢的，可以通过自己编写json指定要验证的结构字段（比如取值范围限定、字符串整型数组类型、必要字段是否存在等），具体内容有兴趣的同学可以自学一下。此处只展示API的调用：
```java
get("https://server/demo?p1=0&p2=1").then().assertThat().body(matchesJsonSchemaInClasspath("test.json"));
```

这里我把写好的test.json文件放在idea自动生成的src/main/resources路径下，里面简单写了下：
```js
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "Product set",
  "type": "object",
  "properties": {
    "result": {
      "type": "object"
    },
    "returncode": {
      "type": "integer"
    },
    "message": {
      "type": "integer"
    }
  },
  "required": [
    "result",
    "returncode",
    "message"
  ]
}
```
这里验证点有：保证返回result、returncode、message字段，并对每个字段的value进行类型限定。
暂时写到这吧（其实是我暂时就只看了一小部分），某校长还等着吐槽我QAQ。</br>

心好累_(:3」∠)_  </br>

另外，我的打赏二维码只是开个玩笑，别当真。。。