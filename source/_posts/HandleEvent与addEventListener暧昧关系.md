---
title: HandleEvent与addEventListener暧昧关系
date: 2016-11-20 17:36:50
tags: javascript
categories: 技术
---
> 减肥就是一个自我虐待的过程。

“每个胖子都是潜力股”，看到一美工同事的变化，这话我信了。所以，我也开始了减肥这条不归路。写这篇以及以后的很多篇文章的时候，我都可能处在肚子咕咕叫或者头晕无力的状态之下（至少目前是这样），所以时不时会发生断片，当然希望真不是晕倒了。好了，回归正题，今天在写个东西的时候，发现了以前在某处绑定事件时用到过得一个属性handleEvent，于是为了打发这无聊的下雨天，就稍微给这个属性做个简短记录。
说起handleEvent，往往不得不提一下addEventListener，addEventListener 是dom上的一个事件绑定方法，我们熟悉的模式大概是这样

    dom.addEventListener(type,fn,bool);
    
其中fn为一个回调函数，里面写的都是事件触发后需要执行的一些逻辑。但是在DOM2的标准中除了fn是一个函数外，也可以是个对象（object），但是这个object必须要有handleEvent方法，通过这样的方式，可以把任何对象注册为事件处理的逻辑。简单实现方式：
<!--more-->
    var o = {
        ...   //该对象的其他方法
        handleEvent:function(evt){
            //to do;
            //evt为事件对象
        }
        ....
    };
    dom.addEventListener(type,o,bool);
    
基本实现就是上面方式，上面方式使我们在处理某些复杂的操作时，可以更有条理的组织处理逻辑。之前我们如果要改变事件时，基本都需要先remove掉当前的绑定，然后在add新的绑定，但是有了这个方法之后，我们就不需要这么做了，只需要动态的改变handleEvent事件处理器，就可以做到：

    o.handleEvent = o.handler2; 
    /
    o.handleEvent = fun;

看看确实省了不少工作，不用再去纠结我绑了哪个，需要remove掉哪个了，而且它跟aaddEventListener基本共生的，也就是能用addEventListener地方，你可以放心的使用它，不能用addEventListener，就不要挣扎了。不过，如果你在使用的时候，忽然发现，this不再指向dom的时候，那么恭喜你，你终于发现它的缺陷了，不过，这个时候你应该控制台输出下this,会看到this具体是哪个对象，机灵的人可能会很快发现上面我们传的是对象，handleEvent是对象的一个属性，那么它的this必然是指向object本身，所以，这个时候，就稍微注意下，具体怎么改进自己的代码，根据实际情况，想出一个完善的解决方案，我这里就不多说了，毕竟，方法是死的，人是活的。