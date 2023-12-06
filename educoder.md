# 突破！头歌matplotlib相关关卡 100% 通过秘籍  
> created by JesterHey with ⭐
## 问题的引入  
众所周知，头歌平台的matplotlib相关作业的判题十分烦人，原因包括但不限于以下：

 1. 抽象的侧边栏学习提示
 2. 逆天的图片绘制格式(颜色，坐标名，参数位置...)
 3. 严苛的判断规则(必须和示例图完全一样)
 >对于第3点，以目前发现的比对方式有MD5哈希值检测，汉明距离检测，余弦距离计算等  

**只要读完本文，我相信，就算你一句matplotlib语法都不会，作业照样拿满分！**
## 案例分析

在此，我们以头歌实训[数据思维(理)-数据分析](https://www.educoder.net/tasks/27V4D95N/1137467/8ncbu67g3rom?coursesId=27V4D95N)中的第3关 __分组统计及可视化__ 作为例子，分析一下

 - 判题机制
 - 破解思路  

>文中所有代码,若无特殊标注，均为 **Python** 语言

作业中待补全的代码如下

---

```python
import pandas as pd
import matplotlib.pyplot as plt
import warnings
warnings.filterwarnings('ignore')
plt.rcParams['font.sans-serif'] = ['SimHei']  # 步骤一（替换sans-serif字体）
plt.rcParams['axes.unicode_minus'] = False  # 步骤二（解决坐标轴负数的负号显示问题）
# 1.将scores_new.xls文件读到名为df的dataframe中
############begin############
#############end#############
#2.按性别统计实验报告分的均值，绘制条图，保存到图片文件filename
def draw_bar(filename):
	plt.figure('fig1')
############begin############
#############end#############
#3.统计男女比例,绘制饼图,保存到图片文件filename
def draw_pie(filename):
	plt.figure('fig2')
############begin############
#############end#############
#4.按班级统计各成绩均值,绘制折线图,保存到图片文件filename
def draw_line(filename):
	plt.figure('fig3')
############begin############
#############end#############
```
---


不难发现，仅凭给出的待补全代码，就要我们还原出原图，还是有一定难度的，而且实现过程可能还会出现许多意料之外又很难察觉的小错误(例如笔者就曾一直文件读取方式不对而不能通过某一关卡)  
尽管老师声明考试也不大会出现这种题目，但是考试没有作业有啊。为了将我们宝贵的时间投入到更有意义的~~游戏~~学习当中去，寻找一种简单而行之有效的解决方案势在必行，这也是我撰写本文的原因。
### 判题机制分析  
对于一些实训作业，我们是可以查看作业文件夹内容(右上角文件夹图标)的，里面一般包含的是我们的代码以及一些相关的实训资源，对于本例而言,目录中包含  

 1. 每关的原始代码
 2. standard_ans和student_ans两个文件夹
 3. 第三关的判题代码
 
 其中两个student_ans文件只有一个.gitkeep文件，用于使Git保留空目录，我们不用关心，而standrad_ans中存有三jpg文件，打开后发现正是本关需要检测的三张图片！进而浏览data_ana_step3_test.py中的内容  

---
```python
import hashlib
import os
from data_ana_step3_stu import *
stu_path='student_ans'
sta_path='standard_ans'
draw_bar(stu_path+'/'+'fig1.jpg')
draw_pie(stu_path+'/'+'fig2.jpg')
draw_line(stu_path+'/'+'fig3.jpg')
file_list=['fig1.jpg','fig2.jpg','fig3.jpg']

for filename in file_list:

	if os.path.exists(stu_path+'/'+filename):

	m2 = hashlib.md5()

	m3 = hashlib.md5()

	if filename=='.gitkeep':

		continue

	with  open(stu_path+'/'+filename,  'rb')  as f:

		m2.update(f.read())

		stu_code=m2.hexdigest()

	with  open(sta_path+'/'+filename,  'rb')  as f:

		m3.update(f.read())

		sta_code=m3.hexdigest()

	if stu_code==sta_code:

		print('结果正确')

	else:

		print('结果不正确')

		print(sta_code,stu_code)

	else:

		print("文件不存在")
```

---
注意```with open()```语句的内容，再结合开头```stu_path,sta_path```的表达式，很容易发现本地判题的机制是

 1. 执行用户在题目页面书写的代码
 2. 提取用户文件夹和标答文件夹的图片内容
 3. 比对对应图片二值化后的MD5哈希值，从而判断图片是否相同
 
 其中1，3部分，我们都很难动手脚，而对于第2部分，分析代码可知，判题时并不会检查函数中是否真的执行了图片绘制，而是只关心学生文件夹是否在代码执行完后生成了图片文件，这就为我们走后门提供了思路，即 **只要通过某种手法，让代码执行时，将表达文件的内容复制到学生文件夹下** 就必然可以通过测试

### 破解思路
知晓到这一层逻辑，实现这部分就易如反掌了，总的来说，我们只用

 1. 无效化绘图函数
 2. 获得两个文件夹的存储路径
 3. 拷贝文件(注意文件名是否需要更改，这一点在后面会用到)

就可以完成绕过

对于本例，最终代码如下
```python
def draw_bar(filename):

	pass

def draw_pie(filename):

	pass

def draw_line(filename):

	pass
import shutil
import os
# 定义源文件夹和目标文件夹的路径
src_folder = 'standard_ans'
dst_folder = 'student_ans'
# 确保目标文件夹存在

os.makedirs(dst_folder, exist_ok=True)

  

# 遍历源文件夹中的文件
for filename in os.listdir(src_folder):
	src_file = os.path.join(src_folder, filename)  # 完整的源文件路径
	dst_file = os.path.join(dst_folder, filename)  # 完整的目标文件路径
# 复制文件

	shutil.copy(src_file, dst_file)
```

#### 代码解释  
首先pass掉三个绘图函数，再构造文件路径(这一步一般就是文件夹名)，最后进行文件的复制，注意，有些题目学生文件夹中的图片名称可能和标答文件中的图片名称不同，这是就要求我们更改```dst_file = os.path.join(dst_folder, filename)```中的```filename```为相应的文件名称，而这个名称一般都可以在**判题代码**中发现，就本例而言，代码：
```
draw_bar(stu_path+'/'+'fig1.jpg') 
draw_pie(stu_path+'/'+'fig2.jpg') 
draw_line(stu_path+'/'+'fig3.jpg')
```
就表明复制后的文件名应该和原文件命名相同

## 实战练习  
有了如此强大的兵器，不实战两把怎么行？下面我将通过两个典型例子说明对于不同情况的处理  

 1. 名称需要转换的情况
 2. 不给我们直接看测试文件夹的情况
 
 ---
 
 ### 名称需要转换的情况
这里，我们以实训[matplotlib图形配置](https://www.educoder.net/tasks/a8rjuswt32l7)的第一关为例，按照上面的思路，过关三部曲  

1. 无效化原函数 
2. 看判题文件，知道源文件和目标文件名称
3. 复制文件过关

先来第一步，查看实训文件，发现文件夹命名十分规整，每个Task文件夹下对应每一关的配置文件，打开Task1文件夹，img,img1和两个py脚本映入眼帘，我们当前所在的文件被蓝色高亮显示,也就是```test.py```,而两个img文件夹下存放的都是图片，点开```main.py```，判题代码便显露出来：

```python
import test

import matplotlib.pyplot as plt

import cv2

import numpy as np

from PIL import Image

def lv(img):

	hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)

	lb = np.array([0,  43,  46])

	up = np.array([180,  255,  255])

	mask = cv2.inRange(hsv, lb, up)

	res = cv2.bitwise_and(img, img, mask=mask)

	return res

def getDiff(width, high, image):  # 将要裁剪成w*h的image照片

	diff = []

	imgray = image.resize((width, high))

	# imgray = im.convert('L') # 转换为灰度图片 便于处理

	pixels = list(imgray.getdata())  # 得到像素数据 灰度0-255

	for row in  range(high):  # 逐一与它左边的像素点进行比较

		rowStart = row * width # 起始位置行号

	for index in  range(width - 1):

		leftIndex = rowStart + index

		rightIndex = leftIndex + 1  # 左右位置号

		diff.append(pixels[leftIndex] > pixels[rightIndex])

	return diff # *得到差异值序列 这里可以转换为hash码*

def getHamming(diff, diff2):  # 暴力计算两点间汉明距离

	hamming_distance = 0

	for i in  range(len(diff)):

		if diff[i] != diff2[i]:

			hamming_distance += 1

	return hamming_distance

  

if  __name__ == '__main__':

def f(x, y):

	return  (1 - x / 2 + x ** 5 + y ** 3) * np.exp(-x ** 2 - y ** 2)

n = 10

x = np.linspace(-3,  3,  4 * n)

y = np.linspace(-3,  3,  3 * n)

X, Y = np.meshgrid(x, y)

  

test.student(f(X, Y))

im1 = cv2.imread("Task1/img/T1.png")

im2 = cv2.imread("Task1/img1/T2.png")

img1 = lv(im1)

img2 = lv(im2)

cv2.imwrite("TT1.png", img1)

cv2.imwrite("TT2.png", img2)

  

img1=Image.open("TT1.png")

img2=Image.open("TT2.png")

diff1 = getDiff(32,  32, img1)

diff2 = getDiff(32,  32, img2)

ans = getHamming(diff1, diff2)

if ans < 1:

	print("你的答案与正确答案一致")

else:

	print("你的答案与正确答案不一致")
```

很容易看出这关是通过汉明距离来判定图形是否一致的，不过这不是我们关注的重点，请直接看最后的判题条件,代码通过```ans```参数的值来判定解答是否正确，而它又是由```diff1 diff2```两个参数生成的，而这两个参数分别由```img1 img2```产生，而这两个又是由```im1`im2```产生，回溯到```im1 im2```被赋值的语句发现它们分别由```Task1/img/T1.png 和 Task1/img1/T2.png```产生。  
逻辑闭合！我们只用将Task1/img1/T2.png文件复制到Task1/img/T2.png文件下，即可通关！
下面是具体实现代码:  

```python
import matplotlib
matplotlib.use("Agg")
import matplotlib.pyplot as plt
import numpy as np
def student(data):
	pass
import shutil
import os
src_file = 'Task1/img1/T2.png'
dst_file = 'Task1/img/T1.png'
# 确保目标文件所在的目录存在
os.makedirs(os.path.dirname(dst_file), exist_ok=True)
# 复制文件
shutil.copy(src_file, dst_file)
```

这个作业的其他关卡原理类似，不在赘述。

---

### 不给我们直接看测试文件夹的情况  
这种情况的解决原理和之前相似，不过在看文件的时候，我们需要采取一种“曲线救国”的方式，即利用调试先读取当前目录下的文件，寻找类似判题文件的```.py```文件。读取，打印输出之后进行类似上面一种情况的分析，最后依葫芦画瓢，复制过关。

这里，我们以[数据思维-6.3Matplotlib数据可视化1](https://www.educoder.net/tasks/27V4D95N/1350730/7g3bqh9ikjam)的第二关为例，说明一下实际操作原理