---
title: 重名就会被覆盖？那JavaScript中是如何实现重载的呢？
date: 2018-07-13 14:15:22
tags:
  - js
  - 重载
categories:
    blog  
---

![--](http://p7jj1bbaq.bkt.clouddn.com/1648d606a47dc799.png)

> 大家都知道，所谓重载，就是一组相同的函数名，有不同个数的参数，在使用时调用一个函数名，传入不同参数，根据你的参数个数，来决定使用不同的函数！重载这个在JAVA这些经典的编程语言里面都很好用，可以说调用同一个方法名用不同的参数就可以为所欲为了。



但是我们知道JavaScript中是没有重载的（为什么没重载？不是JAVA的特性JavaScript也会有的吗？），因为后面定义的函数会覆盖前面的同名函数，但是重载那么好用，我们想在JavaScript实现函数重载该怎么办呢？

实现javascript重载一般有两种方式：

## 第一张方式

这种方法比较简单，给一个思路，大家肯定都能理解，就是函数内部用switch语句，根据传入参数的个数调用不同的case语句，从而功能上达到重载的效果。这种方法简单粗暴。但是对于一个正在学习js的人来说，这种方法未免太敷衍了。（那么没技术含量，怎么能让我变成前端开发大神呢？）

下面重点介绍一下第二种，老实说我第一次看的时候很吃力，看了一个小时才捋清楚，因为有的知识点虽然看过了但是不熟悉。（干货很足，别看走神）

## 第二种方法

我们这个例子，是如果你不传入参数，就会输出所有的人，输入firstname，就会输出匹配的人，如果输入全名的人，也会输出匹配的人。如果用重载的话，用户体验确实会很好（这个例子是我以前学习时从网上扒下来的，很经典，具有代表性，但是他们都没有写实现过程，所以今天我来给大家说讲一下）

``` javascript
// 第二种实现重载
functuin method(obj, name, fnc){
  var old = obj[name];
  console.log(old, instanceof Function);

  obj[name] = function(){
    console.log(arguments.length + " " + fnc.length);
    if(arguments.length === fnc.length){
      return fnc.apply(this, arguments);
    }else if(typeog old === "function"){
      return old.apply(this, arguments);
    }
  }
}

// 顶一个用于查找和输出的数组
var people = {
  values : ["Zhang san","Li si","Wang wu"]
};

```
``` javascript
// 重载实现无参数查找方法，结果输出所有人
method(people, "find", function(){
  console.log("无参数");
  return this.values;
})

// 重载一个参数的查找方法，输入firstname参数，找到匹配该firstname的人
method(people,"find", function(firstname){
  console.log("一个参数");
  var ret = [];
  for(var i = 0 ; i < this.values.length ; i ++){
    if(this.values[i].indexOf(firstname) === 0){
      ret.push(this.values[i]);
    }
  }

  return ret;
})

//重载两个参数的查找方法，输入全名，找到对应的人
method(people, "find", function(firstname, lastname){
  console.log("两个参数");
  var ret = [];

  for(val i = 0 ; i < this.values.length ; i ++){
    if(this.values[i] == firstname + " " + lastname){
      ret.push(this.values[i]);
    }
  }

  return ret;
})


console.log(people.find());
console.log(people.find("Zhang"));
```

### 思路

这段代码第一眼看的时候肯定是懵的，再看一次好像有点思路，再看就又懵了。其实呢，这种方法巧妙的运用了JavaScript的闭包原理（重点），既然js后面的函数会覆盖前面的同名函数，我就强行让所有的函数都留在内存里，等我需要的时候再去找它。有了这个想法，是不是就想到了闭包，函数外访问函数内的变量，从而使函数留在内存中不被删除。这就是闭包的核心作用。

### 实现过程

从上面代码来看，最重要的就是`method`方法的定义：这个方法中最重要的一点就是这个`old`，在这里`old`的设置很巧妙。他的作用相当于一个指针，指向上一次调用的`method`函数，具体从代码中来说。先看js中的解析顺序来看：

1. 解析`method`（先忽略里面的东西）
2. 执行`method(people, "find", function())`的时候，他会去执行上面定义的方法，然后此时的`old`值为空。因为你还没有定义这个函数，所以它此时是`undefined`然后继续执行，这时候我们才定义`object[name] = function()`。这时候js解析的时候发现返回了`fnc`函数，更重要的是`fnc`函数里面还调用了`method`里面的变量，这样就构成了闭包。
因为`fnc`函数的实现是在调用的时候才会去实现，所以js就想，这我执行完也不能删除，要不外面的用什么。就先留着吧（此处用`apply`函数改变了`fnc`函数的`this`指向）
3. 好了第一次的`method`的使用结束了，开始第二句，`method(peole, "find", function(firstname))`然后在这次使用的时候，又会去执行一次`old = obj[name]`。此事的`old`是什么，是函数了，因为上一次已经定义过了，且没有被删除，那么这次的`old`实际上是上次定义的方法，他起的作用好像一个指针，指向了上一次定义的`obj[name]`。然后再往下看，又是闭包，还得继续留着。
4. 第三局的`method`调用开始了，同理`old`指向的是上次定义的`obj[name]`,同样还是闭包，还得留着。
5. 到这里，内存中存在着三个`obj[name]`，因为三次`method`的内存都没有被删除，这是不是实现了三个函数共存，同时还可以用`old`将他们联系起来岂不是很妙。
6. 我们在`people.find()`的时候，就会最先调用最后一次调用的`method`时定义的`function`，如果参数个数相同也就是`arguments.length === fnc.length`那么执行就好了，也就无需找其他的函数了，如果不同，那就得用到`old`了最后`return old.apply(this, arguments);`。`old`指向的是上一次`method`调用时候定义的函数，所以我们就去上一次找，如果找到了，继续执行`arguments.length === fnv.length`如果找不到，再次调用`old`往上面找，只要你有定义就能找的到。

### 总结

运用闭包的原理使得三个函数共存于内存中，`old`相当于一个指针，指向上一次定义的`function`，每次调用的时候，决定是否需要寻找。

最后执行的结果

![console](http://p7jj1bbaq.bkt.clouddn.com/1648d5ff924ed332.png)
