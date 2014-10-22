---
date: '2011-11-29 06:23:55'
layout: post
slug: an-easy-way-to-create-windows-phone-7-icons-using-expression-design
status: publish
title: An easy way to create Windows Phone 7 Icons using Expression Design
wordpress_id: '38'
categories:
- design
tags:
- expressiondesign
- wp7
- wp7dev
---
In the past I had problems when I wanted to create icons quickly for my Windows Phone 7 apps before submitting them to the marketplace certification. A good set of consistent icons is a good way to give a good first impression to your future customers, but if you decide to create those icons yourself then it is a time consuming task. I decided to fix this problem for good!

{% img /images/expression_design-300x176.png 'Windows Phone 7 Icons with Expression Designy' 'Windows Phone 7 Icons with Expression Design' %}

For your application and for marketplace submission you will need the following artwork:

  * Application Icon (62 x 62)	
  * Application Tile Image (173 x 173)
  * Marketplace catalog icon (small) (99 x 99)
  * Marketplate catalog icon (large) (173 x 173)
  * Desktop application icon (200 x 200)

To create an icon and the resize it correctly and export it is always a lot of repetitive work, the one I do not enjoy. So in my favorite design tool - Expression Design 4 - created a file, where all you need is to replace the shape in each icon and select **File | Export** and you are done. All the icons will be ready for your project.

I created a git repository for it called [WP7ExpressionIcons](https://github.com/gyurisc/WP7ExpressionIcons).
I used this to articles as my starting point:

  * [Programming Windows Phone 7.5 Part 2: Icons](http://cgeers.com/2011/10/30/programming-windows-phone-7-5-part-2-icons/#more-3521)
  * [Creating Windows Phone 7 Application and Marketplace icons](http://expression.microsoft.com/en-us/gg317447)