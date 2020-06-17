# jekyll本地调试的教程

## 输入命令行，然后使用浏览器进行调试

输入以下命令，其中**--host**后接你的调试IP地址，然后**--port**接你的调试端口号。

```shell
jekyll serve --host 127.0.0.1 --port 8080
```

## 没有发布的文章在**_drafts**下，下面的命令是本地调试未发布的文章

通过以下的命令可以看到还没有发布的文档

```shell
jekyll serve --host 127.0.0.1 --port 8080 --drafts
```

