---
layout: post
title:      "React Form Input Changes: Using Hooks"
date:       2020-03-17 01:44:21 +0000
permalink:  react_form_input_changes_using_hooks
---

### In this post, I will explore a very useful tool when updating forms within a React app, the React `useState()` hook, while also discussing the benefits that let me to choosing this method.

As always, let me start with a brief review of the project I will be using to review these two React concepts. For my final Flatiron bootcamp project (about as bitter sweet as it gets) I decided to build a Songkick clone utilizing a Rails backend and React/Redux frontend. This app allows the user to search for upcoming concerts based on their chosen location and add them to their own personal calendar, ultimately allowing the user to efficiently track concerts they are attending or interested in attending. The app currently utilizes an external Songkick API to pull the relevant concert info but I have high hopes to integrate Spotify API playlist functionality that will link the user to Spotify playlists relevant to artists the user will be seeing in upcoming concerts. Brief introduction done...now let's get to the fun stuff.

The example I will be using to provide code snippets will be the user profile updating functionality (i.e. user sending input to the Rails backend and updating their information if all validations succeed). Pretty basic stuff, however, the lessons learned will be able to be applied to forms throughout any React application. Let's check out what we'll be working with - see below for an image of the form we will be editing:

![](https://i.imgur.com/2TJZZOV.jpg)

As you can see, the form itself is fairly simple. When I first started thinking about how I would get this form to update properly, my mind was *dispatched* to the land of React-Redux (redux humor...nailed it). 

![Alt Text](https://media.giphy.com/media/5gw0VWGbgNm8w/giphy.gif)

This meant that I would have my UserProfile component setup something like below:

```
import React, { useState, useEffect } from 'react'
import { connect } from 'react-redux'
import { updateUserProfile } from '../actions/authForm'

const UserProfile = (props) => {

  # some code
	
  const handleInputChange = (event) => {
    const { name, value } = event.target
    const updatedFormInfo = {
      ...props.profileFormData,
      [name]: value
    }
    updateUserProfile(updatedFormInfo)
  }	
	
	# more code
	 
  const mapStateToProps = (state) => {
    return {
      currentUser: state.currentUser.user,
			profileFormData: state.profileForm
    }
}

export default connect(mapStateToProps, { updateUserProfile })(UserProfile);
```

To quickly breakdown what is going on in the code above:

1. The first three lines are importing React, React-Redux (which allows us to connect to our store and, therefore, our overall state.
2. The code at the bottom of the snippet is what actual sets up our props: it pulls something called `profileForm` from our store and sets it equal to `profileFormData` while also mapping the `updateUserProfile` dispatch method to the props, which would ultimately be used to update the form (by sending the returned function to the action, which would then be passed along to the associated reducer and update the state).
3. The `handleInputChange` method simple updates `props.profileFormData` through the use of the `updateUserProfile` dipatch the calls to its respective action. 

This method provides the benefit of having the user's input immediately stored within state as it is being updated, which can then be used to eventually submit the form and update the user's profile. As I was looking into this option, right away I noticed a few things that I was *not thrilled* about:

1. If the user decides to cancel their edits, the app's state would have already been updated with what had been changed. This is easy enough to clean up with some sort of "reset" profile form method but an extra step nonetheless.
2. The updates to this profile form are only temporarily - only the completed form will be sent to the backend once the user has completed their edits. Do we really need to continually update the app's state to keep track of this form data? This seemed overkill to me. 
3. I had issues figuring out how I would use the user's current information to already have the form populated with the needed info if the user were to first navigate to the edit page. Typically, an initial state is setup within the reducer and I had trouble figuring out how I would feed the user's current info from outside the action/reducer to the relevant reducer to accurately provide an initial profile form state.

After pondering this a while longer, I eventually found myself looking into using those ever-so-nifty React Hooks. After some pretty brief tinkering I ended up with the following code:

```
import React, { useState, useEffect } from 'react'
import { connect } from 'react-redux'

const UserProfile = (props) => {
  const [profileInfo, setProfileInfo] = useState('')
	
	const handleInputChange = (event) => {
    const { name, value } = event.target
    const updatedFormInfo = {
      ...profileInfo,
      [name]: value
    }
    setProfileInfo(updatedFormInfo)
  }
	
	const mapStateToProps = (state) => {
  return {
    currentUser: state.currentUser.user
  }
}

export default connect(mapStateToProps, null)(UserProfile);
```

Breaking down the code above:

1. The import for `updateUserProfile` is no longer needed as this dispatch action will not be used after the changes. This is also reflected in the final line of the code - `mapDispatchToProps` is now set to `null`.
2. We are utilizing the `useState` method provided by the React import. In this instance, we are creating a local state called `profileInfo`  (initially set to `''`). The `setProfileInfo` variable is what will allow us to properly update this local state.
3. And finally, `profileInfo` is the object now being updated as the `handleInputChange` method handles the user's changing input. At the end of `handleInputChange`, `setProfileInfo` is used to update `profileInfo` with the latest changes.

With this relatively minor change, I have refrained from accessing and updating the stored state with the changing user input (which is temporary until submitted), allow the user to cancel their edits and still maintain the original user profile info without issue, and made it much easier to setup the initial form data.

To setup the initial form data, I have the following setup within the `onClick` event handling function that is triggered when a user initially decides to edit their profile:

```
  const handleEditClick = (event) => {
    event.preventDefault()
    setProfileInfo({
      first_name: props.currentUser.first_name,
      last_name: props.currentUser.last_name,
      location: props.currentUser.location,
      about: props.currentUser.about})
  }
```

Once the user clicks the button that triggers a profile edit, the `setProfileInfo` method is called with an object containing the current user's profile information. This then allows the form to already contain existing information so that the user does not have to re-type various fields that would otherwise remain unchanged. And if the user decides to cancel his edits, we again use this `setProfileInfo` method to revert the form data back to the original values:

```
  const handleCancel = (event) => {
    event.preventDefault()
    setProfileInfo({
      first_name: props.currentUser.first_name,
      last_name: props.currentUser.last_name,
      location: props.currentUser.location,
      about: props.currentUser.about})
  }
```

Both of these event handling methods make use of the state we have mapped to props from our store and provide the data we need to effectively update the user's profile.

Through the use of React's `useState` hook, we were able to quickly get our form updated without making unnecessary dispatches to our actions/reducer. I hope this proves useful for those wanting to make more use of React's Hooks, which are quickly growing on me as a critical tool in the React toolbelt. Happy coding!

![Alt Text](https://media.giphy.com/media/eQyPiQGIkaQSI/giphy.gif)

