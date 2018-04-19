title: 三像素问题
author: gsal
tags:
  - 前端
  - css
categories:
  - 前端
date: 2018-03-28 20:52:00
---
img直接放在div中时，会比div的下边缘高出3个像素，因为img属于行内元素，因此其不是与下边缘对齐而是与基线对齐的。
1.img标签增加vertical-align:top
``` css
img {
      vertical-align:top;
}
```
2.img标签的父标签增加font-size:0;
``` css
body {
         font-size:0;
}
```
3.img标签增加display:block;
``` css
img {
    display:block;
}
```

