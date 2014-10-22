---
date: '2012-05-07 16:13:28'
layout: post
slug: developer-recipes-using-git-and-dropbox-together
status: publish
title: Developer Recipes - Using Git and Dropbox together
wordpress_id: '117'
categories:
- Recipes
---
I love coding. My work is my hobby and I like to write small little apps and tools that I can use myself. I also use several machines with multiple operating systems on them and I need a workflow that would allow me to seamlesly synchornize between my machines. For this I created this process that involves git and dropbox, to keep my personal projects under source code control and have it synchroned to all my machines automatically. So I decided to document this method here.

<!--more--> 

**Disclaimer:** I do not invented this method. Originally I saw the idea on stackoverflow.com:

[Stackoverflow - Using git dropbox together effectively?](http://stackoverflow.com/questions/1960799/using-gitdropbox-together-effectively)

I am also not convinced that this method is suitable for more than one person to use. I am a single developer and this process fits my need perfectly.


### Overview


I use Visual Studio.NET 2010, [dropbox](https://www.dropbox.com/) and [msysgit](http://code.google.com/p/msysgit/) on all my Windows machines. I am mainly developing for my Windows Phone and soon I will try to create Metro Apps for Windows 8. Although, this will be .NET and Visual Studio centric recipe, you can still use it with your own stuff. The only crucial component is dropbox and git.

Let say, you have two machines. One laptop that you carry around and one desktop. Both have dropbox and git installed. You start creating your project on your laptop, but when you arrive home you would like to continue the coding on your desktop machine. So what will happen is the following:

  * I will set up the project on my laptop and add it to the git source control
  * I will initialize a bare repository on my laptop’s dropbox folder.
  * Will push the project to dropbox.
  * Get the project on the desktop machine

### Setting up project and add it to git
	
  * Set up your Visual Studio.NET project and do some coding

First, thing first is to create your application in Visual Studio.NET 2010. I will create a new todo app for my Windows Phone called **TodoApp_WP7**. Cannot see enough todo app on this new platform yet. So we need one :)
	
  * Initialize the git repository in the solution folder

Open up msysgit and navigate to your solution folder. Initialize your git repo and your
```
git init
```
Before adding your project files, it is a good idea to create a .gitognore file. Here you can specify what files you want to exclude from source code controll! So in your msysgit command line you issue this command to create .gitignore and add the things you would like to ignore.

```
touch .gitignore
notepad .gitignore
```

I have something like this in my .gitignore

```
User-specific files
 
*.suo
*.user
*.sln.docstates
  
Others
  
[Bb]in
[Oo]bj
```

Then you can add your files and do your first commit

```
git add .
git commit -m “first commit”
```

### Pushing the changes to Dropbox
	
  * Create a git folder in your Dropbox folder and initialize a bare git repository

Navigate to your git dropbox folder and create your bare repository. Navigating in the git shell can be tricky to someone, who knows only windows command line.

My Dropbox is installed on my machine here D:\Dropbox, so to go to that folder you need to issue this command:
```
cd /d/Dropbox/git
git init —bare TodoApp_WP7.git
```
  * Adding remotes

Now you need to go back to your project directory and configure the dropbox folder as the origin for your project. I also add a remote called dropbox as a convenience.
```
git remote add origin /d/Dropbox/git/TodoApp_WP7.git
git remote add dropbox /d/Dropbox/git/TodoApp_WP7.git
```	
 * Push the project to your bare dropbox repository

Now you need to push the project to your dropbox repository
```
 git push -u origin master
```
  * On your other machine just simply do a git clone to get the project

### Getting your project from Dropbox to another computer for the first time

When logging on to your other machine you will see that dropbox is busy to refresh all the files you were changing or it already has been happened. All you need to do is to open up the git client on your machine, navigate to your dev folder an issue the command
```
git clone /d/Dropbox/git/TodoApp_WP7.git
```

This will create the folder in your dev folder and gets the source code of your app. You will need not to worry about remotes, however you can create anew remote called dropbox, if you like.
Now you are ready to launch Visual Studio on the machine and do some changes. After finishing your work you can return to the git command line and issue the following command to find out what has changed:
```
git status
```

You will see all files that are changed or new. You will need to add the changes to your change set and commit these changes .

```
git add .
git commit -m “My new changes to my app”
```

With the commit command you added all the changes to your git repository. However, you still need to push these changes to your dropbox. It is called origin and it is automatically created when you cloned your project from dropbox.

All you need to do is to issue the following command:

```
git push
```

Now you are done with all the changes and these are now synchronized on all your developer machines.

### Getting the changes for your project on a machine that already has your project.

This is the simples command. All you need to do is to go to your project repository and do a pull operation:

```
git pull
```
I hope that you will find this method useful for synchronizing code between your computer via dropbox and git. If you have any questions, please do not hesitate to contact me!

### Useful links
	
  * [git cheat sheet](https://github.com/AlexZeitler/gitcheatsheet/blob/master/gitcheatsheet.pdf)


