---
title: 近期遇到的BUG汇总【1】
date: 2018-05-10 10:11:54
tags: 
  - bug
  - js
  - css
categories:
    BUG
---

> 最近一段时间一直忙于公司项目的跟进，没能继续博客的更新但是在项目中遇到了一些自己没怎么遇见或者说比较棘手，也许说是比较奇葩的几个BUG。

---

## pointer-events css属性

``` css
pointer-events: none;
```

起初的实现是在写一个hover出的a<link>标签的时候点击实现一个跳转，但是在我手抖写css的时候手抖写出了以前没有遇到过的属性`pointer-events`。起初的跳转失败以为是js时间绑定，各种疯狂的寻找后发现问题并不是出现在这里。然后思路跳回css从`z-index`页面层级上面着手，层级关系显示父级的`position`为absolute,子元素也没有受到父级的遮挡，但是点击以后没有能够执行。万般无奈的只能复查css属性，结果查到了这个`none`值。

### 了解pointer-events

真的说完全没有见过这个属性还是不对的，对于这个属性还是有着一面之缘，在一些注重节假日氛围烘托的网站是有一些跟随节日主题的特效生成，比如什么圣诞节的雪花飘落，闪烁的星空等等。而这些粒子效果有的实现是用`flash`、`canvas`，但是也有纯`html、css`写成的效果，这里面一般在最外层的效果层级都会加上这个属性，类似于点击穿透不会影响正常层级页面的事件委托，click、mouseover等所有事件会穿透它到达它的下一级元素。从而达到只是效果呈现的目的。算得上是一个比较黑科技的武器了。

适用的场景除了上面所说的还比如有一些类似数据可视化的在一些操作用的`action`的地方全局`pointer-events: none`然后在想要有事件捕获的子元素加上`pointer-events: auto`。这样就不会影响数据展示层也能实现面板自由使用。

兼容性

![兼容性](http://p7jj1bbaq.bkt.clouddn.com/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_15259213366850.png)

然后这里还有一篇张鑫旭大佬在13年关于这个属性引出的一篇文章的疯狂Diss,可以看看程序猿的众生百态[点位](http://www.zhangxinxu.com/wordpress/2014/01/pointer-events-none-avoiding-unnecessary-paints/)

## vue 事件绑定 fn()里括号的书写

有这样一个点击搜索按钮，获取分页的表格数据回来的代码。
``` html
<el-button type="success" @click="fetchData()">搜索</el-button>
```

``` js
  async fetchData(page) {
    page = page || 1;
    console.log(page);

    this.list = await this.fetchPage({
      method: 'get',
      url: `${base}/`,
      data: {
        where: this.filters,
        page,
        sort: this.sort,
      },
      loading: 'loading',
    });
  }
```
如果绑定的获取事件丢失括号获取到的`page`属性为

![page属性](http://p7jj1bbaq.bkt.clouddn.com/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_15241931435010.png)

正确加了括号后取到的

![page值](http://p7jj1bbaq.bkt.clouddn.com/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_15241931410.png)

## 微信开发中引起的跨域问题

在做微信扫码登录的时候用到了微信的api,直接在前端用AJAX请求会百分百提示跨域，那是不可取的，非要上前端请求去拿`JSONP`解决跨域，对于微信来说这种非官方的协议是不安全的，会报错导致无返回正确结果。然后就是本地环境`localhost`请求也是无法正确请求的，微信不能以`ip`形式请求，所以老老实实测试环境上动刀了。