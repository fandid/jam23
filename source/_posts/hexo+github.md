---
title: hexo+github
date: 2017-11-20 18:28:36  
tags: 
- tech
- hexo
- github
categories: tech
---
前几天一直在折腾静态博客，相比wordpress之类臃肿的动态博客来说，static website越来越成为个人博客、轻量级博客的首选。在接触hexo和github之前，一直用的是`dropbox+droppages`的形式搭建静态页面。这种形式足够简洁，本地生成markdown后直接在文件夹内更新，省去在线编辑和修改的限制。droppages本身提供了几个theme，可修改css等文件，当然排版方面非常简陋，如果只是专注于写作、文字，droppages足够了，重点是真的很方便。（与后面hexo一比就能感受到）

下面来说说hexo和github的搭建过程，也算做个记录。某些方面droppages用起来还是有些限制，所以想着寻找合适的替代。首选还是希望有dropbox+something的组合，可惜除了droppages，没有找到什么合适的。寻找期间，尝试了evernote+postach.io的形式，也还不错，不过始终觉得别扭，也许对evernote没啥好感？然后尝试了farbox的最新产品bitcron，据说很多人都从其他平台迁移至bitcron，我试用了一下还行，就是价格感觉不太适合轻量级用户，对我这种经常折腾的人来说也不太实用，放弃。
没办法，只能自己动手搭一个了，在jekyll，hexo，hugo，octopress，ghost等众多框架中，我选了hexo，一来相对难度小一点，小白多钻研一下也可以弄明白；二来hexo基于node.js环境，对于Windows友好一点，而jekyll基于ruby，在windows上配置起来要繁琐一些。更重要的是hexo好看啊，充分展现了simple is beautiful

虽说hexo简单一些，但搭起来也处处是坑，第一遍啥也不懂结果费了半天劲404了，第二遍大概用了二十分钟就搭好了。前期准备工作做好之后搭起来就特别快，找一个好的教程也很重要，很多小问题基本google都能解决。

## 前期准备
* 安装node.js和git
  这是hexo搭建的基础，首先要安装node.js和git。按照[hexo官网](https://hexo.io/zh-cn/)文档中的流程一步一步来，基本没什么问题。

## 建站
* 安装hexo
  利用npm安装hexo 具体命令为
  `$ npm install -g hexo-cli` 
* 建立站点
  有过droppages的使用经验就很容易理解这一步要干嘛了。简单来说，一个文件夹就是一个website，就是一个blog，里面放了你网站的所有内容。这个文件夹可以自定义位置和名称。你可以放到任何一个你想要的盘里，命名为hexo或其他任何名称，打开此文件夹，shift+鼠标右键会出现`在此处打开命令窗口`的字样，点击会出现控制台，执行命令
`$ hexo init <folder>`
`$ cd <folder>`
`$ npm install`
folder为你自定义的文件夹名称 例如 `$ hexo init dd`
如果你没有事先建立文件夹，直接执行命令，hexo会按照你自定义的名称自动创建文件夹，一般是在C/user/administrator目录下。 
这一步完成后可以看到文件夹里已生成基本的配置文件。

* 自定义配置及主题更换
  打开根目录，找到`_config.yml` 这是站点的主配置文件，可以自定义网站title，author name等
  主题设置默认为landscape，hexo提供了很多好看简洁的theme，可根据要求自己选择[hexo theme](https://hexo.io/themes/) 在众多theme里我选了NexT这款主题，也是公认为hexo中最漂亮最简洁的一个主题 附上地址[NexT](http://theme-next.iissnan.com/)里边有关于主题各部分的配置内容，可参阅。以NexT为例，下载好主题，然后解压至根目录的themes文件夹，将解压后的文件重命名为next，然后在站点配置文件`_config.yml`中将`theme： landscape` 修改为`theme： next` 即可成功切换。打开themes中next文件夹，里边也有个`_config.yml`，这个是主题配置文件，可自定义修改主题。NexT主题本身有四个方案，在`_config.yml`里找到# Schemes可以看到有Muse，Mist，Pisces，Gemini四个方案，依个人喜好选择，启用哪个将#删除即可

* 本地预览
  第一次搭建时可先本地预览下初步成果，熟练后可将所有自定义配置完一并预览然后部署，顺序不重要，依个人习惯。本地预览时主要用到以下三个命令
  `$ hexo clean`清除缓存文件（这步也可省略，我的习惯是生成前都clean一下，当你发现修改某些东西后站点未发生变化或未生效，可执行clean）
  `$ hexo generate` 生成静态文件 可简写成hexo g
  `$ hexo server` 本地预览 默认地址为http://localhost:4000/
  启用本地预览可以看到站点的一些变化，我的建议是不要急着deploy，先在本地预览，一切都没问题后再进行部署。

* 主题的具体设置
  本地预览后你可能会发现网站还是非常简陋，想要内容更加丰富，可在主题配置文件里进行一些修改。（主要包括新建page，自定义tag和category，自定义social，增加rss，disqus插件，首页隐藏文章等）
  * 新建page  
  打开主题配置文件 可以看到menu的设置中有home，about，categories，tags，archives等文本，可自行选择启用或不启用。值得注意的是，启用主题配置文件中about，tags等文本并不代表可以直接在站点上显示，还需在根目录source文件夹创建相应的page才可。以tags为例，应用命令`$ hexo new page tags`，可以看到source文件夹中多了tags文件，打开，新增`type: "tags"` 这下可以正常显示tags页面了，其他about页面同理。
  * 修改post默认模板
  生成新文章时，使用`$ hexo new "xxx"`命令
  想要新建文章时默认添加tags，categories等文本，需要打开根目录scaffolds文件夹中的post文件，增加`tags:` 、`categories:` 字段即可，下次新建文章时不用再手动添加这两项，节省时间。
  * 自定义social
  next主题提供了许多social的默认项，打开主题配置文件，在social项中可自定义想要展示的social链接，有github，facebook，instagram，email等多个链接，还可以自定义 social icon，附上[fontawesome](http://fontawesome.io/3.2.1/)的链接，可以替换自己喜欢的图标。
  * 添加rss
  hexo提供了rss的生成插件 `hexo-generator-feed` 可参阅[官方教程](https://github.com/hexojs/hexo-generator-feed)
  首先执行命令`$ npm install hexo-generator-feed --save` 然后可在站点配置文件里Plugins行下添加字段 注意缩进
 > feed:
  type: atom
  path: atom.xml
  limit: 20
   
   然后在主题配置文件`rss:`后添加`"/atom.xml"` rss即可添加成功
  * 添加disqus评论插件
  首先要有[disqus](https://disqus.com/)账号，在setting中点击`add disqus to site` 设置shortname（可简短一些，便于设置），添加你网站的url，然后在主题配置文件disqus项中填写你的shortname 以及将 `enable:false`改为`enable:true` 成功开启评论插件
  * 控制首页文章显示字数
  如果文章很长的话，在首页显示全部字数不是很美观。想设置`read more`可在主题配置文件中将`auto_excerpt:`设置为`enable:true` 即可开启`read more`
  * 隐藏首页特定文章
  受此文章启发-[关于Hexo next主题如何在首页隐藏指定的文章](http://forwardkth.github.io/2016/05/08/next-theme-post-visibility/) 详情参阅。

  至此，自定义主题的配置项已基本涵盖，其他需要请自行查阅[hexo](https://hexo.io/zh-cn/docs/)和[next](http://theme-next.iissnan.com/)相关说明
  
## 部署
  本地阅览完，接下来要deploy到github pages 说白了hexo就是本地生成静态文件，然后再push到github  
* github准备
 先准备好github pages一些基本配置，到后面deploy可以节省一些时间。首先你要有个github账号（github真的是一个利器，即使不是程序猿，了解它版本控制的核心思想也是非常有用的） ，然后新建repository，为便于部署，推荐repository name设置为 username.github.io

* deploy
  参阅hexo文档[部署](https://hexo.io/zh-cn/docs/deployment.html) 
  首先要在站点配置文件`deploy:`下修改`type:`为`type: git` 
  执行命令`$ npm install hexo-deployer-git --save`安装hexo-deployer-git 然后在站点配置文件`type: git`下增加字段
 >  repo: （你的repository地址，可在github查看，默认形式为https://github.com/username/username.github.io.git）
>   branch: master （默认分支为master）
>   message: `"&#123;&#123; now&#40;'YYYY-MM-DD HH:mm:ss'&#41; &#125;&#125; "`
  
  配置好后即可部署 
  deploy命令为`$ hexo deploy` 可简写为hexo d
  
  至此整个静态网站就搭好了，输入github pages即可查看

## 其他
* hexo优缺点
  优点当然是简洁简洁简洁好看好看好看
  缺点（与droppages体验相比）
   * 发布文章虽然已经很简单了，但相比直接markdown丢到dropbox的方式仍然要多了几条命令
   * 不能手机编辑
   * 每次稍微改动一下就得重新generate deploy 不如dropbox方便
   * 换电脑后需要重新配置环境，不方便多端同步
 
* 关于github和coding
  github很多优秀的程序员都在用，如果觉得不太习惯可以用国内[coding](https://coding.net/)代替 相比github来说，coding的响应速度绝对没的说，另外github的私有库是要收费的，而coding可拥有两个免费的私有库。coding也提供静态pages和动态pages的服务，可结合hexo+github的方法将hexo部署到coding静态pages，动态pages可部署wordpress，目前官方正在公测动态pages，之后可能采取收费模式。不论是github还是coding，我想说的是这种版本控制的idea实在值得我们每个人学习，对于学生党来说，不失为写论文的利器。

* 关于error
  出现error不用着急 可先结合error info查找问题 注意中英文转换、空格、缩进等问题，其他问题大部分可google解决 （还是之前说的，拿不准可先localhost预览，没问题再deploy以减少错误发生）

* 关于图床
  可用七牛云










 

