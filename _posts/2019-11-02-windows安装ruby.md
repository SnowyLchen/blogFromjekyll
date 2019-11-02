---

layout:post

title:ruby和搭建博客

categories:other

description:ruby安装

keywords:ruby,jekyll,博客

---

# 搭建博客

​	参考<https://www.cnblogs.com/sqchen/p/10757927.html>

# ruby内容

​	更新ruby版本 gem update --system

​	安装jekyll gem install jekyll

​	清华大学源：<https://mirror.tuna.tsinghua.edu.cn/help/msys2/>

​	各种错误

<	https://www.jianshu.com/p/58e2c5ea3103>

<	https://www.bbsmax.com/A/1O5EpbXyJ7/>

​	启动Jekyll服务 bundle exec jekyll serve

​	启动Jekyll服务时，可能会遇到如下错误：

``` 
 		 Conversion error: Jekyll::Converters::Scss encountered an error
  		while converting 'css/main.scss':Invalid GBK character "\xE2" on line 10
		jekyll 3.4.0 | Error:  Invalid GBK character "\xE2" on line 10
```

​		很明显，是编码问题，参考网上方法[解决 Invalid GBK character "\xE2" 过程中的发现](https://yangaijun.com/2017/04/05/problem-solved.html)，找到			`D:\RailsInstaller\Ruby2.3.3\lib\ruby\gems\2.3.0\gems\sass-3.7.2\lib\sass.rb`文件，在`require后`追加：

```
	Encoding.default_external = Encoding.find('utf-8')
```