---
layout: page
mathjax: true
permalink: /2018/projects/p15/midterm/
---

## 项目进展报告

### 数据获取及预处理
  本系统是某律师网站选取用户3个月的用户访问数据（2015-2-1—2015-4-29）作为原始数据集，每个地区用户访问习惯和爱好存在差异性，本系统抽取广州的访问数据进行分析，共837450条数据，包括用户号、访问时间、来源网站、访问节目、页面标题、来源网页、标签、网友类别、关键词等属性。采用MariaDB开源数据库，读取7law.sql文件 ，
  因为数据比较大需要进行分块处理，然后进行数据清洗。在这个数据库中，中间页面的网址、咨询发布成功页面、律师登录助手等页面与分析数据无关，从而进行数据清洗。因为用户访问知识有翻页的情况，翻过页的网站都属于同一类型的网页，直接删除会失去大量有用数据，所以要对这些网页进行识别并还原，最后对每个用户进行去重操作。因为有些网页所属的类别是错误的，需要对数据网站进行分类，因为类别主要分为咨询类别和知识类别，基本可以把带有“ask””askzt”分为咨询类别，“zhishi””faguizt”分为知识类别。
   由于是推荐系统对输入数据的需要，可以对处理后的数据进行属性规约，提取所需要的属性，本数据需要的数据属性是用户和用户访问的网页


### 数据分析与可视化

1、我们针对原始数据中用户点击的网络类型进行统计，网页类型指的是“网页类型”字段中的前三个数字，用分块读取数据文件的思想进行处理，得到的结果保存在cate.csv中，其中第一列代表网页类型，第二列代表记录数。并用饼图进行可视化分析，图文件为type.png：
从中发现点击与资讯相关（101）的记录占了49.71% ，其次是其他的类型（199）占比21.05%  ，然后是知识相关（107）占比24.64%。   
 
 
  因此可以得到用户点击的页面类型的排行榜为：咨询相关、知识相关、其他方面的网页、法规（类型为301）等。
2、对咨询类别内部进行统计分析，结果保存于101counts.csv文件中，用饼图进行可视化分析，结果保存于101counts.png文件中。
 
 
从图表可以看出，浏览咨询内容页（101003）记录最多，其次是咨询列表（101002）和咨询首页（101001）。初步得出结论，用户都喜欢通过浏览问题的方式找到自己需要的信息，而不是以提问的方式或者查看长篇知识的方式得到所需信息。
3、统计分析知识类型内部的点击情况，因为知识类型中只有一种类型（107001），所以利用网址对其进行分类，获得知识内容页以及知识首页和知识列表页的分布情况，结果保存于counts107.csv文件中，其中第二列代表记录数。

知识内容页	323
知识列表页	11
知识首页	9
   
从表中可以看出，访问知识内容页的记录最多，其次是知识列表页，访问记录最少的是知识首页。

4、分析其他（199）页面的情况，利用网址对其进行分类，获得访问地区和律师页、带？页面、律师事务所页面、咨询主页的记录条数，，结果保存于counts199.csv文件中，其中第二列代表记录数。


地区和律师	69
带？号	65
律师事务所	1
资讯主页	78

从中可以看出，访问咨询主页的记录最多，其次是地区和律师，再者是网址中带？的记录。
5、统计分析原始数据用户浏览网页次数（以“真实IP”区分）的情况，其结果保存于onclick_count.csv中
 
由表可以看出，浏览一次的用户最多，大部分用户浏览的次数在2-8次。

### 模型选取

本系统为推荐程序，存在长尾网页丰富，用户个性化需求强烈、推荐结果的实时变化，原始数据中网页数明显小于用户数，故而采用基于物品的协同过滤推荐系统对用户进行个性化推荐，以推荐系统结果作为系统结果的重要部分。
它的主要过程是：分析用户和物品的数据集，通过对项目浏览喜好找到相似的物品，然后根据用户历史喜好，推荐相似的项目贵目标用户。
    
这就
1.	需要计算物品直接的相似度。这个本系统采用jaccard相似系统法计算，这是因为用户行为是0-1型。
2.	根据相似度和用户历史行为为用户生成推荐列表。在计算之间的相似度后会产生相似度矩阵利用P=SIM*R度量，其中P为用户的感兴趣程度，R为用户对物品的兴趣（0-1），SIM表示所有物品之间的相似度
  评测模型的方法采取随机打乱数据的方法：随机函数打乱原始数据，然后将用户行为数据集按照均匀反驳分成M份，1份为测试集，M-1份为训练集，在训练集上建立模型，在测试集进行评测。原因：随机可以保证系统的随机性得到稳定可靠的结果

### 挖掘实验的结果

  目前做完了数据清洗、数据变换、数据分类。
  数据清洗：即从探索分析的过程中发现与目标无关的数据，归纳总结其数据的规则：中间页面的网址、咨询发布成功页面、律师登录助手页面等。将其整理成删除数据的规则，其清洗结果保存于数据库中的cleaned_gzdata表中。
  数据变换：由于用户访问知识的过程中，存在翻页情况，不同的网址属于同一类型的网页，针对这些网页我们采取还原其原始类别的方式，处理方式为首先识别翻页的网址，然后对翻页的网址进行还原，最后针对每个用户访问的页面进行去重操作，其数据变换结果保存于数据库中的changed_gzdata表中。
  数据分类：由于在探索阶段发现有部分网页的所属类别是错误的，需要对其数据进行网址分类，且分类结果是分析咨询类别和知识类别，因此需要对这些网址进行手动分类，人为制定分类规则，分类的的结果保存于数据库中的splited_gzdata表中。
  协同过滤的结果由于实验还没有完成，现在无法展示，将在最终报告中进行展示。

### 存在的问题

选用的法律网站的数据集过小，用协同过滤算法推荐的结果不够理想。

### 下一步工作

   1、属性规约：由于该推荐系统的模型需要的数据属性为用户和用户访问的网页，所以需要进行属性规约。
   2、基于协同过滤算法对用户进行推荐：首先需要计算物品之间的相似度，然后根据物品的相似度和用户的历史行为给用户生成推荐列表。
   