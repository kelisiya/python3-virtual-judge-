# python3-virtual-judge-

由于学了一阵子的爬虫，因此顺手拿爬取OJ并模拟提交系统写一个VJ系统

* * *

### 项目工作

- 爬取题面：
   - 爬取文字题面
   - 处理图片和数学公式问题
   - 使用Class类把各种OJ分离，这里只以HDU为例
   - 利用json格式保题目信息
  
- 模拟提交：
	- 将提交代码下至本地		
	- 模拟POST提交
	- RunID返回status信息
* * *

### 爬取HDU页面
'''
	
	# -*- coding=utf-8 -*-
	import requests
	import re
	import time
	from bs4 import BeautifulSoup
	class spider(object):
	   def get_PD(self,proID):
		url = 'http://acm.hdu.edu.cn/showproblem.php?pid='
		html = requests.get(url + str(proID)).content
		soup= BeautifulSoup(html, 'lxml')

		print("======题目名称:======")
		hduoj = soup.find_all('h1')
		for i in hduoj:
		    title = i.contents[0].string
		    break
		print(title)

		soup = soup.find('table', attrs={'width': '980', 'border': '0', 'align': 'center', 'cellspacing': '0'})
		soup = soup.find_all('div',attrs={'class':'panel_content'})
		n = 0
		s = list()
		for i in soup:
		    if n == 0 :
			n+=1
			print("======Problem Description======")
			try:  #对图像题的异常处理
			    title = i.img['src']
			    for j in title:
				if n < 2:
				    n += 1
				    continue
				s.append(j)
			    strlen = ''.join(s)
			    url2 = 'http://acm.hdu.edu.cn/' + strlen
			    print(i)
			    print(url2)
			    continue
			except:
			    print(i)
			    continue
		    if n==1:
			n+=1
			print("======Input======")
			print(i)
			continue
		    if n==2:
			n+=1
			print("======Output======")
			print(i)
			continue
		    if n==3:
			n+=1
			print("======Sample Input======")
			print(i)
			continue
		    if n==4:
			n+=1
			print("======Sample Output======")
			print(i)
			continue
		    if n==5:
			n+=1
			print("======Author======")
			print(i)
			continue
		    if n>5:
			break
	if __name__ == '__main__':
	    c = spider()
	    problemID = input("请输入你想查询的题号:")
	    c.get_PD(problemID)

'''
