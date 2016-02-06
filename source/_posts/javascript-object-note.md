title: javascript object筆記
date: 2016-01-27 07:20:42
description: 記錄一些javascript object用法和注意地方
categories:
tags:
---

# Object
javascript的object是key-value pair
因此key有時會被叫作property, member, etc...
value 可以是integer, true, false, function, etc 

## √ 定義
{% codeblock lang:javascript %}
var object = {} // empty object
var object = { name : my_nickname } // my_nickanme is the defined variable
var object = { 'name' : "jason" , 'job' : "sutdent" }   // hv ''
var object = { name : "jason", job : "student" }     // no ''
var object = { 名字 : "jason" }     // console.log(object.名字) -> jason
var object = { nick_name : "jason" }     // console.log(object.nick_name) -> jason
{% endcodeblock %}

## ✘ 定義
{% codeblock lang:javascript %}
var object = { my name : "jason" } // key 如有空格要''
var object = { nick-name : "jason" } // key 如有-要''
{% endcodeblock %}


## 存取Object成員(Access object's property)
利用`.`作分隔，注意提取未定義的property 會回傳undefined value
{% codeblock lang:javascript %}
var object = { name : "jason" };
object.name // jason
object.age // undefined  
{% endcodeblock %}

因此可以利用undefined value 檢查某object 成員有沒有被定義
{% codeblock %}
object.name === undefined // false
object.age === undefined // true
{% endcodeblock %}

也可以利用`[]` (Object literal), 適用於有-的key
{% codeblock %}
var object = { "my-name": "jason", "image-list" : "image.png" };
object.image-list; // reference error: list
object.my-name; // NaN
object["my-name"]; // jason
{% endcodeblock %}

Object 可以隋意新加成員
{% codeblock lang:javascript %}
var object = { name : "jason" };
object.nickname = "tresa";
object.nickname; // "tresa"
{% endcodeblock %}

# 檢查type
Javascript 使用String定義type
{% codeblock %}
var object = { name : "jason" };
typeof object; // "object"
{% endcodeblock %}

## 檢查Array
不過Array的type也是'object', 但可以利用Array.isArray()檢查 (ECMAScript >= 5)
{% codeblock %}
var array = ["jason", "object"]
typeof array; // "object" 
Array.isArray(array) // true
{% endcodeblock %}


## 檢查一個variable 有沒有value
可以利用typeof 去避免ReferenceError: Can't find variable: list
注意: 所有variable 即使定義了，沒有acess前value都是undefined

{% codeblock %}
var x;
typeof x; // "undefined"
typeof list // "undefined" (list is not "defined yet")
{% endcodeblock %}

## 另外方法檢查undefined

From [Mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)
{% quote %}
The global undefined property represents the primitive value undefined. It is one of JavaScript's primitive types.
{% endquote %}

由於undefined的type是'undefined'，因於檢查某成員有沒有被定義也可以用typeof
{% codeblock %}
var object = { name : "jason" };
typeof object.age === 'undefined' // true
{% endcodeblock %}



# 檢查Empty object
利用`for ... in ...` 檢定有沒有property name, 有則return true
{% codeblock lang:javascript %}
var emptyObject = {};
var student = { name : "jason", job : "student" };

function isEmptyObject(testObject){
  var property;
  for (property in testObject){
    return true;
  }
  
  return false;
}

isEmptyObject(emptyObject); // false
isEmptyObject(student); // true
{% endcodeblock %}

# Array

## 存取每個element
傳統方法, 利用`length`
{% codeblock %}
for (var i = 0 ; i < array.length; i++ ){
  console.log(array[i]);
}
{% endcodeblock %}

利用 `forEach` (ECMAScript >= 5)
接受 callback(element, index, processingArray)
{% codeblock lang:javascript %}
array.forEach(function(element, index, processingArray){
  console.log(element);
});
{% endcodeblock %}

利用`for ...of...` (ECMAScript >= 6)
{% codeblock lang:javascript %}
for (var e of array){
  console.log(e);
}
{% endcodeblock %}

# Reference
- [ECMAScript Compatibility table](http://kangax.github.io/compat-table/es5/)
- [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/)

