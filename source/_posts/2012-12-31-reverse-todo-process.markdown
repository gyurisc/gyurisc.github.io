---
layout: post
title: "reverse todo process"
date: 2012-12-31 08:52
comments: true
categories: 
- productivity
- recipes
---
In my [previous article](http://www.littlebigtomatoes.com/2012/12/todo-or-todont/), I wrote about the my motivations using the Anti Todo list and how it took out tons of stress of my daily life. This way of doing things works well for me and here is how I implemented it. 

<!--more--> 

{% img /images/20121218_anti_todo.png 'Anti Todo' 'Anti Todo list in Sublime Text 2' %}

The idea here is to have this list everywhere and to be easily accessible and synchronized all the time. For synchronization I am using the [Dropbox](http://www.dropbox.com) service. In the root of my dropbox folder I created a file called anti.todo The easiest way of do something like this from your bash command line: 

	touch anti.todo 

With this, the synchronization part of my solution is taken care of. For creation of the items you will need to have Sublime Text 2 installed on your machine, which is a terrific text editor. You will need the [PlainTasks](https://github.com/aziz/PlainTasks) package to be installed as well. This package will be responsible for displaying the list. You can learn how to use the [PlainTasks](http://youtu.be/VQMU0PDfXyA) package.  

Once it is all set, you can open the anti.todo file with Sublime Text 2 and start adding your achivements to the list like this: 

 - Add todays date with a colon at the end. Underneath, you will collect the list you achieved for today. 
 - If you have done something then press ⌘+enter (ctrl+enter on Windows) to add achievement for today. 
 - After adding the new achievement, press ⌘+D (ctrl+d on Windows) to mark your achievement as done
 - Also you can use tags using @ sign, like this @tag
 - Once you are done for today just save the file. 
 - Rinse and repeat. 

Another alternative I can recommend on thhe Mac for creating this list is [TaskPaper](http://www.hogbaysoftware.com/products/taskpaper) for the Mac, or try [TodoPaper](http://widefido.com/products/todopaper/) on Windows.

If you find this usefull and start using yourself, please let me know how it works out for you!  
