---
layout: post
title: "Exploring Xamarin iOS on Windows"
date: 2013-03-28 08:24
comments: true
categories: 
- code 
- xamarin 
---
Xamarin not so long ago released the version 2.0 of their product that allows all .NET developers to use C# and .NET libraries to code apps for iOS and Android. Better yet, it allows us to use our bellowed Visual Studio.NET to code for these platforms. 

I love to try out new things, so I decide to figure out if it is really possible to create very basic application for iOS and for Android purely from Visual Studio.NET.

<!--more--> 

The one thing I need to add is that I am without any knowledge of Objective-C or the iOS platform. I can construct the very basic HelloWorld app in XCode, but this is how far my iOS knowledge reaches. Regarding Android, I have zero knowledge of the platform and how to construct the HelloWorld app for the platform. Nevertheless, I give it a try and document my ups and downs creating these apps in vs.net. 

### Creating a new project 

I installed the Xamarin environment and started up Visual Studio.NET 2012. Selected File | New Project and created the project. 

{%img /images/xam_vs_001.png 'New Project' 'Creating new project for iOS in Visual Studio' %}

In order to be able to use Visual Studio.NET with Xamarin, you need the Business version or you will need to start the 30 days trial. I opted for the second option.

But first begin the [30 days trial](http://docs.xamarin.com/guides/cross-platform/getting_started/beginning_a_xamarin_trial).

{%img /images/xam_vs_002.png 'Starting the 30 days trial' 'Starting the 30 days trial' %} 

After creating and logging in with the Xamarin account the 30 days trial starts and Visual Studio .NET restarts. It starts searching on the network for active OS X machines to be used as build machine. I have nothing like that on my network, so let’s close the dialog.  

{%img /images/xam_vs_003.png 'Searching for build machine' 'Searching for build machine' %} 

I have no mac machine on the network currently so let’s choose **Close**. 

// footnote??? 
Minor inconvenience is that you will need a live network connection for Xamarin software to check the license with the server and this can be problematic, if you are doing this while you are commuting without network. 

### Dude, where is my design surface? 

I created my HelloWorld project and my limited iOS knowledge tells me that this point I need to open .xib or .nib file and start dragging controls onto the design surface. Looking through the project there are no .xib files to open. Searching through the Add New Item dialog yields no result as well.  

Looking through the guide I find the following section: 

{% blockquote Xamarin http://docs.xamarin.com/guides/ios/getting_started/introduction_to_xamarin_ios_for_visual_studio Introduction to Xamarin iOS for Visual Studio %}
Storyboard and XIB files (used by Apple’s Interface Builder for GUI design) cannot currently be edited in Visual Studio. If you create an application from a Storyboard template (or a template that includes XIB files) then you’ll have to switch to the Mac and open the entire solution in Xamarin Studio. From within Xamarin Studio, double-click on XIBs or Storyboards to open and edit them. This includes adding a new item after the project is created, from the following options:

Working with Storyboards and XIBs

Editing storyboard and XIB files must be done on OS X with Interface Builder. The Xamarin.iOS plug-in for Visual Studio does not provide any support for this.
{% endblockquote %}

[Xamarin Guide](http://xamarin.com/guide/) explains the alternatives in greater details:

{% blockquote Xamarin http://xamarin.com/guide/ Xamarin Guide %}
Xamarin.iOS for Visual Studio comes with several simplified iOS application templates that eschew XIB and Storyboard files, giving developers the option of building their entire user interface in code.  That approach could be desirable for some developers on Windows who want to avoid Xcode and minimize their reliance on a Mac. Xamarin’s MonoTouch.Dialog library— which provides a simple, declarative API for building iOS user interface layouts in code—might prove especially useful for that particular audience.

Developers who still want to use XIB or Storyboard files can still use Xcode on Mac OS X in order to modify the layout. Fortunately, the high degree of interoperability between Xamarin Studio and Visual Studio makes that a relatively straightforward process. A developer can do most of their development with Visual Studio on Windows and jump over to Xamarin Studio on a Mac while working on user interface design. The same exact project and solution files can be used in both environments.
{% endblockquote %}

So you can get rid of XIB if you like and define your UI entirely using the dialog library or you can use XiB but then you need a Mac too.

### Rebooting to OS X 

I decided to stay with the path I know and restarted my macbook to boot OS X. This way I can use the xib approach for now and once I finished I will be going back to Visual Studio.

{%img /images/xam_vs_004.png 'Xamarin Studio on OS X' 'Looking at Xamarin Studio in OS X' %}

Opening the project in Xamarin Studio I realized that I have no idea how to integrate a new storyboard to my project, so I opted to create a brand new project that has a xib file included. It is not a very high-tech move, but sometimes it pays to create your project from scratch. 

{%img /images/xam_vs_005.png 'Creating a new project' 'Creating a new project' %}

### Creating the UI in XCode 

Once the new project is created, I can open the xib file in XCode to add my controls and create actions and outlets for these controls. I started with dragging a label and a button onto the design surface. 

{%img /images/xam_vs_006.png 'Dragging the controls in XCode' 'Dragging the controls in XCode' %}

Selecting the code icon in XCode will bring in the source control editor. I now can control-drag – clicking on the control while dragging the control to the source code editor – to create my outlet for the Label and the action for my button. 

{%img /images/xam_vs_007.png 'Press this button to bring in the source code editor in XCode' 'Press this button to bring in the source code editor in XCode' %}

Pressing control and dragging the button to the source code editor will bring up a little window that allows me to set up the action for my button. I changed the connection type to action and name it btnPRessed. 

{%img /images/xam_vs_008.png 'Creating an action for the button in XCode' 'Creating an action for the button in XCode' %}

I do the same control dragging for the labe, but this time the connection type is set to outlet and the name lblText. 

{%img /images/xam_vs_009.png 'Creating an outlet for the label in XCode' 'Creating an outlet for the label in XCode' %}

The last important step is to make sure that you save the .xib file. I forget this most of the time annd the result is that these changes will not be generated in the code behind for the view controlller. Back in Xamarin Studio, I can confirm by looking at the codebehind for the view controller that the outlet and action for my controlls are showing up in the code. Let’s go back to Visual Studio.NET.

{%img /images/xam_vs_010.png 'Codebehind for the ViewController in Xamarin Studio shows that the constructs for my controlls are generated' 'Codebehind for the ViewController in Xamarin Studio' %}

### Back to Visual Studio 

All I have left is to define the event handler – or action method if you like for my button and set the content of the label. The action is defined as a partial method in the code-behind, so I need to provide the implementation for my method. With this I am finished constructing my very basic application for iOS. All is left to test it on a Mac in an emulator to see if it works. 

{%img /images/xam_vs_011.png 'Partial method for the button in Visual Studio' 'Partial method for the button in Visual Studio' %}


### Conclusion 

Xamarin version 2.0 brings the possibility to develop for iOS from Visual Studio.NET for the first time. This is a very welcome change, but it has its own weakness. One is that for creating the UI you will need a Mac computer or you will need to code your whole UI in code. This can be addressed by creating a tool that allows us developers to do UI creation on Windows. Hopefully this will come in the next version. The other area that for testing and debugging you will not be able to live without a mac computer. Once again having a cheap mac computer on the network solves this problem. I have no experience how convenient the debugging, but I will try this soon. 

Overall, I am very happy with the new version and the fact that they allow free use with limitation makes me very happy and probably I will invest my time to learn the technology. Well done, Xamarin! 