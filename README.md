#ESTA API

##API Endpoint
[http://api.visas.io](http://api.visas.io)

##Summary of Resource URL Patterns
/v1/forms/esta/applications   
/v1/forms/esta/applications/{APPLICATION_NUMBER}   
/v1/forms/esta/applications/{APPLICATION_NUMBER}/payments  


|Resource                                     |GET|POST|PUT|DELETE|
|---------------------------------------------|:---:|:----:|:---:|:------:|
|`/applications`                              |[Get application number](#retrieve)|[Create application](#create)|X|X|
|`/applications/{APPLICATION_NUMBER}`         |[Get application data](#get-data)|X|[Update application](#update)|X|
|`/applications/{APPLICATION_NUMBER}/payments`|[Get payment data](#get-data)|[Create a payment](#pay)|X|X|


Resources are returned as JSON by default or if a .json extension is appended to the resource URI, so these requests below are valid:
`/v1/forms/esta/applications?query=string`
`/v1/forms/esta/applications?query=string&format=xml`
`/v1/forms/esta/applications.xml?query=string`

Available formats: JSON, XML, JSONP


##Authorization
`username` & `api_key` are always required

|Parameter|Required|Format  |Description  |Default|Example|
|---------|--------|--------|-------------|-------|-------|
|`username` |yes     |String  |Your username|       |simplevisa
|`api_key` |yes     |String  |Your API key |       |mogadishu21


####Examples queries:
`/v1/forms/esta/applications?passport_number=37SD7382&dob=1984-04-21&passport_country=FR&last_name=SMITH&first_name=JOHN`
`/v1/forms/esta/applications?format=xml&passport_number=37SD7382&dob=1984-04-21&passport_country=FR&last_name=SMITH&first_name=JOHN`
`/v1/forms/esta/applications.xml?passport_number=37SD7382&dob=1984-04-21&passport_country=FR&last_name=SMITH&first_name=JOHN`

####Failed authentication response:
#####JSON
```json
{"error":{"msg":"unauthorized", "details":"access denied"}}
```
#####XML
```xml
<?xml version='1.0' standalone='yes'?> 
<error details="access denied" msg="unauthorized" /> 
```

##Response format

If `format` is specified, the result will be sent in the selected format. Available format are **json**, **jsonp**, **xml** and **txt**

|Parameter|Required|Format  |Description  |Default|Example|
|---------|--------|--------|-------------|-------|-------|
|`format`   |No      |String  | Format to be returned | json | jsonp|


###json (application/json)
Response will be returned as a json string

```json
{
"result":"data"
}
```

###jsonp (text/javascript)
Response will be returned as a jsonp string, (a callback will wrap the json result)

```jsonp
callback({"result":"data"})
```
###xml (application/xml)

Response will be returned as an xml string

```xml
<?xml version='1.0' encoding='utf-8'?>
<result><data></data></result>
```

#GET 

##[Retrieve application number](id:retrieve)

###With manual parameters 

`/v1/forms/esta/applications{?passport_number,dob,passport_country,last_name,first_name}`

|Parameter|Required|Format  |Description  |Default|Example|
|---------|--------|--------|-------------|-------|-------|
|`lastname`|Yes|String|             |       |SMITH|
|`firstname`|Yes|String|             |       |JOHN|
|`dob`|Yes|Date|||1979-03-02|
|`passport`|Yes|String|||M4774088|
|`passport_country`|Yes|String|||AU|
|`get_data`|No|Boolean||false|true|
|`format`|No|options (json, xml, jsonp)||json|xml|

###With MRZ 

`/v1/forms/esta/applications{?line1,line2}`

|Parameter|Required|Format  |Description  |Default|Example|
|---------|--------|--------|-------------|-------|-------|
|`line1`|Yes|String|MRZ Line 1|       |`P<UTOERIKSSON<<ANNA<MARIA<<<<<<<<<<<<<<<<<<<`|
|`line2`|Yes|String|MRZ Line 2|       |`L898902C<3UTO6908061F9406236ZE184226B<<<<<14`|
|`get_data`|No|Boolean||false|true|

####Example Query

`/v1/forms/esta/applications?passport_number=37SD7382&dob=1984-04-21&passport_country=FR&last_name=SMITH&first_name=JOHN`

####Example Result:

**200 OK** - If your request was successful, you will receive a 200 OK response.

Return

* Application Status
* Application Number
* Application Expiration Date 

(@zsan: To improve speed,do not parse the application data here, except if `get_data` is set to `true`. If `get_data` is set to true, return the same info than [Get application data with application number](#get-data))

```
{
   "success": {
   },
   "data": {
   }
}
```
**400 BAD REQUEST** - If the information being queried isn't valid, you will receive a 400 BAD REQUEST response.
If a field sent is not correctly formatted, like incorrect date of birth, etc  

OR

**401 UNAUTHORIZED** - If you incorrectly specify your account credentials, you will receive a 401 UNAUTHORIZED response. This means you need to check that you have the correct Account SID and Auth Token specified.

OR

*~ no limit set right now* **403 FORBIDDEN** - If you have gone past your hourly throttle limits, you will receive a 403 FORBIDDEN response.

OR

**404 NOT FOUND** - If the information you're querying doesn't have any information available, you will receive a 404 NOT FOUND response. (querying an non-exisiting application should return a 404 not found status)


##[Get application data with application number](id:get-data)

If the application number is known, we can query it's data with the following:  

`/v1/forms/esta/applications/{APPLICATION_NUMBER}{?passport_number,dob}`

####Example Query

`/v1/forms/esta/applications/XXASGBWJJUSOUSDHJ87?passport_number=37SD7382&dob=1955-12-04`

|Parameter|Required|Format  |Description  |Default|Example|
|---------|--------|--------|-------------|-------|-------|
|`dob`|Yes|Date|             |       |1979-03-02|
|`passport`|Yes|String|             |       |M4774088|


####Example Result:
Actual today's api result

```json
{
    "success": {
        "msg": "Authorization Approved",
        "code": 200
    },
    "applicant": {
        "passports": [
            {
                "number": "618023949",
                "visas": [
                    {
                        "number": "X79XR197RB227722",
                        "status": "Authorization Approved",
                        "type": "esta",
                        "data": {
                            "visa": {
                                "PaymentAmount": "$14.00",
                                "PaymentTrackingCode": "090457",
                                "PaymentDate": "2012-06-07"
                            },
                            "application": {
                                "travels": [
                                    {
                                        "carrier": "",
                                        "us_address": {
                                            "city": "SAN FRANCISCO",
                                            "line1": "",
                                            "line2": "",
                                            "state": "CA"
                                        },
                                        "flight_number": "AB",
                                        "boarding_city": "DUSSELDORF"
                                    }
                                ],
                                "applicant": {
                                    "citizenship_country": "DE",
                                    "birth_country": "DE",
                                    "living_country": "CH"
                                },
                                "7qs": {
                                    "F": false,
                                    "A": false,
                                    "D": false,
                                    "C": false,
                                    "G": false,
                                    "E": false,
                                    "B": false
                                }
                            }
                        },
                        "expiring_date": "2014-06-07"
                    }
                ],
                "issuing_date": "2006-08-14",
                "issuing_country": "DE",
                "expiring_date": "2016-08-13"
            }
        ],
        "email": "",
        "dob": "1978-06-21",
        "firstname": "YVONNE",
        "lastname": "RIESENER",
        "phone": "",
        "sex": "F"
    }
}
```

#POST

##[Create application](id:create)
`/v1/forms/esta/applications`

|Parameter|Required|Format  |Description  |Default|Example|
|---------|--------|--------|-------------|-------|-------|
|`firstname`             |Yes      ||||emmet|
|`lastname`              |Yes      ||||rudoviz|
|`citizenship_country`           |Yes      ||||AU|
|`living_country`        |Yes      ||||AU|
|`birth_country`        |Yes      ||||AU|
|`sex`                   |Yes      ||||M|
|`dob`                   |Yes      ||||1979-03-02|
|`passport`              |Yes      ||||M4774088|
|`passport_country`      |Yes      ||||AU|
|`passport_issuing_date` |Yes      ||||2003-02-12|
|`passport_expiring_date`|Yes      ||||2013-02-12|
|`a`                     |No       |||false|false|
|`b`                     |No       |||false|false|
|`c`                     |No       |||false|false|
|`d`                     |No       |||false|false|
|`e`                     |No       |||false|false|
|`f`                     |No       |||false|false|
|`fwhere`                |If `f=true`|||NULL|NULL|
|`fwhen`                 |If `f=true`|||NULL|NULL|
|`g`                     |No       |||false|false|
|`email`                 |No       |||NULL|test@example.com|
|`phoneA`                |No       |||NULL|61|
|`phoneP`                |No       |||NULL|219265324|
|`boarding_city`          |No       |||NULL|MELBOURNE|
|[`carrier`](#carrier)   |No       |||NULL||
|`flight_number`          |No       |||NULL|VA|
|`us_address_line1`         |No       |||NULL|HOTEL BEACH
|`us_address_line2`        |No       |||NULL|COAST HIGHWAY
|`us_address_city`                  |No       |||NULL|HUNTINGTON BEACH
|[`us_address_state`](#us_address_state)                 |No       |||NULL|CA|
|`overwrite`             |No       |||false|false|


When data is posted, post the form on original website, but stop at Pending Payment (do not pay for the application).

@zsan: Select between these 2 success response solution while coding the api:

* **Success Response:** if script fast enough:

  * **Code:** 201 Created <br />
    **Content:** `{ message:"ok", status:"Pending Payment", APPLICATION_NUMBER : "XXDF4TG4D5HGRW4WG"}`
    
* **Success Response:** if script not fast enough:

  * **Code:** 202 Accepted <br />
    **Content:** `{ message:"ok", status:"processing"}`
 
* **Error Response:**

  * **code** 400   BAD REQUEST   
     **Content** `{ error : "Family Name is a required field" }`
 
  OR
  
  * **Code:** 409	Conflict <br />
    **Content:** `{ error : "Already Existing Application", msg :"Use overwrite=true to overwrite existing application" }` 

  OR

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "You are unauthorized to make this request." }`
    
##[Create a payment](id:pay)
`/v1/forms/esta/applications/{APPLICATION_NUMBER}/payments`

Add a payment to an application that needs payment

|Parameter|Required|Format  |Description  |Default|Example|
|---------|--------|--------|-------------|-------|-------|
|`passport` |Yes     |String  |Applicant's Passport number             |       |08WE55362|
|`dob` |Yes        |String|Applicant's Date of birth|       |1983-07-23|
|`card_on_file` |No|Boolean| Use of the card on file|`true`|`false`       |
|`cc_number` |If `card_on_file = false`|Number|             |       |4111111111111111|
|`cvv` |If `card_on_file = false`|Number|             |       |123|
|`first_name` |If `card_on_file = false`|String|             |       |Georges|
|`last_name` |If `card_on_file = false`|String|             |       |Smith|
|`type` |If `card_on_file = false`|String|             |       |VS|
|`addr_line_1` |If `card_on_file = false`|String|             |       |Street 1|
|`addr_line_2` |If `card_on_file = false`|String|             |       |Street 2|
|`city` |If `card_on_file = false`|String|             |       |New York|
|`state` |If `card_on_file = false`|String|             |       |NY|
|`zip_code` |If `card_on_file = false`|Number|             |       |10010|
|`country` |If `card_on_file = false`|String|             |       |US|
|`exp_month` |If `card_on_file = false`|Number|             |       |03|
|`exp_year` |If `card_on_file = false`|Number|             |       |2014|

@zsan: **Don't implement card_on_file = false while we don't have SSL**

**PCI Compliance**

While the use of this API greatly reduces the scope of PCI Compliance and increases your security, you’re still required to be compliant with the Payment Card Industry Data Security Standards (PCI-DSS) and the Payment Application Data Security Standards (PA-DSS) whenever applicable.

If you don't want to deal with PCI compliance, we recommend that you use the `card_on_file` parameter. 

* **Success Response:** if script not fast enough:

  * **Code:** 202 Accepted    
    **Content:** `{ message:"ok", status:"processing"}`
 
* **Error Response:**

  * **code** 400   BAD REQUEST   
  	**Content** `{ error : "Family Name is a required field" }`

  OR
  
  * **Code:** 409	Conflict   
    **Content:** `{ error : "Already Existing Application", msg :"Use overwrite=true to overwrite existing application" }` 

  OR

  * **Code:** 401 UNAUTHORIZED    
    **Content:** `{ error : "You are unauthorized to make this request." }`
    
#PUT

Update a specific application about basic data. 

`/v1/forms/esta/applications/{APPLICATION_NUMBER}`

To obtain the application number, use the GET method "**[Retrieve application number](#retrieve)**"

##[Update basic information](id:update)

It's possible to update these fields:

* `email`
* [`phoneA`](#phoneA)
* `phoneP`
* `boarding_city`
* [`carrier`](#carrier)
* `flight_number`
* `us_address_line_1`
* `us_address_line_2`
* `us_address_city`
* [`us_address_state`](#us_address_state)

|Parameter|Required|Format  |Description  |Default|Example|
|---------|--------|--------|-------------|-------|-------|
|`passport_number`|Yes|String|             |       |       |
|`dob`|Yes|String|             |       |       |
|`email`|No|String|             |       |       |
|[`phoneA`](#phoneA)|No|String|             |       |       |
|`phoneP`|No|String|             |       |       |
|`boarding_city`|No|String|             |       |       |
|[`carrier`](#carrier)|No|String|             |       |       |
|`flight_number`|No|String|             |       |       |
|`us_address_line_1`|No|String|             |       |       |
|`us_address_line_2`|No|String|             |       |       |
|`us_address_city`|No|String|             |       |       |
|[`us_address_state`](#us_address_state)|No|String|             |       |       |

To update core data, use POST method "**[Create](#create)**" with option `overwrite` set to `true`


* **Success Response:** if script not fast enough:

  * **Code:** 202 Accepted <br />
    **Content:** `{ message:"ok", status:"processing"}`
 
* **Error Response:**

  * **code** 400   BAD REQUEST   
  	**Content** `{ error : "The request is empty, nothing to update" }`

  OR

  * **Code:** 401 UNAUTHORIZED <br />
    **Content:** `{ error : "You are unauthorized to make this request." }`


#List of data

##[country](id:country)

##[phoneA](id:phoneA)

##[carrier](id:carrier)

##[us_address_state](id:us_address_state)

##table model
|Parameter|Required|Format  |Description  |Default|Example|
|---------|--------|--------|-------------|-------|-------|
|         |        |        |             |       |       |
