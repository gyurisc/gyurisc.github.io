---
layout: post
title: "Fixing the SelectedItems property binding for the silverlight listbox control"
date: 2013-02-21 15:43
comments: true
categories: 
- code 
- silverlight
- xaml 
---
I recently had to implement a feature in our silverlight app, where the user can remove multiple items selected in a listbox. The project uses the MVVM pattern, so all logic and state is handle in the ViewModel class. When trying to implement the multiple selection, I needed to add a property to my viewmodel that would be a reflection of the items selected on the view. It turns out the ListBox control does not support binding against the SelectedItems property for some reason. 

This article describe, how the problem has been solved. 

<!--more--> 

### Solution: 

The full code for the classes is included at the end of the article.  
The idea here is to able to create a two-way binding that would be able to represent the items selected in the listbox control. The definition for the property that holds the selected items on the viewmodel side would be defined like this: 

``` csharp property definition for the view model 
    private ObservableCollection<string> _selectedItems = new ObservableCollection<string>();

    public ObservableCollection<string> SelectedItems
    {
      get { return _selectedItems; }
      set { _selectedItems = value; RaisePropertyChanged("SelectedItems"); }
    }
``` 

The expected behavior is that whenever the user selects one or more items in the listbox our SelectedItems property would be able to hold selections and notify all parties that are subscribed to the changes of our collections. This is why the ObservableCollection<T> type is picked for this property. 

Next on the view side we would like to add a databinding that binds the listbox's selecteditems collection to our view-model SelectedItems property. As the listbox is not allowing to bind to this property, we need to have a new control called SmartListbox. 

The binding expression is the most important one here. On the left hand side you can see the SmartSelectedItems property of the SmartListBox. On the right hand side of the binding expression there is the SelectedItems property of our viewmodel. For the rest of the article, I will refer these properties as left side and right side of the binding expression. 

``` xml SmartListBox 
    <my:SmartListBox HorizontalAlignment="Stretch" x:Name="smartListBox" 
                     VerticalAlignment="Stretch" VerticalContentAlignment="Stretch" HorizontalContentAlignment="Stretch" 
                     ItemsSource="{Binding Items, Mode=TwoWay}" SmartSelectedItems="{Binding SelectedItems, Mode=TwoWay}"
                     SelectionMode="Extended" />
``` 

To be able to bind to a property in Xaml word the property needs to be defined as a DependencyProperty. This will be consisting a static and a non-static definition of the property. The static definition describes the property, so the runtime and editor tools will understand what the property type is, what the name of the property is and so on... 

``` csharp DependencyProperty definition 
    public static readonly DependencyProperty SmartSelectedItemsProperty =
      DependencyProperty.Register("SmartSelectedItems", typeof(INotifyCollectionChanged), typeof(SmartListBox), new PropertyMetadata(OnSmartSelectedItemsPropertyChanged));

    public INotifyCollectionChanged SmartSelectedItems
    {
      get { return (INotifyCollectionChanged)GetValue(SmartSelectedItemsProperty); }
      set { SetValue(SmartSelectedItemsProperty, value); }
    }
``` 

The general idea here is that we have two collections and we need to be able to detect and synchronize the changes between them, so we subscribe to the SmartListbox collection changes and we also subscribe to the changes of the collection that binds to our property. 

In the constructor we simply just subscribe to the changes of our control

``` csharp SmartListBox constructor 
    public SmartListBox()
    {
      SelectionChanged += new SelectionChangedEventHandler(BaseListBoxSelectionChanged);
    }
```

When the dependency property is defined we referenced a method called OnSmartSelectedItemsPropertyChanged. The purpose of the method is to handle the subscription to the dependency property. We would like to detect everything that happens to the right side collection in the binding, so we can support two way bindings as well. 

You might notice the weirdness of the subscription here - the unsubscribe before subscribe - this is needed because if a view is visited more than binding is evaluated every time and we would end up subscribing to the collection more than once. This is unneeded, so the best is to make sure that we have one subscription only. Kudos for this tip to my colleague Mr. Rajnai for the tip. 

``` csharp Handling collection changes
    private static void OnSmartSelectedItemsPropertyChanged(DependencyObject target, DependencyPropertyChangedEventArgs args)
    {
      var collection = args.NewValue as INotifyCollectionChanged;
      if (collection != null)
      {
        // unsubscribe, before subscribe to make sure not to have multiple subscription
        collection.CollectionChanged -= ((SmartListBox)target).SmartSelectedItemsCollectionChanged;
        collection.CollectionChanged += ((SmartListBox)target).SmartSelectedItemsCollectionChanged;
      }
    }
``` 

The next method is handling the changes in the right hand side of the binding expression. The control unsubscribes from all collection notification, then transfers the selected items from the right hand side collection to the left hand side collection and then subscribes back to the events. The unsubscribe-subscribe is need this because there is no need to trigger any notifications to the collection that is being updated.   

``` csharp Handling selection changes 
    void SmartSelectedItemsCollectionChanged(object sender, NotifyCollectionChangedEventArgs e)
    {
      //Need to unsubscribe from the events so we don't override the transfer
      UnsubscribeFromEvents();

      //Move items from the selected items list to the list box selection
      Transfer(SmartSelectedItems as IList, base.SelectedItems);

      //subscribe to the events again so we know when changes are made
      SubscribeToEvents();
    }
```

The following method is responsible to handle the changes when the selected items in the listbox are changing. 

``` csharp 
    void BaseListBoxSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
      //Need to unsubscribe from the events so we don't override the transfer
      UnsubscribeFromEvents();

      //Move items from the selected items list to the list box selection
      Transfer(base.SelectedItems, SmartSelectedItems as IList);

      //subscribe to the events again so we know when changes are made
      SubscribeToEvents();
    }
```

There is one possible risk with the implementation though. The dependency property is defined as INotifyCollectionChanged, but the code works with IList when doing the transfer. It is possible that the control binds against a property in the future that not implementing the IList. What will happen then? 

Well, in the worst case when the Transfer method is called the conversion to IList results a null and the method simply returns resulting the binding to be not working. This is not ideal, but it solves the problem I faced.

Obviously, if this control would be sold to third parties the hidden requirement to also implement the IList for the viewmodel property needed to be addressed in some ways. 

**Disclaimer:** I do not work in vacuum nor inventing everything from scratch. I rely on google searching when coding and to find great solutions from fellow developers. For this particular problem, I found this great article and it served me as a starting point: 

[No Binding for you a ListBox SelectedItems behavior solution](http://blog.bdcsoft.com/developer-blog/2011/no-binding-for-you-a-listbox-selecteditems-behavior-solution/)

### Full code 

``` csharp SmartListBox full class
  public class SmartListBox : ListBox
  {
    #region Properties 

    public static readonly DependencyProperty SmartSelectedItemsProperty =
      DependencyProperty.Register("SmartSelectedItems", typeof(INotifyCollectionChanged), typeof(SmartListBox), new PropertyMetadata(OnSmartSelectedItemsPropertyChanged));

    public INotifyCollectionChanged SmartSelectedItems
    {
      get { return (INotifyCollectionChanged)GetValue(SmartSelectedItemsProperty); }
      set { SetValue(SmartSelectedItemsProperty, value); }
    }

    #endregion

    public SmartListBox()
    {
      SelectionChanged += new SelectionChangedEventHandler(BaseListBoxSelectionChanged);
    }

    private static void OnSmartSelectedItemsPropertyChanged(DependencyObject target, DependencyPropertyChangedEventArgs args)
    {
      var collection = args.NewValue as INotifyCollectionChanged;
      if (collection != null)
      {
        // unsubscribe, before subscribe to make sure not to have multiple subscription
        collection.CollectionChanged -= ((SmartListBox)target).SmartSelectedItemsCollectionChanged;
        collection.CollectionChanged += ((SmartListBox)target).SmartSelectedItemsCollectionChanged;
      }
    }

    void SmartSelectedItemsCollectionChanged(object sender, NotifyCollectionChangedEventArgs e)
    {
      //Need to unsubscribe from the events so we don't override the transfer
      UnsubscribeFromEvents();

      //Move items from the selected items list to the list box selection
      Transfer(SmartSelectedItems as IList, base.SelectedItems);

      //subscribe to the events again so we know when changes are made
      SubscribeToEvents();
    }

    void BaseListBoxSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
      //Need to unsubscribe from the events so we don't override the transfer
      UnsubscribeFromEvents();

      //Move items from the selected items list to the list box selection
      Transfer(base.SelectedItems, SmartSelectedItems as IList);

      //subscribe to the events again so we know when changes are made
      SubscribeToEvents();
    }

    private void SubscribeToEvents()
    {
      SelectionChanged += BaseListBoxSelectionChanged;

      if (SmartSelectedItems != null)
      {
        SmartSelectedItems.CollectionChanged += SmartSelectedItemsCollectionChanged;
      }
    }

    private void Transfer(System.Collections.IList source, IList target)
    {
      if (source == null || target == null)
      {
        return;
      }

      target.Clear();

      foreach (var o in source)
      {
        target.Add(o);
      }
    }

    private void UnsubscribeFromEvents()
    {
      SelectionChanged -= BaseListBoxSelectionChanged;

      if (SmartSelectedItems != null)
      {
        SmartSelectedItems.CollectionChanged -= SmartSelectedItemsCollectionChanged;
      }
    }
  }
```

``` csharp ViewModel full class
  public class SmartListboxViewModel : ViewModelBase
  {
    public SmartListboxViewModel()
    {
      _selectedItems.CollectionChanged += (sender, args) => { SelectionCount = SelectedItems.Count; };
    }

    #region Properties
    private ObservableCollection<string> _items = new ObservableCollection<string>();
 
    public ObservableCollection<string> Items
    {
      get { return _items;  }
      set { _items = value; RaisePropertyChanged("Items"); }
    }

    private ObservableCollection<string> _selectedItems = new ObservableCollection<string>();

    public ObservableCollection<string> SelectedItems
    {
      get { return _selectedItems; }
      set { _selectedItems = value; RaisePropertyChanged("SelectedItems"); }
    }

    private int _selectionCount;

    public int SelectionCount
    {
      get { return _selectionCount; }
      set { _selectionCount = value; RaisePropertyChanged("SelectionCount"); }
    }

    #endregion
  }
```