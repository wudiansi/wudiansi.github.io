---
title: HEXO博客加上来必力（livere）评论
date: 2018-04-12 13:56:48
tags: 
  - hexo
categories:
    blog  
---

## 接入合适的评论
---

目前市面上大家用的比较多的几个评论接入有如下几个：

* HyperComments [https://www.hypercomments.com](https://www.hypercomments.com) 一款来自毛子的系统，考虑到战斗民族的战斗性情，我还是选择了放弃（主要还是主题没接入(；´д｀)ゞ）
* 畅言 [http://changyan.kuaizhan.com ](http://changyan.kuaizhan.com ) 在国内大小网站都有接入，对于国内用户有不错的体验，但是丑丑的界面有点不能接受，但是这个需要有备案号，虽然我有但是我注册了居然还各种给我审核，丑拒~
* Gitment [https://github.com/imsun/gitment ](https://github.com/imsun/gitment ) 之前大家说最好不怕和谐的方案就是直接issue的方式提交到github上不为一个不错的方案。gitman就是解决了这样一个东东，但是比较差的是貌似只能是github登录评论？岂不是不友好，弃之~
* Gitalk [https://gitalk.github.io/](https://gitalk.github.io/) 一个基于 Github Issue 和 Preact 开发的评论插件,比较好的是支持Markdown语法，挺实用的一个功能吧，舍弃的原因同上。
* Disqus [https://disqus.com](https://disqus.com) 做得很好的一款，支持很多登录，而且我对他的界面UI真的很心水，很符合当下的这个主题风格，非常的整洁舒适的风格。但是就是被墙了、被墙了、被墙了（重要的事情说三遍）。无奈放弃了（后面会说到）
* 来必力 [https://livere.com](https://livere.com) 韩国思密达出品，界面中规中矩。能够接受的风格，重要的是没被墙，而且后台管理挺不错，可以邮箱通知提交的评论。支持三种语言中、英、汉语。而且根据语言环境支持合适的登录方式。一溜的国内大厂的登录方式对于大家的使用友好感MAX百分百。
* 友言 [http://www.uyan.cc](http://www.uyan.cc) 国内出品，没有具体了解过，风格看起来挺清爽的样子，有时间可以尝试接入一下。
* 多说 已经下线，记得烧纸。
* 网易云 已经下线，记得烧纸。

## 起初采坑

最开始的时候接入的是我最心水风格的`Disqus`，清爽的风格呈现，友好的交互效果，通俗易懂的接入文档。。。（好了，我编不下去了，其实就是因为主题默认的第一个社会化评论就是它。。。）

### 起初的一切都是那么的完美

可以看见更多的支持的是国外使用率较高的、或者是存在的（此处404）登录方式。实在是可惜。
![未登录界面](http://p7jj1bbaq.bkt.clouddn.com/0.png)

![登录后无评论界面](http://p7jj1bbaq.bkt.clouddn.com/1.png)

![登录后有评论界面](http://p7jj1bbaq.bkt.clouddn.com/4.png)

只是登录方式的不完备性其实还能都接受，然而这一切都是在我打开了代理的情况下呈现的，当我关闭代理后，我猛然发现--我的评论模块去哪儿了？ 再去一百度结果才发现，被赤裸裸的墙了。太可惜了，对于我这样的没有梯子不能访问评论的依赖我是不能忍得，忍痛割爱了~

### 尝试其他

> 期间我也尝试接入了其他的一些评论接入，比如Gitalk和畅言。但是最后都还是舍弃了，不过他们都有各自的优点，可以先注册好，以便于以后这个挂了之后有一个备用的方案，一定要有plan B ~。

## 来必力

1.第一步当然是打开官网先注册了。[https://livere.com](https://livere.com),按照

![注册填写](http://p7jj1bbaq.bkt.clouddn.com/5.png)

邮箱注册，限制性可以说是基本没有了。貌似官网是需要科学上网的，昵称就是你回复的昵称啦。是可以随时修改的。

2.选择你想要的版本

好吧其实，我只会选City版（免费...），其他的也可以能享受更好的定制服务，土豪随意~

![选择版本](http://p7jj1bbaq.bkt.clouddn.com/6.png)

3.拿到你无与伦比、高贵奢华的uid

![获取uid](http://p7jj1bbaq.bkt.clouddn.com/3.png)

4.将你的uid正确的放到你的hexo项目下

不同的主题可能设置的方式有所区别。以下是我收集到的几个流行主题的设置方式：

### 在Next中接入来必力配置如下：
- 首先在 _config.yml 文件中添加如下配置：
```
# Support for LiveRe comments system.
# You can get your uid from https://livere.com/insight/myCode (General web site)
livere_uid: your uid
```
- 在 layout/_scripts/third-party/comments/ 目录中添加 livere.swig，文件内容如下：
```
{% if not (theme.duoshuo and theme.duoshuo.shortname) and not theme.duoshuo_shortname and not theme.disqus_shortname and not theme.hypercomments_id and not theme.gentie_productKey %}
  {% if theme.livere_uid %}
    <script type="text/javascript">
      (function(d, s) {
        var j, e = d.getElementsByTagName(s)[0];
        if (typeof LivereTower === 'function') { return; }
        j = d.createElement(s);
        j.src = 'https://cdn-city.livere.com/js/embed.dist.js';
        j.async = true;
        e.parentNode.insertBefore(j, e);
      })(document, 'script');
    </script>
  {% endif %}
{% endif %}
```
优先使用其他评论插件，如果其他评论插件没有开启，且 LiveRe 评论插件配置开启了，则使用 LiveRe。其中脚本代码为上一步管理页面中获取到的。 
- 在 layout/_scripts/third-party/comments.swig 文件中追加：
```
{% include './comments/livere.swig' %}
```
- 最后，在 layout/_partials/comments.swig 文件中条件最后追加 LiveRe 插件是否引用的判断逻辑：
```
{% elseif theme.livere_uid %}
      <div id="lv-container" data-id="city" data-uid="{{ theme.livere_uid }}"></div>
{% endif %}
```

### 在主题yilia中接入如下：

- 找到并打开yilia目录下的新建文件livere.ejs

具体目录 `/yilia/layout/_partial/post` 下

- 将一般网站下的代码复制粘贴进去

代码实例
```
<!-- 来必力City版安装代码 -->
<div id="lv-container" data-id="city" data-uid="你的uid">
<script type="text/javascript">
   (function(d, s) {
       var j, e = d.getElementsByTagName(s)[0];

       if (typeof LivereTower === 'function') { return; }

       j = d.createElement(s);
       j.src = 'https://cdn-city.livere.com/js/embed.dist.js';
       j.async = true;

       e.parentNode.insertBefore(j, e);
   })(document, 'script');
</script>
<noscript>为正常使用来必力评论功能请激活JavaScript</noscript>
</div>
<!-- City版安装代码已完成 -->
```
- 打开yilia的文件夹里的_config.yml并粘贴一下代码
```
# 来必力
livere: 你的uid
```

### 在主题melody下面接入：

也就是我现在使用的主题，其实[官方文档]()https://molunerfinn.com/hexo-theme-melody-doc/#/third-party-support?id=laibili已经讲的很明白，很简答了。
Set the melody.yml

```
laibili:
  enable: true # or false
  uid: your laibili uid
```

## 小结

此处没有小结。