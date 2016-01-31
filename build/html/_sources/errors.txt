.. _Errors:

Errors
*******
Every response includes a "status" key that indicates the success or failure of an API request. The "status" codes follow conventional HTTP response codes. Status 200 indicates a successful request. In the event of an error (any status code other than 200), ``error_type`` and ``error_message`` keys are also included in the request. 

Error Codes
===========

+---------------------+----------------------------------+
|200- OK              | Successful request               |
+---------------------+----------------------------------+
|400- Bad Request     | Unacceptable Request             |
+---------------------+----------------------------------+
|401- Unauthorized    | User could not be authenticated  |
+---------------------+----------------------------------+
|404- Not Found       | Requested resource does not exist|
+---------------------+----------------------------------+

Error Types and Messages
========================
If the response status is anything other than 200, ``error_type`` and ``error_message`` keys are included in the request. The ``error_message`` includes more specific information about the error type. 

**Possible error types include:**

* ``authentication_failed`` user could not be authenticated
* ``authorization_failed`` missing or invalid API key
* ``object_not_found`` requested object (usually a Castie, Group, or User) could not be found
* ``inactive_user`` the requested profile is not active
* ``castie_not_created`` castie could not be created/saved to database
* ``comment_not_created`` comment not saved to database
* ``account_not_created`` new Cassie account could not be created/saved to database
* ``email_not_unique`` email address is already associated with an existing account
* ``handle_not_unique`` handle is already associated with an existing account
* ``incorrect_request_type`` making a GET request when it should be POST and vice versa
* ``invalid_request`` request could not be completed
* ``improper_group_request`` trying to follow/unfollow a group you already follow/unfollow
* ``missing_request_data`` the expected POST/GET data was not found in the request 
* ``castie_not_answerable`` the Castie is not accepting the correct answer (either the end date has not passed or it is already answered)
* ``cannot_forecast`` the User cannot submit a forecast becuase they have already done so (a User can only forecast each Castie one time)
* ``cannot_add_frodad`` the User is trying to send a friend request to another User that is already a friend
* ``improper_frodad_response`` the only acceptable actions for responding to a friend request are "accept" and "reject"
