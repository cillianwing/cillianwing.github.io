---
layout: post
title:      "Pushing Sinatra To Its Limits?"
date:       2019-07-01 03:38:15 +0000
permalink:  pushing_sinatra_to_its_limits
---


When I first started working through various labs that incorporated Sinatra, I was blown away. It was able to provide simple patterns of code that allowed a coding newbie, such as myself, to create the necessary relationships, page renderings, etc. that are needed for a functioning (albeit simplistic) web app. However, during my attempts to complete my first Sinatra based project, I started to find what I believe to be limitations with Sinatra.

My project goal was to create a Content Management System that would allow a user to track their various musical instruments and accessories, including more detailed information on said instruments/accessories (such as make, model, condition, status, and options for editing the item). I later decided to add in a repair feature, which would allow the user to separately manage their instruments/accessories currently being repaired. Through this feature, they could track the work being done, cost, and the duration/estimated completion date. The repair feature is the portion where I started running into problems due to the associations I was attempting to implement. 

Before I dive into the issues I came across, let me quickly go through the User-Instrument-Accessory associations to ensure those were setup properly and did not cause my issues. The associations were as follows:

1. User: *has_many* Instruments and *has_many* Accessories

```
   class User < ActiveRecord::Base
     has_secure_password

     has_many :instruments
     has_many :accessories
   end
```

2. Instrument: *belongs_to* User

```
   class Instrument < ActiveRecord::Base
     belongs_to :user
   end
```

3. Accessory: *belong_to* User

```
   class Accessory < ActiveRecord::Base
     belongs_to :user
   end
```

As Users were created and the users in turn added Instruments/Accessories to their inventory, I was able to use the code below to successfully call and manipulate the Instrument/Accessory objects:

```
   current_user.instruments #=> [InstrumentObject1, InstrumentObject2, etc.]
```
OR
```
   current_user.accessories #=> [AccessoryObject1, AccessoryObject2, etc.]
```

(Side note: the *current_user* method show above was a nifty little method nestled into my ApplicationController shown below.)

```
    def current_user
      @current_user ||= User.find_by_id(session[:user_id]) if session[:user_id]
    end
```

Up to this point, Sinatra made everything so easy to implement that I actually breezed through this section of my project! Unfortunately, the euphoria of running into very few obstacles while coding this portion quickly came to an end once I added in the Repair functionality. I intended the Repair feature to function as follows:

1. A User would have a Repair object which would contain the instruments/accessories from the User's inventory that are currently in repair.
2. This Repair object would have the following associations:
   * *has_many* Instruments and *has_many* Accessories
3. The instruments/accessories would then require a *belongs_to* Repairs each.

Thanks to the handy abstraction done by Sinatra, these associations were easy enough to setup. However, the trouble came in the form of managing the routes and rendering the correct webpages. For example, the typical convention for a "edit" PATCH request in Sinatra is:

```
   patch '/repairs/:id' do
      # some code
   end
```

In my case, the ":id" shown in the PATCH request, would be that of the Repair. However, I was also interested in maintaining the ":id" of the particular Instrument/Accessory that was being utilized in this Repair object because I wanted to make modifications to that Instrument/Accessory on the homepage based on what was being edited within this Repair object. If found this difficult to implement through that standard routing techniques and ultimately relied on inserting foreign keys ("instrument_id" and "accessory_id") into the Repair object. 

Ultimately, I was able to obtain the desired functionality for my web app but I believe this was at the expense of effiency in my coding; there were far more lines of code within this RepairsController than it's Instrument/Accessory counterparts. In time, I hope to go back and lean this coding out a bit more. At the moment, I have started learning some basic Rails techniques and am intrigued by the prospect of the "dynamic routes" contained within the framework. 
