 # javascript 无块级作用域引发循环绑定问题的探讨 #

 javascript 中一个比较经典的问题-**循环绑定问题**大家对这个问题也一定不陌生，最近也有同事问我缘由，今天我就粗浅的分析下。

## 问题的产生 ##

假设，我们对一组列表进行事件绑定，我们可以用一个函数或者统一的事件处理器进行事件响应。此时，我们就对这组按钮进循环绑定事件。

     `<ul>
     		<li>
     			<p class="number">1000</p>
     			<p class="pay">购买起点（元） </p>
     		</li>`
     		`<li>
     			<p class="number">24.87%</p>
     			<p class="pay">近一年收益率 </p>
     		</li>
     </ul>`
     `<div class="egg_btn">
     	<a href="">立即购买</a>
     </div>`

    var ulNode = document.querySelector('ul');
    var liNodes = document.querySelectorAll("li");


    
下面我们需要循环对liNodes节点进行遍历，对其中的每一个节点进行事件绑定。

    for(var i=0;i<liNodes.length;++i){
	    liNodes[i].onclick = function(event){
			var pNode =liNodes[i].firstElementChild;
		    pNode.innerHTML = i;
	    };
    }	

这段代码看上去不会有什么问题，但是，当我们点击任意选项时，程序报错.问题的原因：JavaScript没有块级作用域，计数器变量i 存在上一层作用域中，即使for循环结束，也依旧会存在于循环外部的执行环境中。所以，i一直是为liNodes.length 的值，即退出循环的值。

## 解决问题的几种经典方案 ##



* 事件委托
* 函数闭包
* ES6中新的定义变量方式let 


### 事件委托 ###


针对要处理的元素不必每个绑定一个onclick事件，利用事件委托，只需要在DOM树的尽量最高的层次添加一个事件处理程序，（一般是在父节点上）一般是添加事件之前，这个节点已经存在，这样，还可以对于动态添加的子节点仍然能触发事件。  

    ulNode.onclick=function(event){
	    var event = event || window.event;
	    var target = event.target || event.srcElement;
	    var pNode =target.firstElementChild;
	    pNode.innerHTML = "hello";
    }

Jquery中利用事件委托来绑定事件的方法有很多，如：**bind,live,delegate,on**，也注意它们之间的区别，请看我的其他文章。


### 函数闭包 ###

函数闭包也是前端一个很重要的概念。js高级程序设计书中是这样定义的。闭包是指有权访问另一个函数作用域中的变量的函数。创建闭包的常用方式，就是在一个函数内部创建另一个函数。从上面的定义，函数闭包之所以能解决我们讨论的问题，根本还是改变了变量的作用域。在内部定义的函数会把外部函数的活动对象添加到它的作用域链中。
    
    for(var i=0;i<liNodes.length;++i){
	    liNodes[i].onclick = (function(i){
		    return  function(){
		    pNode.innerHTML = i;
		    }
	    })(i);
    
    }
    // 全局参数定义
	var config = {
		map: {
			jsURI: [
				'http://web.com/new_public/header_footer.js',
				'http://web.com/device.min.js',
				'http://web.com/app_js/stock/lib/jquery-1.8.3.min.js'
			],
			cssURI: [
				'http://web.com/new_public/header_footer.css',
				'http://web.com/lib/normalize.min.css'
			],
		}

	};
	load: function(callback) {
		var loadFun = callback;
		
		for ( var i = 0;  i < config.map.jsURI.length; i++ ) {
			var jsUrl = config.map.jsURI[i];
			loadFun = (function(url, callback){
				return function(){ loader.loadScript(url, callback); };
			})(jsUrl, loadFun);
		}
		
		for ( var i = 0;  i < config.map.cssURI.length; i++ ) {
			var cssUrl = config.map.cssURI[i];
			loadFun = (function(url, callback){
				return function(){ loader.loadCss(url, callback); };
			})(cssUrl, loadFun);
		}
		loadFun();
	}

还有一些项目中要动态加载文件的时候，也是需要函数闭包的来实现。

### ES6中定义变量方式let ###

2015年最火的可能就是es6的问世，它也给我们带来了全新的定义变量和常量的方式**let**和**const**。使用let定义的变量能形成块级作用域，上述的问题就可以从根本解决。

     for(let /* var */ i=0;i<liNodes.length;++i){
    	// ...
    }

但截至目前，大部分浏览器还不支持这种语法，但是，**Babel**等可以将其转换为ES5语法。
