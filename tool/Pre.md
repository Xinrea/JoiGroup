# 预处理工具

## FFmpeg

官网：[http://ffmpeg.org](http://ffmpeg.org)

### 下载地址

<a href="../assets/ffmpeg-4.2.2-win64-static.zip" target="_blank">Windows64位</a>

<a href="../assets/ffmpeg-4.2.2-macos64-static.zip" target="_blank">MacOS64位</a>

### 安装方法

解压文件至某目录，然后将 bin 文件夹路径添加到 Path 环境变量，便于在控制台 cmd 中使用. 若不添加到环境变量，也可将 bin 目录下的`ffmpeg.exe`拷贝到所需要的目录进行使用

添加目录至 Path 环境变量，可参考：[百度知道的提问和回答](https://zhidao.baidu.com/question/204690598371989925.html)


### 常见的使用方式

更详细的说明，请看[工具-压制工具](Encode.md)

**Adobe Premiere 等工具不支持导入 flv 文件进行剪辑，录播文件 flv 内音视频编码分别为 aac 和 h264，因此可以直接转换封装为 mp4 格式，便于导入 Pr 进行剪辑，转换命令如下所示**

```ffmpeg -i "输入文件.flv"   -vcodec copy -acodec copy -f  mp4 "输出文件.mp4"```

也可以简略为

```ffmpeg -i 输入文件.flv   -c:v copy -c:a copy 输出文件.mp4```

也可使用以下脚本进行一键转换

```
mkdir converted
for %%a in ("*.flv") do ffmpeg -i "%%a"   -vcodec copy -acodec copy -f  mp4 "converted\%%~na.mp4"
pause
```
使用方法：将上面的文本复制，新建 txt 文件粘贴到里面，保存，然后把文件后缀名更改为 .bat 后，将其拷贝到 flv 文件所在的目录. 确保 FFmpeg 已添加到环境变量或`ffmpeg.exe`文件已拷贝到同目录后，双击运行 bat 脚本，转换后的文件会放置在同目录的`converted`目录下