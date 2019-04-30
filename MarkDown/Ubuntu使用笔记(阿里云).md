# Ubuntu使用笔记(阿里云)

* 文件运行提示`permission denied`,一是因为登录用户无权限，使用root用户登录，二是由于登录用户对此文件无权限，执行`chmod +x *.run` . 后缀写该文件后缀

* 后台运行程序`nohup ./IDEA.run &`

* 阿里云端口使用需在防火墙中添加端口规则

* 更改apt源
```
vi /etc/apt/sources.list
```

* Filezilla 配置
```
# 修改
nano /etc/ssh/sshd_config
# 修改如下：密码登陆
PermitRootLogin yes
StrictModes no
PasswordAuthentication yes

service ssh restart
```