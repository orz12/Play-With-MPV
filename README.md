# 使用 MPV 播放


适用于 windows10/11 Edge/Chrome

本项目包含以下功能：

1. 为支持的网页添加 MPV 播放按钮，点击即可打开 MPV 播放网页中的视频
```
支持的网站列表如下：
大部分支持（youtube需要安装youtube-dl或yt-dlp，推荐yt-dlp）
https://www.youtube.com/watch/*
https://www.bilibili.com/video/*
https://www.bilibili.com/bangumi/*
https://ddrk.me/*

部分支持
http://www.imomoe.live/player/*

小部分支持（很多.m3u8返回.jpg，mpv解析失败）
https://www.yhdmp.net/vp/*
```


## 内容列表

- [背景](#背景)
- [安装](#安装)
- [使用说明](#使用说明)
- [徽章](#徽章)
- [相关仓库](#相关仓库)
- [维护者](#维护者)
- [如何贡献](#如何贡献)
- [使用许可](#使用许可)

## 背景

最近在使用 MPV + Anime4K 播放动漫，效果显著，但是每次都需要下载播放比较麻烦，所以找了不少插件或脚本，但是不太适合自己，所以自己写了这个项目。

### 特别感谢
使用过的插件或脚本：
1. 谷歌插件：[ff2mpv](https://github.com/woodruffw/ff2mpv)
支持：
youtube，其他不清楚，应该是支持youtube-dl的网站都可以
> 安装过程很详细，要仔细安装，但是我只有youtube能用上QAQ

2. 油猴脚本：[play-with-mpv](https://greasyfork.org/zh-CN/scripts/416271-play-with-mpv)
支持：
clips.twitch.tv
www.bilibili.com/video
www.youtube.com
m.youtube.com
> 安装了没用上，主要不支持b站番剧/bangumi，难受QAQ

3. 油猴脚本：[Bilibili-Evolved](https://github.com/the1812/Bilibili-Evolved) + [playwithmpv](https://github.com/videoanywhere/playwithmpv)
> 一开始在用的版本，就是要点几次按钮，稍微有点麻烦，教程可以参看我的b站视频：
https://www.bilibili.com/video/BV1zY41137uX/

这几个项目大概思路
```
ff2mpv 和 play-with-mpv
1. 通过谷歌插件或油猴脚本获取当前网页链接，
2. 通过自定义URL Protocol 调用脚本（py，ps1等）解析参数
3. 通过脚本（py，ps1等）调用 MPV 播放

但是许多网址无法直接通过 MPV + yt-dlp 解析播放

Bilibili-Evolved + playwithmpv
1. 通过b站接口获取flv地址
2. 通过playwithmpv服务，传递flv地址给mpv进行播放

点击功能-选集-开始播放-播放，稍微有点麻烦QAQ，这个是写这个项目的原因之一
```


本项目思路结合以上方式，以及通过抓取视频地址，添加了一些自己常用的网址
```
1. 通过油猴脚本抓取网页视频mp4，m3u8，flv等真实地址，其中：
a. youtube 直接获取当前网页地址，通过yt-dlp解析
b. bilibili 通过b站api获取视频flv地址
c. ddrk.me，immoe.live，yhdmp.net 抓取网页中的视频链接

2. 通过自定义 URL Protocol 调用 powershell 脚本解析参数

3. 通过 powershell 调用 MPV 进行播放
```

## 安装

这个项目使用 [mpv](https://github.com/mpv-player/mpv) 或 [mpv-lazy](https://github.com/hooke007/MPV_lazy)。请确保你本地安装了它们。  
其中 [mpv](https://github.com/mpv-player/mpv) 需要单独安装 [yt-dlp](https://github.com/yt-dlp/yt-dlp) 以支持 youtube

推荐使用：[mpv-lazy](https://github.com/hooke007/MPV_lazy) 集成图形化OSD，多种着色器，插件以及常用配置  
推荐插件：[Glsl_Running_Mode_Cache](https://github.com/LuckyPuppy514/MPV_Glsl_Running_Mode_Cache) 集成 [Anime4K](https://github.com/bloc97/Anime4K) 着色器，可自动缓存上次使用的着色器配置，下次播放自动打开

1. 浏览器安装油猴插件  
[Microsoft Edge](https://microsoftedge.microsoft.com/addons/detail/tampermonkey/iikmkjmpaadaobahmlepeloendndfphd)
[Google Chrome](https://chrome.google.com/extensions/detail/dhdgffkkebhmkfjojejmpbldmpobfkfo)

2. 安装油猴脚本  
[Play-With-MPV](https://greasyfork.org/zh-CN/scripts/444056-play-with-mpv)

3. 下载 powershell 脚本  
[Play-With-MPV](https://github.com/LuckyPuppy514/Play-With-MPV/releases/tag/v1.0.0)

4. 执行 Play-With-MPV 中的 install.ps1 脚本，请注意：  
```
a. windows10/11默认禁止运行powershell脚本运行，解决办法：
以管理员身份打开 PowerShell 输入：
set-executionpolicy remotesigned
``` 

``` 
b. 建议把 Play-With-MPV 放在 MPV 安装目录下
然后右键使用 powershell 运行
注意：路径不能有中文，C盘建议放在C:\Users\用户名 子目录下，否则权限不足
``` 
``` 
脚本说明：
install.ps1
a. 为浏览器添加 URL Protocol 打开应用程序时，记住选项框，仅支持 https
b. 添加自定义 URL Protocol 协议：PlayWithMPV://，
使浏览器能够调用playwithmpv.ps1打开 MPV
c. 添加 MPV 环境变量

提示：该脚本主要是添加注册表信息，运行失败，
可使用 chromeEdgeOpenCheckbox.reg 和 playwithmpv.reg 直接添加，
注意：playwithmpv.reg中的playwithmpv.ps1路径，需要修改成你自己的路径；
MPV 环境变量也可自己手动添加：
windows图标=>右键=>系统=>高级设置=>环境变量=>path，添加 MPV 路径

playwithmpv.ps1
a. 接受浏览器参数，调用 MPV 播放
``` 

## 使用说明
打开支持网址，选择视频，点击左下角播放按钮，稍等片刻即可
附运行效果图：  
<img src="https://github.com/LuckyPuppy514/Play-With-MPV/blob/main/work_picture/work_on_youtube.jpg" />  
<img src="https://github.com/LuckyPuppy514/Play-With-MPV/blob/main/work_picture/work_on_bilibili_bangumi.jpg" />  
<img src="https://github.com/LuckyPuppy514/Play-With-MPV/blob/main/work_picture/work_on_bilibili_video.jpg" />  
<img src="https://www.lckp.top:8888/work_picture/work_on_bilibili_bangumi.jpg" />  
<img src="https://github.com/LuckyPuppy514/Play-With-MPV/blob/main/work_picture/work_on_ddrk.jpg" />  
<img src="https://github.com/LuckyPuppy514/Play-With-MPV/blob/main/work_picture/work_on_imomoe.jpg">  
<img src="https://github.com/LuckyPuppy514/Play-With-MPV/blob/main/work_picture/work_on_yhdmp.jpg">  


## 徽章

[![standard-readme compliant](https://img.shields.io/badge/readme%20style-standard-brightgreen.svg?style=flat-square)](https://github.com/RichardLitt/standard-readme)


## 相关仓库

- [mpv](https://github.com/mpv-player/mpv) — mpv播放器。
- [mpv-lazy](https://github.com/hooke007/MPV_lazy) — mpv播放器懒人版。
- [yt-dlp](https://github.com/yt-dlp/yt-dlp) — 视频下载。
- [ff2mpv](https://github.com/woodruffw/ff2mpv) — 谷歌 MPV 插件。
- [Bilibili-Evolved](https://github.com/the1812/Bilibili-Evolved) - B站增强脚本
- [playwithmpv](https://github.com/videoanywhere/playwithmpv) — B站增强脚本 MPV 播放插件
- [Anime4K](https://github.com/bloc97/Anime4K) — 动漫画质增强
- [Glsl_Running_Mode_Cache](https://github.com/LuckyPuppy514/MPV_Glsl_Running_Mode_Cache) — MPV 着色器运行模式缓存
## 维护者

[@LuckyPuppy514](https://github.com/LuckyPuppy514)

## 如何贡献

非常欢迎你的加入！[提一个 Issue](https://github.com/LuckyPuppy514/Play-With-MPV/issues/new) 或者提交一个 Pull Request。


## 使用许可

[MIT](https://github.com/LuckyPuppy514/Play-With-MPV/blob/main/LICENSE) © LuckyPuppy514