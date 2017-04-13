### Java中的异常



#### 使用try-catch去捕获异常

[原文地址](http://stackoverflow.com/documentation/java/89/exceptions-and-exception-handling/385/catching-an-exception-with-try-catch#t=201704131510286518909)

一个异常可以使用``try...catch``语句去捕获和处理(实际上，try语句使用另一种被称为`try...catch...finally` 和 `try-with-resources`的形式， 详情可见另两篇)

##### 仅使用一条Try-catch语句

`````java
try{
  doSomething();
}catch(SomeException e){
	handle(e);
}
// next statement
`````

一条简单的`try...catch`语句的运行规则如下：

* `try`语句内的语句被执行

* 如果`try`语句内没有抛出任何异常，接下来继续执行`try...catch`后的语句，省略`catch`语句

* 如果一条异常在`try`语句内被抛出

  * 这个异常会被检测能否是`SomeException`或者是他的子类类型
  * 如果是，那么`catch`语句会被匹配
    * 变量`e`会被绑定到异常对象
    * `catch`语句内的代码将会被执行
    * 如果代码继续抛出异常，那么新的异常将会代替原来产生的一场继续传播
    * 除此之外，继续执行`try...catch`后的语句


* 如果异常不是`SomeException`或其子类类型，原来的异常将会继续被抛出。


一条`try...catch`语句可以搭配多条`catch`语句块，例如：

``````java
try{
  doSomething();
}catch (SomeException e){
	handleOneWay(e)
}catch (SomeException e){
  	handleAnotherWay(e)
}
//next statement
``````

如果存在多条`catch`语句，将会从第一条语句开始逐次尝试，直到异常与`catch`匹配成功，与之对应的`catch`语句将会被执行，并且将会进入`try...catch`语句的下一条语句。余下的`catch`语句会被跳过，即使处理器中抛出了一条异常。

【这种”自上而下“的匹配机制造成了一种在`catch`语句内部的异常不相交的后果。】(存疑)

``````java
try{
  throw new RuntimeException ("test");
} catch (Exception e){
  	System.out.println("Exception");
} catch (RuntimeException e){
  	System.out.println("RuntimeException");
}
``````

这段代码将会打印出“Exception”而不是“RuntimeException”。因为`RuntimeException`是`Exception`的子类，第一个(更宽泛)的`catch`语句将会被匹配，而第二个(更具体的)`catch`语句将永远不会被执行。

从这里我们可以学到更具体的错误类型的`catch`语句(根据异常类型)应该首先出现，而更为宽泛的`catch`语句则应放在最后。(如果一个`catch`语句永远不会被执行，一些Java编译器将会发出警告，但这不属于编译错误)

##### 多重异常catch块

在Java SE 7 中，一个单个`catch`块可以处理一系列的无关的异常。异常类型以`|`分隔而列举出来。例如：

``````java
try{
  	doSomething();
} catch(SomeException | SomeOtherException e ){
	handleSomeException(e);
}
``````

多重异常的catch语句行为是对处理单个异常的简单的扩展。如果被抛出的异常至少匹配到列出的异常中的一种，那么`catch`语句即为匹配成功。

