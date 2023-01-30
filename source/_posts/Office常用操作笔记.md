---
title: Office常用操作笔记
date: 2019-07-06 14:19:23
tags: office
categories: Office
---

# Word设置自动推导的标题列表

word自动标题列表的设置是写architecture文档必不可少的过程，自动标题能自动推导更新各级标题的序号，增删改查任何标题都不需要手动的写标题序号。

（1）创建多级列表
![image-20221206142307899](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212061423943.png)

（2）设置一级标题

- 设置一级标题的序号样式为1,2,3
- 链接一级标题的字体样式到word文档的一级标题字体样式
![image-20221206142315070](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212061423121.png)

（2）设置二级、三级、n级标题

需要设置列表序号，标题字体两部分。注意列表序号的正确设置是序号自动推导的关键。
以二级标题为例，其他子级类推。

- 设置二级标题中的一级序号来自于level1。这一步保证二级标题中的一级序号是自动推导的。
![image-20221206142327739](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212061423795.png)

- 设置二级标题中的二级序号的样式，二级标题中的一、二级序号用.号隔开
![image-20221206142401132](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212061424185.png)

- 二级标题最终的序号样式如下
![image-20221206142411436](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212061424492.png)

- 然后设置二级标题的字体风格，直接链接到word的二级标题字体风格
![image-20221206142420685](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212061424730.png)

- 二级标题列表的所有设置完毕，如下
![image-20221206142428828](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212061424880.png)

(3)依次完成所有标题列表设置，例如三级标题，前两级的值来自于level1,level2，第三级设置数字格式即可，中间用.号隔开。完成以后各级标题就可以自动推导。

# Word导出原图

Word默认图片如果直接复制出来，不是原图是压缩后的图。 
保存原图方法： 文档另存为html网页格式，会把word文档转换成资源文件夹，里面有原始图片。
