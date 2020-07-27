## HTML
1. 什么是html HyperText Markup Language 超文本标记语言。
1. html是最基础的网页语言。
1. html都是由标签组成。
1. html的基本格式
    ```html
    	<html>
    		<head>
    			放置一些属性信息，辅助信息。
    			引入一些外部的文件。（css，javascript）
    			它里面的内容会先加载。
    		</head>
    		<body>
    			存放真正的数据。
    		</body>
        </html>
    ```
1. html注意事项
	1. 多数标签都是有开始标签和结束标签，其中有个别标签因为只有单一功能，或者没有要修饰的内容可以在标签内结束。
	1. 想要对被标签修饰的内容进行更丰富的操作，就用到了标签中的属性，通过对属性值的改变，增加了更多的效果选择。
    1. 属性与属性值之间用“=”连接，属性值可以用双引号或单引号或者不用引号，一般都会用双引号。或公司规定书写规范。
1. 排版标签
	1. 换行 ```<br/>```
	1. ```<p></p>``` 段落标签 在开始和结束的位置上会留一个空行。
		属性：align= 对齐方式  
	1. ```<hr />``` 一条水平线
		属性：
		1. 宽度：width 值像素 100px  可以写百分比 30%
		1. align= 对齐方式  
		1. size 粗细
		1. color 值 red green blue  RGB 三原色 (red green blue #aa55ff)		
	1. div  声明一块区域  ```<div>数据</div>```  css+div 
	1. span 声明一块区域  			
1. 字体标签
	1. <font>文本内容</font>
		属性：
			- size 字号的大小 最大值是7 最小值是1
			- color 颜色
			- face 字体
	
	1. 标题标签
		```<h1></h1>```
		...
		```<h6></h6>```
			从大到小字体缩小。
	
    1. ```<B></B>```粗体 
	1. ```<I></I>```斜体
		标签支持嵌套	
1. 列表标签
	数据格式化。
	1. dl 列表标签
		```html
        <dl>
			<dt>上层项目</dt>
			<dd>下层项目</dd>特点：自动对齐，自动缩进。
		</dl>
        ```
		
	
	1. 有序列表和无序列表
		- 有序：```<ol>```
			type：列表前序标号
			start：从第几个开始。
		- 无序：```<ul>```
			数据条目：```<li>数据内容</li>```
			
			```<li><a href="后台的路径">用户管理</a></li>```
			
1. 图片标签
	```<img >```
		属性：src="图片的路径"
			  width 显示图片的宽度
			  height 显示图片的高度
			  alt 图片的说明文字
			  	
1. 超链接链接
	```<a></a>```
	作用：
    1. 链接资源
		href="" 必须指定  如果href的值不指定，默认是file文件议。
						只有自己指定协议，链接资源。
						如果href中指定的协议，浏览器不能解析，就会调用应序，可以解析的程序就可以打开。
								
	1. 定位资源
		name 名称 专业术语 锚

1. 表格标签（重点）
作用：格式化数据
	- ```<table></table>``` 声明一个表格
		属性：
			1. 框 border 
			2. width 宽度
			3. 文字与内边框的距离 cellpadding 
	- ```<tr></tr>``` 行
		属性：align 对齐方式（文本内容）
	- ```<td></td>```
		属性：
			1）width 
			2）height
			3）colspan 列合并单元格
			4）rowspan 行合并单元格
	- ```<th></th>``` 会加粗 并且会居中。
	- ```<caption>``` 表格的标题
		
		
12.表单标签（重点）
作用：可以和服务器进行交互。 输入项的内容  用户名 密码
- ```<form></form>```
    - 属性：action="提交的请求位置" 
	       method 提交方式（get和post） 如果method没有写默认是ge式提交。 
	- get和post区别：
	    1. get方式表单封装的数据直接显示在urlpost方式数据不显示在url上。
	    1. get方式安全级别较低，post级别较高。
	    1. get方式数据的长度，post支持大数据。
				
		
	- ** ?sex=on：
	在每个输入的标签中指定name和value  name必须指定
	?username=haha&pwd=1223&sex=nv&jishu=html
		
- ```<input />```
	- 属性：type 值可以指定很多的值，每一个不同的值代表的不同输入组件。
		1. type=text 文本框 
		1. type=password 密码 
		1. type=radio 单选按钮 
			name属性 
		1. type=checkbox 多选按钮	
			单选和多选都有默认值：checked="checked"
		
		1. type=reset  重置按钮
		1. type=submit 提交按钮
		1. type=file  上传文件的输入项
		1. type=button 按钮 
		1. type=image 图片（也是提交按钮，）
		1. type=hidden 隐藏标签（用户不用看到的，但是咱们开发时必须要的，可以把数据封装到隐藏标签中，和表单一起提交到后台）。
		1. 选择标签
			```<select></select>```选择下拉框
		1. 文本域textarea
			```<textarea>文本内容</textarea>```
			
1. 框架标签
	作用：
        ```
        <frameset>
			<frame>
	     </frameset>
         ```
		框架标签不能写在<body>的内部。body不能写在frameset的上面。
				
			
	

## css
1. 什么是css： Cascading Style Sheets 层叠样式表
2. 作用：定义网页的显示效果。
3. css和html的结合方式
	1. 在每个html的标签中都提供了一个属性 style样式属性 style="就是css的代码"
	2. html提供了一个标签 ```<style type="text/css">css的代码</style>  写在<head></head>```
	3. 在stype标签中 css提供了 @import url("链接文件的地址");
	4. html一个标签 ```<link rel="引入的文件与html的关系" type="text/css" href="文件的地址" >```
	
4. 优先级 从上到下，从外到内，优先级从低到高。（大多数情况下）
	
5. 代码规范
	- 选择器名称 { 属性名：属性值；属性名：属性值；…….}
	- 属性与属性之间用 分号 隔开
	- 属性与属性值直接按用 冒号 连接
	- 如果一个属性有多个值的话，那么多个值用 空格 隔开。

6. css基本选择器
	- 标签名称选择器
	- html的标签提供属性 class    类属性	通过class编写的样式的优先级别高。
	- html的标签提供的属性 id  通常会把id定义成唯一性的。 id的优先级别高于class。style属性的优先级最高。
	 标签名 < class < id < style属性 
	
7. css的扩展选择器
	- 关联选择器：标签是可以嵌套的，两个或多个选择器之间产生关系，就可以用此选择器。
	- 组合选择器：对多个不同选择器进行相同样式设置的时候应用此选择器。
	- 伪元素选择器：
		1. a:link  超链接未点击状态。
		2. a:hover 光标移到超链接上的状态（未点击）。
		3. a:active 点击超链接时的状态。
		4. a:visited 被访问后的状态。
        5. p:first-letter 段落中的第一个字母。
		6. :focus 具有焦点的元素

8. 盒子模型

9. 布局--漂浮
	- float
		right：文本流向对象的左边	
	- position 
		absolute  :　 将对象从文档流中拖出，使用 left ， right ， top ， bottom属性相对于其最接近的一个最有定位设置的父对象进行绝对定位。 
