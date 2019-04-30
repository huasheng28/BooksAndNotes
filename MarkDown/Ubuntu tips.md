# Ubuntu tips

初次进入系统，设置root密码`sudo passwd`

以root身份进入图形界面
```
gedit /usr/share/lightdm/lightdm.conf.d/50-unity-greeter.conf &
#添加文本
user-session=ubuntu
greeter-show-manual-login=true
all-guest=false
#重启后
nano /root/.profile
#修改
tty -s && mesg n
```



