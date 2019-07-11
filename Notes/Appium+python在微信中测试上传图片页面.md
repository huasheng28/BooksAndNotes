# Appium+python在微信中测试上传图片页面
* 需先在电脑安装python,seleium,appium环境
* 在脚本写好的情况下，命令行中输入`appium -a 192.168.1.144 -p 4723`-a 为连接的电脑IP地址，-p为端口，手机测试一般使用4723，没有报错基本就成功了
再打开一个新的命令行窗口，载入到脚本所在目录，输入`python rednote3.py`启动编写的脚本
* 脚本编写基于python unittest标准库及seleium框架webdriver
* 脚本头一般为
```
# coding:utf-8
import os
import HTMLTestRunner
import unittest
from selenium import webdriver

# Returns abs path relative to this file and not cwd
PATH = lambda p: os.path.abspath(
    os.path.join(os.path.dirname(__file__), p)
)

class elementA(unittest.TestCase):
    def test_(self):
        desired_caps = {}
        desired_caps['deviceName'] = '6947f050'  # adb devices查到的设备名
        desired_caps['platformName'] = 'Android'
        desired_caps['platformVersion'] = '6.0'
        desired_caps['appPackage'] = 'com.tencent.mm'  # 被测App的包名
        desired_caps['appActivity'] = 'com.tencent.mm/.plugin.webview.ui.tools.WebViewUI'  # 启动时的Activity
        driver = webdriver.Remote('http://localhost:4723/wd/hub', desired_caps)
```
HTMLTestRunner.py需从其官网[下载](http://tungwaiyip.info/software/HTMLTestRunner.html)并放在Python安装路径的Lib目录下
devicename 在命令行中输入`adb devices`显示的设备名称
appPcakage为测试App的包名
appActivity可通过日志输出查看，启动待测程序，在命令行输入`adb logcat>F:/log.txt`，按下CTRL+C停止，查看输出的log文件，查找时间最近的displayed，可以看到`ActivityManager: Displayed com.tencent.mm/.plugin.webview.ui.tools.WebViewUI: +195ms`
appPcakage=com.tencent.mm
appActivity=.plugin.webview.ui.tools.WebViewUI
