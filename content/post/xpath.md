* 导包
pip3 install lxml
from lxml import etree
* 定位
	1. 通过id定位
		xpat="//a[@id='xxx']"
	2. 通过属性定位
		xpath="//a[@name='']"
	3. 多个条件
		xpath="//a[@type='' and @name='']"
	4. text()方法定位
		xpath="//a[text()='']"
	5. 模糊定位
		xpath="//a[contains(@属性，'属性值')]"
	6. 获取指定属性的属性值
		xpath('//input[@name="SERVERTYPE"]')
	7. 获取a标签中href中的值
		html.xpath('//li/a//@href')


# 多线程
from concurrent.futures import ThreadPoolExecutor
ProcessPoolExecutor
1. map
	```python
	from concurrent import futures
	urls=[1,2,3,4,5]
	def downloads(urls):
		print("正在下载......")
		return "ok"
	with futures.ThreadPoolExecutor(10) as ex:
		res=ex.map(downloads,urls)
		for i in res:
			print(i)
	```


2. 带回调函数

	t.submit(get_page, url).add_done_callback(parse_page)

	```python
			from concurrent import futures
		urls=[1,2,3,4,5]
		def downloads(urls):
			print("正在下载......")
			return "ok"
		def call_back(a):
			print(a.result())
			print("over")
		with futures.ThreadPoolExecutor(10) as ex:
			for url in urls:
				ex.submit(downloads,url).add_done_callback(call_back)
	```	


3. 其他函数方法

```
from concurrent import futures
import time
urls=[1,2,3,4,5]

def downloads(url):
	print("正在下载......")
	print(1/0)
	time.sleep(3)
	return url


def call_back(a):
	print(a.result())
	print("over")

with futures.ThreadPoolExecutor(10) as ex:
	future_tasks=[ex.submit(downloads,url) for url in urls]
	

	for f in future_tasks:
		if f.running():
			print("%s is running" %str(f))
		if f.exception():
			print("粗错啦%s" %str(f))

	#results=futures.wait(future_tasks)
	#done=results[0]
	#for x in done:
	#	print(x)

```

## future对象
	submit函数返回future对象，future提供了跟踪任务执行状态的方法,Future实例可以被Executor.submit()方法创建。除了测试之外不应该直接创建。

```
cancel()：尝试去取消调用。如果调用当前正在执行，不能被取消。这个方法将返回False，否则调用将会被取消，方法将返回True
cancelled()：如果调用被成功取消返回True
running()：如果当前正在被执行不能被取消返回True
done()：如果调用被成功取消或者完成running返回True
result(Timeout = None)：拿到调用返回的结果。如果没有执行完毕就会去等待
exception(timeout=None)：捕获程序执行过程中的异常
add_done_callback(fn)：将fn绑定到future对象上。当future对象被取消或完成运行时，fn函数将会被调用
```
## wait方法
	wait方法接收返回一个tuple元祖,tuple中包含两个set集合,一个是completed(已完成),另外一个是uncompleted(未完成)
	他接收三个参数FIRST_COMPLETED, FIRST_EXCEPTION 和ALL_COMPLETE，默认设置为ALL_COMPLETED。

	1. ALL_CPMPLETED 程序会阻塞直到线程池里面的所有任务都完成,再执行主线程
	2. FIRST_COMPLETED参数，程序并不会等到线程池里面所有的任务都完成。

```python
def download_many(cc_list):
    cc_list = cc_list[:5]
    with futures.ThreadPoolExecutor(max_workers=3) as executor:
        to_do = []
        # 用于创建并排定 future
        for cc in sorted(cc_list):
            # submit 方法排定可调用对象的执行时间然后返回一个future，表示这个待执行的操作
            future = executor.submit(download_one, cc)
            to_do.append(future)
            msg = 'Scheduled for {}: {}'
            print(msg.format(cc, future))
        
        results = []
        # 用于获取future 结果
        # as_completed 接收一个future 列表，返回值是一个迭代器，在运行结束后产出future
        for future in futures.as_completed(to_do):
            res = future.result()
            msg = '{} result: {!r}'
            print(msg.format(future, res))
            results.append(res)
    
    return len(results)



```

## 异常处理
```python
def download_many(cc_list):
    cc_list = cc_list[:5]
    with futures.ThreadPoolExecutor(max_workers=20) as executor:
        to_do_map = {}
        for cc in sorted(cc_list):
            future = executor.submit(download_one, cc)
            to_do_map[future] = cc
            msg = 'Scheduled for {}: {}'
            print(msg.format(cc, future))
        results = []
        for future in futures.as_completed(to_do_map):
            try:
                res = future.result()
            except requests.exceptions.HTTPError as exc:
                # 处理可能出现的异常
                error_msg = '{} result {}'.format(cc, exc)
            else:
                error_msg = ''
            if error_msg:
                cc = to_do_map[future]  # <16>
                print('*** Error for {}: {}'.format(cc, error_msg))
            else:
                msg = '{} result: {!r}'
                print(msg.format(future, res))
                results.append(res)
    return len(results)
```


## selenium
查找指定的标签并点击
driver.find_element_by_xpath("//div[@id='btnplayvideopic']").click()
查找到标签后,获取标签中属性值
print(soure[0].get_attribute('src'))
print(ss.get_attribute("innerHTML"))                # 用innerHTML 会返回元素的内部 HTML， 包含所有的HTML标签。
print(ss.get_attribute('textContent'))              #用textContent 和 innerText 只会得到文本内容，而不会包含 HTML 标
from selenium import webdriver
chrome_options = webdriver.ChromeOptions()
chrome_options.add_argument('--headless')
chrome_options.add_argument('--disable-gpu')
chrome_options.add_argument('--no-sandbox')
driver = webdriver.Chrome(chrome_options=chrome_options) 
options.add_argument('--ignore-certificate-errors')
d=driver.get("http://3838bbb.com/?m=vod-index-pg-2.html")

https://www.cnblogs.com/imyalost/p/7846653.html
https://www.cnblogs.com/ospider/p/5911339.html
from selenium.webdriver.support.select import Select
Select(driver.find_element_by_name("select")).select_by_index("3")
Select(driver.find_element_by_name("select")).select_by_index("4")
driver.find_element_by_xpath("//button[@id='fastpostsubmit']").click()
etree.tostring(tests[0])

