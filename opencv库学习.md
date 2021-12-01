# 基本函数

## 1、方便开发的函数

### 1）设置滑动条

```c++
namedWindow("二值化图像", 1);
createTrackbar("i值,下阈值", "二值化图像", &i, 255);
setTrackbarPos("i值,下阈值", "二值化图像", i);
```

```python
cv2.nameWindow('name',mat)
cv2.createTrackbar(trackbarName,windowName,value,count,onChange)
# trackbarName: 滑动条的名字
# windowName: 所在窗口名称
# value: 滑块起始值
# count: 滑块最大值
# onChange: 回调函数，即当滑块位置改变的回调函数，第一个参数就是滑块的位置对应的值
cv2.getTrackbarPos(trackbarname, winname) # 获取窗口winname中滑块trackbarname的值
```

```python
# python举例
###############################################################
# 1、不使用回调函数
import cv2
import numpy as np
#创建一个（500，500）的numpy数组
img=np.zeros((500,500),dtype='uint8')
#新建一个窗口
cv2.namedWindow('img')
#定义回调函数，这里pass表示不做任何事，用作占位语句保持程序结构的完整性
def nothing(x):
    pass
#新建一个滑动条
cv2.createTrackbar('intensity','img',0,255,nothing)
while(1):
    cv2.imshow('img',img)
    #返回滑块所在位置对应的值
    intensity=cv2.getTrackbarPos('intensity','img')
    img[:]=[intensity]
    if cv2.waitKey(1)==ord('q'):
        break
cv2.destroyAllWindows()
###########################################################
# 2、使用回调函数
import cv2
import numpy as np
#创建一个（500，500）的numpy数组
img=np.zeros((500,500),dtype='uint8')
#新建一个窗口
cv2.namedWindow('img')
#定义回调函数，参数x为函数cv2.createTrackbar()传递的滑块位置对应的值
def img_intensity_change(x):
    img[:]=[x]
#新建一个滑动条
cv2.createTrackbar('intensity','img',0,255,img_intensity_change)
while(1):
    cv2.imshow('img',img)
    if cv2.waitKey(1)==ord('q'):
        break
cv2.destroyAllWindows()
```



### 2）设置鼠标事件

```c++
setMouseCallback("二值化图像", mouseCallBack);
void mouseCallBack(int event, int x, int y, int flags, void* param)；
/*第二个是回调函数，必须这样写的，然后判断鼠标事件的就是event和flags，具体请Google*/
```

### 3）图像处理

```c++
cvtColor(源图像, 目标图像, COLOR_BGR2GRAY); //灰度化图像
threshold(源图像,目标图像, i, j,THRESH_BINARY); //二值化
erode(源图像, 目标图像, kImage); //腐蚀图像
dilate(源图像, 目标图像, kImage);//膨胀图像
//kImage是一个算子
```

```python
grayFrame = cv2.cvtColor(frame,cv2.COLOR_BGR2GRAY) #灰度化图片
ret2,thresholdFrame = cv2.threshold(grayFrame,127,255,cv2.THRESH_BINARY) # 二值化
```



# 4) 图像进行透视

![image-20210125190528312](https://gitee.com/lmz2498369702/image-repository/raw/master/202112010848161.png)

```c++
/*
 * 首先是需要得到一个matrix转换矩阵
 * 通过getPerpectiveTransform(src,dst)得到一个转换矩阵
 * 然后可以运用这个转换矩阵进行图像透视
*/
```

# 5) 颜色检测

### 1、图像空间分为HSV HSL RGB图像空间

### 2、先转换为HSL HSV图像

```shell
Mat maskImage, imgHSV;
然后定义
Scalar lower( hmin, smin, vmin);
Scalar upper( hmax, smax, vmax);
之后调用函数
inRange(imgHSV,lower,upper,maskImage);
```

![image-20210126220658220](C:\Users\86186\AppData\Roaming\Typora\typora-user-images\image-20210126220658220.png)

# 6) 图像画图操作

### 1、写字

```c++
//写字
putText();
```

# 7) 寻找轮廓

```c++
vector<vector<Point>> contours; 
//保存着所有轮廓信息，一个轮廓由多个点构成

vector<Vec4i> hierarchy;  //不知道有什么用
Mat outImage;

findContours(outImage, contours, hierarchy, RETR_EXTERNAL, CHAIN_APPROX_NONE, Point()); //这是寻找轮廓，然后保存在contours中

//下面是遍历所有轮廓的
for(int i = 0 ; i < contours.size() ; i++){
    // contours[i] 这就是第i个轮廓包括的所有点
    
    //寻找轮廓最小矩形
    RotatedRect rect = minAreaRect(contours[i]); 
    //画圆
    circle(outImage,rect.center,1,Scalar(255,0,0));
    //由最小矩形生成矩形，虽然我还不太懂为什么是这样的
    Rect minrect = rect.boundingRect();
    //画矩形
    rectangle(outImage,minrect,Scalar(0,255,0),2);
}
```

```python
# 寻找轮廓
contours, hierarchy = cv2.findContours(binary,cv2.RETR_TREE,cv2.CHAIN_APPROX_SIMPLE)
# 绘制轮廓
cv2.drawContours(img,contours,0,(0,0,255),3)

# 说明
# findContours参数说明：
####binary  只能是二值图片，不能是灰度图，需要寻找轮廓的图片

####第二个参数 cv2.RETR_EXTERNAL表示只检测外轮廓
#   		 cv2.RETR_LIST检测的轮廓不建立等级关系
#            cv2.RETR_CCOMP建立两个等级的轮廓，上面的一层为外边界，里面的一层为内孔的边界信息。如果内孔内还有一个连通物体，这个物体的边界也在顶层。
#            cv2.RETR_TREE建立一个等级树结构的轮廓。

####第三个参数 cv2.CHAIN_APPROX_NONE存储所有的轮廓点，相邻的两个点的像素位置差不超过1，即max（abs（x1-x2），abs（y2-y1））==1
#            cv2.CHAIN_APPROX_SIMPLE压缩水平方向，垂直方向，对角线方向的元素，只保留该方向的终点坐标，例如一个矩形轮廓只需4个点来保存轮廓信息
#            cv2.CHAIN_APPROX_TC89_L1，CV_CHAIN_APPROX_TC89_KCOS使用teh-Chinl chain 近似算法

####第一个返回值contours 
#            cv2.findContours()函数首先返回一个list，list中每个元素都是图像中的一个轮廓，用numpy中的ndarray表示。这个概念非常重要。在下面drawContours中会看见。
print (type(contours))
print (type(contours[0]))
print (len(contours))

####第二个返回值hierarchy
#            此外，该函数还可返回一个可选的hiararchy结果，这是一个ndarray，其中的元素个数和轮廓个数相同，每个轮廓contours[i]对应4个hierarchy元素hierarchy[i][0] ~hierarchy[i][3]，分别表示后一个轮廓、前一个轮廓、父轮廓、内嵌轮廓的索引编号，如果没有对应项，则该值为负数。（看不懂，用不到）

```



# 8）滤波

# 9）形态学操作

### 1、腐蚀、膨胀。开，闭。顶帽，黑帽。形态学梯度

```c++
/*一般都是对二值化之后的图像或者灰度图才做*/
腐蚀：黑多白少 。 膨胀：白多黑少；
开操作：去小白，先腐蚀后膨胀。  闭操作：去小黑，先膨胀再腐蚀；
形态学梯度：膨胀的减去腐蚀的
顶帽：原图像与开操作之间的差值， 黑帽：原图像与闭操作之间的差值
```



### 2、函数

```c++
//1、先生成运算核心，可选参数MORPH_RECT,MORPH_CROSS,MORPH_ELLIPSE
//后面的m，n即生成m*n的核，可以生成m*1或者1*n来过滤竖线或者横线
Mat kenerl = getStructuringElement(MORPH_RECT, Size(m,n));

//操作通用函数
morphologyEx(binImage,outImage,mode,kenerl);
/* mode:
MORPH_ERODE    = 0, 腐蚀
MORPH_DILATE   = 1, 膨胀
MORPH_OPEN     = 2, 开
MORPH_CLOSE    = 3, 闭
MORPH_GRADIENT = 4,梯度
MORPH_TOPHAT   = 5,顶帽
MORPH_BLACKHAT = 6,黑帽
MORPH_HITMISS  = 7 一般用不到
*/
```

```python
# 生成运算核心，可选参数cv2.MORPH_RECT,...同上的c++
kernel = cv2.getStructuringElement(cv2.MORPH_RECT,(3, 3))

```



### 3、代码

```c++
Mat srcImage, grayImage, binaImage, outImage;
//灰度化操作
cvtColor(srcImage,grayImage,COLOR_BGR2GRAY);
//二值化操作，bianValue是二值化阈值的意思
threshold(grayImage, binaImage, bianValue,255,THRESH_BINARY); 
//生成运算核 kenerl，可以生成 n*1 或者 1*n 的核来对水平或者竖直方向滤波，
这里是m*n m=n
Mat kenerl = getStructuringElement(MORPH_RECT, Size(m,n));//可百度如何生成n*1的核
//形态学操作，mode代表是模式，有腐蚀，膨胀，开，闭，黑帽，顶帽等操作
morphologyEx(binImage,outImage,mode,kenerl);
```



# 10霍夫变化

##### 霍夫变换之前需要进行边缘检测Canny()

```C++
// 霍夫直线检测
CV::HoughLines(
    InputArray src, //只能8bit的灰度图像
    OutputArray lines, //输出的极坐标来表示直线
    double rho, //生成极坐标时候的像素扫描步长
    double theta, //生成极坐标时候的角度步长，一般取值CV_PI/180
    int threshold,  //阈值，只有获得足够交点的极坐标才被看成是直线
    double srn = 0, //是否应用多尺度的霍夫变换，如果不是设置0表示经典霍夫变换
    double stn = 0, //是否应用多尺度的霍夫变换，如果不是设置0表示经典霍夫变换
    double min_theta=0, //表示角度扫描范围0~180之间，默认即可
    double max_theta=CV_PI
);   //一般情况是有经验的开发者使用，需要自己反变换到平面空间，不建议使用
cv::HoughLinesP(
	InputArray src, //只能8bit的灰度图像
    OutputArray lines, //输出的极坐标来表示直线
    double rho, //生成极坐标时候的像素扫描步长，一般取1
    double theta, //生成极坐标时候的角度步长，一般取值CV_PI/180
    int threshold,  //阈值，只有获得足够交点的极坐标才被看成是直线
    double minLineLength=0, //最小直线长度
    double maxLineGap=0  //最大间隔
); //推荐使用
```

```c++
// 霍夫圆检测
HoughCircles(
    InputArray image, //输入图像，必须是8位的单通道图像
	OutputArray circles, //输出结果
    int method, //方法-HOUGH_GRADIENT
    double dp, //dp = 2
    double mindist, //10最短距离-可以分辨是两个圆的，否则认为是同心圆-src.arav.rows/8 
    double param1, //canny edge detection high threshold
    double param2, //中心点累加器阈值-候选圆心
    int minradius, //最小半径
    int maxradius //最大半径
);
```











