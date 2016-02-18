.. _ReportingInappropriateContent:

Reporting Inappropriate Content
*******************************

Per Apple App Store rules, any user-generated content must have a way to be reported as inappropriate/offensive. 
Cassie Users can report invididual Casties and individual Comments as inappropriate using the endpoints below.

------------------
Reporting a Castie
------------------

**Definition:** 

``POST https://cassieapp.com/api/report/castie/{uuid}/``

**Arguments**

* **uuid**: *string*, unique ID of the Castie being reported

**Returns**

A simple success message if the request worked.

**Sample Response** ::

    {
      "status": 200,
      "message" "castie has been reported"
    }

-------------------
Reporting a Comment
-------------------

**Definition:** 

``POST https://cassieapp.com/api/report/comment/{uuid}/``

**Arguments**

* **uuid**: *string*, unique ID of the Comment being reported

**Returns**

A simple success message if the request worked.

**Sample Response** ::

    {
      "status": 200,
      "message" "comment has been reported"
    }
