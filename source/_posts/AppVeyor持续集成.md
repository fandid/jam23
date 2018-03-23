---
title: AppVeyor持续集成
date: 2018-01-20 17:59:47
tags:
- tech
- hexo
- github
- appveyor
categories: tech
---
前面介绍过如何用hexo＋github生成静态page，今天小小总结一下如何用Appveyor持续集成，解决hexo多端同步的问题。

hexo＋github确实还可以，但多端同步始终是困扰大多数人的一个问题。hexo的一切活动都依赖于本地构建的git和node.js环境，之后再将hexo生成的静态page托管到github上。如果出现换电脑、重装系统等情况，只能重新搭建环境，这样未免有些麻烦。受此文章[Hexo的版本控制与持续集成](https://formulahendry.github.io/2016/12/04/hexo-ci/#)启发，终于找到了合适的同步方法，主要是利用AppVeyor进行持续集成。

## 建立source repo
* 在github上新建repo作为source repo，source repo用来存放hexo源文件，当hexo源文件发生改变，AppVeyor将自动生成静态页面并push到content repo，即github上我们托管的静态页面，这样一来既对hexo源文件进行了备份，又避免了手动deploy的繁琐。

## 将源文件上传至source repo
* 在本地hexo文件夹下通过git bash依次执行命令
  `git init`  （初始化仓库）
  `git add .` （将上传文件添加到暂存区，添加单个文件用 `git add test.txt`；添加所有文件则用 `git add .`）
  `git commit` (将暂存区文件提交至本地仓库，git commit后可加 -m进行描述，如 `git commit -m "first commit"`）
  `git remote add origin 远程仓库地址` （关联远程仓库，即source repo）
  `git pull --rebase origin master` 
  `git push origin master`（将本地仓库内容push至远程仓库）

## AppVeyor关联github
* 登录[AppVeyor](https://ci.appveyor.com/login)，可使用github账号关联
* 新建project，选择自己的source repo
* 添加appveyor.yml文件至source repo根目录下，code为

``` 
clone_depth: 5

environment:
  access_token:
    secure: xxxxxxxxxxxxxxxxxxxxxx
install:
  - ps: Install-Product node 6
  - node --version
  - npm --version
  - npm install -g npm
  - npm install
  - npm install hexo-cli -g

build_script:
  - hexo generate

artifacts:
  - path: public

on_success:
  - git config --global credential.helper store
  - ps: Add-Content "$env:USERPROFILE\.git-credentials" "https://$($env:access_token):x-oauth-basic@github.com`n"
  - git config --global user.email "%GIT_USER_EMAIL%"
  - git config --global user.name "%GIT_USER_NAME%"
  - git clone --depth 5 -q --branch=%TARGET_BRANCH% %STATIC_SITE_REPO% %TEMP%\static-site
  - cd %TEMP%\static-site
  - del * /f /q
  - for /d %%p IN (*) do rmdir "%%p" /s /q
  - SETLOCAL EnableDelayedExpansion & robocopy "%APPVEYOR_BUILD_FOLDER%\public" "%TEMP%\static-site" /e & IF !ERRORLEVEL! EQU 1 (exit 0) ELSE (IF !ERRORLEVEL! EQU 3 (exit 0) ELSE (exit 1))
  - git add -A
  - if "%APPVEYOR_REPO_BRANCH%"=="master" if not defined APPVEYOR_PULL_REQUEST_NUMBER (git diff --quiet --exit-code --cached || git commit -m "Update Static Site" && git push origin %TARGET_BRANCH% && appveyor AddMessage "Static Site Updated") 
```    
注：`secure: xxxxxxxxxxx`处应替换为加密后的github access token。
* 关于access token生成及加密
   * 首先生成github access token 参考[Creating a personal access token for the command line](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
   * 在[AppVeyor加密页面](https://ci.appveyor.com/tools/encrypt)对access token进行加密
* 对project environment进行设置，在setting中添加四个环境变量，分别为：
> GIT_USER_EMAIL  （个人github账号信息）
  GIT_USER_NAME   （个人github账号信息）
  STATIC_SITE_REPO  (content repo的地址）
  TARGET_BRANCH   （默认master）

至此，AppVeyor的相关设置就完成了。此后，只要source repo有内容变化，AppVeyor便会自动生成静态信息并push到content repo，实现自动更新，是不是很方便，终于不用担心多端同步、备份问题了。

references：
[Hexo的版本控制与持续集成](https://formulahendry.github.io/2016/12/04/hexo-ci/#)
[Git上传项目到远程仓库](http://blog.csdn.net/taoyuxin1314/article/details/78949339)
[通过git工具上传本地文件到github](http://blog.csdn.net/weixin_38015384/article/details/79005371)
