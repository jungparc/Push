## Notification > Push > API v2.1 Guide

### Overview of v2.1 API 

#### Add 
- Added 'Get tokens with token' API. 
- Added 'ADM' push type. 

### Basic Information 
#### Endpoint
```
API Endpoint: https://api-push.cloud.toast.com
Endpoint for Collecting Message Delivery Receipt: https://collector-push.cloud.toast.com
```

#### Secret Key
- Available on console. 
- To call APIs requiring a secret key, configure the header as below: 
```
Header
X-Secret-Key: [a-zA-Z0-9]{8}
```

Go to [Console] > [Notification] > [Push] > [URL & AppKey] to create one. 

#### Response

##### Response HTTP Status Code
200 OK.  
Respond with 200 OK for all API requests.  
See Header at the response body for response details. 

##### Response Header

```
{
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```
##### resultCode, resultMessage

| isSuccessful | resultCode | resultMessage |
| --- | --- | --- |
| true | 0 | SUCCESS |
| false | 40001 | Client Error. Parameter is invalid. |
| false | 40002 | Client Error. Parameter is invalid format. |
| false | 40003 | Client Error. Parameter is empty or null. |
| false | 40004 | Client Error. Duplicate certificate. |
| false | 40005 | Client Error. Expired certificate. |
| false | 40006 | Client Error. Already registered. |
| false | 40007 | Client Error. Maximum limit exceeded. |
| false | 40008 | Client Error. Already completed. |
| false | 40101 | Client Error. Access is not allowed. |
| false | 40102 | Client Error. Unavailable key. |
| false | 40401 | Client Error. Not found. |
| false | 50001 ~ 50501 | Internal Error. Please report this. 'http://cloud.toast.com/support/faq'. |
| false | 400 | Client Error. Client error occurred at tag API. |
| false | 500 | Internal Error. Internal error occurred at tag API. |

## Tokens 
### Create 
- Can be queried from client. 

##### Method, URL
```
POST /push/v2.1/appkeys/{appkey}/tokens
Content-Type: application/json;charset=UTF-8
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |

##### Request Body

```
{
  "oldToken": "oldToken",
  "token": "token",
  "isNotificationAgreement": true,
  "isAdAgreement": true,
  "isNightAdAgreement": true,
  "pushType": "GCM",
  "timezoneId": "Asia/Seoul",
  "uid": "uid",
  "country": "KR",
  "language": "ko",
  "deviceId": "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD"
}
```

|Field|	Usage | Description |
|---|---|---|
|token|	Required, String| Token, no more than 1,600 characters |
|oldToken|	Optional, String|	Existing token, no more than 1,600 characters |
|pushType|	Required, String| 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
|isNotificationAgreement|	Required, Boolean|	true or false|
|isAdAgreement|	Required, Boolean|	true or false|
|isNightAdAgreement| Required, Boolean|	true or false|
|timezoneId|	Required, String|	Area/Name. IANA time zone database.|
|country|	Required, String| ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3 characters |
|language|	Required, String| ISO 639-1, ISO 639-2, iOS(language code + script code), 8 characters |
|uid|	Required, String| User ID, up to 64 characters, no emojis allowed, |
|deviceId|	Optional, String| Device ID, up to 36 characters |


##### Response Body

```
{
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/tokens -d '{"oldToken":"oldToken","token":"token","isNotificationAgreement":true,"isAdAgreement":true,"isNightAdAgreement":true,"pushType":"GCM","timezoneId":"Asia/Seoul","uid":"uid","country":"KR","language":"ko"}'
```

##### Description

- If token is registered again when it is already registered, existing information is updated. 
- If a token is to be changed, set existing token for oldToken and new token for token, and register; then, it is updated to a new token. 
- "isNotificationAgreement" refers to consent to receive push messages; "isAdAgreement" for ad push messages, and "isNightAdAgreement" for night-time ad push messages. 
- For instance, to receive all push messages, set true for all three fields. To receive push messages only, set true for "isNotificationAgreement" only.  
- Consent to receive push messages is in accordance with Act on Promotion of Information and Communications Network Utilization and Information Protection, etc. (from Article 50 to 50-8).  
    - [Go to KISA Guidelines](https://spam.kisa.or.kr/spam/na/ntt/selectNttInfo.do?mi=1020&nttSn=1171&bbsId=1002)    
    - [Check the Act](http://www.law.go.kr/lsEfInfoP.do?lsiSeq=123210#)  
- Response may be delayed due to many reasons, including bad network connection. To minimize effects on mobile application operations, it is recommended to set shorter timeout, and register tokens every time they are operated. 
- Tokens may be re-issued, on many accounts, including security issues, or app updates or deletion. Although they may not be frequently changed, it is recommended to register the most updated tokens whenever they are operated, so as to raise the receiving rate. 
- Even if a token is expired due to app deletion, it is not immediately applied to GCM or APNS server, so push message delivery can be successful after app is deleted. 

### Query 
#### Query Tokens by Token or Push Type 
- Can be queried from client. 
##### Method, URL

```
GET /push/v2.1/appkeys/{appkey}/tokens/{token}?pushType={pushType}
Content-Type: application/json;charset=UTF-8
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| pushType | Required, String | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |

##### Response Body

```
{
	"token" : {
		"pushType" : "GCM",
		"isNotificationAgreement": true,
		"isAdAgreement": true,
		"isNightAdAgreement": true,
		"timezoneId" : "Asia/Seoul",
		"country": "KR",
		"language": "ko",
		"uid" : "User ID",
		"token" : "Token",
		"updateDateTime": "2017-08-12T01:04:18.000+09:00",
		"adAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
		"nightAdAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
		"deviceId": "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD",
		"activatedDateTime": "2017-08-12T01:04:19.000+09:00"
	},
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| updateDateTime | -, DateTime String | Date and time of token update |
| adAgreementDateTime | -, DateTime String | Date and time of consent to receive ad push messages |
| nightAdAgreementDateTime | -, DateTime String | Date and time of consent to receive night-time ad push messages |
| deviceId | -, String | Device ID |
| activatedDateTime | -, Datetime String | Date and time of request for recent token registration |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/tokens/TOKEN?pushType=GCM
```

#### Query Tokens by User ID 
- The API requires a secret key and must be called from a server. 
##### Method, URL

```
GET /push/v2.1/appkeys/{appkey}/tokens?uid={uid}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| uid | Required, String | User ID to query |

##### Response Body

```
{
	"tokens": [{
		"pushType" : "GCM",
		"isNotificationAgreement": true,
		"isAdAgreement": true,
		"isNightAdAgreement": true,
		"timezoneId" : "Asia/Seoul",
		"country": "KR",
		"language": "ko",
		"uid" : "User ID",
		"token" : "Token",
        "updateDateTime": "2017-08-12T01:04:18.000+09:00",
        "adAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
        "nightAdAgreementDateTime": "2017-08-12T01:04:19.000+09:00"
	}],
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/tokens?uid=uid
```

#### Query Invalid Tokens 
##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/invalid-tokens?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&messageId={messageId}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable,  appkey issued on product use |
| pageIndex | Optional, Number | Default is 0 |
| pageSize | Optional, Number | Default is 25; max is 100 |
| from | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| messageId | Optional, Number | Message ID in which invalid token occurs |

##### Request Body
```
N/A 
```
##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"invalidTokens" : [{
			"messageId" : 0,
			"uid" : "uid",
			"token" : "invalid-token",
			"pushType" : "GCM",
			"createdDateTime" : "2017-02-08T19:39:04.000+09:00"
		}
	]
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/invalid-tokens
```


#### Query Statistics for Token Attributes API 

**This API has been deprecated. Please use API v2.4 or higher.**

##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/statistics/token-properties?from={from}&to={to}&tokenProperties={tokenProperties}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| from | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| tokenProperties | Optional, String Array | 'agreement', 'country', 'language', 'timezoneId'<br/> delimited by ',', e.g. tokenProperties=country,language |

##### Request Body
```
N/A
```

##### Response Body
```json
{
	"tokenPropertiesStatistics" : [{
			"dateTime" : "2016-07-11 17:50:00.00+9:00",
			"countries" : {
				"KR" : 100,
				"JP" : 60,
				"CN" : 100
			},
			"languages" : {
				"ko" : 90,
				"ja" : 60,
				"zh" : 100
			},
			"timezoneIds": {
				"Asia/Seoul": 260
			},
			"agreements": {
				"ON": 260
			}
		}, {
			"dateTime" : "2016-07-11 17:51:00.00+9:00",
			"countries" : {
				"KR" : 100,
				"JP" : 60,
				"CN" : 100
			},
			"languages" : {
				"ko" : 90,
				"ja" : 60,
				"zh" : 100
			},
			"timezoneIds": {
				"Asia/Seoul": 260
			},
			"agreements": {
				"ON": 260
			}
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| dateTime | String | Date and time of data collection |
| agreements | String | 'ON' (receive all), 'NIGHT_AD_OFF' (reject night-time ads), 'AD_OFF' (reject ads), 'OFF' (reject all) |
| countries.XX | String | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3 characters |
| languages.XX | String | ISO 639-1, ISO 639-2, iOS (language code + script code), 8 characters |
| timezoneIds.XX | String | Area/Name. IANA time zone database |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/statistics/token-properties
```

#### Query Statistics for Token Registration 

**This API has been deprecated. Please use API v2.4 or higher.**

##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/statistics/token-registrations?from={from}&to={to}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| from | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |

##### Request Body
```
N/A
```

##### Response Body
```json
{
	"tokenRegistrationStatistics" : [{
			"dateTime" : "2016-07-11 17:50:00.00+9:00",
			"registered" : 90,
			"deleted" : 20
		}, {
			"dateTime" : "2016-07-11 17:51:00.00+9:00",
			"registered" : 45,
			"deleted" : 10
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| dateTime | String | Date and time of data collection |
| registered | Number | Number of registered tokens |
| deleted | Number | Number of deleted tokens |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/statistics/token-registrations
```

## Messages 
### Send  
※ Push messages sent by API cannot be retrieved in the console or by the Get or List API.
##### Method, URL, Headers
```
POST /push/v2.1/appkeys/{appkey}/messages
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```json
{
    "target" : {
        "type" : "UID",
        "to": ["uid-1", "uid-2"]
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body"
        }
    },
    "messageType" : "AD",
    "contact": "1588-1588",
    "removeGuide": "Menu > Settings",
    "timeToLiveMinute": 1,
    "provisionedResourceId": "id",
    "adWordPosition": "TITLE"
}
```

##### Response Body
```json
{
    "message" : {
        "messageId" : 0,
        "messageIdString": "0"
    },
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "SUCCESS"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| target.type | Required, String | Type of receiving targets: 'ALL', 'UID', or 'TAG' |
| target.to | Optional, String Array | target.type as UID list for recipients (up to 10,000) or as TAG condition |
| target.pushTypes | Optional, String Array | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
| target.countries | Optional, String Array | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3 (up to three characters) |
| content | Required, Map | To be delivered to recipients (up to 8,192 characters) |
| content.default | Required, Map | See 'common message type as below for more details |
| content.default.title | Optional, String |  |
| content.default.body | Optional, String |  |
| messageType | Required, String | NOTIFICATION, AD |
| contact | Optional, String | Required, if the messageType is AD; only numbers (0-9) and hyphens (-) are available. |
| removeGuide | Optional, String | Required, if the messageType is AD |
| timeToLiveMinute | Optional, Number | By the minute: from 1 to 60. Default is 10. |
| provisionedResourceId | Optional, String | ID for provisioned resource. <br />Contact support@cloud.toast.com for details. |
| adWordPosition | Optional, String | Places where ad phrases, like 'TITLE' or 'BODY' are located: default is 'TITLE'. |

##### Description
- If "target.type" is set with 'UID', up to 10,000 UIDs can be set for "target.to". 
- If 'TAG' is set for "target.type", a tag ID, three conditions, and one parenthesis ('()') can be set for "target.to". 
    - e.g. To deliver messages for those tagged with men, and the 30s, or women,     
    you can set "target.to=(,men_ID,AND,30s_ID,),OR,women_ID".
- Messages can be delivered only in particular push types, with the "target.pushTypes" field.  
If not defined, they can be delivered to all push types, including, GCM, APNS, APNS_SANDBOX, TENCENT, and ADM.
- If the "target.countries" field is "['KR', 'JP']", messages are delivered to tokens of which the national codes are "KR" or "JP". 
- "content.default" is a required field, and for more details regarding "content", see [Common Message Format] as below.
- To send messages in the "messageType": "AD" type, it is required to include the "contact" and "removeGuide" fields. 
Enter contact information in "contact", and how to withdraw receiving in "removeGuide".  
- With the timeToLiveMinute setting, delivery delays beyond certain configured time shall be automatically processed as failure.  

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/messages -d '{"target":{"type":"UID","to":["uid"]},"content":{"default":{"title":"title","body":"body","customKey1":"It is default"},"ko":{"title":"Title","body":"Body","customKey2":"It is Korean."}},"messageType":"AD","contact":"1588-1588","removeGuide":"Menu > Setting","timeToLiveMinute":1}'
```

### Common Messages 
When messages are written for "content" as described in the below table, messages are created and delivered to suit for each push type. 

|Reserved Word|	Platform|	Usage|	GCM|	APNS|	TENCENT| ADM |
|---|---|---|---|---|---|---|
|title|	Android, <br/> iOS Watch, <br/> Tencent, <br/> ADM|	Optional, String|	data.title|	aps.alert.title|	title| data.title |
|body|	Android, <br/> iOS, <br/> Tencent, <br/> ADM|	Optional, String|	data.body|	aps.alert.body|	content| data.body|
|title-loc-key|	iOS|	Optional, String| - | aps.alert.title-loc-key| - | - |
|title-loc-args|	iOS|	Optional, Array of Strings| - | aps.alert.title-loc-args	| - | - |
|action-loc-key|	iOS|	Optional, String| - |aps.alert.action-loc-key	| - | - |
|loc-key|	iOS|	Optional, String| - |aps.alert.loc-key	| - | - |
|loc-args|	iOS|	Optional, Array of String	| - | aps.alert.loc-args	| - | - |
|launch-image|	iOS|	Optional, String	| - | aps.alert.launch-image	| - | - |
|badge|	iOS|	Optional, Number| - | aps.badge	| - | - |
|sound|	Android, <br/> iOS, <br/> Tencent, <br/> ADM|	Optional, String|	data.sound|	aps.sound|	custom_content.sound| data.sound |
|content-available|	iOS|	Optional, String	| - | aps.content-available	| - | - |
|category|	iOS|	Optional, String	| - | aps.category	| - | - |
|mutable-content| iOS | Optional, String | - | aps.mutable-content | - | - |
|consolidationKey| ADM | Optional, String | - | - | - | consolidationKey |
|expiresAfter| ADM | Optional, Number | - | - | - | expiresAfter |
|messageDeliveryReceipt| Android, <br/>iOS, <br/> Tencent | Unnecessary | - | - | - | - |
|messageDeliveryReceiptData| Android, <br/>iOS, <br/> Tencent | Unnecessary | - | - | - | - |

Reserved words are set at the right location for each platform when a message is created. User cannot change data type or location on his own. 
Other user-defined words are included to Custom Key/Value as follows: 

|Word|	Platform|	Usage|	GCM|	APNS|	TENCENT| ADM|
|---|---|---|---|---|---|---|
|customKey|	Android, <br/> iOS, <br/> Tencent|	Optional, <br/> Object, <br/> Array, <br/> String, <br/> Number|	data.customKey|	customKey|	custom_content.customKey| data.customKey|

### Example of Sending Messages  

- content.default must be included at the request body of Send Messages API. 

#### 1. Send to All 
The example shows how to send messages to all registered targets. 

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body"
        }
    },
    "messageType" : "NOTIFICATION"
}
```
##### Description
- If the target.type is 'ALL', messages are sent to all tokens. 

#### 2. Send to Particular Users 
The example shows how to send messages to particular users by entering user IDs. 

##### Request Body
```json
{
    "target" : {
        "type" : "UID",
        "to": ["uid-01", "uid-02"]
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body"
        }
    },
    "messageType" : "NOTIFICATION"
}
```
##### Description
- Set 'UID' for target.type and user ID for target.to, and send messages to such particular users. 

#### 3. Send to Users of Particular Nation or Push Type 
The example shows how to send messages to users of particular nation or device (e.g. Android or iOS) only. 

##### Request Body
```json
{
    "target" : {
        "type" : "ALL",
        "countries": ["KR", "JP"],
        "pushTypes": ["GCM", "APNS"]
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body"
        }
    },
    "messageType" : "NOTIFICATION"
}
```
##### Description
- Set country code for target.countries, and push type for target.pushTypes, and send messages to users who satisfy such conditions. 

#### 4. Convert Messages for Each Push Type 
The example describes the conversion rule which is applied for each push type of messages to send. 

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body",
            "badge": 1,
            "customKey": "value"
        }
    },
    "messageType" : "NOTIFICATION"
}
```

##### On GCM (Android) 
```json
{
	"data": {
		"title": "title",
		"body": "body",
		"customKey": "value"
	}
}
```
##### On APNS (iOS)
```json
{
    "aps": {
        "alert": {
            "title": "title",
            "body": "body"
        },
        "badge": 1
    },
    "customKey": "value"

}
```
##### On TENCENT (Android)
```json
 {
    "title": "title",
    "body": "body",
    "custom_content": {
        "customKey": "value"
    }
}
```

##### On ADM (Fire OS)
```json
{
	"data": {
		"title": "title",
		"body": "body",
		"customKey": "value"
	}
}
```

##### Description
- Messages for content are converted and sent for each push type. 
- Reserved words, such as title and body, are set for specified locations of delivery, when they are converted to each push type messages. 
Other user-defined fields are set where custom key is located of each push type. 
- Reserved words, such as badge and consolidationKey, which are specified for particular push types only, are excluded from other push types. 
For instance, badge is set for APNS (iOS) messages, while GCM, TENCENT, and ADM are excluded. 

#### 5. Ad Messages 
The example regards to the ad phrase which is added to a message, for the delivery of ad messages. 

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title": "Special Friday Event",
            "body": "Order Now at 50% Discounts!"
        }
    },
    "messageType" : "AD",
    "contact": "1588",
    "removeGuide": "Menu > Notification Setting"
}
```

##### On GCM (Android), ko (Korean)
```json
 {
    "data": {
        "title": "(광고) 금요일 특별 이벤트 1588",
        "body": "지금 주문하시면 50% 할안된 가격으로!\n메뉴 > 알림 설정"
    }
}
```
##### On APNS (iOS), ko (Korean)
```json
{
    "aps": {
        "alert": {
            "title": "(광고) 금요일 특별 이벤트 1588",
            "body": "지금 주문하시면 50% 할안된 가격으로!\n메뉴 > 알림 설정"
        }
    }
}
```
##### On GCM (Android), ja (Japanese)
```json
 {
    "data": {
        "title": "금요일 특별 이벤트",
        "body": "지금 주문하시면 50% 할안된 가격으로!"
    }
}
```
##### On APNS (iOS), ja (Japanese)
```json
{
    "aps": {
        "alert": {
            "title": "금요일 특별 이벤트",
            "body": "지금 주문하시면 50% 할안된 가격으로!"
        }
    }
}
```
##### Description
- To send ad messages, set AD for messageType, as well as contact and how to withdraw consent of receiving, each for contact and removeGuide. 
- When a message is delivered for each push time, title and body are added: ad phrase and contact for Title, and how to withdraw consent of receiving for Body. 
- Ad messages display Ad Phrase for those users whose language code is Korean (ko, ko-) only. Other language users (e.g. Japanese) shall not find ad phrases. 

#### 6. Multiple-Language Messages  
The example describes how to send messages in multiple languages. 

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title": "Title",
            "body": "Body",
            "customKey": "Value"
        },
        "ko" : {
            "title": "Title",
            "body": "Body",
            "customKey": "Set for language codes starting with'ko'or 'ko-'."
        },
        "ja" : {
            "title": "タイトル",
            "body": "プッシュ・メッセージ"
        }
    },
    "messageType" : "NOTIFICATION"
}
```

##### Received on GCM (Android), ko (Korean)
```json
{
    "data": {
        "title": "제목",
        "body": "내용",
        "customKey": "'ko', 'ko-'로 시작하는 언어 코드에 설정됩니다."
    }
}
```
##### Received on GCM (Android), ko-KR (Korean)
```json
{
    "data": {
        "title": "제목",
        "body": "내용",
        "customKey": "'ko', 'ko-'로 시작하는 언어 코드에 설정됩니다."
    }
}
```
##### Received on GCM (Android), ja (Japanese)
```json
{
    "data": {
        "title": "タイトル",
        "body": "プッシュ・メッセージ",
        "customKey": "value"
    }
}
```
##### Received on GCM (Android), en (English)
```json
{
    "data": {
        "title": "title",
        "body": "body",
        "customKey": "value"
    }
}
```
##### Description
- Enter message for each language code under Content, and it is converted to such message of a matching language code of token or similar language, and sent. 
If no match is available, default shall be delivered. For those users whose language code is en (English), content.default shall be delivered.   
- If not a perfect match for a token language code, the best closest language shall be selected at the comparison of language codes. Although the request body contains content.ko only, those users whose language code is Korean (ko-KR), content. ko shall be delivered.  
- Since customKey is not defined at content.ja, it shall be replaced by content.default. Common messages are available at content.default. 

#### 7. Rich Messages 
When the 'richMessage' field is defined at 'content', rich messages can be delivered. 
Such messages can be sent along with Common, Ad, or Multi-Language Messages.  
v1.7 or higher SDKs are required.  

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title" : "title",
            "body" : "body",
            "richMessage" : {
                "buttons" : [{
                        "name" : "Button Name",
                        "submitName": "Send Button Name",
                        "buttonType" : "REPLY | OPEN_APP | OPEN_URL | DISMISS",
                        "link" : "URL | ...",
                        "hint" : "Enter your message."
                    }
                ],
                "media" : {
                    "sourceType" : "REMOTE | LOCAL",
                    "source" : "URL | LOCAL_RESOURCE",
                    "mediaType" : "IMAGE | GIF | VEDIO | AUDIO",
                    "extension" : "jpg | png",
                    "expandable" : true
                },
                "largeIcon" : {
                    "sourceType" : "REMOTE | LOCAL",
                    "source" : "URL | LOCAL_RESOURCE"
                },
                "group" : {
                    "key" : "KEY",
                    "description" : "Group of Notifications"
                }
            }
        }
    },
    "messageType" : "NOTIFICATION"
}
```

| Field | Usage | Description |
| - | - | - |
| richMessage | Optional, Object | Required for rich messages |
| richMessage.buttons | Optional, Object Array | Buttons added for rich messages: up to 3. |
| richMessage.button.name | Required, String | Button name |
| richMessage.button.submitName | Optional, String | Send button name: shows when the button type on iOS is REPLY. |
| richMessage.button.buttonType | Required, String | Button type: REPLY, OPEN_APP, OPEN_URL, or DISMISS |
| richMessage.button.link | Required, String | Link connected at the press of the button |
| richMessage.button.hint | Required, String | Hint for a button |
| richMessage.media | Optional, Object | Media added for rich messages |
| richMessage.media.sourceType | Required, String | Media location: REMOTE or LOCAL |
| richMessage.media.source | Required, String | Address where media is located |
| richMessage.media.mediaType | Required, String | Media type: IMAGE, GIF, VEDIO, or AUDIO. Android supports IMAGE only. |
| richMessage.media.extension | Required, String | Extension of media file |
| richMessage.media.expandable | Required, Boolean | Click to expand media on Android |
| richMessage.largeIcon | Optional, Object | Large icons added for rich messages: only on Android. |
| richMessage.largeIcon.sourceType | Required, String | Location of large icons: REMOTE or LOCAL |
| richMessage.largeIcon.source | Required, String | Address where media is located |
| richMessage.group | Optional, Object | Bind many messages into a group: only on Android |
| richMessage.group.key | Required, String | Key of a group |
| richMessage.group | Required, String | Description of a group |

### Query
#### List
※ Only the push messages sent using the console can be retrieved by the List API.
##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/messages?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&deliveryType={deliveryType}&messageStatus={messageStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| pageIndex | Optional, Number | Default is 0 |
| pageSize | Optional, Number | Default is 25: max is 100 |
| from | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| deliveryType | Optional, String | 'INSTANT' (immediate delivery), 'RESERVATION' (scheduled delivery) |
| messageStatus | Optional, String | 'READY', 'PROCESSING', 'COMPLETE', 'CANCEL_NO_TARGET', 'CANCEL_INVALID_CERTIFICATE', 'CANCEL_INVALID_MESSAGE', 'CANCEL_UNSUPPORTED_MESSAGE_TYPE', 'CANCEL_UNAUTHORIZED', 'CANCEL_UNKNOWN' |

##### Request Body
```
N/A
```
##### Response Body
```json
{
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "SUCCESS"
    },
    "messages" : [{
        "messageId" : 0,
        "messageIdString": "0",
        "target" : {
        "type" : "ALL"
        },
        "content" : {
            "default" : {
                "title": "title",
                "body": "body"
            }
        },
        "messageType" : "AD",
        "contact": "1588-1588",
        "removeGuide": "Menu > Settings",
        "timeToLiveMinute": 60,
        "createdDateTime": "2017-02-13T09:30:00.000+09:00",
        "completedDateTime": "2017-02-13T09:30:00.000+09:00",
        "targetCount": 1000,
		"sentCount": 1000,
        "messageStatus": "COMPLETE",
        "provisionedResourceId": "[a-zA-Z0-9]{16}"
    }],
    "toatalCount": 1
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/messages
```

| Field | Usage | Description |
| - | - | - |
| createdDateTime | - | Date and time of message creation (ISO 8601) |
| completedDateTime | - | Date and time of message delivery completion (ISO 8601) |
| targetCount | - | Number of target tokens to send |
| sentCount | - | Number of tokens actually delivered |
| provisionedResourceId | - | Provisioned resource ID to send messages |
| totalCount | - | Total number of filtered messages |

- "messageStatus" shows the status of a message, as follows:  
    - READY: Request for message delivery has been registered. 
    - PROCESSING: Message is completely created, and ready or under delivery.  
    - COMPLETE: Message delivery is completed. 
    - CANCEL_NO_TARGET: Message delivery has been canceled as target is unavailable. Delivery may be canceled on the following accounts: 
    When there is no registered token;
 For ad push messages, when no user has agreed to receive ones; 
 For night-time ad push messages (21pm to 8am), when no user has agreed to receive ones;   
 When there is no available token, since previously-registered tokens are deleted.     
    - CANCEL_INVALID_CERTIFICATE: Canceled because certificate is invalid. Check certificate status. 
    - CANCEL_INVALID_MESSAGE: Canceled due to invalid message type. 
    - CANCEL_UNSUPPORTED_MESSAGE_TYPE: Canceled due to invalid message type. 
    - CANCEL_UNAUTHORIZED: Failed while authenticating certificate. Check certificate status. 
    - CANCEL_UNKNOWN: Error has occurred internally. 

#### Get 
※ Only the push messages sent using the console can be retrieved by the Get API.
##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/messages/{message-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| messageId | Required, Number | Message ID |

##### Request Body
```
N/A
```
##### Response Body
```json
{
    "message" : {
        "messageId" : 0,
        "messageIdString": "0",
        "target" : {
        "type" : "ALL"
        },
        "content" : {
            "default" : {
                "title": "title",
                "body": "body"
            }
        },
        "messageType" : "AD",
        "contact": "1588-1588",
        "removeGuide": "Menu > Settings",
        "timeToLiveMinute": 60,
        "createdDateTime": "2017-02-13T09:30:00.000+09:00",
        "completedDateTime": "2017-02-13T09:30:00.000+09:00",
        "targetCount": 1000,
        "messageStatus": "COMPLETE",
        "provisionedResourceId": "[a-zA-Z0-9]{16}"
    },
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "SUCCESS"
    }
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/messages/{messageId}
```

#### List Failed Messages 
List messages that are failed in delivery. 
However, if there is no token available (INVALID_TOKEN), it is not deemed as delivery failure. 

##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/message-errors?messageId={messageId}&messageErrorType={messageErrorType}&messagErrorCause={messageErrorCause}&from={from}&to={to}&limit={limit}
HEADER
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| messageId | Optional, Number | Message ID |
| messageErrorType | Optional, String | 'CLIENT_ERROR', 'EXTERNAL_ERROR', 'INTERNAL_ERROR' |
| messageErrorCause | Optional, String | 'UNSUPPORTED_MESSAGE_TYPE', 'INVALID_MESSAGE', 'INVALID_CERTIFICATE', 'UNAUTHORIZED', 'EXPIRED_TIME_OUT', 'APNS_ERROR', 'GCM_ERROR', 'TENCENT_ERROR', 'AGENT_ERROR', 'ADM_ERROR'  |
| from | Optional, DateTime String | Up to the latest 30 days, or before recent 7 days for default (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | Up to the latest 30 days, or now for default (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| limit | Optional, Number | Size to list at once: 100 for default and max |
| pageNumber | Optional, Number | Page number, default is 1 |

##### Description
- messageErrorType and messageErrorCause refer to the following: 
    - CLIENT_ERROR: Invalid request of client 
        - UNSUPPORTED_MESSAGE_TYPE: Unsupported type of messages 
        - INVALID_MESSAGE: Invalid messages
        - INVALID_CERTIFICATE: Expired or invalid certificate 
        - UNAUTHORIZED: Expired or invalid certificate 
    - EXTERNAL_ERROR: Error in external services connected with push, such as APNS, GCM, Tencent, or ADM 
        - APNS_ERROR: Delivery failed to APNS (iOS)
        - GCM_ERROR: Delivery failed to GCM (Google)
        - TENCENT_ERROR: Delivery failed to Tencent
        - ADM_ERROR: Delivery failed to ADM
    - INTERNAL_ERROR: Error occurred within push 
        - EXPIRED_TIME_OUT: Message validity expired due to delivery delays 
        - AGENT_ERROR: Delivery failed due to internal errors of agent  
- If header.resultCode is 40010 at the response body, reduce query period (from, to) and query again.

##### Request Body
```
N/A 
```

##### Response Body
```
{
	"messageErrors" : [{
			"messageId" : 0,
			"messageIdString" : "0",
			"pushType" : "GCM",
			"messageErrorType" : "ClientError",
			"messageErrorCause" : "INVALID_CERTIFICATE",
			"payload" : {
				"data" : {
					"title" : "title",
					"body" : "body"
				}
			},
			"createdDateTime" : "2017-05-18T15:47:00.000+09:00",
			"tokens" : [{
					"uid" : "uid-1",
					"token" : "token-1"
				}
			]
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "Success."
	}
}
```
```
{
    "messageErrors" : [{
        ...
    }],
	"header" : {
		"resultCode" :  40010,
		"resultMessage" :  "Client Error. It's too many. Please, change 'from' and 'to' shortly. totalCount<10>",
		"isSuccessful" :  false
	}
}
```
| Field | Usage | Description |
| - | - | - |
| messageId | - | ID of failed messages |
| messageIdString | - | ID of failed messages |
| pushType | - | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', or 'ADM' |
| payload | - | Message actually delivered on device |
| tokens | - | UID and token of recipients for failed delivery |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/message-errors
```

#### Query Statistics for Message Delivery Receipt 

**This API has been deprecated. Please use API v2.4 or higher.**

Enable Message Delivery Receipt and apply v1.4 or higher SDK, to confirm receipt of delivered messages. Such collected data can be queried by statistics API. To enable the feature, go to  [Console] > [Settings].   

##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/statistics/message-delivery-receipts?from={from}&to={to}&event={event}&timeUnit={timeUnit}&messageId={messageId}
HEADER
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| from | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| event | Optional, String | 'SENT', 'SENT_FAILED', 'RECEIVED', 'OPENED' |
| timeUnit | Optional, String | 'MINUTES', 'HOURS', 'DAYS'<br>If there is no available value, statistics are randomly provided depending on query period. <br>Displays by the day for 1 day or more; by the hour between 1 and 24 hours; and by the minute for less than 1 hour. |
| messageId | Optional, Number | Message ID |

##### Request Body
```
N/A 
```

##### Response Body

```
{
	"messageDeliveryReceiptStatistics" : [{
			"dateTime" : "2016-07-11 17:50:00.00+9:00",
			"sent" : 13,
			"sentFailed": 0,
			"received" : 12,
			"opened" : 10
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| dateTime | Optional, DateTime String | ISO 8601 |
| sent | Optional, Number | Number of deliveries from server |
| sentFailed | Optional, Number | Number of failed deliveries from server |
| received | Optional, Number | Number of receipts on device |
| opened | Optional, Number | Number of user-click opens on device |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/statistics/message-delivery-receipts
```

## Scheduled Messages  

### Create 
#### Create Delivery Schedule for Scheduled Messages 
##### Method, URL, Headers
```
POST /push/v2.1/appkeys/{appkey}/schedules
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |


##### Request Body

```json
{
	"type" : "EVERY_MONTH",
	"fromDate" : "2016-12-30",
	"toDate" : "2017-01-02",
	"times" : [
		"12:00",
		"17:00"
	],
	"days" : [
		1,
		15
    ],
 	"daysOfWeek": [
 		"SUNDAY",
 		"MONDAY"
  	]
}
```

| Field | Usage | Description |
| - | - | - |
| type | Required, String | 'EVERY_DAY' (everyday), 'EVERY_WEEK' (every week), 'EVERY_MONTH' (every month) |
| fromDate | Required, Date String | Start day, month, and year for scheduled message (YYYY-MM-DD) |
| toDate | Required, Date String | End day, month, and year for scheduled message (YYYY-MM-DD) |
| times | Required, Time String | Minute and hour of scheduled message delivery (hh:mm) |
| days | Optional, Number Array | To configure, if the type is 'EVERY_MONTH' (1, 2, ..., 31: 1 day, 2 days, ..., 31days) |
| daysOfWeek | Optional, String Array | To configure, if the type is 'EVERY_WEEK'. ('SUNDAY', 'MONDAY', 'TUESDAY', 'WEDNESDAY', 'THURSDAY', 'FRIDAY', 'SATURDAY') |

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"schedules" : [
		"2016-12-01T12:00",
		"2016-12-01T17:00",
		"2016-12-15T12:00",
		"2016-12-15T17:00",
		"2017-01-01T12:00",
		"2017-01-01T17:00",
		"2017-01-15T12:00",
		"2017-01-15T17:00",
		"2017-02-01T12:00",
		"2017-02-01T17:00",
		"2017-02-15T12:00",
		"2017-02-15T17:00"
	]
}
```

| Field | Usage | Description |
| - | - | - |
| schedules | - | Date and time (ISO 8601, e.g. YYYY-MM-DDThh:mm) |

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/schedules -d '{"type":"EVERY_MONTH","fromDate":"2016-12-30","toDate":"2017-01-02","times":["12:00","17:00"],"days":[1,15],"daysOfWeek":["SUNDAY","MONDAY"]}'
```

#### Create Scheduled Messages
##### Method, URL, Headers
```
POST /push/v2.1/appkeys/{appkey}/reservations
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |

##### Request Body
```json
{
	"schedules" : [
		"2016-12-30T12:40",
		"2016-12-31T12:40"
	],
	"isLocalTime" : false,
	"target" : {
        "type" : "UID",
        "to": ["uid-1", "uid-2"]
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body"
        }
    },
    "messageType" : "AD",
    "contact": "1588-1588",
    "removeGuide": "Menu > Settings",
    "timeToLiveMinute": 1,
	"provisionedResourceId": "id"
}
```

| Field | Usage | Description |
| - | - | - |
| schedules | Required, DateTime String Array | List of scheduled message delivery |
| isLocalTime | Required, Boolean | Delivery on local time |

##### Response Body

```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"reservation" : {
		"reservationId": 666810348995587,
		"reservationIdString": "666810348995587"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| reservationId | Number | ID of scheduled message |
| reservationIdString | String | ID string for scheduled message |

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/reservations -d '{"schedules":["2016-12-30T12:40","2016-12-31T12:40"],"isLocalTime":false,"target":{"type":"UID","to":["uid"]},"content":{"default":{"title":"title","body":"body"}},"messageType":"AD","contact":"1588-1588","removeGuide":"Menu > Settings","timeToLiveMinute":1}'
```

### Query
#### List 
##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/reservations?pageIndex={pageIndex}&pageSize={pageSize}&reservationStatus={reservationsStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use키 |
| pageIndex | Optional, Number | Default is 0 |
| pageSize | Optional, Number | Default is 25; max is 100 |
| from | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| reservationStatus | Optional, String | 'RESERVED', 'COMPLETE' |

##### Request Body
```
N/A
```

##### Response Body

```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"reservations" : [{
			"reservationId" : 666810348995587,
			"reservationIdString" : "666810348995587",
			"schedules" : [{
					"scheduleId" : 2455708,
                    "scheduleIdString" : "2455708",
                    "reservationId" : 666810348995587,
                    "reservationIdString" : "666810348995587",
					"deliveryDateTime" : "2016-12-30T12:40:00.000+09:00",
					"timezoneOffset" : 0,
					"scheduleStatus" : "READY"
				}
			],
			"isLocalTime" : false,
			"target" : {
				"type" : "UID",
				"to" : [
					"uid"
				]
			},
			"content" : {
				"default" : {
					"title" : "default title",
					"body" : "default body"
				},
				"ko" : {
					"title" : "Title in Korean",
					"body" : "Body in Korean"
				}
			},
			"messageType" : "NOTIFICATION",
			"timeToLiveMinute" : 60,
			"createdDateTime" : "2016-12-30T10:34:40.000+09:00",
			"updatedDateTime" : "2016-12-30T10:34:40.000+09:00",
			"completedDateTime" : "2016-12-30T10:34:40.000+09:00",
			"reservationStatus" : "RESERVED"
		}
	],
    "totalCount" : 1
}

```

| Field | Usage | Description |
| - | - | - |
| reservationIdString | - | ID string for scheduled message |
| createdDateTime | - | Date and time of scheduled message registration (ISO 8601) |
| updatedDateTime | - | Date and time of scheduled message modification (ISO 8601) |
| completedDateTime | - | Date and time of completion for scheduled message delivery; if not completed, display current time (ISO 8601) |
| reservationStatus | - | 'RESERVED', 'COMPLETED' |
| schedules.scheduleId | - | ID for scheduled message delivery |
| schedules.scheduleIdString | - | ID string for scheduled message delivery |
| schedules.reservationIdString | - | ID string to which scheduled message delivery belongs to |
| schedules.deliveryDateTime | - | Date and time of scheduled message delivery |
| schedules.timezoneOffset | - | Timezone for scheduled message delivery: set for delivery on local time |
| schedules.scheduleStatus | - | Status of scheduled message delivery: 'READY', 'SENDING', 'CANCELED', or 'DONE' |
| totalCount | - | Total number of scheduled messages that are registered |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/reservations
```

#### Get 
##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/reservations/{reservation-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

##### Request Body
```
N/A
```

#### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"reservation" : {
		"reservationId" : 666810348995587,
		"reservationIdString" : "666810348995587",
		"schedules" : [{
                "scheduleId" : 2455708,
                "scheduleIdString" : "2455708",
                "reservationId" : 666810348995587,
                "reservationIdString" : "666810348995587",
				"deliveryDateTime" : "2016-12-30T12:40:00.000+09:00",
				"timezoneOffset" : 0,
				"scheduleStatus" : "READY"
			}
		],
		"isLocalTime" : false,
		"target" : {
			"type" : "UID",
			"to" : [
				"uid"
			]
		},
		"content" : {
			"default" : {
				"title" : "default title",
				"body" : "default body"
			},
			"ko" : {
				"title" : "Title in Korean",
				"body" : "Body in Korean"
			}
		},
		"messageType" : "NOTIFICATION",
		"timeToLiveMinute" : 60,
		"createdDateTime" : "2016-12-30T10:34:40.000+09:00",
		"updatedDateTime" : "2016-12-30T10:34:40.000+09:00",
		"completedDateTime" : "2016-12-30T10:34:40.000+09:00",
		"reservationStatus" : "RESERVED"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| updatedDateTime | DateTime String | Date and time of schedule modification (ISO 8601) |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/reservations/{reservationId}
```

#### Query Scheduled Messages  
##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/reservations/{reservation-id}/messages?pageIndex={pageIndex}&pageSize={pageSize}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| reservationId | Required, Number | ID of scheduled message |
| pageIndex | Optional, Number | Default is 0 |
| pageSize | Optional, Number | Default is 25; max is 100 |

##### Request Body
```
N/A
```

##### Response Body
```
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "success",
		"isSuccessful" : true
	},
	"messages" : [{
			"messageId" : 356125922591162,
			"messageIdString" : "356125922591162",
			"target" : {
				"type" : "ALL",
				"pushTypes" : ["GCM", "APNS", "APNS_SANDBOX", "TENCENT", "ADM"]
			},
			"content" : {
				"default" : {
					"title" : "Message scheduled at 6:55",
					"body" : "API v2"
				}
			},
			"messageType" : "NOTIFICATION",
			"createdDateTime" : "2017-04-05T18:55:00.000+09:00",
			"completedDateTime" : "2017-04-05T18:55:00.000+09:00",
			"targetCount" : 38,
			"sentCount" : 38,
			"messageStatus" : "COMPLETE"
		}
	],
	"totalCount" : 1
}
```

| Field | Usage | Description |
| - | - | - |
| totalCount | - | Total number of delivered messages |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/reservations/{reservationId}/messages
```

### Modify 
#### Modify Scheduled Messages 
##### Method, URL, Headers
```
PUT /push/v2.1/appkeys/{appkey}/reservations/{reservationId}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

##### Request Body
```json
{
	"schedules" : [
		"2016-12-30T10:05",
		"2016-12-31T12:40"
	],
	"target" : {
		"type" : "UID",
		"to" : [
			"uid"
		]
	},
	"content" : {
		"default" : {
			"title" : "default title",
			"body" : "default body"
		},
		"ko" : {
			"title" : "Title in Korean",
			"body" : "Body in Korean"
		}
	},
	"isLocalTime" : false,
	"messageType" : "NOTIFICATION"
}
```

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	}
}
```

##### Example
```
curl -X PUT -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/reservations/{reservationId} -d '{"schedules":["2018-12-30T12:40","2018-12-31T12:40"],"isLocalTime":false,"target":{"type":"UID","to":["uid"]},"content":{"default":{"title":"title","body":"body"}},"messageType":"AD","contact":"1588-1588","removeGuide":"Menu > Settings","timeToLiveMinute":1}'
```

### Delete 
#### Delete Scheduled Messages
##### Method, URL, Headers
```
DELETE /push/v2.1/appkeys/{appkey}/reservations?reservationIds={reservationId,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| reservationIds | Required, Number Array | Delimited by ',', e.g. reservationIds=1,2 |

##### Request Body
```
N/A
```

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	}
}
```

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/reservations?reservationIds={reservationId,}
```

## Tags

### Create
#### Create Tags
##### Method, URL, Headers
```
POST /push/v2.1/appkeys/{appkey}/tags
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body

```json
{
    "tagName" :  "Thirty"
}
```

| Field | Usage | Description |
| - | - | - |
| tagName | Required, String | Tag name, no longer than 255, no white space allowed |

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "tag" : {
        "tagId" :  "12345678"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| tagId | Required, String | Created tag ID, length is 8 |

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/tags -d '{"tagName":"Thirty"}'
```

#### Append UIDs to Tag 
- Append UID to a tag: adding existing UIDs ends up with more tags of UID 
- The maximum number of tags for a UID is 16. 
##### Method, URL, Headers
```
POST /push/v2.1/appkeys/{appkey}/tags/{tag-id}/uids
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```json
{
    "uids" : [
         "uid-01",
         "uid-02"
    ]
}
```
| Field | Usage | Description |
| - | - | - |
| uids | Required, String Array | UID array, no longer than 16: UID cannot be longer than 64 |

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/tags/{tagId}/uids -d '{"uids":["uid"]}'
```

#### Set Tag List on Uid
- By replacing tags of UID, existing tags are deleted and replaced by new tags.  
##### Method, URL, Headers
```
POST /push/v2.1/appkeys/{appkey}/uids
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```json
{
    "uid" :  "uid-01",
    "tagIds" : [
         "12345678",
         "23456789"
    ]
}
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/uids -d '{"uid":"uid","tagIds":["TAG_ID"]}'
```

### Query 
#### List Tags
##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/tags?tagName={tagName}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| tagName | Optional, String | Tag name |

##### Request Body
```
N/A
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "tags" : [
        {
            "tagId" :  "12345678",
            "tagName" :  "tagName",
            "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
            "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
        }
    ]
}
```

| Field | Usage | Description |
| - | - | - |
| createdDateTime | Required, Date Time String | Date and time of creation (ISO 8601) |
| updatedDateTime | Required, Date Time String | Date and time of modification (ISO 8601) |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/tags
```

#### Get Tags
##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
N/A
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "tag" : {
        "tagId" :  "12345678",
        "tagName" :  "Thirty",
        "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
        "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
    }
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/tags/{tagId}
```

#### List UIDs of Tag
- List tagged UIDs. 

##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/tags/{tag-id}/uids?offsetUid={uid}&limit={limit}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| offsetUid | Optional, String | List following configured UID |
| limit | Optional, Number | Number of UIDs to list |

##### Request Body
```
N/A
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "uids" : [
        {
            "uid" :  "uid-01",
            "tags" : [
                {
                    "tagId" :  "tag-id-01",
                    "tagName" :  "tag-name-01",
                    "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
                    "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
                }
            ],
            "contacts" : [
                {   
                    "contactType" :  "TOKEN_GCM",
                    "contact" :  "token",
                    "createdDateTime" :  "2017-07-07T07:07:07.777+09:00"
                }
            ]
        }
    ]
}
```

| Field | Usage | Description |
| - | - | - |
| contacts | -, Object Array | Contact of UID, list of token information |
| contactType | -, String | Token Type: 'TOKEN_GCM', 'TOKEN_APNS', 'TOKEN_APNS_SANDBOX', 'TOKEN_TENCENT', 'TOKEN_ADM' |
| contact | -, String | Token |
| createdDateTime | Required, Date Time String | Date and time of creation (ISO 8601) |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/tags/{tagId}/uids
```

#### Query UIDs
- Query UID.
- Token is registered, along with contact. 
##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/uids/{uid}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
N/A
```

##### Response Body
```json
{
"header" : {
"isSuccessful" :  true,
"resultCode" :  0,
"resultMessage" :  "SUCCESS"
},
"uid" : {
    "uid" :  "uid-01",
        "tags" : [
            {
                "tagId" :  "12345678",
                "tagName" :  "tag-name-01",
                "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
                "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
            }
            ],
            "contacts" : [
            {
                "contactType" :  "TOKEN_GCM",
                "contact" :  "token",
                "createdDateTime" :  "2017-07-07T07:07:07.777+09:00"
            }
        ]
    }
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/uids/uid
```

### Modify
#### Modify Tags 
##### Method, URL, Headers
```
PUT /push/v2.1/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```json
{
    "tagName" :  "30s"
}
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X PUT -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/tags/{tagId} -d '{"tagName":"Thirty three"}'
```

### Delete
#### Delete Tags
##### Method, URL, Headers
```
DELETE /push/v2.1/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
N/A
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/tags/{tagId}
```

#### Delete UIds
- UIDs are deleted, along with contact and token. 
##### Method, URL, Headers
```
DELETE /push/v2.1/appkeys/{appkey}/uids?uids={uid,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| uids | -, Object Array | List of UIDs to delete, delimited by comma (,). Up to 16 can be deleted at once. |

##### Request Body
```
N/A
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/uids?uids=uid
```

#### Delete UIDs of Tag
- Delete tag-UID relation only. 
- Contact or token is not deleted. 
##### Method, URL, Headers
```
DELETE /push/v2.1/appkeys/{appkey}/tags/{tagId}/uids?uids={uid,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
N/A
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/tags/{tagId}/uids?uids=uid
```

## UIDs

### Create

#### Add Tags
- Add tags to UID with tag ID. 
- No secret key is required: call is available from app. 
##### Method, URL, Headers
```
POST /push/v2.1/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```json
{
    "tagIds": ["TAG_ID01"]
}
```
##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/uids/uid/tag-ids -d '{"tagIds":["TAG_ID"]}'
```

### Query

#### Query Tag IDs of UID
- Query tag IDs of UID. 
- No secret key is required: call is available from app. 
##### Method, URL, Headers
```
GET /push/v2.1/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```
N/A
```
##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "tagIds": ["TAG_ID01"]
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/uids/uid/tag-ids
```

### Modify
#### Modify Tags of UID 
- Modify tags of UID with tag ID. 
- No secret key is required: call is available from app. 
##### Method, URL, Headers
```
PUT /push/v2.1/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```json
{
    "tagIds": ["TAG_ID02"]
}
```
##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X PUT -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/uids/uid/tag-ids -d '{"tagIds":["TAG_ID"]}'
```

### Delete Tags
- Query tag IDs of UID. 
- No secret key is required: call is available from app. 
##### Method, URL, Headers
```
DELETE /push/v2.1/appkeys/{appkey}/uids/{uid}/tag-ids?tagIds={tagId,}
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```
N/A
```
##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| tagIds | Required, String Array | Query String, tag ID to delete, delimited by comma (,) |

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.1/appkeys/{appkey}/uids/uid/tag-ids?tagIds=TAG_ID_01,TAG_ID_02
```


* *Document Updates* 
   * *(Dec. 26, 2018) Added field to set ad-display location, Corrected spell-errors*
    * *(June, 26, 2018) Added example of sending messages*
    * *(June 26, 2018) Added pushType ADM*
    * *(May 29, 2018) Added v2.1 Query Token API*
