Cassie API Documentation
========================

This API is the "backend" for the Cassie v1.0 iOS app. All API access is over HTTPS via the ``https://cassieapp.com`` domain. All responses are returned as JSON.

.. topic:: API endpoint

    ``https://cassieapp.com/api``

The API uses HTTP Basic Authentication to authenticate every request. A valid username (username is the email associated with the account) and password must be included with every request in order to retrieve or post data. See the :ref:`authentication` section for more details.

.. topic:: Image Storage

    ``https://cassieapp.com/static/uploads/{image_location}``

Topics
---------

.. toctree::
   :maxdepth: 2
   
   overview
   authentication
   errors
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
* Activity tab
  * Frodad Feed
  * Notifications
* Notification has been read
* Delete notification

Stephanie's Reminders
---------------------

* Tables for every type of Notification
* Tables for every type of Friend Activity
* Versioning
* Pagination
* Generic 404 error responses if a requested URL does not exist
* Home page takes way too long to load
* What if too many requests are coming in at one time? No idea!!!
* How to make sure the request is authorized?? Not just authenticated....don't want anybody to be able to get all our data
* initialize request
* uploading photos (on initial sign-up and when editing profile)
* I'm using @csrf_exempt decorator for all post views??
* Make sure to use only active Users, profile.is_active == True
* Make sure Personal polls are not showing up anywhere
* Make sure to evaluate booleans correctly, as they might get passed as strings

Indices and tables
------------------

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

