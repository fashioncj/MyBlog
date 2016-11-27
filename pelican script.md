Title: Pelican 的轻松使用-写一个好脚本
Date: 2014-07-26 00:39  
Modified: 2014-07-27 00:54  
Category: Original
Tags: Pelican, Python
Authors: chenjia.me
Summary: 通过脚本快速发表文章，更新主题~

# 一键发表文章 #
在Blog目录下创建`Publish.bat`文件（右键新建文本文档，改文件名-。-）
然后按需要修改以下代码:

    pelican content -s publishconf.py
    cd output
    git add .
    git commit -m "commit bat"
    git push git@github.com:fashioncj/fashioncj.github.io.git
    cd ..

然后每次要发布的时候是要双击它就好啦~

# 一键更新主题 #
在pelican-theme目录下新建`update.bat`文件
然后写入如下代码

    pelican-themes -U 主题名

就ok啦~