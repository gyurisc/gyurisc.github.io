---
date: '2012-03-21 18:18:08'
layout: post
slug: an-easy-way-to-create-awesome-looking-windows-phone7-icons-using-expression-design
status: publish
title: An easy way to create awesome looking Windows Phone7 icons using expression
  Design
wordpress_id: '85'
categories:
- design
- icons
- windowsphone
- wp7
tags:
- wp7
- wp7dev
---
I am always having problems when it comes to creating icons for my Wp7 apps.I am a coder and not a designer, so drawing something nice looking is really really out of my comfort zone.

Previously I spent hours trying to draw symbols myself, but I never really liked the outcome. But lately, I found a quick and effective way to create nice looking icons for my Windows Phone 7 apps. Here it goes:

<!--more--> 

### The Noun project

First, I go to the [nounproject](http://thenounproject.com) website to find some nice looking symbols. This website has hundreeds of symbols and you can download them for free in vector format. The graphics comes with a Creative Commons license, so you're free to do whatever you want with them. Sounds pretty neat, if you ask me!

{% img /images/000.png 'thenounproject website' 'The Noun Project a website a source for symbols in SVG format' %}

Once, you found the symbol you like, download and extract it. The symbol is in SVG format. I am using Expression Design for drawing my icons, but Design unfortunately does not support SVG yet. I need a tool that can convert SVG to something that I can import to Expression Design.

{% img /images/001_thumb.png 'Choose a symbol from thenounproject site.' 'Symbol from thenounproject website.' %}

### Inkscape

With Inkscape I can do exactly this. Inkscape can import SVG and can export .PDF and .PDF can be imported to Expression Design.Inkscape is a very cool, free and open source drawing application. You can download it from [here](http://inkscape.org/).

{%img /images/002_thumb.png 'Inkscape' 'Download and install inkscape' %}

Once Inkscape is installed then you can import the .SVG file and save it as .PDF file. After that I change the  .PDF extension to .AI and now I can import into Expression Design.

First, import your svg file to Inkscape for converting to AI format:

{%img /images/003_thumb.png 'Import your symbol to Inkscape' 'Import your symbol to Inkscape' %}

{%img /images/004_thumb.png 'Symbol imported' 'Symbol imported' %}

Then save the your symbol in PDF format. Make sure that PDF 1.4 is selected as PDF version:

{%img /images/005_thumb.png 'Export your symbol in PDF format' 'Export your symbol in PDF format' %}

{%img /images/007_thumb.png 'Setting the correct PDF version' 'Setting the correct PDF version to PDF 1.4' %}

### Expression Design

Before importing to Expression Design rename the exported .pdf to  .ai. This way Expression Design can export your artwork as an Adobe Illustrator file.

{%img /images/009_thumb.png 'Import your artwork to Expression Design' 'Import your artwork to Expression Design' %}

Here in Expression Design I can re-use my icon design template to create all the different sizes of icons that are needed for my WP7 app and export them to .PNG format to embedd in your WP7 Application project.

{%img /images/010_thumb.png 'Your artwork now imported to Expression Design' 'Your artwork now imported to Expression Design' %}

You can also change the shape as it is vector based to alter the shape!

{%img /images/011_thumb.png 'Vector shapes in Expression Design' 'Vector shapes in Expression Design' %}

### Bonus

If you would like to have this artwork in your silverlight, wpf or your windows phone app, you can also create a xaml file out of it in Expression Blend importing the .ai file and converting it to xaml.

{%img /images/014_thumb.png 'Artwork imported to Expression Blend' 'Artwork imported to Expression Blend' %}

Happy coding!
