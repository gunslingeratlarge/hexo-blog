title: python中使用wordcloud
author: gsal
tags:
  - python
  - 机器学习
  - ''
categories:
  - 机器学习
date: 2018-06-27 22:44:00
---
## 在python中使用wordcloud
@(Machine Learning By Andrew Ng)[机器学习,python]
### 安装
在http://www.lfd.uci.edu/~gohlke/pythonlibs/#wordcloud 下载wordcloud的whl文件，放到任意文件夹，在anaconda prompt中运行`pip install whl文件名`即可。
<!--more-->
###使用
使用非常简单，这里仅仅给出一段示例代码，不做过多解释。　　　
``` python
from wordcloud import WordCloud ,STOPWORDS
stopword=set(STOPWORDS)

subset=train[train.toxic==True]
text=subset.comment_text.values
wc= WordCloud(background_color="black",stopwords=stopword,width=1000,height=600,min_font_size=20,max_words=200,max_font_size=80)
wc.generate(" ".join(text))
plt.figure(figsize=(400,200))
plt.axis("off")
plt.title("Words frequented in Clean Comments", fontsize=20)
plt.imshow(wc)
plt.show()
```

### 常见问题 
1. 生成的词云分辨率低，模糊？
``` python
cloud=WordCloud(width=1000,height=600,min_font_size=20,max_words=200,max_font_size=80)
```
绘制词云的时候把图片的宽和高加大一点，然后字体也加大一点