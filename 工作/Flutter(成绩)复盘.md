#### Flutter遗留问题

* 无法抓包（解决）
* 图片资源找不到（目前采用临时copy方式）（解决）

#### 优化事项

* UI打磨（点击区域等）
* 将Flutter与原生交互功能开发成一个库（解决）
* 验证Flutter与原生复杂交互场景（引擎优化）
* 动态化研究



#### 图片资源找不到解决方法

1.将图片资源统一放到xhb_icons package下的lib目录下；

2.使用时加上package name， 例如 AssetImage("assets/images/screen_img.png", package: 'xhb_icons')；





日报

1.开发Native-Fluttter交互库：nf_bridge，并替换晓成绩中原有写法

2.协助督导部署闸机



#### 日常业务 28

* 日常业务 28
* 基础建设15
* 技术分享 8
* 技术文章 5
* 跨部门合作 10