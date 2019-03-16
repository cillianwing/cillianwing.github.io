---
layout: post
title:      "Struggling through Class/Instance Variables"
date:       2019-03-16 20:02:13 +0000
permalink:  struggling_through_class_instance_variables
---


I am a few weeks into the Flatiron Part-Time Software Engineering and have arrived at what I have been told is a critical part of my learning - **Object Orientation Programming**. In my brief dealings with OOP, I have realized it is conceptually more difficult to wrap my head around. There are a few nuances related to the Class objects we've been dealing with that have tripped me up on a few occasions, one of which I would like to speak to here: Class vs Instance Variables. I will stay away from *self* for now as that appears to be a beast in and of itself that will require a bit more review before I'm prepared to tackle it. 

**Possible spoilers below:** I will be going through some issues I had while completing the "OO Cash Register" lab so this is just a heads up to anyone that may be working on this lab and has yet to complete it. 

As a quick summary, this lab asks us to create a CashRegister class that is able to create new instances which will keep track of a total price and any discount that may be applied to our total. Within this class, we are asked to build an instance method that allows us to add an item, the item price, and the item quantity to the current instance. From here we are to create an instance method that returns the items that have been added to this instance through the *add_item* method. This *items* method is where I ran into issues and was forced to think a bit more about class vs instance variables.

I began this portion of the lab by utilizing a class variable set to an empty array (as seen below) to keep track of the items that were added through the *add_item* method:

```
class CashRegister
  attr_accessor :total, :discount
  @@items = []
```

Now that this was setup, I proceeded to create my *add_item* method such that each item that was added to the instance, was also added to the items array which I would want to call on later. As a side note, I decided to use the .fill method which allowed me to add the correct quantities of each item to the items array.

```
  def add_item(item, price, quantity=1)
    @total += (price * quantity)
    @@items.fill(item, @@items.size, quantity)
  end
```

The next step was to create an *items* instance method that simply called the items variable, which should be an array containing the items added through *add_item*.  Once this was done, I tested the *items* method and the return value contained far more items than I anticipated. I tested this by creating two instances and tried to return only the items in the 2nd instance:

```
checkout1 = CashRegister.new
checkout1.add_item("eggs", 3)
checkout1.add_item("bacon", 5, 5)
checkout1.add_item("OJ", 4)

checkout2 = CashRegister.new
checkout2.add_item("bread", 3)
checkout2.add_item("cheese", 4, 2)
checkout2.add_item("turkey", 6, 2)

checkout2.items
```

The above code ended up outputting the following:

```
["eggs", "bacon", "bacon", "bacon", "bacon", "bacon", "OJ", "bread", "cheese", "cheese", "turkey", "turkey"];
```

What gives?! Calling *.items* on checkout2 should only return the items "bread", "cheese", and "turkey" ! I clearly had managed to combine checkout1 and checkout2 items and it took a few minutes before I realized that this was caused by my use of a *class variable* instead of an *instance variable*. Setting the items variable as a *class variable* meant that all instances created would have their items added to this variable, which is clearly not what we wanted. When you are checking out of a grocery store, you don't care what items others have had checked out at that particular register, all you care about is *your* items that were checked out at that register. 

Once I came to this realization, I was able to modify my class such that it used attr_accessor to create an items *instance variable* and altered the instance methods to utilize this *instance variable* insead of the *class variable* used previously. I was then able to properly return the items needed for my turkey and cheese sandwich from the checkout2 instance!

```
class CashRegister
  attr_accessor :total, :discount, :items

  def initialize(discount=0)
    @total = 0
    @discount = discount
    @items = []
  end


  def add_item(item, price, quantity=1)
    @total += (price * quantity)
    @items.fill(item, @items.size, quantity)
  end

  def items
    @items
  end

end
```

checkout2 #=> `["bread", "cheese", "cheese", "turkey", "turkey"]`
