# GitLab & Git 教程
---
##介绍
Git 是用于 Linux内核开发的版本控制工具。
与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持使源代码的发布和交流极其方便。 
Git 的速度很快，这对于诸如 Linux kernel 这样的大项目来说自然很重要。 
Git 最为出色的是它的合并跟踪（merge tracing）能力。

GitLab，是一个利用 Ruby on Rails 开发的开源应用程序，实现一个自托管的 Git 项目仓库，可通过 Web 界面进行访问公开的或者私人项目。它拥有与Github类似的功能，能够浏览源代码，管理缺陷和注释。可以管理团队对仓库的访问，它非常易于浏览提交过的版本并提供一个文件历史库。它还提供一个代码片段收集功能可以轻松实现代码复用，便于日后有需要的时候进行查找。

##在 Windows 下安装 Git 客户端软件
在你的 Windows 机器上，为了能够方便的检入检出项目，需要安装 Git 客户端软件，
安装完之后会生成Git bash 和Git GUI两个程序
我们使用的是 git extensions（自带git，不用二次安装）的图形客户端进行操作。
这三个都可以在项目中右键调取出来

安装时以下两个选项都需要勾选，其他安装选项请全部勾选
![image_1cbvsr5q98h31avq149u12vjmd1m.png-27.9kB](http://static.zybuluo.com/huasheng28/kpdlsdtluz6v7n3c733xcgbz/image_1cbvsr5q98h31avq149u12vjmd1m.png)
![image_1cbvstn391r831rc93k0v2p3l923.png-27kB](http://static.zybuluo.com/huasheng28/i5gehexkaxqzs99wireqjboq/image_1cbvstn391r831rc93k0v2p3l923.png)

以下选择putty
![image_1cbvsuhobb7o12pvoevbh11soq2g.png-20kB](http://static.zybuluo.com/huasheng28/39f5s2hwpwl2zxa2fp9z622g/image_1cbvsuhobb7o12pvoevbh11soq2g.png)

---
##生成SSH 密匙验证身份
如果你想进行代码的上传与下载等操作，需要你把自己的 ssh key 导入到 gitlab 里，
方法如下：
###git extensions 操作
![](http://static.zybuluo.com/huasheng28/uodkgufquicqqzr23st6bk52/image_1cboj0brh6c91re01tfj1a1v3paa9.png)

点击generate
![20150623160404373.png-42.4kB](http://static.zybuluo.com/huasheng28/yp616f26gj2hrxj8rgdkcwt6/20150623160404373.png)

在红色框中移动鼠标
![image_1cboj9t9hnbr7isja01eep137ccj.png-52.3kB](http://static.zybuluo.com/huasheng28/zskq1733yk2nu7ytt3hhcixf/image_1cboj9t9hnbr7isja01eep137ccj.png)

生成密钥，修改密钥说明，红框中为公匙
![image_1cbojboljh2gjk151pn0n1obid0.png-66.2kB](http://static.zybuluo.com/huasheng28/4oqzzl2f8gltgw1gwlqawshp/image_1cbojboljh2gjk151pn0n1obid0.png)

保存密钥，生成一个.ppk格式文件
![image_1cbojeadj76b1ucn1lpq1og3s7ddd.png-68.3kB](http://static.zybuluo.com/huasheng28/9yuppdy4gv72ri2nzsk00xp4/image_1cbojeadj76b1ucn1lpq1og3s7ddd.png)

启动认证代理
![image_1cbojoclh1toso571f1b1vbu1mildq.png-35.3kB](http://static.zybuluo.com/huasheng28/ujlolsbdsoci1mkt2o2o26y1/image_1cbojoclh1toso571f1b1vbu1mildq.png)

点击add key，选择刚刚生成的.ppk文件
![image_1cbojqitc153uiulgme7b1e0fen.png-34.8kB](http://static.zybuluo.com/huasheng28/af82sev4ljiffv4oyef150ti/image_1cbojqitc153uiulgme7b1e0fen.png)

###git bash 操作
#### 生成一个新的SSH
```
$ssh-keygen -t rsa -C "emailaddress" 
```
之后直接回车，不用填写东西。
之后会让你输入密码（可以不输入密码，直接为空，这样更新代码不用每次输入 id_rsa 密码了）。
完成后在 ~/.ssh/ 会生成2个文件。id_rsa 和 id_rsa.pub。
前者是私钥，注意保管，后者是公钥。

#### 公私匙区别
* ssh-keygen 是公钥私钥的非对称加密方式： 
1.  公钥：用于向外发布，任何人都能获取。
2. 私钥：要自己保存，切勿给别人

* 文件保存目录
1. Windows环境下保存在C:\Users\usrname\ .ssh
2. Linux环境下保存在账户的主目录下的 ~/.ssh 目录

####配置git
配置git的email和name，不然不能push，把邮箱和用户名改成你自己的

* 配置email: `git config --global user.email 'xyz@xx.com'`
* 配置用户名:  `git config --global user.name 'myname'`

####上传公匙

1. 打开id_rsa.pub文件，复制所有内容
2. 登录GitLab，进入设置--SSH密匙
3. 在密匙输入框粘贴第1步复制的内容，填写标题，点击增加密匙。
4. 测试是否成功`ssh -T git@"你的gitlab服务器地址"`

---
##GitLab 操作
###项目概览
####创建项目
![](http://static.zybuluo.com/huasheng28/szmua0a8qyzuql5bfc7be5z4/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20180419103328.png)

可见等级推荐使用私有private
![](http://static.zybuluo.com/huasheng28/o3gyby6alayqlbzvqbky4ijq/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20180419103606.png)

新项目创建后，会有两个地址，一个是HTTP格式，一个是SSH格式，两个都能进行git操作，推荐使用SSH
![](http://static.zybuluo.com/huasheng28/k59aqdtfpx2f7g6gxf1oms8u/%E6%8D%95%E8%8E%B7.png)

####详情
进入一个项目，介绍一下项目详情页
![](http://static.zybuluo.com/huasheng28/35ctvzwpt6mwudstqdrsfs08/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20180417162602.png)

* 星标star：按下此按钮可在项目-已星标项目中查看，相当于收藏功能
* 派生fork：将此项目复制到自己的库中
* README：项目说明，会直接显示在详情页最下面
* 提醒watching：在每次项目变动时发邮件，可设置提醒等级
* master：主分支，点击可切换

####活动
活动页显示的是推送、合并等项目变动记录。
![](http://static.zybuluo.com/huasheng28/f07yfkcm8i70l28ts5eviquc/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20180417162845.png)

###版本库
####文件
显示项目所有文件
####提交commit
显示该项目所有提交信息
####分支branch
显示该项目的分支信息
分支可以看做是项目的版本，各分支之间可以互相合并，各分支在不合并的时候互不影响。
####标签tag
Git 使用的标签有两种类型：轻量级的（lightweight）和含附注的（annotated）。轻量级标签就像是个不会变化的分支，实际上它就是个指向特定提交对象的引用。而含附注标签，实际上是存储在仓库中的一个独立对象，它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明，标签本身也允许使用 GNU Privacy Guard (GPG) 来签署或验证。一般我们都建议使用含附注型的标签，以便保留相关信息；当然，如果只是临时性加注标签，或者不需要旁注额外信息，用轻量级标签也没问题。

###问题issue
Issue 指的是一项待完成的工作，通常与系统的改进相关，中文可以译为"问题"或"事务"。
包括但不限于

* 一个软件的 bug
* 一项功能建议
* 一项待完成的任务
* 文档缺失的报告

每个 Issue 应该包含该问题的所有信息和历史，使得后来的人只看这个 Issue，就能了解问题的所有方面和过程。

####新建
![](http://static.zybuluo.com/huasheng28/lnn4bgr5qslyjp98r1g5cjv5/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20180419110412.png)

* 指派Assignee：能将此问题指定某人解决
* 截止日期：时限
* 里程碑Milestone：用作 Issue 的容器，相关 Issue 可以放在一个 Milestone 里面。
* 标记：Issue 可以贴上标签，这样有利于分类管理和过滤查看。每个 Issue 至少应该有两个 Label ，一个表示性质，另一个表示优先级。
![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017082428.png)

创建成功后
![](http://static.zybuluo.com/huasheng28/ovsouk2bgc2oaxn5wtoq7ww4/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20180419133423.png)

* 未关闭显示的是issue的状态
* 右侧导航栏的todo可将此issue添加至待办事项
* create merge request可新建合并请求或分支

####看板
看板可以更直观的看issue
![](http://static.zybuluo.com/huasheng28/9vbk14ljv9zs8cgqqxmhtc5v/image_1cbe9oa3bj9e195q91ug6bs01p.png)

####标记label
label的作用是进行分类管理和过滤查看，gitlab有一些默认标记，也可以自行添加标记。
![](http://static.zybuluo.com/huasheng28/ijkk5tuonv54dgjom84gdngl/image_1cbeaivjt1t0h3c31orfnenmnk26.png)

####里程碑milestone
里程碑的作用是用作 Issue 的容器，相关 Issue 可以放在一个 Milestone 里面。常见的例子是不同的版本（version）和迭代（sprint），都可以做成 Milestone。
![](http://static.zybuluo.com/huasheng28/puzimmgc5ozq5i9e0arf5mvh/image_1cbei9bsrobc12hel7upcm1gem2j.png)

###合并请求pull request
进行分支合并

* 如果需要在合并时关闭对应issue，在描述中写入`Closes #333, #444, and https://gitlab.com/<username>/<projectname>/issues/<xxx>`，#后跟着的数字是issue的编号
![](http://static.zybuluo.com/huasheng28/i97drojr0s7gorhi5vacqic8/image_1cbljmi4o3n6obnrpq21a7561g.png)


###CI/CD持续集成
####Pipelines
一个pipeline就是一次持续集成任务，一般由一次push触发，在网页上对项目的修改、merge也会触发pipline。pipline由Runner执行，Runner有两种：

* Specific Runners：私有runner，部署和执行在自己服务器上，优点是安全、速度快，缺点是需要提供服务器，部署教程https://docs.gitlab.com/runner/install/linux-repository.html
* Shared Runners：共享runner，官方提供的runner，优点是免费，缺点是会偶尔抽风、或者速度慢

####Jobs
pipline由多个job组成，一个job会发给一个runner来执行，所以各个job之间的数据不是共享的，除非使用cache。所以尽量把一些有依赖的步骤放到一个job里，或者把一些通用步骤放到before_script里，这个后面会提到。

####stages
stage是对job的分组，同一个stage里的job是并行的，两个stage之间是串行的

####.gitlab-ci.yml
要想配置上面说的这些，需要在项目根目录新建.gitlab-ci.yml文件，文件格式为yaml，[官方教程](https://docs.gitlab.com/ee/ci/yaml/README.html)


###维基wiki
每个GitLab项目都内置了一个名为Wiki的单独文档系统。   可以在Web界面中创建Wiki页面，也可以在本地使用Git创建Wiki页面，因为每个Wiki都是独立的Git存储库。
![](http://static.zybuluo.com/huasheng28/u0nf328i20edcvcidgd0cfjx/image_1cbgc7gghs381jlp1jmnadf1v0pp.png)

wiki的作用是可以单独的对项目文档进行编辑。

###代码片段Snippets
功能与GitHub的gist功能类似，可进行少量代码或文本的储存，但没有版本控制。
![image_1cbgo9l4uigi1mb213na182q1afs16.png-50.1kB](http://static.zybuluo.com/huasheng28/a1c71gdfk4qumwi12zg04ot3/image_1cbgo9l4uigi1mb213na182q1afs16.png)

##群组group

##权限

* 新建项目时
![](http://static.zybuluo.com/huasheng28/s1iqgpvew57lqipe2vdr2s19/image_1cbgr61n214fufa7b8q13g8guo1j.png)
可选项目可见等级,推荐私有

* 项目中
![](http://static.zybuluo.com/huasheng28/h7zfqw2bbzvlrwbr8tgjtb5u/image_1cbgr8jgv171516i51688oifgc20.png)
仓库权限需谨慎选择

* 项目用户权限
![](http://static.zybuluo.com/huasheng28/sagyhes1i9stxxhxy7ae9t9f/image_1cblhu2841ld71rhshnt1s215bl13.png)

* 用户权限
![](http://static.zybuluo.com/huasheng28/neg6399kbssrohznhljg8ggj/image_1cbgs5ij41g8g1f9d1j3ivrl1uk42t.png)

---

###添加.gitignore
一般项目中有一些缓存或配置文件，我们不想对他们进行管理，此时我们可以将这些文件加入到.gitignore文件中。
使用Git Bash工具创建.gitignore，或直接新建文件
执行命令`touch .gitignore`
example:
```
# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build

#IDEA
.idea
```

---
##单人操作项目
###自己在服务器新建项目
###在本地克隆自己的项目
在准备克隆的目录右键，点击gitext clone
![image_1cbokbt5o14jmdr1ccs4dn1lruf4.png-24.5kB](http://static.zybuluo.com/huasheng28/3m67h10mv740a3u4geglyms1/image_1cbokbt5o14jmdr1ccs4dn1lruf4.png)

填写项目地址和子目录名称
![image_1cbokg5j31c439jd10b6123618rcfh.png-22.7kB](http://static.zybuluo.com/huasheng28/mpxlri6m4wlcs6vhiinj7fcp/image_1cbokg5j31c439jd10b6123618rcfh.png)

对应命令`git clone git@192.168.1.110:liyur/test.git`

###暂存和提交改动
一旦修改了本地项目中的某个文件，提交按钮就会变红，点击
![image_1cbolj2g0qal1ek51pkd62k1as5fu.png-48.9kB](http://static.zybuluo.com/huasheng28/afyh1h6ddv2cy5xcr075giyy/image_1cbolj2g0qal1ek51pkd62k1as5fu.png)

我们先点击载入，将改动文件放入暂存区，然后输入提交信息，点击提交
![image_1cbolp8jalpj1p8i1e162e71k1bgr.png-58.5kB](http://static.zybuluo.com/huasheng28/3xvyf5yfmwyppwb9ibkah5xe/image_1cbolp8jalpj1p8i1e162e71k1bgr.png)

对应`git add`和`git commit -m "message"`

###推送改动push
上图中的提交并推送可将提交推送合并完成
如果需要单独上传，点击菜单栏——命令——推送
![image_1cbomakfj1112jvkdpiubr1p08h8.png-16.4kB](http://static.zybuluo.com/huasheng28/a7n01z18j4ogmb0yrhmvo9qe/image_1cbomakfj1112jvkdpiubr1p08h8.png)
对应操作`git push`

##多人操作同一项目(单分支)
###克隆同一项目至本地
###暂存提交
###拉取远端更新
clone下来的项目都会自带一个远端地址origin，也就是clone时输入的地址。
在push之前需要先从远端地址获取最新更新。
在菜单栏中选择命令——拉取/获取，选择远程分支，即已更新的分支。
在管理远程按钮中可添加更多远端地址。

![image_1cbomos42ksl1ff31nmtfi1mv5i5.png-34.1kB](http://static.zybuluo.com/huasheng28/eakvo6vw5lljeuf67a9ypjzx/image_1cbomos42ksl1ff31nmtfi1mv5i5.png)

选择完成后点击拉取

###合并冲突
如果本地内容与远端内容有冲突，则拉取失败，关闭错误信息后，弹出提示框
![](http://static.zybuluo.com/huasheng28/atkad920eowajdkdj8c54sn0/image_1cboovpmsdur5sd1evj10ahc7hii.png)

点击是，点击打开kdiff3
![](http://static.zybuluo.com/huasheng28/xvmg4x36crfflw9a7c8fj6ix/image_1cbop1mb7kfp1got8bhuhs1tnqiv.png)

此时显示的是原来的文件、本地修改的文件、和远端修改的问题的冲突。
![](http://static.zybuluo.com/huasheng28/bwtpgz414og09qfxb1acqrj2/image_1cbop8s9f19kstgo1gkjd0ll4e9.png)

点击菜单栏——merge，可选择应用哪个修改，点击保存
![](http://static.zybuluo.com/huasheng28/obvtxbk49yhre2rqui6mdaud/%E6%8D%95%E8%8E%B7.PNG)

关闭框体，解决完冲突后即可进行提交。

###流程
总得来说流程就是这样
![](http://static.zybuluo.com/huasheng28/fzq841ojog163fq10oujri74/image_1cblvi1bl1abo6rg1660agc10pd2n.png)

##多分支操作branch
###常用分支
Git主分支的名字，默认叫做Master。它是自动建立的，版本库初始化以后，默认就是在主分支在进行开发。
主分支只用来发布重大版本，日常开发应该在另一条分支上完成。我们把开发用的分支，叫做Develop。

除了常设分支以外，还有一些临时性分支，用于应对一些特定目的的版本开发。
临时性分支主要有三种：

* 功能（feature）分支
* 预发布（release）分支
* 修补bug（fixbug）分支

###新建分支
在服务器和本地都可以新建分支
![](http://static.zybuluo.com/huasheng28/31k1aobtya3colt2vvv0f8sg/image_1cbm1v5jkmno1vlt10la1pp0uh034.png)
需要注意的是选择的源，可选分支、标签
![](http://static.zybuluo.com/huasheng28/au0vi9y8yodwnqcpkelcrd2k/image_1cbm2em8q1n5s17rl1be15931a473h.png)

###提交上传改动
切换至新建分支，做改动后上传至服务器

###合并分支
选择来源分支和目标分支
![](http://static.zybuluo.com/huasheng28/s2r42fl387og96ts59q2h67p/image_1cbm5pm5k96n1c70146gig8rdk4m.png)

![](http://static.zybuluo.com/huasheng28/uvooy7op7ip6wzehkobmnjzd/image_1cbm80h48121oljsdm21ap6gui53.png)
![](http://static.zybuluo.com/huasheng28/1t64ecrr5qmdpu91eu56shy7/image_1cbm81i1oidkbgl17qc13e3pss5g.png)

可以看到提交记录有三次，确认无误后点击提交，就创建了一个合并请求PR
![](http://static.zybuluo.com/huasheng28/pftrg90ifb4ttgqu9e6ixmbp/image_1cbm97gqmmss5r51h4p174q1olf5t.png)
点击merge按钮即可合并

##深入了解合并操作
###merge
保持你的提交只在独立分支环境下是很有意义的。但是当你想要将这个提交的改动整合到别的分支中去时，就会需要一些额外的工作。例如，你完成了一个新功能的开发，你想要把这个功能整合到你的 “产品” 分支中去。或者相反的流程，你正在一个分支中开发这个新的功能，同时在你所开发项目中发生了一些改变（比如一些严重的错误被修复了），你很需要这些改动也能被整合到你正在使用的分支中。

无论是哪一种情况我们都称这种整合叫做 “合并（merging）”。在 Git 中我们使用 “git merge” 命令来进行合并的操作。

当 Git 执行一个合并时，它实际上会查找三个提交：

* (1)共同的原始提交
如果你在项目中查看两个分支的历史，它们总是会出自于一次共同的提交，那么在当时的时间点上，这两个分支还是拥有相同的内容。之后它们就开始有了差别。
* (2) + (3) 两个分支的最终点
合并操作的目的就是把两个分支的最新状态结合起来。因此他们各自的最新版本是有特殊含义的。

结合这三个提交后得到的结果就是我们整合的目标。

###快进或合并提交
* 一种最简单的情况是，在其中的一个分支上没有任何一个新的改动提交发生。那么在它之前的最后一次提交就仍然还是那个共同的原始提交。
![](http://static.zybuluo.com/huasheng28/vqew75ac84dpvsuah3ceexye/image_1cbo30c10h6g15o210e9ain7on9.png)
在这种情况下，执行整合操作就非常简单了。 Git 仅仅需要添加所有那些在另外一个分支上的新提交就可以了。在 Git 中，这种最简单的整合操作我们称之为 “快进（fast-forward）”合并。之后两个分支就拥有了完全相同的历史。

* 但是在大多数情况下，两个分支都会有自己不同的发展轨迹。
![](http://static.zybuluo.com/huasheng28/qdjfeqebbcchdxis791eq30i/image_1cbo32vlu1jkmknguvm17k4179v16.png)
为了完成整合，Git 会需要创建一个新的提交来含括它们之间的差异，这就是整合提交（merge commit）。
![](http://static.zybuluo.com/huasheng28/l9p2c1ic5ie2wenqhf1x5pe1/image_1cbo34c1737q12812k24891rfo1j.png)

###衍合rebase
通常情况下，提交都是由手工精心创建的。这样也就能更好地保证一次提交只涉及一个关联改动，并且能更好地注释这个提交。

一个合并提交就不同了，它不是由开发人员手动创建的，而是由 Git 自动生成的。它也不涉及一个关联改动，其目的只是连接两个分支，就像节点一样。

有些人并不喜欢使用这种自动合并提交。相反，他们希望项目拥有一个单一的历史发展轨迹。比如一条直线。在历史纪录上没有迹象表明在某些时间它被分成过多个分支。

我们想合并分支 B 到 分支 A 中:
![image_1cbo5vdq31lqf10pa4q87868f220.png-37kB](http://static.zybuluo.com/huasheng28/w3sth5yq6nt5rbls5av0oww5/image_1cbo5vdq31lqf10pa4q87868f220.png)

```
git rebase branch-B
```

首先，Git 会 “撤销” 所有在分支 A 上的那些在与分支 B 的共同提交之后发生的提交。当然，Git 不会真的放弃这些提交，其实你可以把这些撤销的提交想像成 “被暂时地存储” 到另外的一个地方去了。
![image_1cbo61kb41i731jil7eagcg150h2d.png-46.8kB](http://static.zybuluo.com/huasheng28/psq6ua1vv6jum5feng3pb21f/image_1cbo61kb41i731jil7eagcg150h2d.png)

接下来它会整合那些在分支 B（这个我们想要整合的分支）上的还未整合的提交到分支 A 中。在这个时间点，这两个分支看起来会是一模一样的。
![image_1cbo62ii11gka16hp4031ndn16bm2q.png-36.2kB](http://static.zybuluo.com/huasheng28/h0c9npp6q5gvarjp96ft1l37/image_1cbo62ii11gka16hp4031ndn16bm2q.png)

最后，那些在分支 A 的新的提交（也就是第一步中自动撤销掉的那些提交）会被重新应用到这个分支上，但是在不同的位置上，在那些从分支 B 被整合过来的提交之后，它们就被 re-based 了。
整个项目开发轨迹看起来就像发生在一条直线上。相对于一个合并提交，rebase 包括了所有的组合变化，最原始的提交结构会被保留下来。
![image_1cbo64n7bh1p1rr2ae9b0jsr37.png-41.9kB](http://static.zybuluo.com/huasheng28/kpgtvdsbz8njuhmoxl89fva5/image_1cbo64n7bh1p1rr2ae9b0jsr37.png)

##具体合并操作
###合并
先检出合并入的分支，点击菜单栏——命令——合并分支

![image_1cbo90t921gsc11rq1i1nvakjsr61.png-15.4kB](http://static.zybuluo.com/huasheng28/we8o5y8rjwqdp7dmvdh0j9xg/image_1cbo90t921gsc11rq1i1nvakjsr61.png)

###衍合
先检出做过修改的分支，然后点击菜单栏——命令——衍合
![image_1cbo99pgf1ion18ah1t8q1nvq8q86e.png-23.5kB](http://static.zybuluo.com/huasheng28/y0n0i6eqjmyw9a8w7acpr3dn/image_1cbo99pgf1ion18ah1t8q1nvq8q86e.png)

* 衍合于的选项选择要并入的分支
* 勾选交互式衍合，即为命令`git rebase -i`
* 指定区间选择从共同提交到提交完成的分支
![image_1cbo9nj461ka41q1212kstn51mk76r.png-23.5kB](http://static.zybuluo.com/huasheng28/wtn988hmantw4f378gaqjwzp/image_1cbo9nj461ka41q1212kstn51mk76r.png)

 点击衍合，此时出现第一个框体,
 ![image_1cbo9pefr8rk1i1vtf1e7a14tb78.png-43.6kB](http://static.zybuluo.com/huasheng28/ubgx4a1bcwvckcypanld5iwg/image_1cbo9pefr8rk1i1vtf1e7a14tb78.png)
将其中的pick只保留一个，其他改为s，点击保存，关掉框体
![image_1cbo9t6q7afa3g2173313mh1ta17l.png-6.5kB](http://static.zybuluo.com/huasheng28/b026e4z35gfpt285kbaeo29y/image_1cbo9t6q7afa3g2173313mh1ta17l.png)

此时出现第二个框体，此框体用来写提交信息
![image_1cbo9v43u1ni4160f1bcnk94at88i.png-42.5kB](http://static.zybuluo.com/huasheng28/hemf2lccnhmcvahkk9qtyevk/image_1cbo9v43u1ni4160f1bcnk94at88i.png)
将未注释部分写成一个，保存退出
![image_1cboa1fke1evajr1pkc17q3hl59f.png-30.3kB](http://static.zybuluo.com/huasheng28/kbx7pkm1clrawhit4tkb57ht/image_1cboa1fke1evajr1pkc17q3hl59f.png)

此时feature的提交变为一个
![](http://static.zybuluo.com/huasheng28/6w9pq3lnzszgc1yoxf3an490/image_1cboa373le9lkgfr1fr651e389s.png)

再进行正常合并操作即可

##fork开发流程
1. 将项目派生fork至自己的项目
2. 克隆自己的项目到本地
3. 新建一个开发分支进行开发，提交但不上传
4. 将新分支在本地rebase并合并至develop分支
5. 从远端获取最新更新到本地
6. 合并更新和修改的冲突，提交上传


##命令行操作

###基本操作
* 克隆仓库到本地`git clone [projectaddress]`
* 暂存文件`git add [filename]`暂存所有改动`git add -A`
* 提交并写提交信息`git commit -m "[message]"`
* 推送`git push` `git push [remote-name] [branch-name]`
* 查看当前状态`git status`

###版本回退
* `git reset --hard [commit_id]`
* 查看提交历史`git log` 以简略图查看提交历史`git log --graph --pretty=oneline --abbrev-commit`
* 丢弃工作区修改`git checkout -- [file]`
* 丢弃暂存区修改`git reset HEAD [file]`

###分支操作
* 查看分支`git branch`
* 创建分支`git branch [branch-name]`
* 切换分支`git checkout [branch-name]`
* 创建并切换分支`git checkout -b [branch-name]`
* 合并某分支到当前分支`git merge [branch-name]`
* 合并时新建一个提交，禁用快进合并`git merge --no-ff -m "[message]" [branch-name]`
* 删除分支`git branch -d [branch-name]`
* 衍合`git rebase [主分支] [特性分支]`
* 衍合时修改提交历史`git rebase -i [主分支] [特性分支]`
* 设置远程追踪`git branch --set-upstream branch-name origin/branch-name`

###远程仓库
* 查看远程仓库`git remote -v`
* 添加远程仓库`git remote add [shortname] [url]`
* 删除远程仓库`git remote rm [name]`
* 拉取远端修改`git pull`