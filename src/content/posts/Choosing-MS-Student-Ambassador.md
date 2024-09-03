---
title: 我为何选择申请微软学生大使
published: 2023-07-07
description: 浅谈我为何选择申请微软学生大使
image: https://cdn.cnqs.moe/qianshi-cdn/2023/07/08a3f8cefb8bdffb0d57bbae41ec9e7f.png
tags: [微软学生大使]
category: 杂谈
draft: false
---



大家好，我是千石。今天我想和大家聊聊我最近申请微软学生大使这件事。

我选择申请微软学生大使，这个决定的起因是阅读了Jambo的这篇文章：[来 Azure 学习 OpenAI（ 四 ）|我的微软学生大使成长经历- 从 Alpha 到 Beta 的升级经验分享 (qq.com)](https://mp.weixin.qq.com/s/ReSoNYDItwKELo0kNbvOMA)

看完这篇文章，我心潮澎湃，文章中的微软学生大使的成长经历和他们的学习过程深深吸引了我。作者Jambo分享了他从微软学生大使 Alpha 升级到 Beta 的经历，他的努力和收获让我感到非常鼓舞。他不仅分享了他的学习和成长经历，还详细介绍了微软学生大使计划的各个阶段和要求，以及他如何通过举办活动和分享技术来提升自己的影响力。他的故事让我深感微软学生大使不仅是一个技术学习的平台，更是一个分享和传播知识，对社区做出贡献的机会。

也许有人问我，我申请微软学生大使的具体理由是什么呢？

回答这个问题，得追溯到我初中的时候，在QQ群聊中我接触到了“我的世界”，自由的自在的创造飞行让我玩的不亦乐乎。在高中，我了解到了模组(mod)，在下载了许多别人制作的mod后，我萌生了制作一个属于自己的mod的想法，并尝试用Java 1.5编写了我的第一个mod。我记得那是一个简单的天气模组，它可以让玩家在游戏中改变天气。虽然这个mod并不复杂，但是我在编写它的过程中遇到了很多问题，比如如何获取和修改游戏的天气状态，如何处理玩家的输入等。我花了很多时间在网上搜索解决方案，也在论坛上向其他玩家求助。经过一周的努力，我终于完成了这个mod，并在我的QQ群里分享了它。看到其他玩家使用我制作的mod并给予我积极的反馈，我感到非常开心和满足。那时候的我并不清楚什么是编程，也不清楚为什么Java可以编写mod，但那个时候的我，就像是在一个新世界中探险，充满了好奇和热情。

```java
private static void switchWeather(ServerWorld world) {
    if (world.isRaining()) {
        world.getGameRules().get(GameRules.DO_WEATHER_CYCLE).set(false, world.getServer());
        world.setWeather(0, 0, false, false);
    } else {
        world.getGameRules().get(GameRules.DO_WEATHER_CYCLE).set(false, world.getServer());
        world.setWeather(0, 6000, true, true);
    }
}
```

这段经历为我日后自学编程埋下了一颗种子。在高中，因为一些机缘巧合，我开始学习Python。我记得我最初的学习项目是一个简单的网页爬虫，它可以从网站上抓取我感兴趣的信息。虽然这个项目并不复杂，但是我在实现它的过程中遇到了很多问题，比如如何解析网页的HTML代码，如何处理网络请求等。我花了很多时间在网上搜索解决方案，也在论坛上向其他开发者求助。经过一个月的努力，我终于完成了这个项目，并在我的QQ群里分享了它。看到其他人使用我制作的爬虫并给予我积极的反馈，我感到非常开心和满足。那时我已经爱上了编程。(我没找到当时的记录，刚好前段时间帮助一位学妹解决了python爬虫的问题，所以贴上来了)

<img src="https://cdn.cnqs.moe/qianshi-cdn/2023/07/9b2140edd589d68cb9b0f9487932047a.png" alt="image.png" width="70%" />

后来我升入了大学，在大一时在学长的影响下开始学习Go语言。我记得我最初的学习项目是一个简单的[博客](https://github.com/okatu-loli/Q-BLOG)，它可以处理用户的请求并返回相应的结果。虽然这个项目并不复杂，但是我在实现它的过程中遇到了很多问题，比如如何处理HTTP请求，如何设计RESTful API等。我花了很多时间在网上搜索解决方案，也在论坛上向其他开发者求助。经过两个月的努力，我终于完成了这个项目，并在我的GitHub上分享了它。看到其他开发者使用我制作的博客并给予我积极的反馈，我感到非常开心和满足。自那时起，我就深深地陷入了编程的世界。

![image.png](https://cdn.cnqs.moe/qianshi-cdn/2023/07/22eaa9695c5a7af51786175bb90907e0.png)

在自学的过程中，我获得了[微软认证的Azure AI基础知识认证](https://www.credly.com/badges/4cb18693-d50f-4e29-b0a5-453102545a1a/public_url)，还完成了 [Microsoft Learn](https://chat.openai.com/c/learn.microsoft.com) 的 [Go 学习路径](https://learn.microsoft.com/zh-cn/training/achievements/learn.languages.go-first-steps.trophy?username=32736054&sharingId=6B96088597B387E0)，并因此获得了对应的奖杯。我记得我在准备Azure AI基础知识认证的过程中，我花了很多时间学习AI的基础知识，如机器学习，深度学习等。我也在实践中使用Azure的AI服务，如Azure Cognitive Services，Azure Machine Learning等。我在完成Go学习路径的过程中，我深入学习了Go语言的基础知识，如数据类型，函数，接口等。我也在实践中使用Go语言编写了很多项目，如Web服务器，命令行工具等。

<img src="https://cdn.cnqs.moe/qianshi-cdn/2023/07/1173ecdaf9e25089500f0676115521b6.jpeg" alt="_cgi-bin_mmwebwx-bin_webwxgetmsgimg__&MsgID=9168438031816894906&skey=@crypt_eeff3051_df4ce0f8428c9f78a3d4a91f5ba211ab&mmweb_appid=wx_webfilehelper.jfif" width="50%" /><img src="https://cdn.cnqs.moe/qianshi-cdn/2023/07/b5b981105e7412dcca78c95d3e8f0780.png" alt="image.png" width="50%" />

此外，我还有幸参加了字节跳动青训营。在这个训练营中，我作为小队的组长，带领团队完成了一个复杂的项目。虽然我们最后没有进入决赛，但这个经历让我收获了很多。我学到了如何管理项目，如何协调团队成员，如何解决团队中的冲突和问题。我也结识了很多有才华的同学，我们一起学习，一起进步，一起面对挑战。这个经历让我更加明白，团队协作的重要性，以及每个人在团队中的角色和责任。我也因此获得了结营证书，这是对我努力和成就的认可。

<img src="https://cdn.cnqs.moe/qianshi-cdn/2023/07/5711019fe739dd37cb82b25e236cac0c.png" alt="image.png" width="70%" />

每当我获得这样的成就，我都会感到无比的快乐和满足。我知道，这些经历和成就都是我成长的步伐，是我前进的动力。我期待在未来，我可以有更多的机会，更多的挑战，更多的成就。

我也喜欢[在GitHub上做开源开发](https://github.com/okatu-loli)，用Go和Python写代码。我喜欢那种与全球的开发者一起协作的感觉，那就像是一起建造一座城堡，每个人都在贡献他们的一份力量。这也是我想成为微软学生大使的原因之一，我希望能和更多的开发者联系在一起，分享我学到的知识。


![image.png](https://cdn.cnqs.moe/qianshi-cdn/2023/07/4f9e19c588251df3a9504216f573447f.png)

在学校，我有幸担任了计算机协会的科技部部长、副社长，以及樱花社的社长。在计算机协会，我负责组织各种技术活动，如编程比赛，技术研讨会等。我也负责培训新成员，教他们编程基础，如Python，Java等。在我的领导下，我们的协会成员数量有了显著的增长，我们举办的活动也吸引了更多的参与者。在樱花社，我负责组织各种社交活动，如聚餐，游戏比赛等。我也负责协调社员，解决他们的问题。在我的领导下，我们的社员满意度有了明显的提高，我们举办的活动也吸引了更多的参与者。这些经历让我更加深入地理解了团队协作的重要性，也让我有了丰富的组织和管理活动的经验。

在阅读了微软学生大使的成长经历和学习过程后，我深深被吸引。我理解微软学生大使不仅是一个技术学习的平台，更是一个分享和传播知识，对社区做出贡献的机会。我期待通过成为微软学生大使，我可以更好地运用我在学校和社区积累的经验，帮助更多的人了解和学习技术。

所以，回到最开始的问题，`“我为何选择申请微软学生大使？”`。我非常认同微软的开放、多元和包容的价值观。我希望能作为学生大使，让这些价值观在我的影响下能得到更广泛的传播和实现。我计划通过组织各种技术活动，如编程比赛，技术研讨会等，来传播微软的技术和价值观。

最后，我想申请成为微软学生大使，因为我对技术有热情，我享受分享知识的快乐，我希望通过自己的领导力帮助更多的人。我也期待通过这个机会，我可以更好地发展我的技术能力，领导能力，以及社区影响力。我知道，这将是我人生中一次重要的经历，我期待它能为我和周围的人带来新的可能性。

如果你也怀揣对成为`微软学生大使`的憧憬和期待，欢迎点击以下链接进行报名。特别欢迎大中华区的朋友们踊跃参与：<https://studentambassadors.microsoft.com/>

与我相关的链接：
- Github：https://github.com/okatu-loli
- 掘金社区：https://juejin.cn/user/3843548383816862
- Daily dev：https://app.daily.dev/CNQianShi
