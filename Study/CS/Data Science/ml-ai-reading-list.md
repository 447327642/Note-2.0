# 机器学习与人工智能书籍资源导引

比较老了，部分书籍需要与时俱进

这里将最近有关机器学习和人工智能相关的一些学习资源归一个类：

首先是两个非常棒的 Wikipedia 条目，我也算是 wikipedia 的重度用户了，学习一门东西的时候常常发现是始于 wikipedia 中间经过若干次 google ，然后止于某一本或几本著作。

第一个是[人工智能的历史（History of Artificial Intelligence）](https://zh.wikipedia.org/zh-cn/%E4%BA%BA%E5%B7%A5%E6%99%BA%E8%83%BD%E5%8F%B2)，我在讨论组上写道：

而今天看到的这篇文章是我在 wikipedia 浏览至今觉得最好的。文章名为《人工智能的历史》，顺着 AI 发展时间线娓娓道来，中间穿插无数牛人故事，且一波三折大气磅礴，可谓"事实比想象更令人惊讶"。人工智能始于哲学思辨，中间经历了一个没有心理学（尤其是认知神经科学的）的帮助的阶段，仅通过牛人对人类思维的外在表现的归纳、内省，以及数学工具进行探索，其间最令人激动的是 Herbert Simon （决策理论之父，诺奖，跨领域牛人）写的一个自动证明机，证明了罗素的数学原理中的二十几个定理，其中有一个定理比原书中的还要优雅，Simon 的程序用的是启发式搜索，因为公理系统中的证明可以简化为从条件到结论的树状搜索（但由于组合爆炸，所以必须使用启发式剪枝）。后来 Simon 又写了 GPS （General Problem Solver），据说能解决一些能良好形式化的问题，如汉诺塔。但说到底 Simon 的研究毕竟只触及了人类思维的一个很小很小的方面 —— Formal Logic，甚至更狭义一点 Deductive Reasoning （即不包含 Inductive Reasoning , Transductive Reasoning (俗称 analogic thinking）。还有诸多比如 Common Sense、Vision、尤其是最为复杂的 Language 、Consciousness 都还谜团未解。还有一个比较有趣的就是有人认为 AI 问题必须要以一个物理的 Body 为支撑，一个能够感受这个世界的物理规则的身体本身就是一个强大的信息来源，基于这个信息来源，人类能够自身与时俱进地总结所谓的 Common-Sense Knowledge （这个就是所谓的 Emboddied Mind 理论。 ），否则像一些老兄直接手动构建 Common-Sense Knowledge Base ，就很傻很天真了，须知人根据感知系统从自然界获取知识是一个动态的自动更新的系统，而手动构建常识库则无异于古老的 Expert System 的做法。当然，以上只总结了很小一部分我个人觉得比较有趣或新颖的，每个人看到的有趣的地方不一样，比如里面相当详细地介绍了神经网络理论的兴衰。所以我强烈建议你看自己一遍，别忘了里面链接到其他地方的链接。

### 书籍列表

+ Programming Collective Intelligence
    + Toby Segaran
    + 近年出的入门好书，培养兴趣是最重要的一环，一上来看大部头很容易被吓走的
+ Machine Learning
    + Tome M. Mitchell
    + 老书，牛人。现在看来内容并不算深，很多章节有点到为止的感觉，但是很适合新手（当然，不能"新"到连算法和概率都不知道）入门。比如决策树部分就很精彩，并且这几年没有特别大的进展，所以并不过时。另外，这本书算是对97年前数十年机器学习工作的大综述，参考文献列表极有价值。
+ Artificial Intelligence
    + Stuart Russel
    + 无争议的领域经典
+ Pattern Recognition And Machine Learning
    + Christopher M.Bishop
    + 经典中的经典。Pattern Classification 和这本书是两本必读之书。《Pattern Recognition and Machine Learning》是很新（07年），深入浅出，手不释卷。
+ Information Theory, Inference and Learning Algorithms
    + David J. C. MacKay
    + 第四部分：“概率与推理”写得太赞了，简洁而到位，而且既有直观解释又有例子支撑（不信看看28章）。典范啊典范！其余部分还没看，暂不作评价。还有对蒙特卡罗系列方法的介绍是我目前看到的最好的。
+ Data Mining
    + Jiawei Han
    + 华裔科学家写的书，相当深入浅出。
+ Foundations of Statistical Natural Language Processing
    + Christopher D. Manning
    + 自然语言处理领域经典。
+ Modern Information Retrieval
    + Ricardo Baeza-Yates
    + 老书，牛人。貌似第一本完整讲述IR的书。可惜IR这些年进展迅猛，这本书略有些过时了。翻翻做参考还是不错的。
+ Introduction to Information Retrieval
    + Christopher D. Manning
    + 信息检索方面的书现在建议看Stanford的那本《Introduction to Information Retrieval》，这书刚刚正式出版，内容当然up to date。另外信息检索第一大牛Croft老爷也正在写教科书，应该很快就要面世了。据说是非常pratical的一本书。
+ Managing Gigabytes
    + Ian H. Witten
    + 信息检索好书。
+ All of Statistics
    + Larry Wasserman
    + 机器学习这个方向，统计学也一样非常重要。推荐All of statistics，这是CMU的一本很简洁的教科书，注重概念，简化计算，简化与Machine Learning无关的概念和统计内容，可以说是很好的快速入门材料。
+ The Elements of Statistical Learning
    + T. Hastie
    + 统计学习的经典教材
+ 概率论及其应用
    + 威廉·费勒
    + 概率论基础参考书
+ Bounded Rationality
    + Gerd Gigerenzer
    + 这本和《简捷启发法让我们更精明》是姐妹书，具体介绍参见：http://www.douban.com/review/1497779/
+ 简捷启发式
    + 哥德·吉戈伦尔
+ The Cambridge Handbook of Thinking and Reasoning
    + Keith J. Holyoak
    + 好书，盲荐
+ Neural Networks for Pattern Recognition
    + Peter Norvig [推荐的书](http://www.amazon.com/review/RZ7FBFHHLJHYE/ref=cm_cr_rdp_perm)
+ 统计学习理论的本质
    + Vladimir N. Vapnik
+ Statistical Learning Theory
    + Vladimir N. Vapnik
+ Statistical Decision Theory and Bayesian Analysis
    + James O. Berger
    + 这本书有牛人推荐的。nlpers.blogspot.com
