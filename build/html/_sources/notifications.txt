.. _Notifications:

Notifications
*************

.. warning:: **Question for James about Frodad Requests**:
    Since we include the Frodad's picture along with their request, can we link to their profile from the picture??

Users receive notifications from the Activity section found on the bottom navbar. The Activity section consists of "Requests" and "Notifications" tabs. 

The "Requests" tab houses all Frodad Requests- both requests the User has received and also any recently accepted requests the User has sent. 
The "Notifications" tab contains all other notifications to the user (ex. Castie needs to be answered, You forecasted correctly, etc.).

From here forward, when using the word "notifications", I am referring to both frodad requests and generic notifications.

.. note:: Notifications API endpoint

    ``https://cassieapp.com/api/notifications/``


Each notification is saved as a `Notification Object`_. Most GET requests regarding notifications return either an individual object or an array of objects.

Notifications are classified as "unread" when the User has not yet clicked on it and "read" once they have. When a User clicks on a notification (thus making it read), the app must send a request to the appropriate `mark as read`_ endpoint.

The app should display a small red indicator number when the User has 1 or more new notifications (using the `count of new notifications`_ endpoint). A new notification is one that was created at any point since the User last clicked the "Activity" tab. In order for the backend to retrieve the correct number, the app must pass the date/time of the User's last visit to the "Activity" tab in the request to retrieve the `count of new notifications`_.

Users may delete notifications by tapping the small 'x' in the right hand corner of the notification. When they do so, a request is made to the appropriate DELETE endpoint. Notifications that are "completed" after the User takes action must be deleted automatically. For example, when the User receives a friend request, this notification must be deleted whenever the User accepts or rejects it. Because the User may accept/reject from other screens in the app, we need to coordinate for this! Same thing for answering a Castie.

.. _Notification Object:

**The Notification Object**

    **Attributes**

    * **created_on_date:** *string*, date (YYYY-MM-DD) the Notification was created
    * **created_on_timee:** *string*, time (HH:mm:ss) the Notification was created
    * **timesince_created:** *string*, human readable string of time since notification was created (ex. "2 days ago")
    * **id:** *string*, unique id for the Notification
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
|Castie Commented               | castie-commented                 | uuid of the castie and new comment      |
+-------------------------------+----------------------------------+-----------------------------------------+
|New Castie                     | new-castie                       | uuid of the castie                      |
+-------------------------------+----------------------------------+-----------------------------------------+
|Your Castie has been Forecasted| castie-forecasted                | uuid of the castie                      |
+-------------------------------+----------------------------------+-----------------------------------------+
|Castie Forecasted by Friends   | castie-friends-forecasted        | uuid of the castie                      |
+-------------------------------+----------------------------------+-----------------------------------------+
|Group Access Approved          | group-approved                   | slug of the group                       |
+-------------------------------+----------------------------------+-----------------------------------------+

-------------------------
Retrieve All Notifcations
-------------------------
Returns an array of Notification Objects for all notifications. By default, this will not include Frodad Request notifications or notifications of recently accepted frodad requests. To include such notifications, set the ``include_frodads`` parameter to ``True``. To retrieve only Frodad Request and recently accepted frodad request notifications, use the `Frodad Requests`_ endpoint.

**Definition**

``GET https://cassieapp.com/api/notifications/all/?include_frodad_notifications=False``

**Arguments**

None

**Parameters**

* **include_frodad_notifications**: *boolean*, set to ``True`` in order to return Frodad Request notifications

**Returns**

* **notifications**: a list of Notification Objects for all notifications (except Frodad Requests and Recently Accepted Frodad Requests)
* **frodad_notifications**: a list of Notification Objects for all Frodad Requests the User has received and not responded to yet
* **recently_accepted**: a list of Notification Objects for Frodad Requests the User has sent and were recently accepted

**Sample Response**

    Coming soon


.. _Frodad Requests:

-------------------------------------
Retrieve Frodad Request Notifications
-------------------------------------

Returns notifications regarding pending friend requests the User has received and notifications regarding recently accepted friend requests the User has sent. 

Maybe include notification the User has sent but have not yet been accepte/rejected?

**Definition**

    ``GET https://cassieapp.com/api/notifications/frodad-requests/``

**Arguments**
None

**Returns**

* **frodad_notifications**: a list of Notification Objects for all Frodad Requests the User has received and not responded to yet
* **recently_accepted**: a list of Notification Objects for Frodad Requests the User has sent and were recently accepted

**Sample Response**

Coming Soon

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

* **last_seen_date**: *string*, date the User last clicked on the "Activity" tab
* **last_seen_time**: *string*, time the User last clicked on the "Activity" tab

**Sample Response** ::

    {
        "count": 3
    }

