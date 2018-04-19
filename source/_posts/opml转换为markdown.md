title: opml转换为markdown
author: gsal
tags: []
categories:
  - 其他
date: 2018-04-01 08:10:00
---
尝试过pandoc，但是pandoc将所有的opml内容都转换为markdown中的标题，非常难受。最后找到了这个[https://github.com/azu/opml-to-markdown](ttps://github.com/azu/opml-to-markdown)
简单又好用。这样就能够满足我想要在幕布上写大纲，但是又想发布在博客上的需求了。  
命令：opml-to-markdown -e subring.opml -o subring.md
