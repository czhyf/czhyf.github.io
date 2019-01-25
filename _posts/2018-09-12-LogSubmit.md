---
layout: post
title: 'boss直聘爬取具体信息'
date: 2018-09-10
author: 男孩
tags: python
---
##### Log日志提交系统（仅适用于新手，练练手的东西）
主要是为了提交每天工作和学习的情况，后台需要手机可以直接下载访问。
##### 实现流程图
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/LogSubmit/log1.png)
1，采用SSM框架搭建，主要涉及到两个表，一个是类别表。一个提交过来的信息表
2，需要使用下载功能，刚开始是打断直接使用IO流将文件写到用户的次磁盘上，这样肯定会直接被用户的防火墙拦截掉的。所以必须使用下载功能
3，使用下载功能的时候，不能使用js，因为js只有字符串，所以必须要使用表单。
4，用户提交的时候使用的 [jquery-form.js](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/LogSubmit/jquery-form.js) 文件，需要可以下载。
##### 1，关键部分代码，提交部分的代码,这块直接获取当天的时间，不会给用户提供选择时间的权限。
```css

@RequestMapping(value = "/submit.do",method = RequestMethod.POST,produces="text/html;charset=UTF-8")
    @ResponseBody
    public String submit(HttpServletRequest request) throws UnsupportedEncodingException {
        //拿到这个值，在获取当前时间
        String className = request.getParameter("class_name");
        String name= request.getParameter("entry[field_1]");
        String problem =request.getParameter("entry[field_5]");
        String state =  request.getParameter("entry[field_6]");
        String date = get_time();
        Log_info log_info = new Log_info();
        log_info.setClassName(className);
        log_info.setProlem(problem);
        log_info.setStudentName(name);
        log_info.setState(state);
        log_info.setDate(date);
        //存储到数据库中
        int a= logService.inser_student(log_info);
        String result="";
        if(a==1){
            result= "提交成功";
        }else{
            result="提交失败";
        }
        return result;

    }
```
##### 2，关键部分代码 下载的代码，首先先将数据写入到项目里，然后通过拿到本地项目里，然后下载
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/LogSubmit/log2.png)
##### 3，mysql的表
```css
create database Log;

use Log

/*类别：[开发组，前端组，运营组，....]*/

create table class(
    class_id varchar(10)
);

/*
    private String className;
    private String studentName;
    private String problem;
    private String date;
    private String state; */
    
/*类别 人员名称 遇到的问题 今天的状态 日期 */

create table log_ingo(
className varchar(20),
studentName varchar(20),
problem varchar(100),
state varchar(100),
date varchar(30))ENGINE=InnoDB  DEFAULT CHARSET=utf8 AUTO_INCREMENT=1;
```
#### [查看代码](https://github.com/czhyf/LogSubmit)