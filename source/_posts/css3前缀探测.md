---
title: css3前缀探测
date: 2016-11-29 19:32:11
tags: javascript
categories: 技术
---
> 有一种生无可恋叫牙痛。

这些天，牙痛的我对人生都产生了怀疑，做什么事都感觉力不存心的，今天稍微感觉好了点，于是就整理了一下这段时间项目中感觉可以记录的一些东西。首先来说说关于css前缀的问题。
在css3盛行的今天，每个人都会在自己的代码（css/js）中或多或少的加入一些css3样式，前者大部分都是混迹于移动端的，后者可能更多的主攻pc端的，但是无论什么，总会遇到关于css前缀的问题，有时候可能在css文件中涉及，有时候也可能在js文件中涉及。在css文件中（这里不提less，sass等一些预处理语言），我们为了解决不同厂子浏览器兼容问题，会写出类似这样的代码：

    .example{
        -webkit-box-shadow: 2px 2px 8px rgba(0, 0, 0, .1);
        -moz-box-shadow: 2px 2px 8px rgba(0, 0, 0, .1);
        -o-box-shadow: 2px 2px 8px rgba(0, 0, 0, .1);
        box-shadow: 2px 2px 8px rgba(0, 0, 0, .1);
    }
    
然后基本就解决了浏览器兼容问题。那么如果在js中涉及到样式问题呢？<!--more-->例如在写某个滑动或者特效的时候，我们还要这样一下子赋予元素这么多吗？答案当然是...可要可不要。原谅我说了这么一句废话，写这么多其实也可以，没啥毛病，但是身为一个有志青年，毕竟要做到代码与己方便，与人方便，一个样式就要写那么一坨代码，自己都看不下去。然后那些牛逼的大神就想出了各种简化方式，解决方案基本大同小异，都是检测当前浏览器支持那种前缀，从而只写对应前缀的样式，省了不少事。下面我们就来看看这些具体的实现方式。
##方式一： 元素特性探测
这种方式，就是通过给元素加入一些css3的高级属性，然后检测元素具体最终支持哪个，代码如下：

    var prefix = function() {
        var div = document.createElement('div');
        var cssText = '-webkit-transition:all .1s; -moz-transition:all .1s; -o-transition:all .1s; -ms-transition:all .1s; transition:all .1s;';
        div.style.cssText = cssText;
        var style = div.style;
        if (style.transition) {
            return '';
        }
        if (style.webkitTransition) {
            return 'webkit';
        }
        if (style.mozTransition) {
            return 'moz';
        }
        if (style.oTransition) {
            return 'O';
        }
        if (style.msTransition) {
            return 'ms';
        }
    }();
## 方式二： 元素默认样式匹配
其实这个方法跟上面差不多，不过不用自己定义一些样式后，载检测，由于浏览器对于每个元素都有默认所有属性，所以完全可以匹配样式属性是否包含在元素样式对象中，有两种方式，分别如下：
### 1. 
 

    var prefix = (function () {
        var style = window.getComputedStyle(document.documentElement, ''),
        pre = (Array.prototype.slice
              .call(style)
              .join('') 
              .match(/-(moz|webkit|ms)-/)
              )[1]
        return pre
    })();
### 2.
  	var prefix = (function(){
  	    var style = document.createElement('div').style;
  		var preNames = [ "webkitT", "MozT", "msT", "OT","t"],
  			transform,
  			i =preNames.length;

  		while(i--){
  			transform = preNames[i] + "ransform";
  			if (transform in style) return preNames[i].substr(0, preNames[i].length - 1);
  		}
  	})();

以上几种方法根据自己编码习惯选择，不过小一中获取元素样式属性方法存在兼容性问题，之前文章中有做过介绍，就不再详述。