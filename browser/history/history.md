# 历史记录管理

下面，介绍两种用于历史记录管理的方案，可以看到html5提供的history API确实带来了很多方便的地方。

## 方案1：简易的历史记录管理

这个方案的原理是利用location.hash和hashchange事件，实际上hashchange也是h5的事件，在IE的早期版本也是不支持的。因此要想达到一定的兼容性的化，大致的实现思路是利用一个定时器不断轮询location.hash的变化。

这种技术被比较广泛实现了。

1. 设置`location.hash`属性会更新显示在地址栏中的URL（#后的值），同时会在浏览器的历史记录中添加一条记录

2. hash属性设置URL的片段标识符，通常是用于指定要滚动到的文档中某一部分的ID，但是它也可以设置成任何的字符串

3. 可以将一个`应用状态编码成一个字符串`，就可以使用该字符串作为片段的标识符

4. 设置了location.hash属性后，就必须来检测状态的变化，以便它能够读取出存储在片段标识符中的状态并相应地更新自己的状态。

5. 利用`hashchange`事件，通过设置window.onhashchange为一个处理程序，在这个处理程序中对location.hash的值进行`解析`，然后使用该值包含的状态信息来重新显示应用

## 方案2：复杂的历史记录管理

这个方案的基础就是h5的history API，它包括了

* history.pushState()
* popstate事件
* replaceState()

实现思路如下：

1. 当一个Web应用进入一个新的状态的时候，它会调用history.pushSate()方法将该状态添加到浏览器的浏览历史记录中。

    history.pushState(object [,title] [,url] )

    * object:包含用于恢复当前文档状态所需的所有信息。

    * title:可选的标题，用于表示浏览历史记录中保存的状态

    * url:表示当前状态的位置，只是简单指定url的hash部分

2. 当用户通过"后退"和"前进"按钮浏览保存的历史状态时，浏览器会在Window对象上触发一个`popstate`事件。与该事件相关联的事件对象有一个`state`属性，该属性包含传递给pushState()方法的状态对象的副本（另一个结构性复制）。

*但页面新加载时，会触发一个没有状态的popstate事件*

**replaceState()**

跟pushState()接收一样的参数，但是它是用新的状态来替换当前的历史状态。

### popstate VS hashChange

对于http://www.abc.com/path?search=ghj#hashstring 这个url来说，

(1) http://为协议
(2) www.abc.com为host
(3) path为path，也就是我们的子目录，每个子目录可以干很多事情
(4) #hashstring为hash相关

(1-3)的改变会导致页面刷新，(4)的改变则页面不会刷新

1. hashChange只能改变hashstring的部分
2. pushState可改变(1-3)而不会刷新页面

### ? VS #

由于pushState可以随意改变地址栏的地址，因此我们可以用`?`来传递值，而不是`#`。

1. `?参数`即使用户强制刷新了界面，也可以给后台传参数，从后台获取我们所需要的数据
2. `#参数`则不会发送给服务器

## 实际应用

历史记录的管理技术被大量应用于router中，我们都知道在开发单页应用时，router是一个必不可少的功能，而router正是使用这两种方案中的一个来实现的。

## 一个例子

我们在这个里面中使用`?`，而不是`#`，可以看到浏览器的地址栏上的变化

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>I'm thinking of a number</title>
	<style>
	#prompt{
		font-size: 16px;
	}
	table{
		width: 90%;
		margin: 10px;
		margin-left: 5%;
	}
	#low,#high{
		background-color: lightgray;
		height: 1em;
	}
	#mid{
		background-color: green;
	}
	</style>
</head>
<body>
	<h1 id="heading">I'm thinking of a number...</h1>

	<!--用户猜测的数字在有效范围-->
	<table>
		<tr>
			<td id="low"></td>
			<td id="mid"></td>
			<td id="high"></td>
		</tr>
	</table>

	<!--用户输入区-->
	<label  id="prompt"></label>
	<input type="text" id="input">

	<script src="history.js"></script>
</body>
</html>
```

history.js的内容如下

```javascript
window.onload = newgame;
window.onpopstate = popState; //处理历史记录
var state,ui;

function newgame(playagain){
	ui = {
		heading : null,
		prompt : null,
		input : null,
		low : null,
		mid : null,
		high : null
	};

	for(var id in ui){
		ui[id] = document.getElementById(id);
	}

	ui.input.onchange = handleGuess;

	state = {
		n : Math.floor(99 * Math.random()) + 1,// 0<n<100
		low : 0,
		high : 100,
		guessnum : 0,
		guess : undefined
	};

	display(state);

	if(playagain === true){
		save(state);
	}
}

function save(state){
	if(!history.pushState){
		return;
	}

	var url = '?guess' + state.guessnum;

	history.pushState(state, //要保存的状态对象
					 	'', //状态标题，当前浏览器会忽略它
						url); //状态url
}

function popState(event){
	if(event.state){ //如果事件有一个状态对象，则恢复该状态
		//要注意的是，event.state是对已保存状态对象的一个深拷贝
		state = event.state; //恢复历史状态
		display(state); //显示恢复的状态
	} else {
		//第一次载入页面，会触发一个没有状态的popstate事件
		//用真实的状态将null状态替换掉
		history.replaceState(state,'','?guess'+state.guessnum);
	}
}

function handleGuess(){
	var g = parseInt(this.value);
	if((g > state.low) && (g < state.high)){
		if(g < state.n) state.low = g;
		else if (g > state.n) state.high = g;
		state.guess = g;
		state.guessnum++;

		//保存状态
		save(state);
		display(state);
	} else {
		alert('invalible value');
	}
}

function display(state){
	ui.heading.innerHTML = document.title =
		'I am thinking of a number between ' +
		state.low + ' and ' + state.high + '.';

	ui.low.style.width = state.low + '%';
	ui.mid.style.width = (state.high - state.low) + '%';
	ui.high.style.width = (100 - state.high) + '%';

	ui.input.style.visibility = 'visible';
	ui.input.value = '';
	ui.input.focus();

	if(state.guess === undefined){
		ui.prompt.innerHTML = 'Type your guess and hit here';
	} else if (state.guess < state.n){
		ui.prompt.innerHTML = state.guess + ' is too low. Guess again';
	} else if (state.guess > state.n){
		ui.prompt.innerHTML = state.guess + ' is too high. Guess again';
	} else {
		ui.input.style.visibility = 'hidden';
		ui.heading.innerHTML = document.title = state.guess + ' is correct';
		ui.prompt.innerHTML = "<button onclick='newgame(true)'>Play Again</button>";
	}
}
```

## 参考

1. 《JavaScript权威指南》