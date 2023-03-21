---
title: message-board
date: 2023-01-02 22:48:05
---
<style type="text/css">
    *{
    /* 初始化 取消页面元素的内外边距 */
    margin: 0;
    padding: 0;
}
.container{
    /* 100%窗口高度 */
    height: 10vh;
    /* 弹性布局 水平、垂直居中 */
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: --background-secondary;
}
.message{
    font-size: 30px;
    color: --text-bright;
    /* 字间距 */
    letter-spacing: 5px;
    /* 模糊滤镜 */
    filter: blur(2px);
    /* 执行动画：动画名称 时长 线性的 无限次播放 */
    animation: animate 2.5s linear infinite;
}
/* 接下来为每一个span元素设置动画延迟时间 */
.message:nth-child(1){
    animation-delay: 0s;
}
.message:nth-child(2){
    animation-delay: 0.25s;
}
.message:nth-child(3){
    animation-delay: 0.5s;
}
.message:nth-child(4){
    animation-delay: 0.75s;
}
.message:nth-child(5){
    animation-delay: 1s;
}
.message:nth-child(6){
    animation-delay: 1.25s;
}
.message:nth-child(7){
    animation-delay: 1.5s;
}
.message:nth-child(8){
    animation-delay: 1.75s;
}
.message:nth-child(9){
    animation-delay: 2s;
}
.message:nth-child(10){
    animation-delay: 2.25s;
}

/* 定义动画 */
@keyframes animate {
    0%,100%{
        color: --text-bright;
        /* 模糊滤镜 */
        filter: blur(2px);
        /* 文字阴影 */
        text-shadow: 
        0 0 2px #6699ff,
        0 0 4px #6699ff,
        0 0 6px #6699ff,
        0 0 8px #6699ff,
        0 0 10px #6699ff,
        0 0 12px #6699ff,
        0 0 14px #6699ff,
        0 0 16px #6699ff
        ;
    }
    5%,95%{
        color: --text-bright;
        filter: blur(0px);
        text-shadow: none;
    }
}
</style>
<div class="container">
        <span class="message">欢</span>
        <span class="message">迎</span>
        <span class="message">W</span>
        <span class="message">e</span>
        <span class="message">l</span>
        <span class="message">c</span>
        <span class="message">o</span>
        <span class="message">m</span>
        <span class="message">留</span>
        <span class="message">言</span>
</div>
<center><img src="https://pic.imgdb.cn/item/63b583a9be43e0d30e6dd7ee.jpg" width="500" height="400"/></center>
<br/>
<center>😜有什么想问的？😜</center>
<center>🧋有什么想说的？🧋</center>
<center>🤮有什么想吐槽的？🤮</center>
<center>🍲哪怕是有什么想吃的，都可以告诉我哦~🍲</center>
<br/>
<center><img src="https://yimoorua-img.oss-cn-chengdu.aliyuncs.com/images/68887b54-7c2a-4abf-bd3a-3c24aa5595a2.jpg" style="width:100%px" /></center>