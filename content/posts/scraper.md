---
title: Python 爬虫
date: 2021-03-20
lastmod: 2021-04-03
categories:
- Tech
tags:
- CS
- Tool
---


Python 爬虫 101

<!--more-->

---

在浏览器输入地址并按下回车时，其实是向对应的服务器发出了 HTTP 请求，接收到返回数据后进行显示。而爬虫是通过程序发出请求，获取到原始数据，方便进行后续操作。

# 网页分析

在爬取特定网页之前，首先应该使用浏览器对原始网页进行查看与分析。

## HTML

在 Chrome 浏览器中打开开发者工具，在最上方可以看到一些面板，其中 Elements 面板是网页对应的 HTML 代码。我们可以右键网页中的任意元素来检查其在 HTML 中的对应位置，也可以在 HTML 中右键进行各种操作，比如复制元素对应的各种选择器，方便代码使用。

## HTTP 请求

定位至 Network 面板，刷新页面，我们可以看到发出的 HTTP 请求。点击请求的 URL，在 Headers 栏可以看到请求的详细内容：

- General:
  - Request URL: 请求的网址
  - Request Methods: 请求方式，最主要的有 GET 和 POST。其中 GET 是获取信息，而 POST 是提交信息。
  - Status Code: 返回码，请求成功一般是 200，不同代码有不同的含义
- Request Headers: 请求头，在请求时发送至服务器的数据
  - User-Agent: 表明发送请求的系统平台、浏览器等
  - Cookie: 用于告诉服务器两个请求是否来自同一个浏览器，比如保持用户的登陆状态
- Response Headers: 响应头
  - Content-Type: 数据类型

在 Preview 栏可以看到响应内容：渲染后的 html、图片、格式化后的 js 和 css。

在 Response 栏可以看到响应内容的原始格式。

## Example

以 [豆瓣电影 Top 250](https://movie.douban.com/top250) 为例，目标是爬取 TOP 250 的电影信息。

1. 打开网页及开发者工具，定位至 Network 面板，刷新网页，点击最上方的 URL

2. 查看 Headers 栏，发现返回文件类型为 text/html

3. 查看 Preview 栏可以看到一个新的「网页」，==可能和原始网页存在区别！==，因为网页上的某些内容并不是由 HTML 决定的，此时需要分析其它请求

4. 查看 Response 栏，即源格式 html，搜索找到需要的内容，并分析结构，如下

   ```html
   <li>
   ...
       <span class="title">肖申克的救赎</span>
   ...
       <span class="rating_num" property="v:average">9.7</span>
   ...
       <span class="inq">希望让人自由。</span>
   ...
   <li>
   ```

5. 总共有 10 页，每页存在 25 个上述条目。点击第二页可以发现 URL 变为了 `https://movie.douban.com/top250?start=25&filter=`。可以猜测其中的 `?start=25&filter=` 变量达到了翻页的效果，可以在浏览器更改 `start` 变量值进一步确认

6. 设计程序框架：遍历请求 URL，使用 `start` 变量进行翻页，获取响应内容后通过上述结构找到需要的内容

# Request

[Request](https://requests.readthedocs.io/en/master/) 是一个模拟 HTTP 请求的 Python 库。一些常见用法：

```python
import requests

# Get/Post Request
r = requests.get('https://api.github.com/events', params = {'key': 'value'})
r = requests.post('https://httpbin.org/post', data = {'key':'value'})

# Pretend to be a browser
r = requests.get("https://api.github.com/events", headers = {'User-Agent': '[value]'})

# Response
r.status_code  # response status code
r.headers  # response headers
# Response content
r.text  # text
r.content  # binary
r.json()  # json
```

## BeautifulSoup

[BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) 是一个从 HTML/XML 文件提取数据的 Python 库。一些常见用法：

```python
from bs4 import BeautifulSoup

# Load html with lxml parser
soup = BeautifulSoup(r.text, 'lxml')

# Find an element by tag
soup.p  # first element
soup.p.a
soup.find('p')
# Find all elements
soup.find_all('p')
soup.find_all(['p', 'a'])  # pass list

# Find an element by attribute
soup.find(class_='abc')  # use keyword argument
soup.find(href=re.compile('abc'), id='abc')  # multiple attribute
soup.find('p', class_='abc')  # together with tag

# Find by string
soup.find_all(string='abc')

# Find by css selector
soup.select("p > a")

# Get the string of an element
soup.p.string
```

## Example

使用 request 爬取内容，正则表达式提取信息。正则表达式可以在 [regex 101](https://regex101.com/) 进行测试

```python
import requests
import re

url = 'https://movie.douban.com/top250'
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)'}

pattern = re.compile(r'<li>.*?title">(.*?)<.*?average">([\.\d]+)<.*?inq">(.*?)<.*?<\/li>', re.S)

for page in range(10):
    params = {'start': page*25}
    r = requests.get(url, params, headers=headers)
    r.raise_for_status()

    items = re.findall(pattern, r.text)
    for i, item in enumerate(items):
        print(f'{page*25+i+1}: {item[0]}, {item[1]}, {item[2]}')
```

使用 BeautifulSoup：

```python
# ...
for page in range(10):
    # ...

    soup = BeautifulSoup(r.text, 'lxml')
    movies = soup.find(class_='grid_view').find_all('li')
    for movie in movies:
        ind = movie.find('em').string
        title = movie.find(class_='title').string
        rating = movie.find(class_='rating_num').string
        intro = '' if movie.find(class_='inq') is None else movie.find(class_='inq').string
        print(f'{ind}: {title}, {rating}, {intro}')
```

# Selenium

[Selenium](https://selenium-python.readthedocs.io/) 是一个用于浏览器的自动化测试工具，用来模拟人操作网页，提供 Python 的 API。一些常见用法：

```python
from selenium import webdriver

# Basics
driver = webdriver.Chrome()  # Create Chrome instance
driver.get('http://www.python.org')  # Open webpage
driver.close()  # Close one tab
driver.quit()  # Close the browser

# Properties
driver.current_url
driver.get_cookie('key')
driver.get_cookies()
driver.add_cookie(dict)
driver.page_source

# Move between windows and frames
driver.find_elements_by_tag_name('iframe')  # find all frames
driver.switch_to.frame(int)  # switch to frame
driver.switch_to_default_content()  # swith back
windows = driver.window_handles  # get all windows
driver.switch_to.window(windows[i])  # switch to window

# Find an element
driver.find_element_by_id('id')
driver.find_element_by_name('name')
driver.find_element_by_xpath('xpath')
driver.find_element_by_link_text('link text')
driver.find_element_by_partial_link_text('partial link text')
driver.find_element_by_tag_name('tag name')
driver.find_element_by_class_name('class name')  # change space to .
driver.find_element_by_css_selector('css selector')
# Find all elements (return list)
driver.find_elements_by_  # ...

# Interaction
# Send texts/keys
from selenium.webdriver.common.keys import Keys
element.send_keys('text')
element.send_keys('text', Keys.RETURN)  # Using "Keys" class
element.clear()  # clear the contents of a text field
# Click
element.click()

# Waits
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
# EC has some predefined conditions
wait = WebDriverWait(driver, timeout, poll_frequency=0.5, ignored_exceptions=None)
output = wait.until(EC.presence_of_element_located((By.ID, 'id')))
```

## Example

抓取豆瓣电影 TOP250 中未看名单

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
from bs4 import BeautifulSoup
import re
import math


def get_movie():
    wait.until(EC.presence_of_element_located((By.CLASS_NAME, 'article')))
    soup = BeautifulSoup(driver.page_source, 'lxml')
    movies = soup.find(class_='grid_view').find_all('li')
    for movie in movies:
        ind = movie.find('em').string
        title = movie.find(class_='title').string
        rating = movie.find(class_='rating_num').string
        intro = '' if movie.find(class_='inq') is None else movie.find(class_='inq').string
        print(f'{ind}: {title}, {rating}, {intro}')


username = ''  # put username here
password = ''  # put password here

options = webdriver.ChromeOptions()
options.add_argument('headless')

driver = webdriver.Chrome(options=options)
driver.get("https://www.douban.com")
wait = WebDriverWait(driver, 10)

print('Logging in...')
# Swith login method
driver.switch_to.frame(0)
login_switch = driver.find_element_by_class_name('account-tab-account')
login_switch.click()

# Login
user_field = driver.find_element_by_class_name('account-form-input')
pass_field = driver.find_element_by_class_name('account-form-input.password')
user_field.send_keys(username)
pass_field.send_keys(password, Keys.RETURN)

print('Open Top 250 and filter unseen')
# Open movie page
movie = wait.until(EC.presence_of_element_located((By.XPATH, '//*[@id="db-global-nav"]/div/div[4]/ul/li[3]/a')))
movie.click()

# Navigat to 排行榜->全部->我没看过的
windows = driver.window_handles
driver.switch_to.window(windows[1])
top = wait.until(EC.presence_of_element_located((By.LINK_TEXT, '排行榜')))
top.click()
top = wait.until(EC.presence_of_element_located((By.LINK_TEXT, '全部')))
top.click()
unseen = wait.until(EC.presence_of_element_located((By.XPATH, '//*[@id="mine-selector"]/input')))
unseen.click()

total = driver.find_element_by_class_name('count').text
total = int(re.search(r'\d+', total)[0])
print(f'还有 {total} 部未看')
pages = math.ceil(total / 25)
get_movie()

for page in range(2, pages + 1):
    next_page = driver.find_element_by_class_name('next')
    next_page.click()
    wait.until(EC.text_to_be_present_in_element((By.CLASS_NAME, 'thispage'), str(page)))
    get_movie()

driver.quit()
```

# Ajax

浏览器渲染出的网页有可能包含 JavaScript 的数据，此时使用 requests 爬取下来的内容不会包含这一部分。

Ajax 是 JS 异步更新页面上的内容的一种方法。即没有刷新页面，但页面内容可以变换。

对于 JS 内容，在 Chrome 浏览器开发者工具上的 Network 面板，通过选取 `XHR` 进行过滤，在 preview 中查找到需要的内容后，对这一 URL 做一个单独的请求即可。

# 伪装操作

大部分网站都做了反爬取的操作，以豆瓣为例，需要传递 User-Agent 信息假装是浏览器。因此在爬取时需要针对反爬取做一些伪装操作，不仅是为了成功爬取，同时也在保护自己的信息（ip 地址），并且不道德的爬取会给他人服务器带来巨大负担。

## User-Agent

构建一个常见浏览器的 User-Agent 列表，爬取时随机选取

## IP 代理

在使用 `requests` 库进行爬取时存在 IP 被封的风险，因此需要使用 IP 代理

```python
proxies = {'http': 'http://xx.xxx.xxx.xx'}
response = requests.get(url, proxies=proxies)
```

一般会构建一个 IP 池，需要用时从里面随机选取。IP 代理可以购买，也可以先爬取网上免费的 IP，检测可用后添加至 IP 池中。GitHub 上有一些构建 IP 池的爬取的项目，通过其提供的 API 可以很方便地得到可用的代理 IP

## 时间间隔

在请求之间等待随机时间，一方面减缓爬虫的频率，另一方面也使得行为更像人类，避免被封

```python
import time
import random

time.sleep(random.random()*3 + 4)  # 依据实际情况调整
```
