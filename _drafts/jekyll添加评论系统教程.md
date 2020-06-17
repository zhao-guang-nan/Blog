# jekyll添加评论系统教程.md

1. 首先登录GitHub账号，然后然后进入设置部分

   ![gitalk_1](E:\CODE\my_github_page\zhao-guang-nan.github.io\img\blog_Tutorial\gitalk_1.png)

2. 进入开发者设置选项

   ![](E:\CODE\my_github_page\zhao-guang-nan.github.io\img\blog_Tutorial\gitalk_2.png)

3. 进入QAuth Apps的选项

   ![](E:\CODE\my_github_page\zhao-guang-nan.github.io\img\blog_Tutorial\gitalk_3.png)

4. 然后创建一个QAuth APP的选项

   ![](E:\CODE\my_github_page\zhao-guang-nan.github.io\img\blog_Tutorial\gitalk_4.png)

5. 最后把下面的信息填完，其中拿到**Client ID**、**Client Sectet**的序列号，然后其中**Application name**填写创库名称，**Homepage URL**填写的是GitHub pages的连接地址。**Authorization callback URL**填写的是与**Application name**一样的。

   ![](E:\CODE\my_github_page\zhao-guang-nan.github.io\img\blog_Tutorial\gitalk_5.png)

6. 最后在工程的目录下的**_config.yml**的配置文件中添加以下的解析字段

   > ```js
   > gitalk:
   >   enable: true    #是否开启Gitalk评论
   >   clientID: 40e8d2bce8d798da9488                            #生成的clientID
   >   clientSecret: 56bebdf7d971d28d7faa30d1fd3462d0da8cedb3    #生成的clientSecret
   >   repo: zhao-guang-nan.github.io    #仓库名称
   >   owner: zhao-guang-nan    #github用户名
   >   admin: zhao-guang-nan	   #github用户名
   >   distractionFreeMode: true #是否启用类似FB的阴影遮罩
   > ```
   

   
7. 最后在界面刷新，就可以看到以下现象。

   ![](E:\CODE\my_github_page\zhao-guang-nan.github.io\img\blog_Tutorial\gitalk_6.png)

   







