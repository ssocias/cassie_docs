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

The app should display some type of indicator when the User has 1 or more new notifications (using the `count of new notifications`_ endpoint). A new notification is one that was created at any point since the client app last called the `retrieve notifications`_  endpoint. To determine whether or not new notifications have arrived, use the :ref:`Initialize` endpoint ``GET https://cassieapp.com/api/initialize/``.

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
    * **data:** *string*, data needed to take action of the notification (ex. handle of the user who accepted the friend request or uuid of the Castie that needs to be answered)

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

.. _retrieve notifications:

---------------------
Retrieve Notifcations
---------------------
Returns arrays of `Notification Object`_ grouped by notification type for all notifications. By default, ALL types of notifications are returned (including Frodad Requests and Recently Accepted frodad requests). They are returned in arrays matching their notification type, listed in chronological order.

To return notifications of a particular type, you may use the "notification_type" parameter to include one or more notification_types from the table above. For example, ``/?notification_type=group-approved&notification_type=castie-needs-answer/``, will return two lists of Notification Objects. One for "group-approved" notifications and one for "castie-needs-answer" notifications.

Calling this enpoint sets all notificaitons to ``has_been_read = True``. So, only call this endpoint when the User clicks on the Activity tab. Or else it will interfere with the way I keep track of new notifications.


**Definition**

``GET https://cassieapp.com/api/notifications/``

**Arguments**

None

**Parameters**

* **notification_type (optional)**: *string*, filter by any one of the ``notification_type`` notifications in the Table above

**Returns**

A list(s) of Notification Objects.

    **If ALL notifications have been requested, multiple lists are returned. There is a separate list of Notification Objects for every notification_type, as specified in the table above.**

**Sample Response**

**No Filtering:** ::

    {
      "status": 200,
      "castie-friend-forecasted": [],
      "castie-forecasted": [],
      "new-casties": [],
      "friend-request-received": [],
      "castie-answered": [],
      "castie-friend-commented": [],
      "group-access-granted": [
        {
          "id": "56d52be599c809a029620b24",
          "url": "/categories/ism-6216-data-base/",
          "created_on_time": "00:43:01",
          "message": "You have been approved to join the ISM 6216 Data Base group",
          "data": "ism-6216-data-base",
          "timesince_created": "seconds ago",
          "created_on_date": "2016-03-01",
          "has_been_read": false,
          "notification_type": "group-access-granted"
        }
      ],
      "friend-request-accepted": [],
      "castie-needs-answer": [
        {
          "id": null,
          "url": null,
          "created_on_time": "11:57:00",
          "message": "Your Castie has ended- time to answer it!",
          "data": "0f05ac0abc714bcf896ce60a2cdd2b55",
          "timesince_created": "4 days ago",
          "created_on_date": "2016-02-25",
          "has_been_read": false,
          "notification_type": "castie-needs-answer"
        },
        {
          "id": null,
          "url": null,
          "created_on_time": "20:00:00",
          "message": "Your Castie has ended- time to answer it!",
          "data": "dacd33b5ffce4396a1b092c8901a8e08",
          "timesince_created": "6 months ago",
          "created_on_date": "2015-09-30",
          "has_been_read": false,
          "notification_type": "castie-needs-answer"
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


.. _count of new notifications:

-------------------------------------
Retrieve a Count of New Notifications
-------------------------------------

This is the same as the :ref:`Initialize` endpoint.

Returns a count of the number of new notifications since the client app last called the `retrieve notifications`_ endpoint. Casties that need to be answered, regardless of how many there are, count as 1 collective notification. That is why, on the sample response below, the User has 11 Casties that need to be answered but their number_notifications is just 2 (one notification for the Casties needing answer and another for a separate notification).

**Definition**

``GET https://cassieapp.com/api/initialize/``

**Arguments**

None

**Sample Response** ::

  {
    "status": 200,
    "needs_answer": true,
    "number_needing_answer": 11,
    "number_notifications": 2
  }


.. _delete notification:

---------------------------------
Delete an Individual Notification
---------------------------------

**Definition**

``POST https://cassieapp.com/api/notifications/delete/{notification_id}/``

**Arguments**

* **notification_id**: *string*, the Notification's uniquie id 

**Sample Response** ::

    {
      'status': 200
    }


------------------------
Delete all Notifications
------------------------

.. warning:: This "Delete All" functionality is not needed in the iOS app.

Deletes all notifications except for any ``castie-needs-answer`` notifications. ``castie-needs-answer`` notifications can never be deleted. They are only "deleted" once the User answers the castie.

**Definition**

``POST https://cassieapp.com/api/notifications/delete/all/``

**Arguments**

None

**Sample Response** ::

  {
    "status": 200,
    "delete-all": "all notifications have been deleted"
  }

