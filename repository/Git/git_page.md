**一、本文说明**<br>
本文是在已经使用gitpage搭建完成博客的基础上，并且已经安装好了git for windows等工具。 <br>
本文是在Windows环境下安装的示例，Linux和Mac可以坐参考<br>
**二、安装Jekyll本地编译环境**<br>
1.使用gitpage写博客的时候，每次都需要commit-->push--等待gitPage刷新页面。 <br>
效率很低，有没有可以在本地直接预览文章显示在页面上的方法呢？答案是有<br>
2.安装[ruby](http://rubyinstaller.org/downloads/)<br>
<img src="https://raw.githubusercontent.com/wangyuansxya/wangyuansxya.github.io/master/assets/ruby/rubyInstallers.png" style="zoom:50%" />
<br>
注意：这里一定要勾选添加到环境变量PATH！<br>
<img src="https://github.com/wangyuansxya/wangyuansxya.github.io/blob/master/assets/ruby/ruby.png?raw=true" style="zoom:50%" />
<br>
3.安装 [RubyGems](https://rubygems.org/pages/download) <br>
windows下下载ZIP格式较为方便，下好后解压到本地任意路径，下面以 {unzip-path} 代替你解压的路径。 <br>打开windows的cmd终端（按win+R快捷键打开“运行”，输入cmd，确定），输入命令:<br>
> $ cd {unzip-path}  <br>
> //如果你没有解压在C盘，windows的终端切换到其他盘需要写为 cd /d {unzip-path} <br>
> $ ruby setup.rb
> <br>
4.安装Jekyll <br>
在终端里输入<br>
> $ gem install jekyll
5.安装jekyll-paginate
在终端里输入
> $ gem install jekyll-paginate 
** 三、开启本地实时预览 **<br>
上一小节的安装都完成以后，在终端中输入命令
> $ cd {local repository} // {local repository}替换成你的本地仓库的目录
> $ jekyll serve

1.如遇到以下错误，说明网络不通：
> ERROR:  While executing gem ... (Gem::RemoteFetcher::FetchError)
> Errno::ECONNRESET: An existing connection was forcibly closed by the remote host.

这时候有三种解决方法：<br>

等天气好的时候再来<br>
架梯子<br>
[推荐] 更换为Ruby China源，点进去会有详细的设置方法。<br>
（感谢 @mortred 博友告知淘宝源已不可用，建议使用Ruby China的源，上不去的话换个时间就好。）<br>

1.如遇到以下错误，说明网络不通：
> jekyll 3.1.2 | Error:  Permission denied - bind(2) for 127.0.0.1:4000

说明有程序在占用这个本地端口，这时候输入命令
> $ netstat -ano

<img src="https://github.com/wangyuansxya/wangyuansxya.github.io/blob/master/assets/RubyGems/ip.png?raw=true" style="zoom:50%" />

可以看到如下进程与所占用端口的对应情况，找到本地地址为 127.0.0.1:4000 的记录，看到该条记录的PID为6668 (当然你的和我的不一样)。

输入命令
> $ tasklist /svc /FI "PID eq 6668"

该进程的名称就会显示出来:

<img src="https://github.com/wangyuansxya/wangyuansxya.github.io/blob/master/assets/RubyGems/pid.png?raw=true" style="zoom:50%" />


打开windows的任务管理器，结束它：

<img src="https://github.com/wangyuansxya/wangyuansxya.github.io/blob/master/assets/RubyGems/windows_tasks.png?raw=true" style="zoom:50%" />

再次运行 jekyll serve 就可以了。

如果一切顺利，通过在浏览器地址栏输入 http://localhost:4000/ 回车就已经可以看到自己网站的模样啦。
只要 jekyll serve 服务开着，你的本地仓库文件有任何更新，本地网站刷新都能马上看到，欧耶！

2.如果遇到以下错误：
> to install this gem. The full error message from Ruby is: 'cannot load such fil e -- tzinfo' If you run into trouble

则执行：

> $gem install tzinfo

3.如果遇到以下错误：

> No source of timezone data could be found. (TZInfo::DataSourceNotFound)

则执行：

> gem install tzinfo-data

即可解决


参考文章地址：http://playingfingers.com/2016/03/26/build-a-blog/