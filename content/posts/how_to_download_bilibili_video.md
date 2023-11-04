+++
title = '怎样下载 Bilibili 视频？'
date = 2023-11-04T14:25:58+08:00
draft = false
+++

# 第一步：安装 Bilibili Macos 客户端
在设置页面可以找到下载设置，缓存目录，设置好这个目录，并找到想要下载的视频，缓存下来。

这里需要说明的是，Bilibili 缓存好的视频是分成**音频**和**视频**两个 .m4s 格式的文件，所以我们需要将其整合成一个文件，并且最好转换成常用的 .mp4 格式。

# 第二步：安装 FFMPEG
Macos 系统可以使用 brew 安装 FFMPEG，命令：
```bash
brew install ffmpeg
```

# 第三步：准备转换脚本
将以下脚本内容保存成 .sh 格式文件, 例如 convert.shell

需要说明的是，Bilibili 缓存的音频和视频文件都在文件的开头插入了 “000000000” 这个 9 个 0，这会导致文件不可播放，所以我们需要将其去掉，脚本中使用了 sed 命令做了处理。

```shell
#!/bin/bash

# find the bilibili download video path, which can be found in bilibili settings.
# then copy this script into the path
# usage:
# ./merge_bilibili_video.sh v1.m4s v2.m4s output.mp4

# Before exec ffmpeg command, need to modify bilibili video file content:
# 1. open the video file like v1.m4s with nvim or some other editors
# 2. delete the "00000000" charactors at the beginning of the file
# 3. save the modified content
# we do it with the 3 lines code below
export LC_ALL='C' # in macos, need this line
sed '1s/^000000000//g' $1 >> temp1.m4s
sed '1s/^000000000//g' $2 >> temp2.m4s

# use brew to install ffmpeg, if you have not installed it yet, command: brew install ffmpeg
ffmpeg -y -i temp1.m4s -i temp2.m4s -codec copy $3

# delete the temp files
rm -f temp1.m4s
rm -f temp2.m4s
```

给脚本添加可执行权限，执行命令：
```bash
chmod +x ./convert.sh
```

# 第四步：执行转换脚本
将写好的脚本拷贝到缓存视频的目录，找到两个 .m4s 格式的文件分别做为输入的第一个和第二个参数，第三个参数是输出的文件名。
例如：
```bash
./convert.sh v1.m4s v2.m4s output.mp4
```
执行完毕就可以看到当前目录下生成了 ouput.mp4 文件就是转换好的视频文件。
