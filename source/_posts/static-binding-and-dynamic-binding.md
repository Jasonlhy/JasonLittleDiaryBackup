title: static binding and dynamic binding
date: 2016-04-23 20:01:06
description:
categories:
- Note
tags:
- Programming Principle
---

# Example
Copied from [https://www.emacswiki.org/emacs/DynamicBindingVsLexicalBinding](https://www.emacswiki.org/emacs/DynamicBindingVsLexicalBinding)
{% codeblock lang:lisp %}
    (let ((a 1))                            ; binding (1)
      (let ((f (lambda () (print a))))
        (let ((a 2))                        ; binding (2)
          (funcall f))))
{% endcodeblock %}

{% quote %}
A name that is lexically bound is looked up only in bindings in the lexical environment of the name – that is, in bindings that enclose the name in the source code. So if “a” is lexically bound, the code above prints “1”, because only binding (1) is in the lexical environment. When there are multiple bindings in the lexical environment, the innermost one is used.

A name that is dynamically bound is looked up only in bindings in the dynamic environment of the name – that is, in all bindings which have been created since the program began and which have not yet been destroyed. When there are multiple bindings in the dynamic environment, the most recently created one is used. So if “a” is dynamically bound, the code above prints “2” because both binding (1) and binding (2) have been created by the time “a” is evaluated, but binding (2) was created more recently.

(In a multi-threaded Lisp we would have to be a bit more careful about dynamic binding to make sure that one thread doesn’t see bindings created on another thread. But EmacsLisp is single-threaded so that’s not a worry.)
{% endquote %}

# Static binding
又叫`lexical binding`
呼叫variable時侯尋找scope內裡的variable value
compile 時巳經決定了使用那個variable

# Dynamic binding
又叫`late binding`，呼叫variable時根據runtime sequence去尋找value
好的地方可以在runtime 時改變variable的數值，又或者改變function 的implementation

# Fluid binding
兩個合體, 平常為static binding , 但可以指定某個scope 內有dynamic binding

# 總結
現代的programming language 多算是lexical binding 為主
但會提供一些方法去實作dynamic binding
