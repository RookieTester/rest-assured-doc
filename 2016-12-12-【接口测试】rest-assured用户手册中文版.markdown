>原文：https://github.com/rest-assured/rest-assured/wiki/Usage
>本文github地址：https://github.com/RookieTester/rest-assured-doc

注意，如果您正在使用1.9.0或者更早的版本请参考[旧文档](https://github.com/rest-assured/rest-assured/wiki/Usage_Legacy)。

REST Assured是一个可以简化HTTP Builder顶层 基于REST服务的测试过程的Java **DSL**（针对某一领域，具有受限表达性的一种计算机程序设计语言）。它支持发起POST,GET,PUT,DELETE,OPTIONS,PATCH和HEAD请求，并且可以用来验证和校对这些请求的响应信息。

# 目录
1. [静态导入方法](#静态导入方法)
1. [示例](#示例)
  1. [JSON 示例](#例一-JSON)
  1. [JSON Schema Validation](#JSONSchemavalidation)
  1. [XML 示例](#例2-XML)
  1. [高级用法](#例3-复杂的解析和验证)
    1. [XML](#XML示例)
    2. [JSON](#JSON示例)
  1. [其它示例](#其它例子)
1. [关于float和double](#关于float和double)
1. [语法关注点](#语法关注点) ([语法糖](#语法糖))
1. [获得响应体信息](#获得响应体信息)
  1. [从已验证的响应体中提取值](#从已验证的响应体中提取值)
  1. [JSON (使用 JsonPath)](#JSON(使用JsonPath))
  1. [XML (使用XmlPath)](#XML(使用XmlPath))
  1. [获取某个路径下的值](#获取某个路径下的值)
  1. [Headers, cookies, status等](#headers-cookies-status-etc)
    1. [获取全部header值](#multi-value-headers)
    1. [获取全部cookie值](#multi-value-cookies)
    1. [获取详细的cookie值](#detailed-cookies)
1. [获得响应信息](#getting-response-data)
  1. [在验证响应之后提取特定的值](#extracting-values-from-the-response-after-validation)
  1. [JSON (使用JsonPath)](#json-using-jsonpath)
  1. [XML (使用XmlPath)](#xml-using-xmlpath)
  1. [单独使用路径](#single-path)
  1. [Headers, cookies, status等](#headers-cookies-status-etc)
    1. [获取header](#多个header)
    1. [获取cookie](#多个cookie)
    1. [获取详细的cookie值](#详细的Cookies信息)
1. [指定请求数据](#指定请求数据)
  1. [请求HTTP资源](#请求HTTP资源)
  1. [参数化](#参数化)
  1. [多值参数](#多值参数)
  1. [参数不赋值](#无值参数)
  1. [路径参数](#路径参数)
  1. [Cookie](#Cookie)
  1. [Header](#Header)
  1. [Content-Type](#ContentType)
  1. [请求正文](#请求正文)
1. [验证响应信息](#验证响应信息)
  1. [响应体](#响应体)
  1. [Cookie](#Cookie)
  1. [状态码](#状态码)
  1. [Header](#Header)
  1. [Content-Type](#Content-Type)
  1. [内容全匹配](#内容全匹配)
  1. [关联类型验证](#关联类型验证)
  1. [计算响应时间](#计算响应时间)
1. [认证](#认证)
  1. [基本认证](#基本认证)
    1. [抢占式的基本认证](#抢占式的基本认证)
    1. [受质询的基本认证](#受质询的基本认证)
  1. [摘要认证](#摘要认证)
  1. [表单认证](#表单认证)
    1. [CSRF](#csrf)
  1. [OAuth](#oauth)
    1. [OAuth1](#oauth-1)
    1. [OAuth2](#oauth-2)
1. [Multi-part类型的表单数据](#Multi-part表单数据)
1. [对象映射](#对象映射)
  1. [序列化](#序列化)
    1. [基于Content-Type的序列化](#基于Content-Type的序列化)
    1. [由HashMap创建JSON](#由HashMap创建JSON)
    1. [使用显式序列化器](#使用显式序列化器)
  1. [反序列化](#反序列化)
    1. [基于Content-Type的反序列化](#content-type-based-deserialization)
    1. [自定义content-type的反序列化](#自定义content-type的反序列化)
    1. [使用显式反序列化器](#使用显式反序列化器)
  1. [配置](#配置)
  1. [自定义](#自定义)
1. 解析器
  1. [自定义解析器](#自定义解析器)
  1. [默认解析器](#默认解析器)
1. [默认值](#默认值)
1. [模式复用](#模式复用)
1. [过滤器](#过滤器)
  1. [Response Builder](#response-builder)
1. [日志](#日志)
  1. [请求日志](#请求日志)
  1. [响应日志](#响应日志)
  1. [认证失败日志](#认证失败日志)
1. [根路径](#根路径)
  1. [路径参数](#路径参数)
1. [Session支持](#Session支持)
  1. [Session过滤器](#Session过滤器)
1. [SSL](#ssl)
  1. [SSL无效的主机名](#SSL无效主机名)
1. [URL编码](#URL编码)
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
     1. [使用 Spring Security 测试](#使用 Spring Security 测试)
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

如果您想使用[Json Schema](http://json-schema.org/) validation 还应该静态导入这些方法： 

```java
io.restassured.module.jsv.JsonSchemaValidator.*
```

更多使用方法参阅 [Json Schema Validation](#json-schema-validation) 。

如果您正在使用SpringMVC，你可以使用[spring-mock-mvc](#spring-mock-mvc-module) 模型的Rest Assured DSL来对Spring的controller层进行单元测试。为此需要从[RestAssuredMockMvc](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/RestAssuredMockMvc.html)静态导入这些方法，而不是`io.restassured.RestAssured`:

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

REST assured可以帮您轻松地进行get请求并对响应信息进行处理。举个例子，如果想要判断lottoId的值是否等于5，你可以这样做：

```java
get("/lotto").then().body("lotto.lottoId", equalTo(5));
```
又或许您想要检查winnerId的取值**包括**23和54：

```java
get("/lotto").then().body("lotto.winners.winnerId", hasItems(23, 54));
```

注意: `equalTo` 和 `hasItems` 是 Hamcrest matchers的方法，所以需要静态导入 `org.hamcrest.Matchers`。

注意这里的"json path"语法使用的是<a href='http://groovy-lang.org/processing-xml.html#_gpath'>Groovy的GPath</a>标注法，不要和Jayway的<a href='https://github.com/jayway/JsonPath'>JsonPath</a>语法混淆。

### 以BigDecimal返回float和double类型 ###
（译者注：Java在java.math包中提供的API类BigDecimal，用来对超过16位有效位的数进行精确的运算）

您可以对rest-assured和JsonPath进行配置，使之以BigDecimal返回json里的数值类型数据，而不是float或者double。可以参考下面json文本：

```javascript
{

    "price":12.12 

}
```
默认情况下您验证price字段是否等于float类型的12.12像这样：

```java
get("/price").then().body("price", is(12.12f));
```
但是如果想用rest-assured的JsonConfig来配置返回的所有的json数值都为BigDecimal类型：

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
您可以使用这个schema验证(`/products`)这个请求是否符合规范：
```java
get("/products").then().assertThat().body(matchesJsonSchemaInClasspath("products-schema.json"));
```
`matchesJsonSchemaInClasspath` 静态导入自 `io.restassured.module.jsv.JsonSchemaValidator` 并且我们推荐从这个类中静态导入所有的方法。然而为了使用它需要依赖于`json-schema-validator` module 或者从这个网页 [下载](http://dl.bintray.com/johanhaleby/generic/json-schema-validator-3.0.1-dist.zip) 它， 又或者通过maven添加下面的依赖:
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>json-schema-validator</artifactId>
    <version>3.0.1</version>
</dependency>
```

### JSON Schema Validation 设置项 ###

rest-assured的`json-schema-validator` module使用Francis Galiegue的[json-schema-validator](https://github.com/fge/json-schema-validator) (`fge`) 库来进行验证。 如果您想配置使用基础`fge`库，你可以像下面例子中：

```java
// Given
JsonSchemaFactory jsonSchemaFactory = JsonSchemaFactory.newBuilder().setValidationConfiguration(ValidationConfiguration.newBuilder().setDefaultVersion(DRAFTV4).freeze()).freeze();

// When
get("/products").then().assertThat().body(matchesJsonSchemaInClasspath("products-schema.json").using(jsonSchemaFactory));
```

using方法允许您进入`jsonSchemaFactory`的实例，rest-assured在验证期间也会进行此操作。这种方式允许我们对验证进行细粒度的配置。

`fge`库也允许验证状态是 `checked`或者`unchecked`（译者注：表示不懂）。默认情况，rest-assured使用`checked`验证，但是如果你想要改变这种方式，您可以提供一个matcher的[JsonSchemaValidatorSettings](http://static.javadoc.io/io.restassured/json-schema-validator/3.0.1/io/restassured/module/jsv/JsonSchemaValidatorSettings.html)实例。举个例子：

```java
get("/products").then().assertThat().body(matchesJsonSchemaInClasspath("products-schema.json").using(settings().with().checkedValidation(false)));
```

这些`settings`方法静态导入自 [JsonSchemaValidatorSettings](http://static.javadoc.io/io.restassured/json-schema-validator/3.0.1/io/restassured/module/jsv/JsonSchemaValidatorSettings.html)类。

### Json Schema Validation的静态配置###
现在想象下您总是使用`unchecked`验证，并且设置默认的json schema版本为3。与其每次都在代码里进行设置，不如静态地进行定义设置。举个例子：
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
您也可以在不依赖rest-assured的情况下使用`json-schema-validator` module。如想要把json文本表示为`String`类型的字符串，可以这样做：

```java
import org.junit.Test;
import static io.restassured.module.jsv.JsonSchemaValidator.matchesJsonSchemaInClasspath;
import static org.hamcrest.MatcherAssert.assertThat;
 
public class JsonSchemaValidatorWithoutRestAssuredTest {
 
 
    @Test 
    public void validates_schema_in_classpath() {
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
换言之，它在请求中返还了一个基于firstname和lastname请求参数的greeting节点。您可以通过rest-assured轻易地展现和解析这个例子：
```java
given().
         parameters("firstName", "John", "lastName", "Doe").
when().
         post("/greetXML").
then().
         body("greeting.firstName", equalTo("John")).
```
如果您想同时解析firstname和lastname可以这样做：
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

看[这里](http://groovy-lang.org/processing-xml.html#_gpath) 的链接获取有关语法的更多信息(它遵循 Groovy的 [GPath](http://groovy-lang.org/processing-xml.html#_gpath) 语法).

### XML 命名空间 ###
考虑到您需要使用[io.restassured.config.XmlConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/XmlConfig.html)声明一个命名空间。举个例子，有一个位于`http://localhost:8080`的资源`namespace-example`，返回如下的XML：
```xml
<foo xmlns:ns="http://localhost/">
  <bar>sudo </bar>
  <ns:bar>make me a sandwich!</ns:bar>
</foo>
```

可以然后声明`http://localhost/`这个URI并且验证其响应：
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

您也可以使用x-path来解析XML响应。举个例子：

```java
given().parameters("firstName", "John", "lastName", "Doe").when().post("/greetXML").then().body(hasXPath("/greeting/firstName", containsString("Jo")));
```

或者

```java
given().parameters("firstName", "John", "lastName", "Doe").post("/greetXML").then().body(hasXPath("/greeting/firstName[text()='John']"));
```

在XPath表达式中使用命名空间，你需要在配置中启用这些选项：
```java
given().
        config(RestAssured.config().xmlConfig(xmlConfig().with().namespaceAware(true))).
when().
         get("/package-db-xml").
then().
         body(hasXPath("/db:package-database", namespaceContext));
```

`namespaceContext` 是一个[javax.xml.namespace.NamespaceContext](http://docs.oracle.com/javase/7/docs/api/javax/xml/namespace/NamespaceContext.html)的实例 。

### Schema和DTD ###

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

在这个category上我们接下来继续收集所有相关联的项目（item）。

由于这里与category相关联的项目不止一个，所以会返回一个列表。接下来我们通过Hamcrest matcher的`hasItems`方法来解析它。

但是如果我们想取得一些项目（item）但又不想进行断言验证该怎么办？您可以参考[XmlPath](http://static.javadoc.io/io.restassured/xml-path/3.0.1/io/restassured/path/xml/XmlPath.html):

```java
// Get the response body as a String
String response = get("/shopping").asString();
// And get the groceries from the response. "from" is statically imported from the XmlPath class
List<String> groceries = from(response).getList("shopping.category.find { it.@type == 'groceries' }.item");
```

如果groceries是您对这个响应里唯一的关注点，也可以使用一个[捷径](#single-path):

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

这是个挺难以回答的问题，也正展示了闭包和Groovy集合的强大之处。在rest-assured里可以：
 
 ```java
 when().
        get("/store");
 then().
        body("store.book.author.collect { it.length() }.sum()", greaterThan(50));
```

首先我们通过(`store.book.author`)得到了所有的author字段值，然后使用闭包里的方法`{ it.length() }`解析这个集合。

它所做的是对列表里的每一个author字段执行一次`length()`方法，然后返回一个新的列表。在这个列表中，我们再调用`sum()`方法来求得字符长度的总和。

最终的结果是53，并且我们使用`greaterThan`匹配器的断言结果是大于50 。
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
Micha Kops曾写过一篇很优秀的博客，里面包含大量示例（包括可检出的代码）。您可以[由此进入试读](http://www.hascode.com/2011/10/testing-restful-web-services-made-easy-using-the-rest-assured-framework/)。

[Bas Dijkstra](https://www.linkedin.com/in/basdijkstra)也开展过不少关于rest-assured的开源研究和资源。你可以[由此进入试读](http://www.ontestautomation.com/open-sourcing-my-workshop-an-experiment/)，如果您想试用或者作出贡献，[他的github仓库](https://github.com/basdijkstra/workshops/)里有些可以尝试的练习题。


## 关于float和double ##
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

## 语法关注点 ##
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

将会仅仅报告首个预期/断言失败的内容（比如预期状态码是400实际是200），第二个断言将不执行。您将不得不重新运行这个用例以期获取到第二个断言的结果。

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

# 获得响应体信息 #
你也可以获得响应的内容。比方说你想通过发起一个get请求"/lotto"并获取其响应内容。你可以以多种方式：
```java
InputStream stream = get("/lotto").asInputStream(); // Don't forget to close this one when you're done
byte[] byteArray = get("/lotto").asByteArray();
String json = get("/lotto").asString();
```

## 从已验证的响应体中提取值 ##
您可以从响应信息中提取值，或者使用`extract`方法仅仅返回response本身的一个实例。如何你想获取响应里的值，并将其作为接下来的请求内容，这会很有用。下面是一个叫做`title`的资源返回的JSON数据：
```javascript
 {
     "title" : "My Title",
      "_links": {
              "self": { "href": "/title" },
              "next": { "href": "/title?page=2" }
           }
 }
```

想验证内容类型是JSON格式且标题是`My Title`，但是还想要从中提取next的值并用来发起请求，下面是使用方法：

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

如果您想提取多个值，也可以考虑返回整个响应体：
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
一旦我们取得了响应体，可以使用[JsonPath](http://static.javadoc.io/io.restassured/json-path/3.0.1/io/restassured/path/json/JsonPath.html)来提取相应的数据：

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

注意这里我们独立地使用了`JsonPath`，而没有依赖rest-assured本身的功能，看[getting started guide](https://github.com/rest-assured/rest-assured/wiki/GettingStarted) 获取更多信息。

### JsonPath 配置 ###
您可以为JsonPath配置反序列化对象（object de-serializers），举个例子：
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
您也可以使用[XmlPath](http://static.javadoc.io/io.restassured/xml-path/3.0.1/io/restassured/path/xml/XmlPath.html)相应的功能：

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

注意，您可以独立于rest-assured，单独使用`XmlPath`的功能，更多信息参见[getting started guide](https://github.com/rest-assured/rest-assured/wiki/GettingStarted)。

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

## 获取某个路径下的值 ##
如您你只是想发起一个请求并返回一个路径下的值，你可以使用一个捷径：
```java
int lottoId = get("/lotto").path("lotto.lottoid");
```

rest-assured会基于响应体的content-type自动决定是使用JsonPath还是XmlPath。如果这个类型在rest-assured没有被定义，它将会自动到[default parser](#default-parser)中查找。你可以自行（代码指定）决定使用哪种，比如：

```java
String firstName = post("/greetXML?firstName=John&lastName=Doe").andReturn().xmlPath().getString("firstName");
```

`xmlPath`, `jsonPath`和`htmlPath`都是可选项。

## Headers, cookies, status等 ##

您也可以获取 header, cookie, 状态行，状态码:

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

## 多个 header 和 cookie ##
header 和 cookie 可以包含同名的多个值。

### 多个 header ###

要获取header的所有值，您需要首先从[Response](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/response/Response.html)对象中获取[Headers](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/http/Headers.html) 对象。您需要首先从Response对象中获取Headers对象。您可以使用Headers.getValues（
）方法返回一个具有所有header值的List列表。


### 多个 cookie ###

要获取cookie的所有值，您需要首先从[Response](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/response/Response.html)对象中获取[Cookie](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/http/Cookies.html)对象。您可以使用Cookie.getValues（）方法获取所有值，该方法返回包含所有Cookie值的List列表。


## 详细的 Cookies 信息 ##

如果您需要获取Cookie的路径或过期日期等详细信息，您需要从REST Assured获取一个[detailed cookie](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/http/Cookie.html)。您可以使用[Response.getDetailedCookie(java.lang.String)](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/response/ResponseOptions.html#getDetailedCookie-java.lang.String-) 方法获取单个Cookie，包括与给定名称相关联的所有属性。

您还可以使用[Response.getDetailedCookies()](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/response/ResponseOptions.html#getDetailedCookies--)方法获取所有详细的响应[cookies](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/http/Cookies.html)。

# 指定请求数据 #

除了指定请求参数，您还可以指定header，Cookie，正文和Content Type。

## 请求HTTP资源 ##

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


## 参数化 ##
通常您可以这样指定参数：


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

参数如果上传的是文件，字节数组，输入流或文本的可以参照[Multi-part类型的表单数据](#Multi-part表单数据)部分


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


路径参数使得更容易读取请求路径，且使请求路径能够在具有不同参数值的许多测试中容易地重复使用。

从版本2.8.0开始，您可以混合未赋值和赋值好的路径参数：

```java
given().
        pathParam("hotelId", "My Hotel").        
when(). 
        post("/reserve/{hotelId}/{roomNumber}", 23).
then().
         ..
```
这里 `roomNumber` 的值`My Hotel`将被替换为 `23`.


注意，指定太少或太多的参数将导致错误消息。对于高级用例，您可以从[过滤器]（＃过滤器）添加，更改，删除（甚至冗余的路径参数）。


## Cookie ##

通常模式下，您可以通过以下方法指定Cookie：
```java
given().cookie("username", "John").when().get("/cookie").then().body(equalTo("username"));
```
也可以像这样给cookie指定多个值：

```java
given().cookie("cookieName", "value1", "value2"). ..
```

这将创建两个cookie：cookieName = value1和cookieName = value2。

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

## Header ##
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


您还可以将Java对象序列化为JSON或XML。点击[这里](#序列化)了解详情。


# 验证响应数据 #

您还可以验证状态码，状态行，Cookie，headers，内容类型和正文。

## 响应体 ##

请参阅使用示例，例如[JSON](#例一-JSON) 或 [XML](#例2-XML).

您还可以将响应正文映射到Java对象，单击[这里](#deserialization) 了解详细信息。


## Cookie ##
```java
get("/x").then().assertThat().cookie("cookieName", "cookieValue"). ..
get("/x").then().assertThat().cookies("cookieName1", "cookieValue1", "cookieName2", "cookieValue2"). ..
get("/x").then().assertThat().cookies("cookieName1", "cookieValue1", "cookieName2", containsString("Value2")). ..
```

## 状态码 ##
```java
get("/x").then().assertThat().statusCode(200). ..
get("/x").then().assertThat().statusLine("something"). ..
get("/x").then().assertThat().statusLine(containsString("some")). ..
```


## Header ##

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


## 内容全匹配 ##

```java
get("/x").then().assertThat().body(equalTo("something")). ..
```

## 关联类型验证 ##

您可以使用响应中的数据来验证响应的另一部分。 例如，从服务端返回的以下JSON：
```javascript
{ "userId" : "some-id", "href" : "http://localhost:8080/some-id" }
```

您可能会注意到，“href”属性以“userId”属性的值结尾。 如果我们想验证这个，我们可以实现一个io.restassured.matcher.ResponseAwareMatcher，可以：

```java
get("/x").then().body("href", new ResponseAwareMatcher<Response>() {
                                  public Matcher<?> matcher(Response response) {
                                          return equalTo("http://localhost:8080/" + response.path("userId"));
                                  }
                       });
```
如果您使用Java 8，你可以使用lambda表达式：

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

 `and` 方法是由`io.restassured.matcher.ResponseAwareMatcherComposer`静态导入的。

## 计算响应时间 ##

从 REST Assured  2.8.0开始支持测量响应时间，例如：

```java
long timeInMs = get("/lotto").time()
```

或使用特定时间单位：

```java
long timeInSeconds = get("/lotto").timeIn(SECONDS);

```
其中SECONDS只是一个标准的TimeUnit。 您还可以使用DSL验证：

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

需要注意的是，您只能参考性地将这些测量数据与服务器请求处理时间相关联（因为响应时间将包括HTTP往返和REST Assured处理时间等，不能做到十分准确）。


# 认证 #

REST assured还支持多种认证方案，例如OAuth，摘要，证书，表单和抢占式基本认证。 您可以为每个请求设置身份验证：

```java
given().auth().basic("username", "password"). ..
```

也可以为所有请求定义身份验证：

```java
RestAssured.authentication = basic("username", "password");
```
或者您也可以使用 [specification](#模式复用).

## 基本认证 ##

有两种类型的基本认证，抢占和“受质询的基本认证”。

### 抢占式 ###

服务器在某些情况下给出未授权响应之前发送基本认证凭证，从而减少进行附加连接的开销。 大多数情况下可以这么使用：

```java
given().auth().preemptive().basic("username", "password").when().get("/secured/hello").then().statusCode(200);
```

### 受质询的基本认证 ###

使用“受质询的基本认证”时，REST Assured将不提供凭据，除非服务器已明确要求。 这意味着REST Assured将向服务器发出一个附加请求，以便进行质询，然后再次处理相同的请求，但此时会在header中设置基本凭据。

```java
given().auth().basic("username", "password").when().get("/secured/hello").then().statusCode(200);
```

## 摘要认证 ##

目前只支持受质询的摘要认证：


```java
given().auth().digest("username", "password").when().get("/secured"). ..
```

## 表单认证 ##

[表单认证](https://en.wikipedia.org/wiki/Form-based_authentication)在互联网上非常流行。 它通常与用户在网页上填写其凭据（用户名和密码），然后在按某种类型的登录按钮时发起请求。 提供表单身份验证基础的一个非常简单的HTML页面可能如下所示

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
 在REST中使用此类表单身份验证时，会导致为检索包含登录详细信息的网页而向服务器发出附加请求。 REST Assured将尝试解析此页面并查找两个输入字段（用户名和密码）以及表单操作的URI。 这可能失败，取决于网页的复杂性。 更好的选择是在设置表单身份验证时提供这些详细信息。 在这种情况下，可以：

```java
given().
        auth().form("John", "Doe", new FormAuthConfig("/j_spring_security_check", "j_username", "j_password")).
when().
        get("/formAuth");
then().
        statusCode(200);
```
这样REST Assured不需要提出额外的请求并解析网页。 还有一个预定义的FormAuthConfig称为`springSecurity`，如果你使用默认的Spring Security属性，可以使用它：
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
rest-assured允许您创建一个自定义的身份验证。你可以通过实现`io.restassured.spi.AuthFilter`接口，并作为一个过滤器。假设您的安全机制，是由两个header值相加然后组成一个新的叫做"AUTH"的header（当然这并不安全）。然后您可以这样做（Java 8的语法）：
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

## 序列化 ##
假设我们有下面的Java对象：

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

您也可以提供一个Map，由此rest-assured可以创建一个JSON。
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

### 使用显式序列化器 ###
如果您的classpath中同时有多个对象、或者不考虑content-type的设置，可以显示地指定一个序列化器。

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
让我们再次假设我们有以下的Java对象：

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

#### 自定义类型content-type反序列化 ####
如果服务端返回一个自定义的content-type，假设是"application/something"，你仍然想使用rest-assured的对象映射的话，这有两种方法。你可以使用[显式指定](http://code.google.com/p/rest-assured/wiki/Usage#Using_an_Explicit_Deserializer)的方法或者为自定义的content-type注册一个解析器：

```java
Message message = expect().parser("application/something", Parser.XML).when().get("/message").as(Message.class);
```

或

```java
Message message = expect().defaultParser(Parser.XML).when().get("/message").as(Message.class);
```

你也可以注册一个默认解析器，或者静态式注册一个自定义的解析器，也可以使用[模式（specifications）](#模式复用)。

### 使用显式反序列化器 ###
如果您的classpath下同时有多个对象或者不在意响应体的content-type，你可以使用显示的反序列化器。

```java
Message message = get("/message").as(Message.class, ObjectMapperType.GSON);
```

## 配置 ##
您可以使用[ObjectMapperConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/ObjectMapperConfig.html)配置预定义的对象映射，并传递给[细节配置](#detailed-configuration)。举个例子，你可以将GSON的命名策略改为LowerCaseWithUnderscores（译者注：一种策略，将大写字母改为小写字母并添加下划线）：

```java
RestAssured.config = RestAssuredConfig.config().objectMapperConfig(objectMapperConfig().gsonObjectMapperFactory(
                new GsonObjectMapperFactory() {
                    public Gson create(Class cls, String charset) {
                        return new GsonBuilder().setFieldNamingPolicy(LOWER_CASE_WITH_UNDERSCORES).create();
                    }
                }
        ));
```

这里为GSON、JAXB、Jackson和Faster Jackson都预定义了用来映射实例的工厂。

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

# 自定义解析器 #
rest-assured提供了预定义的解析器，例如HTML、XML和JSON的。但是您可以通过注册预置的解析器来解析现在不支持的内容类型：
```java
RestAssured.registerParser(<content-type>, <parser>);
```
例如注册一个可以解析'application/vnd.uoml+xml'类型的XML解析器：
```java
RestAssured.registerParser("application/vnd.uoml+xml", Parser.XML);
```
您也可以注销一个解析器：
```java
RestAssured.unregisterParser("application/vnd.uoml+xml");
```

解析器可以指定于每个请求中：
```java
get(..).then().using().parser("application/vnd.uoml+xml", Parser.XML). ..;
```

然后使用[模式](#模式复用)。

# 默认解析器 #
有时如果响应中不包含任何content-type，指定一个默认的解析器会很有用。

```java
RestAssured.defaultParser = Parser.JSON;
```

你也可以为一次请求指定默认的解析器：
```java
get("/x").then().using().defaultParser(Parser.JSON). ..
```

或者使用[响应体模式](#模式复用)。

# 默认值 #
rest-assured发起请求时默认使用localhost的8080端口.如果你需要换个端口：
```java
given().port(80). ..
```
或者简单些:
```java
..when().get("http://myhost.org:80/doSomething");
```
您也可以改变默认的基本URI、基本路径、端口和认证scheme：
```java
RestAssured.baseURI = "http://myhost.org";
RestAssured.port = 80;
RestAssured.basePath = "/resource";
RestAssured.authentication = basic("username", "password");
RestAssured.rootPath = "x.y.z";
```
这意味着类似`get("/hello")`这样的请求实际上会被解析为http://myhost.org:80/resource/hello，附带身份认证中的用户名和密码属性。关于设置根路径参考[这里](http://code.google.com/p/rest-assured/wiki/Usage#Root_path)。其它的默认值也可以被指定：

```java
RestAssured.filters(..); // List of default filters
RestAssured.requestSpecification = .. // Default request specification
RestAssured.responseSpecification = .. // Default response specification
RestAssured.urlEncodingEnabled = .. // Specify if Rest Assured should URL encoding the parameters
RestAssured.defaultParser = .. // Specify a default parser for response bodies if no registered parser can handle data of the response content-type
RestAssured.registerParser(..) // Specify a parser for the given content-type
RestAssured.unregisterParser(..) // Unregister a parser for the given content-type
```

您可以设置重置为标准的baseURI (localhost)，basePath（空），标准端口（8080），标准根路径（“”），默认身份认证scheme（none）和URL编码启用（true）：
```java
RestAssured.reset();
```

# 模式复用 #
与其复制一份响应的断言或者请求参数（的代码）到另一个测试用例中，我们可以使用 [RequestSpecBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/builder/RequestSpecBuilder.html)或者[ResponseSpecBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/builder/ResponseSpecBuilder.html)定义一个规范提案。

举个例子，在多个测试用例中，我们都涉及到这样的内容：判断响应码是否为200，JSON数组x.y的长度是否是2，您可以定义一个ResponseSpecBuilder：

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

在这个例子中需要重用的数据定义并合并在"responseSpec"，并且仅当所有预期都通过时用例才能通过。

您也可以将相同的请求参数重用：
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

这里请求数据合并在"requestSpec"中，由此上面例子中实际请求参数包括两个("parameter1" 和 "parameter2")和一个header("header1")。

# 过滤器 #
过滤器会在请求实际发起之前侦测和改变该请求的内容，也可以在响应体实际返回之前拦截并[改变](#response-builder)。您可以将其理解为AOP中的around advice（译者注：可以自行搜索切片编程）。过滤器也可以用在认证scheme、session管理、日志中。创建一个过滤器需要实现[io.restassured.filter.Filter](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/filter/Filter.html)接口。使用过滤器：

```java
given().filter(new MyFilter()). ..
```

rest-assured提供了几个过滤器：
  1. `io.restassured.filter.log.RequestLoggingFilter`: 可以打印出请求模式的细节。
  1. `io.restassured.filter.log.ResponseLoggingFilter`: 可以打印响应信息的细节如果响应体的状态码匹配given方法的参数。
  1. `io.restassured.filter.log.ErrorLoggingFilter`: 如果发生了异常（状态码在400和500之间），过滤器将会打印响应的内容。


## Response Builder ##

如果您想要通过一个过滤器改变[Response](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/response/Response.html) ，可以使用[ResponseBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/builder/ResponseBuilder.html)创建一个基于原始response的新实例。比如把原始响应体改为something：
```java
Response newResponse = new ResponseBuilder().clone(originalResponse).setBody("Something").build();
```

# 日志 #
在大量的用例中，打印出响应或者请求的细节将有助于创建正确的预期、发送准确的请求。为此您可以使用rest-assured预定义的[过滤器](#过滤器)，或者使用其中的快捷方法。

## 请求日志 ##
自1.5版本起rest-assured支持对[特定的请求](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/specification/RequestSpecification.html)打日志，之前的做法是使用[RequestLoggingFilter（译者注：应该是通过过滤器进行控制）](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/filter/log/RequestLoggingFilter.html)。注意打印日志后HTTP Builder和HTTP Client会添加额外的header内容。这个过滤器可以只记录特定请求的特定细节。换句话说，你可以不关注[RequestLoggingFilter](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/filter/log/RequestLoggingFilter.html)记录的有关实际往服务端发送的内容。因为随后的其它过滤器会在日志记录后改变这个请求。如果你想要记录实际发送的内容，参阅[HTTP Client logging docs](http://hc.apache.org/httpcomponents-client-ga/logging.html) or use an external tool such [Wireshark](http://www.wireshark.org/)，或者使用第三方工具例如[Wireshark](http://www.wireshark.org/)。示例如下：
```java
given().log().all(). .. // Log all request specification details including parameters, headers and body
given().log().params(). .. // Log only the parameters of the request
given().log().body(). .. // Log only the request body
given().log().headers(). .. // Log only the request headers
given().log().cookies(). .. // Log only the request cookies
given().log().method(). .. // Log only the request method
given().log().path(). .. // Log only the request path
```

## 响应日志 ##
如果您想打印除了状态码以外的响应信息，可以：

```java
get("/x").then().log().body() ..
```

这样做，无论是否有异常错误发生，都会打印出响应信息。如果您希望只有当错误发生时才打印响应信息，可以：

```java
get("/x").then().log().ifError(). .. 
```

您也可以记录响应里包括状态码、header、cookie的所有细节：

```java
get("/x").then().log().all(). .. 
```

也可以只记录状态码、header或者cookie：
```java
get("/x").then().log().statusLine(). .. // Only log the status line
get("/x").then().log().headers(). .. // Only log the response headers
get("/x").then().log().cookies(). .. // Only log the response cookies
```

您也可以配置为仅当状态码匹配某个值时才打印响应体：
```java
get("/x").then().log().ifStatusCodeIsEqualTo(302). .. // Only log if the status code is equal to 302
get("/x").then().log().ifStatusCodeMatches(matcher). .. // Only log if the status code matches the supplied Hamcrest matcher
```

## 认证失败日志 ##

自rest-assured2.3.1版本起，您可以仅当认证失败时记录请求或者响应的日志。为请求打日志：
```java
given().log().ifValidationFails(). ..
```

为响应打日志：
```java
.. .then().log().ifValidationFails(). ..
```

同时启用对请求和响应的认证失败记录，可以使用[LogConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/LogConfig.html):

```java
given().config(RestAssured.config().logConfig(logConfig().enableLoggingOfRequestAndResponseIfValidationFails(HEADERS))). ..
```

认证失败，仅记录header。

还有种针对所有请求的简单写法：

```java
RestAssured.enableLoggingOfRequestAndResponseIfValidationFails();
```

# 根路径 #
为避免在body方法里使用重复的路径，您可以指定一个根路径：
```java
when().
         get("/something").
then().
         body("x.y.firstName", is(..)).
         body("x.y.lastName", is(..)).
         body("x.y.age", is(..)).
         body("x.y.gender", is(..));
```

使用根路径：

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
也可以设置一个默认的根路径：
```java
RestAssured.rootPath = "x.y";
```

在许多高级用例中，在根路径上附加一些参数也很有用。您可以使用`appendRoot`方法：
```java
when().
         get("/jsonStore").
then().
         root("store.%s", withArgs("book")).
         body("category.size()", equalTo(4)).
         appendRoot("%s.%s", withArgs("author", "size()")).
         body(withNoArgs(), equalTo(4));
```

也可以对根路径进行拆分：
```java
when().
         get("/jsonStore").
then().
         root("store.category").
         body("size()", equalTo(4)).
         detachRoot("category").
         body("size()", equalTo(1));
```

# 路径参数 #
在预定义的路径包含变量时，路径参数会很有用。举个例子：
```java
String someSubPath = "else";
int index = 1;
get("/x").then().body("something.%s[%d]", withArgs(someSubPath, index), equalTo("some value")). ..
```

将会对"`something.else[0]（译者注：这里只是举个例子）`"是否等于"some value"进行判断。

另一种用法是，如果您有复杂的[根路径](http://code.google.com/p/rest-assured/wiki/Usage#Root_path)：
```java
when().
       get("/x").
then().
       root("filters.filterConfig[%d].filterConfigGroups.find { it.name == 'GroupName' }.includes").
       body(withArgs(0), hasItem("first")).
       body(withArgs(1), hasItem("second")).
       ..
```

路径参数遵循Java的标准[格式语法](http://download.oracle.com/javase/1,5.0/docs/api/java/util/Formatter.html#syntax)。

注意`withArgs`方法可以从[io.restassured.RestAssured](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/RestAssured.html)类中静态导入。

有时当所有在根路径中指定的参数都已经验证过了，只想要验证一个不含多余参数的body时，可以使用`withNoArgs`：
```java
when().
         get("/jsonStore").
then().
         root("store.%s", withArgs("book")).
         body("category.size()", equalTo(4)).
         appendRoot("%s.%s", withArgs("author", "size()")).
         body(withNoArgs(), equalTo(4));
```

# Session支持 #
rest-assured提供了一套简单的管理session的方式。您可以在DSL（译者注：领域特定语言）中预定义一个session的id值：
```java
given().sessionId("1234"). .. 
```

上面的代码实际上是这个的简写：
```java
given().cookie("JSESSIONID", "1234"). .. 
```

您也可以为所有的请求指定一个默认的`sessionId`：
```java
RestAssured.sessionId = "1234";
```

默认情况下session id的名字是`JSESSIONID`，但是您可以通过使用[SessionConfig](#session-config)来修改：
```java
RestAssured.config = RestAssured.config().sessionConfig(new SessionConfig().sessionIdName("phpsessionid"));
```

您也可以指定一个sessionid并在其它用例中复用，使用`RequestSpecBuilder`：
```java
RequestSpecBuilder spec = new RequestSpecBuilder().setSessionId("value1").build();
   
// Make the first request with session id equal to value1
given().spec(spec). .. 
// Make the second request with session id equal to value1
given().spec(spec). .. 
```

从响应对象中获取一个session id：
```java
String sessionId = get("/something").sessionId();
```

## Session过滤器 ##
2.0.0版本起您可以使用[session filter](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/filter/session/SessionFilter.html)截获并提供一个session，举个例子：
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

要想获取由`SessionFilter`截获的session id：
```java
String sessionId = sessionFilter.getSessionId();
```

# SSL #

在多数场景下，SSL能顺利运转，这多亏于HTTP Builder和HTTP Client。如果服务端使用了无效的证书，然而有些例子下还是会出错。最简单的方法您可以使用"relaxed HTTPs validation"：
```java
given().relaxedHTTPSValidation().when().get("https://some_server.com"). .. 
```
也可以为所有的请求静态定义这个配置：
```java
RestAssured.useRelaxedHTTPSValidation();
```
或者放在请求指定中进行复用。

这将会假定我们使用SSL协议。想要改变需要覆盖`relaxedHTTPSValidation`方法：

```java
given().relaxedHTTPSValidation("TLS").when().get("https://some_server.com"). .. 
```

您也可以创建一个Java keystore文件并执行一些细粒度的操作。这并不难，首先阅读[指南](https://github.com/jgritman/httpbuilder/wiki/SSL)，然后在rest-assured中使用keystore：

```java
given().keystore("/pathToJksInClassPath", <password>). .. 
```

或者为每一个请求指定：

```java
RestAssured.keystore("/pathToJksInClassPath", <password>);
```

您也可以定义一个[可复用的](http://code.google.com/p/rest-assured/wiki/Usage#Specification_Re-use)keystore。

如果您已经使用密码载入了一个keystore，可以将其作为可信的keystore：
```java
RestAssured.trustStore(keystore);
```

在[这](https://github.com/rest-assured/rest-assured/blob/master/examples/rest-assured-itest-java/src/test/java/io/restassured/itest/java/SSLTest.java)也可以找到一些相关的示例。

有关更多SSL的高级配置参阅[SSL Configuration](#ssl配置)。

## SSL 无效主机名 ##
如果证书指定了一个无效的主机名，并且您无需创建和导入一个keystore。自2.2.0起您可以：
```java
RestAssured.config = RestAssured.config().sslConfig(sslConfig().allowAllHostnames());
```

让所有请求支持所有的主机名。

```java
given().config(RestAssured.config().sslConfig(sslConfig().allowAllHostnames()). .. ;
```
对于单个请求。

注意如果您使用了"relaxed HTTPs validation"，那么`allowAllHostnames`将会默认启用。

# URL 编码 #
由于rest-assured提供了优秀的自动编码，通常您无需考虑URL编码的事情。在有些用例中仍然需要关闭URL编码的功能。其中一个原因是在使用rest-assured之前可能你已经做过一次编码。为防止两次URL编码您需要告诉rest-assured禁用这个功能。

```java
String response = given().urlEncodingEnabled(false).get("https://jira.atlassian.com:443/rest/api/2.0.alpha1/search?jql=project%20=%20BAM%20AND%20issuetype%20=%20Bug").asString();
..
```

或者

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

## 参数配置 ##
[ParamConfig](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/config/ParamConfig.html) 允许您配置在“冲突”时，更新不同的参数。 默认情况下，所有参数都将合并，因此如果您执行以下操作：
  
```java
given().queryParam("param1", "value1").queryParam("param1", "value2").when().get("/x"). ...
```
  
REST Assured将发送一个查询字符串`param1 = value1＆param1 = value2`。 
如果这不是您想要的，你可以配置REST Assured为* replace *值代替：


```java
given().
        config(config().paramConfig(paramConfig().queryParamsUpdateStrategy(REPLACE))).
        queryParam("param1", "value1").
        queryParam("param1", "value2").
when().
        get("/x"). ..
```

REST Assured现在将替换`param1`的值为`value2`（因为它是最后写的），而不是将它们合并在一起。 您也可以为所有参数类型的每种类型配置统一的更新策略

```java
given().config(config().paramConfig(paramConfig().replaceAllParameters())). ..
```

也支持在[Spring Mock Mvc模块]（# Spring Mock Mvc 模块）（配置[MockMvcParamConfig]（http://static.javadoc.io/io.restassured/spring-mock -mvc / 3.0.1 / io / restassured / module / mockmvc / config / MockMvcParamConfig.html）。


# Spring Mock Mvc 模块 #

REST Assured 2.2.0引入了对[Spring Mock Mvc]（http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/web/servlet/MockMvc.html）的支持，使用 `spring-mock-mvc`模块。 这意味着您可以单元测试Spring Mvc控制器。 例如给出以下Spring控制器：

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

你可以使用[RestAssuredMockMvc]（http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/RestAssuredMockMvc.html）来测试它，像这样：

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

即它与标准的REST Assured语法非常相似。 这使得运行测试真的很快，并且比标准的REST Assured更容易引导环境和使用mock。 你常用的标准REST Assured中的大多数东西都可以使用RestAssured Mock Mvc。 例如（某些）配置，静态规范，日志等等。要使用它，你需要依赖于Spring Mock Mvc模块：

```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>spring-mock-mvc</artifactId>
      <version>3.0.1</version>
      <scope>test</scope>
</dependency>
```

或者[下载]（http://dl.bintray.com/johanhaleby/generic/spring-mock-mvc-3.0.1-dist.zip）。

## Bootstrapping RestAssuredMockMvc ##

静态导入方法:
```java
io.restassured.module.mockmvc.RestAssuredMockMvc.*
io.restassured.module.mockmvc.matcher.RestAssuredMockMvcMatchers.*
```



有关其他静态导入，请参阅文档的[静态导入]（＃static-imports）部分。

为了使用RestAssuredMockMvc启动测试，您需要使用一组控制器，MockMvc实例或Spring的WebApplicationContext来初始化它。您可以对单个请求执行此操作，如上例所示：


```java
given().standaloneSetup(new GreetingController()). ..
```

也可以使用静态方法:

```java
RestAssuredMockMvc.standaloneSetup(new GreetingController());
```

如果静态定义，则不必在DSL中指定任何控制器（或MockMvc或WebApplicationContext实例）。这意味着前面的例子可以写成：

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

## 异步请求 ##

从版本`2.5.0` RestAssuredMockMvc支持异步请求。例如，假设有以下控制器

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

你可以这样测试：

```java
given().
        body("a string").
when().
        async().post("/stringBody").
then().
        body(equalTo("a string"));
```

默认超时为1秒。也可以使用DSL更改超时：

```java
given().
        body("a string").
when().
        async().with().timeout(20, TimeUnit.SECONDS).post("/stringBody").
then().
        body(equalTo("a string"));    
```
还可以使用[AsyncConfig](http://static.javadoc.io/io.restassured/spring-mock-mvc/2.4.1/io/restassured/module/mockmvc/config/AsyncConfig.html)），例如：

```java
given().
        config(config().asyncConfig(withTimeout(100, TimeUnit.MILLISECONDS))).
        body("a string").
when().
        async().post("/stringBody").
then().
        body(equalTo("a string"));
```


`withTimeout`是从`io.restassured.module.mockmvc.config.AsyncConfig`静态导入的，只是创建一个具有给定超时的`AsyncConfig`的快捷方式。全局应用配置以应用于所有请求：

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

请求1和2现在将使用默认超时100毫秒。

## 添加请求后处理器 ##
Spring MockMvc已经对[请求后处理器](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/request/RequestPostProcessor.html)做了支持，并且您也可以在RestAssuredMockMvc中使用。举个例子：
```java
given().postProcessors(myPostProcessor1, myPostProcessor2). ..
```

请注意，推荐从`org.springframework.security.test.web.servlet.request.SecurityMockMvcRequestPostProcessors`(例如认证相关的 `RequestPostProcessors`)加入请求后处理器时，直接使用`auth`方法，这样可读性更强，当然结果是一样的：
```java
given().auth().with(httpBasic("username", "password")). ..
```
这里的httpBasic静态导入自[SecurityMockMvcRequestPostProcessor](http://docs.spring.io/autorepo/docs/spring-security/current/apidocs/org/springframework/security/test/web/servlet/request/SecurityMockMvcRequestPostProcessors.html)。



## 添加结果处理器 ##
Spring MockMvc对[结果处理器](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/ResultHandler.html) 做了支持，您也可以在RestAssuredMockMvc中使用。比方说您想要使用原生的MockMvc日志功能：

```java
.. .then().apply(print()). .. 
```

这里的`print`静态导入自`org.springframework.test.web.servlet.result.MockMvcResultHandlers`。请注意如果您正在使用2.6.0或更早版本的rest-assured，需要这样使用结果处理器：

```java
given().resultHandlers(print()). .. 
```
但是rest-assured2.8.0起不推荐使用这种语法。

## 使用结果匹配器 ##
Spring MockMvc提供了许多[结果匹配器](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/ResultMatcher.html)，您可以从中获益。RestAssuredMockMvc也对其中必要的功能进行支持。举个例子，基于某种原因您想要使用结果匹配器验证状态码是否等于200：
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
这里的`status`静态导入自`org.springframework.test.web.servlet.result.MockMvcResultMatchers`。注意，您可以使用`expect`方法，功能上和`assertThat`一样，但是更接近原生的MockMvc的语法。

## 拦截器 ##
您也可以在请求（译者注：这里指mock请求）曝光之前截住并改变[MockHttpServletRequestBuilder](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/request/MockHttpServletRequestBuilder.html)。您需要先定义一个[MockHttpServletRequestBuilderInterceptor](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/intercept/MockHttpServletRequestBuilderInterceptor.html)，并在RestAssuredMockMvc中使用：

```java
given().interceptor(myInterceptor). ..
```

## Specifications ##


正如标准的Res​​t Assured，你可以使用[specifications](#specification_re-use)，以便更好地重用。请注意，RestAssuredMockMvc的请求规范构建器称为[MockMvcRequestSpecBuilder](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/specification/MockMvcRequestSpecBuilder.html)。同样的[ResponseSpecBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/builder/ResponseSpecBuilder.html) 也可以在RestAssuredMockMvc中使用。规格可以静态定义，就像标准的Res​​t Assured一样。例如：

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

## 重置 RestAssuredMockMvc ##

如果您使用了任何静态配置，您可以通过调用RestAssuredMockMvc.reset（）方法轻松地将RestAssuredMockMvc重置为其默认状态。

## Spring MVC认证 ##

`spring-mock-mvc`的版本`2.3.0'支持身份验证。例如：

```java
given().auth().principal(..). ..
```

一些认证方法需要Spring安全在类路径（可选）。也可以静态定义认证：

```java
RestAssuredMockMvc.authentication = principal("username", "password");
```

其中`principal`方法是从[RestAssuredMockMvc](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/RestAssuredMockMvc.html)静态导入的。还可以在请求构建器中定义认证方案：

```java
MockMvcRequestSpecification spec = new MockMvcRequestSpecBuilder.setAuth(principal("username", "password")).build();
```

### 使用 Spring Security 测试 ###

从版本`2.5.0`也有更好的支持Spring Security。如果你在类路径中有`spring-security-test`，你可以这样做：

```java
given().auth().with(httpBasic("username", "password")). ..
```

其中`httpBasic`是从[SecurityMockMvcRequestPostProcessor](http://docs.spring.io/autorepo/docs/spring-security/current/apidocs/org/springframework/security/test/web/servlet/request/SecurityMockMvcRequestPostProcessors.html)静态导入的。这将对请求应用基本认证。为了这个工作，你需要应用[SecurityMockMvcConfigurer](http://docs.spring.io/autorepo/docs/spring-security/current/apidocs/org/springframework/security/test/web/servlet/setup/SecurityMockMvcConfigurers.html)到MockMvc实例。您可以手动执行此操作：

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

### 注入一个用户 ###


也可以使用Spring Security测试注释，例如[@WithMockUser](http://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#test-method-withmockuser)和 [@WithUserDetails](http://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#test-method-withuserdetails)。例如，假设您想测试此控制器：

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


您可以看到`userAware'方法需要一个 [User](http://docs.spring.io/autorepo/docs/spring-security/current/apidocs/org/springframework/security/core/userdetails/User.html) 作为参数，我们让Spring Security使用[@AuthenticationPrincipal](http://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/web/bind/annotation/AuthenticationPrincipal.html) 注入它。要生成测试用户，我们可以这样做：


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

注意，也可以不使用注释，而是使用[RequestPostProcessor](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/request/RequestPostProcessor.html) ，例如[SecurityMockMvcRequestPostProcessors#user(java.lang.String)](http://docs.spring.io/autorepo/docs/spring-security/4.0.0.RELEASE/apidocs/org/springframework/security/test/web/servlet/request/SecurityMockMvcRequestPostProcessors.html#user(java.lang.String)).


## 参数相关 ##

MockMvc没有区分不同类型的参数，所以`param`，`formParam`和`queryParam`目前只是委托给MockMvc中的param。 `formParam`自动添加`application / x-www-form-urlencoded`内容类型的头部，就像标准的Res​​t Assured一样。

# Scala支持 #

REST Assured 2.6.0引入了将“别名”添加到“then”方法的[scala-support](http://dl.bintray.com/johanhaleby/generic/scala-support-3.0.1-dist.zip)模块定义在[Response](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/response/Response.html)或[MockMvcResponse](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/response/MockMvcResponse.html)中调用“Then”。这样做的原因是`then`在将来可能是Scala中的保留关键字，并且当使用具有此名称的方法时，编译器会发出警告。要启用`Then`，只需从`scala-support`模块导入`io.restassured.module.scala.RestAssuredSupport.AddThenToResponse`类。例如：


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
注意：同样支持 [Spring Mock Mvc Module](#spring-mock-mvc-module).


可以像这样使用它：

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
手动下载 [distribution file](http://dl.bintray.com/johanhaleby/generic/scala-support-3.0.1-dist.zip) 。

# Kotlin支持 #

Kotlin是由[JetBrains](https://www.jetbrains.com/)开发的一种语言，它与Java和REST Assured非常好地集成。当使用它与REST Assured有一件事，必须逃避`when`，因为它是Kotlin中的保留关键字。例如：

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


为了解决这个问题，创建一个[extension function](https://kotlinlang.org/docs/reference/extensions.html)，创建一个别名为`when`时叫做`When`：

```kotlin
fun RequestSpecification.When(): RequestSpecification {
    return this.`when`()
}
```

代码现在可以像这样写：

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

注意，我们不需要任何转义。有关更多详细信息，请参阅[this](http://code.haleby.se/2015/11/06/rest-assured-with-kotlin/)博客文章。

# 更多信息 #
其他相关信息，请参考 [javadoc](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/index.html):
  * [RestAssured](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/RestAssured.html)
  * [RestAssuredMockMvc Javadoc](http://static.javadoc.io/io.restassured/spring-mock-mvc/3.0.1/io/restassured/module/mockmvc/RestAssuredMockMvc.html)
  * [Specification package](http://static.javadoc.io/io.rest-assured/rest-assured/3.0.1/io/restassured/specification/package-summary.html)

一些代码示例:
  * REST Assured [tests](https://github.com/rest-assured/rest-assured/tree/master/examples/rest-assured-itest-java/src/test/java/io/restassured/itest/java)
  * [JsonPathTest](https://github.com/rest-assured/rest-assured/blob/master/json-path/src/test/java/io/restassured/path/json/JsonPathTest.java)
  * [XmlPathTest](https://github.com/rest-assured/rest-assured/blob/master/xml-path/src/test/java/io/restassured/path/xml/XmlPathTest.java)

如果你需要支持，可以加入 [mailing list](http://groups.google.com/group/rest-assured).

如果需要专业支持，请联系 [johanhaleby](https://github.com/johanhaleby).
