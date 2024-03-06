<!--
    需要填充的占位符：
    
    README.md
    
        牛客网教程合集：文档中文名
        {nameEn}：文档英文名
        {urlEn}：文档原始链接
        nwcd：域名前缀
        飞龙：负责人名称
        wizardforcel：负责人 Github 用户名
        562826179：负责人 QQ
        nowcoder-tut-zh：ApacheCN 的 Github 仓库名称
        nowcoder-tut-zh：DockerHub 仓库名称
        nowcoder-tut-zh：PYPI 包名称
        nowcoder-tut-zh：NPM 包名称
    
    CNAME
    
        nwcd：域名前缀

    index.html
    
        牛客网教程合集：文档中文名
        #009d9c：显示颜色
        nowcoder-tut-zh：ApacheCN 的 Github 仓库名称

    asset/docsify-flygon-footer.js
    
        nowcoder-tut-zh：ApacheCN 的 Github 仓库名称
-->

# 牛客网教程合集

> 协议：[CC BY-NC-SA 4.0](http://creativecommons.org/licenses/by-nc-sa/4.0/)
> 
> 真相一旦入眼，你就再也无法视而不见。——《黑客帝国》

* [在线阅读](https://nwcd.flygon.net)

## 下载

### Docker

```
docker pull apachecn0/nowcoder-tut-zh
docker run -tid -p <port>:80 apachecn0/nowcoder-tut-zh
# 访问 http://localhost:{port} 查看文档
```

### NPM

```
npm install -g nowcoder-tut-zh
nowcoder-tut-zh <port>
# 访问 http://localhost:{port} 查看文档
```
