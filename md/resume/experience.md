[TOC]

### 自我介绍

---

我叫... 目前在香港科技大学读IT专业，本科是在浙江大学修读了信息工程专业。 

之所以申请这个岗位，是因为本科学过一些nlp相关的课程，大四的时候也做了一段时间和自然语言处理相关的实习让我对自然语言处理产生了兴趣，自己平时也会到网上上网课以及medium等论坛上看到有趣的跟着实践一下。



#### 金融投资顾问平台

面向内部投资部用户

项目分工 / 项目中遇到的问题 / 项目的亮点 / 项目的不足 / 开发时间

---

主要涉及到 平台 和 爬虫

##### 平台

* 基于django + vue 前后端分离的框架
* 功能模块
  * 搜索功能 - 搜索学者/公司/新闻
  * 公司相关
    * 新获投的公司 -- 爬自稀牛网站
      * 公司全称才是unique的id，爬取的新获投公司只有简称，无法支持跳转。 根据简称与库中已有的简称匹配对应的全称。 针对没有匹配到的，用爬虫去搜索，然后更新入库
    * 微博热搜词 / 网页热搜词
      * 数据来源
    * 热门榜单
    * 正负面新闻
      * 目标公司相关的新闻状态- 正负面
  * 学术相关
    * 学术领域
      * 各个领域 -- 下分各个子领域
    * 中科院学者赛道 -- 不同领域 对应不同的 学者们
    * 学者创业 
    * 高校创业 / 学者主持的项目
  * 新闻版块
    * 新闻多种类型-按照赛道，科技前沿，投资机构，人 等等分成很多
    * 每个新闻
      * 正负面
      * 事件类型
      * 实体
      * 赛道
  * 登陆功能模块

* 难点
  * 公司一个全称 对应很多简称
  * 学者数据的来源， 怎么消歧



##### 爬虫

新闻网站 / xiniu网站 /  微博投资人 / 

scrapy / request / 

爬虫的管理: 定时脚本(稀牛) + 实时的服务(微博，新闻)

一开始写脚本从mysql同步到es中，后来引入实时处理平台， 爬取的数据进入消息队列(kafka)，然后起服务去消耗，一方面入mysql,另一方面入es。

可能存在的问题？ 引入消息队列后是否有数据丢失的问题

##### 后端

* 全站的搜索 
* pdf的搜索
* 用户注册/鉴权
* 相似的文章推荐？ LSH
* docker 部署服务
  * 为了可移植性较高得部署服务





### NER 命名体识别

---

* 为了 电子病历的自动化处理，将其转化为结构化的数据，自动识别出 病历报告中所涉及的“病种”，疾病出现的位置，等等 specific

* 方法：

* 1. CRF 条件随机场
  2. 双向LSTM -- Bi-LSTM
  3. 两者相结合

- 步骤

- - 依靠人工标注一部分数据，一个是出现的部位，类似于LOC标签，还有一个是疾病的名称。

  - 然后做一个label的映射

  - - B-loc, I-loc , B-des,        I-des,O 五类





### TED 笑话检测

---



目的-建立一个判断演讲台词是否能够引起观众笑声; 

数据-正负样本各5000个(剔除了长度过少和过多的)

方法:

* NB - SVM的做法

* TFIDF 抽取特征 加 NB

* TFIDF + Trucated SVD 后加SVM

* TFIDF 用词向量替换 
  * 自己用三个词向量分别train了一遍，发现哪个好， 怎么解释
  * 然后用三个预训练的词向量做了一遍，哪个好，怎么解释
  * 动态的直接用了ELMO的预训练好的
* Sentence 的代表方式
  * average /  tf-idf作为weight   /SIF
* 预处理的trick 讲一些
* DL 的方法
  * CNN / RNN / Attention / Self- attention

---

* 目的： 分析TED演讲中成功引起观众笑的原因？

* 数据：从TED官网爬取了大约6000个TED演讲视频的英文字幕，字幕弄成json格式，有(laughter)的标签

* - 正样本：laughter的上一句 或者 laughter所在句(laughter)所在位置的不同，有一些是单独一行，有一些是在句子里
  - 负样本：为了防止句意的大幅度转移造成的误差，就是为了尽可能让正样本和负样本描述的是同一类事物，在正样本附近取任意一句其他的为负样本

* 方法分为传统ML,和DL

  * 传统的就是 tfidf 加分类器
    * Naive Bayes
    * 用SVM前先用truncatedSVD 降维
      * 可能会问 PCA，和PCA的区别，和一般SVD区别
  * 词向量
    * 静态词向量用了word2vec,glove2vec,fasttext,哪个效果好 为什么？
      * 用word2vec 和 glove 训练词向量
        * 用后感觉是 小数据量 情况下 glove相对效果会好一点，因为glove还有共现矩阵
        * 两者效果理论上应该差别不大 只不过glove的并行化做得好，大语料的时候 速度快一点
        * 两种都是context base 只不过word2vec是基于 predictive ， 而 glove则是 context-counting
    * 动态的用了 ELMO
    * sentence embedding的表示
      * 直接average
      * 以每个词的tf-idf为权重，对所有词的word vector加权平均
      * smooth inverse frequency (简称SIF)为权重，对所有词的word vector加权平均，最后从中减掉principal component，
  * 处理的tricks：
    * tokenize的方式来增加coverage，通过kaggle上一个处理词的方式来增加miss 减少miss spell的概率
    * 尝试了NB - SVM strong baseline

  * 拓展，如果在无标注的数据上计算相似度
    * 先用unsupervised learning 对句子集合做简单归类和标注
    * 利用上一步得到的标注数据，训练分类模型
    * 评估模型效果，适当引入新的标注数据
  * DL - deep learning 的方法
    * CNN
    * RNN-Attention
    * RNN self attention

* 总结: 

  - - 从这次经历中，我了解到模型的好坏与最终的结果差距不是那么的大，**预处理的一些过程**值得付出一些时间
    - 语料清洗的好坏是成功的前提，词向量的覆盖率也是关键
    - 不同语料预训练的词向量效果可能也有差异，用wiki预训练的和用tweet预训练的可能就不一样

* 参考文章
  * Predicting Audience’s Laughter Using Convolutional Neural Network
    * ![image-20190311140006520](/Users/K/Library/Application Support/typora-user-images/image-20190311140006520.png)

### 文章推荐系统

#### 文章相似度的计算

---

* 文章的表示

* 用LSI来得到每篇文章的降维后的向量

  * LSI能在线更新，新文章进来可以直接加进库里计算，但若出现新词，不能够更新当前的词库。坏处是不知道topic是什么。
  * LSI的好处不是快，因为SVD分解很慢

* 用LDA来得到每篇文章的降维后的向量

  * 也可以在线更新,同样无法添加新词,(因为新文章在被送进update前，会先用已有的dictionary class把词先映射成index)，好处是知道topic是什么。

* 用NMF(非负矩阵分解) gensim 也有接口

  * 可以用topic coherence 来衡量

  * topic coherence需要得到的每个topic对应的词的分布,如有K个topic的话，需要每个topic对应的比如前10个词，算K个topic两两之间词的相似度，最后取平均 (用word2vec来得到词向量来求相似度)

  * K越大的话,topic coherence貌似会越大吧，找拐点？？  [github NMF找合适component](https://github.com/derekgreene/topic-model-tutorial)

    

* 用LSI和LDA怎么调参

  * LDA的话可以把数据集分成训练，验证，测试，用训练，验证来调topic数目，用测试来测结果。
    * 指标有perplexity， Coherence (如何解释物理意义)
  * LSI怎么调
    * LSI没有topic关于词的分布，虽然SVD分解后有三个矩阵，但是左边那个word-topic矩阵中有负值，很难用概率来解释其物理含义

---

* document * words 矩阵

* 借助gensim的LSI计算topic model 然后算相似度

* - 尝试了LSI 和 LDA
    - 用LSI前，用TF-IDF先转换文本
    - LSI:的原理是矩阵分解-直接将文章向量降到某一维然后计算相似度
      - SVD的话 三个矩阵之间的关系 ； 计算相似度除了说用余弦相似度还能用什么？
      - LSI的坏处在于不能直接看出来对应的topic是什么
      - LSI的增量模型- LSI可以通过add_documents来增加文本，但不知道具体如何操作，add后是重新做一次 SVD吗？
        - 会更新模型
    - LDA:狄利克雷- 可以输出指定的数目的topic，每一个topic是由文章中的词按照一定权重组成
      - LDA的原理
      - LDA的增量-有update的接口
  - LSI和LDA的参数如何调整，topic的数目选择有何依据
    - 对于LSI
      * 降的维度的大小如何选择
    - 对于LDA
      - 小数据量有pylda 那种接口可以可以可视化每个topic的分布
      - 还有coherence指标
        - 该指标衡量的是每个topic之间的差异吗？
        - 有多个选项

* 另外一个思路：
  * 不用主题模型
  * 检测出文档中的 实体，人名、地名、组织名 和其他的，和一些其他重要的词将组成关键词，对**关键词进行排名**，计算每个关键词的权重，从而生成关键词向量
    * 如果物品是电影，可以根据演员在剧中的重要程度赋予他们权重

* 处于内存优化，gensim支持流处理，即一开始不把所有的数据都load导内存里，写一个函数，每读一行数据，yield <https://www.cnblogs.com/iloveai/p/gensim_tutorial.html>



* gensim 接口说明

  * gensim LSI 的add_documents只能增加文本，文本中出现的**新词不会被加到模型的字典**里

  * lsi可以对**新的文章**计算一个向量,用add_documents把文章添加进corpus，LSI model 会更新

    > ```
    > model.add_documents(another_tfidf_corpus) # now LSI has been trained on tfidf_corpus + another_tfidf_corpus
    > ```



#### 文章相似度计算思路

---

##### 1.计算相似度的指标

* 欧几里得距离  / 曼哈顿距离 
* 明科夫斯基距离
  * ![image-20190313115344885](/Users/K/Library/Application Support/typora-user-images/image-20190313115344885.png)
  * p = 1 - 曼哈顿距离
  * p = 无穷 ， 切比雪夫距离
* Jaccard 关心个体间共同具有的特征是否一致
  * ![image-20190313115512537](/Users/K/Library/Application Support/typora-user-images/image-20190313115512537.png)
  * Jaccard相似度与cosine相似度区别？
    * Jaccard是集合操作，句子向量长度由两个句子中unique的词汇数目决定，而cosine相似度使用的向量大小由词向量的维度决定。
    * 业务场景包含很多重复的词
      * 若重复对想做的任务没有关系，计算文本相似度可以用Jaccard
      * 重复对结果影响大，用cosine
* 皮尔森相关系数



* 余弦相似度 / 调整余弦相似度
  * 由于余弦相似度对数值不敏感，如个体X、Y对两个条目的评分分别为(1,2)、(4,5)，其余弦相似度为0.98，调整后需要减去各自的均值，计算相似度为-0.8.通过求出每位用户的平均打分，调整评分向量为评分偏差向量，再求解余弦相似度。



* 大规模数据的相似度计算

  * 两个用户之间相似度的计算随着物品维度的增加而增加
  * 计算每一个用户和其他所有用户之间的相似度的复杂度随着用户规模的增长，呈平方增长
  * 使用近似算法，牺牲一些精度来大大提高计算效率

* Min-hashing

  * 思路是把一个高维向量通过哈希函数投影到低维空间中
  * https://zhuanlan.zhihu.com/p/46164294
  * 目前只能对 每个位置取值为0，1这种 ，即向量 [0,1,1,1,0,0,0]
  * 如果是小数的话，可能需要乘上一个大数把小数都放大，放大后的小数部分是否保留就用随机的方法，随机生成一个小数，比这个小数大的话就保留

* LSH - Locality sensitive hashing

  * Min Hashing算法解决了前面所说的计算复杂度的第一个方面：它通过**将向量A、B映射到低维空间中的两个签名向量，并且近似保持A、B之间的相似度**
  * 当用户数目较大时（例如用户数 ![N>10^6](https://www.zhihu.com/equation?tex=N%3E10%5E6) ），计算两两用户之间相似度就需要 ![C_N^2](https://www.zhihu.com/equation?tex=C_N%5E2) 次计算，
  * 能先粗略地将用户分桶，将可能相似的用户以较大概率分到同一个桶内，这样每一个用户的“备选相似用户集”就会相对较小，降低寻找其相似用户的计算复杂度，
  * LSH的做法就是对各个用户的signature向量在每一个band上分别进行哈希分桶，在任意一个band上被分到同一个桶内的用户就互为candidate相似用户，这样只需要计算所有candidate用户的相似度就可以找到每个用户的相似用户群了。

  

#### 用户相似度的计算

---

* 如果有用户看过哪些文章的数据，可以将每个用户映射成一个 稀疏向量，向量对应位置的值代表用户是否有看过这个文章，然后用Min - hashing , LSH  来做这个
* user profile similarity
  * weighted sum 人为给觉得重要的特征加权重



#### 冷启动问题的处理

---

- 制造选项，让用户选择自己感兴趣的点后，即时生成粗粒度的推荐

  - 如可以让用户选择想要关注的方面是育婴，还是养生还是等等
  - 利用注册时的信息，如是男是女等，是老是年轻等，做一些推荐

  - 人口统计学信息。包括用户的年龄、性别、居住地、职业和学历等信息。

  - 用户的兴趣描述。有一些网站会让用户用文字描述他们的兴趣。

  - 从其他网站导入的用户站外行为数据。      比如用户通过豆瓣、新浪微博的账号登录，就可以在得到用户同意的情况下获取用户在豆瓣或者新浪微博的一些行为数据和社交网络数据

- 对于新的文章的冷启动

  - 可以推荐给喜欢过类似的物品的用户




* 用户冷启动 - 新进用户

  * 根据注册信息，搜索关键词等信息来推测用户的兴趣主题分布，进而找到和该用户类似兴趣主题的其他用户，通过他们的历史行为来预测该用户的。
  * 每个用户看作主题中的文档，用户特征对应文档中的单词

* 物品冷启动 - 新上市的商品或电影

  * 从电影的导演，演员，关键词，类别等推断该电影的主题

* 系统冷启动 - 为**一个新开发的网站**设计个性化推荐系统

  * 需要得到每个用户和每部电影对应的主题向量，还需要知道用户主题和电影主题之间的**偏好程度**，也就是哪些主题的用户可能喜欢哪些主题的电影。
  * 当系统中没有任何数据时，我们需要一些先验知识来指定，并且由于主**题的数目通常比较小**，随着系统的上线，收集到少量的数据之后我们就可以对主题之间的偏好程度得到一个比较准确的估计。

  





* 最后衡量效果好坏的指标
  * 用户点击
  * 用户停留时间
  * 用户举报



### 医学同义词的归一化

---

医学术语的同义词的一个词典建设。每一次会指定一批标准词，然后去扩充。

* 收集了1000万左右的病历样本，然后把这些文本数据处理成gensim,fasttext,glove对应要的格式，然后训练。



- gensim的训练方式

  - 没有去停用词，word2vec的算法依赖于上下文，而上下文有可能就是停词
  - 选择了skip-gram，没用cbow，虽然cbow快

    - **skip-gram慢，但对罕见词有利**

- - 词向量大小
  - hs - 用hierachical       softmax
  - negative  负样本的样本设小一点,快一点

- glove

  - 小数据集上表现可能相对更好
  - 基于全局语料，结合上下文语境构建词向量
  - 结合了LSA和word2vec的优点
  - **每行一个句子或一段话，注意要分好词**。
  - load时可以借助gensim接口

- fasttext

  - asttext的表现没有预计的好
  - sub-words的trick       针对英文这种       ”english-born”和”china-born”，从单词层面上看，是两个不同的单词，但是如果用char-level的ngram来表示，都有相同的后缀”born”。因此这种表示方法可以学习到当两个词有相同的词缀时，其语义也具有一定的相似性。
  - 但是针对中文不行，中文原则和原来不一样，根据偏旁更有效可能



* 结论：
  * 在训练完模型后，是用标准词到每一种词向量中去算前K个近似的词，然后人工筛选
    * gensim的话用了skip-gram，因为对罕见词可能会好一点
    * 然后fasttext找到的词相对也更泛一点，结果很多意思比较杂，后来分析英文效果好可能是形态学上英文差几个char意思可能接近，而中文的话可能只是差一个字意思就差很多了。



* 分词上用的结巴分词

  * 几个模式的区别

    * 精确模式

      * 将句子精确切开，默认精确

    * 全模式

      * 将句子中所有可以成词的词语都扫描出来

    * 搜索引擎模式

      * 在精确模式基础上，对长词再次切分

\#精确模式: 我/ 去过/ 清华大学/ 和/ 北京大学/ 。

\#全模式: 我/ 去过/ 清华/ 清华大学/ 华大/ 大学/ 和/ 北京/ 北京大学/ 大学/ /

\#搜索引擎模式: 我/ 去过/ 清华/ 华大/ 大学/ 清华大学/ 和/ 北京/ 大学/ 北京大学/ 。

\#精确模式/全模式-新词发现: 他/ 来到/ 了/ 网易/ 杭研/ 大厦

\#搜索引擎模式-新词发现: 他/ 来到/ 了/ 网易/ 杭研/ 大厦



### 基于GAN的文本生成

![image-20190310154131557](/Users/K/Library/Application Support/typora-user-images/image-20190310154131557.png)

![image-20190310154702912](/Users/K/Library/Application Support/typora-user-images/image-20190310154702912.png)





###  用户职业标签检测

* 多分类问题- 4分类问题 (房产中介，医生，老师，其他)
  * 由于要预测的label(职业)之间不存在交叉关系，直接用一个model 预测多个类别
* 最后指标-多分类问题的指标
  * 对应看了每个类的precision,recall,F1
  * Macro F1:
    * n分类的评价拆成n个二分类的评价，计算每个二分类的F1 score，n个F1 score的平均值
  * Micro F1:
    * 将n分类的评价拆成n个二分类的评价，将n个二分类评价的TP、FP、RN对应相加，计算评价准确率和召回率，由这2个准确率和召回率计算的F1 score
* 数据增强
  * 房产中介的数据不足，通过爬虫爬取链家，再到数据库去比对，抽出对应的样本





### 基于机器学习模型的海外专利是审查周期影响因素选择

刘夏, 黄灿,余骁锋.  基于机器学习模型的专利质量预测初探 收录于情报学报-中文一级刊物

**如何加速快海外专利授权？基于大数据的变量选择与测度**

####  目的：

 	探讨**影响海外专利是否授权和是否快速授权**的因素

#### 数据:

​	基于国家知识产权局受理并授权的25362个专利，其海外同族专利的授权情况及相关信息

 	35个变量 - 预测 海外专利授权(Grant or not)， 以及快速授权(Fast grant or not)

​	特征有

​		专利个体层面的质量、复杂度，申请人层面的申请经验，

​		技术产业层面的技术发展周期，以及不同区域的区域特征

#### 经管文章原来的特征选择方法

* 显著性检验
  * 即通过T-test, Chi Square等**统计指标**判断该变量在处理组和对照组是否存在显著的差异。该方法能够有效**判断单独变量对于经济假设的解释**能力，但是变量之间存在的**共线性**会造成统计检验的结果失去可靠性（+文献)。
* 通过向前 (Forward Selection)以及向后 (Backward Elimination) 的逐步回归法进行变量筛选

#### 好处

​	因为机器学习模型**不需要基于经济理论给予预先假设**，**完全由数据驱动**，以预测准确度为目标挖掘变量间的相关性

​	用到了 LASSO和随机森林

如何检测变量的多重共线性



### 深度学习在NLP中的困境

![image-20190324150816099](/Users/K/Library/Application Support/typora-user-images/image-20190324150816099.png)

缺乏带标注的数据

解决方式：

* 无监督预训练
  * 句子 - ELMo, GPT, BERT  ,skip-thought, paragraph vector
  * Word2Vec,GLOVE,FastText
  * 
* 多任务学习
  * ![image-20190324151721294](/Users/K/Library/Application Support/typora-user-images/image-20190324151721294.png)
  * 在学习不同的任务过程中使用共享表示，可以使在某个任务中学习到的内容可以帮助其他任务学习的更好。
  * 优点
    * **隐式的数据增强：**一个任务的数据量相对较少，而实现多个任务时数据量就得到了扩充，隐含的做了一个数据共享
    * **更好的表示学习**
    * **正则化：**共享参数在一定程度上弱化了网络能力，防止过拟合。
* 迁移学习



### 想要问的问题

* 组里之后技术交流 研讨会 读论文

  * 平时有没有一些前言技术或者论文的分享之类的

* 刚才面试表现怎么样 有什么意见 我哪里需要再加强的

  





### 最骄傲的事

大二暑假小学期的时候，有上那种电路的课，就是电路图差不多都有了，用软件画好，按照那个搭好就完课了，还有就是学院新尝试的，买来无人机的飞控板  飞思卡尔K80吧，差不多两周多的时间，从什么都不会，到上论坛查各种资料，然后让无人机飞起来做一些基本的运动，之后也因为这个经历 去给来学校参观的小朋友讲解无人机基本原理。

### 最艰难的事








