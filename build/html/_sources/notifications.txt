.. _Notifications:

Notifications
*************

.. warning:: **Question for James about Frodad Requests**:
    Since we include the Frodad's picture along with their request, can we link to their profile from the picture??

Users receive notifications from the Activity section found on the bottom navbar. The Activity section consists of "Requests" and "Notifications" tabs. 

The "Requests" tab houses all Frodad Requests- both requests the User has received and also any recently accepted requests the User has sent. Requests are ordered chronologically (most recent to oldest). 
The "Notifications" tab contains all other notifications to the user (ex. Castie needs to be answered, You forecasted correctly, etc.). Notifications are ordered chronologically (most recent to oldest). However, if the User has any Casties that need to be answered, these notifications should be displayed first. 

From here forward, when using the word "notifications", I am referring to both frodad requests and generic notifications.

.. note:: Notifications API endpoint

    ``https://cassieapp.com/api/notifications/``


Each notification is saved as a `Notification Object`_. Most GET requests regarding notifications return either an individual object or an array of objects.

The app should display some type of indicator when the User has 1 or more new notifications (using the `count of new notifications`_ endpoint). A new notification is one that was created at any point since the User last clicked the "Activity" tab. In order for the backend to retrieve the correct number, the app must pass the date/time of the User's last visit to the "Activity" tab in the request to retrieve the `count of new notifications`_.

Users may delete notifications by tapping the small 'x' in the right hand corner of the notification. When they do so, a request is made to the appropriate `delete notification`_ endpoint. Notifications that are "completed" after the User takes action must be deleted automatically. For example, when the User receives a friend request, this notification must be deleted when the User accepts or rejects it. Because the User may accept/reject from other screens in the app, we need to coordinate for this! Same thing for answering a Castie. The server takes care of deleting such notifications so just make sure to "refresh" notifications on the client-side once the action is completed!

.. _Notification Object:

**The Notification Object**

    **Attributes**

    * **created_on_date:** *string*, date (YYYY-MM-DD) the Notification was created
    * **created_on_time:** *string*, time (HH:mm:ss) the Notification was created
    * **timesince_created:** *string*, human readable string of time since notification was created (ex. "2 days ago")
    * **id:** *string*, unique id for the Notification (casti-needs-answer notifications do not have an id)
    * **message:** *string*, text of the notification
    * **url:** *string*, action url used in the web app
    * **has_been_read:** *boolean*, indicates whether or not the notification has been read

    * **notification_type:** *string*, what type of notification this is (friend request, answer castie, etc); see the `Notification Types Table`_ for details
    * **data:** *array*, data needed to take action of the notification (ex. handle of the user who accepted the friend request or uuid of the Castie that needs to be answered)

.. _Notification Types Table:

**Notification Types**

+-------------------------------+----------------------------------+-----------------------------------------+
|**Notification Name**          | **notification_type**            | **data**                                |
+-------------------------------+----------------------------------+-----------------------------------------+
|Friend Request Received        | friend-request-received          | handle of user who sent the request     |
+-------------------------------+----------------------------------+-----------------------------------------+
|Friend Request Accepted        | friend-request-accepted          | handle of user who accepted the request |
+-------------------------------+----------------------------------+-----------------------------------------+
|Castie Needs Answer            | castie-needs-answer              | uuid of the castie to be answered       |
+-------------------------------+----------------------------------+-----------------------------------------+
|Castie Answered                | castie-answered                  | uuid of the newly answered castie       |
+-------------------------------+----------------------------------+-----------------------------------------+
|Castie Commented by Friend     | castie-friend-commented          | uuid of the castie                      |
+-------------------------------+----------------------------------+-----------------------------------------+
|New Casties Added to Group     | new-casties                      | slug of the group                       |
+-------------------------------+----------------------------------+-----------------------------------------+
|Your Castie has been Forecasted| castie-forecasted                | uuid of the castie                      |
+-------------------------------+----------------------------------+-----------------------------------------+
|Castie Forecasted by Friend    | castie-friend-forecasted         | uuid of the castie                      |
+-------------------------------+----------------------------------+-----------------------------------------+
|Group Access Approved          | group-access-granted             | slug of the group                       |
+-------------------------------+----------------------------------+-----------------------------------------+

There are some notification objects that we don't need on the iOS app. These will have ``None/null`` values for ``notification_type`` and ``data``. Just don't display these!

.. _retrieve notifications:

---------------------
Retrieve Notifcations
---------------------
Returns an array of Notification Objects for all notifications. By default, ALL types of notifications are returned (including Frodad Requests and Recently Accepted frodad requests). They are returned in chronological order.
To return notifications of a particular type, you may use the "notification_type" parameter to include one or more notification_types from the table above. For example, ``/?notification_type=group-approved&notification_type=castie-needs-answer/``, will return two list of Notification Objects. One for "group-approved" notifications and one for "castie-needs-answer" notifications.


**Definition**

``GET https://cassieapp.com/api/notifications/all/?notification_type=``

**Arguments**

None

**Parameters**

* **notification_type**: *string*, filter by any one of the ``notification_type`` notifications in the Table above

**Returns**

A list(s) of Notification Objects.

    **If ALL notifications have been requested, only one list entitled "notifications" will be returned.**
        * **notifications**: a list of Notification Objects for all notifications 

    **If only certain types of notifications have been requested, there will be a separate list for each type.**

**Sample Response**

**No Filtering:** ::

    {
      "status": 200,
      "notifications": [
        {
          "url": "/profile/c71d8f8144f74a3498d279043182d600/",
          "notification_type": "friend-request-accepted",
          "id": "56c8d5969e4d5b596129cebe",
          "timesince_created": "an hour ago",
          "has_been_read": true,
          "created_on_date": "2016-02-20",
          "data": "Luly",
          "message": "@Luly has accepted your friendship request",
          "created_on_time": "16:07:34"
        },
        {
          "url": "/friends/requests/",
          "notification_type": "friend-request-received",
          "id": "56c8d3287d97535a6195f8cc",
          "timesince_created": "an hour ago",
          "has_been_read": true,
          "created_on_date": "2016-02-20",
          "data": "Luly",
          "message": "@Luly has sent you a friendship request",
          "created_on_time": "15:57:12"
        }
      ]
    }

**Filtered by Type:** ::

    {
      "status": 200,
      "castie-needs-answer": [],
      "friend-request-accepted": [
        {
          "url": "/profile/c71d8f8144f74a3498d279043182d600/",
          "notification_type": "friend-request-accepted",
          "id": "56c8d5969e4d5b596129cebe",
          "timesince_created": "an hour ago",
          "has_been_read": true,
          "created_on_date": "2016-02-20",
          "data": "Luly",
          "message": "@Luly has accepted your friendship request",
          "created_on_time": "16:07:34"
        }
      ]
    }


.. _delete notification:

---------------------------------
Delete an Individual Notification
---------------------------------

**Definition**

``POST https://cassieapp.com/api/notifications/delete/{notification_id}/``

**Arguments**

* **notification_id**: *string*, the Notification's uniquie id 

**Sample Response**

    Coming soon


------------------------
Delete all Notifications
------------------------

**Definition**

``POST https://cassieapp.com/api/notifications/delete/all/``

**Arguments**

None

**Sample Response**

    Coming soon

.. _mark as read:

------------
Mark as Read
------------

**PROBABLY WON'T USE THIS**

Use this endpoint to indicate that a notification has been read. A notification is "read" once a User clicks the notification.

**Definition**

``POST https://cassieapp.com/api/notifications/read/{notification_id}/``

**Arguments**

* **notification_id**: *string*, the Notification's uniquie id 

**Sample Response** ::

    Coming Soon

.. _count of new notifications:

-------------------------------------
Retrieve a Count of New Notifications
-------------------------------------

Returns a count of the number of new notifications since the User last visited the "Activity" section. The date and time of the last visit must be passed in the request.

**Definition**

``GET https://cassieapp.com/api/notifications/count/?last_seen_date={last_seen_date}&last_seen_time={last_seen_time}``

**Arguments**

None

**Parameters**

* **last_seen_date**: *string*, date (YYYY-MM-DD) the User last clicked on the "Activity" tab
* **last_seen_time**: *string*, time (HH:mm:ss) the User last clicked on the "Activity" tab

**Sample Response** ::

    {
      "status": 200,
      "count": 3
    }

