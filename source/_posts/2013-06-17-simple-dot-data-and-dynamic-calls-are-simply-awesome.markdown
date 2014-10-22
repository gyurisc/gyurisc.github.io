---
layout: post
title: "Simple.Data and dynamic calls are awesome"
date: 2013-06-17 11:59
comments: true
categories: 
- code 
- dotnet 
---
Couple of weeks ago I started to look into different open source packages on the .NET platform such as [Nancy](http://nancyfx.org/) and [Simple.Data](http://blog.markrendle.net/2010/08/05/introducing-simple-data/). They all looked very cool, but one paragraph in an article called [Introducing Simple.Data](http://blog.markrendle.net/2010/08/05/introducing-simple-data/) - explaining how the code snippet below works - caught my attention:

``` csharp getting data from db 
var db = Database.Open(); // Connection specified in config.
var user = db.Users.FindByNameAndPassword(name, password);
```
{% blockquote Mark Rendle http://blog.markrendle.net/2010/08/05/introducing-simple-data Introducing Simple.Data %}
That’s pretty neat, right? So, did we have to generate the Database class and a bunch of table classes to make this work?

No.

In this example, the type returned by Database.Open() is dynamic. It doesn’t have a Users property, but when that property is referenced on it, it returns a new instance of a DynamicTable type, again as dynamic. That instance doesn’t actually have a method called FindByNameAndPassword, but when it’s called, it sees “FindBy” at the start of the method, so it pulls apart the rest of the method name, combines it with the arguments, and builds an ADO.NET command which safely encapsulates the name and password values inside parameters. The FindBy* methods will only return one record; there are FindAllBy* methods which return result sets. This approach is used by the Ruby/Rails ActiveRecord library; Ruby’s metaprogramming nature encourages stuff like this.
{% endblockquote %}

Just read that sentence once again... 

*That instance doesn’t actually have a method called FindByNameAndPassword, but when it’s called, it sees “FindBy” at the start of the method, so it pulls apart the rest of the method name, combines it with the arguments, and builds an ADO.NET command which safely encapsulates the name and password values inside parameters.*

This sounds like crazy awesome magic and I need to understand it! 

## How magic works? 

To find out how the magic worked, I downloaded the source code from github and started to poke around the code. The rest of the article explains what I got out of this experiment. 

<!--more--> 

The sample project I created is sort of the skeleton of what Simple.Data does on the inside. This is not an attempt to reproduce or fully understand the library. I just wanted to understand how one can call a non-existing method on a class and what is happening behind the curtain that enables this at runtime.

``` csharp disecting the magic    
  class Program
  {
    static void Main(string[] args)
    {
      var dyn = DynamicTest.GetDynamic();
      var user = dyn.Users.FindByEmail("someone@somewhere.com");
    }
  }
```

So, after poking around in the [source code](https://github.com/markrendle/Simple.Data) of Simple.Data I realized that the magic happens by applying the [dynamic](http://msdn.microsoft.com/en-us/library/vstudio/dd264741.aspx) keyword and DynamicObject.

``` csharp DynamicObject skeleton     
  public class DynamicTest : DynamicObject
  {
    public static dynamic GetDynamic()
    {
      if (_instance == null)
      {
        _instance = new DynamicTest();
      }

      return _instance;
    }
  }
```

I created a class that derives from DynamicObject and a method that returns an instance of this object, but instead of a return type I added the dynamic keyword to the method signature. What this does effectively is that when compiling my simple test code, the compiler will not look for the declaration of a Users property. It knows that this will resolved at runtime. 

Once running this code there will be a RuntimeBinderException exception thrown with the the following message: 

```
	Message='object' does not contain a definition for 'Users'
```

Now, we need to somehow create a Users property on the DynamicTest class. For this, we need to override the TryGetMember method of the DynamicObject and implement this behavior. 

So, TryGetMember will just create and return an instance of an ObjectReference object. It has a parameter called binder that has information about the member that is called and an out parameter to pass back the result. The created object will be stored with binder.Name as key, so next time the same instance can be returned. This will represent the **.User** part of our dynamic call. But how will we handle the remaining method call? 

``` csharp TryGetMember implementation 
    public override bool TryGetMember(GetMemberBinder binder, out object result)
    {
      result = GetOrAddDynamicReference(binder.Name);
      return true; 
    }

    private dynamic GetOrAddDynamicReference(string name)
    {
      return _members.GetOrAdd(name, new ObjectReference(name));
    }
```

ObjectReference is also derived from DynamicObject and it overrides the TryInvokeMember method to handle the remaining method call. Again, it receives a binder that contains information about the member that is being invoked, the parameters that is passed in and it has an out parameter that is used to pass back the result. What this method does is just simply prints out the method that is being invoked and its parameters.

``` csharp TryInvokeMember implementation
    public override bool TryInvokeMember(InvokeMemberBinder binder, object[] args, out object result)
    {
      if (base.TryInvokeMember(binder, args, out result)) return true;

      try
      {
        Console.WriteLine("Called " + 
                          _name + "." + 
                          binder.Name + 
                          "(" + 
                          args.Aggregate((current, next) => current + ", " + next) + 
                          ")");

        result = "result";
        return true;
      } catch
      {
        throw new InvalidOperationException(string.Format("Method {0} not recognized", binder.Name));
      }

      throw new InvalidOperationException(string.Format("Method {0} not recognized", binder.Name));
    }
```

Now, that I better understand how all this works I realized that I am way behind with the new features of C#. I guess, I need to buy a book about the last version of the language, read it and learn a bit more as I am behind. Below is the full sample code that I created: 

``` csharp full source code for dynamic test
using System;
using System.Collections.Concurrent;
using System.Collections.Generic;
using System.Dynamic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace DynamicTest
{
  class Program
  {
    static void Main(string[] args)
    {
      var dyn = DynamicTest.GetDynamic();
      var user = dyn.Users.FindByEmail("someone@somewhere.com");
    }
  }

  public class DynamicTest : DynamicObject
  {
    private readonly ConcurrentDictionary<string, dynamic> _members = new ConcurrentDictionary<string, dynamic>();

    private static DynamicTest _instance; 

    public static dynamic GetDynamic()
    {
      if (_instance == null)
      {
        _instance = new DynamicTest();
      }

      return _instance;
    }

    public override bool TryGetMember(GetMemberBinder binder, out object result)
    {
      result = GetOrAddDynamicReference(binder.Name);
      return true; 
    }

    private dynamic GetOrAddDynamicReference(string name)
    {
      return _members.GetOrAdd(name, new ObjectReference(name));
    }
  }

  public class ObjectReference : DynamicObject
  {
    private string _name;

    public ObjectReference(string name)
    {
      _name = name;
    }

    public override bool TryInvokeMember(InvokeMemberBinder binder, object[] args, out object result)
    {
      if (base.TryInvokeMember(binder, args, out result)) return true;

      try
      {
        Console.WriteLine("Called " + 
                          _name + "." + 
                          binder.Name + 
                          "(" + 
                          args.Aggregate((current, next) => current + ", " + next) + 
                          ")");

        result = "result";
        return true;
      } catch
      {
        throw new InvalidOperationException(string.Format("Method {0} not recognized", binder.Name));
      }

      throw new InvalidOperationException(string.Format("Method {0} not recognized", binder.Name));
    }
  }
}
```