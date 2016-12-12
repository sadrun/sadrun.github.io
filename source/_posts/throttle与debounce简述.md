---
title: throttle与debounce简述
date: 2016-11-30 19:35:16
tags: javascript
categories: 技术
---
> 这是你第一次秀恩爱，你说：你要结婚了。

人生处处是意外，处处是无奈。有些事，过去了就过去了，过不去，就是遗憾了。不扯了，越扯感觉越蓝瘦。回归正题，来说说js中throttle与debounce。
throttle，词如其名，就是阀门的意思，当然更确切的叫节流阀。它主要是用来防止在较短时间内多次调用某个方法导致浏览器性能降低。debounce，中文译为防反跳，防抖之类的（想象一下你把一个弹簧压下去一直不动，直到你放手的时候，它才恢复原状的那个样子），他主要是防止多次触发同样的事件而导致多次调用同一个方法，发生一些让人意外的结果。throttle多用于类似滚动，拖动，滑动之类的，而debounce多用于频率不那么高的事件（鉴于debounce我没怎么用过，所以你懂得）。下面来看看具体实现吧：<!--more-->
throttle：

    function throttle(delay,callback){//delay间隔内必然执行一次
    	var last,//上次调用时间
    		timer = null;
    	return function(){
    		var curr = Date.now(),//当前调用时间
    			context = this,
    			args = arguments;
    		clearTimeout(timer);//清除上个定时器
    		if(!last){
    			last = curr;//第一次调用赋值
    		}
    		if((curr - last) > delay){//若时间间隔大于给定延时，则立即调用回调
    			callback.apply(context,args);
    			last = curr;//将最后一次时间改为当前时间
    		}else{//如果时间间隔较小，则延时执行
    			timer = setTimeout(function(){
    				callback.apply(context,args);
    			},delay);
    		}
    	}
    }
具体注释已给出，应该都明白了。在最近写的一个弹性滚动案例中，就用到这个方式（后边文章会介绍下怎么写的），由于在移动端不断触摸滑动时，touchmove在不停的触发（好像每秒有几百次，具体多少其实我也不清楚），如果在每次touchmove之后都去操作元素位置，这样在移动端那脆弱的小生命有点吃不消，会出现卡顿之类的，所以，节流在这个时候显得很重要，算是救命仙药。上面给出节流写法只是其中一种，还有好多实现方式，当然大同小异，不过更精细的实现适用性比较高，有兴趣的话可参照underscore.js中的实现方式。
debounce：

    var debounce = function(delay, callback){
      var timer;
      return function(){
        var ctx = this, args = arguments
        clearTimeout(timer)
        timer = setTimeout(function(){
            callback.apply(ctx, args)
        }, delay)
      }
    }
上面是一个debounce的简单实现，在实际中你可以用于监听用户输入发送ajax请求频率控制，减少服务端压力，也可以控制多次点击按钮触发一些多次提交之类的问题。
最后再说明一点，throttle和debounce必须要用在适合的场景才能发挥它最大功效，切记不要在该用throttle的时候你用了debounce，这样的后果是相当糟糕的，假设你在touchmove或者mousemove一个元素的时候，你用了debounce模式，那它很可能就不是连续的动画了，很可能就变成跳跳猪了，产品会提刀来见你。本文只是对这两个设计思路的一种具体实现，实现方式因人而异，只要在主旨思想内就行。