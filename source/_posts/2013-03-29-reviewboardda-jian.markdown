---
layout: post
title: "ReviewBoard搭建"
date: 2013-03-29 16:39
comments: true
categories: 
---

之前在Mac上折腾了一上午，最终因为包依赖的问题而放弃，转而投向了Ubuntu,因为apt-get实在是太好用，省去了许多包管理带来的麻烦
不过在Ubuntu上安装也不会很省心，前面一路顺风，当执行 `easy_install ReviewBoard` 时候总是卡在了安装pytz上面，提示访问http://pytz.sourceforge.net 超时，但ping的时候又显示okay,所以怀疑是读取到下载地址后的下载超时，总不能在一棵树上吊死，所以只能先手动装上pytz,执行`easy_install pytz`完成之后继续`easy_instal ReviewBoard`
之后也碰到过其他的错误，不过google一下，基本都能解决

安装完了ReviewBoard,就可以开始配置站点了，在此之前，最好先用mysql新建一张表，然后执行 `rb-site install /var/www/reviews.example.com` 之后就进入了一个交互式的安装过程啦，输入之前创建的表名，以及对该表有创建修改权限的用户（可以填入root用户，当然你也可以使用GRANT新指定一个用户），之后填入你的admin帐号信息，一路Next

Okay，在浏览器输入localhost, 如果没有问题，会看到ReviewBoard的登陆页面，使用admin登入，点击右上角的用户头像，下拉菜单中有一个Admin，点击进去，在这里你就可以添加repository乐

Reviewboard支持两种审核方式，pre-commit 和 post-commit

+ pre-commit 在本地有变动，但没有提交到中心仓库前，提交review请求，有两种方法:

a. 手动: 执行svn diff,将其输出到一个.diff文件中，在reviewboard的web页面上手动添加该diff文件，创建并发布一个新的review request

b. RBTools: 它提供一个post-review的python脚本，可以在你当前svn checkout的目录或其任意父目录中创建.reviewboardrc文件，设置REVIEWBOARD_URL = "http://your/reviewboard/url",之后执行`post-review`即可自动为你生成一个新的review request,默认是未发表的，你可以加上 -p参数指明创建后发表，更多参数，你可以参考`post-review -h`

+ post-commit 在本地改动提交之后，再创建的review request,你可以手动生成diff文件来创建review request，当然你仍然可以通过post-review 这个脚本来做，此时你需要指定revision-range这个参数来让脚本生成diff，否则脚本会说：好像没有任何改动吧？


在使用post-review脚本的时候，还是碰到有意思的问题的，在ubuntu下面一切okay,但在mac机器下面，因为我既安装乐git，又装乐svn,结果使本来应该识别为SVN的识别成了git,而且还因此提交失败
使用 post－review --debug比较了一下成功和失败的不同，发现在ubuntu下没有卡在git检测下面，好吧，为了比较错误，我在ubuntu下面安装了git. 之后再运行post-review --debug,这回检测在git处卡顿，之后出现一个fatal error,导致脚本判断当前不是一个git仓库，然后继续检测，直到SVN成功
fatal error是怎么出现的呢？ 原来是`git rev-parse --git-dir`失败了，在mac下面这条指令输出的是～目录，而我的svn检出的目录就是～的子目录，我试着在它的上级目录重新检出，再执行`post-review --debug`,结果这一次和再ubuntu的情况一样了，检测git失败，接着检测到svn 成功发布
所以说碰到这种情况，一种解决方式就是避开git的git-dir（包括其子目录）检出svn仓库

虽然解决了，但不是很甘心，应该还有其他的办法。
供我研究折腾的时间不多，马上要策划公司的公共组件了，先记录下来，留待以后折腾：

+ 既然post-review debug的时候有一个checking顺序，那就应该可以配置这个顺序，让svn优先级大于git,这样也可以解决问题

+ 既然是卡在 `git rev-parse --git-dir`上面，那就应该可以修改git-dir来避开检出的svn

+ 难道这不是一个bug？有机会还是需要让reviewboard的那群家伙知道 ：）
