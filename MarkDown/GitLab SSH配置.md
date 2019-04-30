# GitLab SSH配置
---
SSH 为 Secure Shell 的缩写，安全外壳协议。
SSH 是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。利用 SSH 协议可以有效防止远程管理过程中的信息泄露问题。

---

## 生成SSH key
### 检查是否已经有SSH Key
```
$cd ~/.ssh
```
### 生成一个新的SSH
```
$ssh-keygen -t rsa -C "emailaddress" -b 4096
```
之后直接回车，不用填写东西。之后会让你输入密码（可以不输入密码，直接为空，这样更新代码不用每次输入 id_rsa 密码了）。完成后在 ~/.ssh/ 会生成2个文件。id_rsa 和 id_rsa.pub。前者是私钥，注意保管，后者是公钥。

### 公私匙区别
* ssh-keygen 是公钥私钥的非对称加密方式： 

1.  公钥：用于向外发布，任何人都能获取。
2. 私钥：要自己保存，切勿给别人

* 公钥私钥加解密的原理 
1. 客户端把自己的公钥存放到要链接的远程主机上（相当于我们把自己的 id_rsa.pub 存放到 git 服务器上）
2. 客户端要链接远程主机的时候，远程主机会向客户的发送一条随机的字符串，客户的收到字符串之后使用自己的私钥对字符串加密然后发送到远程主机，远程主机根据自己存放的公钥对这个字符串进行解密，如果解密成功证明客户端是可信的，直接允许登录，不在要求登录。

* 文件保存目录
1. Windows环境下保存在C:\Users\usrname\.ssh
2. Linux环境下保存在账户的主目录下的 ~/.ssh 目录

---
##配置git
配置git的email和name，不然不能push，把邮箱和用户名改成你自己的

* 配置email:git config --global user.email 'xyz@xx.com'
* 配置用户名: git config --global user.name 'myname'

---
##上传公匙

1. 打开id_rsa.pub文件，复制所有内容
2. 登录GitLab，进入设置--SSH密匙，如下图
![微信截图_20180413114000.png-39.4kB](http://static.zybuluo.com/huasheng28/m2nn2w6ss8dr7zrkc81umvgc/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20180413114000.png)
3. 在密匙输入框粘贴第1步复制的内容，填写标题，点击增加密匙。
4. 测试是否成功`ssh -T git@"你的gitlab服务器地址"`

