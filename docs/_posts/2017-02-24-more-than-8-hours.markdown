---
layout: post
title: 名义8小时
date: '2017-02-24 00:50:00'
tags:
- thinking
---

【全文约1000字，1.6分钟读完】
<div id="player1" class="aplayer" style="margin-bottom: 40px"></div>

### 大熊猫的进食
大熊猫[日食量达10-60公斤](http://panda.china.com.cn/2010-06/29/content_20381363.htm)，有些季节更要在40公斤以上，如此大的食量，几乎相当于体重一半。这使得大熊猫每天要花费十几个小时去不停的吃竹子，才能满足能量需要。对于大熊猫而言，生活就是不停的吃竹子嘛，这是它的常态。

然而，哪个物种又何尝不在时时刻刻的觅食？即便强如狮子、老虎，也要不停的观察猎物，然后追捕它们；醒来，要么是不停的吃，要么就是不停的找吃的。

找食，吃食，继续找食，这是生命的常态。

### 名义8小时

成年后，会越发感觉**时间不够用了**。这当中有多方面的原因，其中一点就是我们要工作。

公司雇佣员工，不管目的如何，首先要“**购买员工时间**”，这是多数公司让让员工创造价值的基本手段。我所在的公司上班时间是9:00-17:00，属标准的朝九晚五。虽然公司只规定了8小时的工作时间，但我为此付出的可不止8小时（更别提有加班的情况）。

为什么这么说：

* 首先，这8小时是一天最精华的**黄金时间**。
* 其次，全天的安排，包括8小时之外，都要围绕着“这8小时”开展（自由进一步丧失）。
* 最后，8小时之外的时间，被严重碎片化。

这就是**“名义8小时”**，更严重的说:
>**公司购买的是：你的全部时间**。

对个人而言，自由是多么可贵，**可整块支配自己的时间**更是自由的第一步。

### 加班的机会成本
进一步考虑，8小时还只是理想情况，多数人还要加班。我们先谈有加班费的情况，看看是否划算？

我国劳动法规定，加班的薪酬标准是：

* 平日，1.5倍
* 周末，2倍
* 法定假日，3倍

看似通过加班能以**更高的效率**赚钱，但是否如此呢？在这个过程中，我们要明白此消彼长的道理。也就是“多一小时的加班，就少一小时的自由”。

我们先计算一些时间：

以名义的8小时计算，一周工作小时数是W=8\*5 = 40，自由时间是F = 24\*7-W = 128。所以，W:F = 40:128 = 1:3.2，即:

>**每增加1份工作时间，至少需要3.2份自由时间补偿**。

比如，我晚上加3小时的班，我的**休息时间应额外增加9.6小时**，才算恢复正常水平。

从这个角度去看，你还会觉得加班的代价很低吗？加班费最高3倍是合理的吗？

事实上，加班费通常又以基础工资为计算标准，保险、福利等也不会根据加班费而增加，这进一步降低了加班费的价值。更不要说没有加班费的情况了。

成本，是丧失的最大机会，**仔细考虑9.6小时的自由价值，若大于3小时的加班价值，你就应该拒绝**。当然，自由时间若只是用来kill的，那么本文就对你没有任何意义了。

每当事事都为8小时让路的时候，每当年年要挤在同一天放假的时候，我多么希望能得到真正的自由！

人类早已从食物链解放，但8小时的工作和大熊猫十几个小时吃竹子，又有什么区别呢？

（完）
<!-- 修正ghost博客默认主题对button的设置导致aplayer播放器按钮未知错乱-->
<style type="text/css">
.aplayer-icon {min-height:0;}
</style>

<!-- 引入aplayer播放器-->
<script src="https://cdn.bootcss.com/aplayer/1.6.0/APlayer.min.js"></script>

<!-- aplayer播放器配置-->
<script>
var ap = new APlayer({
    element: document.getElementById('player1'),                       // Optional, player element
    narrow: false,                                                     // Optional, narrow style
    autoplay: false,                                                    // Optional, autoplay song(s), not supported by mobile browsers
    showlrc: 0,                                                        // Optional, show lrc, can be 0, 1, 2, see: ###With lrc
    mutex: true,                                                       // Optional, pause other players when this player playing
    theme: '#e6d0b2',                                                  // Optional, theme color, default: #b7daff
   mode: 'order',                                                    // Optional, play mode, can be `random` `single` `circulation`(loop) `order`(no loop), default: `circulation`
    preload: 'metadata',                                               // Optional, the way to load music, can be 'none' 'metadata' 'auto', default: 'auto'
  //  listmaxheight: '513px',                                             // Optional, max height of play list
    music: {                                                           // Required, music info, see: ###With playlist
        title: '名义8小时',                                          // Required, music title
        author: ' 朗读者：顾唐散人',                          // Required, music author
        url: 'http://ok4jsyu7n.bkt.clouddn.com/content/auido/2017/05/more-than-8-hours-v2.mp3',  // Required, music url
        pic: 'http://ok4jsyu7n.bkt.clouddn.com/content/images/touxiang200705.jpgface-coolart-360x360.jpg',  // Optional, music picture
      
    }
});

ap.volume(100);
</script>
