---
layout: post
title: 搭建博客和ruby的安装
date: 2019-11-02
---

### 使用GitHub Page搭建博客

- 参考:<https://www.cnblogs.com/sqchen/p/10757927.html>

### ruby:

- 更新ruby版本`gem update --system`

- 安装jekyll `gem install jekyll`

- 清华大学源：
  <https://mirror.tuna.tsinghua.edu.cn/help/msys2/>

- 各种错误参考

```bash
1. https://www.jianshu.com/p/58e2c5ea3103
2. https://www.bbsmax.com/A/1O5EpbXyJ7
```

- 启动Jekyll服务 

  bundle exec jekyll serve，自动生成yml，启动Jekyll服务时，可能会遇到如下错误：

```bash
	Conversion error: Jekyll::Converters::Scss encountered an error
  	while converting 'css/main.scss':Invalid GBK character "\xE2" on line 10
	jekyll 3.4.0 | Error:  Invalid GBK character "\xE2" on line 10
```

​	 很明显，是编码问题，找到`ruby目录\lib\ruby\gems\2.3.0\gems\sass-3.7.2\lib\sass.rb`文件，在`require后`追加：`Encoding.default_external = Encoding.find('utf-8')`即可。