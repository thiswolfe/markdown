---
title: Git 操作
description: Git 是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。 
tag:
 - git
categories:
 - code
---

### 拉取代码


	git clone GIT库的网址连接      # 拉取/获取代码
	git pull                      # 更新代码


### 查看状态等


	git status       # 查看状态
	git log          # 查看 commit 历史信息
	git diff A       # 查看修改内容
	git branch -a    # 查看所有分支branch


### 提交更新


	git clone http://ABC          # 拉取项目代码
	git branch -a                 # 查看所有分支branch
	git checkout ABC              # 切换分支到 ABC 分支
	git pull                      # 更新
	git add filename.txt          # 添加更新内容
	git commit -m "commit info"   # 添加更新的commit信息
	git push origin ABC           # 推送更新到远端 ABC 分支


### 提交本地分支


	git clone http://ABC_ref                      # 拉取代码
	git checkout ABK                              # 切换分支
	git pull                                      # 更新分支
	git checkout -b task/ABC                      # 创建本地分支
	git add -A testfile                           # 添加本地修改
	git commit -m "commit info"                   # 添加commit信息
	git push --set-upstream origin task/ABC       # 提交本地分支修改, git push 会提示提交命令


### 修改账户


	git config --global user.email ABC@abc.com    # 修改邮箱
	git config --global user.name "ABC"               # 修改昵称
	git commit --amend --reset-author                 # 更新commit 权限
	git push                                          # 用修改后的用户推送更新


### git 生成patch文件

1.还未提交的修改

	git diff > commit.patch

2.已提交的修改

	git diff 3da71ca35 8b5100cdcd > commit.patch
	注）3da71ca35 在 8b5100cdcd 前面

3.已经add但是未commit的修改

		git diff --cached > commit.patch

4.检查patch是否可以应用

	git apply --check commit.patch

5.打补丁

	git apply commit.patch


### 生成并获取完整patch


	git add test.txt  # 添加修改的文件到缓冲区
	git commit -m "update test.txt"   # 添加修改信息
	git format-patch [commit id] -1   # 生成patch文件


### 发生错误需要修改邮箱和用户名


	git config --global user.name "name"
	git config --global user.email  "name@test.com"
	git commit --amend --reset-author
	git push


### 切换分支，下载subproject文件


	git clone -b mplane_testing ssh://git@bb.baidu.com/test.git --recursive


### 完整分步骤示例


	git clone http.git
	git branch -a
	git checkout ABC
	git submodule update --init --recursive
	git pull

接下来可以对文件进行修改

	git status
	git diff
	git add a.txt
	git commit -m "commit info"
	git push


### 一些问题：

关于patch的介绍：

- 当使用内核的代码时，没有办直接修改仓库代码，可以生成patch
- patch 是对 源 工程，进行追加的 文件

关于下载的仓库文件：

- 文件会下载到当前位置
- 默认会保存到仓库名称的目录下面
- 需要再仓库环境创建账户，才可以获取和更新仓库文件
git clone 的文件，与网页 Download 的压缩包，不一定是一致的。

## 获取所有分支代码

	git branch -r | grep -v '\->' | while read remote; do git branch --track "${remote#origin/}" "$remote"; done
