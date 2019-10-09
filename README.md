在互联网，图片的大小对一个网站的响应速度有着明显的影响，因此在提供用户预览的时候，图片往往是使用压缩后的。如果一个网站图片较多，一张张压缩显然很浪费时间。那么接下来，我就跟大家分享一个批量压缩图片的方法，只需几行python代码，即可轻松实现图片压缩。

# 压缩算法
用到PIL库，PIL是Python平台事实上的图像处理标准库，支持多种格式，并提供强大的图形与图像处理功能。使用如下命令安装：
```
pip install pillow
```
代码如下：
```
#图片压缩批处理
def compressImage(srcPath,dstPath):
    for filename in os.listdir(srcPath):
        #如果不存在目的目录则创建一个，保持层级结构
        if not os.path.exists(dstPath):
                os.makedirs(dstPath)
 
        #拼接完整的文件或文件夹路径
        srcFile=os.path.join(srcPath,filename)
        dstFile=os.path.join(dstPath,filename)
 
        # 如果是文件就处理
        if os.path.isfile(srcFile):
            try:
                #打开原图片缩小后保存，可以用if srcFile.endswith(".jpg")或者split，splitext等函数等针对特定文件压缩
                sImg=Image.open(srcFile)
                w,h=sImg.size
                dImg=sImg.resize((int(w/2),int(h/2)),Image.ANTIALIAS)  #设置压缩尺寸和选项，注意尺寸要用括号
                dImg.save(dstFile) #也可以用srcFile原路径保存,或者更改后缀保存，save这个函数后面可以加压缩编码选项JPEG之类的
                print (dstFile+" 成功！")
            except Exception:
                print(dstFile+"失败！")
 
        # 如果是文件夹就递归
        if os.path.isdir(srcFile):
            compressImage(srcFile, dstFile)
```

# 方法调用
1.在该python文件所在目录新建三个目录，prepare(存放待压缩图片)、finish(压缩完成的原图)和compress(存放压缩后的图片)
2.遍历prepare目录，将图片移动到finish目录
3.遍历compress目录删除压缩图片
4.将finish中的图片全部进行压缩，对应存到compress目录

```
if __name__ == '__main__':
    # 遍历待加入图片
    path = os.walk("./prepare")
    for root, dirs, files in path:
        for f in files: 
            shutil.move(os.path.join(root,f),os.path.join('./finish',f))                #移动文件

    # 遍历删除压缩图片
    path = os.walk("./compress")
    for root, dirs, files in path:
        for f in files:
            os.remove(os.path.join(root, f))

    # 压缩图片
    compressImage("./finish","./compress")
```
# 完整代码
新建`photo.py`,复制如下代码

```
from PIL import Image
import os,shutil

#图片压缩批处理
def compressImage(srcPath,dstPath):
    for filename in os.listdir(srcPath):
        #如果不存在目的目录则创建一个，保持层级结构
        if not os.path.exists(dstPath):
                os.makedirs(dstPath)
 
        #拼接完整的文件或文件夹路径
        srcFile=os.path.join(srcPath,filename)
        dstFile=os.path.join(dstPath,filename)
 
        # 如果是文件就处理
        if os.path.isfile(srcFile):
            try:
                #打开原图片缩小后保存，可以用if srcFile.endswith(".jpg")或者split，splitext等函数等针对特定文件压缩
                sImg=Image.open(srcFile)
                w,h=sImg.size
                dImg=sImg.resize((int(w/2),int(h/2)),Image.ANTIALIAS)  #设置压缩尺寸和选项，注意尺寸要用括号
                dImg.save(dstFile) #也可以用srcFile原路径保存,或者更改后缀保存，save这个函数后面可以加压缩编码选项JPEG之类的
                print (dstFile+" 成功！")
            except Exception:
                print(dstFile+"失败！")
 
        # 如果是文件夹就递归
        if os.path.isdir(srcFile):
            compressImage(srcFile, dstFile)

if __name__ == '__main__':
    # 遍历待加入图片
    path = os.walk("./prepare")
    for root, dirs, files in path:
        for f in files: 
            shutil.move(os.path.join(root,f),os.path.join('./finish',f))                #移动文件

    # 遍历删除压缩图片
    path = os.walk("./compress")
    for root, dirs, files in path:
        for f in files:
            os.remove(os.path.join(root, f))

    # 遍历压缩图片
    compressImage("./finish","./compress")
```
最后，大家只要把要压缩的图片放入prepare目录，然后执行`photo.py`即可。

[源代码](https://github.com/codernice/photo_compress.git)