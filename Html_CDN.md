Title: 使用公共CND进行加速
Date: 2014-10-07 20:17  
Modified: 2014-10-07 20:17  
Category: Tips
Tags: October, HTML
Authors: chenjia.me

# 问题 #
1. 现在大多数前端都用了jQuery等类库
2. google font被墙不能用
3. 网站3网访问速度不一样

# 解决 #

## 类库Cdn ##
个人觉得很全的~七牛CND 
[http://www.staticfile.org/](http://www.staticfile.org/)

360前端CDN
[http://libs.useso.com/](http://libs.useso.com/)

## 字体加速 ##
360字体镜像
[http://libs.useso.com/](http://libs.useso.com/)

使用:

1. 首先在程序源代码中找到调用Google免费字体库的地址，比如：

	    <link href='http://fonts.googleapis.com/css?family=Open+Sans:300,400,600&subset=latin,latin-ext' rel='stylesheet'>
    
 
2. 将Google免费字体库的域名 fonts.googleapis.com 修改为：fonts.useso.com 即可，如下所示：

		<link href='http://fonts.useso.com/css?family=Open+Sans:300,400,600&subset=latin,latin-ext' rel='stylesheet'>


简洁来说，就是讲`googleapis`替换为`useso`

替换后记得刷新~