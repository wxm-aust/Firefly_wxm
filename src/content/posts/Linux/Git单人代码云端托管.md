---
title: 系统操作
published: 2023-12-24
pinned: false
tags: [Linux]
category: 学习项目
draft: false
---
## GitHub创建仓库
1. 登录 GitHub 官网，点击右上角的“+”号，选择“New repository”（新建仓库）。
2. 填写仓库名称（Repository name），并选择公开（Public）或私有（Private）。
3. 在创建成功后的“快速设置”页面，复制该远程仓库的 URL
## 本地关联
#### 初始化本地仓库
```bash
git init
```
#### 关联远程仓库
```bash 
git remote add origin REMOTE-URL
```
#### 修改本地分支名
GitHub 现在的默认分支名为 `main`，如果本地是 `master`，建议重命名以保持一致
```
git branch -m master main
```
#### 拉取远程仓库
拉取远程默认的md、licenes和gitignore（或者一开始创建仓库就不要选择）
```bash
git pull origin main --allow-unrelated-histories
```
#### 将修改添加到暂存区
如果想提交特定文件，可以将 `.` 替换为具体的文件名
```bash
git add .
```
#### 提交更改到本地仓库
```bash
git commit -m "首次提交：项目初始版本"
```
#### 推送到 GitHub 远程仓库
`-u` 参数会将本地 `main` 分支与远程分支关联，方便以后直接推送
```bash
git push -u origin main
```

## 更新代码
```bash
git add.
git commit -m "xxx"
git pull
git push
```

---
## 常见报错
### 拒绝合并
```
! [rejected] main -> main (fetch first)
Updates were rejected because the remote contains work that you do not have locally
```
**原因:** 你的本地代码与 GitHub 远程仓库的代码不一致，远程仓库中有你本地没有的提交记录
**解决办法：** 先pull再push养成好习惯
或者
```bash
git pull origin main --allow-unrelated-histories
```
然后处理合并项
### 跨平台换行
```
in the working copy of xxxx CRLF will be replaced by LF next time Git touches it
```
**原因:** Windows 系统默认使用 `CRLF`作为换行符，而 Linux/Mac 等系统使用 `LF`。Git 提示在下次处理这些文件时，会自动将 `CRLF` 转换为 `LF`。
**解决办法：** 在项目根目录下创建或修改 `.gitattributes` 文件，强制统一换行符规则。例如，强制所有文本文件在仓库中使用 `LF`
```bash
# 默认：自动识别文本文件，统一转为 LF 
* text=auto eol=lf 
# Windows 批处理文件必须保留 CRLF 
*.bat text eol=crlf
```
添加 `.gitattributes` 后，Git 不会自动去修改之前已经 `add` 过的文件。你需要强制它重新走一遍换行符归一化流程：
```bash
git add --renormalize .
```