# vundle

[vundle](https://github.com/gmarik/vundle)是vim的插件管理器，它是基于git架构，每一个插件都是一个项目的Repository，通过Vundle可以用简单的指令，一键安装/更新/删除所有的插件。

此外，在window下还需要使用一个curl脚本用于Vundle的远程链接。

因此，在使用Vundle之前，需要在电脑上安装

1. [curl](./curl.md)
2. [git](https://git-scm.com/)

安装完这两个软件之后，我们需要创建一个文件curl.cmd，里面的内容为

```shell
@rem Do not use "echo off" to not affect any child calls.
@setlocal

@rem Get the abolute path to the parent directory, which is assumed to be the
@rem Git installation root.
@for /F "delims=" %%I in ("%~dp0..") do @set git_install_root=%%~fI
@set PATH=%git_install_root%\bin;%git_install_root%\mingw\bin;%PATH%

@if not exist "%HOME%" @set HOME=%HOMEDRIVE%%HOMEPATH%
@if not exist "%HOME%" @set HOME=%USERPROFILE%

@curl.exe %*
```

保存后运行cmd，输入

```shell
curl --version
```



