

import os
import time
import requests
from bs4 import BeautifulSoup
from selenium import webdriver
from scrapy.selector import Selector
from selenium.webdriver.chrome.options import Options
# 无头浏览器 这样浏览器就不会弹出
options = Options()
options.add_argument('--headless')
options.add_argument('blink-settings=imagesEnabled=false')  # 不加载图片
downloadname = r'G:\漫画2'
url_base = input('\n-----------------------请在下方输入起始链接-----------------------\n')
url = str(url_base)
headers = {'Referer': url,
           "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36"}
dr = webdriver.Chrome(options=options)
def get_link_url(url):
    response = requests.get(url,headers=headers)
    response.encoding = response.apparent_encoding
    page = BeautifulSoup(response.text, "lxml")
    total_html = page.find(name='ol', attrs={"id": "mh-chapter-list-ol-0"})
    # 以下为避免崩溃后重头下载的处理方案
    # #设定一个列表组，将所有元素通过for in 存入 b[]内，之后通过调用b列表内指定元素 达到下载指定章节的效果 b[起始页的前一页：留空为最后一页]
    b = []
    for i in reversed(total_html.find_all('li')):
        b.append(i)
    c = b[:]
    for i in c:
        href = 'http://www.php06.com'+i.a['href']
        dr.get(href)
        pages = dr.find_elements_by_xpath("//*[@id='k_pageSelect']/option")
        tempNum = len(pages)
        comic_name = dr.find_elements_by_xpath("//div[@class='w996 title pr']/h1/a")[0].text
        cheapter = dr.find_elements_by_xpath("//div[@class='w996 title pr']/h2")[0].text
        print(comic_name,cheapter,'本章图片共计：', tempNum, '张')
        for page in range(1,tempNum+1):
            #time.sleep(2)
            source = dr.page_source
            selector = Selector(text=source)
            image_url = selector.xpath("//*[@id='qTcms_pic']/@src").get()
            dr.find_element_by_xpath("//*[@id='k_next']").click()
            download(image_url,comic_name,cheapter,page)
def download(image_url,comic_name,cheapter,page):
    imgs = requests.get(image_url,headers=headers)
    dirnames =  os.path.join(downloadname,comic_name)
    filename = cheapter+'-'+str(page)+'.jpg'
    if not os.path.exists(dirnames):
        os.makedirs(dirnames)
    os.chdir(dirnames)  # 打开要写入内容的文件夹
    with open(filename, 'wb') as fd:  # 将图片写入
        fd.write(imgs.content)
        fd.close()
    with open('快速查看.html', 'a') as fdd:
        fdd.write("<div align='center'><img src='{}'></div>".format(filename))
        fdd.close()
        print(filename,'已写入网页')

if __name__ == "__main__":
    get_link_url(url)
    dr.quit()
print('\n -----------------------已经下载完毕！请打开目录查看.-----------------------------')
