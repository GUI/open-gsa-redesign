---
title: Opportunity Management SOAP APIs
banner-heading: Opportunity Management SOAP APIs
---

## Overview

The Opportunity Management SOAP APIs will allow authorized users to submit and request opportunities data. This document will provide electronic users with the technical specifications required to utilize the Contract Opportunities Web Services capability.

**Note:** The specifications on this page are for a soon to be released API. Check back here or be in contact with IAE for the release date and testing session.

## Getting Started

### Generating a System Account API Key
* Users registered with a government email address and have appropriate Opportunities Domain role may request a system account for data access.
* If a user satisfies the above registration criteria they will be able to access the System Accounts widget from their Workspace page after logging in.
* The user can then select “Go to System Accounts” from the widget and fill out the required sections.
* The requested system account will then need to be approved. After approval the user will be notified via email and they can also see the updated status in the System Account widget.
* The user can select ‘Go to System Accounts’ again in the widget from their workspace and enter a new system account password.
* After setting up the password the user will see a new section for retrieving a system account API Key.
* The user must enter their password again to retrieve the key.

## Web Services Description Language (WSDL)
To view the WSDL for all available methods and object definitions, refer below links:
* Alpha WSDL Link: https://api-alpha.sam.gov/prodlike/ws/services.WSDL
* Beta WSDL Link: TBD
WSDL attached below can be downloaded:

## Authentication

### *User Accounts*
To call any of the available web services, a valid government user account must exist in the beta.sam.gov system registered at the Office Location Level in the hierarchy. To perform an operation, user who is registered with beta.sam.gov should have either Contracting Officer role OR Contracting Specialist role. Note that to perform an operation, user must have only one role.
Refer to section 5 and 6 for role specific methods.

**Note** To submit any opportunity for an office, user should provide office org key or AAC. If office org key is known, please provide the same in the 'officeid' field in the requests. If office org key is not available, then users can provide AAC in place of office org key in 'officeid' field. In order to get AAC:
* On beta.sam.gov, please log in and click on the profile and go to Account Details. AAC is listed under 'Organization Information' section.
* On alpha.sam.gov, please log in and click on the profile and go to Account Details. AAC is listed under 'Organization Information' section.

### *Authentication Methods*
beta.SAM.gov Web Services supports SOAP header authentication. Configure your client to send a specific SOAP header with every method call which contains the authentication data. Below is the example of header included before the body:
   <soapenv:Header>
      <AuthenticationData xsi:type="sam:AuthenticationData">
        <username xsi:type="xsd:string">system account username</username>
        <password xsi:type="xsd:string">system account password</password>
        <emailid xsi:type="xsd:string">ContractingOfficeEmail@gsa.gov</emailid>
     </AuthenticationData>
   </soapenv:Header>

**Note**:
When submitting, retrieving or archiving/unarchiving, if user provided officeId along with system account user name and password, then the service first validates if the officeId provided is a valid office in Federal Hierarchy. If it’s a valid office, then the service validates if the given system account has access to that office. If the system account has access to the office, only then the user can proceed ahead with the SOAP services.

When the given officeId is not a valid office in Federal Hierarchy, then the service throws below error and user cannot proceed ahead with using SOAP services:
*Insufficient privileges to retrieve system account profile as the given organization is invalid*

When the given officeId is valid but does not fall under the approved Federal Hierarchy for the given system account, then the service throws below error and user cannot proceed ahead with using SOAP services:
*Insufficient privileges to retrieve system account profile as the given organization is not part of the approved FH hierarchy*

**Note**:
The complex type definition for this object (AuthenticationData) is located in the WSDL. It contains three string elements named “username”, “password” and “emailid”. Refer the WSDL attached below:

### *Namespace Guidance*
The authentication namespace must match for a web service call to be successful.
This is due to core settings for the web services internals where the authorization header validates the namespace against the WSDL. So, when the namespace for your authentication header in soap xml does not match the namespace defined in the WSDL at the endpoint (in this case sam), it does not pass on the credentials (username/password/emailid). Therefore, the Contract Opportunities service is not able to authenticate the user and returns an authentication error.

Test Server Namespace: https://www.sam.gov
Production Server Namespace: https://www.sam.gov

## Method Overview
All methods available can be found in the WSDL and will be listed in this document. Methods will take different parameters ranging from basic types (string/integer/boolean/date and array of these types) or complex data types that are further comprised of these basic types and sometimes other complex data types.  
Supported input content type formats are text/xml.
Note: For all elements/parameters that are specified as type “date,” please supply date in YYYYMMDD (i.e. 20090428) format.

### *Responses*
Most methods will return data in the format of the PostingResponse complex type. This consists of two elements:
* The first element is named ‘success’ and is a Boolean value. If the method successfully completed, this element will be true or 1.  If it is false, empty, or 0, then the method was not successful.
* The second element is named ‘messages’ and is an array of strings. Mostly for error cases, this element will contain any relevant error messages (or sometimes success messages) that pertain to the web services method called.

Posting Response Complex Type Definition

Element Name | Type
------- | -------
success | boolean
messages | string [] - array of strings

**Note**: Some methods will have a different response value format due to the nature of the data being returned. These custom cases will be outlined below.

### Set-Aside Values
Several methods pertaining to submitting Contract Opportunities involve the Set-Aside Type field.

Refer below table for mapping between legacy SetAside Values to modern SetAside Value:

Modern SetAside Values | Legacy SetAside values
------- | -------
Total Small Business Set-Aside (FAR 19.5)	| Total Small Business
Partial Small Business Set-Aside (FAR 19.5) |	Partial Small Business
8(a) Set-Aside (FAR 19.8)	| Competitive 8(a)
8(a) Sole Source (FAR 19.8)	| Competitive 8(a)
Historically Underutilized Business (HUBZone) Set-Aside (FAR 19.13) |	HUBZone
Historically Underutilized Business (HUBZone) Sole Source (FAR 19.13) |	HUBZone
Service-Disabled Veteran-Owned Small Business (SDVOSB) Set-Aside (FAR 19.14) |	Service-Disabled Veteran-Owned Small Business
Service-Disabled Veteran-Owned Small Business (SDVOSB) Sole Source (FAR 19.14) |	Service-Disabled Veteran-Owned Small Business
Women-Owned Small Business (WOSB) Program Set-Aside (FAR 19.15) |	Women-Owned Small Business
Women-Owned Small Business (WOSB) Program Sole Source (FAR 19.15) |	Women-Owned Small Business
Economically Disadvantaged WOSB (EDWOSB) Program Set-Aside (FAR 19.15) |	Economically Disadvantaged Women-Owned Small Business
Economically Disadvantaged WOSB (EDWOSB) Program Sole Source (FAR 19.15) |	Economically Disadvantaged Women-Owned Small Business
Local Area Set-Aside (FAR 26.2)	|

### Notice Types
The web service API includes specific methods to submit each of the base notice types (i.e. presolicitation, combined/synopsis, award, etc.). You will find these outlined in the sections below.

### Stauth Valid Values
Below table captures stauth values to use while making requests as needed.

Code | Description
------- | --------
1 |	Urgency
2	| Only One Source (except brand name)
3 |	Follow-on Delivery Order Following Competitive Initial Order
4 |	Minimum Guarantee
5 |	Other Statutory Authority (e.g. 8a, etc.)
brand |	FAR 6.302-1(c) - Brand name
far1 | FAR 6.302-1 - Only one responsible source (except brand name)
far2 | FAR 6.302-2 - Unusual and compelling urgency
far3 | FAR 6.302-3 - Industrial mobilization; engineering, developmental or research capability; or expert services
far4 | FAR 6.302-4 - International agreement
far5 | FAR 6.302-5 - Authorized or required by statute
far6 | FAR 6.302-6  - National security
far7 | FAR 6.302-7 - Public interest

## Contracting Officer Method Details
### Award Notice (submitAward)
This method is used to submit an award notice.

Input parameters:

Input Parameter | Type | Description
------- | ------- | -------
Data | Award | Complex type defined

Response:

Output Parameter | Type | Description
------ | ------- | -------
Response | PostingResponse | ComplexType

Award Complex Type Definition:

Element Name | Type | Required | Description | Character Limit / Restrictions
------ | ------- | ------- | ------- | -------
date |	date |	No |	Posting Date |	YYYYMMDD
zip |	string |	No |	Zip Code |	5 digits
classcod |	string |	No |	Class-Code |	Valid classification code (FAR, Section 5.207(g))
naics |	string |	No |	NAICS Code |	Valid NAICS Code NAICS Reference
offadd |	string |	No |	Office Address |	65535 characters
officeid |	string |	Yes |	Office id of the office where an opportunity is being submitted |	20 characters
subject |	string |	Yes |	Subject |	255 characters
solnbr |	string |	Yes |	Sol # |	128 characters from the set: a-z A-Z 0-9 - _ ( ) {}
ntype	| string |	No |	Base Notice Type |	Valid values: "PRESOL" - for Presolicitation, "COMBINE" - for Combined Synopsis/Solicitation, "SRCSGT" - for Sources Sought, "SSALE" - for Sale of Surplus Property, "SNOTE" - for Special Notice, “ITB” - for Intend to bundle
awdnbr |	string |	 Yes |	Award Number |	255 characters
awdamt |	string |	Yes |	Award Amount |	64 characters
linenbr |	string |	No |	Line Number |	255 characters
awddate |	date |	Yes |	Award Date |	YYYYMMDD
archdate |	date |	No |	Archive Date |	YYYYMMDD
awardee |	string |	Yes |	Awardee |	65535 characters
awardee_duns |	string |	No |	Awardee DUNS |	9 digits with optional plus 4
contact |	string |	Yes |	Contact Info |	65535 characters
desc |	string |	No |	Description |	65535 characters
link |	GovURL |	No |	Government Link	255 characters, consist of a restricted set of characters (see URL specification - RFC 2396)
email |	GovEmail |	No |	Government Email |	128 characters
links |	DocumentLink[] |	No |	Array Of links |
files |	DocumentFile[] |	No |	Array of files |
setaside |	string |	No |	Set Aside |	See Set Aside Value Section for valid values
recovery_act |	boolean |	No |	Recovery Act |	true or false
correction |	boolean |	No |	Correction of previous Award |	true or false. If correcting a previously submitted award notice, specify true and the system will lookup the award by award number and sol number if applicable.

GovURL Complex Type Definition

Element Name | Type | Required | Description | Character Limit / Restrictions
------ | ------- | ------- | ------- | -------
url |	string |	yes |	Website Address |	255 characters, consist of a restricted set of characters (see URL specification - RFC 2396)
desc |	string |	yes |	Description |	255 characters

GovEmail Complex Type Definition

Element Name | Type | Required | Description | Character Limit / Restrictions
------ | ------- | ------- | ------- | -------
address | string |	Yes |	Email Address |	128 characters
desc |	string |	Yes |	Description |	255 characters

DocumentLink Complex Type Definition

Element Name | Type | Required | Description | Character Limit / Restrictions
------ | ------- | ------- | ------- | -------
url |	string |	No |	External URL |	255 characters, consist of a restricted set of characters (see URL specification - RFC 2396)
Desc |	string |	No |	Description/Title |	255 characters

DocumentFile Complex Type Definition

Element Name | Type | Required | Description | Character Limit / Restrictions
------ | ------- | ------- | ------- | -------
filename |	string |	No |	File Name |	255 characters
filedata |	base64binary |	No |	File Data |	100 MB
desc |	string |	No |	Description |	255 characters
explicit_access |	boolean |	No |	Explicit Access |
export_controlled |	boolean	No |	Export Controlled |

### Delete Notice/ Document Package (deleteNoticeOrDocumentPackage)

This method is used to permanently delete an entire notice or delete attachments across all versions of the notice. Modifications/Amendments are recommended instead of using this method. Specify the solicitation number or award number to delete a notice. To delete attachments, also specify the attachment deletetype.

Input Parameters:

Input Parameter |	Type |	Description
------- | ------ | -------
data |	DeleteNoticeOrDocumentPackage |	Complex type defined below

Response:

Output Parameter |	Type |	Description
------- | ------ | -------
solnbr |	string |	no |	Solicitation # |	128 characters from the set: a-z 0-9 -_ ( ) { }
ntype |	string |	no |	Base Notice Type |	Valid values: "PRESOL" - for Presolicitation, "COMBINE" - for Combined Synopsis/Solicitation, "SRCSGT" - for Sources Sought, "SSALE" - for Sale of Surplus Property, "SNOTE" - for Special Notice, "ITB" - for Intent to Bundle Requirements (DoD- Funded)
awdnbr | string | no | Award # |	255 characters
deletetype |	string |	no |	Notice or Attachment delete operation type |	Valid Values: “notice” for notice, “attachment” for attachment. Defaults to “notice” if not provided
deletemethod |	string | no | Delete latest or all versions |	Valid Values: “latest” for latest version, “all” for all versions. Defaults to “all” if not provided

DeleteNoticeOrDocumentPackage Complex Type Definition:

Element Name | Type | Required | Description | Character Limit / Restrictions
------ | ------- | ------- | ------- | -------
solnbr |	string |	no |	Solicitation # | 128 characters from the set: a-z 0-9 -_ ( ) { }
ntype |	string |	no |	Base Notice Type | Valid values: "PRESOL" - for Presolicitation, "COMBINE" - for Combined Synopsis/Solicitation, "SRCSGT" - for Sources Sought, "SSALE" - for Sale of Surplus Property, "SNOTE" - for Special Notice, "ITB" - for Intent to Bundle Requirements (DoD- Funded)
awdnbr |  string | no | Award # |	255 characters
deletetype |	string |	no |	Notice or Attachment delete operation type |	Valid Values: “notice” for notice, “attachment” for attachment. Defaults to “notice” if not provided
deletemethod |	string | no | Delete latest or all versions |	Valid Values: “latest” for latest version, “all” for all versions. Defaults to “all” if not provided

### Archive Notice (ArchiveNotice)

This method is used to update the archive date on an existing notice.  If a past date is provided or no date provided at all, the notice will be immediately archived.

Input Parameters:

Input Parameter |	Type |	Description
------- | ------ | -------
data | ArchiveNotice | Complex type defined below

Response:

Output Parameter |	Type |	Description
------- | ------ | -------
response | PostingResponse | Complex type defined below

ArchiveNotice Complex Type Definition:

Element Name | Type | Required | Description | Character Limit / Restrictions
------ | ------- | ------- | ------- | -------
date |	date |	No |	Posting Date | YYYYMMDD
solnbr | string |	Yes |	Solicitation # | 128 characters from the set: a-z 0-9 -_ ( ) { }
ntype |	string | no |	Base Notice Type | Valid values: "PRESOL" - for Presolicitation, "COMBINE" - for Combined Synopsis/Solicitation, "SRCSGT" - for Sources Sought, "SSALE" - for Sale of Surplus Property, "SNOTE" - for Special Notice, "ITB" - for Intent to Bundle Requirements (DoD- Funded)
archdate | date |	no | New Archive Date – If none provided, notice will archive immediately | YYYYMMDD
officeid | string |	Yes |	Office id of the office where an opportunity is being submitted. Officeid must be associated with user account |	20 characters

### Cancel Notice (CancelNotice)





## FAQ

<p><small><a href="#">Back to top</a></small></p>

## Contact Us

* Reach out to the beta.sam.gov team at [newsamtesting@gsa.gov](mailto:newsamtesting@gsa.gov).

<p><small><a href="#">Back to top</a></small></p>