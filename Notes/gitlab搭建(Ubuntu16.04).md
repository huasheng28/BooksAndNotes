
# Gitlab搭建(Ubuntu16.04)

本文主要记录在Ubuntu 16.04操作系统中搭建GitLab服务器的操作记录，以下是操作步骤（主要参考资料：https://about.gitlab.com/downloads/#ubuntu1604）。

## 安装
1.安装依赖包，运行命令
```
sudo apt-get install curl openssh-server ca-certificates postfix
```
执行完成后，出现邮件配置，选择Internet那一项（不带Smarthost的）

2.利用清华大学的镜像（https://mirror.tuna.tsinghua.edu.cn/help/gitlab-ce/）来进行主程序的安装。 
首先信任 GitLab 的 GPG 公钥:
```
curl https://packages.gitlab.com/gpg.key 2> /dev/null | sudo apt-key add - &>/dev/null
```
利用root用户打开文件`vi /etc/apt/sources.list.d/gitlab-ce.list`，加入下面一行：
```
deb https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu xenial main
```
安装 gitlab-ce:
```
sudo apt-get update
# 可能需要安装apt-transport-https
# 先查看有哪些版本
apt-cache policy gitlab-ce
# 选择一个
sudo apt-get install gitlab-ce=10.7.3-ce.0
```
3.配置GitLab IP地址，首先运行
```
sudo vi /etc/gitlab/gitlab.rb
```
在文本中修改"externval_url"之后的域名，可以是本机IP或指向本机IP的域名（注意要带有“http://”），这一行在全部文本中位于很靠上面的位置

```
sudo gitlab-ctl reconfigure
```
4.打开 sshd 和 postfix 服务
```
service sshd start 
service postfix start 
```
5.为了使 GitLab 社区版的 Web 界面可以通过网络进行访问，我们需要允许 80 端口通过防火墙，这个端口是 GitLab 社区版的默认端口。为此需要运行下面的命令
```
sudo iptables -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT 
```
6.检查GitLab是否安装好并且已经正确运行，输入下面的命令
```
sudo gitlab-ctl status
```
如果得到类似下面的结果，则说明GitLab运行正常
```
run: gitlab-workhorse: (pid 1148) 884s; run: log: (pid 1132) 884s
run: logrotate: (pid 1150) 884s; run: log: (pid 1131) 884s
run: nginx: (pid 1144) 884s; run: log: (pid 1129) 884s
run: postgresql: (pid 1147) 884s; run: log: (pid 1130) 884s
run: redis: (pid 1146) 884s; run: log: (pid 1133) 884s
run: sidekiq: (pid 1145) 884s; run: log: (pid 1128) 884s
run: unicorn: (pid 1149) 885s; run: log: (pid 1134) 885s
```
如果出现`runsv not running`的情况，尝试
```
systemctl start gitlab-runsvdir
gitlab-ctl restart
```

7.在浏览器地址栏中输入：http://127.0.0.1，即可访问GitLab的Web页面

8.安装完成之后默认是用80端口，如果需要用其他端口，需要修改一下配置文件。
```
sudo vi /etc/gitlab/gitlab.rb
```
修改external_url，直接增加端口号即可
```
external_url 'http://127.0.0.1:8800'
```
除了这个端口外，还有一个unicorn用的端口，默认是8080，如果8080端口被其他程序占用。那么unicorn就会无法启动，显示为502错误，”GitLab is not responding”。
这种情况下修改unicorn的配置`vi /var/opt/gitlab/gitlab-rails/etc/unicorn.rb`：
```
unicorn['listen'] = '127.0.0.1'
unicorn['port'] = 8801
```
修改后再次执行`sudo gitlab-ctl reconfigure`以便配置修改生效。
此步骤后可能需要重启。

9.默认管理员密码如下：
```
Username: root
Password: 5iveL!fe
```
root账户第一次登录时会要求修改密码，为了安全我们在管理页面可以新建一个普通用户，注意新建用户过程中不能设置密码，在建立成功之后可以edit这个账号然后这里可以设置密码。

修改root密码方法：
```
gitlab-rails console production
user = User.where(id: 1).first
user.password='12345678'
user.password_confirmation='12345678'
user.save!
quit
gitlab-ctl reconfigure
gitlab-ctl restart
```

10.汉化
检测当前版本
```
cat /opt/gitlab/embedded/service/gitlab-rails/VERSION
10.6.4
```
git操作
```
# 克隆 GitLab 源代码仓库
git clone https://gitlab.com/xhang/gitlab.git -b v10.6.4-zh
# 先进入克隆出来的 GitLab 文件夹里
cd gitlab
# 也可以在这里查看 tag 版本
# git tag
# 比对不同，diff 结果便是汉化补丁
# 注意此处比较的是 tag
sudo git diff v10.6.4..v10.6.4-zh > /tmp/10.6.diff
```
其他操作
```
# 停止 gitlab
sudo gitlab-ctl stop
# 应用汉化补丁
cd /opt/gitlab/embedded/service/gitlab-rails
git apply /tmp/10.6.diff
# 启动gitlab
sudo gitlab-ctl start
# sudo gitlab-ctl reconfigure
```

## 修改仓库存放目录
```
vi /etc/gitlab/gitlab.rb
# 添加以下语句
git_data_dir "/gitspace"
```

## 备份
1.创建备份
```
gitlab-rake gitlab:backup:create
```
以上命令会在 `/var/opt/gitlab/backups` 目录下创建一个名称类似为1534916238_2018_08_22_10.7.3_gitlab_backup.tar的压缩包, 这个压缩包就是Gitlab整个的完整部分。

也可以通过`/etc/gitlab/gitlab.rb`配置文件来修改默认存放备份文件的目录
```
gitlab_rails['backup_path'] = "/var/opt/gitlab/backups"
```
修改完成后使用 `gitlab-ctl reconfigure` 重载。

2.自动备份
cron是一个Linux下的后台进程，用来定期的执行一些任务。
想要让cron执行你指定的任务，首先就要编辑crontab文件。crontab是一个文本文件，用来存放你要运行的命令。
使用命令 `crontab -e` 来打开crontab文件，第一次用这个命令，会让你选择文本编辑器，选定的编辑器也可以使用 `select-editor` 命令来更改。
文件内容: 
```
# m h dom mon dow user command
17 *   * * *  root   cd / && run-parts --report /etc/cron.hourly 
25 6   * * *  root   test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily ) 
47 6   * * 7  root   test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly ) 
52 6   1 * *  root   test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
```
在crontab文件里面，每一行代表一项任务，每行的每个字段代表一项设置，它的格式共分为六个字段，前五段是时间设定段，第六段是要执行的命令段，每个字段之间用空格分割，没用的段用*代替，格式如`m h dom mon dow user command` 一样， 其中

* m： 表示分钟，可以是从0到59之间的任何整数。
* h：表示小时，可以是从0到23之间的任何整数。
* dom：表示日期，可以是从1到31之间的任何整数。
* mon：表示月份，可以是从1到12之间的任何整数。
* dow：表示星期几，可以是从0到7之间的任何整数，这里的0或7代表星期日。
* user : 表示执行的用户。
* command：要执行的命令，可以是系统命令，也可以是自己编写的脚本文件(如shell文件)。

我们在/home/backup目录下创建auto_backup.sh文件，同时赋予执行权限，操作如下：

```
sudo chmod +x auto_backup.sh
sudo vim auto_backup.sh
```

添加下列代码
```
gitlab-rake gitlab:backup:create
```
然后保存退出。我们可以执行一遍该脚本，可以发现在bakcup目录下又创建了一个备份。 
修改crontab文件，添加一行：
```
0 20 * * * root /home/backup/auto_backup.sh -D 1
```

3.备份恢复（迁移）
Gitlab的版本必须与创建备份时的Gitlab版本号相同
如果你没修改过默认备份目录的话，需要将老服务器上的备份文件目录（/var/opt/gitlab/backups目录）下的备份文件拷贝到新服务器上的/var/opt/gitlab/backups目录。

将备份文件权限修改为777，不然可能恢复的时候会出现权限不够，不能解压的问题
```
chmod 777 1534916238_2018_08_22_10.7.3_gitlab_backup.tar 
```

执行命令停止相关数据连接服务
```
gitlab-ctl stop unicorn
gitlab-ctl stop sidekiq
```

从备份文件中恢复
```
gitlab-rake gitlab:backup:restore BACKUP=1534916238_2018_08_22_10.7.3
gitlab-ctl start
```

