title: parameter passing
date: 2016-02-23 22:39:29
description:
categories:
- Note
tags:
- Javascript
- Programming Principle
---

主要分為兩種Evalulation first, or Evaluation when needed

# Evalulation first

## Call-By-Value
最common 的做法，先計算parameter 的value，然後把value copy 進function內
不會影響出面的數值

{% codeblock lang:cpp %}
#include <iostream>

using namespace std;

void addOne(int n){
    n = n + 1;
}

int main(){
    int x = 5;
    cout << "before function is called: " << x << endl;
    addOne(x);
    cout << "after function is called: " << x << endl;

    return 0;
}


Output:
before function is called: 5
after function is called: 5

{% endcodeblock %}

## Call-By-Reference
把呼叫者(caller)的variable 直接在function內使用, 但叫第二個名字（Reference)
可以有side effect 出面的數值

{% codeblock lang:cpp %}
#include <iostream>

using namespace std;

void addOne(int &n){
    n = n + 1;
}

int main(){
    int x = 5;
    cout << "before function is called: " << x << endl;
    addOne(x);
    cout << "after function is called: " << x << endl;

    return 0;
}


Output:
before function is called: 5
after function is called: 6

{% endcodeblock %}

# Evaluation when needed

## Call-By-Name
用的時侯才把parameter evaluate
在passing as parameter是產生closure (enclosing environement) 裝著evaluation所需的variable

以下add function 中的n 是call-by-name的，
`n` 是array(index), 當中的`index` 是記憶著上面的`index`

{% codeblock %}
object CallByName {
  var array = Array(0, 1, 2, 3)
  var index = 0 // 這是closure 內的variable
  
  def add(n: => Int) : Int = {
    return n;
  }
  
  def callAdd() = {
    add(array(index)) // 我連著上面的 index !!
  }
  
}

defined object CallByName

scala> CallByName.callAdd
res2: Int = 0
{% endcodeblock %}


所以如果closure 內的variable數值被外內改變
會影響evaluation的結果
{% codeblock %}
object CallByName {
  var array = Array(0, 1, 2, 3)
  var index = 0 
  
  def add(n: => Int) : Int = {
    index = 1; // 改變了closure 內的variable 數值
    return n;
  }
  
  def callAdd() = {
    add(array(index))
  }
  
}



defined object CallByName

scala> CallByName.callAdd
res3: Int = 1
{% endcodeblock %}


改變local variable 是沒有作用的
{% codeblock %}
object CallByName {
  var array = Array(0, 1, 2, 3)
  var index = 0
  
  def add(n: => Int) : Int = {
    var index = 1; // 這不是closure內的variable
    return n;
  }
  
  def callAdd() = {
    add(array(index))
  }
  
}

defined object CallByName

scala> CallByName.callAdd
res2: Int = 0

{% endcodeblock %}

### Clousre in Javascript
利用javascript 達成類似call-by-name的效果

{% codeblock lang:js %}
function CallByName(){
    var array = [0, 1, 2, 3];
    var index = 0;

    this.add = function(f){
        index = 1;
        return f();
    }

    this.callAdd = function(){
        return this.add(function(){
            return array[index];
        });
    }
}

var callByName = new CallByName();
console.log(callByName.callAdd()); // 1
{% endcodeblock %}

## Call-By-Need
Call-By-Name + memory, evalution 後把結果儲起來

## Call-By-Text
類似Call-By-Name ，但passing as parameter時沒有closure 裝著所需的variable
到evaluation 時才找所需的variable

個人認為javascript內的eval 是類似效果？
{% codeblock lang:js %}
function CallByText(){
    var array = [0, 1, 2, 3];
    var index = 0;

    this.add = function(n){
        var index = 1;
        return eval(n);
    }

    this.callAdd = function(){
        return this.add('array[index]');
    }
}

var callByName = new CallByName();
console.log(callByName.callAdd()); // 1
{% endcodeblock %}