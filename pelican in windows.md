Title: Pelican 的Windows 入门教程
Date: 2014-07-26 00:31  
Modified: 2014-7-25 16:59
Category: Original
Tags: Pelican, Python
Authors: chenjia.me
Summary: # 为什么选择TA #
1. 在windows下可以很方便的运行，不需要ruby，只要安装pytho	n，而且不需要知道python脚本怎么使用
2. 简洁，带有code 高亮，如果用rst写的话还支持更多代码...

# 为什么选择TA #
1. 在windows下可以很方便的运行，不需要ruby，只要安装pytho	n，而且不需要知道python脚本怎么使用
2. 简洁，带有code 高亮，如果用rst写的话还支持更多代码显示
3. 模块设计，支持bootstrap，可以自己写Theme和plugins
4. 看了这个文章，你只需要不到一个 小时就可以写出HelloMyBlog

> 感谢知乎上的题目给了我对jekyII，Octopress，Pelican的对比
> [http://www.zhihu.com/question/19996679](http://www.zhihu.com/question/19996679)

> 感谢以下两个Blog给我很多提示
> [http://www.lizherui.com/pages/2013/08/17/build_blog.html](http://www.lizherui.com/pages/2013/08/17/build_blog.html)
> [http://www.cnblogs.com/ballwql/p/pelican.html](http://www.cnblogs.com/ballwql/p/pelican.html)

# 让我们开始 #
官方文档：[http://pelican-cn.readthedocs.org/zh_CN/latest/quickstart.html](http://pelican-cn.readthedocs.org/zh_CN/latest/quickstart.html)
## 安装python ##
pelican对于python2和3 都兼容，我个人习惯是都安装，因为现在可以使用`py -2`或者`py -3`来选择是python2还是python3运行。

## 安装pelican ##
首先，我们要确定pip命令是否可用，在cmd中输入pip看是否识别，不能识别的请将python script的路径加入环境变量path中，不会请google it

现在我们开始安装pelican

首先是下载安装pelican

    pip install pelican markdown

等待完成就好~

## 部署新的pelican blog ##
找个新的文件夹~也就是你准备要存放Blog的文件夹~
然后用cmd运行到这个目录下~
本文使用的是newblog这个目录~

在cmd中输入

    pelican-quickstart

然后就开始配置，网站标题，作者，前缀，分页，页数都自己填，如果你购买了域名就在网站前缀直接输入你的域名~

一路回答后~就完成了~你会发现文件夹里面多了好多东西~

## 写一篇新文章 ##
首先下载MarkdownPad2~这个是在Windows编辑.md文件比较好的软件~
然后在MP中输入：

    Title: Pelican 的标准发表模板
    Date: 2014-7-25 16:59
    Modified: 2014-7-25 16:59
    Category: Pelican
    Tags: pelican, publishing, python
    Authors: chenjia.me
    Summary: Template
	
	<在这里填写文件内容>

然后保存到`content`这个文件夹下~

## 生成Blog ##
在根目录下，cmd中~输入

    pelican content

这样在output文件夹中就会有文件了~可以直接打开里面的index.html，看看是不是很难看？没事不要担心~我们慢慢搞定它~

## 将代码托管到GitHub/GitCafe ##
这两个都提供了个人page的功能~
Github在国外，GFW墙过，gitcafe是国内~比较稳定，不过貌似对某些地方的联通网络支持不好~


看到大家都喜欢用github~就介绍这个吧~
> 如果新注册的GitCafe的用户~可以添加我作为邀请用户~用户名fashioncj

在Github上注册好厚创建 用户名.github.io 的仓库，然后在右边点击设置，里面就有一个生成个人page的选择~然后你就填写资料~就好了~

ps.那个主题神马的都无所谓~因为我们是要用Pelican的！

然后配置你的SSH，在本地装好Git环境
> 如果不会git请百度~很快就会的~因为我们只要会clone,add，commit，push

然后到本机，cmd切换到output这个目录，使用`git clone`把仓库里面的代码复制下来，然后我们就完成了第一步。

接下来，修改文件`publishconf.py`中的

    DELETE_OUTPUT_DIRECTORY = False

防止我们每次生成Blog的时候清空.git目录

然后我们使用命令`pelican content -s publishconf.py`再次生成。

成功后，我们打开output目录，将代码push到远程仓库。
然后用浏览器访问：用户名.github.io 看看是不是Blog出现了~

## 绑定域名 ##
1. 在仓库中创建一个文件，文件名为CNAME,内容为 你的域名 
2. 修改你的域名dns解析商，推荐dnspod和dnsla
3. 使用dnspod或者dnsla，将你的域名添加两条A记录，指向的地址分别是
> 192.30.252.154
> 
> 192.30.252.153

然后过几分钟，打开你的域名~blog是不是出现了！


----------
接下来的一篇文章会介绍添加主题，插件，修改主题模板，脚本快速发表等内容

modified at 2014-07-26 01:15  
