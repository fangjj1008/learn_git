### 1.解决本地GitHub网站进不去的问题

https://github.com/

#### 问题描述

当不做任何操作时，发现github官网进不去，如下图所示：

![image-20210319221520226](E:\data\Geek\learn_git\markdown\image-20210319221520226.png)

ping github.com 命令显示超时，是因为本地的NDF无法解析所导致的。

#### 解决方法

1.打开C:\Windows\System32\drivers\etc的hosts文件，发现没有解析github的语句

![image-20210319222211781](E:\data\Geek\learn_git\markdown\image-20210319222211781.png)

2.添加以下内容（以管理员身份编辑）

```
192.30.252.131 github.com
185.31.16.185 github.global.ssl.fastly.net
74.125.237.1 dl-ssl.google.com
173.194.127.200 groups.google.com
192.30.252.131 github.com
185.31.16.185 github.global.ssl.fastly.net
74.125.128.95 ajax.googleapis.com
```

3.还是不行

另一个解决方法：

https://blog.csdn.net/weixin_45126117/article/details/103160228?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.baidujs&dist_request_id=1328679.13045.16161645827194289&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.baidujs



### 2.安装Git、初始化仓库并做最简单的配置

如图所示：

![image-20210319225223684](E:\data\Geek\learn_git\markdown\image-20210319225223684.png)

除了global，还有local，system俩个选项

git账户级别有system、global、local，system为整个计算机的配置，global为当前用户的配置，local为当前项目的配置，一般会配置global级别的账户，如果有项目需要单独的账户，配置为local级别账户即可，在此项目下会覆盖global级别配置。

我的电脑 git config --global配置为

```
user.name=fjj
user.email=894639875@qq.com
gui.recentrepo=E:/workspace/idea-workspace/ah_pad_git_2
```

这一步涉及命令有：

```
git --version 查看版本
git init 初始化git
git config --global user.name "xxx" 设置用户名
git config --global user.email "xx@xx.com" 设置邮箱
git config --global --list 打印global配置
```



### 3.一个简单的Git操作流程：工作区、暂存区、仓库

git状态查看、添加文件至git

![image-20210319231523989](E:\data\Geek\learn_git\markdown\image-20210319231523989.png)

提交文件

![image-20210319232304536](E:\data\Geek\learn_git\markdown\image-20210319232304536.png)

上俩步总结

![image-20210319232754333](E:\data\Geek\learn_git\markdown\image-20210319232754333.png)

这一步涉及命令有：

```
git add xx.html 将工作区文件添加到暂存区
git rm --cache xx.html 将文件从暂存区移到工作区，相当于取消add
git commit -m "create learn_git.html" 提交文件，添加提交的注释
```

修改文件后提交

![image-20210319234853319](E:\data\Geek\learn_git\markdown\image-20210319234853319.png)

查看提交记录

![image-20210319235151547](E:\data\Geek\learn_git\markdown\image-20210319235151547.png)

这一步涉及命令有：

```
git add . 提交所有“工作区”文件到“暂存区”
git restore xx.xx 恢复该文件在工作区所作的修改
git log 打印出所有的提交信息
```



### 4.将本地仓库同步到远程GitHub仓库

我的GitHub账号：``894639875@qq.com``

创建仓库，如下图

![image-20210320210351332](E:\data\Geek\learn_git\markdown\image-20210320210351332.png)

会跳到该页面

![image-20210320211209638](E:\data\Geek\learn_git\markdown\image-20210320211209638.png)

生成公私钥

![image-20210320211738685](E:\data\Geek\learn_git\markdown\image-20210320211738685.png)

新建SSH keys

![image-20210320211950893](E:\data\Geek\learn_git\markdown\image-20210320211950893.png)

![image-20210320212222485](E:\data\Geek\learn_git\markdown\image-20210320212222485.png)

测试本地和远程的gitHub能否建立连接

![image-20210320212336395](E:\data\Geek\learn_git\markdown\image-20210320212336395.png)

本地代码push到远程

![image-20210320212640277](E:\data\Geek\learn_git\markdown\image-20210320212640277.png)

add->commit->push

![image-20210320213705834](E:\data\Geek\learn_git\markdown\image-20210320213705834.png)

![image-20210320213727860](E:\data\Geek\learn_git\markdown\image-20210320213727860.png)

这一步涉及命令有：

```
git remote add origin git@github.com/learn_git.git 为本地仓库添加远程仓库
git remote 检查远程连接，返回远程仓库代号
ssh -keygen -t rsa -C "894639875@qq.com" 输入命令生成公私钥
ssh -T git@github.com 测试本地和远程的gitHub能否建立连接
git push -u origin master 推送到远程仓库
```



### 5.fork

fork就是将远程仓库代码复制一遍到本地

![image-20210320215134849](E:\data\Geek\learn_git\markdown\image-20210320215134849.png)

github上操作（使用ssh协议，即图中的 Use SSH）

![image-20210320215258237](E:\data\Geek\learn_git\markdown\image-20210320215258237.png)

克隆到本地（不要在一个已经存在git仓库的地址下克隆），剩下的提交流程同上

![image-20210320215453392](E:\data\Geek\learn_git\markdown\image-20210320215453392.png)

这一步涉及到的命令有：

```
fork 远程项目拷贝到本地，后续需使用SSH地址
git clone git@github.com:xxxx/xxxx 克隆项目到本地
```

