### 有图有真相
 
![image.png](http://upload-images.jianshu.io/upload_images/2815894-098006bc36d34759.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 要实现的目标
要想在斗图中立于不败之地，必须要有充足的表情包可用。想来撕逼的随时奉陪。也不用太多，就爬个网站的图来用用好了。也不能太欺负人是不。

### 如何做
 有了目标之后，接下来就是有预谋的实施了。
  用到的技术栈：Python BeautifulSoup etree
先说一下思路：无非就是遍历每个页面，把每个页面的图片地址提取出来，并按图片分组保存的本地文件夹中。
目标网站：https://www.doutula.com
#### 先把依赖的包引用进来
```#!usr/bin/env python
#_*_ coding: utf-8 _*_

import requests
import threading
from bs4 import BeautifulSoup
from lxml import etree #解析网页
import os #文件操作
```
#### 定义个获取HTML的函数，设置请求头
```
def getHtml(url): 
  header = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3107.4 Safari/537.36'}
  req = requests.get(url = url, headers = header)
  res = req.text
  return res
```
#### 分析下网页结构
页面地址是这样的：https://www.doutula.com/article/list/?page=2
每页有N个分组(懒得数)，每个分组有一个链接，这个是分组的详情页，页就是套图的内容。


![image.png](http://upload-images.jianshu.io/upload_images/2815894-cc7226df46ca09cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那我们就这样来操作，把每个套图的链接和标题存到一个list中，然后循环这个list，去抓取每页的表情。
```
def getGroupList(html):
  soup = BeautifulSoup(html, 'lxml') #实例化soup
  allImg = soup.find_all('a', class_='list-group-item') #获取所有图片链接
  groupList = [] #套图列表
  for link in allImg:
    img_html = getHtml(link['href']) #获取每个套图详情的源码
    imgGroup_title = link.find_all('div', class_='random_title')[0].contents[0]
    imgGroup = {'title': imgGroup_title, 'html': img_html}
    groupList.append(imgGroup)
  return groupList
```
剩下的就是保存图片了
```
def saveImg(imgUrl, imgTitle, groupTitle):
  #print imgTitle[0]
  #print imgTitle[0].encode('utf-8')
  #imgUrl = imgUrl.split('=')[-1][1:-2].replace('jp','jpg') #提取url
  if imgUrl[0:2] == '//':
   imgUrl = 'http:' + imgUrl
  print('正在下载'  + imgUrl)
  imgContent = requests.get( imgUrl).content
  fileName = 'H:\doutu2\\' + groupTitle + '\\' + imgTitle[0] + '.jpg'
  with open(unicode(fileName), 'wb') as f:
    f.write(imgContent)
```

源码地址：https://github.com/fuyunzhishang/doutuSpider
