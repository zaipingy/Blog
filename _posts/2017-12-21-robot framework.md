---
layout: post
title:  "robot framework"
categories: robot-framework
tags:  robot-framework
author: zaipingy
---

* content
{:toc}

所有test case写在一个文件中，这个文件构成一个test suite，多个文件构成一个文件夹，这个文件夹也是一个test suite。也就是说test suite可以包含test suite。






支持的文件格式：

HTML、TSV、reST、纯文本格式。

数据表名：
- Settings
 - Force Tags, Default Tags
 - Test Setup, Test Teardown
 - Test Template
 - Test Timeout
- Variables
- Test Cases
 - [Documentation]
可以写成多行，或者在其中添加变量。
 - [Tags]
可以根据tag来选择执行部分case。
 - [Setup][Teardown]
在每个case执行前、后执行。
 - [Template]
 - [Timeout]
- Keywords

@keyword(name,tag)

传入robot_name和robot_tag两个参数，若没有则是None


suite = api.TestSutieBuilder().build(robot文件路径）
suite.name:	该robot文件名，如果多个则用 & 连接。


logger.console('string',stream='stdout/stderr/invalid')
日志记录，若stream中不是常规的输出，则默认stdout


run(*test, **options)
- test 是执行的文件路径。
- option 执行时添加的参数。
- 返回值：
  - 0 代表没有case 失败
  - 1-250 case失败个数
  - 250以上则是异常

 - 参数：
 若同一个参数后存在多个，可以写成列表的形式。
   - -name:	执行的test suite的名字，如果不指定就是文件名。
   - -critical：	unknown
   - -exclude:	不执行这个tag
   - -include:	只执行这个tag
   - -listener：	添加监听器，
   - -prerunmodifier:
通过class的方式过滤test suite中的case。
若传入不正常的参数，则正常执行，只是打印error log。
class 继承自 Visitor#SuiteVisitor
里面的方法：
 - visit_suite（ ）
 - start_suite( )
 - end_suite( ）
 - visit_test( )
 - start_test( )
 - end_test( )
 - visit_keyword( )
 - start_keyword( )
 - end_keyword( ）
 - visit_message( )
 - start_message( )
 - end_message( )
 - -rerunfailed:	从上一个output中选择失败的case，rurun。
 - -timestampoutput:	
True	输出的xml文件名使用时间戳

- run_cli(argument,exit=True )
通过命令行执行
exit	True：返回时调用sys.exit,若存在错误，则会报错。
否则只返回result code(rc)不会报错。
- rebot( )	
用来处理输出的xml文件
可以用-prerebotmodifier来过滤或者处理case。
- rebot_cli(argument,exit=True )	
命令行方式执行

参数传递过程中空格是被过滤的



























