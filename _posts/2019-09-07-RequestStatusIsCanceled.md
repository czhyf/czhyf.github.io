---
layout: post
title: 'request�ύ֮�󷵻ص�״̬Ϊcanceled'
date: 2019-09-07
author: �к�
tags: java
---
### ��Ӧ״̬statusΪcanceled
#### ������$.post ����֮����һֱstatus:canceled
#### ����취��
```css
//ajax������Ҫ������ʽ�ĳ�ͬ��
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
��������ʽ���ҽ��鵥��д������ȫ���ϼ���һ��ͬ��
$.ajaxSetup({
        async: false
    });
```

#### �޸�֮��ģ�

1,״̬�޸ĺ�
![](https://mgimg-ali.oss-cn-beijing.aliyuncs.com/Java/work/1.png)
2,�޸�js���ݣ�
![](https://mgimg-ali.oss-cn-beijing.aliyuncs.com/Java/work/2.png)


