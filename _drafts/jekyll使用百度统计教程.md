# jekyll使用百度统计教程.md

1、首先到下面这个网站注册并且登录百度统计账号

https://tongji.baidu.com/web/welcome/login?castk=LTE%3D

2、登录之后按照这里填写域名与网址什么的

![](E:\CODE\my_github_page\zhao-guang-nan.github.io\img\blog_Tutorial\baidu_1.png)

3、最后在工程的目录下的**_config.yml**的配置文件中添加以下的解析字段

ba_track_id: 5e6a2dbb34e5706394da9fda0fe7abf6

这个序列号是百度统计网址生成的

可以在新统计网站中获取


var _hmt = _hmt || [];
(function() {
  var hm = document.createElement("script");
  hm.src = "https://hm.baidu.com/hm.js?5e6a2dbb34e5706394da9fda0fe7abf6";
  var s = document.getElementsByTagName("script")[0]; 
  s.parentNode.insertBefore(hm, s);
})();