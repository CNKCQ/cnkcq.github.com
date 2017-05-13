title: 一步一步搭建Hexo博客
date: 2015-02-20 9:58:12
tags: [iOS,Hexo,博客]
---

## 个人博客快速入门

### 一、生成个人主页
  注册一个github账号，创建一个以你用户名为前缀的仓库，例如：KingCQ.github.com,点击仓库的设置（Settings）进入里面生成 GitHubPages 成功完成相应操作后你会看到 Your site is published at http://kingcq.github.io. 打开这个网页，这就是你的pages页面了

  <!-- more -->
### 二、添加SSH Keys 
  
  生成密钥：
  ``` bash
  $ssh-keygen -t rsa -C "wangchengqvan@hotmail.com" 
  ```
  上述命令若执行成功，会在H:\git\myssh目录下生成两个文件id_rsa和id_rsa.pub，最后两步：
  用文本编辑器打开ssh.pub文件，拷贝其中的内容，将其添加到Add SSH Key
  好，我们来验证一下：
  ``` bash
  $ssh -T git@github.com
  ```
  如果出现Permission denied (publickey).（addkey哪一步可能出现了问题）
  仔细检查，可能是密钥失效或者拼写错误，重新来一次，成功后会有如下提示：
  ``` bash
  Hi username! You've successfully authenticated...
  ```

### 三、安装Hexo
  假设你已经安装好了npm，如果没有请自行Google
  ``` bash
  $npm install -g hexo
  ```
  然后，执行init命令初始化hexo到你指定的目录：
  ``` bash
  $ hexo init <folder>
  ```
  好啦，至此，全部安装工作已经完成！

### 四、生成静态网页
  cd 到你的init目录，执行如下命令，生成静态页面至hexo\public\目录。
  ``` bash
  $hexo generate
  ```
  命令必须在init目录下执行，否则不成功，但是也不报错
  把生成的网页部署到服务器,执行命令
  ``` bash
  $hexo deploy
  ```
  More info: [Deployment](https://hexo.io/docs/deployment.html)
  在执行 hexo deploy 后,如果出现 error deployer not found:github 的错误

  ``` bash
  $npm install hexo-deployer-git --save
  ``` 
  改了之后执行，然后再部署试试
  本地启动
  执行如下命令，启动本地服务，进行文章预览调试。

  ``` bash
  $hexo server
  ```
  用浏览器打开(http://localhost:4000)
  你就可以欣赏到hexo个人主页的效果了。
  ![断货](http://7xslr9.com1.z0.glb.clouddn.com/kingcqduanhuo%402x.png)![断货](http://7xslr9.com1.z0.glb.clouddn.com/kingcqduanhuo%402x.png)![断货](http://7xslr9.com1.z0.glb.clouddn.com/kingcqduanhuo%402x.png)![断货](http://7xslr9.com1.z0.glb.clouddn.com/kingcqduanhuo%402x.png)![断货](http://7xslr9.com1.z0.glb.clouddn.com/kingcqduanhuo%402x.png)![断货](http://7xslr9.com1.z0.glb.clouddn.com/kingcqduanhuo%402x.png)![断货](http://7xslr9.com1.z0.glb.clouddn.com/kingcqduanhuo%402x.png)![断货](http://7xslr9.com1.z0.glb.clouddn.com/kingcqduanhuo%402x.png)![断货](http://7xslr9.com1.z0.glb.clouddn.com/kingcqduanhuo%402x.png)




