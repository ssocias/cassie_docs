Other Requests
**************

These endpoints accept both GET and POST requests, depending on what action is being taken. For POST requests, all data should be sent in the body of the request as key:value pairs of type ``application/x-www-form-urlencoded``.


Answer Castie
=============

Input the correct answer for an ended Castie.

Use a ``GET`` request to retrieve the available answer options for the Castie. Answer options are available only for Casties that had set answer options. If the Castie accepted write-in answers, there are no answer options to choose from and the ``GET`` response will return only the appropriate Castie data.

Use a ``POST`` request to save the correct answer/answer-keyword. If the Castie allowed write-in answers, the User inputs a keyword that all correct forecasts must contain. If the Castie consists of set answer options, the User selects the correct Answer from the list of options.

---
GET
---

**Definition:** 

``GET https://cassieapp.com/api/casties/{uuid}/answer/``

**Parameters**

  * **uuid:** *string*, unique id for the Castie

**Returns**

    * **uuid:** *string*, unique id for the Castie
    * **question:** *string*, question text 
    * **group:** *string*, group to which the Castie belongs
    * **allowWriteIn:** *boolean*, ``True`` if the Castie allows write-in forecasts
    * **forecastsCount:** *integer*, number of total forecasts for the Castie

    * **answer_options:** *dictionary*, this field is present only for the Casties that have set answer options. it maps an answer option's unique ID to the answer option text

**Sample Response** 

For a write-in castie: ::

  {
    "status": 200,
    "forecastCount": 2,
    "group": "subtest",
    "question": "Who will become president?",
    "uuid": "227e85f969874a50988301966ecdb1fa",
    "allowWriteIn": true
  }

For a set answer option castie: ::

  {
    "status": 200,
    "forecastCount": 2,
    "group": "subtest",
    "question": "Who will become president?",
    "uuid": "227e85f969874a50988301966ecdb1fa",
    "allowWriteIn": true,
    "answer_options": {
      "818": "Jack",
      "819": "Amy",
      "820": "Ana"
    },
  }

----
POST
----

**Definition:** 

``POST https://cassieapp.com/api/casties/{uuid}/answer/``

**Parameters**

  * **uuid:** *string*, unique id for the Castie

  If it's a write-in Castie, include the correct answer "keyword" in the request body:

  * **keyword:** *string*, the keyword a forecast must contain in order to be considered correct

  If it's a Castie with set answer optoins only, include the ID of the correct answer request body:

  * **answer_id:** *integer*, the Castie's correct answer ID

**Returns**

A successfull request returns a :ref:`Detailed Castie Object <Detailed Castie Object>` so the User can now see the results of the Castie they just answered.

**Sample Response** 

See :ref:`Detailed Castie Object <Detailed Castie Object>`


Edit Profile
============

Use this request to update any of the fields on the "Edit Profile" page. The GET request to this endpoint will show data for:
handle, first name, last name, city, state, profile picture, and background picture.

Users may edit any one of the fields right on the page. Therefore, a POST to the endpoint will check to see which fields have changed and will save those new fields.

---
GET
---

View the "Edit Profile" landing page, with all editable fields displayed.

**Definition**

``GET https://cassieapp.com/api/profile/{handle}/edit``

**Arguments**

* handle: *string*, the handle of the profile to be edited (it must be the same as the requesting User or else you will get an error)

**Returns**

* **handle**: *string*, User's unique identifier- must be at least three characters long, ASCII only, no /\@ characters
* **firstName**: *string*, User's first name
* **lastName**: *string*, User's last name
* **city**: *string*, city
* **state**: *string*, state or country
* **profPic**: *string*, location of User's profile picture
* **backgroundPic**: *string*, location of User's background picture

Fields that have no value are returned as "", see "backgroundPic" in the sample below.

**Sample Response** ::

  {
    "status": 200,
    "handle": "myHandle",
    "firstName": "bradley",
    "lastName": "cooper",
    "city": "Los Angeles",
    "state": "CA",
    "profPic": "static/images/user1/prof.png",
    "backgroundPic": ""
  }

----
POST
----

Save changes to any fields that were edited. Perform basic validation client-side (ASCII only, image type/size, etc.).

All fields passed in the request will be compared to their present values. Any fields that have changed will be updated in the DB. So, you can always pass all the fields and I will check to see which ones have changed.

**Definition**

``POST https://cassieapp.com/api/profile/edit``

**Parameters**

* **handle**: *string*, User's unique identifier- must be at least three characters long, ASCII only, no /\@ characters
* **firstName**: *string*, User's first name
* **lastName**: *string*, User's last name
* **city**: *string*, city
* **state**: *string*, state or country
* **profPic**: *string*, location of User's profile picture
* **backgroundPic**: *string*, location of User's background picture

**Returns**

Returns a success message if updates were successfully made. If the "handle" is already taken (handle must be unique), an error message will be returned: ::

    {
    "status": 400,
    "error_type": "handle_not_unique",
    "error_message": "the hanlde provided is already associated with a Cassie account"
    }

.. note:: **Real-time Handle Uniqueness Validation**

    It would be great to perform a uniqueness check on the handle as the user is typing so that they don't have to submit the form and then wait for a response. If I send over a list of existing handles, can you perform the uniqueness validation right there on the app?

**Sample Response** 

If data was updated, the response indicates what changes were made: ::

  {
    "status": 200,
    "message": "data has been successfully updated",
    "updated_fields": [
      "firstName",
      "lastName"
    ]
  }

If no updates were made becuase the data did not change: ::

  {
    "status": 200,
    "message": "nothing has changed- no data to update"
  }

Preferences- Main
=================

The preferences page contains sections to 'edit profile', 'change password', and 'change email'. Use a ``GET`` request to view this main Preferences page. To actually make changes to the data fields, the User must click the desired field. When that occurs, use the appropriate requests described below. 

The 'private account' field, however, can be changed right from this Preferences page. When the User changes the 'private account' toggle, perform a ``POST`` request to update the field. 

---
GET
---

View the main "Preferences" landing page, with "is_private" boolean field displayed.

**Definition**

``GET https://cassieapp.com/api/preferences``

**Parameters**

None

**Returns**

Returns the status of the "is_private" field, since that is the only piece of data shown on the main Preferences page.

**Sample Response** ::

  {
    "status": 200,
    "is_private": True
  }

----
POST
----

Change the status of the "is_private" boolean on the main preferences page.

**Definition**

``POST https://cassieapp.com/api/preferences``

**Parameters**

  **is_private:** *boolean*, True if the account should be private

**Returns**

Returns the status of the newly updated "is_private" field.

**Sample Response** ::

  {
    "status": 200,
    "message": "data has been successfully updated"
    "is_private": True
  }


Preferences- Email
==================

---
GET
---

View the existing email associated with the account.

**Definition**

``GET https://cassieapp.com/api/preferences/edit/email/``

**Parameters**

None

**Returns**

**email:** *string*, email associated with the account

**Sample Response** ::

  {
    "status": 200,
    "email": "myemail@me.com"
  }

----
POST
----

Change the the existing email associated with the account. The email address must be unique. If the User tries to change the email to an email already associated with a Cassie account, an error message will be returned.

.. note:: **Changing Email Address Changes the Username**

  The email address associated with an account is also the User's username. When the User changes their email, the username is also changed.

**Definition**

``POST https://cassieapp.com/api/preferences/edit/email/``

**Parameters**

**email:** *string*, the new email 

**Returns** ::

  {
    "status": 200,
    "message": "email has been successfully updated",
    "new_email": "newemail@me.com",
    "new_username": "myusername"
  }

**If the email is not unique:** ::

  {
    "status": 400,
    "error_type": "email_not_unique",
    "error_message": "the email address provided is already associated with a Cassie account. it cannot be used again."
  }

Preferences- Password
=====================

---
GET
---

"View" the existing password associated with the account- it will be encoded though so there is really nothing to see.

**Definition**

``GET https://cassieapp.com/api/preferences/edit/password/``

**Parameters**

None

**Returns**

**password:** *string*, encrypted associated with the account

**Sample Response** ::

  {
    "status": 200,
    "password": "encryptedstuff"
  }

----
POST
----

Change the the existing password associated with the account.

**Definition**

``POST https://cassieapp.com/api/preferences/edit/password/``

**Parameters**

  **password:** *string*, the new password 

**Returns** ::

  {
    "status": 200,
    "message": "password has been successfully updated"
  }
