---
layout: post
title:      "Ramping Up With Rails"
date:       2019-08-28 03:31:00 +0000
permalink:  ramping_up_with_rails
---


As I (finally) reach the end of this Rails module, I am left acutely aware that while we are already taking in a vast amount of knowledge as we progress through each module, we are then asked to soak up even more information as we attempt to complete the portfolio project. As I've moved through each section of Flatiron's coursework, there has been a common cycle.

1. Struggle slightly to gain an understanding of the core concepts related to that module.
2. Slowly start to get a foothold of the material and gain confidence in my abilities.
3. Arrive at the portfolio project feeling highly accomplished and unstoppable after successfully working through a couple difficult, involved labs.
4. And finally, promptly be reminded by the project that my skills still have a ways to go and there is a large amount of learning left to be done before I am able to finally submit the project.

While this final phase is always a bit discouraging at first, I ultimately come around to the idea that *this* is likely the time when my continued growth into a successful developer is kicked into full gear. I am left with nothing but newfound skills, some reference material, and a plethora of blank files eagerly waiting to be filled with code - the training wheels have been removed. With these tools, I am tasked to find a way to make my ideas for this project become a reality. 

The first two portfolio projects required me to step beyond the material that is covered in the curriculum in order to achieve what I set out to do. However, the amount of additional work from these two projects does not come close to that required by the Rails portfolio project. I was challenged harder than I had yet been challenged in this course and still feel I have yet to scratch the surface of what Rails is capable of. 

Today I'm going to discuss three new personal revelations that came as a result of working through my Rails project. The first two topics discussed challenged me greatly, while the third wasn't quite as challenged but did provide me with a glimpse of the cool capabilities of Ruby on Rails as framework.

## Where Are You Going?!

I'd like to start with what is definitely the least technical of the three topics I'll be discussing but without a doubt a crucial aspect of any project - organization. Up until now, it was possible to get away with not devoting many resources towards the overall organization of how I went about completing my projects...until Rails came along. Much like an overly eager child on Christmas morning tearing into his first present, I jumped headon into my Rails project, creating every resource under the sun that was eventually going to be used.

Before I knew it, I was bouncing from setting up relationships between a couple Models to constructing the user login/signup pages to creating various *new* forms that would be needed at some point. After a couple unproductive hours of this, I realized just how inefficient I was being. At this point I decided to take a step back and think about two things:

1. How can I better structure and track what is complete, what needs to be completed, and what is not yet started?
2. What is the best plan of attack to ensure the project is complete from start to finish without any disconnect between various aspects?

When it comes to the first question, there are countless options in the form of apps, software, sticky notes, etc. Ultimately, I landed on an app called Trello. This app allows the user to create a board with multiple *lists* that contain *cards* in which the user can enter items that need to be worked on for that particular card. I decided to divide my list into "Pending", "In Progress", and "Complete." Within these lists, I created various cards for each resource within my project and divided these cards further into "Functionality" and "Styling." Taking the extra 30 minutes to setup my Trello board was able to stymie the chaotic bouncing around and allowed me to gain a better visual for what needed to be done and the general order in which this should be done.

As for the second question I asked myself, I came to the conclusion that it made no sense to jump right into the project by creating every possible model, view, and controller that would eventually be needed. Having all of these resources to work with made it much easier to jump from one resource to another instead of simply finishing the original resource at hand. Ultimately, I scrapped all I had done and started fresh with only the User resource. From then on, I did not progress to the next resource until all functionality within the current resource was complete. Looking back, this appears to be an obvious course of action - but who doesn't get a little overzealous at the start of a project and want to just start creating all of the things at once?!

## Relationships...Always Testing You

Once the initial portion of my project was out of the way (user authorization/authentication), it was time to move onto the money making part of the project and with that came the fateful *has_many, through*: relationship. Before I dive into my troubles with and how I ultimately utilize *has_many, through*, I'd like to give some quick background on my project. My Rails project was a simple travel itinerary app. Within this app, a user is able to create trips, which will then have associated flights, activities, and accommodations. It was once I arrived at the trips section (containing associations to flight, activities, and accommodations), that I knew a *has_many, through* relationship was required. 

For the purpose of this post, I'll use the Trips and Flights relationship as an example. When I first began looking at the relationship between these two, I thought I'd be able to get away with the following:

```
class Trip < ApplicationRecord
   has_many :flights
end
```
```
class Flight < ApplicationRecord
   belongs_to :trip
end
```

I quickly realized this setup would only be valid if you were the only person taking a specific flight and that flight was solely dedicated to your trip. I don't know much about the rest of you...but I don't think I've ever been on a flight that *isn't* packed to the point of bursting. This simple relationship setup had to be scrapped and it became immediately apparent that *has_many, through* would need to be called upon to get the job done. After some thought, I decided to use a Ticket model as the join table between the two. This left me with the following setup:

```
class Trip < ApplicationRecord
  has_many :tickets
  has_many :flights, through: :tickets
end
```
```
class Flight < ApplicationRecord
  has_many :tickets
  has_many :trips, through :tickets
end
```
```
class Ticket < ApplicationRecord
  belongs_to :trip
  belongs_to :flight
end
```

Through the use of this Ticket, each trip was able to have multiple flights and each flight could be associated with multiple trips, thereby replicating a much more realworld scenario. With this relationship setup, I was able to access all the information within the flights objects from the scope of the current trip (i.e. `current_trip.flights`) and visa versa. The *has_many, through* relationship, while a bit tricky to comprehend initially, enables the user to easily access all the data included with the two resources being associated and allows for a much intricate, realistic user experience.

## Scoping Out My Options

The final topic I'd like to discuss briefly is that of Scopes within Active Record. To quote the holy grail that is the RailsGuides resource documents, scope is a feature "that allows you to specify commonly-used queries which can be referenced as method calls on the association objects or models." In other words, without having to create an entirely separate class or instance methods, the user can define a scope within the model which performs the desired query and returns the queries information when called as if it was a typical object/model method.

I was able to make use of this neat little feature multiple times throughout my app. See below for its use within my Trip model:

```
class Trip < ApplicationRecord
  scope :past_trips, -> { where("end_date < ?", Date.today) }
  scope :upcoming_trips, -> { where("start_date > ?", Date.today) }
end
```	

With the addition of two simple lines of code, I was given the ability to call past and upcoming trips on a given trip and even on a collection of trips (i.e. current_user.trips.past_trips returns a collection of all trips that happened before today's date). I found this to be highly beneficial in cleaning up the models as well as providing quite useful functionality.

As I mentioned towards the very beginning of this post, I am well aware that there is so much I have yet to learn about Rails and each day I spend utilizing the framework, I am struck by the amount of new information that seems to jump out at me. Being able to utilize such an expansive, powerful framework to *actually create something* leaves me with a sense of optimism that I am well and truly on my way to being an actual developer and I couldn't be more excited to see what struggles come next!

Don't be afraid to reach out to my email (cillianwinger@gmail.com) with any questions, comments, concerns, vehement disagreements - I'm more than open for some input to help me continue to grow!

