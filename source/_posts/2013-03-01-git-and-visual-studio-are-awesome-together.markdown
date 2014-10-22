---
layout: post
title: "Git and Visual Studio are awesome together"
date: 2013-03-01 19:59
comments: true
categories: 
- code 
---
I love Visual Studio and I love git too. What can be even better than this? Visual Studio 2012 integrated with Git. The two together are pure awesomeness and I fell in love with them. 

{%img /images/git_vs_001.png 'Git SSCP' 'Git Source Control Provider' %}

There is a provider that you can install in visual studio 2012 and you will get git integration. It can talk to tfs, if you use git there. IT can create local git repos and even can clone repositories from github or from other git hosting servers. 

<!--more--> 

To get the source control provider installed, you need to download and install the [Git Source Control Provider](http://gitscc.codeplex.com/) package from codeplex. Once you are finished, launch Visual Studio and just open team explorer. You can connect to TFS hosted project or to local git repositories. 

{%img /images/git_vs_002.png 'Git SSCP' 'Git Source Control Provider' %}

## Getting familiar 

You can create a new repo by just providing the folder name. 

{%img /images/git_vs_003.png 'Git New' 'Creating a new Git repo' %}

Once you are done, navigate there using git bash and you will see that it is a proper git repo. 

{%img /images/git_vs_004.png 'Git Bash' 'New repository from git bash' %}

You can add existing local repos as well by providing the path 

{%img /images/git_vs_005.png 'Existing Repos' 'Adding existing git repositories' %}

Clone existing repos form github

{%img /images/git_vs_006.png 'Cloning Repos' 'Cloning repos from github' %}

## Nice touches 

There are several things to like in the visual studio git integration. You can change your git settings from git explorer such as gitignore file, your email, the root folder for your repos, even it can get your images for you, to make the history to look nicer. 

There is also a super nice and clean looking diff tool for you to go through the differences in commits.

{%img /images/git_vs_007.png 'Diff tool' 'Nice and clear diff tool in IDE' %}

## The Bad 

The one thing I do not like is that there is no way to open the solution from the ide. You need to browse out the solution manually. Obviously, this does not happen when you clone from TFS. Little inconvenience, but still tolerable though. 

Now go download it and use it. Cheers!



