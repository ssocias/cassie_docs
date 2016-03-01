Get Requests
************

Here you will find the list of possible GET requests. A valid username and password must be included in the body of every request. See the :ref:`Authentication` and :ref:`Errors` sections for more information on handling authentication and errors.

A User's username is the email address associated with their account. The email/username is unique across all Users. Additionally, we use the "handle" attribute to uniquely identify Profiles.

.. _Initialize:

Initialize
==========

This must be called anytime a User opens the app. It returns the number of notifications the User has not yet seen and indicates if the User has Casties that can be answered.

In order to get the number of notifications, the date of the most recent notification the user has seen should be sent in the request.

Before/after making this request, make a request to `Home`_ to get the home Castie data. 

.. note:: **Periodaclly Updating Notifications Count**

  You need to make periodic requests to this "Initialize" endpoint in order to alert the User when they have a new notification while they are using the app. 


**Definition:** 

``GET https://cassieapp.com/api/initialize/``

**Returns**

  * **number_notifications:** *integer*, number of notifications the User has
  * **needs_answer:** *boolean*, True if the User has a Castie(s) that is ready to be answered
  * **number_needing_answer:** *integer*, number of Casties needing to be answered

**Sample Response** ::

    {
      "status": 200,
      "number_notifications": 7,
      "needs_answer": true,
      "number_needing_answer": 3
    }


Needs Answer
============

A boolean to denote if the User has a Castie(s) that can be answered. This does not check Open-ended casties. It only returns data for set end date casties.

**Definition:** 

``GET https://cassieapp.com/api/casties/needsanswer/``

**Arguments**

None

**Returns**

``True`` is the User has non-open-ended Casties that need to be answered. Otherwise, it returns ``False``. Also returns the number of non-open-ended Casties needing to be answered.

**Sample Response** ::

    {
      "status": 200,
      "needs_answer": True,
      "number_needing_answer": 3
    }


.. _Home:

Home
====
Returns a dictionary of Castie objects for the Cassie home page. The home page (accessed via the Home tab on the footer nav bar) is divided into three tabs- "following", "spicy", and "new". By default, this request returns data for the "following" tab unless otherwise specified in the URL. 

This response takes a very long time. To cut back on load time, a list of Castie UUIDs may be requested in place of a list of Castie objects. By including an "ids" parameter in the request, a list of Castie UUIDs will be returned. Each Castie object can then be retrieved by making subsequent requests to ``GET https://cassieapp.com/api/casties/{uuid}/``

**Definition:** 

``GET https://cassieapp.com/api/home/{which_tab}/``

**Arguments**

* which_tab (*optional*, default="following"): *string*, indicates which tab of the home page is being requested- "following", "spicy", or "new"

**Parameters**

* ids (*optional*): *string*, set this to 'true' to return only a list of Castie UUIDs, otherwise do not include this parameter in the request
* groups (*optional*): *string*, the group slug to which the Casties being returned must belong 


.. note:: **Filtering Groups from the Side Menu**

  The side hamburger menu of the home page can be used to filter which Group's Casties should be shown on the "following" tab. By default, Casties for all Groups the User follows are shown. 

  When the User has filtered by Groups in the side bar, those Groups should be passed in the request query string using the 'groups' parameter and the group's slug as the value.

  Sample URL: 

  ``https://cassieapp.com/api/home/following/?groups=politics&groups=music``

  If the User has de-selected all Groups (i.e. does not want to show any Casties), pass the 'groups' parameter in the request but leave the value blank (ex. ``/?groups=``).

.. note:: **Returning Castie IDs Only**

  Instead of requesting full Castie details at this point, an "ids" parameter may be included in the request to return only a list of Castie UUIDs. This makes the request much faster. Any of the original parameters (i.e. filtering by "groups" using the "groups" parameter) can still be used.

  Sample URL: 

  ``https://cassieapp.com/api/home/following/?ids=true``

  Sample Response ::

      {
        "profile_handle": "steph",
        "status": 200,
        "castie_ids": [
          "5af84f2f80c5460d9940f1f91c8caae6",
          "d22c242ac27e4e5591db39880d2ad450",
          "77306de040c247e9bffb140b650e0689",
          "ec14686bc80c4d54bab69fed27902eef",
          "d43676b36a364327a953f557fd279173",
          "f98c8cac32184e5f93dda3f42d57f7b0",
          "133cae1c81ef4f24901651acab8171af",
          "687aaf49af1a43a4876b70e35b577c52",
          "7d9c4797f2f54348bbd2971e379f7462",
          "82c104c6b0dd423f99aeabb1322fdb34",
          "d7329cc1a3ef4522933b867151ec9906",
          "db1db36469f54ea3a0433a2e8421ed60"
        ]
      }

**Returns**

Returns a dictionary, entitled 'casties', of Castie objects to be displayed on the homepage. Casties are indexed by uuid. If "ids" was included in the request, only a list of Castie UUIDs will be returned. If "following" was specified, the returned dictionary of Casties is sorted by most recently forecasted and includes only Casties in groups the User follows. If "spicy" was specified, Casties are ordered by most recently forecasted (not limited only to Groups the User follows). If "new" was specified, Casties are ordered by most newly created (not limited only to Groups the User follows).

If the "following" tab has been specified but the User is not following any groups, there is no Castie data to display. The response will then include a ``following`` key that is set to ``False``, and an appropriate message should be displayed to the User instucting them to Follow groups- "You need to follow gropus before you can see Casties. Click on that sweet Octopus up top to get going."

Sample response for a User that has selected the "Following" tab but is not following any groups::

    {
      "following": false,
      "status": 200
    }

If, however, the 'castie' dictionary is returned empty, that means there are no Casties that meet the criteria requested. An appropriate message should be displayed- "Oh snap, there aren't any Casties. You should go create one of your own."


.. _Castie Object:

**The Castie Object**

    **Attributes**

    * **uuid:** *string*, unique id for the Castie
    * **question:** *string*, question text 
    * **groupSlug:** *string*, unique slug of the group to which the Castie belongs
    * **showUsername:** *boolean*, ``True`` if the Castie creator's handle should be displayed
    * **submitter:** *string*, handle of the User that created the Castie
    * **createdAtDate:** *string*, date (YYYY-MM-DD) the Castie was created
    * **createdAtTime:**  *string*, time (HH:MM:SS.mmmmmm) the Castie was created
    * **lastForecastedDate:** *string*, date (YYYY-MM-DD) the Castie was last forecasted
    * **lastForecastedTime:**  *string*, time (HH:MM:SS.mmmmmm) the Castie was last forecasted
    * **allowWriteIn:** *boolean*, ``True`` if the Castie allows write-in forecasts
    * **setAnswered:** *boolean*, ``True`` if the Castie consists of set answer options
    * **openEnded:** *boolean*, ``True`` if the Castie has no end date
    * **endDate:** *string*, date (YYYY-MM-DD) the Castie closes; ``None/null`` if this is an open-ended Castie
    * **endTime:**  *string*, time (HH:MM) the Castie closes; ``None/null`` if this is an open-ended Castie
    * **userForecast:** *dictionary*, this dictionary contains data about the User's forecast. If the User did not forecast this Castie, this dictionary does not exist. Keys in the dictionary include:

      * **forecast_text:** *string*, the text of the forecast
      * **forecast_id:** *integer*, the unique id of the forecast object
      * **is_correct:** *boolean*, designates if the User's forecast was correct or not (if the Castie has not been answered, this will still be False)
      * **answer_id:** *integer*, if this forecast is for a Castie that has set answer options, this answer_id is the unique id of the answer option chosen. If the Castie consists of write-in answers only, this field does not exist.

    * **is_answerable:** *boolean*, indicates if the Castie can be answered
    * **friendCount:** *integer*, number of friends that have forecasted the Castie
    * **commentsCount:** *integer*, number of comments left on the Castie
    * **answerSubmitted:** *boolean*, ``True`` if the Castie has been answered
    * **correctIndex:** *string*, correct answer text; ``None/null`` if no answer has been provided
        * **NOTE:** if the Castie allowed write-in forecasts, the 'correctIndex' is the keyword the forecast must contain in order to be considered correct. If the Castie did not allow write-in answers, 'correctIndex' is the text of the correct forecast.
    * **forecastsCount:** *integer*, number of total forecasts for the Castie
    * **forecast_options:** *list*, list of dictionaries for every forecast option
        **Forecasts Dictionary Attributes:**
            * **answer_id**: *integer*, for Casties that have set answer options, every Forecast corresponds to one of those answer options. This 'answer_id' is the unique ID for the forecasted answer option. If the Castie had only write-in answers, this field does not exist and the forecasts can be grouped using the 'answer_text' field.

            * **answer_text**: *string* text of the forecast 

            * **percentage**: *integer*, percent of users that have made this forecast

            * **is_correct**: *boolean*, if the Castie has been answered and the forecast has been designated correct, this key exists in the dictionary and is set to ``True``. Otherwise, this key does not exist.

**Sample Response** ::

    {
        "status": 200,
        "profile_handle": "steph",
        "casties": {
            "929559bf4dba4049b01efa673b8b85bf": {
              "answerSubmitted": true,
              "is_answerable": false,
              "friendCount": 3,
              "createdAtDate": "2015-08-12",
              "groupSlug": "around-campus",
              "uuid": "929559bf4dba4049b01efa673b8b85bf",
              "friendPics": [
                "profiles/user-159/image_Fbr8GSY.jpg",
                "profiles/user-11/image_2M3365a.jpg",
                ""
              ],
              "createdAtTime": "03:37:24.295700",
              "submitter": "csocias",
              "showUsername": false,
              "question": "How many students will join Cassie after the first week of school?",
              "forecastsCount": 23,
              "setAnswered": true,
              "userForecast": {
                "is_correct": false,
                "answer_id": 827,
                "forecast_text": "greater than 50",
                "forecast_id": 1853
              },
              "openEnded": false,
              "forecasts": [
                {
                  "answer_id": 827,
                  "answer_text": "between 10 and 50",
                  "percentage": 21.73913043478261,
                  "is_correct": true
                },
                {
                  "answer_id": 828,
                  "answer_text": "greater than 50",
                  "percentage": 78.26086956521739
                }
              ],
              "endDate": "2015-08-21",
              "correctIndex": "between 10 and 50",
              "allowWriteIn": false,
              "endTime": "23:00"
            },
            "6785577f160f45b0989dcee31bd762bf": {
              "answerSubmitted": false,
              "is_answerable": true,
              "friendCount": 3,
              "createdAtDate": "2015-08-13",
              "groupSlug": "around-campus",
              "uuid": "6785577f160f45b0989dcee31bd762bf",
              "friendPics": [
                "profiles/user-280/image.jpg",
                "profiles/user-159/image_Fbr8GSY.jpg",
                ""
              ],
              "createdAtTime": "03:37:08.153640",
              "submitter": "csocias",
              "showUsername": false,
              "question": "Which company will have the most Q4 revenue?",
              "forecastsCount": 25,
              "setAnswered": false,
              "userForecast": {
                "is_correct": false,
                "answer_id": 827,
                "forecast_text": "Best Buy",
                "forecast_id": 1853
              },
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
              "endTime": "11:20"
            },
        }
    }

Castie Detail
=============

Returns detailed information about a given Castie. This should be called anytime a User clicks on a specific Castie to view its information.

**Definition:** 

``GET https://cassieapp.com/api/casties/{uuid}/``

**Arguments**

* uuid: *string*, the unique id of the castie

**Returns**

Returns a Detailed Castie Object (expands upon the basic `Castie Object`_ returned as part of the `Home`_ request). In addition to basic Castie details, this Castie object contains information on Friends forecasts (i.e. what each of the User's friends forecasted).

If the requested Castie cannot be found, the response will indicate a ``404 Error``::

  {
    "status": 404,
    "error_message": "the requested castie could not be found. make sure you are sending a valid uuid",
    "error_type": "object_not_found"
  }

.. _Detailed Castie Object:

**The Detailed Castie Object** 

This object is the same as `Castie Object`_  described above in the `Home`_ section, but instead of a ``friendPics`` attribute, there is a ``friendForecasts`` attribute that lists each friend and their forecast.

* **friendForecasts:** *dictionary* a dictionary of dictionaries indexed by the forecast ``id`` of every forecast made by a User's friend

    **friendForecasts Dictionary Attributes:**
      * **handle**: *string*, the friend's handle

      * **forecast_text**: *string*, the friend's forecast text

      * **answer_id**: *integer*, If the Forecast is for a Castie with pre-defined answer options, this is the ID of the Answer option chosen

        Whenever a User forecasts a Castie, a new Forecast object is created. For pre-defined Casties, this Forecast object is tied to an Answer object. There is one Answer object for each answer option on the Castie. There may be X number of Forecast objects for any of the Answer objects.

      * **is_correct**: *boolean*, if the Castie has ended and the forecast was correct, this key exists in the dict and is set to ``True``. Otherwise, the key does not exist.

      **Example:** ::

        "friendForecasts": {
          "222": {
            "handle": "Lizzyswanson",
            "forecast_text": "Female",
            "answer_id": 209,
            "is_correct": true
          },
          "343": {
            "handle": "user-18",
            "answer_id": 210,
            "forecast_text": " Male"
          },
          "45": {
            "handle": "jennysmith",
            "forecast_text": "Female",
            "answer_id": 209,
            "is_correct": true
          }
        },

**Sample Response** ::

  {
    "status": 200,
    "castie": {
      "friendForecasts": {
        "34": {
          "handle": "Lizzyswanson",
          "forecast_text": "Female",
          "answer_id": 209,
          "is_correct": true
        },
        "323": {
          "handle": "user-18",
          "answer_id": 210,
          "forecast_text": " Male"
        },
        "44": {
          "handle": "jennysmith",
          "forecast_text": "Female",
          "answer_id": 209,
          "is_correct": true
        }
      },
      "correctIndex": "Female",
      "createdAtTime": "05:29:28.893629",
      "groupSlug": "pop-culture",
      "userForecast": " Male",
      "openEnded": true,
      "endDate": "open",
      "setAnswered": true,
      "createdAtDate": "2015-04-07",
      "showUsername": false,
      "answerSubmitted": true,
      "friendCount": 9,
      "question": "What will be the royal baby's gender?",
      "endTime": "open",
      "forecastsCount": 14,
      "forecasts": [
        {
          "answer_id": 827,
          "answer_text": "Female",
          "is_correct": true,
          "percentage": 64.28571428571429
        },
        {
          "answer_id": 828,
          "answer_text": " Male",
          "percentage": 35.714285714285715
        }
      ],
      "allowWriteIn": false,
      "submitter": "csocias"
    }
  }


Leaders
=======

Data for the Leaderboard pages. If no *group_slug* attribute is passed in the URL, data for the "overall" leaderboard is returned. If a *group_slug* is included, returns leaderboard data for that group.

The number of top Users to be returned can be specified using the "limit" parameter in the GET request. If "limit" is not specified, the top 150 Users for the Leaderboard requested are returned by default.

.. note:: **Filtering by Frodads**
  
  Leaderboards can be filtered to show only the User's friends. This filtering hould be done client side. The request to `/leaderboard/{group_slug}` will return data for all Users in the leaderboard. 

  (Let me know if we should do this differently...)

**Definition:** 

``GET https://cassieapp.com/api/leaderboard/{group_slug}/``

**Arguments**

* group_slug (*optional*): *string*, indicates which Group's leaderboard to return. If no group_slug is given, will return data for the overall Cassie leaderboard

**Parameters (sent as key:value pairs in the request)**

  * **limit (*optional*):** *integer*, number of profiles to return; defaults to 150

**Returns**

A dictionary with a "leaderboard_groups" list and a "leaderboard_profiles" dictionary. "leaderboard_groups" is a list of dictionaries containing the Group Name and Group Slug of all groups the User follows. "leaderboard_profiles" is a list of Users ordered by highest to lowest ranked in the Leaderboard.
Each User object in the list contains:

    * **handle:** *string*, the user's handle; uniquely identifies the friend
    * **lastName:** *string*, the user's last name 
    * **firstName:** *string*, the user's first name 
    * **profPic:** *string*, location of the friend's profile picture
    * **city:** *string*, the User's city
    * **state:** *string*, the User's state
    * **level:** *integer*, the User's level (used for Overall Leaderboard)
    * **xp:** *integer*, the User's number of experience points (used for Group specific leaderboards)

.. note:: **Level vs Points**
  
  Although all Leaderboards are ranked by experience points, only the Group Specific Leaderboards will display each User's "xp" (experience points). The Overall Leaderboard should display a User's "level".

**Sample Resopnse**

**Group Specific Leaderboard** (Overal Leaderboard is the same except "xp" would be "level") ::

  {
    "status": 200,
    "leaderboard_groups": [
      {
        "slug": "Politics",
        "groupName": "politics"
      },
      {
        "slug": "Basketball",
        "groupName": "bball"
      }
    ],
    "leaderboard_profiles": [
      {
        "handle": "steph",
        "profPic": "profiles/user-2/socias_photo_wp1ENod.jpg",
        "firstName": "Stephanie",
        "city": "Boston",
        "xp": 700,
        "lastName": "Socias",
        "state": "MA"
      },
      {
        "handle": "csocias",
        "profPic": "profiles/user-4/image_QPZAEEG.jpg",
        "firstName": "Christina",
        "city": "New York",
        "xp": 0,
        "lastName": "Socias",
        "state": "NY"
      }
    ]
  }


Profile
=======
The Profile tab contains four main subdividions: `My Casties`_, `Stats`_, `Groups`_, and `Frodads`_. These subdivisions are visible under the User's basic profile data (picture, name, location, etc.). When a User clicks on any of these subdivisions, the top part of the screen remains the same while the bottom part "switches" out to reveal the appropriate data. **To view another User's profile, place the handle of the profile to be viewed in the URL. To view your own profile, place your own handle in the URL.**

If the requested profile cannot be found, the following response is returned::

  {
    "status": 404,
    "error_type": "object_not_found",
    "error_message": "the requested user could not be found"
  }

If the requested profile is inactive, the following response is returned ::

  {
    "status": 404,
    "error_type": "inactive_user",
    "error_message": "the profile you requested is not active"
  }

Profiles are uniquely defined by both their ``handle`` and ``user_uuid`` attributes. ``user_uuid`` is only used for the :ref:`notifications` API.

There are four GET endpoints for this profile screen, corresponding to the four subdivisions. Every endpoint contains the same basic profile data, followed by the subdivision-specific data. The basic profile data consists of a `Profile Object`_.

.. warning:: Users may designate their Profile as private. If a User's profile is private, the "is_private" boolean will be ``true``. In this case, the server will only return `Profile Object`_ data (the value for the myCasties, frodads, groups, or stats dictionary will be returned as the string ``private``). The privacy applies to friends and non-friends alike.

.. _Profile Object:

**The Profile Object**

    **Attributes**

    * **handle:** *string*, unique identifier for the profile (each User selects their own handle)
    * **user_uuid:** *string*, unique identifier for the profile; random 32 character string
    * **firstName:** *string*, first name of the user
    * **lastName:** *string*, last name of the user
    * **city:** *string*, city
    * **state:** *string*, state
    * **profPic:** *string*, location where the User's profile picture is stored
    * **level:** *integer*, the profile's level
    * **adjective:** *string*, part of the User's ranking title (ranking title can include an adjective followed by a noun- ex. "rookie benchwarmer" )
    * **noun:** *string*, part of the User's ranking title (ranking title can include an adjective followed by a noun- ex. "rookie benchwarmer" )
    * **is_private:** *boolean*, indicates wheter profile data should be visible to non-friends

      * If ``True``, do not show any data below the four subdivision numbers. Instead, display a message saying "This account is private."
    * **percentCorrect:** *integer*, percentage of correct forecasts
    * **myCastiesNumber:** *integer*, number of Casties the User has created
    * **groupsNumber:** *integer*, number of Groups the User follows
    * **frodadsNumber:** *integer*, number of Frodads the User has
    * **forecastsNumber:** *integer*, number of Forecasts the User has made
    * **friend_status:** *string*, indicates the relationship of the profile being returned to the authenticated user. this field will be one of five options-
        * **self**: the authenticated User and the profile being requested are the same
        * **friend**: the authenticated User and the profile being requested are friends
        * **pending**: the authenticated User has sent a friend request to the profile; friendship awaiting the profile's response
        * **respond**: the profile has sent a request to the authenticated User; the autheticated User can click this button to accept/reject the request
        * **not-friends**: the authenticated User and the profile being requested are not friends

-----------
My Casties
-----------

This is the "main" Profile subsection shown when "Profile" is selected from the bottom nav bar. It contains information on all Casties the User has created, including whether or not the Castie is ready to be answered. A Castie is ready to be answered if the Castie end date has passed. 

.. warning:: When looking at another user's profile (not your own), the "Answer" button for the individual Casties the user has created should NOT be visible.

**Definition:** 

``GET https://cassieapp.com/api/profile/{handle}/mycasties/``

**Arguments**

* handle: *string*, the handle of the profile to be viewed

**Returns**

A dictionary of dictionaries, with the "profileInfo" entry mapping to a dictionary of the basic profile data and the "myCasties" entry mapping to a dictionary of Casties indexed by ``uuid``. Each Castie in the myCasties dictionary contains a "question" attribute and an "answerable" attribute. "answerable" is set to ``True`` if the Castie is ready to be answered.

**Sample Response** ::

  {
    "profileInfo": {
      "state": "MA",
      "lastName": "Socias",
      "firstName": "Stephanie",
      "handle": "steph",
      "user_uuid": "dfgo9e8b733700981f14cccd39cd8462",
      "profPic": "profiles/user-2/socias_photo_wp1ENod.jpg",
      "is_private": false,
      "city": "Boston"
    },
    "myCasties": {
      "1c68c6227af14adcae3aece67ac42c64": {
        "question": "asdf",
        "answerable": true
      },
      "43b9bb04a4d24126ab16a558250cbafe": {
        "question": "Who will win the Boston Marathon?",
        "answerable": true
      },
      "e96c251cf07d45a6a4bae3d620513bd9": {
        "question": "NC State v. Louisville",
        "answerable": true
      },
      "6ce0072752ae4026acad97a8ce96ffb3": {
        "question": "Who will win the Masters?",
        "answerable": true
      },
    }
  }

-----
Stats
-----

The Stats subdivision includes two stats, percent correct and percent incorrect, followed by a dictionary of all forecasts placed by the User.

**Definition:** 

``GET https://cassieapp.com/api/profile/{handle}/stats/``

**Arguments**

* handle: *string*, the handle of the profile to be viewed

**Returns**

A dictionary of dictionaries, with the "profileInfo" entry mapping to a dictionary of the basic profile data and the "myForecasts" entry mapping to a dictionary of forecast objects indexed by the ID of the forecast.  

.. _Forecast Object:

**The Forecast Object**

    **Attributes**

    * **question:** *string*, the question text of the Castie being forecasted
    * **uuid:** *string*, unique id for the Castie
    * **forecast:** *string*, the text of the User's forecast for the Castie
    * **is_active:** *boolean*, ``True`` if Castie is still open for forecasting (i.e. end date/time have not passed)
    * **answerSubmitted:** *boolean*, indicates if the correct answer has been submitted. this field is only present if the Castie has ended (``is_active`` would be ``False``)
    * **is_correct:** *boolean*, if the castie has ended, this indicates if the User's forecast was correct- ``True`` if correct, ``False`` if incorrect. this field is only present if the Castie has ended.
    * **points_earned**: *integer*, number of points the User was awarded if they were correct
    * **endDate:** *string*, date (YYYY-MM-DD) the Castie closes; ``None/null`` if this is an open-ended Castie
    * **endTime:**  *string*, time (HH:MM) the Castie closes; ``None/null`` if this is an open-ended 

**Sample Response** ::

  {
    "profileInfo": {
      "profPic": "profiles/user-2/socias_photo_wp1ENod.jpg",
      "friend_status": "self",
      "is_private": false,
      "city": "Boston",
      "myCastiesNumber": 138,
      "groupsNumber": 1,
      "firstName": "Stephanie",
      "percentCorrect": 63.1578947368421,
      "state": "MA",
      "forecastsNumber": 210,
      "handle": "steph",
      "user_uuid": "dfgo9e8b733700981f14cccd39cd8462",
      "frodadsNumber": 41,
      "lastName": "Socias"
    },
    "myForecasts": {
      "6": {
        "endTime": "22:36",
        "endDate": "2015-12-09",
        "question": "Who will become the next prez?",
        "uuid": "8b2f08bb4cd64c98bab5e87efdf32b24",
        "is_active": false,
        "forecast": "Joe",
        "answerSubmitted": false
      },
      "111": {
        "endTime": null,
        "answerSubmitted": true,
        "question": "Tampa Bay Lightning v. Detroit Red Wings in the playoffs",
        "uuid": "8b2f08bb4cd64c98bab5e87ef98u2b24",
        "endDate": null,
        "is_correct": true,
        "is_active": false,
        "forecast": "Lightning"
      },
      "88": {
        "question": "Will Zayn Malik ever return to One Direction?",
        "uuid": "8b2f08bb4cd64c98bab5e87efdf32b24",
        "endDate": null,
        "endTime": null,
        "is_active": true,
        "forecast": "lol no"
      },
      "342": {
        "endTime": "20:49",
        "answerSubmitted": true,
        "question": "Kentucky v. Wisconsin",
        "uuid": "8b2f08bb4cd64c98bab5e87efdf32b24",
        "endDate": "2015-04-04",
        "is_correct": false,
        "is_active": false,
        "forecast": "Kentucky"
      }
    }
  }

.. _groups:

------
Groups
------

The Groups subdivision contains a listing of all Groups the User follows.

**Definition:** 

``GET https://cassieapp.com/api/profile/{handle}/groups/``

**Arguments**

* handle: *string*, the handle of the profile to be viewed

**Returns**

A dictionary of dictionaries, with the "profileInfo" entry mapping to a dictionary of the basic profile data and the "myGroups" entry mapping to a dictionary of Groups the user follows indexed by the slug of the group name. The items in the "myGroups" dictionary are Group Objects (see the `Group Object`_ in the general Groups section below).

**Sample Response** ::

  {
    "profileInfo": {
      "myCastiesNumber": 138,
      "state": "MA",
      "firstName": "Stephanie",
      "is_private": false,
      "percentCorrect": 63.1578947368421,
      "frodadsNumber": 41,
      "lastName": "Socias",
      "forecastsNumber": 210,
      "profPic": "profiles/user-2/socias_photo_wp1ENod.jpg",
      "handle": "steph",
      "user_uuid": "dfgo9e8b733700981f14cccd39cd8462",
      "city": "Boston",
      "groupsNumber": 3,
      "friend_status": "self"
    },
    "myGroups": {
      "politics": {
        "accessDeniedMessage": "",
        "access": "granted",
        "followersCount": 58,
        "friendsCount": 6,
        "groupIcon": "category-bkgds/palm_trees.jpg",
        "slug": "general-stuff",
        "following": true,
        "requiresApproval": true,
        "accuracy": 0,
        "groupInfoText": "",
        "groupName": "General Stuff"
      },
      "pop-culture": {
        "accessDeniedMessage": "",
        "access": null,
        "followersCount": 1,
        "friendsCount": 0,
        "groupIcon": "category-bkgds/pop_culture_final.png",
        "slug": "pop-culture",
        "following": true,
        "requiresApproval": false,
        "accuracy": null,
        "groupInfoText": "",
        "groupName": "Pop Culture"
      }
    }
  }

.. _Frodads:

-------
Frodads
-------

The Frodads subdivision consists a listing of the User's friends.

**Definition:** 

``GET https://cassieapp.com/api/profile/{handle}/frodads/``

**Arguments**

* handle: *string*, the handle of the profile to be viewed

**Returns**

A dictionary of dictionaries, with the "profileInfo" entry mapping to a dictionary of the basic profile data and the "myFrodads" entry mapping to a dictionary of Frodad objects indexed by ``handle``.

.. _Frodad Object:

**The Frodad Object**

    **Attributes**

    * **handle:** *string*, the user's handle; uniquely identifies the friend
    * **lastName:** *string*, the user's last name 
    * **firstName:** *string*, the user's first name 
    * **profPic:** *string*, location of the friend's profile picture
    * **friend_status:** *string*, indicates if this user is the requesting user's friend- possible values are "friends", "not-friends", "pending", "respond", and "self"
    * **level:** *integer*, the User's level

**Sample Response** ::
  
  {
    "profileInfo": {
      "percentCorrect": 63.1578947368421,
      "state": "MA",
      "is_private": false,
      "forecastsNumber": 210,
      "city": "Boston",
      "lastName": "Socias",
      "handle": "steph",
      "user_uuid": "dfgo9e8b733700981f14cccd39cd8462",
      "frodadsNumber": 41,
      "myCastiesNumber": 138,
      "firstName": "Stephanie",
      "profPic": "profiles/user-2/socias_photo_wp1ENod.jpg",
      "groupsNumber": 3,
      "friend_status": "self"
    },
    "myFrodads": {
      "Fmswizard": {
        "profPic": "profiles/user-50/image.jpg",
        "handle": "Fmswizard",
        "firstName": "Fernando",
        "lastName": "Socias",
        "friend_status": "friends",
        "level": 31
      },
      "majesty227": {
        "profPic": "",
        "handle": "majesty227",
        "firstName": "Patti",
        "lastName": "Alvarez",
        "friend_status": "pending",
        "level": 3
      },
      "JAldersonSmith-280": {
        "profPic": "profiles/user-280/image.jpg",
        "handle": "JAldersonSmith-280",
        "firstName": "James",
        "lastName": "Alderson Smith",
        "friend_status": "not-friends"
        "level": 21
      }
    }
  }

Groups
======
Returns a dictionary of all Group objects indexed be the Group's slug attribute. This request is called when the User clicks on the "Groups" button (i.e. the Octopus icon) in the top nav bar. 

.. note:: To return ONLY a list of group slugs for the given user, use this endpoint: ``GET https://cassieapp.com/api/profile/{handle}/groups/?slugs_only`` 


.. note:: To return information on only one group at a time, use this endpoint: ``GET https://cassieapp.com/api/groups/{group_slug}/?casties=`` By default, this request will also include a list of all Casties within the Group. Set the 'casties' parameter to False to omit this list and only return the Group Object.

  This request will also return two additional attributes to the Group Object: **ranking** and **totalRanked**, which are both integers to indicate where the User is ranked within the Group (if no ranking, '0' is returned).


**Definition:** 

``GET https://cassieapp.com/api/groups/``

**Arguments**

None

**Returns**

Returns a dictionary, entitled 'groups', of Group objects. Groups are indexed by the Group's slug (the slug is a shortened form of its name). The Group objects are not ordered in any way.

.. _Group Object:

**The Group Object**

    **Attributes**

    * **slug:** *string*, a shortened Group name, used as a unique identifier for the Group
    * **groupName:** *string*, the name of the group
    * **groupInfoText:** *string*, a short text string providing info on the group
    * **groupIcon:** *string*, location of the Group's icon image
    * **requiresApproval:** *boolean*, if the Group requires approval before it can be followed, this is set to ``True``
    * **accessDeniedMessage:** *string*, if the Group requires approval before it can be followed, this is the message that shoudl be displayed when the User clicks the "Follow" button
    * **followersCount:** *integer*, number of Users that "Follow" the given group
    * **friendsCount:** *integer*, number of the User's friends that "Follow" the given group
    * **accuracy:** *integer*, percent correct for the User's forecasts in the Group
    * **access:** *string*, set to either "granted", "pending", or ``None/null``. "granted" indicates that the User has been approved. "pending" indicates that the User has requested access but has not yet been approved. If the User is "pending", the "following" field is ``False``.
    * **following:** *boolean*, ``True`` if the User is following the Group, ``False`` if the User is not following the Group
    * **canCreateCastie:** *boolean*, ``True`` if the User has permission to create Casties in the Group
    * **numberCasties:** *integer*, number of total casties in the Group

**Sample Response** ::

  {
    "status": 200,
    "groups": {
      "uf-weekly-matchups": {
        "access": null,
        "friendsCount": 0,
        "requiresApproval": false,
        "slug": "uf-weekly-matchups",
        "accuracy": null,
        "groupIcon": "category-bkgds/uf1.jpg",
        "following": false,
        "followersCount": 0,
        "groupInfoText": "",
        "groupName": "UF Weekly Matchups",
        "accessDeniedMessage": "",
        "numberCasties": 14
      },
      "academy-holy-names": {
        "access": "granted",
        "friendsCount": 6,
        "requiresApproval": true,
        "slug": "academy-holy-names",
        "accuracy": null,
        "groupIcon": "category-bkgds/AHN.jpg",
        "following": true,
        "followersCount": 58,
        "groupInfoText": "",
        "groupName": "Academy of the Holy Names",
        "accessDeniedMessage": "<h3>Do you even go here?</h3>\r\n\r\n<p>This is a private group for AHN students only.</p>",
        "numberCasties": 94
      }
    }
  }

---------
Followers
---------

View a listing of followers for a given group. This is accessed by clicking on "Followers" from the Groups section.

If the requested Group cannot be found, the response will indicate a ``404`` error: ::

  {
    "error_type": "object_not_found",
    "error_message": "the requested group could not be found",
    "status": 404
  }

**Definition:** 

``GET https://cassieapp.com/api/groups/{group_slug}/followers/``

**Arguments**

* group_slug: *string*, the slug of the group to be viewed

**Returns**

Returns a dictionary, entitled 'followers', of User objects. these are the Users that follow the Group. Users are indexed by handle. The User object is the same as the `Frodad Object`_.

**Sample Response** ::

  {
    "followers": {
      "TJakubiec-298": {
        "firstName": "Tess",
        "lastName": "Jakubiec",
        "profPic": "",
        "friend_status": "friends",
        "handle": "TJakubiec-298"
      },ndle": "SBahr-313"
      },
      "CPaman-312": {
        "firstName": "Chloe ",
        "lastName": "Paman",
        "profPic": "",
        "friend_status": "respond",
        "handle": "CPaman-312"
      },
      "LCruz-314": {
        "firstName": "Lisette",
        "lastName": "Cruz",
        "profPic": "",
        "friend_status": "pending",
        "handle": "LCruz-314"
      }
    }
  }


--------
Accuracy
--------

View your accuracy in a given group. This is accessed by clicking on "Accuracy" from the Groups section. Includes percent correct, leaderboard ranking, and your forecasts in the Group.

If the requested Group cannot be found, the response will indicate a ``404`` error: ::

  {
    "error_type": "object_not_found",
    "error_message": "the requested group could not be found",
    "status": 404
  }

**Definition:** 

``GET https://cassieapp.com/api/groups/{group_slug}/accuracy/``

**Arguments**

* group_slug: *string*, the slug of the group to be viewed

**Returns**

In addition to a "myForecasts" dictionary, returns a dictionary entitled 'accuracy' that contains the following fields: 

  * **percentCorrect**: *integer*, the User's accuracy in the Group
  * **ranking**: *integer*, the User's ranking in the Group leaderboard
  * **totalRanked**: *integer*, total number of Users used in ranking (ranking on the app should be displayed as "ranking / totalRanked"; ex. 1/189)
  * **myForecasts**: *dictionary*, dictionary of `Forecast Object`_ entries indexed by forecast id 

**Sample Response** ::

  {
    "status": 200,
    "accuracy": {
      "percentCorrect": 34,
      "ranking": 2,
      "ranking_outOf": 189,
      "myForecasts": {
        "1517": {
          "endTime": "16:00",
          "question": "Bucs to win their season opener vs the Titans?",
          "endDate": "2015-09-13",
          "uuid": "6fgt4f2f80c5460d9940f1f91c8caae6",
          "answerSubmitted": false,
          "is_active": false,
          "forecast": "Yes"
        },
        "1525": {
          "question": "Taylor Swift and Calvin Harris getting engaged?",
          "endTime": null,
          "uuid": "5af84f2f80c5460d9940f1f91c8caae6",
          "is_active": true,
          "endDate": null,
          "forecast": "No way"
        }
      },
    }
  }

-------
Frodads
-------

View a listing of friends that are followers for a given group. This is accessed by clicking on "Frodads" from the Groups section. 

If the requested Group cannot be found, the response will indicate a ``404`` error: ::

  {
    "error_type": "object_not_found",
    "error_message": "the requested group could not be found",
    "status": 404
  }

**Definition:** 

``GET https://cassieapp.com/api/groups/{group_slug}/followers/frodads/``

**Arguments**

* group_slug: *string*, the slug of the group to be viewed

**Returns**

Returns a dictionary, entitled 'friendFollowers', of User objects- these are followers of the group that are also the requesting User's friends. Users are indexed by handle. The User object is the same as the `Frodad Object`_.

**Sample Response** ::

  {
    "friendFollowers": {
      "JAldersonSmith-280": {
        "profPic": "profiles/user-280/image.jpg",
        "lastName": "Alderson Smith",
        "firstName": "James",
        "friend_status": "friends",
        "handle": "JAldersonSmith-280"
      },
      "csocias": {
        "profPic": "profiles/user-4/image_QPZAEEG.jpg",
        "lastName": "Socias",
        "firstName": "Christina",
        "friend_status": "friends",
        "handle": "csocias"
      }
    }
  }
  
Activity
========
The Activity section is where Users receive notifications. See the :ref:`Notifications` section for more details. 

Search - NEW
============

Search Cassie users. Send the User's query as a string. Will return a list of User Objects (a User Object is the same as a `Frodad Object`_) but without the 'level' field.

**Definition**

``GET https://cassieapp.com/api/search/people/?q=``

**Arguments**

None

**Parameters**

* q: *string*, the search query

**Returns**

A list of User Objects entitled 'search_results' that meets the search criteria.

**Sample Response** ::

    {
      "status": 200,
      "search_results": [    
        {
          "handle": "csocias",
          "first_name": "Christina",
          "last_name": "Socias",
          "profPic": "profiles/user-4/image_QPZAEEG.jpg",
          "friend_status": "not-friends"
        },
        {
          "handle": "alexsocias",
          "first_name": "alex",
          "last_name": "socias",
          "profPic": "profiles/user-13/1427919205429.jpg",
          "friend_status": "friends"
        }
      ]
    }


Search - OLD
============

The actual search should be performed client-side. A request to any of the search endpoints will return the list of items to be searched.

.. note:: For now, each request shouldn't take too long. In the future, maybe we can store some the data client side so a new request is not needed each time (or, we can do server-side search).

**Definition**

``GET https://cassieapp.com/api/search/{search_type}/``

**Arguments**

* **search_type** (*optional*, default="people"): *string*, indicates which tab of the search page is being requested- "people", "groups", or "casties"

**Returns**

Either a list of dictionaries (for peopla and casties) or a dictionary of dictionaries, indexed by group slug, for groups. The main list/dictionary is entitled either "people", "groups", or "casties" depedning on what tab was selected. The people objects are ordered by last name. The group and castie objects are not ordered to save time.

**Sample Response**

For **people**: ::

  {
    "status": 200,
    "people": [
      {
        "last_name": "Alvarez",
        "first_name": "Mark",
        "friend_status": "pending",
        "handle": "markey"
      },
     {
        "last_name": "Swanson",
        "first_name": "Lizzy",
        "friend_status": "friends",
        "handle": "lizzyswan"
      },
      {
        "last_name": "Tanner",
        "first_name": "Gretchen",
        "friend_status": "respond",
        "handle": "tgretch"
      },
      {
        "last_name": "Weitz",
        "first_name": "Helen",
        "friend_status": "not-friends",
        "handle": "weitzup"
      },
    ]
  }

For **groups**: ::

  {
    "status": 200,
    "groups": {
      "uf-weekly-matchups": {
        "groupIcon": "category-bkgds/uf1.jpg",
        "friendsCount": 0,
        "groupName": "UF Weekly Matchups",
        "requiresApproval": false,
        "numberCasties": 0,
        "accuracy": 65,
        "following": true,
        "accessDeniedMessage": "You need approval to join this group.",
        "groupInfoText": "All about UF football",
        "slug": "uf-weekly-matchups",
        "followersCount": 91,
        "access": "granted"
      },
      "general-stuff": {
        "groupIcon": "category-bkgds/palm_trees.jpg",
        "friendsCount": 5,
        "groupName": "General Stuff",
        "requiresApproval": true,
        "numberCasties": 29,
        "accuracy": 87,
        "following": true,
        "accessDeniedMessage": "",
        "groupInfoText": "",
        "slug": "general-stuff",
        "followersCount": 56,
        "access": null
      }
    }
  }

For **casties**: 

If the User has forecasted the Castie and the Castie has been answered, use a green or red box to indicate if they were correct/incorrect. ::

  {
    "status": 200,
    "casties": [
      {
        "uuid": "cd993ecff27c46fa80bda9e3571e580c",
        "is_active": false,
        "userForecast": null,
        "showUsername": true,
        "group": "General Pop Culture",
        "forecastsCount": 0,
        "answerSubmitted": true,
        "groupIcon": "category-bkgds/pop_culture_final_jcGNqfM.png",
        "userCorrect": null,
        "question": "Another sample castie with set answer options, open",
        "submitter": "steph"
      },
      {
        "uuid": "371083f1c4694d30b8f2de0f3812a3e8",
        "is_active": true,
        "userForecast": true,
        "showUsername": true,
        "group": "General Pop Culture",
        "forecastsCount": 1,
        "answerSubmitted": true,
        "groupIcon": "category-bkgds/pop_culture_final_jcGNqfM.png",
        "userCorrect": null,
        "question": "testing write-in answer forecasts",
        "submitter": "steph"
      },
      {
        "uuid": "f9428a64bf3642cc9e1f64e7314ed9ee",
        "is_active": false,
        "userForecast": true,
        "showUsername": true,
        "group": "General Pop Culture",
        "forecastsCount": 1,
        "answerSubmitted": true,
        "groupIcon": "category-bkgds/pop_culture_final_jcGNqfM.png",
        "userCorrect": true,
        "question": "testing set answer option forecasts",
        "submitter": "steph"
      }
    ]
  }


.. _Hamburger:

Hamburger Side Menu
===================

The side menu is accessed via the "hamburger" icon on the home page. In addition to various settings, this side menu displays a list of Groups the User follows. A request to this "hamburger" endpoint returns a list of Groups in alphabetical order. The User's handle is also provided (the handle is needed when a User clicks on Preferences or Log Out).

**Definition:** 

``GET https://cassieapp.com/api/hamburger/``

**Arguments**

None

**Returns**

An alphabetical list of (group name, group slug) tuples for Groups the User follows (the slug is the unique identifier for a group). Additionally, the User's handle is provided in the "handle" field.

**Sample Response** ::

  {
    "status": 200,
    "handle": "steph",
    "groups": [
      [
        "Politics",
        "politics"
      ],
      [
        "Pop Culture",
        "pop-culture"
      ],
      [
        "European Sports",
        "european-sports"
      ]
    ]
  }

Create Castie- Group List
=========================

The request to actually save a Castie being created can be found in the :ref:`create castie` section of the POST requests docs. This request will return a list of groups for the User to choose from in the process of creating their Castie.

**Definition:** 

``GET https://cassieapp.com/api/casties/create/groups/``

**Arguments**

None

**Returns**

An alphabetical list of (group name, group slug) tuples for all existing Groups (the slug is the unique identifier for a group).

This is similar to the :ref:`Hamburger` request, which returns a list of all Gropus a User follows, but this endpoint returns data for all Groups a User folows AND has permission to create Casties in (as some Groups might not allow Users to create Casties).

**Sample Response** ::

  {
    "status": 200,
    "groups": [
      [
        "Politics",
        "politics"
      ],
      [
        "Pop Culture",
        "pop-culture"
      ],
      [
        "European Sports",
        "european-sports"
      ]
    ]
  }

View All Frodad Requests
========================

Information about the Friend Requests a User has sent and received. 

.. note:: There is a separate endpoint for retrieving frodad :ref:`  notifications <retrieve notifications>`


**Definition**

``GET https://cassieapp.com/api/frodad-requests/``

**Arguments**

None

**Returns**

* **number_received**: The number of pending requests a user has received (i.e. requests the User has not responded to yet)
* **requests_received**: a list of dictionaries with handle, firstName, and lastName fields for each request; if there are no requests, this field is set to ``None/null``
* **number_sent**: The number of requests a user has sent 
* **requests_sent**: a list of dictionaries with handle, firstName, and lastName fields for each request; if there are no requests, this field is set to ``None/null``


**Sample Response** ::

  {
    "status": 200,
    "number_sent": 43,
    "number_received": 0,
    "requests_received": null,
    "requests_sent": [
      {
        "handle": "user-478",
        "firstName": "Sam",
        "lastName": "Smith"
      },
      {
        "handle": "user-465",
        "firstName": "Amy",
        "lastName": "Jones"
      }
    ]
  }

.. _Comments:

Comments
========

Return a list of Comment Objects for a given Castie. Comments are ordered from newest to oldest.

To save a new comment for a Castie, use the :ref:`CreateComment` endpoint found in the :ref:`Post Requests` section.

**Definition:** 

``GET https://cassieapp.com/api/casties/{uuid}/comments/``

**Returns**

The Castie UUID, number of comments for the given Castie, and a list of Comment Objects

* **castieUUID:** *string*, unique id for the Castie
* **numberComments:** *integer*, numer of comments left on the Castie

**Comment Object** 

  * **handle:** *string*, the user's handle; uniquely identifies the friend
  * **lastName:** *string*, the user's last name 
  * **firstName:** *string*, the user's first name 
  * **profPic:** *string*, location of the friend's profile picture
  * **commentUUID:** *string*, unique id for the Comment
  * **commentText:** *string*, the text of the comment itself
  * **commentDate:** *string*, date (YYYY-MM-DD) the comment was made
  * **commentTime:**  *string*, time (Hour:Minute:Second:Microsecond) the comment was made
  * **isOffensive:** *boolean*, ``True`` if a User has marked the comment as offfensive; server does not return any comments marked as offensive

**Sample Response** ::

    {
      "status": 200,
      "numberComments": 3,
      "uuid": "a1f318a5c9ab4858a15996f69f851938",
      "comments": [
        {
          "profPic": "profiles/user-4/image_QPZAEEG.jpg",
          "handle": "csocias",
          "firstName": "Christina",
          "commentDate": "2016-01-31",
          "lastName": "Socias",
          "commentTime": "19:14:25.847464",
          "commentUUID": "zzf3v908c9ab4858a15996f69f851938",
          "commentText": "third comment left by christina",
          "isOffensive": false
        },
        {
          "profPic": "",
          "handle": "user-581",
          "firstName": "Stephanie",
          "commentDate": "2016-01-31",
          "lastName": "Socais",
          "commentTime": "19:14:05.613757",
          "commentUUID": "zzf3v908c9ab4858a15996f69f851938",
          "commentText": "second comment left by other steph",
          "isOffensive": false
        },
        {
          "profPic": "profiles/user-2/socias_photo_wp1ENod.jpg",
          "handle": "steph",
          "firstName": "Stephanie",
          "commentDate": "2016-01-31",
          "lastName": "Socias",
          "commentTime": "19:13:43.694687",
          "commentUUID": "zzf3v908c9ab4858a15996f69f851938",
          "commentText": "first comment left by steph",
          "isOffensive": false
        }
      ]
    }