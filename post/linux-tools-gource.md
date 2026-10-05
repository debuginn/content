---
title: "Gource 版本可视化工具 使用手册"
date: 2022-03-16T19:31:40+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "git,gource"
comments: true
weight: 0
tags: ["git","gource"]
categories: ["git"]
image: "https://static.debuginn.com/202303161933689.jpg"
---

**Gource** 是一款版本控制可视化的工具，使用这个工具可以将自己的 Git 提交的代码包括对 **Mercurial**，**Bazaar** 和 **SVN** 的内置日志生成可视化支持。Gource 还可以解析由多个第三方工具为 CVS 存储库生成的日志。
提交的代码按照时间轴的顺序动态显示出来，可以使你的工作过程以动画的形式显现，并且 Gource 这个工具可以显示出来不同用户对一个代码库进行同一时间内的修改操作。

官方网站：[https://gource.io/](https://gource.io/)

## 常用命令

在这里我列举几个经常使用到的命令，PS：你需要先进入到对应项目目录中去，这个很重要，要不然会提示该目录下没有 log 记录。

```shell
gource                      # 使用Gource查看版本历史
gource -f -1280×720         # 设置分辨率大小
gource -s 0.5               # 每天以0.5秒的速度播放
gource -o 1.mp4             # 将版本动画导出到 1.mp4 文件中
gource -s 0.1 -o 2.mp4      # 每天以0.1秒的速度导出到 2.mp4 文件中
gource -f -b red            # 将背景设置为红色
gource --title “Gource”     # 为gource设置title
```

## 基本命令

```shell
➜  ~ gource -help
Gource v0.51
Usage: gource [options] [path]
用法: gource [选项] [路径]

Options:
  -h, --help                       帮助

  -WIDTHxHEIGHT, --viewport        设定窗口大小
  -f, --fullscreen                 全屏显示
      --screen SCREEN              画面编号
      --multi-sampling             启用多重采样
      --no-vsync                   禁用垂直同步

  --start-date 'YYYY-MM-DD hh:mm:ss +tz'  从日期和可选时间开始
  --stop-date  'YYYY-MM-DD hh:mm:ss +tz'  停在某个日期和可选时间

  -p, --start-position POSITION    从某个位置开始(0.0-1.0 or 'random')
      --stop-position  POSITION    停在某个位置
  -t, --stop-at-time SECONDS       在指定的秒数后停止

      --stop-at-end                在日志结尾处停止
      --dont-stop                  在日志结束后继续运行
      --loop                       在日志末尾循环

  -a, --auto-skip-seconds SECONDS  如果没有任何反应，则自动跳至下一个条目
                                   持续几秒钟(default: 3)
      --disable-auto-skip          禁用自动跳过
  -s, --seconds-per-day SECONDS    每天以秒为单位的速度(default: 10)
      --realtime                   实时播放速度
      --no-time-travel             如果提交时间是过去的时间请使用上一次提交的时间
  -c, --time-scale SCALE           更改模拟时间范围(default: 1.0)
  -e, --elasticity FLOAT           节点弹性(default: 0.0)

  --key                            显示文件扩展名

  --user-image-dir DIRECTORY       包含要用作头像的图像的目录
  --default-user-image IMAGE       默认用户图像文件
  --colour-images                  使用单色图像

  -i, --file-idle-time SECONDS     时间文件保持空闲(default: 0)

  --max-files NUMBER      最大文件数或0（无限制）
  --max-file-lag SECONDS  提交的最大时间文件可能会出现

  --log-command VCS       显示VCS日志命令(git,svn,hg,bzr,cvs2cl)
  --log-format  VCS       指定日志格式(git,svn,hg,bzr,cvs2cl,custom)

  --load-config CONF_FILE  加载配置文件
  --save-config CONF_FILE  使用当前选项保存配置文件

  -o, --output-ppm-stream FILE    将PPM流输出到文件 ('-' for STDOUT)
  -r, --output-framerate  FPS     输出帧率(25,30,60)

PATH可以是受支持的版本控制目录，日志文件，Gource配置文件或用于读取STDIN的'-'。
如果省略，则gource将尝试从当前目录生成日志。

要查看完整的命令行选项，请使用 “-H”
```
