# 预处理工具

## FFmpeg

官网：[http://ffmpeg.org](http://ffmpeg.org)

### 下载地址

<a href="../assets/ffmpeg-4.2.2-win64-static.zip" target="_blank">Windows64位</a>

<a href="../assets/ffmpeg-4.2.2-macos64-static.zip" target="_blank">MacOS64位</a>

### 安装方法

解压文件至某目录，然后将bin文件夹路径添加到Path环境变量，便于在控制台cmd中使用. 若不添加到环境变量，也可将bin目录下的`ffmpeg.exe`拷贝到所需要的目录进行使用。

?> 添加目录至Path环境变量的方法，可参考：[百度知道的提问和回答](https://zhidao.baidu.com/question/204690598371989925.html)。


### 常见的使用方式

更详细的说明，请看[工具-压制工具](Encode.md)。

#### 封装转换

Adobe Premiere等工具不支持导入flv文件进行剪辑，录播文件flv内音视频编码分别为aac和h264，因此可以直接转换封装为mp4格式，从而导入Pr进行剪辑，转换命令如下所示：

```bash
ffmpeg -i "输入文件.flv"   -vcodec copy -acodec copy -f  mp4 "输出文件.mp4"
```

也可以简略为：

```bash
ffmpeg -i 输入文件.flv   -c:v copy -c:a copy 输出文件.mp4
```

也可使用以下脚本进行一键转换：

```bash
mkdir converted
for %%a in ("*.flv") do ffmpeg -i "%%a"   -vcodec copy -acodec copy -f  mp4 "converted\%%~na.mp4"
pause
```
?> 使用方法：将上面的文本复制，新建 txt 文件粘贴到里面，保存，然后把文件后缀名更改为 .bat 后，将其拷贝到 flv 文件所在的目录. 确保 FFmpeg 已添加到环境变量或`ffmpeg.exe`文件已拷贝到同目录后，双击运行 bat 脚本，转换后的文件会放置在同目录的`converted`目录下。

#### 视频合并

在录屏过程中，如有网络波动，则可能导致录播文件分段；因此经常需要将多段视频合并为一段，用于剪辑或投稿。FFmpeg便可以简单快捷地解决这一问题。

!> 通过FFmpeg合并视频的方法有很多，但考虑到录播的实际情况，如：各片段显然编码一致、由于网络波动片段可能受损等，在此只介绍针对录播片段合并的通用解决方案。

```python
# Author: Xinrea

import sys
import subprocess

ffmpeg_exec = 'ffmpeg.exe'

def publish():
    print(sys.argv)
    tslist = ''
    if len(sys.argv) == 1:
        return
    filelist = sys.argv[1:]
    filelist.sort()
    for i in range(len(filelist)):
        source = filelist[i]
        ffargs = [
            ffmpeg_exec,
            '-i', source,
            '-c', 'copy',
            '-bsf:v', 'h264_mp4toannexb',
            '-y',
            '-f', 'mpegts',
            str(i)+'.ts']
        subprocess.run(ffargs)
        tslist = tslist + str(i) + '.ts'
        if i != len(filelist)-1:
            tslist = tslist + '|'
    ffargs_combine = [
        ffmpeg_exec,
        '-i', 'concat:'+tslist,
        '-c', 'copy',
        '-bsf:a', 'aac_adtstoasc',
        '-y',
        'out.mp4']
    subprocess.run(ffargs_combine)

if __name__ == "__main__":
    publish()
```

上述代码将输入的文件进行排序，然后将其分别转换为`TS`格式，最后将片段连接合成为一个完整的`mp4`文件。

?> 如果你不知道该如何使用上述Python代码，可以点击<a href="../assets/AllToOne.zip" target="_blank">这里</a>下载打包好的文件。下载完成后请解压，然后只需将你想合并的片段文件全部选中后，拖拽到`AllToOne.exe`程序上即可。请耐心等待合并完成，这一过程时间不会太久，最终会输出`out.mp4`文件作为合并结果。