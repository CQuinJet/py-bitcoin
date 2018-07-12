### 比特币项目的python实现，利用python实现一个完整的区块链项目

从零用python实现比特币项目， 写一个完整的BTC链。

- 加密算法的应用 (Base58)
- p2p 网络
- 区块链数据的持久化存储
- 链上数据的查询
- UTXO 模型实现
- Mekletree 
- Wallet & Transaction



#### 项目架构介绍

如下为整个项目的架构图。这里从下往上对下图依次做个说明：

![架构图](https://github.com/csunny/py-bitcoin/blob/master/docs/resource/img/py-bitcoin.jpeg)


- Crypto 此模块为BTC链中用到的密码学相关的内容，主要包括Base58、hash以及椭圆加密等算法。我们知道，区块链中一个非常核心的就是就是加密技术，所以Crypto在整个技术架构中是占据着一个相当重要的位置，是整个区块链技术的基石，承载着整个区块链项目的顺利运行。


- Net 是区块链中的对等网络部分，也就是P2P（Peer-to-Peer）网络，区块链技术是一种去中心话的技术，区别于传统的中心化技术架构，其不是B/S（Brower/Server）也不是C/S（Client/Server）的模式，其各个节点之间是完全对等的，只不过对于不同的链来说，或者基于不同的共识算法，其节点所承担的角色有所差异。比如有些节点负责挖矿是矿工节点，有些节点负责是钱包轻节点spv，有些是全节点 full node （存储了完整的链数据）所以网络也是区块链中撑起整个技术架构的一个底层技术。


- Storage 存储，存储想必重要性不用讲大家也都非常清楚，但是在这里还是要啰嗦两句，讲一讲。 不管是在传统的中心化的互联网技术架构或者是传统行业的技术架构或者任何其他人类从事的活动中，存储一直都是一个非常重要的话题。 大家都知道，人脑是最早的存储“设备”，人一开始没有发明记帐工具的时候，人脑就在担任此项功能，随着人类社会的发展与进步，人们存储知识的方式与手段越来越丰富，到目前主要以电子设备为主。 当然了，在区块链的世界里，存储也是一个逃不开的话题，甚至说，在去中心化的架构当中，存储是一个新的机遇与挑战。目前比特币链上的数据还不大，也主要有几种主流的存储方式，比如内存存储啊，k-v数据库存储啊，等等。 近年来，随着区块链技术的发展以及更多数据的产生与上链，新的存储方案也已经进入了大众的视角，其中最具有代表性的就是IPFS.  

- 讲完了基础的部分，终于要进入到比特币的核心部分了，因为前面都是基础部分，也就是公共的部分。首先我们从BlockChain开始，BlockChain，嗯，是的，这就是我们整个技术架构的主角，因为BlockChain就是区块链啊。（哈哈，真是废话）在比特币白皮书描述当中，为了能够实现去中介化的目的，也就是在没有第三方参与的情况下能够解决double-pay的问题，提出了两个非常重要的概念，一个是区块链，另一个就是POW，一条链其本质就是数据结构中的一个链表，上一个Node指向下一个Node，只不过在这个链表是一个特殊的链表，其设置不能被更改，是一个只能添加的链表，并且永远都只认最长的那一条链，其他链都为假，只有最长链是真， 正如图中所示，一个链中包含有很多信息。 首先一条链是由很多个Block组成，也就是具有很多个块，对应与链表中就是一个链表由很多个节点组成。 而这每个节点中又包含很多信息，最重要的四个信息就是Timestramp（时间戳）、Hash（自己的hash值）、PrevHash（上一个块的Hash值）、Transactions（交易信息），这几个值都相当重要，非常重要，一定要牢记。 相信你也看到了Transactions我是写的复数，是的一个Block里面可能会包含很多个交易信息，这些交易信息也正是比特币能够用于支付、交易的主要原因。在比特币当中，交易主要由三个主要的信息组成：1. 输入  2.输出  3.交易信息，交易值。  Ok， 说到这里，我们先暂停下，先来了解下address，也就是地址。我们知道在传统的金融领域，也就是目前我们使用的各个银行账号啊，或者支付宝账号啊，其都是账户模型，也就是说，一个人对应一个或者多个账号，是一种实名制的方式进行的，其数据完全由第三方的中间机构所掌握，也就是银行，我们因为相信银行的信用，所以我们不怕将我们的个人信息泄露给银行，也不是特别担心银行会将我们的个人信息泄露出去。 当然，随着人们对个人隐私意思的升级，以及银行等中间机构的太强势，这种信任也出现了危机。 现在我们回到比特币上来，在比特币里面采用的是前面提到的椭圆加密的算法，其用椭圆加密算法生成了公、私钥。 也就是对应与我们银行账户的账户、密码。密码是有我们个人保管的，公钥匙公开的，水都可以查到，包括这个账号里的钱啊、转账记录啊等等任何人都可以查到， 看到这里有些人估计要心里开始打鼓了，那既然我的账号是公开的，那么难道说我去个东莞、搞个嫩模也能被查到？ 准确来说是的，但是这里有一个相当精妙的设计，那就是这个地址跟人其实是没有对应的，也就是说，别人能知道这个地址干了啥，缺不能知道这个地址是你干的。（当然你如果把自己的地址+身份公开，那你赶紧准备跪搓衣板吧） 哈哈，我知道，说完这个绝大多数人都松了一口气，好说完地址，那我们继续接着聊上面的交易，其实每一笔交易的产生都是跟地址直接相关的，也可以说交易信息里面天然涵盖了交易地址，当然在比特币交易信息里面你可能看不到，因为这背后又是密码学的东西，这里不展开，总之你知道，你的每一笔交易都会跟你的地址直接相关，而且当你发生交易的时候都会触发UTXO（比特币里面独有的交易模型）来验证是否有足够的余额以及确认交易的顺利进行，这所有的东西组在一起就生成了一条比特币链。是不是觉得相当强大？ 嗯，更强大的还在后面。

- 重点来了，前面讲到交易的时候要用到UTXO，那么到底什么是UTXO？  UTXO是比特币特有的交易模型，防止你的交易出错，验证你的账号余额等一系列的事情，都是机遇UTXO。UTXO的官方解释是：Unspent Transaction output. 怎么翻译可以自己查查字典，我觉得翻译过来比较怪，所以就直接UTXO了，那么UTXO怎么理解呢？ 前面有说在一个交易里面会有输入、输出。其实在每次交易的时候，都会去遍历整个链，去查找这个账号下的所有交易，然后将所有的输入、跟输出进行对比。如果还有剩余的输入，也就是说你还有钱没花出去，而且你发生交易的数额又正好小于或等于你剩余的支出，那么ok，你可以发生这笔交易，也就是你还有钱交易。否则，你就不能进行交易，就跟你没钱不能消费是一样的道理。当然UTXO的设计非常精美，其不同与我们日常所用的账户模型，不再是资产、负债表的那一套，是一个全新的概念，我认为相当惊艳。当然，你或许会讲，每一次交易都要重新查一遍我历史的所有交易，那岂不是很慢？ 嗯，我告诉你，当数据量很大的时候，是很慢。这一点比特币也想到了，所以他采用了chainstate 我这里叫状态机来保存账户的历史交易状态，这样就避免了每次都要遍历整个链，极大的提高了效率。

- POW 前面提到为了实现去中心化的目的，比特币提出了两个核心的概念，一个是链、另一个就是共识算法POW（proof-of-work）工作量证明算法，简单理解就是谁干的活最多，说来记账，将区块的交易信息写到链条上。POW算法，是目前最为成熟的算法，正是因为POW，造就了挖矿行业，也让我国的半导体芯片行业有了十足的进步。如果非要说我们在半导体领域里面跑在最前面的是什么，那我一定会说是矿机。

- RPC作为分布式系统之间的一种通信方式，没有什么特别要说的。 

- API层就是整个系统暴露在外面的部分，是外部与系统进行通信的窗口。

总结： 看似简简单单的一张图，讲了大半天，还感觉没讲很完整，里面还有很多内容需要阐述、需要挖掘，这也正从侧面说明了比特币整个系统的强大，看似简单的系统里面，包含了太多的东西。 需要我们慢慢去挖掘，去吃透。 架构的部分就简单讲到这里，既然这是一个系列的教程，同时代码又是会开源出来的，那么就很有必要在这里贴一下，整个项目的代码组织架构，毕竟，talk is cheap， show me the code 是我们的遵旨。