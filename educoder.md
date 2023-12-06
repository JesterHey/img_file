# 突破！头歌matplotlib相关关卡 100% 通过秘籍  
> created by JesterHey with ⭐
## 问题的引入  
众所周知，头歌平台的matplotlib相关作业的判题十分烦人，原因包括但不限于以下：

 1. 抽象的侧边栏学习提示
 2. 逆天的图片绘制格式(颜色，坐标名，参数位置...)
 3. 严苛的判断规则(必须和示例图完全一样)
 >对于第3点，目前发现的比对方式有MD5哈希值检测，汉明距离检测，余弦距离计算等  

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

这个作业的其他关卡原理类似，不再赘述。

---

### 不给我们直接看测试文件夹的情况  
这种情况的解决原理和之前相似，不过在看文件的时候，我们需要采取一种“曲线救国”的方式，即利用调试先读取当前目录下的文件，寻找类似判题文件的```.py```文件。读取，打印输出。之后进行类似上面一种情况的分析，最后依葫芦画瓢，复制过关。

这里，我们以[数据思维-6.3Matplotlib数据可视化1](https://www.educoder.net/tasks/27V4D95N/1350730/7g3bqh9ikjam)的第二关为例，说明一下实际操作原理。

进入关卡，发现不能直接读取文件目录，不过这也是马奇诺防线，直接调用下面的代码读取当前文件夹下的所有文件：

```python
def get_all_files_in_current_directory():
	current_directory = os.getcwd()  # 获取当前工作目录
	all_files = []
	for root, dirs, files in os.walk(current_directory):
		for  file  in files:
			all_files.append(os.path.join(root,  file))
	return all_files
print(get_all_files_in_current_directory())
```

在调试中执行代码，会返还一大堆文件信息

```
['/data/workspace/myshixun/2007股市.xlsx', '/data/workspace/myshixun/step1.py', '/data/workspace/myshixun/step2.py', '/data/workspace/myshixun/国民生产总值.csv', '/data/workspace/myshixun/step1_1.png', '/data/workspace/myshixun/step1_1_t.png', '/data/workspace/myshixun/step1_2.png', '/data/workspace/myshixun/step1_2_t.png', '/data/workspace/myshixun/step1_3.png', '/data/workspace/myshixun/step1_3_t.png', '/data/workspace/myshixun/step1_stud.py', '/data/workspace/myshixun/step2_1.png', '/data/workspace/myshixun/step2_1_t.png', '/data/workspace/myshixun/step2_2.png', '/data/workspace/myshixun/step2_2_t.png', '/data/workspace/myshixun/step2_stud.py', '/data/workspace/myshixun/.git/description', '/data/workspace/myshixun/.git/HEAD', '/data/workspace/myshixun/.git/config', '/data/workspace/myshixun/.git/shallow', '/data/workspace/myshixun/.git/packed-refs', '/data/workspace/myshixun/.git/index', '/data/workspace/myshixun/.git/COMMIT_EDITMSG', '/data/workspace/myshixun/.git/FETCH_HEAD', '/data/workspace/myshixun/.git/ORIG_HEAD', '/data/workspace/myshixun/.git/info/exclude', '/data/workspace/myshixun/.git/hooks/fsmonitor-watchman.sample', '/data/workspace/myshixun/.git/hooks/pre-commit.sample', '/data/workspace/myshixun/.git/hooks/pre-receive.sample', '/data/workspace/myshixun/.git/hooks/post-update.sample', '/data/workspace/myshixun/.git/hooks/pre-push.sample', '/data/workspace/myshixun/.git/hooks/applypatch-msg.sample', '/data/workspace/myshixun/.git/hooks/pre-applypatch.sample', '/data/workspace/myshixun/.git/hooks/commit-msg.sample', '/data/workspace/myshixun/.git/hooks/update.sample', '/data/workspace/myshixun/.git/hooks/prepare-commit-msg.sample', '/data/workspace/myshixun/.git/hooks/pre-rebase.sample', '/data/workspace/myshixun/.git/refs/heads/master', '/data/workspace/myshixun/.git/refs/remotes/origin/HEAD', '/data/workspace/myshixun/.git/refs/remotes/origin/master', '/data/workspace/myshixun/.git/objects/40/9013e597f95dccec2f7225d097f86e90227ab0', '/data/workspace/myshixun/.git/objects/6c/dc7c7714d38c85238b4e1ad9dc927065f76f9c', '/data/workspace/myshixun/.git/objects/7d/aef5ee27afb8165e8f1c3aaca503431e284e87', '/data/workspace/myshixun/.git/objects/95/39b8f25b824e08d2dfd12656e987189ac0180b', '/data/workspace/myshixun/.git/objects/95/0c8c6afaeae647ecafb05e1ede2eb2a5447f75', '/data/workspace/myshixun/.git/objects/95/6a25247453fd04aab5fa81ae8b870c8df03471', '/data/workspace/myshixun/.git/objects/cd/9857adfa57e0b2da6acae5c35559fc0c782dbb', '/data/workspace/myshixun/.git/objects/cd/d5c31ec572fd003ee00df0959489d6aab2c245', '/data/workspace/myshixun/.git/objects/55/6eeaf753485206d50fe54aaeb584cf0fa3b62f', '/data/workspace/myshixun/.git/objects/70/5574e6bd03fe57376ca1d80e0060d1db7a8619', '/data/workspace/myshixun/.git/objects/70/45a3b14ed1d02d48575f518c0167730a5e0e35', '/data/workspace/myshixun/.git/objects/70/ac53e71e38a62d3669200a679020e1fc940818', '/data/workspace/myshixun/.git/objects/41/12397ec53d9984bd5de11295ad9d2ca106d9fe', '/data/workspace/myshixun/.git/objects/c1/abdfcf84da269b5292fc3adef5e01b879f2427', '/data/workspace/myshixun/.git/objects/c1/64d667e54d45bd9e8d910108d80bd84accf420', '/data/workspace/myshixun/.git/objects/bd/02e0bdd9c3b04db9d25fa80d63ea41d31ada6a', '/data/workspace/myshixun/.git/objects/ad/cba6fd0702aa480559c2119f694e952bee2f6a', '/data/workspace/myshixun/.git/objects/ad/288ee643220288ec9a5c7cfd3bdfdc04474bc1', '/data/workspace/myshixun/.git/objects/e4/a2092cb6678404e99e5b3ad12b9c8933363ac3', '/data/workspace/myshixun/.git/objects/e4/72e8693b07d1e3266821fb96d8fadfec9ad042', '/data/workspace/myshixun/.git/objects/e4/bc6dbbfc2cce478ad341312eedf23628c162ee', '/data/workspace/myshixun/.git/objects/96/85c16dc3641b23f57bfd84a37e2562ab6e0025', '/data/workspace/myshixun/.git/objects/96/4618456d708f704ddff15daa91f2b5b0208fa2', '/data/workspace/myshixun/.git/objects/e0/9bfbd4ba28228bd6255077322d81eb4293e07d', '/data/workspace/myshixun/.git/objects/6d/274895d09a74a385e186055c0973fb177880e4', '/data/workspace/myshixun/.git/objects/c9/67102f7489604cd42095433a0ab80612d1d4ca', '/data/workspace/myshixun/.git/objects/c9/1ee507bda725d52df7777237748aa17ac4abf1', '/data/workspace/myshixun/.git/objects/f2/ba82335efed812bb622760d38bd2586d4021a5', '/data/workspace/myshixun/.git/objects/0b/b7bdc3792b0cf31b08a1b2490f8fb3cf90550f', '/data/workspace/myshixun/.git/objects/0b/d8b228a1f8ed034358181bd58435d21f8b3aab', '/data/workspace/myshixun/.git/objects/8f/d8383a1e32156810bd249d524c91215d66e9f8', '/data/workspace/myshixun/.git/objects/e3/de9579264ece6311dbce7e90de9b4857c82494', '/data/workspace/myshixun/.git/objects/61/13891abae67bb12ae8d57e176f79e473648dae', '/data/workspace/myshixun/.git/objects/32/6213ea677079918a66843e991ccba7dcd343d1', '/data/workspace/myshixun/.git/objects/32/58a8077ad9f87a3842ac2e5a4d8ade1b5af691', '/data/workspace/myshixun/.git/objects/eb/d4cc74f76f4b7edb3d785ea561bd48c6197bd7', '/data/workspace/myshixun/.git/objects/75/2a42c2a846ae0109a0c942b984abc4e38f61cd', '/data/workspace/myshixun/.git/objects/75/ff99b0d2328d8f61d7c1b9e423ea43ee7cc396', '/data/workspace/myshixun/.git/objects/50/a454f5e07068729eeabba607ee9ce2372ff107', '/data/workspace/myshixun/.git/objects/a6/dfdb80100155e552f1bc289ae7b1c46552d603', '/data/workspace/myshixun/.git/objects/a6/70bdfa2f87552a87705d1af9b45bfb85dd3903', '/data/workspace/myshixun/.git/objects/ee/f6d3d239bbf025fbc58a97ef42cc26745c7fe1', '/data/workspace/myshixun/.git/objects/ee/8b22a96ea4306fbb52b5fdc54790a685afe175', '/data/workspace/myshixun/.git/objects/56/e53b1ee49381831cb7c0ee37cd5dfd7dc0bc06', '/data/workspace/myshixun/.git/objects/56/7a0bf371a72ef85d4368c66904e8d7bb43cafa', '/data/workspace/myshixun/.git/objects/5b/d2c08a943802183ac23215c9777c40765476a7', '/data/workspace/myshixun/.git/objects/91/3180986692fce5a33c6e5d6eba97109c9099c8', '/data/workspace/myshixun/.git/objects/9b/a14dcad8c6af7d69d1df885ae414ec922c6f3b', '/data/workspace/myshixun/.git/objects/3b/9c9b80cb1ef9bb39f7505f1472273148dbd0e9', '/data/workspace/myshixun/.git/objects/3b/15e37c8a985b1fcdd7a66fc8f4fa819b5815a2', '/data/workspace/myshixun/.git/objects/83/f881249835e947f09aad3ddbd3fa23ea7fe315', '/data/workspace/myshixun/.git/objects/17/1a33561cdf31691aa61f109fa095d2313e00b7', '/data/workspace/myshixun/.git/objects/17/4be2b38a66dab749a49e853b9d207ad1850761', '/data/workspace/myshixun/.git/objects/25/455cc225bd75342b9ef70d6c39ed6675923d08', '/data/workspace/myshixun/.git/objects/22/1d9ec5c27826f1d54ac22442255bedbf4881d8', '/data/workspace/myshixun/.git/objects/22/04a2147c209e6e47723cf698cad93f3598c9c5', '/data/workspace/myshixun/.git/objects/22/d6cfce0fab1ff3249d6aab9ec9dad5c00dd35f', '/data/workspace/myshixun/.git/objects/df/d16dbbdbd2c8cff31e6e3019f057acb6cabd26', '/data/workspace/myshixun/.git/objects/73/a8728946d2118a664f83a546b71ff64394711e', '/data/workspace/myshixun/.git/objects/87/a456d8be00950eae260c90c9d324aa157127f7', '/data/workspace/myshixun/.git/objects/92/55303ffb216e03c4eab5c8326d32dbc18698b9', '/data/workspace/myshixun/.git/objects/dc/c525c267a6f4cc096300bd22e482d0534b0bf5', '/data/workspace/myshixun/.git/objects/dc/8c78711b85c757ca5bdb7674b1d045eb9918d8', '/data/workspace/myshixun/.git/objects/67/2ab85ef8e2648bb6eebf1426a296f52a1e4abd', '/data/workspace/myshixun/.git/objects/21/1133d81dd7e0675910e0f6e92f03e47c7f5fc0', '/data/workspace/myshixun/.git/objects/ff/7a694fea82390594cab0481144a9b078a566de', '/data/workspace/myshixun/.git/objects/6b/14958fcff2475d7634482bb11d6da5035cb58e', '/data/workspace/myshixun/.git/objects/d7/ba8a6a9e2159732786c47f8857db70bdf15bc1', '/data/workspace/myshixun/.git/objects/d7/2d6bc76ead3793b74062f76809303e638e3442', '/data/workspace/myshixun/.git/objects/11/1aacd24914a22dae69467f07d3d2737d4e15a1', '/data/workspace/myshixun/.git/objects/dd/abde4949c1ccc9f8c4deab1f2f95af96d96890', '/data/workspace/myshixun/.git/objects/33/2a4019c47c7ce0ae61fcedbaa2f7440bc012a0', '/data/workspace/myshixun/.git/objects/28/1225500ae12c768cd902ea3b7815098e5f6c6c', '/data/workspace/myshixun/.git/objects/49/0f3a41ab3431da9bb77a078d6b8c7b753dba3a', '/data/workspace/myshixun/.git/objects/1d/539f0a4bb0987814850f90f15c39bfacb0bebe', '/data/workspace/myshixun/.git/objects/1d/a2d58231e9364f0e6093876f8bbd6d91f57896', '/data/workspace/myshixun/.git/objects/d5/2ad156cb31bd02d79bd7b68a760029030127f0', '/data/workspace/myshixun/.git/objects/7c/31287ce9415bc72123d89dfdaa0b7be98730e9', '/data/workspace/myshixun/.git/objects/e8/ed5372be6450793fa8742588e55b3aca031482', '/data/workspace/myshixun/.git/objects/86/11a0c608956ef64a5b8bae63d692e9a967f70f', '/data/workspace/myshixun/.git/objects/59/ec0120cb6252db9e354b99e0513da66f9fa202', '/data/workspace/myshixun/.git/objects/59/e1151e4e256e2948e63894d8a521d9fb0a292b', '/data/workspace/myshixun/.git/objects/f0/967cbde58ab7db0b7e249c7a3a4fcf3e509516', '/data/workspace/myshixun/.git/objects/f0/cc62df7a3e2ff9881db503f8fe67183fc0b10a', '/data/workspace/myshixun/.git/objects/f0/d716481d08e156eb4034123af6133d85985621', '/data/workspace/myshixun/.git/objects/43/6b1bbdfe900529cc2f802661bd15b516431122', '/data/workspace/myshixun/.git/objects/78/94dc70a4b42bb6455262d6fcbd9e95cad457a2', '/data/workspace/myshixun/.git/objects/78/1b0eaf7b7809ba18852cc7f1bd70634f2489f8', '/data/workspace/myshixun/.git/objects/a5/0c8d86dfaab94a6b935454d17692dbbb7c7274', '/data/workspace/myshixun/.git/objects/a5/bab03b9d7027f7eadc1883cd1412316a6078db', '/data/workspace/myshixun/.git/objects/ab/2a59e4b549e16d0bc281168f036da20f9f1f73', '/data/workspace/myshixun/.git/objects/ba/8117db7def5e61bd70dd799f15d5dd65fa1e1a', '/data/workspace/myshixun/.git/objects/ba/d24045e6c4009bc1a51148adde68e6294a5a14', '/data/workspace/myshixun/.git/objects/cb/4873ac900b527e01e649cba35880fdf17cec99', '/data/workspace/myshixun/.git/objects/cb/34bb7dba44fa995de80199c6ab4fff741fdd73', '/data/workspace/myshixun/.git/objects/81/431dd9658efc30f7e0170d3814b1622fc93cba', '/data/workspace/myshixun/.git/objects/81/c9b5476429765174e910809ad2dab3a5e459b9', '/data/workspace/myshixun/.git/objects/90/589b35bae26c4763b195f53d73c803a99462d9', '/data/workspace/myshixun/.git/objects/af/b6d46980d0c50680d1a0f27b39bb50a7696271', '/data/workspace/myshixun/.git/objects/37/685460458e216c5e07964a1f4b2a6fedf26e98', '/data/workspace/myshixun/.git/objects/52/5857ff7c8ddab765e0a0f92afd492d75d4ca7e', '/data/workspace/myshixun/.git/objects/52/62f352db21afad21f01b1b584fba14838b0a3e', '/data/workspace/myshixun/.git/objects/38/95d7d76be402b5cb91855d1eee8094a2e14fb1', '/data/workspace/myshixun/.git/objects/38/3ef585878149d77c5ceb8ed51213ec16a33d8b', '/data/workspace/myshixun/.git/objects/2d/d19cd00ca732a1fd0f93eff0c7c16f20239f9e', '/data/workspace/myshixun/.git/objects/2d/a2cf735d56c9accba4f0a4bafb54aa58d56ec9', '/data/workspace/myshixun/.git/objects/ce/d3ab75c4fc5dd4297b711751f066f33bd8e14b', '/data/workspace/myshixun/.git/objects/4c/55a839602dccfd100e071dbc6ada47df353840', '/data/workspace/myshixun/.git/objects/36/e9b16c5536c0582a4ec03aaf8f1a9a22e1f6cf', '/data/workspace/myshixun/.git/objects/ae/b245eda3dea1c18a4f9b74491291e97ccfc587', '/data/workspace/myshixun/.git/objects/66/324ca29891377d74a8c4c384148c43731a2b87', '/data/workspace/myshixun/.git/objects/ec/dd57b4dc19d12a5c52d2913344a9d7e64f6908', '/data/workspace/myshixun/.git/objects/97/342280239a8b0f06ec6416ddc28e6701f38980', '/data/workspace/myshixun/.git/objects/e9/64bcdd9d004a75a93464c3efbd59e5d6c08e18', '/data/workspace/myshixun/.git/objects/03/8fb70d2754dcbc85bccfb7b604b4f6a6c687eb', '/data/workspace/myshixun/.git/objects/51/fcf0023c55a3a47a505f8fd2e8a9a69a782d41', '/data/workspace/myshixun/.git/objects/bb/b3239aa810c18252d10dfaa724a26ce9aa4239', '/data/workspace/myshixun/.git/objects/9f/77e890db7929da26a987ae36fdb89f5ddd3d60', '/data/workspace/myshixun/.git/objects/80/8374794e4fd246ad624f6696481db9bad18e95', '/data/workspace/myshixun/.git/objects/fc/aa9204d258585921f91cbf295e02df69cdc898', '/data/workspace/myshixun/.git/objects/2f/846f5874eaf9bbc2b9441adc8c7ff790e9778c', '/data/workspace/myshixun/.git/objects/f8/70144f7243b3c4c7c147248a9598324dd1090f', '/data/workspace/myshixun/.git/objects/f4/cb8e7e84d7eab8288ee4b46a2248972ff3dd0e', '/data/workspace/myshixun/.git/objects/47/3a926527633b742a5a8277225a38ddddbe5456', '/data/workspace/myshixun/.git/objects/e2/3f41176f20e442ee5459bde2f199d4b6925cd8', '/data/workspace/myshixun/.git/objects/e2/deb2d019c690026b46252afed048a66c94ae7b', '/data/workspace/myshixun/.git/objects/e2/261cf52d1f7202d09bb5468783b9b6c7136afc', '/data/workspace/myshixun/.git/objects/71/9fdf10c8a79e565a499c2ad4c43c79c6a734da', '/data/workspace/myshixun/.git/objects/2e/e51ac81ec6033cfe3283f8411e743be6efedf2', '/data/workspace/myshixun/.git/objects/2e/27fb9996c572b77e8505682b85221f07f29cb7', '/data/workspace/myshixun/.git/objects/1f/cb50844081b36bf48ab0199e5c3183a59f1a43', '/data/workspace/myshixun/.git/objects/1f/fdc9b40c411202cc6b8d14267584e699763537', '/data/workspace/myshixun/.git/objects/84/92c88bd5885027c9dbfdfd89d2555b5f6bca4c', '/data/workspace/myshixun/.git/objects/10/d4d94651faf4fa4a4b727691ea6be09144acd4', '/data/workspace/myshixun/.git/objects/0c/dc6e4bb21832b35506189153e107b353aaabec', '/data/workspace/myshixun/.git/objects/3e/286d07221b3fe4b34fa56eb70345491a102148', '/data/workspace/myshixun/.git/objects/99/b0b866dd3921827af9102e6e01e86b51b61949', '/data/workspace/myshixun/.git/objects/bf/7bd6c99448e41374cb08aa353a93203b904ed8', '/data/workspace/myshixun/.git/objects/01/d285a6886ca03dff85a0b60a3b1b2b832791dc', '/data/workspace/myshixun/.git/objects/01/0393426bf464694d01d865c59c02ee15d4fa9f', '/data/workspace/myshixun/.git/objects/cc/c6b5c362a65541daf674a9b1decfe90d1eeab0', '/data/workspace/myshixun/.git/objects/5c/0a1b6090a996e33bdcc497c44fb0a39cd92be9', '/data/workspace/myshixun/.git/objects/b7/6408d467d5efa63c4272ea7cf3c57e78d5245d', '/data/workspace/myshixun/.git/objects/4a/28b7ae22db561e6f94f3f00a2bf983a9522af3', '/data/workspace/myshixun/.git/objects/88/d8ff20ba3aa74d99a417c7888a8dec953a8dda', '/data/workspace/myshixun/.git/objects/ca/8b9f6e2a942acf27c9f0854318ceee0aa836ed', '/data/workspace/myshixun/.git/objects/39/c801386b3ac0effbaf1e84be84010eed594066', '/data/workspace/myshixun/.git/objects/85/9680a20c27ce0b01f13bd276a8a19c59927c5a', '/data/workspace/myshixun/.git/objects/1e/cb2ab58d332e2993b111425ebd4b54462ce5ea', '/data/workspace/myshixun/.git/objects/1e/283ddc167efb51f0f30b701c51ef7763648c2c', '/data/workspace/myshixun/.git/objects/07/06dbdf71c4720891904f0c86a218ccf6c52e06', '/data/workspace/myshixun/.git/objects/27/1479a4e50d12fa63a54cb59a3651a15e653748', '/data/workspace/myshixun/.git/objects/be/f99d717327b33ec795a5d155146b5371c5bd61', '/data/workspace/myshixun/.git/objects/be/465d874c1baf215aa82860e67bb34bfef682ba', '/data/workspace/myshixun/.git/objects/db/9ae1e214cd5800bb712db83477fc7d951b7a00', '/data/workspace/myshixun/.git/objects/e1/153efbf341b067fca9201fbc63d58b30233817', '/data/workspace/myshixun/.git/objects/1b/a0cf95196e37d6d6993822d6abaf9a75fc9dec', '/data/workspace/myshixun/.git/objects/a2/e7cef3a89355a7ba46efbf7a916c164d68d1b5', '/data/workspace/myshixun/.git/objects/a3/63a057ba4d12552fc9fceb41d30af8caa219ea', '/data/workspace/myshixun/.git/objects/f5/9de5d7c84f82cbe44efaf0c377d0cdb560a638', '/data/workspace/myshixun/.git/objects/0d/3e9d1e69b9aef7467a1f4b60e0c10559118b19', '/data/workspace/myshixun/.git/objects/04/d5308661d7365a10dd73763d574541d243fee3', '/data/workspace/myshixun/.git/objects/2a/51a3846c47cab797a8823af76bde5a918899b9', '/data/workspace/myshixun/.git/objects/15/af9f1404b86f6263b03f44085c9321c02be1a3', '/data/workspace/myshixun/.git/objects/ed/4f86b5b638deb98275ec5b890f60d9cdf7f0a3', '/data/workspace/myshixun/.git/objects/fb/adb7e7883639a24672217a7c0e8c3acfdc8579', '/data/workspace/myshixun/.git/objects/48/26aa1154754cfdb6f72aa706d81db2e630fe55', '/data/workspace/myshixun/.git/objects/c0/58c298be6886bb8a97f707c5d2961c82189066', '/data/workspace/myshixun/.git/objects/82/dbdb443b377efa5be7793a0bafd7c0986b4214', '/data/workspace/myshixun/.git/objects/f3/cd24282361e8822224430829112c5860dad2d4', '/data/workspace/myshixun/.git/objects/62/b2f8c0e980a237c8472e33a06d59881c6d7403', '/data/workspace/myshixun/.git/objects/4b/7fee1472b31eccce2f8e364b569b64a0696570', '/data/workspace/myshixun/.git/objects/7b/a4c88c179115392c96169080347ed6f3ac8ef1', '/data/workspace/myshixun/.git/objects/3d/6bd44242ee83880acc0fa39ba38664240c2993', '/data/workspace/myshixun/.git/objects/f9/57375df756f5a16fe4d16cfdf2904dfe9103ba', '/data/workspace/myshixun/.git/objects/a4/e9d1cec180fc1f4a639215b06cdfa9dd92e445', '/data/workspace/myshixun/.git/objects/da/63ed0eab22386d843f7366bd305e1664c11f72', '/data/workspace/myshixun/.git/objects/65/47fabf72a037f88f46f0e248c77cd89de47e01', '/data/workspace/myshixun/.git/objects/0f/94f1e84b57fcd64cb9e3906144fb22b9518c63', '/data/workspace/myshixun/.git/logs/HEAD', '/data/workspace/myshixun/.git/logs/refs/remotes/origin/HEAD', '/data/workspace/myshixun/.git/logs/refs/remotes/origin/master', '/data/workspace/myshixun/.git/logs/refs/heads/master', '/data/workspace/myshixun/__pycache__/step1_stud.cpython-36.pyc', '/data/workspace/myshixun/__pycache__/step2_stud.cpython-36.pyc']

```

不过不用头大，细细一看，大部分都是无关紧要的配置文件，始终记得，我们关心的是文件夹下的相关png或jpg文件，以及相关的判题文件，浏览文件列表，发现最相关的就是```/data/workspace/myshixun/step2_1_t.png```之类的文件以及```step2.py```之类的文件，尝试读取文件内容  

```python
import os
with open('/data/workspace/myshixun/step1.py','r',encoding = 'utf-8') as f:
	print(f.read())
```

读取后是本关代码+判题代码，判题代码如下：  

 ```python

def get_md5(file_name):
    m1 = hashlib.md5()   
    f1 = open(file_name, 'rb')
    m1.update(f1.read())   
    f1.close()
    return m1.hexdigest()
    

def check(file1, file2):
    code1 = get_md5(file1)
    code2 = get_md5(file2)
    print('结果正确') if code1==code2 else print('结果错误')


pathd_s='2007股市.xlsx'
path_s='step2_1.png'
step2_stud.DrawLine(pathd_s, path_s)
path_t='step2_1_t.png'
DrawLine_t(pathd_s,path_t)
check(path_s, path_t)


pathd_s='国民生产总值.csv'
path_s='step2_2.png'
step2_stud.DrawBar(pathd_s,path_s)
path_t='step2_2_t.png'
DrawBar_t(pathd_s,path_t)
check(path_s, path_t)

```

仍然是MD5判题，观察代码中的check()函数，接受file1，file2两个参数，在实际代码中,分别为```path_s path_t```，直接从变量名就可以猜测出来_s结尾的是学生文件, _t结尾的是标答文件，并且注意，复制的时候，要把文件名```step2_2_t.png```变成```step2_2.png```回到之前获得的文件路径列表，发现这两文件竟然都保存在统一文件夹下(也太偷懒了吧),直接搬出复制大法，最终代码如下：

```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib
import pandas as pd
matplotlib.rcParams['font.family']='simHei'  #显示汉字
plt.rcParams['axes.unicode_minus'] = False  #显示负号
def DrawLine(input_name, output_name):
	pass
#定义源文件名称和目标文件名
src1 = 'step2_1_t.png'
src2 = 'step2_2_t.png'
des1 = 'step2_1.png'
des2 = 'step2_2.png'
#定义源文件和目标文件夹，这里特殊的一点是，二者放在同一文件夹下，所以直接获得当前文件夹路径
file1 = file2 = os.getcwd()
# 确保目标文件夹存在
src_file1 = os.path.join(file1, src1)  # 完整的源文件路径1
dst_file1 = os.path.join(file2, des1)  # 完整的目标文件路径1
src_file2 = os.path.join(file1, src2)  # 完整的源文件路径2
dst_file2 = os.path.join(file2, des2)  # 完整的目标文件路径2
# 复制文件
shutil.copy(src_file1, dst_file1)
shutil.copy(src_file2, dst_file2)
```

---
## 总结
要通过此法实现通关，我们必须关注

 1. 判题代码中的文件路径和名称
 2. 标答文件和我们的目标文件夹的路径

此外，对于不能直接看当前文件夹的情况，我们可以通过导入os模块来实现强制获取文件路径并采用相关文件操作读取判题代码。

上面所说的一些方法，对于不同的题目，可能需要进行不同的调整，但大体上的思路是相似的，希望大家自己动手实操一遍，毕竟**纸上得来终觉浅，绝知此事要躬行**。

## 写在最后

本文只是提供了一种帮助大家规避这种严苛而无太大意义的测评的方式，毕竟我们不可能要求每个人的绘图都趋向某种**标准化**。况且，我们之所以用python，很大程度上是因为其丰富而强大的第三方库。matplotlib固然经典，然而seaborn也未尝不想,GitHub上琳琅满目的第三方库更是百花齐放，我们或许可以让视野变得更开阔一些。

---
