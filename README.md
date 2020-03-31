### 笔记

1、本地离线打包
* 下载Hbuilder X正式版HTML5+SDK(https://ask.dcloud.net.cn/article/103)
* 解压SDK包，使用android studio打开解压包下的HBuilder-Hello
* 切换文件夹到app->src->main->assets->app.helloH5(或者类似这个名字)
* 将HBuilder X编辑器中项目进行发行->生成本地APP资源
* 将生成的APP本地资源文件夹整体copy替换第三步下的app.helloH5文件夹
* 修改dcloud_control.xml中的id值该值和manifest.json中置顶的APPID一致
* 修改AndroidManifest.xml中的package包名，一般同java package包名格式一致
* 修改build.gradle文件中，若存在applicationId，则该值与修改的包名package一致
* 最后就可以打包了，Build->Build Bundle(s)/APK(s)->Build APK(s)，即可在项目app->build->outputs->apk文件夹下找到生成的apk文件
* (ext: 修改app icon、开启页等可直接访问替换app->src->res->drawable-xxhdpi文件夹下的文件)