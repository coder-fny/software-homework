# 彩色弹幕

姓名：付南阳       学号：U201711917   班级：电气1710

## 项目简介

通过输入文字内容就可以在屏幕上产生颜色随机，大小随机，特效随机的弹幕，如果要制作个人博客或者是网站的话，可以通过这个方法让你的博客和网站中的视频更具吸引力。也可以作为网络聊天室的载体，结构简单，可拓展性强。

## 使用的软件以及编程语言

采用sublime进行编写，语言使用HTML+CSS+JavaScript

## 项目大致分析

要实现该功能的关键就是每当我在 $input$ 中输入内容的时候，按下 $enter$ ，它能够自动为我这个文字添加随机的颜色，随机的文字大小，随机出现的位置，随机的特效。因此，每次发送的时候会触发一个事件，在这个事件中调用那些随机产生颜色，文字大小，特效的函数，然后修改我输入文字的属性，然后追加到我的屏幕中去。

##  代码分析

### 页面布局

~~~html
<div class="screen" id="background">

</div>
<div class="form-inline send">
	<input type="text" class="form-control" id="context" maxlength="30" style="width: 70%" placeholder="请输入要发送的弹幕（最多不超过30个字）">
	<button class="btn btn-default" onclick="send()">发送</button>
</div>
~~~

### 随机颜色函数

颜色可以通过6位十六进制的字符串组合来确定的，产生随机颜色也就是产生6位随机十六进制码。

~~~javascript
function getColor(){
			var colorStr = "";
  			var randomArr = ['0','1','2','3','4','5','6','7','8','9','a','b','c','d','e','f'];
  			for(var i = 0;i < 6;i++){
    			colorStr += randomArr[Math.ceil(Math.random()*15)];
  			}
			return colorStr;
		}
~~~

### 随机文字大小

设置文字大小的范围为10到40px。

~~~javascript
function getFontsize(){
			fontSize = Math.ceil(Math.random() * 40)
			while (fontSize < 10){
				fontSize = Math.ceil(Math.random() * 40)
			}
			return fontSize;
		}
~~~

### 根据文字长度获取速度

速度范围在10-25之间。

~~~javascript
function getSpeed(length){
			return 0.5 * length + 10;
		}
~~~

### 随机特效

目前一共有两个特效，一个是水平单次不循环滚动，另一个是随机出现在屏幕中，然后数秒后消失。并且输入文字越多，移动速度越快，显示时间越长。

~~~JavaScript
function getMotion(length){
			switch (Math.ceil(Math.random()*2)) {
				case 1:
					var p = document.createElement("marquee");
					p.loop = 1;
					p.scrollAmount = getSpeed(length);
					p.onmouseover = function(e){
						e.srcElement.stop();
					}
					p.onmouseout = function(e){
						e.srcElement.start();
					}
					return p;
				case 2:
					var p = document.createElement("p");
					var message = document.getElementById("background").appendChild(p);
					message.style.marginLeft = 1.6*getFontposition() + 'px';
					setTimeout(function(){
						p.remove();
					},getSpeed(length)*200);
					return p;
			}
		}
~~~

### 随机文字位置

~~~JavaScript
function getFontposition(){
			return Math.random()*500;
		}
~~~

### 发送事件

定义了按下 $enter$ 也能触发事件，以及对防止文字溢出屏幕做了限制。

~~~javascript
document.onkeydown = function(event){
			var e = event || window.event || arguments.callee.caller.arguments[0];
			if(e && e.keyCode == 13){
				send();
			}
		}
function send(){
			var context = document.getElementById("context");
			var contextLength = context.value.length;
			if(context.value != ''){
				var border = 495;
				var place = document.getElementById("background");
				p = getMotion(contextLength);
				var message = place.appendChild(p);
				var fontSize = getFontsize();
				var fontPosition = getFontposition();
				message.innerHTML = context.value;
				message.style.color = '#'+getColor();
				message.style.fontSize = fontSize + 'px';
				while(fontPosition + fontSize >= border){
					fontPosition = getFontposition();
				}
				message.style.marginTop = fontPosition + 'px';
			}
			context.value = '';
			context.focus();
		}
~~~

### 样式设定

~~~css
<style type="text/css">
		.screen{
			border: solid 1px black;
			width: 100%;
			height: 500px;
			overflow: hidden;
		}
		.send{
			margin-top: 10px;
			text-align: center;
		}
		marquee{
			position: absolute;
		}
		p{
			position: absolute;
		}
</style>
~~~

## 附录：所有代码

~~~html
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<title>彩色弹幕</title>
	<link rel="stylesheet" href="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/css/bootstrap.min.css">  
	<script src="https://cdn.staticfile.org/jquery/2.1.1/jquery.min.js"></script>
	<script src="https://cdn.staticfile.org/twitter-bootstrap/3.3.7/js/bootstrap.min.js"></script>
	<script type="text/javascript">
		document.onkeydown = function(event){
			var e = event || window.event || arguments.callee.caller.arguments[0];
			if(e && e.keyCode == 13){
				send();
			}
		}
		function send(){
			var context = document.getElementById("context");
			var contextLength = context.value.length;
			if(context.value != ''){
				var border = 495;
				var place = document.getElementById("background");
				p = getMotion(contextLength);
				var message = place.appendChild(p);
				var fontSize = getFontsize();
				var fontPosition = getFontposition();
				message.innerHTML = context.value;
				message.style.color = '#'+getColor();
				message.style.fontSize = fontSize + 'px';
				while(fontPosition + fontSize >= border){
					fontPosition = getFontposition();
				}
				message.style.marginTop = fontPosition + 'px';
			}
			context.value = '';
			context.focus();
		}
		function getSpeed(length){
			return 0.5 * length + 10;
		}
		function getColor(){
			var colorStr = "";
  			var randomArr = ['0','1','2','3','4','5','6','7','8','9','a','b','c','d','e','f'];
  			for(var i = 0;i < 6;i++){
    			colorStr += randomArr[Math.ceil(Math.random()*15)];
  			}
			return colorStr;
		}
		function getFontsize(){
			fontSize = Math.ceil(Math.random() * 40)
			while (fontSize < 10){
				fontSize = Math.ceil(Math.random() * 40)
			}
			return fontSize;
		}
		function getFontposition(){
			return Math.random()*500;
		}
		function getMotion(length){
			switch (Math.ceil(Math.random()*2)) {
				case 1:
					var p = document.createElement("marquee");
					p.loop = 1;
					p.scrollAmount = getSpeed(length);
					p.onmouseover = function(e){
						e.srcElement.stop();
					}
					p.onmouseout = function(e){
						e.srcElement.start();
					}
					return p;
				case 2:
					var p = document.createElement("p");
					var message = document.getElementById("background").appendChild(p);
					message.style.marginLeft = 1.6*getFontposition() + 'px';
					setTimeout(function(){
						p.remove();
					},getSpeed(length)*200);
					return p;
			}
		}
	</script>
	<style type="text/css">
		.screen{
			border: solid 1px black;
			width: 100%;
			height: 500px;
			overflow: hidden;
		}
		.send{
			margin-top: 10px;
			text-align: center;
		}
		marquee{
			position: absolute;
		}
		p{
			position: absolute;
		}
	</style>
</head>
<body>
	<div class="screen" id="background">

	</div>
	<div class="form-inline send">
			<input type="text" class="form-control" id="context" maxlength="30" style="width: 70%" placeholder="请输入要发送的弹幕（最多不超过30个字）">
			<button class="btn btn-default" onclick="send()">发送</button>
	</div>
</body>
</html>
~~~



