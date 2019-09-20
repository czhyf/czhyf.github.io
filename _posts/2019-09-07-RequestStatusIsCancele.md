---
layout: post
title: 'request提交之后返回的状态为canceled'
date: 2019-09-07
author: 男孩
tags: java
---
### 响应状态status为canceled
#### 今天用$.post 请求之后发现一直status:canceled
#### 解决办法：
```css
//ajax请求，需要将请求方式改成同步
async:false;
$.ajax({ 
		async:false,
		url: "test.html", 
		context: document.body, 
		success: function(){
        	$(this).addClass("done");
     	}
});
```
```css
$.post("/getYears.htm", function (data) {});
这种请求方式，我建议单独写，我在全局上加了一个同步
$.ajaxSetup({
        async: false
    });
```

#### 修改之后的：

1,状态修改后：
![](https://mgimg-ali.oss-cn-beijing.aliyuncs.com/Java/work/1.png)
2,修改js内容：
![](https://mgimg-ali.oss-cn-beijing.aliyuncs.com/Java/work/2.png)


