Title: 列出PHP文件夹下的所有文件
Date: 2014-10-07 19:48  
Modified: 2014-10-07 19:48  
Category: Original
Tags: October, PHP
Authors: chenjia.me

# 目的 #
实现和FTP服务器一下一个简单的文件列表，便于我们查看服务器的文件。

也是开源必备的文件

# 实现 #
一个index.php，可以实现向上级，下级跳转，列出当前目录文件，文件。并可以点击。

# 实例 #
![结果图片](https://i.imgur.com/xDdoCuQ.png)

# 代码 #
	:::php
	<html>
	<head><title>Index of <?php echo dirname(__FILE__);  ?>/</title></head>
	<body bgcolor="white">
	<h1>Index of <?php echo dirname(__FILE__);  ?>/</h1><hr><pre><a href="../">../</a>
	<?php        
	function getFileList3($directory) {        
    $files = array();        
    try {        
        $dir = new DirectoryIterator($directory);        
    } catch (Exception $e) {        
        throw new Exception($directory . ' is not readable');        
    }        
    foreach($dir as $file) {        
        if($file->isDot()) continue;  
        if($file->isDir()) {
            $files[] = $file->getFileName()."/"; 
        } 
        else {
           $files[] = $file->getFileName();     
        }      
    }        
    return $files;        
	}             

	$dir = dirname(__FILE__);   
	$fileslist=getFileList3($dir);     
	
	for($x=0;$x<count($fileslist);$x++) {
       echo "<a href=".$fileslist[$x].">".$fileslist[$x]."</a>";
       echo "<br>";
	}   
	?>  

	</pre><hr></body>
	</html>

