# BIT_yanhe_download

## 介绍

本项目可下载[延河课堂 (yanhekt.cn)](https://www.yanhekt.cn/recordCourse)中的课程视频。延河课堂是北京理工大学的在线课堂，提供了大量的课程视频，但是没有提供下载功能。本项目可以下载指定课程的视频录像和屏幕信号，包括无权限的课程。

欢迎提出建议和star！

## 使用：下载指定课程

[点击此处下载](https://github.com/AuYang261/BIT_yanhe_download/releases/latest/download/release_downloader.zip)并解压。

在[延河课堂 (yanhekt.cn)](https://www.yanhekt.cn/recordCourse)中找到想下载的课程，以链接为https://www.yanhekt.cn/course/40524 的课程为例，复制地址栏最后的五位编号40524。注意是课程列表的链接（以`yanhekt.cn/course/五位编号`开头），不是视频界面的链接（以`yanhekt.cn/session/六位编号`开头）。

![image-20231018204208066](md/README/image-20231018204208066.png)

双击运行`main.exe`文件，并输入你想下载的课程编号(40524)。输出课程视频列表：

![image-20240409103306945](md/README/image-20240409103306945.png)

输入想下载的视频编号，用英文逗号(,)分隔，回车。接着输入数字选择下载video视频录像（即教室后的摄像头录像）还是下载screen信号（即教室电脑的屏幕），默认为视频录像。回车即开始下载：

![image-20240409103338980](md/README/image-20240409103338980.png)

下载完成的文件在`output/`目录下以`课程名-video/screen`格式命名的文件夹中。

![image-20230926124922726](md/README/image-20230926124922726.png)

## 自动生成字幕

本项目提供自动生成字幕功能，使用openai的[whisper](https://github.com/openai/whisper)项目及其模型在本地进行语音转文字生成字幕。

最好使用GPU运行，否则速度较慢，依赖见[下文](#依赖)。

下载[字幕生成程序gen_caption](https://github.com/AuYang261/BIT_yanhe_download/releases/latest)，由于程序比较大，采用了分卷压缩发布。全部下载并解压，得到一个`gen_caption.exe`可执行文件，保存在上述`release_downloader.zip`解压的目录中，和保存视频的目录`output/`同级，如下所示：

![image-20240409105228362](md/README/image-20240409105228362.png)

下载完视频后，双击运行`gen_caption.exe`（文件较大，需要等一会），输入数字选择视频，回车。再输入数字选择使用多大的模型，越往下效果越好，但所需时间也越长，默认使用base模型。第一次使用会自动下载模型（几百M），请耐心等待。如下所示：

![image-20240409131033038](md/README/image-20240409131033038.png)

等待程序运行完成，生成的字幕文件为`.srt`格式，与视频文件在同级目录下，用支持字幕的播放器（如potplayer）打开视频即可看到带字幕的视频。

*tips: 语音转文字所需的时间较长，可以先观看视频，字幕生成好了再重新打开视频享受字幕。使用GPU大约需要几分钟，不使用GPU则需要更长时间。*

## 依赖

* ffmpeg，已在Release中提供。若在Linux环境下运行，需手动安装ffmpeg：

```bash
sudo apt update
sudo apt install ffmpeg
```

* **若使用GPU运行自动生成字幕功能，需要先安装cuda，安装方法见[cuda安装](https://blog.csdn.net/chen565884393/article/details/127905428)。**

*若想用python环境运行，需安装以下依赖*

* python，[下载](https://www.python.org/ftp/python/3.9.4/python-3.9.4-amd64.exe)并安装

* python第三方库requests。打开命令行（按win+r，在打开的窗口中输入cmd，回车），运行如下命令安装：

```bash
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```

* 安装语音转文字的依赖：（依赖于pytorch，若未安装pytorch，会自动安装，但是cpu版本。安装cuda版本的pytorch方法见[pytorch官网](https://pytorch.org/get-started/locally/)。）

```bash
pip install -r requirements_whisper.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```

## 注意

* 需要关闭本机上的代理，否则会提示类似`check_hostname requires server_hostname`的报错信息。
* 可以下载无权限的课程，只要知道课程链接（中的课程编号）就行。

## 打包（仅开发者需要）

如果想要运行时不依赖python环境，可将python程序打包成可执行文件。Release中已打包。

使用如下命令打包：

```bash
# 若未安装pyinstaller，运行以下命令安装
pip install pyinstaller
# 打包
pyinstaller -F main.py
pyinstaller -F gen_caption.py
```
打包`gen_caption.py`时可能会失败，提示递归过深：

<img src="md/README/image-20240409095211597.png" alt="image-20240409095211597" style="zoom:50%;" />

解决方法参考[这里](https://zhuanlan.zhihu.com/p/661325305)，需要修改项目根目录下的`gen_caption.spec`配置文件，在文件开始处加上以下代码：

```python
import sys ; sys.setrecursionlimit(sys.getrecursionlimit() * 5)
```

再使用如下命令打包：

```bash
pyinstaller --clean .\gen_caption.spec
```

打包完成后运行若出现Temp目录下的文件未找到：

![image-20240409095831766](md/README/image-20240409095831766.png)

解决方法参考[这个](https://blog.csdn.net/qq_42324086/article/details/118280341)，将项目`hooks`目录下的`hook-whisper.py`和`hook-zhconv.py`文件复制到pyinstaller的hook目录下（通常在`python根目录\Lib\site-packages\PyInstaller\hooks`），。
