Kipling Package Index server
============================

Kipling Package Index server that allows for basic management of user accounts
as well as create, read, update, and delete (CRUD) control over the package
index. Also provides access to S3 uploads for the packages themselves.

<br>
Configuration values
--------------------
The Kipling Package Index server has some configuration values stored in kpiserver.cfg to allow for multiple deployment. Required values are:

**Outbound email settings**  
Currently, KPI only supports Mandrill for transactional outbound emails.

 - ```EMAIL_FROM_ADDRESS``` The email address that kpiserver should send from.
 - ```EMAIL_FROM_NAME``` The human name of the person or service to report emails as having been sent from.
 - ```MANDRILL_API_KEY``` The API key to use to access the Mandrill transactional email service.


**File uploads / source hosting**  
Currently, KPI only supports using Amazon Web Services' S3 for storing and serving file uploads / Kipling package sources.

 - ```UPLOADS_BUCKET_NAME``` The S3 bucket where uploads should be saved.
 - ```S3_SECRET_KEY``` The private key to use when interacting with Amazon Web Services.
 - ```S3_ACCESS_KEY``` The user key identifying the user account to interact with Amazon Web Services.


**Data peristance service**  
Currently, KPI only supports Mongodb as the data persistance backend:

 - ```MONGO_URI``` URI with user and connection information for the data persistance backend.


**Top-level application settings**

 - ```DEBUG``` Boolean value indicating if stack traces and detailed debug information should be provided in the instance of an error.


<br>
Endpoints
---------
**POST /kpi/users.json**  
Create a new user in the user access controls service for the package index. Causes an email to be sent to the new user with a temporary password.

Form-encdoded params:  

 - ```username``` The name of the new user.
 - ```email``` The email address for the new user.

JSON-document returned:

 - ```success``` Boolean value indicating if successful. Will be true if new user created and false otherwise.
 - ```message``` Deatails about the result of the operation. Will be provided in both the success and failure cases.
  

<br>
**PUT /kpi/user/username.json**  
Updates a user in the user access controls service for the package index. This includes modifying the user's password.

Form-encdoded params:

 - ```username``` The name of the user to update.
 - ```old_password``` The current password for the user.
 - ```new_password``` The password to assign to the user.

JSON-document returned:

 - ```success``` Boolean value indicating if successful. Will be true if the user was updated and false otherwise.
 - ```message``` Details about the result of the operation. Will be provided in both the success and failure cases.

<br>
**POST /kpi/packages.json**  
Create a new package in the index. No prior packages may have the same name and the submitting user must be in the authors list.

Form-encoded params:  

 - ```username``` The username of the user who is creating a new package.
 - ```password``` The password of the user who is creating a new package.
 - ```authors``` CSV list of usernames who have authorial access to this package.
 - ```license``` String description of the license the package is released under (like MIT or GNU GPL v3)
 - ```name``` The machine safe name (any valid javascript identifier) of the package. 
 - ```humanName``` The name of the package to present to the user (can be any valid string).
 - ```version``` The major.minor.incremental (ex: 1.2.34) version number that this package is currently releasing.
 - May also include module.json fields listed in README for kpiclient.

JSON-document returned:

 - ```success``` Boolean value indicating if successful. Will be true if the package was created and false otherwise.
 - ```message``` Details about the result of the operation. Will be provided in both the success and failure cases.

<br>
**GET /kpi/package/package_name.json**  

Form-encoded params: none

JSON-document returned:

 - ```successful``` Boolean indicating if the package was found and read successfully.
 - ```message``` Information about the error encountered. Blank if no error.
 - ```username``` The username of the user who is updating the package.
 - ```password``` The password of the user who is updating the package.
 - ```record``` Information about the package.
   - ```authors``` CSV list of usernames who have authorial access to this package.
   - ```license``` String description of the license the package is released under (like MIT or GNU GPL v3)
   - ```name``` The machine safe name (any valid javascript identifier) of the package. 
   - ```humanName``` The name of the package to present to the user (can be any valid string).
   - ```version``` The major.minor.incremental (ex: 1.2.34) version number that this package is currently releasing.
   - ```description``` Human-friendly short description of the package. May contain markdown and may be a blank string.
   - ```homepage``` The URL to a website with more information for this package. May be a blank string.
   - ```repository``` The URL to a repository with the source for this package. May be a blank string.

<br>
**PUT /kpi/package/package_name.json**  
Update an existing package in the index. A prior packages must have the same name, the submitting user must have permissions to edit that package, and the submitting user must be in the authors list.

Form-encoded params:  

 - ```username``` The username of the user who is updating the package.
 - ```password``` The password of the user who is updating the package.
 - ```authors``` CSV list of usernames who have authorial access to this package.
 - ```license``` String description of the license the package is released under (like MIT or GNU GPL v3)
 - ```name``` The machine safe name (any valid javascript identifier) of the package. 
 - ```humanName``` The name of the package to present to the user (can be any valid string).
 - ```version``` The major.minor.incremental (ex: 1.2.34) version number that this package is currently releasing.
 - May also include module.json fields listed in README for kpiclient.

JSON-document returned:

 - ```success``` Boolean value indicating if successful. Will be true if the package was updated and false otherwise.
 - ```message``` Details about the result of the operation. Will be provided in both the success and failure cases.

<br>
**DELETE /kpi/package/package_name.json**  
Remove a new package from the the index. A prior packages must have the same name and the submitting user must have permissions to edit that package.

Form-encoded params:  

 - ```username``` The username of the user who is deleting the package.
 - ```password``` The password of the user who is deleting the package.

JSON-document returned:

 - ```success``` Boolean value indicating if successful. Will be true if the package was updated and false otherwise.
 - ```message``` Details about the result of the operation. Will be provided in both the success and failure cases.

<br>