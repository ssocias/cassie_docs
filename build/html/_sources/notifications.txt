.. _Notifications:

Notifications
*************

Users receive notifications from the Activity section found on the bottom navbar. The Activity section consists of "Requests" and "Notifications" tabs. The "Requests" tab houses all Frodad Requests- both requests the User has received and also any recenty accepted requests the User has sent. 
The "Notifications" tab contains all other notifications to the user (ex. Castie needs to be answered, You forecasted correctly, etc.).

From here forward, when using the word "notifications", I am referring to both frodad requests and generic notifications.

.. note:: Notifications API endpoint

    ``https://notifications.cassieapp.com/``

    All requests except for the request to obtain a count of notifications require Basic Authentication using the User's email and password.

Notifications are accessed via `https://notifications.cassieapp.com/notifications/` 
Each notification is saved as a "Notificaion Object". Most GET requests regarding notifications return either an individual object or an array of objects. See the `Notification Object`_ section below for more details.
Notifications are classified as "unread" when the User has not yet clicked on it and "read" once they have. When a User clicks on a notification (thus making it read), the app must send a request to the appropriate "has been read" endpoint.
The app should display a small red indicator number when the User has 1 or more new notifications. A new notification is one that was created at any point since the User last clicked the "Activity" tab. In order for the backend to retrieve the correct number, the app must pass the date/time of the User's last visit to the "Activity" tab in the request to retrieve the number of new notifications.

Users may delete notifications by tapping the small 'x' in the right hand corner of the notification. When they do so, a request is made to the appropriate DELETE endpoint. Notifications that are "completed" after the User takes action must be deleted automatically. For example, when the User receives a friend request, this notification must be deleted whenever the User accepts or rejects it. Because the User may accept/reject from other screens in the app, we need to coordinate for this! Same thing for answering a Castie.

.. _Notification Object:

**The Notification Object**

    **Attributes**

    * **created_on:** *date*, date (YYYY-MM-DD?) the Notification was created
    * **uuid:** *string*, unique id for the Notification
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
|Your Castie has been Forecasted| castie-forecasted                | uuid of the castie                      |
+-------------------------------+----------------------------------+-----------------------------------------+
|Castie Forecasted by Friends   | castie-friends-forecasted        | uuid of the castie                      |
+-------------------------------+----------------------------------+-----------------------------------------+
|Group Access Approved          | group-approved                   | slug of the group                       |
+-------------------------------+----------------------------------+-----------------------------------------+

---------------------------------
Delete an Individual Notification
---------------------------------

**Definition**

``DELETE https://notifications.cassieapp.com/notifications/{user_uuid}/``

**Arguments**

* user_uuid: *string*, the User's uniquie uuid 

**Sample Response**

-------------------------------------
Retrieve a Count of New Notifications
-------------------------------------

No authentication required.

**Definition**

``GET https://notifications.cassieapp.com/notifications/{user_uuid}/unread/?last_seen_date={last_seen_date}&last_seen_time={last_seen_time}``

**Arguments**

* user_uuid: *string*, the User's uniquie uuid 
* last_seen_date: *string*, date the User last clicked on the "Activity" tab
* last_seen_time: *string*, time the User last clicked on the "Activity" tab


**Sample Response** ::

    {
        "count": 3
    }

---------------
Frodad Requests
---------------

**Definition**

``GET https://notifications.cassieapp.com/notifications/{user_uuid}/frodad-requests/``
``GET https://notifications.cassieapp.com/notifications/{user_uuid}/frodad-requests-accepted/``

**Arguments**

**Returns**

**Sample Response**

-------------
Notifications
-------------

Coming Soon


