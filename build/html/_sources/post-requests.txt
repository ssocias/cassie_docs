.. _Post Requests:

Post Requests
*************

Endpoints for posting data (making forecasts, adding friends, creating casties, etc.) can be found below. All data should be sent in the body of the request as key:value pairs of type ``application/x-www-form-urlencoded``.

Each endpoint is expecting certain parameters. I don't always check to make sure these keys exist in the request...So, make sure to include the correct parameters!! 


Sign Up (create account)
========================

The sign up process occurs over a series of steps as the User inputs the various pieces of data needed to create their account. Each step requires a separate request. No authentication required until after the final step and the account has been created.

.. topic:: Abandoning the Account Creation Process

  If the User terminates the account creation process at any time after they have inputted a password, make a request to the following endpoint so any temporary data can be deleted:

    ``POST https://cassieapp.com/api/signup/incomplete``

    **Parameters:**

    **email:** *string*, the User's email address

    **Response:**::

      {
        "status": 200,
        "message": "user data has been deleted"
      }


**1. Email Address**

  Perform basic validation client-side (that it is indeed an email) and then send the email address to the backend to verify that it is not already associated with an account. The email address becomes the User's username.

  **Definition:**

  ``POST https://cassieapp.com/api/signup/email``

  **Parameters (sent as key:value pairs)**

    * **email:** *string*, the validated email address inputted by the User

  **Returns**

  If the email is already in use, an error message will be returned: ::

      {
        "status": 400,
        "error_type": "email_not_unique",
        "error_message": "the email address provided is already associated with a Cassie account. it cannot be used to create a new account."
      }

  If the email is valid, it will be returned: ::

      {
        "status": 200,
        "email": "myemail@gmail.com",
        "message": "email is unique- proceed to the password screen"
      }

**2. Password**

  Perform basic validation client-side to ensure that the two passwords match and that they contain only alphanumeric characters (no spaces). Then, send the email address (provided in the response of the previous request) and password in the request.

  **Definition:**

  ``POST https://cassieapp.com/api/signup/password``

  **Parameters (sent as key:value pairs)**

    * **password:** *string*, the validated password inputted by the User
    * **email:** *string*, the validated email address inputted by the User

  **Returns**

  A success message once the email and password have been used to create an initial User object.

  **Sample Response**: ::

    {
      "status": 200,
      "email": "myemail@gmail.com",
      "username": "myemail@gmail.com",
      "message": "User has been created. proceed to the name screen."
    }

**3. Name and Handle**

  The User inputs their name and then their Handle on distinct sign-up screens. Perform basic validation client-side to ensure that the Names and Handle contain only alphanumberic characters (ASCII characters only). Additionally, the handle must be at least three characters long and cannot contain the following characters: @ \ /. 
  Once this client-side validation is complete, send the handle to the backend so uniqueness can be verified.


  **Definition:**

  ``POST https://cassieapp.com/api/signup/handle``

  **Parameters (sent as key:value pairs)**

    * **first_name:** *string*, the User's first name
    * **last_name:** *string*, the User's last name
    * **handle:** *string*, the User's unique handle; this is used to identify the User
    * **email:** *string*, the email address

  **Returns**

  If the handle is already in use, an error message will be returned: ::

      {
        "status": 400,
        "error_type": "handle_not_unique",
        "error_message": "the hanlde provided is already associated with a Cassie account"
      }

  If the handle is valid, it will be returned: ::

      {
        "status": 200,
        "handle": "myhandle",
        "message": "proceed to the picture screen"
      }

**4. Picture**

  User uploads a profile picture. Images must be jpg, jpeg, png, or gif.

  **Definition:**

  ``POST https://cassieapp.com/api/signup/picture``

  **Parameters (sent as key:value pairs)**

    * **handle:** *string*, the User's unique handle; this is used to identify the User
    * **email:** *string*, the User's email address
    * **profile_picture:** optional (if no profile picture, do not include this field in the request) COMING SOON since I don't know how to handle this yet!

  **Returns**

  A success message.

  The User should then be directed to their Home page. Since the Home page will be empty (as the User has not followed any groups on their own yet), display a message instructing the User to click on the Octopus in the top bar to follow groups and start forecasting.

  **Sample Response**: ::

    {
      "status": 200,
      "email": "myemail@gmail.com",
      "handle": "myhandle",
      "message": "Sign up complete- now show me the home page"
    }

.. _Log In:

Log In
======

Updates the User's "last_login" date/time and returns all data needed to start the app. No additional calls to initialize the app are needed. One request to "Log In" will provide all data needed to display the home page, with notification indications.

**Definition:**

``POST https://cassieapp.com/api/login``

**Parameters**

None 

**Returns**

Data needed to display the home page (same data returned in :ref:`Home`), the number of notifications the user has never seen, and a boolean indicating if the User has any Casties that are ready to be answered.

  * **number_notifications:** *integer*, number of notifications the User has
  * **needs_answer:** *boolean*, True if the User has a Castie(s) that is ready to be answered


**Sample Response** ::

    {
        "status": 200,
        "profile_handle": "steph",
        "number_notifications": 4,
        "needs_answer": false,
        "casties": {
            "6785577f160f45b0989dcee31bd762bf": {
              "answerSubmitted": false,
              "friendCount": 3,
              "createdAtDate": "2015-08-13",
              "group": "Around Campus",
              "uuid": "6785577f160f45b0989dcee31bd762bf",
              "friendPics": [
                "profiles/user-280/image.jpg",
                "profiles/user-159/image_Fbr8GSY.jpg",
                ""
              ],
              "createdAtTime": "03:37:08.153640",
              "submitter": "csocias",
              "showUsername": false,
              "commentsCount": 0,
              "question": "Which company will have the most Q4 revenue?",
              "forecastsCount": 25,
              "setAnswered": false,
              "userForecast": "Best Buy",
              "openEnded": false,
              "forecasts": [
                {
                  "answer_text": "Visa",
                  "percentage": 48
                },
                {
                  "answer_text": "Starbucks",
                  "percentage": 16
                },
                {
                  "answer_text": "American Express",
                  "percentage": 4
                }
              ],
              "endDate": "2015-08-31",
              "correctIndex": null,
              "allowWriteIn": true,
              "endTime": "11:20:00"
            },
        }
    }



Log Out
=======
Nothing needs to be done on the backend


Delete Account
==============

Completely deletes a User's Cassie account.

**Definition:**

``POST https://cassieapp.com/api/profile/delete``

**Parameters**

None 

**Returns**

A success message indicating the User has been deleted. If the User is not deleted because the User cannot be found, an error message is returned. ::

    {
      "status": 400,
      "error_type": "object_not_found",
      "error_message": "the User requested could not be found- nothing could be deleted"
    }


**Sample Response**: ::

  {
    "status": 200,
    "handle_deleted": "myhandle",
    "message": "Account has been deleted"
  }


Make a Forecast
===============

Place a forecast on any active Castie. Forecast can be made when the Castie's end date/time have not yet passed (or it is an open-ended castie). A User can only place one forecast per Castie.

**Definition:**

``POST https://cassieapp.com/api/casties/{uuid}/forecast/``

**Parameters**

  * **uuid:** *string*, unique id for the Castie

  If this is a write-in forecast, send the forecast_text in the request body:

  * **forecast_text:** *string*, the text of the User's write-in forecast

  Otherwise, if it's a forecast for a Castie with set answer optoins only, send the ID of the answer option chosen:

  * **answer_id:** *integer*, the ID of the answer option chosen

**Returns**

A successfull request returns a status 200, the uuid of the Castie, the User's forecast text, and, for a set answer option forecast, the answer_id of the answer option chosen.

For a write-in forecast, the answer_id field is None.

Appropriate errors will be returned in a variety of circumstances-

If the User has already forecasted the Castie: ::

  {
    "status": 400,
    "error_type": "cannot_forecast",
    "error_message": "the User has already forecasted this Castie"
  }

If the Castie is not accepting forecasts: ::

  {
    "status": 400,
    "error_type": "cannot_forecast",
    "error_message": "this castie is not accepting forecasts- end date/time has passed"
  }


If the Castie does not exist: ::

  {
    "status": 404,
    "error_type": "object_not_found",
    "error_message": "the requested castie could not be found. make sure you are sending a valid uuid"
  }

If the request is missing required parameters: ::

  {
    "status": 400,
    "error_type": "cannot_forecast",
    "error_message": "answer_id was not sent in request"
  }

If the answer_id sent in the request does not correspond to an answer option for the Castie: ::

  {
    "status": 400,
    "error_type": "cannot_forecast",
    "error_message": "the answer_id provided does not correspond to a valid answer option for this castie"
  }

**Sample Response**

Set answer option forecast: ::

  {
    "status": 200,
    "message": "forecast successfully recorded",
    "castie_uuid": "f9428a64bf3642cc9e1f64e7314ed9ee",
    "answer_id": 845,
    "user_forecast_text": "Yes"
  }

Write-in forecast: ::

  {
    "status": 200,
    "message": "forecast successfully recorded",
    "castie_uuid": "371083f1c4694d30b8f2de0f3812a3e8",
    "answer_id": null,
    "user_forecast_text": "howdy "
  }

.. _Follow:

Follow a Group
==============

Allows a User to "Follow" a Group so that the Group's Casties appear on the User's home page. This request should only be made if the User is NOT already following the Group. If the User is already following the Group, you should make a request to `Unfollow`_.

Some Groups are private, meaning that Users must be approved in order to follow it and thus forecast its Casties. If the User requests to follow a private Group, their request is pending until it's approved. 

**Definition:** 

``POST https://cassieapp.com/api/groups/{group_slug}/follow/``

**Parameters**

* **group_slug**: *string*, the slug of the group to be followed

**Returns**

If the Group cannot be found, the following error is returned: ::

  {
    "error_type": "object_not_found",
    "error_message": "the requested group could not be found",
    "status": 404
  }

If the Group is not private and the "follow" was successful: ::

  {
    "status": 200,
    "handle": "myHandleHere",
    "group_slug": "groupSlugHere",
    "access": None,
    "following": true
  }

If the Group is private, the request will return a message indicating that the User's access is pending approval: ::

  {
    "status": 200,
    "handle": "myHandleHere",
    "group_slug": "groupSlugHere",
    "access_pending": "user has requested access to group",
    "access": "pending",
    "following": false
  }

If the User is already following the Group: ::

  {
    "status": 400,
    "handle": "myHandleHere",
    "group_slug": "groupSlugHere",
    "error_type": "improper_group_request",
    "error_message": "cannot follow the Group. user is already following"
  }

.. _Unfollow:

Unfollow a Group
================

Unfollows a Group so that the Group's Casties no longer appear on the User's home page. A User must currently be following the Group in order to unfollow it. If the User is not currently following the Group, make the request to `Follow`_.

**Definition:** 

``POST https://cassieapp.com/api/groups/{group_slug}/unfollow/``

**Parameters**

* **group_slug**: *string*, the slug of the group to be un-followed

**Returns**

If the Group cannot be found, the following error is returned: ::

  {
    "error_type": "object_not_found",
    "error_message": "the requested group could not be found",
    "status": 404
  }

If the User is already "not following" the Group: ::

  {
    "status": 400,
    "handle": "myHandleHere",
    "group_slug": "groupSlugHere",
    "error_type": "improper_group_request",
    "error_message": "cannot un-follow the Group. user does not follow it yet"
  }

If the "un-follow" was successful: ::

  {
    "status": 200,
    "handle": "myHandleHere",
    "group_slug": "groupSlugHere",
    "access": None,
    "following": false
  }

.. _Create Castie:

Create Castie
=============

Save a newly created Castie to the database. All details pertinent to the Castie being created must be passed in the body of the request.

**Definition:** 

``POST https://cassieapp.com/api/casties/create/``

**Parameters**

    **Data needed to create a Castie, sent as key:value pairs:**

    * **group_slug:** *string*, the slug of the Group the Castie will belong to
    * **poll_type:** *string*, either "Open" for open-ended Casties or "Regular" for those that have an end date
    * **closes_on_date:** *string*, the date the Castie closes; in the format YYYY-MM-DD
    * **closes_on_time:** *string*, the time the Castie closes; in the format Hour:Minute:Second
    * **question:** *string*, the Castie question text
    * **answer_type:** *string*, either "Set answer options" if the User has inputted answer options or "Write-in answers" if Users can write-in their own
        * **answer_option:** *string*, if answer_type is "Set answer options", include answer_option fields for each answer option provided
    * **display_created_by:** *boolean*, True if the Castie creator's handle should be displayed 


**Returns**

If the Castie is created successfully, the response is: ::

    {
      "status": 200,
      "castie": "created successfully",
      "castie_uuid": "a52560fa75f1403b9e635d01ad364111"
    }

Otherwise, if the Castie was not created, the response looks something like: ::

    {
      "status": 400,
      "error_type": "castie_not_created",
      "error_message": "more detailed info on what went wrong"
    }

That type of error resonpose will be returned for a variety of error conditions. 

Add Frodad
===========

Send a friend request to another User.

**Definition**

``POST https://cassieapp.com/api/add-frodad/{handle}/``

**Parameters**

* **handle:** *string*, the handle of the profile being friend requested

**Returns**

Returns the status of the relationship between the two Users-

If the two Users (the one sending and the one receiveing the friend request) are not yet friends, a success message is returned. ::

  {
    "message": "friend request sent",
    "status": 200
  }

If a friend request between the two Users is already pending: ::

  {
    "status": 400,
    "error_message": "friend request is already pending",
    "error_type": "cannot_add_frodad"
  }

If the two Users are already friends: ::

  {
    "status": 400,
    "error_message": "these Users are already friends",
    "error_type": "cannot_add_frodad"
  }

  {
    "status": 400,
    "error_message": "cannot frodad request yourself",
    "error_type": "cannot_add_frodad"
  }

Accept or Reject a Frodad Request
=================================

Accept or reject an existing friend request. To accept the request, pass "accept" in the URL. To reject the request, pass "reject" in the URL.

**Definition**

``POST https://cassieapp.com/api/frodad-requests/{handle}/{action}/``

**Parameters**

* **handle:** *string*, the handle of the profile being friend requested

* **action:** *string*, either "accept" or "reject"

**Returns**

An appropriate message indicating whether or not the request was successful. ::

  {
    "status": 200,
    "message": "you and steph are now frodads"
  }

  {
    "status": 200,
    "message": "frodad request from steph has been rejected"
  }

Unfrdodad
==========

Un-friend an existing friend.

**Definition**

``POST https://cassieapp.com/api/unfrodad/{handle}/``

**Parameters**

* **handle:** *string*, the handle of the profile being friend requested

**Returns**

A success message: ::

  {
    "message": "csocias has been unfriended",
    "status": 200
  }

Notification Has Been Read
==========================

Forgot Password
===============

Retrieve a forgotten password. This request will send the User an email with a link to reset their password.