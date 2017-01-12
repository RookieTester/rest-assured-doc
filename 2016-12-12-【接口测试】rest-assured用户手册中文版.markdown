---
layout:     post
title:      "rest-assured用户手册"
subtitle:   ""
date:       2016-12-12 00:52:45
author:     "小白，士钊"
tags:
    - rest-assured
    - 接口测试
    - Java后端
---

> 初次翻译英文文档，如有误翻渣翻欢迎指出，谢绝喷子。

注意，如果你正在使用1.9.0或者更早的版本请参考[旧文档](https://github.com/rest-assured/rest-assured/wiki/Usage_Legacy)。

REST Assured是一个可以简化HTTP Builder（创建者模式？）顶层基于REST的服务的测试过程的Java **DSL**（针对某一领域，具有受限表达性的一种计算机程序设计语言）。它支持发起POST,GET,PUT,DELETE,OPTIONS,PATCH和HEAD请求，并且可以用来验证和校对这些请求的响应信息。

# 目录
1. [静态导入方法](#static-imports)
1. [示例](#examples)
  1. [JSON 示例](#example-1---json)
  1. [JSON Schema Validation](#json-schema-validation)
  1. [XML 示例](#example-2---xml)
  1. [高级用法](#example-3---complex-parsing-and-validation)
    1. [XML](#xml-example)
    2. [JSON](#json-example)
  1. [其它示例](#additional-examples)
1. [对float和double类型的支持](#note-on-floats-and-doubles)
1. [关于语法结构](#note-on-syntax) ([syntactic sugar](#syntactic-sugar))
1. [获得响应信息](#getting-response-data)
  1. [在验证响应之后提取特定的值](#extracting-values-from-the-response-after-validation)
  1. [JSON (使用JsonPath)](#json-using-jsonpath)
  1. [XML (使用XmlPath)](#xml-using-xmlpath)
  1. [单独使用路径](#single-path)
  1. [Headers, cookies, status等](#headers-cookies-status-etc)
    1. [获取全部header值](#multi-value-headers)
    1. [获取全部cookie值](#multi-value-cookies)
    1. [获取详细的cookie值](#detailed-cookies)
1. [指定请求信息](#specifying-request-data)
  1. [请求HTTP资源](#invoking-http-resources)
  1. [参数化](#parameters)
  1. [多值参数](#multi-value-parameter)
  1. [参数不赋值](#no-value-parameter)
  1. [路径参数](#path-parameters)
  1. [Cookie](#cookies)
  1. [Header](#headers)
  1. [Content-Type](#content-type)
  1. [请求体（正文）](#request-body)
1. [指定响应信息](#verifying-response-data)
  1. [响应体（正文）](#response-body)
  1. [Cookie](#cookies-1)
  1. [状态码](#status)
  1. [Header](#headers-1)
  1. [Content-Type](#content-type-1)
  1. [内容全匹配](#full-bodycontent-matching)
  1. [不同响应信息之间的对比关联](#use-the-response-to-verify-other-parts-of-the-response)
  1. [测量响应时间](#measuring-response-time)
1. [身份认证](#authentication)
  1. [基本模式](#basic-authentication)
    1. [抢占式](#preemptive-basic-authentication)
    1. [Challenged（？）](#challenged-basic-authentication)
  1. [摘要模式](#digest-authentication)
  1. [表单模式](#form-authentication)
    1. [CSRF](#csrf)
  1. [OAuth](#oauth)
    1. [OAuth1](#oauth-1)
    1. [OAuth2](#oauth-2)
1. [Multi-part类型的表单数据](#multi-part-form-data)
1. [对象映射](#object-mapping)
  1. [序列化](#serialization)
    1. [基于Content-Type的序列化](#content-type-based-serialization)
    1. [通过HashMap创建JSON](#create-json-from-a-hashmap)
    1. [使用Explicit Serializer](#using-an-explicit-serializer)
  1. [反序列化](#deserialization)
    1. [基于Content-Type的反序列化](#content-type-based-deserialization)
    1. [自定义类型反序列化](#custom-content-type-deserialization)
    1. [使用Explicit Deserializer](#using-an-explicit-deserializer)
  1. [配置](#configuration)
  1. [自定义](#custom)
1. 解析器
  1. [自定义](#custom-parsers)
  1. [默认](#default-parser)
1. [默认值](#default-values)
1. [模式重用](#specification-re-use)
1. [过滤器](#filters)
  1. [响应构建器](#response-builder)
1. [日志](#logging)
  1. [请求日志](#request-logging)
  1. [响应日志](#response-logging)
  1. [认证失败后记录](#log-if-validation-fails)
1. [根目录](#root-path)
  1. [路径参数](#path-arguments)
1. [Session支持](#session-support)
  1. [Session过滤](#session-filter)
1. [SSL](#ssl)
  1. [SSL无效的主机名](#ssl-invalid-hostname)
1. [URL编码](#url-encoding)
1. [代理（proxy）配置](#proxy-configuration)
  1. [静态代理配置](#static-proxy-configuration)
  1. [请求规范代理配置](#request-specification-proxy-configuration)
1. [详细配置](#detailed-configuration)
  1. [编码配置](#encoder-config)
  1. [解码配置](#decoder-config)
  1. [Session配置](#session-config)
  1. [重定向（Redirect） DSL](#redirect-dsl)
  1. [网络连接配置](#connection-config)
  1. [JSON配置](#json-config)
  1. [HTTP客户端配置](#http-client-config)
  1. [SSL配置](#ssl-config)
  1. [参数配置](#param-config)
1. [Spring Mock Mvc模型](#spring-mock-mvc-module)
  1. [Bootstrapping RestAssuredMockMvc](#bootstrapping-restassuredmockmvc)
  1. [异步请求](#asynchronous-requests)
  1. [添加Request Post Processors](#adding-request-post-processors)
  1. [添加Result Handlers](#adding-result-handlers)
  1. [使用Result匹配器](#using-result-matchers)
  1. [拦截器](#interceptors)
  1. [Specifications](#specifications)
  1. [重置 RestAssuredMockMvc](#resetting-restassuredmockmvc)
  1. [Spring MVC 身份认证](#spring-mvc-authentication)
     1. [使用Spring的安全测试](#using-spring-security-test)
     1. [注入一个用户](#injecting-a-user)
  1. [参数相关](#note-on-parameters)
1. [Scala支持](#scala-support-module)
1. [Kotlin支持](#kotlin)
1. [更多](#more-info)

## 静态导入方法 ##

推荐大家从以下的类中静态导入方法，以提高使用rest-assured的效率。

```java
io.restassured.RestAssured.*
io.restassured.matcher.RestAssuredMatchers.*
org.hamcrest.Matchers.*
```

如果你想使用[Json Schema](http://json-schema.org/) validation 你还应该静态导入这些方法： 

```java
io.restassured.module.jsv.JsonSchemaValidator.*
```

更多使用方法参阅 [Json Schema Validation](#json-schema-validation) 。

如果你正在使用SpringMVC，你可以使用[spring-mock-mvc](#spring-mock-mvc-module) 模型的Rest Assured DSL来对Spring的controller层进行单元测试。为此你需要从[RestAssuredMockMvc](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/RestAssuredMockMvc.html)静态导入这些方法，而不是`io.restassured.RestAssured`:

```java
io.restassured.module.mockmvc.RestAssuredMockMvc.*
```
# 示例

## 例一 - JSON ##
假设某个get请求 (to http://localhost:8080/lotto) 返回JSON如下:

```javascript
{
"lotto":{
 "lottoId":5,
 "winning-numbers":[2,45,34,23,7,5,3],
 "winners":[{
   "winnerId":23,
   "numbers":[2,45,34,23,3,5]
 },{
   "winnerId":54,
   "numbers":[52,3,12,11,18,22]
 }]
}
}
```

REST assured可以帮助你轻松地进行get请求并对响应信息进行处理。举个例子，如果你想要判断lottoId的值是否等于5，你可以这样做：

```java
get("/lotto").then().body("lotto.lottoId", equalTo(5));
```
又或许你想要检查winnerId的取值**包括**23和54：

```java
get("/lotto").then().body("lotto.winners.winnerId", hasItems(23, 54));
```

注意: `equalTo` 和 `hasItems` 是 Hamcrest matchers的方法，所以你需要静态导入 `org.hamcrest.Matchers`。

注意这里的"json path"语法使用的是<a href='http://groovy-lang.org/processing-xml.html#_gpath'>Groovy的GPath</a>标注法，不要和Jayway的<a href='https://github.com/jayway/JsonPath'>JsonPath</a>语法混淆。

### 以BigDecimal返回float和double类型 ###
（译者注：Java在java.math包中提供的API类BigDecimal，用来对超过16位有效位的数进行精确的运算）

你可以对rest-assured和JsonPath进行配置，使之以BigDecimal返回json里的数值类型数据，而不是float或者double。可以参考下面json文本：

```javascript
{

    "price":12.12 

}
```
默认情况下你验证price字段是否等于float类型的12.12像这样：

```java
get("/price").then().body("price", is(12.12f));
```
但是如果你想用rest-assured的JsonConfig来配置返回的所有的json数值都为BigDecimal类型：

```java
given().
        config(RestAssured.config().jsonConfig(jsonConfig().numberReturnType(BIG_DECIMAL))).
when().
        get("/price").
then().
        body("price", is(new BigDecimal(12.12));
```

### JSON Schema validation ###

自从 `2.1.0` 版本rest-assured开始支持[Json Schema](http://json-schema.org/) validation. 举个例子，在classpath中放置以下的schema文件（译者注：idea的话可以放在resources目录下），products-schema.json:

```javascript
{
    "$schema": "http://json-schema.org/draft-04/schema#",
    "title": "Product set",
    "type": "array",
    "items": {
        "title": "Product",
        "type": "object",
        "properties": {
            "id": {
                "description": "The unique identifier for a product",
                "type": "number"
            },
            "name": {
                "type": "string"
            },
            "price": {
                "type": "number",
                "minimum": 0,
                "exclusiveMinimum": true
            },
            "tags": {
                "type": "array",
                "items": {
                    "type": "string"
                },
                "minItems": 1,
                "uniqueItems": true
            },
            "dimensions": {
                "type": "object",
                "properties": {
                    "length": {"type": "number"},
                    "width": {"type": "number"},
                    "height": {"type": "number"}
                },
                "required": ["length", "width", "height"]
            },
            "warehouseLocation": {
                "description": "Coordinates of the warehouse with the product",
                "$ref": "http://json-schema.org/geo"
            }
        },
        "required": ["id", "name", "price"]
    }
}
```
你可以使用这个schema验证(`/products`)这个请求是否符合规范：
```java
get("/products").then().assertThat().body(matchesJsonSchemaInClasspath("products-schema.json"));
```
`matchesJsonSchemaInClasspath` 静态导入自 `io.restassured.module.jsv.JsonSchemaValidator` 并且我们推荐从这个类中静态导入所有的方法。然而为了使用它你需要依赖于`json-schema-validator` module 或者从这个网页 [下载](http://dl.bintray.com/johanhaleby/generic/json-schema-validator-3.0.1-dist.zip) 它， 又或者通过maven添加下面的依赖:
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>json-schema-validator</artifactId>
    <version>3.0.1</version>
</dependency>
```

### JSON Schema Validation 设置项 ###

rest-assured的`json-schema-validator` module使用Francis Galiegue的[json-schema-validator](https://github.com/fge/json-schema-validator) (`fge`) 库来进行验证。 如果你想配置使用基础`fge`库，你可以像下面例子中：

```java
// Given
JsonSchemaFactory jsonSchemaFactory = JsonSchemaFactory.newBuilder().setValidationConfiguration(ValidationConfiguration.newBuilder().setDefaultVersion(DRAFTV4).freeze()).freeze();

// When
get("/products").then().assertThat().body(matchesJsonSchemaInClasspath("products-schema.json").using(jsonSchemaFactory));
```

using方法允许你进入`jsonSchemaFactory`的实例，rest-assured在验证期间也会进行此操作。这种方式允许我们对验证进行细粒度的配置。

`fge`库也允许验证状态是 `checked`或者`unchecked`（译者注：表示不懂）。默认情况，rest-assured使用`checked`验证，但是如果你想要改变这种方式，你可以提供一个matcher的[JsonSchemaValidatorSettings](http://static.javadoc.io/io.restassured/json-schema-validator/3.0.1/io/restassured/module/jsv/JsonSchemaValidatorSettings.html)实例。举个例子：

```java
get("/products").then().assertThat().body(matchesJsonSchemaInClasspath("products-schema.json").using(settings().with().checkedValidation(false)));
```

这些`settings`方法静态导入自 [JsonSchemaValidatorSettings](http://static.javadoc.io/io.restassured/json-schema-validator/3.0.1/io/restassured/module/jsv/JsonSchemaValidatorSettings.html)类。

### Json Schema Validation的静态配置###
现在想象下你总是使用`unchecked`验证，并且设置默认的json schema版本为3。与其每次都在代码里进行设置，不如静态地进行定义设置。举个例子：
```java
JsonSchemaValidator.settings = settings().with().jsonSchemaFactory(
        JsonSchemaFactory.newBuilder().setValidationConfiguration(ValidationConfiguration.newBuilder().setDefaultVersion(DRAFTV3).freeze()).freeze()).
        and().with().checkedValidation(false);

get("/products").then().assertThat().body(matchesJsonSchemaInClasspath("products-schema.json"));
```

现在任意一个由[JsonSchemaValidator](http://static.javadoc.io/io.restassured/json-schema-validator/3.0.1/io/restassured/module/jsv/JsonSchemaValidatorSettings.html)导入的`matcher`都会使用`DRAFTV3`作为默认版本并且unchecked validation。

想要重置`JsonSchemaValidator`到默认设置仅仅需要调用`reset`方法：

```java
JsonSchemaValidator.reset();
```

### 不使用rest-assured的Json Schema Validation  ###
你也可以在不依赖rest-assured的情况下使用`json-schema-validator` module。如果你想要把json文本表示为`String`类型的字符串，你可以这样做：

```java
import org.junit.Test;
import static io.restassured.module.jsv.JsonSchemaValidator.matchesJsonSchemaInClasspath;
import static org.hamcrest.MatcherAssert.assertThat;
 
public class JsonSchemaValidatorWithoutRestAssuredTest {
 
 
    @Test public void
    validates_schema_in_classpath() {
        // Given
        String json = ... // Greeting response
 
        // Then
        assertThat(json, matchesJsonSchemaInClasspath("greeting-schema.json"));
    }
}
```

更多信息请参阅[新手入门](GattingStarted)。

### 匿名式的JSON根节点验证 ###

一个JSON文本并不总是有一个命名好的根属性。这里有个验证这种JSON的例子：

```javascript
[1, 2, 3]
```

一个匿名的JSON根属性可以通过使用`$`或者空字符串作为路径来识别。举个例子，通过访问`http://localhost:8080/json`这个地址可以获得一个JSON文本，我们可以使用rest-assured验证：

```java
when().
     get("/json").
then().
     body("$", hasItems(1, 2, 3)); // An empty string "" would work as well
```

## 例2 - XML ##
XML可以一种通过简单的方式解析。假设一个POST请求`http://localhost:8080/greetXML`返回：
```xml
<greeting>
   <firstName>{params("firstName")}</firstName>
   <lastName>{params("lastName")}</lastName>
</greeting>
```
换言之，它在请求中返还了一个基于firstname和lastname请求参数的greeting节点。你可以通过rest-assured轻易地展现和解析这个例子：
```java
given().
         parameters("firstName", "John", "lastName", "Doe").
when().
         post("/greetXML").
then().
         body("greeting.firstName", equalTo("John")).
```
如果你想同时解析firstname和lastname你可以这样做：
```java
given().
         parameters("firstName", "John", "lastName", "Doe").
when().
         post("/greetXML").
then().
         body("greeting.firstName", equalTo("John")).
         body("greeting.lastName", equalTo("Doe"));
```
或者稍微简短些：
```java
with().parameters("firstName", "John", "lastName", "Doe").when().post("/greetXML").then().body("greeting.firstName", equalTo("John"), "greeting.lastName", equalTo("Doe"));
```

See [this](http://groovy-lang.org/processing-xml.html#_gpath) link for more info about the syntax (it follows Groovy's [GPath](http://groovy-lang.org/processing-xml.html#_gpath) syntax).
看[这里](http://groovy-lang.org/processing-xml.html#_gpath) 的链接获取有关语法的更多信息(它遵循 Groovy的 [GPath](http://groovy-lang.org/processing-xml.html#_gpath) 语法).

### XML 命名空间 ###
考虑到你需要使用[io.restassured.config.XmlConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/XmlConfig.html)声明一个命名空间。举个例子，有一个位于`http://localhost:8080`的资源`namespace-example`，返回如下的XML：
```xml
<foo xmlns:ns="http://localhost/">
  <bar>sudo </bar>
  <ns:bar>make me a sandwich!</ns:bar>
</foo>
```

你可以然后声明`http://localhost/`这个URI并且验证其响应：
```java
given().
        config(RestAssured.config().xmlConfig(xmlConfig().declareNamespace("test", "http://localhost/"))).
when().
         get("/namespace-example").
then().
         body("foo.bar.text()", equalTo("sudo make me a sandwich!")).
         body(":foo.:bar.text()", equalTo("sudo ")).
         body("foo.test:bar.text()", equalTo("make me a sandwich!"));
```

这个路径语法遵循Groovy的XmlSlurper语法。注意直到2.6.0的路径语法都*不*遵循Groovy的XmlSlurper语法。请看[release notes](https://github.com/rest-assured/rest-assured/wiki/ReleaseNotes26#non-backward-compatible-changes)可以获知2.6.0之前的版本语法是怎样的。

### XPath ###

你也可以使用x-path来解析XML响应。举个例子：

```java
given().parameters("firstName", "John", "lastName", "Doe").when().post("/greetXML").then().body(hasXPath("/greeting/firstName", containsString("Jo")));
```

或者

```java
given().parameters("firstName", "John", "lastName", "Doe").post("/greetXML").then().body(hasXPath("/greeting/firstName[text()='John']"));
```

To use namespaces in the XPath expression you need to enable them in the configuration, for example:
在XPath表达式中使用命名空间，你需要在配置中启用这些选项：
```java
given().
        config(RestAssured.config().xmlConfig(xmlConfig().with().namespaceAware(true))).
when().
         get("/package-db-xml").
then().
         body(hasXPath("/db:package-database", namespaceContext));
```

`namespaceContext` 是一个[javax.xml.namespace.NamespaceContext](http://docs.oracle.com/javase/7/docs/api/javax/xml/namespace/NamespaceContext.html)的实例 .

### Schema和DTD ###

XML response bodies can also be verified against an XML Schema (XSD) or DTD.
XML响应体也可以验证为一个XML Schema (XSD)或DTD.

#### XSD 例子

```java
get("/carRecords").then().assertThat().body(matchesXsd(xsd));
```

#### DTD 例子

```java
get("/videos").then().assertThat().body(matchesDtd(dtd));
```

</p>
<code>matchesXsd</code>和<code>matchesDtd</code>方法在Hamcrest matchers里，你可以从<a href="http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/matcher/RestAssuredMatchers.html">io.restassured.matcher.RestAssuredMatchers</a><br>
导入。</p>

## 例3 - 复杂的解析和验证 ##
这正是rest-assured闪光点所在！由于rest-assured实现了Groovy，它可以从Groovy集合的API的优点中获益。让我们从下面的Groovy例子中开始探索：

```groovy
def words = ['ant', 'buffalo', 'cat', 'dinosaur']
def wordsWithSizeGreaterThanFour = words.findAll { it.length() > 4 }
```

在第一行，我们简单地定义了一个包含一些单词的列表，不过第二行更加有趣。
这里我们检索了列表里的所有长度大于4的单词，通过一个叫做findAll的Groovy闭包。
这个闭包有一个内部变量`it`，代表着列表中当前的元素。
The result is a new list, `wordsWithSizeGreaterThanFour`, containing `buffalo` and `dinosaur`. 
结果是一个新的列表， `wordsWithSizeGreaterThanFour`,包含`buffalo` and `dinosaur`。

这里还有一些其它的有趣的方法，我们也可以使用在Groovy集合中：

* `find` – 找到第一个匹配闭包谓词（closure predicate）的元素
* `collect` – 收集在集合里的每个元素都调用的闭包返回值（collect the return value of calling a closure on each item in a collection）
* `sum` – 对集合里的元素进行求和
* `max`/`min` – 返回集合里的最大值/最小值

所以我们如何在使用rest-assured验证XML和JSON响应时利用这些优点？

### XML示例

比方说我们有个资源`http://localhost:8080/shopping`返回如下的XML：

```xml
<shopping>
      <category type="groceries">
        <item>Chocolate</item>
        <item>Coffee</item>
      </category>
      <category type="supplies">
        <item>Paper</item>
        <item quantity="4">Pens</item>
      </category>
      <category type="present">
        <item when="Aug 10">Kathryn's Birthday</item>
      </category>
</shopping>
```

又比如我们想写一个测试来检验类型为groceries的category节点有Chocolate和Coffee这两个项目。在rest-assured可以这样做：

```java
when().
       get("/shopping").
then().
       body("shopping.category.find { it.@type == 'groceries' }.item", hasItems("Chocolate", "Coffee"));
```

这里发生了什么事？首先使用XML路径`shopping.category`获取了所有categoriy的一个列表。在这个列表中我们又调用了一个方法，`find`，来返回有`type`这个属性且该属性值为`groceries`的单个category节点。

在这个category上我们接下来继续收集所有相关联的项目。

由于这里与category相关联的项目不止一个，所以会返回一个列表。接下来我们通过Hamcrest matcher的`hasItems`方法来解析它。

但是如果我们想取得一些项目但又不想进行断言验证该怎么办？你可以参考[XmlPath](http://static.javadoc.io/io.restassured/xml-path/3.0.1/io/restassured/path/xml/XmlPath.html):

```java
// Get the response body as a String
String response = get("/shopping").asString();
// And get the groceries from the response. "from" is statically imported from the XmlPath class
List<String> groceries = from(response).getList("shopping.category.find { it.@type == 'groceries' }.item");
```

If the list of groceries is the only thing you care about in the response body you can also use a [shortcut](#single-path):
如果groceries是你对这个响应里唯一的关注点，你也可以使用一个[捷径](#single-path):

```java
// Get the response body as a String
List<String> groceries = get("/shopping").path("shopping.category.find { it.@type == 'groceries' }.item");
```

#### 深度优先搜索

实际上之前的例子我们还可以继续简化：

```java
when().
       get("/shopping").
then().
       body("**.find { it.@type == 'groceries' }", hasItems("Chocolate", "Coffee"));
```

`**`是一种在XML文件中做深度优先搜索的捷径。

我们搜索第一个`type`属性值等于"groceries"的节点。注意我们没有在"item"这个XML路径结束。

原因是在category节点返回一个列表的项目值时，自动调用了`toString()`这个方法（译者注：这两句有啥因果关系我没搞懂）。

### JSON示例

假设`http://localhost:8080/store`返回如下的JSON：

```javascript
{  
   "store":{  
      "book":[  
         {  
            "author":"Nigel Rees",
            "category":"reference",
            "price":8.95,
            "title":"Sayings of the Century"
         },
         {  
            "author":"Evelyn Waugh",
            "category":"fiction",
            "price":12.99,
            "title":"Sword of Honour"
         },
         {  
            "author":"Herman Melville",
            "category":"fiction",
            "isbn":"0-553-21311-3",
            "price":8.99,
            "title":"Moby Dick"
         },
         {  
            "author":"J. R. R. Tolkien",
            "category":"fiction",
            "isbn":"0-395-19395-8",
            "price":22.99,
            "title":"The Lord of the Rings"
         }
      ]
   }
}
```

#### 例1
在本例中我们发起一个请求"/store"，并且做了一个断言：搜集满足price字段值小于10的所有book数组里的title字段，得到了"Sayings of the Century"和"Moby Dick"这两个结果：

```java
when().
       get("/store").
then().
       body("store.book.findAll { it.price < 10 }.title", hasItems("Sayings of the Century", "Moby Dick"));
```

就像上面XML的例子，我们使用闭包获取所有price字段值低于10的book数组，并且返回相应的title字段值集合。

然后使用`hasItems`这个匹配器来断言得到我们预期的结果。使用[JsonPath](http://static.javadoc.io/io.restassured/json-path/3.0.1/io/restassured/path/json/JsonPath.html) 我们可以用下面的方法替代：

```java
// Get the response body as a String
String response = get("/store").asString();
// And get all books with price < 10 from the response. "from" is statically imported from the JsonPath class
List<String> bookTitles = from(response).getList("store.book.findAll { it.price < 10 }.title");
```

#### 例2
考虑下该如何断言所有author字段值长度总和是否大于50的结果。

这是个挺难以回答的问题，这也正展示了闭包和Groovy集合的强大之处。在rest-assured里可以：
 
 ```java
 when().
        get("/store");
 then().
        body("store.book.author.collect { it.length() }.sum()", greaterThan(50));
```

首先我们通过(`store.book.author`)得到了所有的author字段值，然后使用闭包里的方法`{ it.length() }`解析这个集合。

它所做的是对列表里的每一个author字段执行一次`length()`方法，然后返回一个新的列表。在这个列表中，我们再调用`sum()`方法来求得字符长度的总和。

最终的结果是53，并且我们使用`greaterThan`匹配器的断言结果是大于50 。
But it's actually possible to simplify this even further. Consider the "[words](#example-3---complex-parsing-and-validation)" example again:
但是实际上可以继续简化这种写法。可以再次参考"[words](#例3---复杂的解析)"这个例子


```groovy
def words = ['ant', 'buffalo', 'cat', 'dinosaur']
```

Groovy有一个便利的方法可以遍历列表中的所有元素，使用`*`来调用。举个例子：


```groovy
def words = ['ant', 'buffalo', 'cat', 'dinosaur']
assert [3, 6, 3, 8] == words*.length()
```

Groovy返回了一个新的包含words中每个字段字符长度的列表。我们也可以把rest-assured中的这个语法用在author列表中：

```java
when().
       get("/store");
then().
       body("store.book.author*.length().sum()", greaterThan(50)).
```

当然我们可以使用[JsonPath](http://static.javadoc.io/io.restassured/json-path/3.0.1/io/restassured/path/json/JsonPath.html)来获取这个结果：

```java
// Get the response body as a string
String response = get("/store").asString();
// Get the sum of all author length's as an int. "from" is again statically imported from the JsonPath class
int sumOfAllAuthorLengths = from(response).getInt("store.book.author*.length().sum()");
// We can also assert that the sum is equal to 53 as expected.
assertThat(sumOfAllAuthorLengths, is(53));
```

## 其它例子 ##
Micha Kops曾写过一篇很优秀的博客，里面包含大量示例（包括可检出的代码）。你可以[由此试读](http://www.hascode.com/2011/10/testing-restful-web-services-made-easy-using-the-rest-assured-framework/)。

[Bas Dijkstra](https://www.linkedin.com/in/basdijkstra)也开展过不少关于rest-assured的开源研究和资源。你可以[由此阅读更多](http://www.ontestautomation.com/open-sourcing-my-workshop-an-experiment/)，如果你想试用或者作出贡献，[他的github仓库](https://github.com/basdijkstra/workshops/)里有些可以尝试的练习题。


## 关注于floats和doubles ##
浮点型数字必须和Java的基本类型"float"区分开。举个例子，如果我们看下面的JSON对象：


```javascript
{

    "price":12.12 

}
```

如下的测试将会失败，因为我们在拿一个"double"在比较，而不是"float"：

```java
get("/price").then().assertThat().body("price", equalTo(12.12));
```

想用"float"比较的话写法应该是：

```java
get("/price").then().assertThat().body("price", equalTo(12.12f));
```

## 语法糖 ##
当阅读rest-assured的博客时，你也许会看到许多使用"given / expect / when"语法的例子，举个例子：
```java
given().
        param("x", "y").
expect().
        body("lotto.lottoId", equalTo(5)).
when().
        get("/lotto");
```

这是一种“遗留语法”，这实际上是rest-assured 1.x.版本用来写测试用例的方式。然而这种运作方式令许多用户迷惑甚至恼怒。这是因为一开始没有把"given / when / then"作为主要的技术来使用。所以rest-assured得2.0版本之前差不多不支持这种类似BDD-like测试的标准用法。"given / expect / when"在2.0仍然可用但是"given / when / then"可读性更强所以在测试用例中更为推荐。然而使用"given / expect / when"还有一个好处，就是所有的期望中的错误可以在同时展示出来，这是新语法做不到的（自从预期结果放在了最后面）。这意味着如果你有多个预期结果想要检验你可以：

```java
given().
        param("x", "y").
expect().
        statusCode(400).
        body("lotto.lottoId", equalTo(6)).
when().
        get("/lotto");
```

rest-assured将同时报告状态码预期和响应体预期结果都是错的。将这些用新语法重写：

```java
given().
        param("x", "y").
when().
        get("/lotto").
then().
        statusCode(400).
        body("lotto.lottoId", equalTo(6));
```

将会仅仅报告首个预期/断言失败的内容（比如预期状态码是400实际是200），第二个断言将不执行。你将不得不重新运行这个用例以期获取到第二个断言的结果。

### 语法糖 ###
rest-assured中另一件值得注意的是，有些语法仅仅存在于语法糖中，举个例子，"and"在一行代码中使用可以增强可读性。

```java
given().param("x", "y").and().header("z", "w").when().get("/something").then().assertThat().statusCode(200).and().body("x.y", equalTo("z"));
```

这等价于:

```java
given().
        param("x", "y").
        header("z", "w").
when().
        get("/something").
then().
        statusCode(200).
        body("x.y", equalTo("z"));
```

# 获取响应信息 #
你也可以获得响应的内容。比方说你想通过发起一个get请求"/lotto"并获取其响应内容。你可以以多种方式：
```java
InputStream stream = get("/lotto").asInputStream(); // Don't forget to close this one when you're done
byte[] byteArray = get("/lotto").asByteArray();
String json = get("/lotto").asString();
```

## 从验证过的响应信息中提取值 ##
你可以从响应信息中提取值，或者使用`extract`方法仅仅返回response本身的一个实例。如何你想获取响应里的值，并将其作为接下来的请求内容，这会很有用。下面是一个叫做`title`的资源返回的JSON数据：
```javascript
 {
     "title" : "My Title",
      "_links": {
              "self": { "href": "/title" },
              "next": { "href": "/title?page=2" }
           }
 }
```

你想验证内容类型是JSON格式且标题是`My Title`，但是还想要从中提取next的值并用来发起请求，下面是使用方法：

```java
String nextTitleLink =
given().
        param("param_name", "param_value").
when().
        get("/title").
then().
        contentType(JSON).
        body("title", equalTo("My Title")).
extract().
        path("_links.next.href");

get(nextTitleLink). ..
```

如果你想提取多个值，也可以考虑返回整个响应体：
```java
Response response = 
given().
        param("param_name", "param_value").
when().
        get("/title").
then().
        contentType(JSON).
        body("title", equalTo("My Title")).
extract().
        response(); 

String nextTitleLink = response.path("_links.next.href");
String headerValue = response.header("headerName");
```

## JSON (使用 JsonPath) ##
一旦我们取得了响应体，我们可以使用[JsonPath](http://static.javadoc.io/io.restassured/json-path/3.0.1/io/restassured/path/json/JsonPath.html)来提取相应的数据：

```java
int lottoId = from(json).getInt("lotto.lottoId");
List<Integer> winnerIds = from(json).get("lotto.winners.winnerId");
```

或者更高效一些:
```java
JsonPath jsonPath = new JsonPath(json).setRoot("lotto");
int lottoId = jsonPath.getInt("lottoId");
List<Integer> winnerIds = jsonPath.get("winners.winnderId");
```

Note that you can use `JsonPath` standalone without depending on REST Assured, see [getting started guide](GettingStarted) for more info on this.
注意这里我们独立地使用了`JsonPath`，而没有依赖rest-assured本身的功能，看[getting started guide](GettingStarted) 获取更多信息。

### JsonPath 配置 ###
你可以为JsonPath配置反序列化对象（object de-serializers），举个例子：
```java
JsonPath jsonPath = new JsonPath(SOME_JSON).using(new JsonPathConfig("UTF-8"));
```

也可以静态配置好JsonPath，这样所有的JsonPath实例都会共享这个配置：

```java
JsonPath.config = new JsonPathConfig("UTF-8");
```

更多JsonPath的内容参照[这篇博客](http://www.jayway.com/2013/04/12/whats-new-in-rest-assured-1-8/)。

注意这里的JsonPath基于<a href='http://groovy-lang.org/processing-xml.html#_gpath'>Groovy的GPath</a>，不要和<a href='https://github.com/jayway/JsonPath'>Jayway</a>的搞混了。

## XML (使用XmlPath) ##
你也可以使用[XmlPath](http://static.javadoc.io/io.restassured/xml-path/3.0.1/io/restassured/path/xml/XmlPath.html)相应的功能：

```java
String xml = post("/greetXML?firstName=John&lastName=Doe").andReturn().asString();
// Now use XmlPath to get the first and last name
String firstName = from(xml).get("greeting.firstName");
String lastName = from(xml).get("greeting.firstName");

// or a bit more efficiently:
XmlPath xmlPath = new XmlPath(xml).setRoot("greeting");
String firstName = xmlPath.get("firstName");
String lastName = xmlPath.get("lastName");
```

注意你可以独立于rest-assured，单独使用`XmlPath`的功能，更多信息参见[getting started guide](GettingStarted)。

### XmlPath配置 ###
你可以配置XmlPath的对象反序列化器和字符编码，举个例子：
```java
XmlPath xmlPath = new XmlPath(SOME_XML).using(new XmlPathConfig("UTF-8"));
```

也可以静态地配置XmlPath，使得所有的实例都能共享这套配置：

```java
XmlPath.config = new XmlPathConfig("UTF-8");
```

更多关于XmlPath的信息参阅[这篇博客](http://www.jayway.com/2013/04/12/whats-new-in-rest-assured-1-8/)。

## 单独路径 ##
如果你只是想发起一个请求并返回一个单独路径下的值，你可以使用一个捷径：
```java
int lottoId = get("/lotto").path("lotto.lottoid");
```

rest-assured会基于响应体的content-type自动决定是使用JsonPath还是XmlPath。如果这个类型在rest-assured没有被定义，它将会自动到[default parser](#default-parser)中查找。你可以自行（代码指定）决定使用哪种，比如：

```java
String firstName = post("/greetXML?firstName=John&lastName=Doe").andReturn().xmlPath().getString("firstName");
```

`xmlPath`, `jsonPath`和`htmlPath`都是可选项。

## Headers, cookies, status etc ##

你也可以获取 headers, cookies, 状态行 ， 状态码:

```java
Response response = get("/lotto");

// 获取所有 headers 信息
Headers allHeaders = response.getHeaders();

// 获取单个 header 信息
String headerName = response.getHeader("headerName");

// 获取所有 cookie 键值对
Map<String, String> allCookies = response.getCookies();

// 获取单个 cookie 信息
String cookieValue = response.getCookie("cookieName");

// 获取状态行信息
String statusLine = response.getStatusLine();

// 获取状态码信息
int statusCode = response.getStatusCode();
```

## 多个 headers 和 cookies ##
header 和 cookie 可以包含同名的多个值。

### 多个 headers ###

要获取header的所有值，您需要首先从[Response](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/response/Response.html)对象中获取[Headers](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/http/Headers.html) 对象。您需要首先从Response对象中获取Headers对象。您可以使用Headers.getValues（
）方法返回一个具有所有header值的List列表。


### 多个 cookies ###

要获取cookie的所有值，您需要首先从[Response](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/response/Response.html)对象中获取[Cookie](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/http/Cookies.html)对象。您可以使用Cookie.getValues（）方法获取所有值，该方法返回包含所有Cookie值的List列表。


## 详细的 Cookies 信息##

如果您需要获取Cookie的路径或过期日期等详细信息，您需要从REST Assured获取一个[detailed cookie](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/http/Cookie.html)。您可以使用[Response.getDetailedCookie(java.lang.String)](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/response/ResponseOptions.html#getDetailedCookie-java.lang.String-) 方法获取单个Cookie，包括与给定名称相关联的所有属性。

您还可以使用[Response.getDetailedCookies()](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/response/ResponseOptions.html#getDetailedCookies--)方法获取所有详细的响应[cookies](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/http/Cookies.html)。

# 设置请求数据 #

除了指定请求参数，您还可以指定headers，Cookie，正文和Content Type。

## 调用HTTP资源 ##

您通常通过调用[request specification](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/specification/RequestSpecification.html)中的“HTTP方法”执行请求。例如：

```java
when().get("/x"). ..;
```
其中`get`是HTTP请求方法。

从REST Assured 3.0.0开始，您可以通过使用该方法为请求使用任何HTTP动词。

```java
when().
       request("CONNECT", "/somewhere").
then().
       statusCode(200);
```

这将向服务器发送“连接”请求。


## 参数 ##
通常你可以这样指定参数：


```java
given().
       param("param1", "value1").
       param("param2", "value2").
when().
       get("/something");
```

REST Assured将自动尝试基于HTTP方法确定哪个参数类型（即查询或表单参数）。在GET的情况下，查询参数将被自动使用，在POST的情况下将使用表单参数。在某些情况下，重要的是在PUT或POST中分离表单和查询参数。你可以这样使用：

```java
given().
       formParam("formParamName", "value1").
       queryParam("queryParamName", "value2").
when().
       post("/something");
```

参数也可以url上进行设置：

```java
..when().get("/name?firstName=John&lastName=Doe");
```

参数如果上传的是文件，字节数组，输入流或文本的可以参照[Multi-part form data](#multi-part-form-data)部分


### 多值参数 ###

多值参数是每个参数名称具有多于一个值的参数（即，每个名称的值的列表）。您可以使用var-args指定这些值：

```java
given().param("myList", "value1", "value2"). .. 
```

或者使用 list 列表：

```java
List<String> values = new ArrayList<String>();
values.add("value1");
values.add("value2");

given().param("myList", values). .. 
```

### 无值参数 ###

您还可以指定一个没有值的请求或表单参数：

```java
given().param("paramName"). ..
```

### 路径参数 ###

您还可以在请求中指定所谓的路径参数，例如
```java
post("/reserve/{hotelId}/{roomNumber}", "My Hotel", 23);
```
这些种类的路径参数在REST Assured中称为“未命名路径参数”，因为它们是基于索引的（`hotelId`将等于“My Hotel”，因为它是第一个占位符）。

您还可以使用命名路径参数：
```java
given().
        pathParam("hotelId", "My Hotel").
        pathParam("roomNumber", 23).
when(). 
        post("/reserve/{hotelId}/{roomNumber}").
then().
         ..
```


路径参数使得更容易读取请求路径以及使请求路径能够在具有不同参数值的许多测试中容易地重复使用。

从版本2.8.0开始，您可以混合未命名和命名的路径参数：

```java
given().
        pathParam("hotelId", "My Hotel").        
when(). 
        post("/reserve/{hotelId}/{roomNumber}", 23).
then().
         ..
```
这里 `roomNumber` 的值`My Hotel`将被替换为 `23`.


注意，指定太少或太多的参数将导致错误消息。对于高级用例，您可以从[filter]（＃filters）添加，更改，删除（甚至冗余的路径参数）。


## Cookies ##

通常模式下，您可以通过以下方法指定Cookie：
```java
given().cookie("username", "John").when().get("/cookie").then().body(equalTo("username"));
```
也可以像这样给cookie指定多个值：

```java
given().cookie("cookieName", "value1", "value2"). ..
```

这将创建两个cookie，cookieName = value1和cookieName = value2。

您还可以使用以下方式指定详细的Cookie：

```java
Cookie someCookie = new Cookie.Builder("some_cookie", "some_value").setSecured(true).setComment("some comment").build();
given().cookie(someCookie).when().get("/cookie").then().assertThat().body(equalTo("x"));
```

或同时指定cookies：

```java
Cookie cookie1 = Cookie.Builder("username", "John").setComment("comment 1").build();
Cookie cookie2 = Cookie.Builder("token", 1234).setComment("comment 2").build();
Cookies cookies = new Cookies(cookie1, cookie2);
given().cookies(cookies).when().get("/cookie").then().body(equalTo("username, token"));
```

## Headers ##
```java
given().header("MyHeader", "Something").and(). ..
given().headers("MyHeader", "Something", "MyOtherHeader", "SomethingElse").and(). ..
```

也可以给一个headers指定多个值：
```java
given().header("headerName", "value1", "value2"). ..
```

这将创建两个header,headerName = value1和headerName = value2

#### Header 合并/覆盖 ####

默认情况下，header合并可以这样：

```java
given().header("x", "1").header("x", "2"). ..
```


请求将包含两个标头，“x：1”和“x：2”。您可以在[HeaderConfig]（http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/HeaderConfig.html）的基础上进行更改。例如：

```java
given().
        config(RestAssuredConfig.config().headerConfig(headerConfig().overwriteHeadersWithName("x"))).
        header("x", "1").
        header("x", "2").
when().
        get("/something").
...
```
这意味着只有header “x = 2”被发送到服务器

## Content Type ##
```java
given().contentType(ContentType.TEXT). ..
given().contentType("application/json"). ..
```


## 请求正文 ##
```java
given().body("some body"). .. // Works for POST, PUT and DELETE requests
given().request().body("some body"). .. // More explicit (optional)
```

```java
given().body(new byte[]{42}). .. // Works for POST, PUT and DELETE
given().request().body(new byte[]{42}). .. // More explicit (optional)
```


您还可以将Java对象序列化为JSON或XML。点击[这里](#serialization)了解详情。


# 验证响应数据 #

您还可以验证状态码，状态行，Cookie，headers，内容类型和正文。

## 响应体 ##

请参阅使用示例，例如[JSON](#example-1---json) 或 [XML](#example-2---xml).

您还可以将响应正文映射到Java对象，单击[这里](#deserialization) 了解详细信息。


## Cookies ##
```java
get("/x").then().assertThat().cookie("cookieName", "cookieValue"). ..
get("/x").then().assertThat().cookies("cookieName1", "cookieValue1", "cookieName2", "cookieValue2"). ..
get("/x").then().assertThat().cookies("cookieName1", "cookieValue1", "cookieName2", containsString("Value2")). ..
```

## 状态码、状态行 ##
```java
get("/x").then().assertThat().statusCode(200). ..
get("/x").then().assertThat().statusLine("something"). ..
get("/x").then().assertThat().statusLine(containsString("some")). ..
```


## Headers ##

```java
get("/x").then().assertThat().header("headerName", "headerValue"). ..
get("/x").then().assertThat().headers("headerName1", "headerValue1", "headerName2", "headerValue2"). ..
get("/x").then().assertThat().headers("headerName1", "headerValue1", "headerName2", containsString("Value2")). ..
```

还可以在验证头时使用映射函数。 例如，假设您要验证“Content-Length”头部小于1000.然后，您可以使用映射函数首先将头值转换为int，然后在使用Hamcrest验证前使用“整数” 匹配器：
```java
get("/something").then().assertThat().header("Content-Length", Integer::parseInt, lessThan(1000));
```

## Content-Type ##
```java
get("/x").then().assertThat().contentType(ContentType.JSON). ..
```


## 全部内容匹配 ##

```java
get("/x").then().assertThat().body(equalTo("something")). ..
```

## 使用响应来验证响应的其他部分 ##

您可以使用响应中的数据来验证响应的另一部分。 例如，考虑从服务x返回的以下JSON：
```javascript
{ "userId" : "some-id", "href" : "http://localhost:8080/some-id" }
```

您可能会注意到，“href”属性以“userId”属性的值结尾。 如果我们想验证这个，我们可以实现一个io.restassured.matcher.ResponseAwareMatcher，可以像这样使用：

```java
get("/x").then().body("href", new ResponseAwareMatcher<Response>() {
                                  public Matcher<?> matcher(Response response) {
                                          return equalTo("http://localhost:8080/" + response.path("userId"));
                                  }
                       });
```
如果你使用Java 8，你可以使用lambda表达式：

```java
get("/x").then().body("href", response -> equalTo("http://localhost:8080/" + response.path("userId"));
```

有一些预定义的匹配器，您可以使用在io.restassured.matcher.RestAssuredMatchers（或io.restassured.module.mockmvc.matcher.RestAssuredMockMvcMatchers如果使用spring-mock-mvc模块）中定义。 例如：
```java
get("/x").then().body("href", endsWithPath("userId"));
```
ResponseAwareMatchers也可以与另一个ResponseAwareMatcher或与Hamcrest Matcher组成。 例如：
```java
get("/x").then().body("href", and(startsWith("http:/localhost:8080/"), endsWithPath("userId")));
```

 `and` 方法是静态导入的method is statically imported from `io.restassured.matcher.ResponseAwareMatcherComposer`.

## 测量响应时间 ##

从 REST Assured  2.8.0开始支持测量响应时间，例如：

```java
long timeInMs = get("/lotto").time()
```

或使用特定时间单位：

```java
long timeInSeconds = get("/lotto").timeIn(SECONDS);

```
其中SECONDS只是一个标准的TimeUnit。 您还可以使用验证DSL验证：

```java
when().
      get("/lotto").
then().
      time(lessThan(2000L)); // Milliseconds
```

或

```java
when().
      get("/lotto").
then().
      time(lessThan(2L), SECONDS);
```

需要注意的是，您只能模糊地将这些测量与服务器请求处理时间相关联（因为响应时间将包括HTTP往返和REST Assured处理时间等）。


# 认证 #

REST assured还支持多种认证方案，例如OAuth，摘要，证书，表单和抢占式基本认证。 您可以为每个请求设置身份验证：

```java
given().auth().basic("username", "password"). ..
```

也可以为所有请求定义身份验证：

```java
RestAssured.authentication = basic("username", "password");
```
或者您也可以使用 [specification](#specification-re-use).

## 基本认证 ##

有两种类型的基本认证，抢占和“受质询的基本认证”。

### 抢占式基本认证 ###

服务器在某些情况下给出未授权响应之前发送基本认证凭证，从而减少进行附加连接的开销。 大多数情况下可以这么使用：

```java
given().auth().preemptive().basic("username", "password").when().get("/secured/hello").then().statusCode(200);
```

### 受质询的基本认证 ###

使用“受质询的基本认证”时，REST Assured将不提供凭据，除非服务器已明确要求。 这意味着REST Assured将向服务器发出一个附加请求，以便进行质询，然后再次处理相同的请求，但此时会在标头中设置基本凭据。

```java
given().auth().basic("username", "password").when().get("/secured/hello").then().statusCode(200);
```

## 摘要认证 ##

目前只支持受质询的摘要认证：


```java
given().auth().digest("username", "password").when().get("/secured"). ..
```

## 表单认证 ##

[表单认证](https://en.wikipedia.org/wiki/Form-based_authentication)在互联网上非常流行。 它通常与用户在网页上填写其凭据（用户名和密码），然后按某种类型的登录按钮相关联。 提供表单身份验证基础的一个非常简单的HTML页面可能如下所示

```html
<html>
  <head>
    <title>Login</title>
  </head>

  <body>
    <form action="j_spring_security_check" method="POST">
      <table>
        <tr><td>User:&nbsp;</td><td><input type='text' name='j_username'></td></tr>
        <tr><td>Password:</td><td><input type='password' name='j_password'></td></tr>
          <tr><td colspan='2'><input name="submit" type="submit"/></td></tr>
       </table>
        </form>
      </body>
 </html>
```

也就是说 服务器期望用户填写“j_username”和“j_password”输入字段，然后按“提交”登录。 使用REST Assured，您可以测试受表单身份验证保护的服务，如下所示：

```java
given().
        auth().form("John", "Doe").
when().
        get("/formAuth");
then().
        statusCode(200);
```
 在REST中使用此类表单身份验证时会发生什么会导致为了检索包含登录详细信息的网页而向服务器发出附加请求。 REST Assured将尝试解析此页面并查找两个输入字段（使用用户名和密码）以及表单操作URI。 这可能失败，取决于网页的复杂性。 更好的选择是在设置表单身份验证时提供这些详细信息。 在这种情况下，可以：

```java
given().
        auth().form("John", "Doe", new FormAuthConfig("/j_spring_security_check", "j_username", "j_password")).
when().
        get("/formAuth");
then().
        statusCode(200);
```
这样REST Assured不需要提出额外的请求并解析网页。 还有一个预定义的FormAuthConfig称为`springSecurity`，如果你使用默认的Spring Security属性，你可以使用它：
```java
given().
        auth().form("John", "Doe", FormAuthConfig.springSecurity()).
when().
        get("/formAuth");
then().
        statusCode(200);
```

### CSRF ###
如今，服务器要求请求中提供一个[CSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery) token是常有的事了，这可以抵御多种类型的攻击。rest-assured支持解析并自动给服务器供应一个CSRF token。为此，rest-assured必须先发起一个追加请求来解析该网站（的部分内容）。

你可以通过下面的代码启用对CSRF的支持：

```java
given().
        auth().form("John", "Doe", formAuthConfig().withAutoDetectionOfCsrf()).
when().
        get("/formAuth");
then().
        statusCode(200);
```

现在rest-assured将会自动尝试侦测这个网站是否包含CSRF token机制。为了使rest-assured的暴力破解更加顺利，可能会提供一个CSRF域的名称（这里我们假设我们正在使用Spring的安全默认值，因此我们可以使用预定义的`springSecurity`表单认证配置）：

```java
given().
        auth().form("John", "Doe", springSecurity().withCsrfFieldName("_csrf")).
when().
        get("/formAuth");
then().
        statusCode(200);
```

我们至此已经告诉rest-assured去查找名为"_csrf"的CSRF域了（然而这虽然会比自动侦测更快，也更容易出错）。

默认情况下CSRF值将会作为一个请求参数，但是如果必要你也可以配置其放在请求的header中：

```java
given().
        auth().form("John", "Doe", springSecurity().withCsrfFieldName("_csrf").sendCsrfTokenAsHeader()).
when().
        get("/formAuth");
then().
        statusCode(200);
```

## OAuth ##

为了使用OAuth1和OAuth2（关于查询/请求参数签名方面的机制），您需要添加[Scribe](https://github.com/fernandezpablo85/scribe-java)到classpath中（如果你正在使用2.1.0或者更早之前版本的rest-assured，请参考[旧版指南](https://github.com/rest-assured/rest-assured/wiki/Usage_Legacy#OAuth)）。如果是maven请添加以下的依赖：
```xml
<dependency>
            <groupId>org.scribe</groupId>
            <artifactId>scribe</artifactId>
            <version>1.3.7</version>
            <scope>test</scope>
</dependency>
```

如果您没有使用maven，可以[下载](https://github.com/fernandezpablo85/scribe-java/releases)一个Scribe发行包并把它发在classpath下。

### OAuth 1 ###
OAuth1要求[Scribe](#oauth)在classpath中。为使用auth1的认证您可以：
```java
given().auth().oauth(..). ..
```

### OAuth 2 ###
自从2.5.0版本您可以依赖于[Scribe](#oauth)使用OAuth2的认证：
```java
given().auth().oauth2(accessToken). ..
```
这将会把OAuth2的`accessToken`放入header中。想要更加显式的操作可以：
```java
given().auth().preemptive().oauth2(accessToken). ..
```
这里之所以存在`given().auth().oauth2(..)`这种语法是为了向后兼容(做的是相同的事情)。如果你需要在请求参数中提供一个OAuth2 token，您需要把[Scribe](#oauth)放在classpath下，接下来：
```java
given().auth().oauth2(accessToken, OAuthSignature.QUERY_STRING). ..
```

## 自定义身份验证 ##
rest-assured允许您创建一个自定义的身份验证。你可以通过实现`io.restassured.spi.AuthFilter`接口，并作为一个拦截器。举个例子假设您的安全机制，是由两个header值相加然后组成一个新的叫做"AUTH"的header（当然这并不安全）。然后您可以这样做（Java 8的语法）：
```java
given().
        filter((requestSpec, responseSpec, ctx) -> {
            String header1 = requestSpec.getHeaders().getValue("header1");
            String header2 = requestSpec.getHeaders().getValue("header2");
            requestSpec.header("AUTH", header1 + header2);
            return ctx.next(requestSpec, responseSpec);
        }).
when().
        get("/customAuth").
then().
  statusCode(200);
```

使用`AuthFilter`而不是`Filter`的原因是，当我们执行`given().auth().none(). ..`类似这样的操作时`AuthFilters`会被自动移除。

# Multi-part 表单数据 #
通常我们在向服务器传输大容量的数据时（译者注：比如文件）会使用multipart表单数据技术。rest-assured提供了一种`multiPart`方法来辨别这究竟是文件、二进制序列、输入流还是上传的文本。表单中上传一个文件可以这样：

```java
given().
        multiPart(new File("/path/to/file")).
when().
        post("/upload");
```

它将会假设有一个control叫做"file"。在HTML中这意味着input标签的属性值为file。为了解释得更清楚请看下面的HTML表单：

```html
<form id="uploadForm" action="/upload" method="post" enctype="multipart/form-data">
        <input type="file" name="file" size="40">
        <input type=submit value="Upload!">
</form>
```

在这个例子中control的名字就是一个属性名为file的input标签。如果您使用的control名不是这个，需要指定：

```java
given().
        multiPart("controlName", new File("/path/to/file")).
when().
        post("/upload");
```

在同一个请求中提供多个"multi-parts"事务也是可能的：

```java
byte[] someData = ..
given().
        multiPart("controlName1", new File("/path/to/file")).
        multiPart("controlName2", "my_file_name.txt", someData).
        multiPart("controlName3", someJavaObject, "application/json").
when().
        post("/upload");
```

更多高级使用方法可以使用[MultiPartSpecBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/builder/MultiPartSpecBuilder.html)。举个例子：

```java
Greeting greeting = new Greeting();
greeting.setFirstName("John");
greeting.setLastName("Doe");

given().
        multiPart(new MultiPartSpecBuilder(greeting, ObjectMapperType.JACKSON_2)
                .fileName("greeting.json")
                .controlName("text")
                .mimeType("application/vnd.custom+json").build()).
when().
        post("/multipart/json").
then().
        statusCode(200);
```
你可以通过使用[MultiPartConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/MultiPartConfig.html)指定默认的control名和文件名。举个例子：

```java
given().config(config().multiPartConfig(multiPartConfig().defaultControlName("something-else"))). ..
```

这就会默认把control名配置为"something-else"而不是"file"。

其它用法请查阅 [这篇博客](http://blog.jayway.com/2011/09/15/multipart-form-data-file-uploading-made-simple-with-rest-assured/)。

# 对象映射 #
rest-assured支持从JSON和XML中映射Java对象。映射JSON需要classpath中有Jackson或者Gson才能使用，XML则需要JAXB。

## 序列号 ##
假设我们又下面的Java对象：

```java
public class Message {
    private String message;

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
```

您需要将这个对象序列化为JSON并发送到请求中。可以有多种方式：

### 基于Content-Type的序列化 ###

```java
Message message = new Message();
message.setMessage("My messagee");
given().
       contentType("application/json").
       body(message).
when().
      post("/message");
```

在这个例子里，由于请求中的content-type被设置为"application/json"，rest-assured也就会把对象序列化为JSON。rest-assured首先会在您的classpath中寻找Jackson，如果没有则使用Gson。如果您把请求中的content-type修改为"application/xml"，rest-assured将会使用JAXB把对象序列化为XML。如果没有指定content-type，rest-assured会按照以下的优先级进行序列化：

  1. 使用Jackson 2将对象序列化为JSON（Faster Jackson (databind)）
  1. 使用Jackson将对象序列化为JSON（databind）
  1. 使用Gson将对象序列化为JSON
  1. 使用JAXB将对象序列化为XML

rest-assured也关心content-type的字符集（charset）等等。

```java
Message message = new Message();
message.setMessage("My messagee");
given().
       contentType("application/json; charset=UTF-16").
       body(message).
when().
      post("/message");
```

您也可以把`Message`这个实例序列化为一个表单参数：
```java
Message message = new Message();
message.setMessage("My messagee");
given().
       contentType("application/json; charset=UTF-16").
       formParam("param1", message).
when().
      post("/message");
```


这个message对象将会被实例化为utf-16编码的JSON（如果有Jackson或者Gson）。


### 由HashMap创建JSON ###

你也可以提供一个Map，由此rest-assured创建一个JSON。
```java
Map<String, Object>  jsonAsMap = new HashMap<>();
jsonAsMap.put("firstName", "John");
jsonAsMap.put("lastName", "Doe");

given().
        contentType(JSON).
        body(jsonAsMap).
when().
        post("/somewhere").
then().
        statusCode(200);
```

这将会产生一个JSON数据（JSON payload）：

```javascript
{ "firstName" : "John", "lastName" : "Doe" }
```

### 显示使用序列化器（Using an Explicit Serializer） ###
如果您的classpath中同时有多个对象、或者不在意content-type的设置，可以显示地指定一个序列化器。

```java
Message message = new Message();
message.setMessage("My messagee");
given().
       body(message, ObjectMapperType.JAXB).
when().
      post("/message");
```

在这个例子中message对象将会被JAXB序列化为一个XML。

## 反序列化 ##
让我们再次假设我们又以下的Java对象：

```java
public class Message {
    private String message;

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
```

我们需要把响应体反序列化为一个Message对象。

### 基于Content-Type的反序列化 ###
假设服务端返回一个这样的JSON：
```javascript
{"message":"My message"}
```

将它反序列化为一个Message对象：
```java
Message message = get("/message").as(Message.class);
```

为此响应体的content-type必须是"application/json"（或者其它包含“json”的类型）。如果服务端返回：

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<message>
      <message>My message</message>
</message>
```

且content-type是"application/xml"，代码可以完全不用修改：
```java
Message message = get("/message").as(Message.class);
```

#### 自定义的Content-Type反序列化 ####
如果服务端返回一个自定义的content-type，假设是"application/something"，你仍然想使用rest-assured的对象映射的话，这有两种方法。你可以使用[显式指定](http://code.google.com/p/rest-assured/wiki/Usage#Using_an_Explicit_Deserializer)的方法或者为自定义的content-type注册一个解析器：

```java
Message message = expect().parser("application/something", Parser.XML).when().get("/message").as(Message.class);
```

或

```java
Message message = expect().defaultParser(Parser.XML).when().get("/message").as(Message.class);
```

你也可以注册一个默认解析器，或者静态式注册一个自定义的解析器，也可以使用[规格说明（specifications）](#specification-re-use)。

### 使用显示反序列化器 ###
如果您的classpath下同时有多个对象或者不在意响应体的content-type，你可以使用显示的反序列化器。

```java
Message message = get("/message").as(Message.class, ObjectMapperType.GSON);
```

## 配置 ##
您可以使用[ObjectMapperConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/ObjectMapperConfig.html)配置预定义的对象映射，并传递给[细节配置](#detailed-configuration)。举个例子，你可以将GSON的命名策略改为LowerCaseWithUnderscores（译者注：将大写字母改为小写字母并添加下划线）：

```java
RestAssured.config = RestAssuredConfig.config().objectMapperConfig(objectMapperConfig().gsonObjectMapperFactory(
                new GsonObjectMapperFactory() {
                    public Gson create(Class cls, String charset) {
                        return new GsonBuilder().setFieldNamingPolicy(LOWER_CASE_WITH_UNDERSCORES).create();
                    }
                }
        ));
```

这里为GSON、JAXB、Jackson和Faster Jackson预定义了对象映射的工厂。

## 自定义 ##
默认情况下rest-assured将会扫描classpath中各种各样的对象映射。如果您想要集成一种对象映射，默认是不支持的，如果您已经做好了封装，可以实现[io.restassured.mapper.ObjectMapper](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/mapper/ObjectMapper.html) 接口。告诉rest-assured使用您的对象映射或者将其作为body方法里的第二个参数：

```java
given().body(myJavaObject, myObjectMapper).when().post("..")
```

或者您可以静态式定义：
```java
RestAssured.config = RestAssuredConfig.config().objectMapperConfig(new ObjectMapperConfig(myObjectMapper));
```

更多例子参阅[这里](https://github.com/rest-assured/rest-assured/blob/master/examples/rest-assured-itest-java/src/test/java/io/restassured/itest/java/CustomObjectMappingITest.java)。

# Custom parsers #
REST Assured providers predefined parsers for e.g. HTML, XML and JSON. But you can parse other kinds of content by registering a predefined parser for unsupported content-types by using:
```java
RestAssured.registerParser(<content-type>, <parser>);
```
E.g. to register that mime-type 'application/vnd.uoml+xml' should be parsed using the XML parser do:
```java
RestAssured.registerParser("application/vnd.uoml+xml", Parser.XML);
```
You can also unregister a parser using:
```java
RestAssured.unregisterParser("application/vnd.uoml+xml");
```

Parsers can also be specified per "request":
```java
get(..).then().using().parser("application/vnd.uoml+xml", Parser.XML). ..;
```

and using a [response specification](sSpecification-re-use).

# Default parser #
Sometimes it's useful to specify a default parser, e.g. if the response doesn't contain a content-type at all:

```java
RestAssured.defaultParser = Parser.JSON;
```

You can also specify a default parser for a single request:
```java
get("/x").then().using().defaultParser(Parser.JSON). ..
```

or using a [response specification](#specification-re-use).

# Default values #
By default REST assured assumes host localhost and port 8080 when doing a request. If you want a different port you can do:
```java
given().port(80). ..
```
or simply:
```java
..when().get("http://myhost.org:80/doSomething");
```
You can also change the default base URI, base path, port and authentication scheme for all subsequent requests:
```java
RestAssured.baseURI = "http://myhost.org";
RestAssured.port = 80;
RestAssured.basePath = "/resource";
RestAssured.authentication = basic("username", "password");
RestAssured.rootPath = "x.y.z";
```
This means that a request like e.g. `get("/hello")` goes to: http://myhost.org:80/resource/hello with basic authentication credentials "username" and "password". See [rootPath](http://code.google.com/p/rest-assured/wiki/Usage#Root_path) for more info about setting the root paths. Other default values you can specify are:

```java
RestAssured.filters(..); // List of default filters
RestAssured.requestSpecification = .. // Default request specification
RestAssured.responseSpecification = .. // Default response specification
RestAssured.urlEncodingEnabled = .. // Specify if Rest Assured should URL encoding the parameters
RestAssured.defaultParser = .. // Specify a default parser for response bodies if no registered parser can handle data of the response content-type
RestAssured.registerParser(..) // Specify a parser for the given content-type
RestAssured.unregisterParser(..) // Unregister a parser for the given content-type
```

You can reset to the standard baseURI (localhost), basePath (empty), standard port (8080), standard root path (""), default authentication scheme (none) and url encoding enabled (true) using:
```java
RestAssured.reset();
```

# Specification Re-use #
Instead of having to duplicate response expectations and/or request parameters for different tests you can re-use an entire specification. To do this you define a specification using either the [RequestSpecBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/builder/RequestSpecBuilder.html) or [ResponseSpecBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/builder/ResponseSpecBuilder.html).

E.g. let's say you want to make sure that the expected status code is 200 and that the size of the JSON array "x.y" has size 2 in several tests you can define a ResponseSpecBuilder like this:

```java
ResponseSpecBuilder builder = new ResponseSpecBuilder();
builder.expectStatusCode(200);
builder.expectBody("x.y.size()", is(2));
ResponseSpecification responseSpec = builder.build();

// Now you can re-use the "responseSpec" in many different tests:
when().
       get("/something").
then().
       spec(responseSpec).
       body("x.y.z", equalTo("something"));
```

In this example the data defined in "responseSpec" is merged with the additional body expectation and all expectations must be fulfilled in order for the test to pass.

You can do the same thing if you need to re-use request data in different tests. E.g.
```java
RequestSpecBuilder builder = new RequestSpecBuilder();
builder.addParam("parameter1", "parameterValue");
builder.addHeader("header1", "headerValue");
RequestSpecification requestSpec = builder.build();
  
given().
        spec(requestSpec).
        param("parameter2", "paramValue").
when().
        get("/something").
then().
        body("x.y.z", equalTo("something"));        
```

Here the request's data is merged with the data in the "requestSpec" so the request will contain two parameters ("parameter1" and "parameter2") and one header ("header1").

# Filters #
A filter allows you to inspect and alter a request before it's actually committed and also inspect and [alter](#response-builder) the response before it's returned to the expectations. You can regard it as an "around advice" in AOP terms. Filters can be used to implement custom authentication schemes, session management, logging etc. To create a filter you need to implement the [io.restassured.filter.Filter](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/filter/Filter.html) interface. To use a filter you can do:

```java
given().filter(new MyFilter()). ..
```

There are a couple of filters provided by REST Assured that are ready to use:
  1. `io.restassured.filter.log.RequestLoggingFilter`: A filter that'll print the request specification details.
  1. `io.restassured.filter.log.ResponseLoggingFilter`: A filter that'll print the response details if the response matches a given status code.
  1. `io.restassured.filter.log.ErrorLoggingFilter`: A filter that'll print the response body if an error occurred (status code is between 400 and 500).

## Response Builder ##

If you need to change the [Response](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/response/Response.html) from a filter you can use the [ResponseBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/builder/ResponseBuilder.html) to create a new Response based on the original response. For example if you want to change the body of the original response to something else you can do:
```java
Response newResponse = new ResponseBuilder().clone(originalResponse).setBody("Something").build();
```

# Logging #
In many cases it can be useful to print the response and/or request details in order to help you create the correct expectations and send the correct requests. To do help you do this you can use one of the predefined [filters](#filters) supplied with REST Assured or you can use one of the shortcuts.

## Request Logging ##
Since version 1.5 REST Assured supports logging the _[request specification](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/specification/RequestSpecification.html)_ before it's sent to the server using the [RequestLoggingFilter](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/filter/log/RequestLoggingFilter.html). Note that the HTTP Builder and HTTP Client may add additional headers then what's printed in the log. The filter will _only_ log details specified in the request specification. I.e. you can NOT regard the details logged by the [RequestLoggingFilter](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/filter/log/RequestLoggingFilter.html) to be what's actually sent to the server. Also subsequent filters may alter the request _after_ the logging has taken place. If you need to log what's _actually_ sent on the wire refer to the [HTTP Client logging docs](http://hc.apache.org/httpcomponents-client-ga/logging.html) or use an external tool such [Wireshark](http://www.wireshark.org/). Examples:

```java
given().log().all(). .. // Log all request specification details including parameters, headers and body
given().log().params(). .. // Log only the parameters of the request
given().log().body(). .. // Log only the request body
given().log().headers(). .. // Log only the request headers
given().log().cookies(). .. // Log only the request cookies
given().log().method(). .. // Log only the request method
given().log().path(). .. // Log only the request path
```

## Response Logging ##
If you want to print the response body regardless of the status code you can do:

```java
get("/x").then().log().body() ..
```

This will print the response body regardless if an error occurred. If you're only interested in printing the response body if an error occur then you can use:

```java
get("/x").then().log().ifError(). .. 
```

You can also log all details in the response including status line, headers and cookies:

```java
get("/x").then().log().all(). .. 
```

as well as only status line, headers or cookies:
```java
get("/x").then().log().statusLine(). .. // Only log the status line
get("/x").then().log().headers(). .. // Only log the response headers
get("/x").then().log().cookies(). .. // Only log the response cookies
```

You can also configure to log the response only if the status code matches some value:
```java
get("/x").then().log().ifStatusCodeIsEqualTo(302). .. // Only log if the status code is equal to 302
get("/x").then().log().ifStatusCodeMatches(matcher). .. // Only log if the status code matches the supplied Hamcrest matcher
```

## Log if validation fails ##

Since REST Assured 2.3.1 you can log the request or response only if the validation fails. To log the request do:
```java
given().log().ifValidationFails(). ..
```

To log the response do:
```java
.. .then().log().ifValidationFails(). ..
```

It's also possible to enable this for both the request and the response at the same time using the [LogConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/LogConfig.html):

```java
given().config(RestAssured.config().logConfig(logConfig().enableLoggingOfRequestAndResponseIfValidationFails(HEADERS))). ..
```

This will log only the headers if validation fails.

There's also a shortcut for enabling logging of the request and response for all requests if validation fails:

```java
RestAssured.enableLoggingOfRequestAndResponseIfValidationFails();
```

# Root path #
To avoid duplicated paths in body expectations you can specify a root path. E.g. instead of writing:
```java
when().
         get("/something").
then().
         body("x.y.firstName", is(..)).
         body("x.y.lastName", is(..)).
         body("x.y.age", is(..)).
         body("x.y.gender", is(..));
```

you can use a root path and do:

```java
when().
        get("/something").
then().
         root("x.y"). // You can also use the "root" method
         body("firstName", is(..)).
         body("lastName", is(..)).
         body("age", is(..)).
         body("gender", is(..));
```
You can also set a default root path using:
```java
RestAssured.rootPath = "x.y";
```

In more advanced use cases it may also be useful to append additional root arguments to existing root arguments. To do this you can use the `appendRoot` method, for example:
```java
when().
         get("/jsonStore").
then().
         root("store.%s", withArgs("book")).
         body("category.size()", equalTo(4)).
         appendRoot("%s.%s", withArgs("author", "size()")).
         body(withNoArgs(), equalTo(4));
```

It's also possible to detach a root. For example:
```java
when().
         get("/jsonStore").
then().
         root("store.category").
         body("size()", equalTo(4)).
         detachRoot("category").
         body("size()", equalTo(1));
```

# Path arguments #
Path arguments are useful in situations where you have e.g. pre-defined variables that constitutes the path. For example
```java
String someSubPath = "else";
int index = 1;
get("/x").then().body("something.%s[%d]", withArgs(someSubPath, index), equalTo("some value")). ..
```

will expect that the body path "`something.else[0]`" is equal to "some value".

Another usage is if you have complex [root paths](http://code.google.com/p/rest-assured/wiki/Usage#Root_path) and don't wish to duplicate the path for small variations:
```java
when().
       get("/x").
then().
       root("filters.filterConfig[%d].filterConfigGroups.find { it.name == 'GroupName' }.includes").
       body(withArgs(0), hasItem("first")).
       body(withArgs(1), hasItem("second")).
       ..
```

The path arguments follows the standard [formatting syntax](http://download.oracle.com/javase/1,5.0/docs/api/java/util/Formatter.html#syntax) of Java.

Note that the `withArgs` method can be statically imported from the [io.restassured.RestAssured](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/RestAssured.html) class.

Sometimes it's also useful to validate a body without any additional arguments when all arguments have already been specified in the root path. This is where `withNoArgs` come into play. For example:
```java
when().
         get("/jsonStore").
then().
         root("store.%s", withArgs("book")).
         body("category.size()", equalTo(4)).
         appendRoot("%s.%s", withArgs("author", "size()")).
         body(withNoArgs(), equalTo(4));
```

# Session support #
REST Assured provides a simplified way for managing sessions. You can define a session id value in the DSL:
```java
given().sessionId("1234"). .. 
```

This is actually just a short-cut for:
```java
given().cookie("JSESSIONID", "1234"). .. 
```

You can also specify a default `sessionId` that'll be supplied with all subsequent requests:
```java
RestAssured.sessionId = "1234";
```

By default the session id name is `JSESSIONID` but you can change it using the [SessionConfig](#session-config):
```java
RestAssured.config = RestAssured.config().sessionConfig(new SessionConfig().sessionIdName("phpsessionid"));
```

You can also specify a sessionId using the `RequestSpecBuilder` and reuse it in many tests:
```java
RequestSpecBuilder spec = new RequestSpecBuilder().setSessionId("value1").build();
   
// Make the first request with session id equal to value1
given().spec(spec). .. 
// Make the second request with session id equal to value1
given().spec(spec). .. 
```

It's also possible to get the session id from the response object:
```java
String sessionId = get("/something").sessionId();
```

## Session Filter ##
As of version 2.0.0 you can use a [session filter](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/filter/session/SessionFilter.html) to automatically capture and apply the session, for example:
```java
SessionFilter sessionFilter = new SessionFilter();

given().
          auth().form("John", "Doe").
          filter(sessionFilter).
when().
          get("/formAuth").
then().
          statusCode(200);


given().
          filter(sessionFilter). // Reuse the same session filter instance to automatically apply the session id from the previous response
when().
          get("/x").
then().
          statusCode(200);
```

To get session id caught by the `SessionFilter` you can do like this:
```java
String sessionId = sessionFilter.getSessionId();
```

# SSL #
In most situations SSL should just work out of the box thanks to the excellent work of HTTP Builder and HTTP Client. There are however some cases where you'll run into trouble. You may for example run into a SSLPeerUnverifiedException if the server is using an invalid certificate. The easiest way to workaround this is to use "relaxed HTTPs validation". For example:
```java
given().relaxedHTTPSValidation().when().get("https://some_server.com"). .. 
```
You can also define this statically for all requests:
```java
RestAssured.useRelaxedHTTPSValidation();
```
or in a [request specification](#specification-re-use).

This will assume an SSLContext protocol of  `SSL`. To change to another protocol use an overloaded versionen of  `relaxedHTTPSValidation`. For example:

```java
given().relaxedHTTPSValidation("TLS").when().get("https://some_server.com"). .. 
```

You can also be more fine-grained and create Java keystore file and use it with REST Assured. It's not too difficult, first follow the guide [here](https://github.com/jgritman/httpbuilder/wiki/SSL) and then use the keystore in Rest Assured like this:

```java
given().keystore("/pathToJksInClassPath", <password>). .. 
```

or you can specify it for every request:

```java
RestAssured.keystore("/pathToJksInClassPath", <password>);
```

You can also define a keystore in a re-usable [specification](http://code.google.com/p/rest-assured/wiki/Usage#Specification_Re-use).

If you already loaded a keystore with a password you can use it as a truststore:
```java
RestAssured.trustStore(keystore);
```

You can find a working example [here](https://github.com/rest-assured/rest-assured/blob/master/examples/rest-assured-itest-java/src/test/java/io/restassured/itest/java/SSLTest.java).

For more advanced SSL Configuration refer to the [SSL Configuration](#ssl-config) section.

## SSL invalid hostname ##
If the certificate is specifying an invalid hostname you don't need to create and import a keystore. As of version `2.2.0` you can do:
```java
RestAssured.config = RestAssured.config().sslConfig(sslConfig().allowAllHostnames());
```

to allow all hostnames for all requests or:

```java
given().config(RestAssured.config().sslConfig(sslConfig().allowAllHostnames()). .. ;
```
for a single request.

Note that if you use "relaxed HTTPs validation" then `allowAllHostnames` is activated by default.

# URL Encoding #
Usually you don't have to think about URL encoding since Rest Assured provides this automatically out of the box. In some cases though it may be useful to turn URL Encoding off. One reason may be that you already the have some parameters encoded before you supply them to Rest Assured. To prevent double URL encoding you need to tell Rest Assured to disable it's URL encoding. E.g.

```java
String response = given().urlEncodingEnabled(false).get("https://jira.atlassian.com:443/rest/api/2.0.alpha1/search?jql=project%20=%20BAM%20AND%20issuetype%20=%20Bug").asString();
..
```

or

```java
RestAssured.baseURI = "https://jira.atlassian.com";
RestAssured.port = 443;
RestAssured.urlEncodingEnabled = false;
final String query = "project%20=%20BAM%20AND%20issuetype%20=%20Bug";
String response = get("/rest/api/2.0.alpha1/search?jql={q}", query);
..
```

# 代理（proxy）配置 #

从版本2.3.2开始REST Assured可以更好地支持代理。 例如，如果你有一个代理在localhost端口8888你可以做：
```java
given().proxy("localhost", 8888). .. 
```
如果服务器在本地环境中运行，可以不指定主机名：
```java
given().proxy(8888). .. // Will assume localhost
```
要使用HTTPS，需要提供第三个参数（scheme）或使用`io.restassured.specification.ProxySpecification`。 例如：

```java
given().proxy(host("localhost").withScheme("https")). ..
```

其中host从io.restassured.specification.ProxySpecification静态导入。

从版本2.7.0开始，您还可以为代理指定抢占式基本身份验证。 例如：
  
```
given().proxy(auth("username", "password")).when() ..
```

其中`auth`是从[io.restassured.specification.ProxySpecification](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/specification/ProxySpecification.html)静态导入的，也可以将身份验证与不同的host相结合：
```java
given().proxy(host("http://myhost.org").withAuth("username", "password")). ..
```

## 静态代理配置 ##

可以为所有请求配置静态代理，例如：
```java
RestAssured.proxy("localhost", 8888);    
```

或者：

```java
RestAssured.proxy = host("localhost").withPort(8888);
```

## 请求规范代理配置 ##

您还可以创建请求规范并在其中指定代理：

```java
RequestSpecification specification = new RequestSpecBuilder().setProxy("localhost").build();
given().spec(specification). ..
```

# 详细配置 #

详细配置由[RestAssuredConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/RestAssuredConfig.html)实例提供，您可以使用它配置[HTTP Client](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/HttpClientConfig.html)的参数以及[重定向](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/RedirectConfig.html)，[日志](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/LogConfig.html)，[编码器](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/EncoderConfig.html)，[解码器](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/DecoderConfig.html)，[Session](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/SessionConfig.html)，[ObjectMapper](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/ObjectMapperConfig.html)，[Connection](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/ConnectionConfig.html)，[SSL](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/SSLConfig.html)和[ParamConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/ParamConfig.html) 设置。 例子：

特定请求:
```java
given().config(RestAssured.config().redirect(redirectConfig().followRedirects(false))). ..
```

或使用RequestSpecBuilder：
```java
RequestSpecification spec = new RequestSpecBuilder().setConfig(RestAssured.config().redirect(redirectConfig().followRedirects(false))).build();
```

或所有请求：
```java
RestAssured.config = config().redirect(redirectConfig().followRedirects(true).and().maxRedirects(0));
```
`config()` and `newConfig()` can be statically imported from `io.restassured.config.RestAssuredConfig`.

## 编码配置 ##

使用[EncoderConfig]（http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/EncoderConfig.html），可以指定默认的内容encoding charset（如果没有在content-type头中指定）和查询参数charset为所有请求。 如果未指定内容字符集，则使用ISO-8859-1，如果未指定查询参数字符集，则使用UTF-8。 用法示例：
```java
RestAssured.config = RestAssured.config().encoderConfig(encoderConfig().defaultContentCharset("US-ASCII"));
```

如果没有通过使用[EncoderConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/EncoderConfig.html)中的defaultCharsetForContentType方法为此内容类型显式定义字符集，那么还可以指定用于特定内容类型的编码器字符集。 例如：

```java
RestAssured.config = RestAssured.config(config().encoderConfig(encoderConfig().defaultCharsetForContentType("UTF-16", "application/xml")));
```

这将假设明确指定一个字符集的“application / xml”内容类型的UTF-16编码。 默认情况下，“application / json”被指定为使用“UTF-8”作为默认内容类型，这是由[RFC4627]（https://www.ietf.org/rfc/rfc4627.txt）指定的。

### 避免自动将字符集添加到content-type标头 ###

默认情况下，REST Assured会自动添加字符集标头。 要完全禁用这个，你可以这样配置`EncoderConfig`：

```java
RestAssured.config = RestAssured.config(config().encoderConfig(encoderConfig().appendDefaultContentCharsetToContentTypeIfUndefined(false));
```

## 解码配置 ##

使用[DecoderConfig]（http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/DecoderConfig.html），您可以将默认响应内容解码字符集设置为 所有响应。 如果您期望使用不同于ISO-8859-1的内容字符集（这是默认字符集），并且响应未在内容类型标头中定义字符集，那么这将非常有用。 用法示例：

```java
RestAssured.config = RestAssured.config().decoderConfig(decoderConfig().defaultContentCharset("UTF-8"));
```

您还可以使用“DecoderConfig”来指定要应用哪些内容解码器。 当你这样做时，“Accept-Encoding”头部将被自动添加到请求中，并且响应主体将被自动解码。 默认情况下，启用GZIP和DEFLATE解码器。 例如要删除GZIP解码但保留DEFLATE解码，您可以执行以下操作：
您还可以使用“DecoderConfig”来指定要应用哪些内容解码器。 当你这样做时，“Accept-Encoding”头部将被自动添加到请求中，并且响应主体将被自动解码。 默认情况下，启用GZIP和DEFLATE解码器。 例如要删除GZIP解码但保留DEFLATE解码，您可以执行以下操作：

```java
given().config(RestAssured.config().decoderConfig(decoderConfig().contentDecoders(DEFLATE))). ..
```

如果没有通过使用[DecoderConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/DecoderConfig.html)中的“defaultCharsetForContentType”方法为此内容类型明确定义字符集，则还可以指定要用于特定内容类型的解码器字符集。 例如：
  
```java
RestAssured.config = config(config().decoderConfig(decoderConfig().defaultCharsetForContentType("UTF-16", "application/xml")));
 ```

这将假设明确指定一个字符集的“application / xml”内容类型的UTF-16编码。 默认情况下，“application / json”使用“UTF-8”作为默认字符集，这是由[RFC4627]（https://www.ietf.org/rfc/rfc4627.txt）指定的。

## Session配置 ##

使用Session配置，您可以配置REST Assured使用的默认session ID名称。 默认session id名称是`JSESSIONID`，如果应用程序中的名称不同，并且您希望使用REST Assured的[会话支持]（＃Session_support），您只需更改它。 用法：

```java
RestAssured.config = RestAssured.config().sessionConfig(new SessionConfig().sessionIdName("phpsessionid"));
```

## 重定向 DSL ##

重定向配置可以使用DSL指定。 例如。

```java
given().redirects().max(12).and().redirects().follow(true).when(). .. 
```

## 网络连接配置 ##

让您配置REST Assured的连接设置。 例如，如果要在每个响应后强制关闭Apache HTTP Client连接。 如果您在响应中使用少量数据进行大量快速连续请求，则可能需要执行此操作。然而，如果你正在下载（尤其是大量的）分块，你绝不能每个响应后关闭连接的数据。 默认情况下，连接在每个响应后不关闭。

```java
RestAssured.config = RestAssured.config().connectionConfig(connectionConfig().closeIdleConnectionsAfterEachResponse());
```

## Json Config ##

[JsonPathConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/path/json/config/JsonPathConfig.html)允许您在REST Assured或 [JsonPath](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/path/json/JsonPath.html)使用时配置Json设置。 它让你配置如何处理JSON数字。

```java
RestAssured.config = RestAssured.config().jsonConfig(jsonConfig().numberReturnType(NumberReturnType.BIG_DECIMAL))
```

## HTTP Client 配置 ##

为REST Assured将在执行请求时使用的HTTP Client实例配置属性。 默认情况下，REST Assured会为每个“given”语句创建一个新的http Client实例。 要配置重用，请执行以下操作：

```java
RestAssured.config = RestAssured.config().httpClient(httpClientConfig().reuseHttpClientInstance());
```
您还可以使用`httpClientFactory`方法提供自定义HTTP Client实例，例如：

```java
RestAssured.config = RestAssured.config().httpClient(httpClientConfig().httpClientFactory(
         new HttpClientConfig.HttpClientFactory() {

            @Override
            public HttpClient createHttpClient() {
                return new SystemDefaultHttpClient();
            }
        }));
```

**注意，目前你需要提供一个`AbstractHttpClient`的实例.**

也可以配置默认参数等。

## SSL 配置 ##

[SSLConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/SSLConfig.html) 允许您指定更高级的SSL配置，如信任库，密钥库类型和主机名验证器。 例如：

```java
RestAssured.config = RestAssured.config().sslConfig(sslConfig().with().keystoreType(<type>).and().strictHostnames());
```

## Param Config ##
[ParamConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/ParamConfig.html) allows you to configure how different parameter types should be updated on "collision". By default all parameters are merged so if you do:
  
```java
given().queryParam("param1", "value1").queryParam("param1", "value2").when().get("/x"). ...
```
  
REST Assured will send a query string of `param1=value1&param1=value2`. This is not always what you want though so you can configure REST Assured to *replace* values instead:

```java
given().
        config(config().paramConfig(paramConfig().queryParamsUpdateStrategy(REPLACE))).
        queryParam("param1", "value1").
        queryParam("param1", "value2").
when().
        get("/x"). ..
```

REST Assured will now replace `param1` with `value2` (since it's written last) instead of merging them together. You can also configure the update strategy for each type of for all parameter types instead of doing it per individual basis:

```java
given().config(config().paramConfig(paramConfig().replaceAllParameters())). ..
```

This is also supported in the [Spring Mock Mvc Module](#spring-mock-mvc-module) (but the config there is called [MockMvcParamConfig](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/config/MockMvcParamConfig.html).

# Spring Mock Mvc Module #

REST Assured 2.2.0 introduced support for [Spring Mock Mvc](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/web/servlet/MockMvc.html) using the `spring-mock-mvc` module. This means that you can unit test Spring Mvc Controllers. For example given the following Spring controller:
```java
@Controller
public class GreetingController {

    private static final String template = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @RequestMapping(value = "/greeting", method = GET)
    public @ResponseBody Greeting greeting(
            @RequestParam(value="name", required=false, defaultValue="World") String name) {
        return new Greeting(counter.incrementAndGet(), String.format(template, name));
    }
}
```
you can test it using [RestAssuredMockMvc](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/RestAssuredMockMvc.html) like this:
```java
given().
        standaloneSetup(new GreetingController()).
        param("name", "Johan").
when().
        get("/greeting").
then().
        statusCode(200).
        body("id", equalTo(1)).
        body("content", equalTo("Hello, Johan!"));  
```
i.e. it's very similar to the standard REST Assured syntax. This makes it really fast to run your tests and it's also easier to bootstrap the environment and use mocks (if needed) than standard REST Assured. Most things that you're used to in standard REST Assured works with RestAssured Mock Mvc as well. For example (certain) configuration, static specifications, logging etc etc. To use it you need to depend on the Spring Mock Mvc module:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>spring-mock-mvc</artifactId>
      <version>3.0.1</version>
      <scope>test</scope>
</dependency>
```
Or [download](http://dl.bintray.com/johanhaleby/generic/spring-mock-mvc-3.0.1-dist.zip) it from the download page if you're not using Maven.

## Bootstrapping RestAssuredMockMvc ##

First of all you should statically import methods in:
```java
io.restassured.module.mockmvc.RestAssuredMockMvc.*
io.restassured.module.mockmvc.matcher.RestAssuredMockMvcMatchers.*
```

instead of those defined in

```java
io.restassured.RestAssured.*
io.restassured.matcher.RestAssuredMatchers.*
```

Refer to [static import](#static-imports) section of the documentation for additional static imports.

In order to start a test using RestAssuredMockMvc you need to initialize it with a either a set of Controllers, a MockMvc instance or a WebApplicationContext from Spring. You can do this for a single request as seen in the previous example:

```java
given().standaloneSetup(new GreetingController()). ..
```
or you can do it statically:

```java
RestAssuredMockMvc.standaloneSetup(new GreetingController());
```
If defined statically you don't have to specify any Controllers (or MockMvc or WebApplicationContext instance) in the DSL. This means that the previous example can be written as:
```java
given().
        param("name", "Johan").
when().
        get("/greeting").
then().
        statusCode(200).
        body("id", equalTo(1)).
        body("content", equalTo("Hello, Johan!"));  
```

## Asynchronous Requests ##
As of version `2.5.0` RestAssuredMockMvc has support for asynchronous requests. For example let's say you have the following controller:
```java
@Controller
public class PostAsyncController {

    @RequestMapping(value = "/stringBody", method = POST)
    public @ResponseBody
    Callable<String> stringBody(final @RequestBody String body) {
        return new Callable<String>() {
            public String call() throws Exception {
                return body;
            }
        };
    }
}
```

You can test this like so:

```java
given().
        body("a string").
when().
        async().post("/stringBody").
then().
        body(equalTo("a string"));
```

This will use the default timeout of 1 second. You can change the timeout by using the DSL:
```java
given().
        body("a string").
when().
        async().with().timeout(20, TimeUnit.SECONDS).post("/stringBody").
then().
        body(equalTo("a string"));    
```

It's also possible to configure a default timeout by using the [AsyncConfig](http://static.javadoc.io/io.restassured/spring-mock-mvc/2.4.1/io/restassured/module/mockmvc/config/AsyncConfig.html), for example:

```java
given().
        config(config().asyncConfig(withTimeout(100, TimeUnit.MILLISECONDS))).
        body("a string").
when().
        async().post("/stringBody").
then().
        body(equalTo("a string"));
```

`withTimeout` is statically imported from `io.restassured.module.mockmvc.config.AsyncConfig` and is just a shortcut for creating an `AsyncConfig` with a given timeout. Apply the config globally to apply to all requests:

```java
RestAssuredMockMvc.config = RestAssuredMockMvc.config().asyncConfig(withTimeout(100, TimeUnit.MILLISECONDS));

// Request 1
given().
        body("a string").
when().
        async().post("/stringBody").
then().
        body(equalTo("a string"));

// Request 2
given().
        body("another string").
when().
        async().post("/stringBody").
then().
        body(equalTo("a string"));
```

Both request 1 and 2 will now use the default timeout of 100 milliseconds.

## Adding Request Post Processors ##
Spring MockMvc has support for [Request Post Processors](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/request/RequestPostProcessor.html) and you can use these in RestAssuredMockMvc as well. For example:
```java
given().postProcessors(myPostProcessor1, myPostProcessor2). ..
```

Note that it's recommended the add `RequestPostProcessors` from `org.springframework.security.test.web.servlet.request.SecurityMockMvcRequestPostProcessors` (i.e. authentication `RequestPostProcessors`) to `auth` instead for better readability (result will be the same):
```java
given().auth().with(httpBasic("username", "password")). ..
```
where httpBasic is statically imported from [SecurityMockMvcRequestPostProcessor](http://docs.spring.io/autorepo/docs/spring-security/current/apidocs/org/springframework/security/test/web/servlet/request/SecurityMockMvcRequestPostProcessors.html).

## Adding Result Handlers ##
Spring MockMvc has support for [Result Handlers](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/ResultHandler.html) and you can use these in RestAssuredMockMvc as well. For example let's say you want to use the native MockMvc logging:

```java
.. .then().apply(print()). .. 
```

where `print` is statically imported from `org.springframework.test.web.server.result.MockMvcResultHandlers`. Note that if you're using REST Assured 2.6.0 or older you used the `resultHandlers` method:

```java
given().resultHandlers(print()). .. 
```
but this was deprected in REST Assured 2.8.0.

## Using Result Matchers ##
Spring MockMvc provides a bunch of [Result Matchers](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/ResultMatcher.html) that you may find useful. RestAssuredMockMvc has support for these as well if needed. For example let's say that for some reason you want to verify that the status code is equal to 200 using a ResultMatcher:
```java
given().
        param("name", "Johan").
when().
        get("/greeting").
then().
        assertThat(status().isOk()).
        body("id", equalTo(1)).
        body("content", equalTo("Hello, Johan!"));  
```
where `status` is statically imported from `org.springframework.test.web.server.result.MockMvcResultMatchers`. Note that you can also use the `expect` method which is the same as `assertThat` but more close to the syntax of native MockMvc.

## Interceptors ##
For more advanced use cases you can also get ahold of and modify the [MockHttpServletRequestBuilder](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/request/MockHttpServletRequestBuilder.html) before the request is performed. To do this define a [MockHttpServletRequestBuilderInterceptor](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/intercept/MockHttpServletRequestBuilderInterceptor.html) and use it with RestAssuredMockMvc:

```java
given().interceptor(myInterceptor). ..
```

## Specifications ##
Just as with standard Rest Assured you can use [specifications](#specification_re-use) to allow for better re-use. Note that the request specification builder for RestAssuredMockMvc is called [MockMvcRequestSpecBuilder](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/specification/MockMvcRequestSpecBuilder.html). The same [ResponseSpecBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/builder/ResponseSpecBuilder.html) can be used in RestAssuredMockMvc as well though. Specifications can be defined statically as well just as with standard Rest Assured. For example:
```java
RestAssuredMockMvc.requestSpecification = new MockMvcRequestSpecBuilder().addQueryParam("name", "Johan").build();
RestAssuredMockMvc.responseSpecification = new ResponseSpecBuilder().expectStatusCode(200).expectBody("content", equalTo("Hello, Johan!")).build();

given().
        standaloneSetup(new GreetingController()).
when().
        get("/greeting").
then().
        body("id", equalTo(1));
```

## Resetting RestAssuredMockMvc ##
If you've used any static configuration you can easily reset RestAssuredMockMvc to its default state by callin the `RestAssuredMockMvc.reset()` method.

## Spring MVC Authentication ##
Version `2.3.0` of `spring-mock-mvc` supports authentication. For example:
```java
given().auth().principal(..). ..
```
Some authentication methods require Spring Security to be on the classpath (optional). It's also possible to define authentication statically:
```java
RestAssuredMockMvc.authentication = principal("username", "password");
```
where the `principal` method is statically imported from [RestAssuredMockMvc](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/RestAssuredMockMvc.html). It's also possible to define an authentication scheme in a request builder:
```java
MockMvcRequestSpecification spec = new MockMvcRequestSpecBuilder.setAuth(principal("username", "password")).build();
```

### Using Spring Security Test ###

Since version `2.5.0` there's also better support for Spring Security. If you have `spring-security-test` in classpath you can do for example:
```java
given().auth().with(httpBasic("username", "password")). ..
```
where `httpBasic` is statically imported from [SecurityMockMvcRequestPostProcessor](http://docs.spring.io/autorepo/docs/spring-security/current/apidocs/org/springframework/security/test/web/servlet/request/SecurityMockMvcRequestPostProcessors.html). This will apply basic authentication to the request. For this to work you need apply the [SecurityMockMvcConfigurer](http://docs.spring.io/autorepo/docs/spring-security/current/apidocs/org/springframework/security/test/web/servlet/setup/SecurityMockMvcConfigurers.html) to the MockMvc instance. You can either do this manually:
```java
MockMvc mvc = MockMvcBuilders.webAppContextSetup(context).apply(SecurityMockMvcConfigurers.springSecurity()).build();
```

or RESTAssuredMockMvc will automatically try to apply the `springSecurity` configurer automatically if you initalize it with an instance of [AbstractMockMvcBuilder](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/web/servlet/setup/AbstractMockMvcBuilder.html), for example when configuring a "web app context":
```java
given().webAppContextSetup(context).auth().with(httpBasic("username", "password")). ..
```

Here's a full example:
```java
import io.restassured.module.mockmvc.RestAssuredMockMvc;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.web.context.WebApplicationContext;

import static io.restassured.module.mockmvc.RestAssuredMockMvc.given;
import static org.springframework.security.test.web.servlet.request.SecurityMockMvcRequestPostProcessors.httpBasic;
import static org.springframework.security.test.web.servlet.response.SecurityMockMvcResultMatchers.authenticated;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = MyConfiguration.class)
@WebAppConfiguration
public class BasicAuthExample {

    @Autowired
    private WebApplicationContext context;

    @Before public void
    rest_assured_is_initialized_with_the_web_application_context_before_each_test() {
        RestAssuredMockMvc.webAppContextSetup(context);
    }

    @After public void
    rest_assured_is_reset_after_each_test() {
        RestAssuredMockMvc.reset();
    }

    @Test public void
    basic_auth_example() {
        given().
                auth().with(httpBasic("username", "password")).
        when().
                get("/secured/x").
        then().
                statusCode(200).
                expect(authenticated().withUsername("username"));
    }
}
```

You can also define authentication for all request, for example:
```java
RestAssuredMockMvc.authentication = with(httpBasic("username", "password"));
```
where `with` is statically imported from `io.restassured.module.mockmvc.RestAssuredMockMvc`. It's also possible to use a [request specification](#specifications).

### Injecting a User ###

It's also possible use to of Spring Security test annotations such as [@WithMockUser](http://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#test-method-withmockuser) and [@WithUserDetails](http://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#test-method-withuserdetails). For example let's say you want to test this controller:

```java
@Controller
public class UserAwareController {

    @RequestMapping(value = "/user-aware", method = GET)
    public
    @ResponseBody
    String userAware(@AuthenticationPrincipal User user) {
        if (user == null || !user.getUsername().equals("authorized_user")) {
            throw new IllegalArgumentException("Not authorized");
        }

        return "Success");
    }
}
```

As you can see the `userAware` method takes a [User](http://docs.spring.io/autorepo/docs/spring-security/current/apidocs/org/springframework/security/core/userdetails/User.html) as argument and we let Spring Security inject it by using the [@AuthenticationPrincipal](http://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/web/bind/annotation/AuthenticationPrincipal.html) annotation. To generate a test user we could do like this:

```java
@WithMockUser(username = "authorized_user")
@Test public void
spring_security_mock_annotations_example() {
    given().
            webAppContextSetup(context).
     when().
            get("/user-aware").
     then().
            statusCode(200).
            body(equalTo("Success")).
            expect(authenticated().withUsername("authorized_user"));
}
```

Note that it's also possible to not use annotations and instead use a [RequestPostProcessor](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/request/RequestPostProcessor.html) such as [SecurityMockMvcRequestPostProcessors#user(java.lang.String)](http://docs.spring.io/autorepo/docs/spring-security/4.0.0.RELEASE/apidocs/org/springframework/security/test/web/servlet/request/SecurityMockMvcRequestPostProcessors.html#user(java.lang.String)).

## Note on parameters ##
MockMvc doesn't differentiate between different kinds of parameters so `param`, `formParam` and `queryParam` currently just delegates to param in MockMvc. `formParam` adds the `application/x-www-form-urlencoded` content-type header automatically though just as standard Rest Assured does.

# Scala Support Module #
REST Assured 2.6.0 introduced the [scala-support](http://dl.bintray.com/johanhaleby/generic/scala-support-3.0.1-dist.zip) module that adds an alias to the "then" method defined in the [Response](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/response/Response.html) or [MockMvcResponse](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/response/MockMvcResponse.html) called "Then". The reason for this is that `then` might be a reserved keyword in Scala in the future and the compiler issues a warning when using a method with this name. To enable the use of `Then` simply import the `io.restassured.module.scala.RestAssuredSupport.AddThenToResponse` class from the `scala-support` module. For example:

```java
import io.restassured.RestAssured.when
import io.restassured.module.scala.RestAssuredSupport.AddThenToResponse
import org.hamcrest.Matchers.equalTo
import org.junit.Test

@Test
def `trying out rest assured in scala with implicit conversion`() {
  when().
          get("/greetJSON").
  Then().
          statusCode(200).
          body("key", equalTo("value"))
}
```
Note that this is also supported for the [Spring Mock Mvc Module](#spring-mock-mvc-module).

To use it do like this:

#### SBT:
```scala
libraryDependencies += "io.rest-assured" % "scala-support" % "3.0.1"
```

#### Maven:
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>scala-support</artifactId>
    <version>3.0.1</version>
    <scope>test</scope>
</dependency>
```

#### Gradle:
```xml
testCompile 'io.rest-assured:scala-support:3.0.1'
```

### No build manager:
Download the [distribution file](http://dl.bintray.com/johanhaleby/generic/scala-support-3.0.1-dist.zip) manually.

# Kotlin #
Kotlin is a language developed by [JetBrains](https://www.jetbrains.com/) and it integrates very well with Java and REST Assured. When using it with REST Assured there's one thing that can be a bit annoying. That is you have to escape `when` since it's a reserved keyword in Kotlin. For example:

```kotlin
Test fun kotlin_rest_assured_example() {
    given().
            param("firstName", "Johan").
            param("lastName", "Haleby").
    `when`().
            get("/greeting").
    then().
            statusCode(200).
            body("greeting.firstName", equalTo("Johan")).
            body("greeting.lastName", equalTo("Haleby"))
}
```

To get around this, create an [extension function](https://kotlinlang.org/docs/reference/extensions.html) that creates an alias to `when` called `When`:

```kotlin
fun RequestSpecification.When(): RequestSpecification {
    return this.`when`()
}
```

The code can now be written like this:

```kotlin
Test fun kotlin_rest_assured_example() {
    given().
            param("firstName", "Johan").
            param("lastName", "Haleby").
    When().
            get("/greeting").
    then().
            statusCode(200).
            body("greeting.firstName", equalTo("Johan")).
            body("greeting.lastName", equalTo("Haleby"))
}
```

Notice that we don't need any escaping anymore. For more details refer to [this](http://code.haleby.se/2015/11/06/rest-assured-with-kotlin/) blog post.

# More info #
For more information refer to the [javadoc](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/index.html):
  * [RestAssured](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/RestAssured.html)
  * [RestAssuredMockMvc Javadoc](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/RestAssuredMockMvc.html)
  * [Specification package](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/specification/package-summary.html)

You can also have a look at some code examples:
  * REST Assured [tests](https://github.com/rest-assured/rest-assured/tree/master/examples/rest-assured-itest-java/src/test/java/io/restassured/itest/java)
  * [JsonPathTest](https://github.com/rest-assured/rest-assured/blob/master/json-path/src/test/java/io/restassured/path/json/JsonPathTest.java)
  * [XmlPathTest](https://github.com/rest-assured/rest-assured/blob/master/xml-path/src/test/java/io/restassured/path/xml/XmlPathTest.java)

If you need support then join the [mailing list](http://groups.google.com/group/rest-assured).

For professional support please contact [johanhaleby](https://github.com/johanhaleby).
