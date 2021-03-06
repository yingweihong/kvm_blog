# Git 常用命令

# 1 分支 
## 1.1 本地分支
分支是用来将特性开发绝缘开来的。在你创建仓库的时候，master 是“默认的”。

    建立/删除本地分支
    建立：git checkout -b tst_y
    删除：git branch -d tst_y

## 1.2 远端分支

    a. 创建远端分支
        git push origin <branch_name>
    b. 删除远端分支
        git push origin --delete feature_y
    c. 检查一下现有的所有分支
        git branch -a

## 1.3 合并分支到主干

    git checkout master
    git merge hotfix
    在合并改动之前，也可以使用如下命令查看：git diff <source_branch> <target_branch>


# 2 Patch
## 2.1 查看Patch

    git log -p patch_id
    git diff commit_id~1..commit_id

## 2.2 创建 patch

    git format-patch -n -o .            # one patch will be generated
    or
    git format-patch -n HEAD~3      #3 patches will be generated, their names look like [PATCH n/3] (n=1,2,3)
    or
    git format-patch -n commit ：生成的patch有统计信息和git的版本信息

    和 master 比较，把所有不同的 patch 放到一个文件夹里面
    git format-patch -M master -o outgoing
    git format-patch -M HEAD~1 -o .        跟HEAD进行比较

## 2.3 修改 Patch

    a. 合并上次提交和本次提交的
        git commit -a(将 untagged 变成 tagged)
    b.  修改代码
        git commit --amend -a  
    c.  如果不修改代码，只是修改提交的注释的话
        git commit --amend
    d. 合并中间有间隔的几次提交
        git rebase -i HEAD~3
        修改 pick 的顺序  
    e. 提交并增加签名
        git commit -a -s
    f. 提交并添加注释 
        这是 git 基本工作流程的第一步；使用如下命令以实际提交改动：
        git commit -m "代码提交信息"
        现在，你的改动已经提交到了 HEAD，但是还没到你的远端仓库。

## 2.4 提交Patch
      git am -3 -i -s -u <patch>
      git push 
      你的改动现在已经在本地仓库的 HEAD 中了。执行如下命令以将这些改动提交到远端仓库：
	    git push origin master
      可以把 master 换成你想要推送的任何分支。 
      如果你还没有克隆现有仓库，并欲将你的仓库连接到某个远程服务器，你可以使用如下命令添加：
		git remote add origin <server>
      如此你就能够将你的改动推送到所添加的服务器上去了。

## 2.5 添加patch的版本号方法

    git format-patch --subject-prefix="PATCH v7" --cover-letter HEAD~11
    --subject-prefix="PATCH v7"
    通过 --subject-prefix="PATCH v7"

# 3. Git 创建仓库
## 3.1 建立文件夹

    mkdir mygit
    cd mygit

## 3.2 初始化仓库

    git init --bare

## 3.3 clone 仓库

    git clone ssh://root@vt-nfs/rampup/haibin/githome/mygit
    git push origin master:master

## 3.4 省去每次输入密码

    机器A 要访问机器B
    则将机器A的公钥放到机器B的 authorized_keys

###  3.4.1 修改 hostname

    hostname Server1
    modify /etc/sysconfig/network hostname
    modify /etc/hosts

###  3.4.2 生成公钥
    ssh-keygen -t rsa
    注意:不要输入密码
###  3.4.3 查看钥匙
    ls -l ~/.ssh
###  3.4.4 将公钥复制到被管理机器 Server2 和Server3的.ssh 目录
    scp id_rsa.pub root@192.168.1.3:~/.ssh/id_rsa_server1.pub
    ssh 192.168.1.3
    cat id_rsa_server1 >> ~/.ssh/authorized_keys
###  3.4.5 设置文件和目录权限
    chmod 600 authorized_keys
    chmod 700 -R .ssh
###  3.4.6 验证
    ssh 192.168.1.3   // we should do not input password
    cat /etc/hosts
       192.168.1.2 Server1
       192.168.1.3 Server2
       192.168.1.4 Server3
    ssh Server2
    ssh Server3
  
注意：  
    1、文件和目录的权限千万别设置成chmod 777.这个权限太大了，不安全，数字签名也不支持。  
    2、生成的rsa/dsa签名的公钥是给对方机器使用的。这个公钥内容还要拷贝到authorized_keys  
    3、linux之间的访问直接 ssh 机器ip  
    4、某个机器生成自己的RSA或者DSA的数字签名，将公钥给目标机器，然后目标机器接收后设定相关权限（公钥和authorized_keys权限），这个目标机就能被生成数字签名的机器无密码访问了  

# 4. Git 保存用户名和密码
## 4.1 Git 保存用户名和密码
Git可以将用户名，密码和仓库链接保存在硬盘中，而不用在每次push的时候都输入密码。
保存密码到硬盘一条命令就可以

$ git config credential.helper store
当git push的时候输入一次用户名和密码就会被记录

## 4.2 参考
	$ man git | grep -C 5 password
	$ man git-credential-store
	这样保存的密码是明文的，保存在用户目录~的.git-credentials文件中
	$ file ~/.git-credentials
	$ cat ~/.git-credentials
	https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%87%AD%E8%AF%81%E5%AD%98%E5%82%A8

# 5. Misc
  a. 查看具体文件修改，可以加 commit 在 log 后面  

    git log commit_id -p -1     显示某个commit 的情况，也可以不加 commit_id，显示所有的情况。
    git log --pretty=oneline    只显示一行日志

  b.查看远端服务器上的仓库目录 并 克隆  

    git remote -v
    git clone git://vt-sync/vmm_tree.git

  c. 配置仓库信息

	git config --global user.name "Huang Haibin"
	git config --global user.email huang.haibin@intel.com
	or add following lines to .git/config: 
	[user]
		name = xxx
		email = xxxx@intel.com
	Or add following lines to ~/.gitconfig
	[user]
		name = xxx
		email = xxxx@intel.com

  d. reset commit  

     git reset --hard commit-number
     假如你做错事（自然，这是不可能的），你可以使用如下命令替换掉本地改动：
	 git checkout <filename>

	此命令会使用 HEAD 中的最新内容替换掉你的工作目录中的文件。已添加到缓存区的改动，以及新文件，都不受影响。
	假如你想要丢弃你所有的本地改动与提交，可以到服务器上获取最新的版本并将你本地主分支指向到它：
	 git fetch origin
 	 git reset --hard origin/master

  e. 内建的图形化 git：

     gitk

  f. 彩色的 git 输出：

     git config color.ui true

  g. 标签

     在软件发布时创建标签，是被推荐的。这是个旧有概念，在 SVN 中也有。可以执行如下命令以创建一个叫做 1.0.0 的标签：git tag 1.0.0 1b2e1d63ff 是你想要标记的提交 ID 的前 10 位字符。使用如下命令获取提交 ID：git log
	 你也可以用该提交 ID 的少一些的前几位，只要它是唯一的。##

# 6. git 基本原理
你的本地仓库由 git 维护的三棵“树”组成。第一个是你的 工作目录，它持有实际文件；第二个是 缓存区（Index），它像个缓存区域，临时保存你的改动；最后是 HEAD，指向你最近一次提交后的结果。
     


# 7. git 官方手册
- GIT 官方wiki 中文版，建立理解并掌握

  	http://git-scm.com/book/zh/v2

- GIT 基本操作，15分钟在线练习，建议进行练习

  	https://try.github.io/levels/1/challenges/1

- GIT 命令汇总中文版，建议理解并掌握

	http://justcoding.iteye.com/blog/1830388

- GIT 使用样例介绍，建议理解并掌握

	http://blog.csdn.net/forlong401/article/details/7217771

- Git 简易指南
	
	http://www.bootcss.com/p/git-guide/

- git log常用命令以及技巧

	http://www.cnblogs.com/BeginMan/p/3577553.html
