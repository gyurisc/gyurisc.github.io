---
layout: post
title: "Serializing empty IEnumerable<T> with protobuf.net"
date: 2015-10-30 17:06:13 +0100
comments: true
categories: 
- code 
- dotnet
- protobuf.net
---

I am recently trying to use protobuf.net for serializing objects for network communication in a .net application. Protobuf is a great format for sending data. Protobuf tries to be very frugal to save bandwidth. However, this causes some unexpected side effects when using it with .NET.

<!-- more -->

For example, If there is a collection serialized and the collection count was zero, then the serializer is going to write out null and this collection will be deserialized as null on the other end. 

This is easy to fix when you have only a few classes that holding your models, but can be a huge problem when you have a very large number of classes that you are trying to serialize and the previously used serializer was always deserializing an empty collection to an empty collection. 

To solve this, we need extra wrapper class and within this wrapper class it is possible to detect if the underlying generic type is a IEnumerable&lt;T&gt; and create code to initialize the data member as an empty collection if needed.
   
The code below demonstrates how to use this technique.

``` csharp creating empty collection in data wrapper 
    public class DataWrapper<T>
    {    
        public T Data { get; set; }
        public DataWrapper()
        {
            Type dataType = typeof(T);

            // Protobuf deserializes empty collections as null in order to combat this we create an empty collection by default
            if (dataType.IsGenericType && dataType.GetGenericTypeDefinition() == typeof(IEnumerable<>))
            {
                Type underlyingType = entityType.GetGenericArguments()[0];

                var listType = typeof(List<>);
                var concreteType = listType.MakeGenericType(underlyingType);

                Data = (TEntity)Activator.CreateInstance(concreteType);
            }
            else
            {
                Data = default(T);
            }
         }
     }
```
 