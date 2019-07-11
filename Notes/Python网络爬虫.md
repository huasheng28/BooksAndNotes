#Python网络爬虫
Python因其语法简单及数量众多的网络分析库成为了制作网络爬虫的绝佳语言。
Python中，爬虫相关的库有urllib，bs4，requests等，还拥有诸如scrapy，pyspider之类的分布式爬虫框架可供使用。
***
##使用BeautifulSoup 来写爬虫
###大部分网络爬虫第一步都是获取html页面：
```
from urllib.request import urlopen
from bs4 import BeautifulSoup
#获取html对象
html=urlopen("http://www.baidu.com")

#转换成bs对象，"html.parser"为解析器，相似的还有lxml.parser
bsObj = BeautifulSoup(html, "html.parser")
```
这时候将bsObj打印出来就可以看到html代码了

###定位到需要操作的对象
1. BeautifulSoup中find()和findAll()函数可以帮你定位到特定的对象
`findAll(tag, attributes, recursive, text, limit, keywords)`
`find(tag, attributes, recursive, text, keywords)`
find( )是findAll( limit=1 )的特殊情况
    ```
    参数 tag 是定位的标签
    参数 attributes 是标签属性
    参数recursive是布尔变量，如果recursive 设置为True，findAll就会根据你的要求去查找标签参数的所有子标签，以及子标签的子标签。如果recursive 设置为False，findAll 就只查找文档的一级标签。
    参数text是用标签的文本内容去匹配，而不是用标签的属性
    参数keyword，可以让你选择那些具有指定属性的标签
    ```

2. 处理子标签和后代标签
以[示例网站](http://www.pythonscraping.com/pages/page3.html)为例
一般情况下，BeautifulSoup 函数总是处理当前标签的后代标签。例如，`bsObj.body.h1` 选择了body 标签后代里的第一个h1 标签，不会去找body 外面的标签。
类似地，`bsObj.div.findAll("img")` 会找出文档中第一个div 标签，然后获取这个div 后代里所有的img 标签列表。
如果你只想找出子标签，可以用.children 标签：
    ```
    from urllib.request import urlopen
    from bs4 import BeautifulSoup
    html = urlopen("http://www.pythonscraping.com/pages/page3.html")
    bsObj = BeautifulSoup(html)
    for child in bsObj.find("table",{"id":"giftList"}).children:
        print(child)
    ```
这段代码会打印giftList 表格中所有产品的数据行。如果你用descendants() 函数而不是children() 函数，那么就会有二十几个标签打印出来，包括img 标签、span 标签，以及每个td 标签。

3. 处理兄弟标签
    ```
    from urllib.request import urlopen
    from bs4 import BeautifulSoup
    html = urlopen("http://www.pythonscraping.com/pages/page3.html")
    bsObj = BeautifulSoup(html)
    for sibling in bsObj.find("table",{"id":"giftList"}).tr.next_siblings:
        print(sibling)
    ```
这段代码会打印产品列表里的所有行的产品

4. 正则表达式
    ```
    from urllib.request import urlopen
    from bs4 import BeautifulSoup
    import re
    html = urlopen("http://www.pythonscraping.com/pages/page3.html")
    bsObj = BeautifulSoup(html)
    images = bsObj.findAll("img",{"src":re.compile("\.\.\/img\/gifts/img.*\.jpg")})
    for image in images:
        print(image["src"])
    ```
这段代码会打印出图片的相对路径，都是以../img/gifts/img 开头，以.jpg 结尾

5. 获取属性
对于一个标签对象，可以用下面的代码获取它的全部属性：`myTag.attrs`
要注意这行代码返回的是一个Python 字典对象，可以获取和操作这些属性。比如要获取图
片的资源位置src，可以用这行代码：`myImgTag.attrs["src"]`

###开始采集
以[维基网站页面](http://en.wikipedia.org/wiki/Kevin_Bacon)为例，下面的代码作用是从此页面找到所有wiki词条链接并打印出来，即寻找网页内链。由于wiki数据量过大，这段代码运行完成时间会很长。
```
from urllib.request import urlopen
from bs4 import BeautifulSoup
import re
pages = set()
def getLinks(pageUrl):
    global pages
    html = urlopen("http://en.wikipedia.org"+pageUrl)
    bsObj = BeautifulSoup(html)
    for link in bsObj.findAll("a", href=re.compile("^(/wiki/)")):
        if 'href' in link.attrs:
            #链接去重
            if link.attrs['href'] not in pages:
            newPage = link.attrs['href']
            print(newPage)
            pages.add(newPage)
            
            getLinks(newPage)
getLinks("http://en.wikipedia.org/wiki/Kevin_Bacon")
```

###存储数据
* 在Python 3.x 版本中，urllib.request.urlretrieve 可以根据文件的URL 下载文件：
    ```
    from urllib.request import urlretrieve
    from urllib.request import urlopen
    from bs4 import BeautifulSoup
    html = urlopen("http://www.pythonscraping.com")
    bsObj = BeautifulSoup(html)
    imageLocation = bsObj.find("a", {"id": "logo"}).find("img")["src"]
    urlretrieve (imageLocation, "logo.jpg")
    ```
    
    这段程序从http://pythonscraping.com 下载logo 图片，然后在程序运行的文件夹里保存为logo.jpg 文件。
* 下面的程序会把http://pythonscraping.com 主页上所有src 属性的文件都下载下来：
```
import os
from urllib.request import urlretrieve
from urllib.request import urlopen
from bs4 import BeautifulSoup
downloadDirectory = "downloaded"
baseUrl = "http://pythonscraping.com"
def getAbsoluteURL(baseUrl, source):
    #获取下载链接
    if source.startswith("http://www."):
        url = "http://"+source[11:]
    elif source.startswith("http://"):
        url = source
    elif source.startswith("www."):
        url = source[4:]
        url = "http://"+source
    else:
        url = baseUrl+"/"+source
    if baseUrl not in url:
        return None
    return url
def getDownloadPath(baseUrl, absoluteUrl, downloadDirectory):
    #下载目录创建
    path = absoluteUrl.replace("www.", "")
    path = path.replace(baseUrl, "")
    path = downloadDirectory+path
    directory = os.path.dirname(path)
    if not os.path.exists(directory):
        os.makedirs(directory)
    return path
html = urlopen("http://www.pythonscraping.com")
bsObj = BeautifulSoup(html)
downloadList = bsObj.findAll(src=True)
for download in downloadList:
    fileUrl = getAbsoluteURL(baseUrl, download["src"])
    if fileUrl is not None:
        print(fileUrl)
urlretrieve(fileUrl, getDownloadPath(baseUrl, fileUrl, downloadDirectory))
```
* 在Python 2.X版本中想要保存文件，则需要使用内建函数：
```
import os
import requests
curPath = os.getcwd() + '/'
picPath = curPath + dirName
if os.path.exists(picPath):
    print("Directory exist")
else:
    os.mkdir(picPath)
os.chdir(picPath)
print('下载' + dirName + '...')
f = open(picName, 'wb')
f.write(requests.get(picSrc, headers=header).content)
f.close()
os.chdir(curPath)
```
* 保存为CSV文件
    ```
    import csv
    csvFile = open("../files/test.csv", 'w+')
    try:
        writer = csv.writer(csvFile)
        writer.writerow(('number', 'number plus 2', 'number times 2'))
        for i in range(10):
            writer.writerow( (i, i+2, i*2))
    finally:
        csvFile.close()
    ```

    与爬虫结合起来：
    ```
    import csv
    from urllib.request import urlopen
    from bs4 import BeautifulSoup
    html = urlopen("http://en.wikipedia.org/wiki/Comparison_of_text_editors")
    bsObj = BeautifulSoup(html)
    table = bsObj.findAll("table",{"class":"wikitable"})[0]
    rows = table.findAll("tr")
    csvFile = open("../files/editors.csv", 'wt', newline=", encoding='utf-8')
    writer = csv.writer(csvFile)
    try:
        for row in rows:
            csvRow = []
        for cell in row.findAll(['td', 'th']):
            csvRow.append(cell.get_text())
            writer.writerow(csvRow)
    finally:
        csvFile.close()
    ```
    
* 发送邮件
用Python 发一封邮件只要9 行代码：
    ```
    import smtplib
    from email.mime.text import MIMEText
    msg = MIMEText("The body of the email is here")
    msg['Subject'] = "An Email Alert"
    msg['From'] = "ryan@pythonscraping.com"
    msg['To'] = "webmaster@pythonscraping.com"
    s = smtplib.SMTP('localhost')
    s.send_message(msg)
    s.quit()
    ```
把这个简单的邮件程序封装成函数后，可以更方便地扩展和使用：
    ```
    import smtplib
    from email.mime.text import MIMEText
    from bs4 import BeautifulSoup
    from urllib.request import urlopen
    import time
    def sendMail(subject, body):
        msg = MIMEText(body)
        msg['Subject'] = subject
        msg['From'] = "christmas_alerts@pythonscraping.com"
        msg['To'] = "ryan@pythonscraping.com"
    s = smtplib.SMTP('localhost')
    s.send_message(msg)
    s.quit()
    
    bsObj = BeautifulSoup(urlopen("https://isitchristmas.com/"))
    while(bsObj.find("a", {"id":"answer"}).attrs['title'] == "NO"):
    print("It is not Christmas yet.")
    time.sleep(3600)
    bsObj = BeautifulSoup(urlopen("https://isitchristmas.com/"))
    sendMail("It's Christmas!","According to http://itischristmas.com, it is Christmas!")
    ```
这个程序每小时检查一次https://isitchristmas.com/ 网站（根据日期判断当天是不是圣诞节）。如果页面上的信息不是“NO”，就会给你发一封邮件，告诉你圣诞节到了。

***
## Python requests库
Requests 库（http://www.python-requests.org/就是一个擅长处理那些复杂的HTTP 请求、cookie、header（响应头和请求头）等内容的Python 第三方库。

* 提交表单
表单的源代码是：

    ```
    <form method="post" action="processing.php">
        First name: <input type="text" name="firstname"><br>
        Last name: <input type="text" name="lastname"><br>
        <input type="submit" value="Submit">
    </form>
    ```
用Requests 库提交表单只用四行代码就可以实现，包括导入库文件和打印内容的语句:
    ```
    import requests
    params = {'firstname': 'Ryan', 'lastname': 'Mitchell'}
    r = requests.post("http://pythonscraping.com/files/processing.php", data=params)
    print(r.text)
    ```
* 提交文件和图片
在http://pythonscraping/files/form2.html 有一个文件上传表单，页面上表单的源代码如下所示：
    ```
    <form action="processing2.php" method="post" enctype="multipart/form-data">
    Submit a jpg, png, or gif: <input type="file" name="image"><br>
    <input type="submit" value="Upload File">
    </form>
    ```
    
    需要注意，这里提交给表单字段uploadFile 的值不是一个简单的字符串了，而是一个用open 函数打开的Python 文件对象。
    ```
    import requests
    files = {'uploadFile': open('../files/Python-logo.png', 'rb')}
    r = requests.post("http://pythonscraping.com/pages/processing2.php",files=files)
    print(r.text)
    ```
    
* 处理登录和cookie
大多数新式的网站都用cookie 跟踪用户是否已登录的状态信息。一旦网站验证了你的登录权证，它就会将它们保存在你的浏览器的cookie 中，里面通常包含一个服务器生成的令牌、登录有效时限和状态跟踪信息。网站会把这个cookie 当作信息验证的证据，在你浏览网站的每个页面时出示给服务器。
虽然cookie 为网络开发者解决了大问题，但同时却为网络爬虫带来了大问题。你可以一整天只提交一次登录表单，但是如果你没有一直关注表单后来回传给你的那个cookie，那么一段时间以后再次访问新页面时，你的登录状态就会丢失，需要重新登录。
在http://pythonscraping.com/pages/cookies/login.html 有一个简单的登录表单（用户名可以是任意值，但是密码必须是“password”）。
这个表单在欢迎页面（http://pythonscraping.com/pages/cookies/welcome.php）处理，里面包含一个简介页面：http://pythonscraping.com/pages/cookies/profile.php。
用Requests 库跟踪cookie 同样很简单：
    ```
    import requests
    params = {'username': 'Ryan', 'password': 'password'}
    r = requests.post("http://pythonscraping.com/pages/cookies/welcome.php", params)
    print("Cookie is set to:")
    print(r.cookies.get_dict())
    print("-----------")
    print("Going to profile page...")
    r = requests.get("http://pythonscraping.com/pages/cookies/profile.php",
    cookies=r.cookies)
    print(r.text)
    ```
这里我向欢迎页面发送了一个登录参数，它的作用就像登录表单的处理器。然后我从请求结果中获取cookie，打印登录状态的验证结果，然后再通过cookies 参数把cookie 发送到简介页面。
对简单的访问这样处理没有问题，但是如果你面对的网站比较复杂，它经常暗自调整cookie，或者如果你从一开始就完全不想要用cookie，该怎么处理呢？ Requests 库的session 函数可以完美地解决这些问题：
    ```
    import requests
    session = requests.Session()
    params = {'username': 'username', 'password': 'password'}
    s = session.post("http://pythonscraping.com/pages/cookies/welcome.php", params)
    print("Cookie is set to:")
    print(s.cookies.get_dict())
    print("-----------")
    print("Going to profile page...")
    s = session.get("http://pythonscraping.com/pages/cookies/profile.php")
    print(s.text)
    ```
会话（session）对象（调用requests.Session() 获取）会持续跟踪会话信息，像cookie、header，甚至包括运行HTTP 协议的信息，比如HTTPAdapter（为HTTP和HTTPS 的链接会话提供统一接口）。
* 处理HTTP基本接入认证（HTTP basic access authentication）
Requests 库有一个auth 模块专门用来处理HTTP 认证：

    ```
    import requests
    from requests.auth import AuthBase
    from requests.auth import HTTPBasicAuth
    auth = HTTPBasicAuth('ryan', 'password')
    r = requests.post(url="http://pythonscraping.com/pages/auth/login.php", auth=auth)
    print(r.text)
    ```
虽然这看着像是一个普通的POST 请求，但是有一个HTTPBasicAuth 对象作为auth 参数传递到请求中。显示的结果将是用户名和密码验证成功的页面（如果验证失败，就是一个拒绝接入页面）。

## 使用selenium模拟浏览器行为
Selenium（http://www.seleniumhq.org/）是一个强大的网络数据采集工具，其最初是为网站自动化测试而开发的。近几年，它还被广泛用于获取精确的网站快照，因为它们可以直接运行在浏览器上。Selenium 可以让浏览器自动加载页面，获取需要的数据，甚至页面截屏，或者判断网站上某些动作是否发生。
Selenium 自己不带浏览器，它需要与第三方浏览器结合在一起使用。例如，如果你在Firefox 上运行Selenium，可以直接看到一个Firefox 窗口被打开，进入网站，然后执行你在代码中设置的动作。虽然这样可以看得更清楚，但是我更喜欢让程序在后台运行，所以用一个叫PhantomJS（http://phantomjs.org/download.html）的工具代替真实的浏览器。
PhantomJS 是一个“无头”（headless）浏览器。它会把网站加载到内存并执行页面上的JavaScript，但是它不会向用户展示网页的图形界面。把Selenium 和PhantomJS 结合在一起，就可以运行一个非常强大的网络爬虫了，可以处理cookie、JavaScrip、header，以及任何你需要做的事情。

* 处理Ajax
下面的代码可以获取前面测试页面上Ajax“墙”后面的内容(依据你的PhantomJS 安装位置，在创建新的PhantomJS WebDriver 的时候，你需要在Selenium 的WebDriver 接入点指明PhantomJS 可执行文件的路径)：

    ```
    from selenium import webdriver
    import time
    driver = webdriver.PhantomJS(executable_path=r'')
    driver.get("http://pythonscraping.com/pages/javascript/ajaxDemo.html")
    time.sleep(3)
    print(driver.find_element_by_id('content').text)
    driver.close()
    ```
这段代码用PhantomJS 库创建了一个新的Selenium WebDriver，首先用WebDriver 加载页面，然后暂停执行3 秒钟，再查看页面获取（希望已经加载完成的）内容。
* Selenium选择器
Selenium 在WebDriver 的DOM 中使用了全新的选择器来查找元素，不过它们都使用非常直截了当的名称。
在这个例子中，我们用的选择器是find_element_by_id，虽然下面的选择器也可以获取同样的结果：
`driver.find_element_by_css_selector("#content")`
`driver.find_element_by_tag_name("div")`
当然，如果你想选择页面上具有同样特征的多个元素，可以用elements（换成复数）来返回一个Python 列表：
`driver.find_elements_by_css_selector("#content")`
`driver.find_elements_by_css_selector("div")`
另外，如果你还是想用BeautifulSoup 来解析网页内容，可以用WebDriver 的page_source 函数返回页面的源代码字符串。

    ```
    pageSource = driver.page_source
    bsObj = BeautifulSoup(pageSource,'html.parser')
    print(bsObj.find(id="content").get_text())
    ```
* 隐式等待
除了`time.sleep()`的用法，webdriver还有隐式等待的方式处理持续变化的页面。
隐式等待与显式等待的不同之处在于，隐式等待是等DOM 中某个状态发生后再继续运行代码（没有明确的等待时间，但是有最大等待时限，只要在时限内就可以），而显式等待明确设置了等待时间，如前面例子的等待三秒钟。在隐式等待中，DOM 触发的状态是用expected_conditions 定义的（这里导入后用了别名EC，是经常使用的简称）。

    ```
    from selenium.webdriver.common.by import By
    from selenium.webdriver.support.ui import WebDriverWait
    from selenium.webdriver.support import expected_conditions as EC
    
    driver = webdriver.PhantomJS(executable_path='')
    driver.get("http://pythonscraping.com/pages/javascript/ajaxDemo.html")
    try:
        element = WebDriverWait(driver, 10)
                .until(EC.presence_of_element_located((By.ID, "loadedButton")))
    finally:
        print(driver.find_element_by_id("content").text)
    driver.close()
    ```
这种方式同样可以用来处理网页重定向。

## 避免采集陷阱
* 修改请求头
使用request库模拟发送请求头，http请求头（header）主要有一下几个属性：

    ```
    Host: www.baidu.com
    Connection: keep-alive
    Accept: text/plain, */*; q=0.01
    X-Requested-With: XMLHttpRequest
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.101 Safari/537.36
    Accept-Encoding: gzip, deflate, br
    Accept-Language: zh-CN,zh;q=0.8
    ```
使用Python爬虫时发送的请求头一般为：
    ```
    Accept-Encoding: identity
    User-Agent: Python-urllib/3.4
    ```
请求头可以通过requests 模块进行自定义。https://www.whatismybrowser.com/ 网站就是一个非常棒的网站，可以让服务器测试浏览器的属性。我们用下面的程序来采集这个网站的信息，验证我们浏览器的cookie 设置：
    ```
    import requests
    from bs4 import BeautifulSoup
    session = requests.Session()
    headers = {"User-Agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_5) AppleWebKit 537.36 (KHTML, like Gecko) Chrome",
                "Accept":"text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8"}
    url = "https://www.whatismybrowser.com/developers/what-http-headers-is-my-browser-sending"
    req = session.get(url, headers=headers)
    bsObj = BeautifulSoup(req.text)
    print(bsObj.find("table",{"class":"table-striped"}).get_text)
    ```
* 处理 cookie
在采集一些网站时cookie 是不可或缺的。在一个网站上持续地保持登录状态，需要你在多个页面中保存一个cookie。一些网站不要求在每次登录时都获得一个新cookie，只要保存一个旧的“已登录”的cookie 就可以访问网站。
因为requests 模块不能执行JavaScript，所以它不能处理很多新式的跟踪软件生成的cookie，比如Google Analytics，只有当客户端脚本执行后才设置cookie（或者在用户浏览页面时基于网页事件产生cookie，比如点击按钮）。为了处理这些动作，你需要用Selenium 和PhantomJS 包。
你可以对任意网站（本例用的是http://pythonscraping.com）调用webdriver 的get_cookie()
方法来查看cookie：

    ```
    from selenium import webdriver
    driver = webdriver.PhantomJS(executable_path='<Path to Phantom JS>')
    driver.get("http://pythonscraping.com")
    driver.implicitly_wait(1)
    print(driver.get_cookies())
    ```
你还可以调用delete_cookie()、add_cookie() 和delete_all_cookies() 方法来处理cookie。另外，还可以保存cookie 以备其他网络爬虫使用。
