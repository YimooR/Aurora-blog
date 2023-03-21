---
title: 关于
date: 2023-01-02 22:54:45
---
<style>
    body {/*与边框对其*/margin: 0;
    /*背景颜色*/background-color: var(--background-primary)}
    #d1 {/*高度*/height: 200px;/*宽度*/width: 200px;/*画一个圈*/border-radius: 50%;
        /*边框一致*/border: 8px solid white;/*调位置*/margin: 0 auto;
        /*溢出位置隐藏*/overflow: hidden;}
    #d1>img {/*让img标签占#d1的百分之百*/width: 100%;}
}    
</style>
<center><div id="d1"><img style="wight:200px;height:200px" src="/static/img/avator.gif" alt=""></div>
</center>
<link href="https://fonts.font.im/css?family=Monoton" rel="stylesheet">
<style>
body{background-color: var(--background-secondary);} .neon-wrap span{display: block;} .neon-text{font-family: 'Monoton', cursive; font-size: 50px; animation: neon 4s infinite;} @keyframes neon{0%, 30%, 32%, 34%{color: var(--background-secondary); text-shadow: -3px 0px 5px #222, 0px 0px 2px #222;} 31%, 33%, 35%, 100%{color: #5433ff; text-shadow: -3px 0px 5px #ff0099, 0px 0px 2px #ff0099;} }
  </style>
  <center>
  <div class="neon-wrap">
    <span class="neon-text">YimooRua</span>
  </div>
  </center>

<hr>
<style>
::selection{background: #6d50e2; color: #fff;}
.container{max-width: 950px; width: 100%; padding: 40px 50px 40px 40px; background: var(--background-secondary); margin: 0 20px; border-radius: 12px; }
.container .topic{font-size: 30px; font-weight: 500; margin-bottom: 20px;}
.content{display: flex; align-items: center; justify-content: space-between;}
.content .list{display: flex; flex-direction: column; width: 20%; margin-right: 50px; position: relative;}
.content .list label{height: 60px; font-size: 22px; font-weight: 500; line-height: 60px; cursor: pointer; padding-left: 25px; transition: all 0.5s ease; color: #333; z-index: 12;}
#home:checked ~ .list label.home,
#blog:checked ~ .list label.blog,
#help:checked ~ .list label.help,
#code:checked ~ .list label.code,
#about:checked ~ .list label.about{color: #fff;}
.content .list label:hover{color: #6d50e2;}
.content .slider{position: absolute; left: 0; top: 0; height: 60px; width: 100%; border-radius: 12px; background: #6d50e2; transition: all 0.4s ease;}
#home:checked ~ .list .slider{top: 0;}
#blog:checked ~ .list .slider{top: 60px;}
#help:checked ~ .list .slider{top: 120px;}
#code:checked ~ .list .slider{top: 180px;}
#about:checked ~ .list .slider{top: 240px;}
.content .text-content{width: 80%; height: 100%;}
.content .text{display: none;}
.content .text .title{font-size: 25px; margin-bottom: 10px; font-weight: 500;}
.content .text p{text-align: justify;}
.content .text-content .home{display: block;}
#home:checked ~ .text-content .home,
#blog:checked ~ .text-content .blog,
#help:checked ~ .text-content .help,
#code:checked ~ .text-content .code,
#about:checked ~ .text-content .about{display: block;}
#blog:checked ~ .text-content .home,
#help:checked ~ .text-content .home,
#code:checked ~ .text-content .home,
#about:checked ~ .text-content .home{display: none;}
.content input{display: none;}
</style>
<div class="container">
    <div class="topic">心得路程</div>
    <div class="content">
      <input type="radio" name="slider" checked id="home">
      <input type="radio" name="slider" id="blog">
      <input type="radio" name="slider" id="help">
      <input type="radio" name="slider" id="code">
      <div class="list">
        <label for="home" class="home">
        <i class="fas fa-home"></i>
        <span class="title">建立初衷</span>
      </label>
      <label for="blog" class="blog">
        <span class="icon"><i class="fas fa-blog"></i></span>
        <span class="title">博客内容</span>
      </label>
      <label for="help" class="help">
        <span class="icon"><i class="far fa-envelope"></i></span>
        <span class="title">学习想法</span>
      </label>
      <label for="code" class="code">
        <span class="icon"><i class="fas fa-code"></i></span>
        <span class="title">寄语</span>
      </label>
      <div class="slider"></div>
    </div>
      <div class="text-content">
        <div class="home text">
          <div class="title">搭建博客初衷</div>
          <p>作为一个非科班的学生，一直觉得拥有自己的独立网站是一件十分酷的事情！这样就相当于拥有自己独立的空间，不用去CSDN这些网站上写博客或者用有道云笔记去记笔记。网站成为了自己的一个名片，在上面写写东西，记录生活，分享知识，把自己想与他人展现的技能放在博客上，何尝不是一件有趣的事情！</p>
        </div>
        <div class="blog text">
          <div class="title">博客内容</div>
          <p>在这个博客网站上，发表主要是本人平时所做的笔记与总结，内容主要是关于Hexo博客搭建、 Java 后端、 LeetCode 算法总结，一些想法等等。当然也有部分东西是搬过来的(均经过作者同意并注明了出处)</p>
        </div>
        <div class="help text">
          <div class="title">建立想法</div>
          <p>为什么当初会阴差阳错地选择了Hexo这样的静态博客作为自己的网站框架呢？其实最初是看见B站上看到了别人Hexo博客，觉得好厉害，是二次元风格的(觉得用来装逼十分合适)，自己也萌生了想弄一个博客的想法。后面逐渐了解到，Hexo这种静态博客相比于WordPress这些动态博客，可以不用购买服务器，网站的运营费用主要是域名费用(白嫖)。而采用动态博客就必须自己租云服务器，虽然WordPress部署起来更加简单，不用折腾那么多(但是我是一个比较喜欢折腾的人)。</p>
        </div>
        <div class="code text">
          <div class="title">寄语</div>
          <p>感谢每一位光临小站的朋友，有什么建议可以留言或者<a href="https://pic.imgdb.cn/item/63b44179be43e0d30e8eb44c.jpg">QQ</a>联系我哦~</p>
        </div>
        </div>
      </div>
    </div>
  </div>
<hr>

<b style="color:blue"> To Do List</b>

|             完成情况             | 技术栈                                                       | 完成时间 |
| :------------------------------: | :----------------------------------------------------------- | :------------------------------- |
| <input type="checkbox" checked/> | 精通 Java 面向对象编程思想、集合、多线程、泛型、IO、反射机制等 | 2022年2月5日 |
| <input type="checkbox" checked/> | 熟悉 JVM, 如垃圾回收机制                                     | 2022年4月10日 |
| <input type="checkbox" checked/> | 精通常用设计模式如：工厂模式、单例模式、模版模式             | 2022年5月21日 |
| <input type="checkbox" checked/> | 熟悉 Mysql 常用 sql 语句，索引优化、性能调优等               | 2022年7月1日 |
| <input type="checkbox" checked/> | 熟悉 Spring、Mybatis、Spring MVC 等常用框架以及 Mybatis-plus | 2022年8月29日 |
| <input type="checkbox" checked/> | 熟练掌握 SSM 整合                                            | 2022年9月15日 |
| <input type="checkbox" checked/> | 熟悉使用 SpringBoot                                          | 2022年10月16日 |
| <input type="checkbox" disabled/> | 熟悉 SpringCloud                                             | 待更新 |
| <input type="checkbox" disabled/> | 熟悉各种常用中间件，如 Rabbit                                | 待更新 |
| <input type="checkbox" disabled/> | 熟悉 Redis 缓存数据库应用                                    | 待更新 |
| <input type="checkbox" checked/> | 熟练使用 Gitee、GitHub、Git 等常用代码托管平台               | 2022年10月1日 |
| <input type="checkbox" checked/> | 熟悉常用的 Linux 命令，安装 Mysql、服务器的布置              | 2022年10月21日 |
| <input type="checkbox" checked/> | 熟悉计算机工作原理，操作系统原理，计算机网络原理             | 2022年11月2日 |
| <input type="checkbox" disabled/> | 交换机的配置| 待更新 |
