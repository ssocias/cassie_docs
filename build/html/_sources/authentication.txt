.. _Authentication:

Authentication
**************

HTTP Basic Authentication is used to authenticate every request. A valid username and password must be included in the ``Authorization`` header of every request.

It will also be necessary to authorize every request so that only requests from the iOS app have access; probably using some type of token...this is still TBD!

