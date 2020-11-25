# 赛题理解

赛题理解是切入一道赛题的基础，会影响后续特征工程和模型构建等各种工作，也影响着后续发展工作的方向，正确了解赛题背后的思想以及赛题业务逻辑的清晰，有利于花费更少时间构建更为有效的特征模型， 在各种比赛中， 赛题理解都是极其重要且必须走好的第一步， 今天我们就从赛题的理解出发， 首先了解一下这次赛题的概况和数据，从中分析赛题以及大致的处理方式， 其次我们了解模型评测的指标，最后对赛题的理解整理一些经验。

## 赛题简介

此次比赛是新闻推荐场景下的用户行为预测挑战赛， 该赛题是以新闻APP中的新闻推荐为背景， 目的是**要求我们根据用户历史浏览点击新闻文章的数据信息预测用户未来的点击行为， 即用户的最后一次点击的新闻文章**， 这道赛题的设计初衷是引导大家了解推荐系统中的一些业务背景， 解决实际问题。

### 数据表

`train_click_log.csv`：训练集用户点击日志

`testA_click_log.csv`：测试集用户点击日志

`articles.csv`：新闻文章信息数据表

`articles_emb.csv`：新闻文章embedding向量表示

`sample_submit.csv`：提交样例文件

### 字段表

|       **Field**       |         **Description**          |
| :-------------------: | :------------------------------: |
|        user_id        |              用户id              |
|   click_article_id    |            点击文章id            |
|    click_timestamp    |            点击时间戳            |
|   click_environment   |             点击环境             |
|   click_deviceGroup   |            点击设备组            |
|       click_os        |           点击操作系统           |
|     click_country     |             点击城市             |
|     click_region      |             点击地区             |
|  click_referrer_type  |           点击来源类型           |
|      article_id       | 文章id，与click_article_id相对应 |
|      category_id      |            文章类型id            |
|     created_at_ts     |          文章创建时间戳          |
|      words_count      |             文章字数             |
| emb_1,emb_2,…,emb_249 |      文章embedding向量表示       |

## 结果提交

提交前请确保预测结果的格式与sample_submit.csv中的格式一致，以及提交文件后缀名为csv。其格式如下：

```
user_id,article_1,article_2,article_3,article_4,article_5
```

其中`user_id`为用户`id`, `article_1,article_2,article_3,article_4,article_5`为预测用户点击新闻文章Top5的`article_id`依概率从高到低排序，例如：

```
user_id,article_1,article_2,article_3,article_4  ,article_5

   200000,1,2,3,4,5
   
   200001,1,2,3,4,5
   
   200002,1,2,3,4,5
   
   200003,1,2,3,4,5
```

## 评分方式

`MRR(Mean Reciprocal Rank)`：首先对选手提交的表格中的每个用户计算用户得分

$score(user)=\sum_{k=1}^5\frac{s(user,k)}{k}$

其中, 如果选手对该`user`的预测结果`predict k`命中该`user`的最后一条购买数据则`s(user,k)=1`; 否则`s(user,k)=0`　。而选手得分为所有这些`score(user)`的平均值。

假如article1就是真实的用户点击文章，也就是article1命中， 则s(user1,1)=1, s(user1,2-4)都是0， 如果article2是用户点击的文章， 则s(user,2)=1/2,s(user,1,3,4,5)都是0。也就是score(user)=命中第几条的倒数。如果都没中， 则score(user1)=0。 这个是合理的， 因为我们希望的就是命中的结果尽量靠前， 而此时分数正好比较高。