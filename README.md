# Trace Flight 

This is my personal blog modified from [Microdust](https://github.com/Azeril/azeril.github.io) which is based on  [Clean Blog theme by Start Bootstrap](https://github.com/BlackrockDigital/startbootstrap-clean-blog-jekyll). The theme is designed for [Jekyll](https://jekyllrb.com).

## The main changes are shown as follows.

* Reorganize the folders structure for more elegent look. Such as move *`Tools.md, About.md, Lists.md, tags.html`* to folder *`pages`*, merge folder *`img`* and *`images`*.
* Redesign same pages' logic. Some buttons on top right of the tab bar can not just display a static markdown page but a list of blogs classified by category. So it is fair to these buttons.
* Remove some useless functions in my opinion. For example, remove tags above title. And in [Microdust](https://github.com/Azeril/azeril.github.io) a click on these tags will led to 404 page.
* Remove "#" in front of each title in [Microdust](https://github.com/Azeril/azeril.github.io) theme.  

## How to use it as your own blog?

1. First, you should know some basics. Here is a guide [Creating and Hosting a Personal Site on GitHub](http://jmcglone.com/guides/github-pages/). It shows brief description of [Git](https://git-scm.com), [GitHub](https://github.com), [GitHub Pages](https://pages.github.com/) and [Jekyll](https://jekyllrb.com).
2. Then follow the guide to creat your first blog. Though simple, it is really useful for you to understand Jekyll.
3. After that, you have known how to creat a site on github online, but this is not enough. Then you need to [install Jekyll](https://jekyllrb.com/docs/quickstart/) on your pc in order to view your blog in real-time. To perform this blog on pc you also need to install *jekyll-gallery-generator* and *jekyll-paginate* by gem. Before that you should install *libmagickwand-dev* and *imagemagick* by apt-get or yum depending on you linux distribution.

	Or, you can use commands as follows:

	`sudo apt-get install ruby ruby-dev libmagickwand-dev imagemagick`

	~~`gem sources --add https://ruby.taobao.org/ --remove https://rubygems.org/`~~ *(ruby.taobao.org is no longer maintained.)*
	
	`gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/`
		(*This command is needed for Chinese users.*)

	`sudo gem install jekyll jekyll-gallery-generator jekyll-paginate`

4. Finally, delete the repository you created for test, then fork this repository and rename to your own username. Clone and modify it as you want. Enjoy it!

----------


# Trace Flight

这是我的个人博客主页，博客的主题基于 [Microdust](https://github.com/Azeril/azeril.github.io) 进行了修改。[Microdust](https://github.com/Azeril/azeril.github.io) 主题是[Azeril](https://github.com/Azeril)在[Clean Blog theme by Start Bootstrap](https://github.com/BlackrockDigital/startbootstrap-clean-blog-jekyll)基础上设计的适用于 [Jekyll](https://jekyllrb.com)博客系统的主题。

## 主要修改如下 ##

* 重新组织了文件结构，使得看上去更舒服（只适用于OCD）。包括：将*`Tools.md, About.md, Lists.md, tags.html`* 移动至文件夹 *`pages`* 中，将文件夹 *`img`* 和 *`images`* 合并等.
* 重新设计了主页顶端导航栏按钮的逻辑。 原本按钮除第一个外，其他的仅显示一个`markdown`或`html`页面，将其修改为分类标签，每个按钮显示一类主题的博文。
* 删除了一些感觉没有用的功能。如：删除了文章标题上面的的`tags`，这些标签能够表达的意义有限。而且，在[Microdust](https://github.com/Azeril/azeril.github.io) 中点击这些标签会得到404页面。
* **解决了[Microdust](https://github.com/Azeril/azeril.github.io)中文章标题前显示#的问题。 2016.10.21** 

## 如何使用这个主题？ ##

1. 首先，了解一些基础知识是必要的。既然选择自己建一个博客，就应该折腾一番。这里有一篇文章可以参考 [Creating and Hosting a Personal Site on GitHub](http://jmcglone.com/guides/github-pages/)，这篇文章对 [Git](https://git-scm.com)、[GitHub](https://github.com)、[GitHub Pages](https://pages.github.com/)和[Jekyll](https://jekyllrb.com)都做了介绍。
2. 然后，根据上面提到文章，手动创建第一个博客。尽管所建立的博客非常简洁，但是对于了解Jekyll博客系统很有必要，如能够知道各个文件在博客网站中的作用等。
3. 通过上面两个步骤，已经了解如何在线创建一个博客，但是在线操作总是不方便，那么如何离线创建并运行博客系统呢。本地[安装Jekyll](https://jekyllrb.com/docs/quickstart/)能够在电脑上实时查看博客的样子，而不需要上传到Github才能查看。当然不安装Jekyll也是可以的，但是在创建博客的初期，建议还是安装为好。此外，本博客主题需要用到 *jekyll-gallery-generator* 和 *jekyll-paginate* 以及 *libmagickwand-dev* 和 *imagemagick*。具体安装方法如下：

	`sudo apt-get install ruby ruby-dev libmagickwand-dev imagemagick`

	~~`gem sources --add https://ruby.taobao.org/ --remove https://rubygems.org/`~~ *(ruby.taobao.org is no longer maintained.)*

	`gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/`
		(*对于中国用户需要这个操作，你懂得~*)

	`sudo gem install jekyll jekyll-gallery-generator jekyll-paginate`

4. 最后，首先删除Github中用户学习的Github Page项目，然后fork本项目到自己的项目库中。将项目名称改为`用户名.github.io`，Clone到本地，修改后可使用Jekyll在本地预览（具体操作参见[Jekyll官网](https://jekyllrb.com/)），达到满意的效果后就可以push到Github中，然后在线查看你的博客了。

## TODO ##

* ~~现在文章的标题前面都会显示一个#，这个问题在[Microdust](https://github.com/Azeril/azeril.github.io) 中也存在，但是在[Clean Blog theme by Start Bootstrap](https://github.com/BlackrockDigital/startbootstrap-clean-blog-jekyll)中不存在，因此可以对比两个项目，查出问题所在。（一直没有时间做）~~ **Fixed at 2016.10.21**
* 各个分类文章上一篇和下一篇的按钮和提示。
* 美化工作。
