Title: Pelican 的进阶-添加主题，插件
Date: 2014-07-27 00:03    
Modified: 2014-07-27 00:03  
Category: Original
Tags: Pelican, Python
Authors: chenjia.me
Summary: 为自己的Pelican Blog添加主题和插件！

# 添加主题 #

## 下载主题 ##

在Blog目录下~打开GIT。
输入，

    git clone https://github.com/getpelican/pelican-themes.git
	cd pelican-themes

然后你就会发现多了一个pelican-themes的文件夹~里面就都是主题啦~

里面的每个文件夹都是一个主题，很多都是空的o(╯□╰)o

不过有内容的都是有截图的~然后自己选择一个比较喜欢的~

PO主选择的是`tuxlite_tbs`这个主题~

然后运行一下命令进行安装主题~

	pelican-themes -i tuxlite_tbs

然后修改文件`pelicanconf.py`在其中添加`THEME = 'bootstrap2'`然后重新生成Blog就好了~

新的主题怎么样呢~

## 修改主题 ##

我们可以看到主题有两个目录，一个是css和icon的~一个是全部的HTML，CSS按照自己喜欢的方式添加修改~
HTML就是这个BLOG的核心部分，基本上所有的代码都是根据这个生成的。

主页一般是base.html+index.html+....生成的

我们可以通过观察修改不同的代码，以及我们也可以自己添加函数，标准函数都是{{xxxx.xxxx}}这样的~只要把对应的东西放在对应的位置就好~

比如 让 网页的title显示的是文章的title而不是Blog的Title

修改`article.html`，添加第二行文字

    {% extends "base.html" %}
	{% block windowtitle %}{{ article.title }}{% endblock %}
	{% block content %}

修改主题后，我们要更新主题才会生效

    pelican-themes -U tuxlite_tbs

更新主题后继续重新生成Blog，是不是改变了！

# 添加插件 #
## GITHUB上的插件 ##
和主题一样，GIT CLONE。

    git clone git://github.com/getpelican/pelican-plugins.git

如何使用可以参考开头提到的Blog和插件自带的.md，这里不再赘述。
## 添加评论插件Disqus ##
去Disqus官网注册，然后申请一个，记住SITENAME，然后在`pelicanconf.py`的`DISQUS_SITENAME `这个属性后添加SITENAME,c重新编译即可~

## 添加Google Analytics ##
去Google Analytics申请账号，记下跟踪ID，在`pelicanconf.py`的`GOOGLE_ANALYTICS `添加ID。
注意，现在Google已经修改代码，很多主题的分析代码都是旧版本的，我们先完整的复制Google给的js代码，然后打开主题的 `analytics.html`,删除原来的Google Analytics的代码，替换成新的代码，然后更新主题~搞定~