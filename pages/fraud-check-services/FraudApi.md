# Fraud checking API

Fraud checking API is an additional service used to further identify a person and stop fraudulent activities.

### Available services:

- (*AML*) Peps & Sanctions name check
- (*AML*) Negative articles check
- (*LID*) Lost and Invalid documents registry check

### Calling the API:

Send a *HTTP Post* request to: [https://fraud.idenfy.com/V1/check](https://fraud.idenfy.com/V1/check)<br>
The request must contain *basic auth* headers where *username* is *api key* and *password* is *api secret*.<br>

The request must contain JSON with parameters:

|Key       |Required|Type          |Constraints|Explanation|
|----------|--------|--------------|-----------|-----------|
|`mode`    |Yes     |`String`      |Values:<br>&nbsp;&nbsp;&nbsp;&nbsp;-`DATA`<br>&nbsp;&nbsp;&nbsp;&nbsp;-`IDENTIFICATION`|Specifies how fraud check will be performed. If `IDENTIFICATION` is given, you will have to provide a scanRef in the data field. If `DATA` is given, you will have to provide all necessary data about client for required services.|
|`services`|Yes     |`List[String]`|Values:<br>&nbsp;&nbsp;&nbsp;&nbsp;-`AML`<br>&nbsp;&nbsp;&nbsp;&nbsp;-`LID`|Types of services to apply. `AML` - Anti money laundering service, `LID` - Lost and invalid documents registry.|
|`data`    |Yes     |`List[Dict]`  |-          |Supplied data for fraud check services.|

 ### Receiving response
|Key       |Type         |Explanation|
|----------|-------------|-----------|
|`AML`     |`List[Dict]` |Fraud check results from AML services. If `AML` service was not specified in the request this key is not present in the response. For a complete response documentation please refer to [AML documentation]().|
|`LID`     |`List[Dict]` |Fraud check results from LID services. If `LID` service was not specified in the request this key is not present in the response. For a complete response documentation please refer to [LID documentation]().|

### Examples

#### Example AML check

##### Example request

```json
{
  "mode": "DATA",
  "services": [
    "AML"
  ],
  "data": [
    {
    	"name": "Barack",
        "surname": "Obama"
    }
  ]
}
```

##### Example response

```json
{
  "AML": [
    {
      "status": {
        "serviceSuspected": false,
        "serviceUsed": true,
        "serviceFound": true,
        "checkSuccessful": true,
        "overallStatus": "NOT_SUSPECTED"
      },
      "serviceName": "PilotApiAmlV2NameCheck",
      "serviceGroupType": "AML",
      "uid": "0SZEHQ7S677R0830NJXA2JF9C",
      "errorMessage": null,
      "data": [
        {
          "name": "BARACK",
          "surname": "OBAMA",
          "reason": "USA: WHITE HOUSE: PRESIDENT (EX)",
          "nationality": "USA",
          "dob": "1961",
          "suspicion": "PEPS",
          "score": 98,
          "lastUpdate": "2012-04-02",
          "isPerson": true,
          "isActive": false
        }
      ]
    }
  ]
}
```

##### Example request

```json
{
  "mode": "DATA",
  "services": [
    "AML"
  ],
  "data": [
    {
    	"name": "Barack",
        "surname": "Obama",
        "negativeNews": true,
        "nameCheck": true
    }
  ]
}
```

##### Example response

```json
{
  "AML": [
    {
      "status": {
        "serviceSuspected": false,
        "serviceUsed": true,
        "serviceFound": true,
        "checkSuccessful": true,
        "overallStatus": "NOT_SUSPECTED"
      },
      "serviceName": "PilotApiAmlV2NameCheck",
      "serviceGroupType": "AML",
      "uid": "3639GMXA7XYOLULXIJKU64PH2",
      "errorMessage": null,
      "data": [
        {
          "name": "BARAK",
          "surname": "OBAMA",
          "reason": "USA: WHITE HOUSE: PRESIDENT (EX)",
          "nationality": "USA",
          "dob": "1961",
          "suspicion": "PEPS",
          "score": 98,
          "lastUpdate": "2012-04-02",
          "isPerson": true,
          "isActive": false
        }
      ]
    },
    {
      "status": {
        "serviceSuspected": true,
        "serviceUsed": true,
        "serviceFound": true,
        "checkSuccessful": true,
        "overallStatus": "SUSPECTED"
      },
      "serviceName": "PilotApiAmlV2NegativeNews",
      "serviceGroupType": "AML",
      "uid": "3639GMXA7XYOLULXIJKU64PH2",
      "errorMessage": null,
      "data": [
        {
          "title": "BARACK OBAMA - THE OFFICE OF BARACK AND MICHELLE OBAMA",
          "url": "HTTPS://BARACKOBAMA.COM/",
          "text": "AS PRESIDENT OBAMA HAS SAID, THE CHANGE WE SEEK WILL TAKE LONGER THAN ONE TERM OR ONE PRESIDENCY. REAL CHANGE--BIG CHANGE--TAKES MANY YEARS AND REQUIRES EACH GENERATION TO EMBRACE THE OBLIGATIONS AND OPPORTUNITIES THAT COME WITH THE TITLE OF CITIZEN.",
          "type": "NEGATIVE"
        },
        {
          "title": "BARACK OBAMA (@BARACKOBAMA) | TWITTER",
          "url": "HTTPS://TWITTER.COM/BARACKOBAMA",
          "text": "THE LATEST TWEETS FROM BARACK OBAMA (@BARACKOBAMA). DAD, HUSBAND, PRESIDENT, CITIZEN. WASHINGTON, DC",
          "type": "NEGATIVE"
        },
        {
          "title": "BARACK OBAMA | THE WHITE HOUSE",
          "url": "HTTPS://WWW.WHITEHOUSE.GOV/ABOUT-THE-WHITE-HOUSE/PRESIDENTS/BARACK-OBAMA/",
          "text": "BARACK OBAMA SERVED AS THE 44TH PRESIDENT OF THE UNITED STATES. HIS STORY IS THE AMERICAN STORY -- VALUES FROM THE HEARTLAND, A MIDDLE-CLASS UPBRINGING IN A STRONG FAMILY, HARD WORK AND EDUCATION ...",
          "type": "NEGATIVE"
        }
      ]
    }
  ]
}
```

#### Example LID check

##### Example request

```json
{
  "mode": "DATA",
  "services": [
    "LID"
  ],
  "data": [
    {
    	"type": "PASSPORT",
        "number": "74587539",
        "country": "LT"
    }
  ]
}
```

##### Example response

```json
{
  "LID": [
    {
      "status": {
        "serviceSuspected": true,
        "serviceUsed": true,
        "serviceFound": true,
        "checkSuccessful": true,
        "overallStatus": "SUSPECTED"
      },
      "serviceName": "IrdInvalidPapers",
      "serviceGroupType": "LID",
      "uid": "KBSZDZPBJ3FS6NOT4L33VZ0KS",
      "errorMessage": null,
      "data": [
        {
          "documentNumber": "74587539",
          "documentType": null,
          "valid": false,
          "expiryDate": "2018-03-21"
        }
      ]
    }
  ]
}
```