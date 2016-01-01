.. _Authentication:

Authentication
**************

HTTP Basic Authentication is used to authenticate every request. A valid username and password must be included in the ``Authorization`` header of every request.

Every request must also contain a valid API key in order to "authorize" where the request is coming from. This key must be included in a "Api-Key" header. 

