---
layout: post
title:      "Getting Rails and JS To Talk About Their Issues"
date:       2020-01-01 20:54:31 +0000
permalink:  getting_rails_and_js_to_talk_about_their_issues
---


**The time has finally come**...I have just completed a project that required implementation of everything I have been learning for the past few months: a cohesive, multi-page web app that utilized a Rails backend and JavaScript on the frontend to dynamically render content and allow for an overall more enjoyable experience. 

![Alt Text](https://media.giphy.com/media/5GoVLqeAOo6PK/source.gif)

For this particular project, I decided to create a travel itinerary app. The primary goal of this app was to allow the creation of trips and, within each of these trips, allow the user to record all of the relevant stops, flights, activities, and accommodations. Ultimately, the user will be able to have a detailed record of past and upcoming trips. Below you can see the landing page once a user signs in!

![](https://i.imgur.com/RCg4Wwl.png)

It is from this page that a majority of the dynamic rendering occurs for this app. The user is able to dynamically render a list of all trips, upcoming trips, or past trips, with each showing various info about each trip (bookings, cost, etc.). No doubt due to the excellent Flatiron curriculum/staff, using JS to render various "index" and "show" pages was not terribly difficult. However, I did run into issues when attempting to render error messages when the user tries to to submit an invalid trip creation (failing any one of my various validations). 

In this post, I will discuss how I was able to dynamically render error messages on my create trip form without refreshing the page.

As part of my dynamic rendering, I decided to use a pop-out modal for my new trip creation, which can be seen below.

![](https://i.imgur.com/FDuSQmD.png)

The main issue that needs to be dealt with when attempting to display errors dynamically is that an object is not created on the backend if validations fail, so that is unable to be sent back to the frontend where it is being requested and used. There needs to be a way of capturing why the frontend did not receive a successful object creation from the backend and return *that* to the frontend. 

We Rails users know that, fortunately, Rails (and more specifically Active Model) provides a modified hash where validation errors are stored in the form of `.errors`. With that knowledge at our disposable, we simply need to be able to send, from our create controller action, this errors hash to the frontend when a validation failure occurs. To do this, I found that I was able to render this errors has in JSON format within the "failed validation" section of my trips#create controller action, which you can see below.

```
  def create
    @trip = Trip.new(trip_params)
    respond_to do |f|
      if @trip.save
        f.html { redirect_to user_trip_path(@user, @trip) }
        f.json { render json: @trip }
      else 
        f.html { render :new }
        f.json { render json: @trip.errors.full_messages, status: 400 }
      end
    end
  end
```

Here I am sending an HTTP 400 "bad request" status to the front end when validation fails, along with the JSON formatted errors hash. This wil ultimately tell the frontend that validation failed, a new object was not created, and we should not proceed with the request in the way that we would have if it was successful. Note, I am rendering `@trips.errors.full_messages` so that only the error messages, not the associated key/value pairs that typically come with validation errors, are sent to the front end.

Now it is time to handle this validation failed JSON data on the frontend. On my JS frontend, I am using Ajax to perform this post request. I needed to be able to handle the data in a certain way if the request was unsuccessful. Please see below for the function that handled this request.

```
function listenForNewTripCreation(userId) {
	$('form#new_trip').submit(function(event) {
		event.preventDefault();
		let values = $(this).serialize();
		$.ajax({
			type: "POST",
			url: `http://localhost:3000/users/${userId}/trips`,
			data: values,
			dataType: "json",
			success: function(data) {
				let newTrip = new Trip(data)
				$('div.row').html("");
				$('div.js-render-content').html("");
				$('div.js-render-content').html(newTrip.newTripShow());
				$('#tripModal').modal('hide');
			},
			error: function(data) {
				let errors = data.responseJSON
				$('div#new-modal-div').prepend(modalErrors(errors))
				listenForNewTripCreation(userId);
			}
		})
	})
	eventListeners();	
}
```

Within the `error` section of the Ajax request, which is what will be used triggered due to the 400 status on the backend, there is one critical line: `let errors = data.responseJSON`. In this line, I am setting a variable `errors` equal to the JSON portion of the data  that was returned from the backend; that is, the variable `errors` is being set to the hash containing the full error messages that were returned by `@trip.errors.full_messages` on the backend.

From here it is a matter of simply adding these errors to the modal that remained open on the page (yay dynamic rendering). I handled this through a simple function that returns a string of html that formats the error messages on the page how I would like; however, there are multiple ways you could achieve the same result so I will leave this portion out so as to not spoil the fun!

In terms of lines of code, there is not much to this solution. However, I found it somewhat difficult to keep track of what was being sent between the frontend and backend during the request. It took some playing around but ultimately I was able to figure it out and the errors are now rendering dynamically, leaving the user non-the-wiser that all of this is going on under the hood!
