> 原项目从DavinciEvans/bilibili-subtitle-download-skill fork过来的。
我做了两点修改：
1. 原项目使用的python依赖bilibili-api已经不更新了，安装依赖的时候会报错。我改用了新的依赖bilibili-api-python。
2. 原项目仅下载普通字幕。现在bilibili很多字幕都是ai字幕，我更新了：如果有普通字幕优先下载普通字母。如果没有，就尝试下载ai字幕。
