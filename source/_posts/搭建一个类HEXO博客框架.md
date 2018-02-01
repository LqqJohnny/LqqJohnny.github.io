---
title: 搭建一个类hexo博客框架
date: 2018/2/1 上午9:50:32
tags: [hexo]
categories: hexo
---

前段时间利用空闲时间，结合自己之前失败的经验以及一时的兴奋加上一丝幸运，成功的完成了一个博客框架。

<!-- more -->

## 搭建一个类hexo博客框架

### 背景
> 以下都是废话，请直接跳过

其实在几个月前我就想要自己做一个博客网站了，看着别的大神都有自己的博客网站，甚是羡慕呀！ 那个时候自己用hexo + Git Page 做了一个博客站点，那也让我高兴了好几天。感觉有了自己的秘密小花园。 但是几天后 ，我就开始在想 ，hexo 生成静态网站的原理，在网上经过一番了解之后，想要自己做一个这样的工具。做自己的网站，那肯定会是一件很cool的事。于是，我自己从零开始自己尝试写一个 山寨hexo 。但是第一次尝试 ，以失败告终了。卡在了一个点： 怎么解析 md 文件并把相对应的信息用到 模板文件里去。尝试网上说的各种方法，最终放弃了。知道前段时间我看到一个项目，将数据存入一个json 在用的时候引入这个json。

于是，之前的问题就有了一个解决方案了：

### 思路

- webpack 打包过程用fs 解析每一个md文件，取出文件头中的title ，type，路径 等信息，存入一个 `article.json`
- 在模板文件（该项目选用的是vue，可以任意选用其他框架或者模板）中，直接require 那个文件，即可使用文章title展示博客列表
- 根据路由参数 `articles/:id` 获取文章的标题 ，找到文章的路径，引入对应的 md 文档，并且webpack配置 `md-loader` ,即可展示某一篇博客的内容
- 将一些网站的标题，主题等设置 ，分离到一个json配置文件。
- 将视图模板 分离出来，置于 theme 中 ，便于以后博客主题的扩展。


### 具体实现

#### 获取md信息

分别存为三个文件 ，articles.json   tags.json  categeries.json 分别用于 展示 博客列表页， 分类页面 和  标签页面 。

```js
function findSync(startPath) {
    let ret = {
      result: [],
      tags : {},  //  用于 标签页
      categories : {}  // 用于 分类页
    }
    var path = startPath;
    let files=fs.readdirSync(path);
    files.forEach((val,index) => {
      console.log("正在打包 ："+val);
        let fPath=join(path,val);
        let stats=fs.statSync(fPath);
        // if(stats.isDirectory()) {result.unshift({type:"directory",name:val,path:pa})};
        if(stats.isFile()){
          // 解析出 文章的参数 信息
          var data=fs.readFileSync(fPath,"utf-8");
          var headInfo = getHeader(data,fPath,val,ret.tags,ret.categories);

          ret.result.push(Object.assign({},{type:"file", name:val},headInfo.headJSON));
          ret.tags =  Object.assign({},ret.tags,headInfo.tags);
          ret.categories =  Object.assign({},ret.categories,headInfo.cates);
        }
    });
    return ret;
}
//  获取文章的 头部信息。
function getHeader(data,path,filename,tags,cates){
  var start = data.indexOf("---");
  var end = data.indexOf("---",start+3);
  if(start===-1 || end===-1 || start===end){ // 没有文字信息头
    return {};
  }

  var mdHead = data.substring(start+3,end);

  var headArr = mdHead.split(/\n/).filter(function(val){
      return val!==""&&val!=="\r";
  });
  var headJSON = {};
  headArr.map(function(val){
    var arr = val.split(":");
    var key = arr[0];
    if(key==="date"){
      var val = arr.slice(1).join(":").replace(/\r/g,"").trim();
    }else{
      var val = arr[1].replace(/\r/g,"").replace(/\[/g,"").replace(/\]/g,"").trim();

    }
    if(key ==="date"){ headJSON["timestamp"] = new Date(val).getTime();}// 将时间转为 long 有助于排序
    if(key === "categories"){cates = Object.assign({},cates,addTagsOrCates(key,val,filename,cates)) }
    if(key === "tags"){tags = Object.assign({},tags,addTagsOrCates(key,val,filename,tags))}

    headJSON[key] = val;
  })
    return {headJSON,tags,cates};
}

```


#### 列表页


```js
<template>
···
<div class="articlesList">
  <div class="article_item" v-for="a in list" >
    <router-link :to="genUrl(a.name)">{{genTitle(a.name)}}</router-link>
    <span class="date">{{getDate(a.timestamp)}}</span>
  </div>
</div>
···
</template>  
<script>
var arts = require('../../../../articles.json');
const {site , passwordOn} = require("../../../../blog.config.js");
export default {
  data(){
    return {
      list:arts,
    }
  },
  mounted(){
      document.title= site.title ;
  },
  methods:{

    genUrl(name){
      return "/article/"+name.replace(/\.md/g,'');
    },
    genTitle(name){
      return name.replace(/\.md/g,'');
    },
    getDate(time){
      var date = new Date(time);
      return date.getFullYear()+" / "+(date.getMonth()+1)+" / "+date.getDate();
    }
  }
}
</script>
```

#### 博客页


```js
<template lang="html">
<div class="articleInfo">
  <div class="title">{{$route.params.id}}</div>
  <hr>
  <div class="info">
    <span v-if="date">日期 :{{date}} </span>
    <span v-if="tags">标签 :{{tags}} </span>
    <span v-if="categories">分类 ： {{categories}} </span>
  </div>
  <div id="artConWrap">
      <article class="article" id="article" v-html="articleContent" v-highlight >
      </article>
  </div>
</template>

<script>
var articles = require('../../../../articles.json');
export default {
  data(){
    return {
      articleContent: "",
      date:"",
      tags:"",
      categories:"",
    }
  },

  beforeMount(){
    var id = this.$route.params.id;
    var _this = this;
    articles.map(function(val){
      if(val.title.trim() === id){
        _this.date = val.date ;
        _this.tags = val.tags ;
        _this.categories = val.categories ;
        _this.password=val.password;
      }
    })
  },
  mounted(){

    var id = this.$route.params.id;

    document.title= id;  //  设置标题

    var md = require('../../../../articles/'+id+'.md');
    var start = md.indexOf('<!-- deleteAbove -->');
    if(start>0){
      md = md.substring(start+"<!-- deleteAbove -->".length);
    }
    this.articleContent = md;
  }
}
</script>

```

#### 项目结构优化

现在文章能展示了 ，剩下的就是结构的优化了，参考了hexo的结构，将一些东西给提取到配置文件，具体的介绍可查看(项目介绍)[https://github.com/LqqJohnny/VBlog].


### 总结

自己撸一个博客出来，那股成就感还是很大的，对自己也更有信心了，再一次感觉到vue的魅力，一天就能做出一套博客主题来了。这开发效率真的高。之后还会基于这个项目的结构，做一个 类gitbook的项目 ，在这个基础上，应该能很快的开发完，相当于换一个展示主题而已。
