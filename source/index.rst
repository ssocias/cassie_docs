Cassie API Documentation
========================

This API is the "backend" for the Cassie v1.0 iOS app. All API access is over HTTPS via the ``https://cassieapp.com`` domain. All responses are returned as JSON.

.. topic:: API endpoint

    ``https://cassieapp.com/api``

The API uses HTTP Basic Authentication to authenticate every request. A valid username (username is the email associated with the account) and password must be included with every request in order to retrieve or post data. Additionally, a valid API key must also be sent with every request. See the :ref:`authentication` section for more details.

.. topic:: Image Storage

    ``GET https://cassieapp.com/static/uploads/{image_location}/``

Topics
---------

.. toctree::
   :maxdepth: 2
   
   authentication
   errors
   notifications
   reporting-inappropriate-content
   image-paths
   pagination

Endpoints
---------

.. toctree::
   :maxdepth: 3

   get-requests
   post-requests
   other-requests

Endpoints left to do:
---------------------
* Activity tab- will probably condense into just a notifications tab (no Frodad Feed)
  * Frodad Feed
  * Notifications
* Notification has been read
* Delete notification

Stephanie's Reminders
---------------------

* Pagination- home page and others
* Replace @csrf_exempt decorator
* How to best create/store notifications- Firebase?
* Versioning- not needed at this time
* Home page takes way too long to load
* What if too many requests are coming in at one time? No idea!!!
* Initialize request- needs to return notifications as well
* Uploading photos (on initial sign-up and when editing profile)
* Make sure to use only active Users, profile.is_active == True
* Make sure Personal polls are not showing up anywhere
* Make sure to evaluate booleans correctly, as they might get passed as strings

Indices and tables
------------------

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

