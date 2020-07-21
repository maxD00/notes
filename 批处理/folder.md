## 批处理获取路径命令

批处理脚本中获取路径有两个相关命令

**%cd%**和**%~dp0**

- %cd%

  批处理脚本的执行路径

- %~dp0

  批处理脚本的所在路径



例如,脚本内容如下:

~~~
@echo off
echo 这是执行%%cd%%的结果 %cd%
echo 这是执行%%~dp0的结果 %~dp0
~~~

脚本在powershell调用如下:

~~~
C:\Users\max> D:\programs\bat\directory.bat 
~~~

输出的结果如下:

~~~
执行%cd%的结果 C:\Users\max
这是执行%~dp0的结果 D:\programs\bat\
~~~



