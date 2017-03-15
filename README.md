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

* * *

### 本地提交并获取Status
		# -*- coding: UTF-8 -*-
		import re
		import requests
		import time
		from bs4 import BeautifulSoup

		class Accept(object):
		    def __init__(self):
			object.__init__(self)
			self.session = requests.Session()
			headers = {
			    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/48.0.2564.116 Safari/537.36'
			}
			self.session.headers.update(headers)
		    def login(self, username, password):
			url = 'http://acm.hdu.edu.cn/userloginex.php?action=login'
			data = {
			    'username': username,
			    'userpass': password,
			    'login': 'Sign In',
			}
			headers = {
			    'host': 'acm.hdu.edu.cn',
			    'origin': 'http://acm.hdu.edu.cn',
			    'referer': 'http://acm.hdu.edu.cn/'
			}
			r = self.session.post(url, data=data, headers=headers)

		    def getID(self):  # 新加功能
			status_url = 'http://acm.hdu.edu.cn/status.php?user=zzuliauto2'
			while True:
			    time.sleep(1)
			    req = self.session.get(status_url)
			    soup = BeautifulSoup(req.text, 'lxml')
			    n = 0
			    s = list()
			    for i in soup.table.find_all('table')[-2].find_all('tr'):
				if n == 0:
				    n += 1
				    continue
				else:
				    ans = i.find_all('td')
				    break
			    ans = ans[0].text
			    return ans
		    def getstatus(self,runID):
			status_url = 'http://acm.hdu.edu.cn/status.php?user=zzuliauto2'
			s = list()
			k = list()
			while True:
			    time.sleep(1)
			    k = []
			    s = []
			    req = self.session.get(status_url)
			    soup = BeautifulSoup(req.text, 'lxml')
			    n = 0
			    for i in soup.table.find_all('table')[-2].find_all('tr'):
				#print(i.text)
				for j in i :
				    if n <10:
					n+=1
					continue
				    else:
					s.append(j.string)
			    n = 0
			    for i in s:
				if i == str(runID):
				    break
				else:
				    n+=1
			    for i in range(0,9):
				k.append(s[n+i])
			    for i in k:
				if k[2]!= 'Queuing' and k[2] != 'Compiling' and k[2]!='Running':
				    return k
		    def getCE(self,runID):
			ce_url = 'http://acm.hdu.edu.cn/viewerror.php?rid='+str(runID)
			req = self.session.get(ce_url)
			req.encoding = 'gbk'
			soup = BeautifulSoup(req.text , 'lxml')
			soup = soup.find('pre')
			soup = soup.text
			print(soup)
		    def submit(self, problemID,code ,language):
			url = 'http://acm.hdu.edu.cn/submit.php?action=submit'
			data = {
			    'check': '0',
			    'problemid': str(problemID),
			    'language': str(language),
			    'usercode': code.read()
			}
			headers = {
			    'Connect-Type': 'application/x-www-form-urlencoded'
			}
			print('submitting problem: ', problemID)
			r = self.session.post(url, data=data, headers=headers)
			ans = c.getID()
			answer = c.getstatus(ans)
			if answer[2] != 'Compilation Error':
			    print(answer)
			else:
			    print(answer)
			    c.getCE(str(ans))

		if __name__ == '__main__':
		    c = Accept()
		    user = 'zzuliauto2'
		    password = '19951106'
		    c.login(user, password)
		    code = open('5901.txt','r')
		    c.submit(5091,code=code ,language=5)
* * * 

### 省略了更新日志是因为是直接从博客上搬运过来的

[Kelisiya csdn](http://blog.csdn.net/qq_33638791)
