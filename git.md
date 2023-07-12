# git

当我们需要在vscode或webstorm打开一个文件时,可以通过命令行打开

```bash
code .
webstorm .
```

## 配置

1. name: git config --global user.name "your name"

2. email:git vonfig --global user.email "your email@xxx.com"

## 使用git

1. git status
   
   -   查看当前仓库状态

2. git init
   
   + 初始化仓库(在现实开发不会使用) 

3. 文件状态
   
   + 未修改:表示文件修改已经保存,但是未提交到git仓库
   
   + 暂存:磁盘里面已经有一个与git仓库相同的文件,但是未修改
   
   + 已修改:表示磁盘里面的文件已经修改,与git仓库的文件不同
   
   + 其中我们可以通过<mark>git status</mark>来查看文件状态
   
   刚刚添加的文件处于未跟踪状态： 
   
   当我们需要将文件从“未跟踪-->暂存”
   
   + git add <filename>将文件切换为暂存状态
   + git add * 将所有已修改(未跟踪)的文件
   
   暂存-->未修改
   
   + git commit --m "XXX" 将暂存的文件存储到git仓库里面
   
   + git commit -a -m "xxxx" 提交已修改的文件(未跟踪的文件不会提交)
   
   + 其中"XXX"表示的是你在存储文件时,所保留的信息(也可以称为对所存储的文件的解释)
   
   ```js
    将文件从暂存到未修改
    git commit -m "new content"
   [master (root-commit) 9d6c175] new content
    1 file changed, 39 insertions(+)
    create mode 100644 git.md
   查看日志,可以查看是谁修改,修改了什么,
     git log
   commit 9d6c17592308fb951cc7f5651ee0cfd1af6ea41f (HEAD -> master)
   Author: chenxuyan <mengzgh@163.com>
   Date:   Sat Feb 18 12:12:55 2023 +0800
   
       new content
   
   
   ```

4. 常用命令
   
   - 重置文件
     
     ```bash
     git restore <filename> # 恢复文件
     git restore --staged <filename> # 取消暂存状态
     ```
   
   - 删除文件
     
     ```bash
     git rm <filename> # 删除文件
     git rm <filename> -f # 强制删除
     ```
   
   - 移动文件
     
     ```bash
     git mv from to # 移动文件 重命名文件
     ```

## 分支

git在存储文件时，每一次代码的提交都会创建一个与之对应的节点，git就是通过一个个节点来记录代码的状态的，节点会构成一个树状结构，这个树就会存在分支,主干(master),在使用git时,可以创建多个分支,分支与分支之间相互独立,在一个分支上修改代码不会影响到其他的分支

```bash
git branch //如果没有分支,就只有一个master  查看分支
git branch <branch name> //创建新的分支
git branch -d <branch name> //删除分支
git switch <branch name> //切换分支
git switch -c <branch name> //创建并切换分支
```

- 合并分支
  
  当我们在开发中时,我们需要进行代码处理时,便会创建分支来进行修改和开发,当修改和开发结束后,我们需要进行合并时,
  
  若有分支修改的文件相同时,便会发生分支冲突,我们便需要进行手动处理
  
  ```bash
  git merge <branch name> //当前分支会合并这个分支
  ```

## 变基(rebase)

在开发中除了通过merge合并分支时,还可以通过变基来完成分支的合并

我们通过merge合并分支时,在提交记录中会将所有的分支创建和合并的过程全部显示出来,这样当项目比较复杂时,开发过程比较波折时,必须要反复的创建,合并,删除分支,这样一来将会使得我们的代码的提交记录极为混乱

变基(原理)

1. 当我们发起变基时,git会首先找到两条分支最近的共同祖先

2. 对比当前分支相较于祖先的历史提交,并且将他们提取出来存储到一个临时文件中

3. 将当前部分指向目标的基底

4. 以当前基底开始,重新执行历史操作

变基和merge对于合并分支来说,最终的结果是一样的,但是变基会使代码的提交记录更简洁更清晰,但是大部分情况下,合并和变基都是可以互换的,,

但是如果分支已经提交到远程仓库了,那么这时候尽量不要使用变基

```bash
// 假设当前分支为iss
git rebase master //这意味着iss将会合并到master里
```

## 远程仓库

远程仓库与本地仓库没有本质上的区别,远程仓库方便于多人开发

目前常用的库有:GitHub,gitee

在GitHub上创建一个远程仓库

```bash
git remote add origin git@github.com:omjerry/git-dome.git
# git remote add <remote name> <url>
git branch -M main
# 改变分支的名字为main
git push -u origin main
# git push 将代码上传到服务器上面
```

## 远程库的操作

```bash
git remote //列出当前关联的远程库
git remote add <远程库> <url> //关联远程仓库
git remote remove <远程库>//移除远程库
git push -u <远程库名> <分支名> //向远程库推送代码,并和当前分支关联
git clone <url> //从远程库里面下载项目


# 如果本地库的版本低于远程库,push默认是推不上去的
git push
git push <远程库> <本地库分支>:<远程库分支> #完整写法
git fetch # 要想推送成功,必须先确保本地库和远程库版本一致,fetch它会从远程仓库下载所有代码，但是它不会将代码和当前分支自动合并
# 使用fetch拉取代码后,需要手动对代码进行合并

#当需要对远程仓库进行合并时
git merge <远程库>/<远程库分支>
git pull # 从服务器上拉取代码并自动合并
```

注意:在推送代码前,需要先在远程库拉取代码

## 标签(tag)

- 当头指针没有执行某个分支的头部时,这种状态称之为分离头指针(HEAD detached),分离头指针的状态下,也可以操作代码,但是这些操作不会出现在任何分支上,所以注意不要再分离头指针的状态下来操作仓库

HEAD:一般来说,都是会在分支的头部

- 如果需要回到后面的节点进行操作,则可以选择先建立新的分支再操作(即当我们分离头指针后,想要在分离头指针的地方进行操作,由于这些操作不会出现在任何分支上,所以我们便可以在分离头指针的地方建立新的分支,便可以将这些操作存储在新建的这个分支上)
  
  ```bash
  git switch -c <分支名> <提交id(写前面几个即可)> # 建立一个新分支,在分离头指针这里
  ```

- 可以为提交记录设置标签,设置标签之后,可以通过标签快速的识别出不同的开发节点
  
  ```bash
  git tag
  git tag 版本
  git tag 版本 提交id
  git push 远程仓库 标签名
  git push 远程仓库 --tags
  git tag -d 标签名  # 删除标签
  git push 远程仓库 -delete 标签名 #删除远程标签
  ```

## gitignore

- 默认情况下,git会监视项目中所有内容,但是有些内容比如node_modules目录里面的内容,我们可以在项目目录下添加一个.gitignore文件,来设置那些需要git忽略的文件

## GitHub-Page

- 在GitHub中,可以将自己的静态页面部署在GitHub中,他给我们提供一个地址使得我们的页面变成一个真正的网站,可以给用户访问

- 要求:
  
  + 静态页面的分支必须叫:gh-pages
  
  + 如果希望页面可以

## docusaurus

```bash
npx create-docusaurus@latest my-website classic 
```

- 启动项目
  
  ```bash
  npm start
  yarn start
  ```

- 配置项目
  
  docusaurus.config.js 项目的配置文件

- 部署项目
  
  ```bash
  npm run build 
  yarn build
  ```
