## Notification > Push > API Guide

### Overview of v2.0 API

#### Add

- Respond with detailed resultMessage. If API call fails, return faulty fields or values.
  - e.g. When retrieved with a wrong message ID, include the messageId field and value to resultMessage as follows:

```
{
    "header" : {
		"resultCode" : 40401,
		"resultMessage" : "Client Error. Not found. messageId<3496615188236841>",
		"isSuccessful" : false
    }
}
```
- Added v2.0 Register ToKen API and Property Statistics API.
- v2.0 Get Message API can bring data by period (from, to) and message status (messageStatus).
  Follow the ISO 8601 format (YYYY-MM-DDThh:mm:ss.SSSTZD) for 'from, to'.
  For (+) of '2018-04-11T18:39:04.000+09:00', enter '2018-04-11T18:39:04.000%2B09:00' after URL is encoded.
- createdDateTime and completedDateTime fields added to v2.0 Get Message API.
- v2.0 Get Message Delivery/Receipt Stats API added.  
- v2.0 Invalid Token API can get by PageIndex, PageSize, period (from, to), and messageIDs.

#### Modify
- The URI of v1.3 Feedback API has changed from '/push/v1.3/appkey/{appkey}/feedback' to '/push/v2.0/appkeys/{appkey}/invalid-tokens'.

#### Delete
- Messages delivered by v2.0 Send Message API are not traced down in history, while those sent by console are recorded in history.

  If integration with ‘Log&Crash Search’ is added as scheduled, message delivery can be recorded at a user’s ‘Log&Crash Search’.
- Channel has been deleted from v2.0 API. Channel served as a grouper of tokens: one token was restricted to be included to only one channel.  
  Channel is to be replaced with Tag as of June 2017. One token can have many tags.
    - Register Token API, and Channel field deleted
    - 'Channel' type from target.type of Send Message API deleted
    - Channel API deleted

### Basic Information
#### Endpoint
```
API Endpoint: https://api-push.cloud.toast.com
Collect Receipt/Check Messages Endpoint: https://collector-push.cloud.toast.com
```

#### Secret Key
- Can check at the console.
- Need to configure the Header as below to call API requiring a secret key.
```
Header
X-Secret-Key: [a-zA-Z0-9]{8}
```

Go to console and click **Notification > Push**, then click **URL & AppKey** at the top right to create.

#### Response

##### Response HTTP Status Code
200 OK.
Respond with 200 OK to all API requests.
Can find detailed response results at the Header of Response Body.

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

| isSuccessful | resultCode    | resultMessage                            |
| ------------ | ------------- | ---------------------------------------- |
| true         | 0             | SUCCESS                                  |
| false        | 40001         | Client Error. Parameter is invalid.      |
| false        | 40002         | Client Error. Parameter is invalid format. |
| false        | 40003         | Client Error. Parameter is empty or null. |
| false        | 40004         | Client Error. Duplicate certificate.     |
| false        | 40005         | Client Error. Expired certificate.       |
| false        | 40006         | Client Error. Already registered.        |
| false        | 40007         | Client Error. Maximum limit exceeded.    |
| false        | 40008         | Client Error. Already completed.         |
| false        | 40101         | Client Error. Access is not allowed.     |
| false        | 40102         | Client Error. Unavailable key.           |
| false        | 40401         | Client Error. Not found.                 |
| false        | 50001 ~ 50501 | Internal Error. Please report this. 'http://cloud.toast.com/support/faq'. |
| false        | 400           | Client Error. Error of Client occurred at a tag API. |
| false        | 500           | Internal Error. Internal error occurred at a tag API. |

## Tokens
### Create
- Can get by client.

##### Method, URL
```
POST /push/v2.0/appkeys/{appkey}/tokens
Content-Type: application/json;charset=UTF-8
```

| Field  | Usage            | Description                            |
| ------ | ---------------- | -------------------------------------- |
| appkey | Required, String | Path Variable, Appkey issued on Enable |

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

| Field                   | Usage             | Description                                                  |
| ----------------------- | ----------------- | ------------------------------------------------------------ |
| token                   | Required, String  | Token, the maximum length is 1,600                               |
| oldToken                | Optional, String  | Old token, the maximum length is 1,600                            |
| pushType                | Required, String  | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', and 'ADM' |
| isNotificationAgreement | Required, Boolean | True or false                                                |
| isAdAgreement           | Required, Boolean | True or false                                                |
| isNightAdAgreement      | Required, Boolean | True or false                                                |
| timezoneId              | Required, String  | Area/Name. IANA time zone database.                          |
| country                 | Required, String  | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3 bytes              |
| language                | Required, String  | ISO 639-1, ISO 639-2, iOS (language code + script code), 8 bytes |
| uid                     | Required, String  | User ID, no emojis allowed, no more than 64 bytes            |
| deviceId                | Optional, String  | Device ID, 36 bytes                                          |


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
curl -X POST -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tokens -d '{"oldToken":"oldToken","token":"token","isNotificationAgreement":true,"isAdAgreement":true,"isNightAdAgreement":true,"pushType":"GCM","timezoneId":"Asia/Seoul","uid":"uid","country":"KR","language":"ko"}'
```

##### Description

- In case a token is to be registered when there is an old token already registered, information is updated to a new token.
- As such, when an old token is replaced by a new token, set the old token for oldToken and set/register a new one for Token; it is to be updated to a new token.
- "isNotificationAgreement” means to agree receiving push messages; "isAdAgreement"for ad push messages; and "isNightAdAgreement”for night-time ad push messages.
- For instance, when you agree to receive all push messages, set all three fields as true. Or, if you decide to receive push messages only, set the "isNotificationAgreement” field only as true.
- Consent to receiving messages abides by the Act on Promotion of Information and Communications Network set by the government of Korea (from article 50-1 to 50-8).
    - [Go to KISA for guidance](https://spam.kisa.or.kr/spam/na/ntt/selectNttInfo.do?mi=1020&nttSn=1171&bbsId=1002)
    - [Go to relevant legislation](http://www.law.go.kr/lsEfInfoP.do?lsiSeq=123210#)
- Delays in response may occur due to many reasons, such as bad network connection. To minimize effect on operation of mobile applications, it is recommended to set timeout short and register a token every time the application is operated.
- A token may be re-issued on many accounts, such as security issues, updates or deletion of applications. It is, therefore, recommended to register updated token at every operation, in order to upgrade the receiving rates.
- Even when a token expires due to application deletion, it is not immediately applied to GCM or APNS servers. So push messages could be successfully delivered even after an app has been deleted.  

### Get
#### Get by Token/Push Type
- Can get by Client
##### Method, URL

```
GET /push/v2.0/appkeys/{appkey}/tokens/{token}?pushType={pushType}
Content-Type: application/json;charset=UTF-8
```

| Field    | Usage            | Description                                                  |
| -------- | ---------------- | ------------------------------------------------------------ |
| appkey   | Required, String | Path Variable, Appkey issued on Enable                       |
| pushType | Required, String | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', and 'ADM' |

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
    "nightAdAgreementDateTime": "2017-08-12T01:04:19.000+09:00"        
	},
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field                    | Usage              | Description                                               |
| ------------------------ | ------------------ | --------------------------------------------------------- |
| updateDateTime           | -, DateTime String | Date/time of a token update                               |
| adAgreementDateTime      | -, DateTime String | Date/time agreeing to receive ad push messages            |
| nightAdAgreementDateTime | -, DateTime String | Date/time agreeing to receive night-time ad push messages |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tokens/token?pushType=GCM
```

#### Get by User ID
- This API requires a secret key and should be called from a server.
##### Method, URL

```
GET /push/v2.0/appkeys/{appkey}/tokens?uid={uid}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field  | Usage            | Description                            |
| ------ | ---------------- | -------------------------------------- |
| appkey | Required, String | Path Variable, Appkey issued on Enable |
| uid    | Required, String | User ID to get                         |

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
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tokens?uid=uid
```

#### Get Invalid Tokens
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/invalid-tokens?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&messageId={messageId}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field     | Usage                     | Description                                                  |
| --------- | ------------------------- | ------------------------------------------------------------ |
| appkey    | Required, String          | Path Variable, Appkey issued on Enable                       |
| pageIndex | Optional, Number          | Default is 0                                                 |
| pageSize  | Optional, Number          | Default is 25; Max 100                                       |
| from      | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to        | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| messageId | Optional, Number          | Message ID with invalid token                                |

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
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/invalid-tokens
```


#### Get Token Property Statistics API

**This API has been deprecated. Please use API v2.4 or higher.**

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/statistics/token-properties?from={from}&to={to}&tokenProperties={tokenProperties}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field           | Usage                     | Description                                                  |
| --------------- | ------------------------- | ------------------------------------------------------------ |
| appkey          | Required, String          | Path Variable, Appkey issued on Enable                       |
| from            | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to              | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| tokenProperties | Optional, String Array    | 'agreement', 'country', 'language', 'timezoneId'<br />Delimited by ',',  e.g. tokenProperties=country,language |

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

| Field          | Usage  | Description                                                  |
| -------------- | ------ | ------------------------------------------------------------ |
| dateTime       | String | Time of data collection                                      |
| agreements     | String | 'ON'(receive all), 'NIGHT_AD_OFF' (reject night-time ad messages), 'AD_OFF' (reject ad messages), 'OFF' (reject all) |
| countries.XX   | String | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3 bytes              |
| languages.XX   | String | ISO 639-1, ISO 639-2, iOS(language code + script code), 8 bytes |
| timezoneIds.XX | String | Area/Name. IANA time zone database                           |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/statistics/token-properties
```

#### Get Register Token Statistics

**This API has been deprecated. Please use API v2.4 or higher.**

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/statistics/token-registrations?from={from}&to={to}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field  | Usage                     | Description                              |
| ------ | ------------------------- | ---------------------------------------- |
| appkey | Required, String          | Path Variable, Appkey issued on Enable   |
| from   | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to     | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |

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

| Field           | Usage  | Description                      |
| --------------- | ------ | -------------------------------- |
| dateTime        | String | Date and time of data collection |
| registered | Number | Number of registered tokens      |
| deleted    | Number | Number of deleted tokens         |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/statistics/token-registrations
```

## Messages
### Send
※ Push messages sent by API cannot be retrieved in the console or by the Get or List API.
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/messages
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
    "removeGuide": "menu > setting",
    "timeToLiveMinute": 1,
	"provisionedResourceId": "id"
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

| Field                 | Usage                  | Description                                                  |
| --------------------- | ---------------------- | ------------------------------------------------------------ |
| appkey                | Required, String       | Path Variable, Appkey issued on Enable                       |
| target.type           | Required, String       | Type of receiving targets, such as 'ALL', 'UID', and 'TAG'   |
| target.to             | Optional, String Array | target.type is the UID list of recipients (no more than 10,000) or tag condition |
| target.pushTypes      | Optional, String Array | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', and 'ADM' |
| target.countries      | Optional, String Array | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3 (up to 3 bytes)       |
| content               | Required, Map          | Messages to deliver to recipient (up to 8,192 bytes)         |
| content.default       | Required, Map          | For details, refer to ‘common message type’ below            |
| content.default.title | Optional, String       |                                                              |
| content.default.body  | Optional, String       |                                                              |
| messageType           | Required, String       | NOTIFICATION, AD                                             |
| contact               | Optional, String       | Required if the messageType is AD. Only numbers (0-9) and hyphens are available. |
| removeGuide           | Optional, String       | Required if the messageType is AD.                           |
| timeToLiveMinute      | Optional, Number       | The unit is minute, ranged from 1 to 60. Default is 10.      |
| provisionedResourceId | Optional, String       | Refers to a provisioned resource ID. Contact: support@cloud.toast.com |

##### Description
- When the target.type is set with UID, up to 10,000 UIDs can be configured to ‘target.to’.
- When the target.type is set with TAG, the condition can be configured with a tag ID, three conditions and 1 parenthesis “(())”.
    - e.g. When delivering messages to those tagged with a man in 30s and a woman, the “target.to” can be configured man_ID, AND, 30s-ID, OR woman_ID.
- Using the “target.pushTypes” field, messages can be delivered in specific push types only; or delivered in all push types, such as GCM, APNS, APNS_SANDBOX, and TENCENT, if not defined.
- If “target.countries” is "['KR', 'JP']", messages are sent to those with “KR” or “JP” token country codes.
    The “content.default” field is a must, and for more details on the “content” field, refer to [Common Message Format] below.
- To send a message with “AD” for its “messageType”, you should include “contact” and “removeGuide”. Contact number is required at the “contact” field; and, how to undone subscription is required at “removeGuide”.
- With the timeToLiveMinute field, messages that are delayed in delivery beyond TTL is automatically deemed as a failure.

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/messages -d '{"target":{"type":"UID","to":["uid"]},"content":{"default":{"title":"title","body":"body","customKey1":"It is default"},"ko":{"title":"제목","body":"내용","customKey2":"한국어 입니다."}},"messageType":"AD","contact":"1588-1588","removeGuide":"매뉴 > 설정","timeToLiveMinute":1}'
```

### Common Messages
When “content” has messages written as below, messages are created for each push type before delivered.

| Reserved Word              | Platform                                 | Usage                      | GCM        | APNS                     | TENCENT              | ADM              |
| -------------------------- | ---------------------------------------- | -------------------------- | ---------- | ------------------------ | -------------------- | ---------------- |
| title                      | Android, <br/> iOS Watch, <br/> Tencent, <br/> ADM | Optional, String           | data.title | aps.alert.title          | title                | data.title       |
| body                       | Android, <br/> iOS, <br/> Tencent, <br/> ADM | Optional, String           | data.body  | aps.alert.body           | content              | data.body        |
| title-loc-key              | iOS                                      | Optional, String           | -          | aps.alert.title-loc-key  | -                    | -                |
| title-loc-args             | iOS                                      | Optional, Array of Strings | -          | aps.alert.title-loc-args | -                    | -                |
| action-loc-key             | iOS                                      | Optional, String           | -          | aps.alert.action-loc-key | -                    | -                |
| loc-key                    | iOS                                      | Optional, String           | -          | aps.alert.loc-key        | -                    | -                |
| loc-args                   | iOS                                      | Optional, Array of String  | -          | aps.alert.loc-args       | -                    | -                |
| launch-image               | iOS                                      | Optional, String           | -          | aps.alert.launch-image   | -                    | -                |
| badge                      | iOS                                      | Optional, Number           | -          | aps.badge                | -                    | -                |
| sound                      | Android, <br/> iOS, <br/> Tencent, <br/> ADM | Optional, String           | data.sound | aps.sound                | custom_content.sound | data.sound       |
| content-available          | iOS                                      | Optional, String           | -          | aps.content-available    | -                    | -                |
| category                   | iOS                                      | Optional, String           | -          | aps.category             | -                    | -                |
| mutable-content            | iOS                                      | Optional, String           | -          | aps.mutable-content      | -                    | -                |
| consolidationKey           | ADM                                      | Optional, String           | -          | -                        | -                    | consolidationKey |
| expiresAfter               | ADM                                      | Optional, Number           | -          | -                        | -                    | expiresAfter     |
| messageDeliveryReceipt     | Android, <br/>iOS, <br/> Tencent         | Unnecessary                | -          | -                        | -                    | -                |
| messageDeliveryReceiptData | Android, <br/>iOS, <br/> Tencent         | Unnecessary                | -          | -                        | -                    | -                |

Reserved Word, when a message is created, is set at appropriate locations for each platform. User cannot change data type or locations as they want. User-defined words can be specified in the Custom Key/Value field as below:

| Word      | Platform                          | Usage                                    | GCM            | APNS      | TENCENT                  | ADM            |
| --------- | --------------------------------- | ---------------------------------------- | -------------- | --------- | ------------------------ | -------------- |
| customKey | Android, <br/> iOS, <br/> Tencent | Optional, <br/> Object, <br/> Array, <br/> String, <br/> Number | data.customKey | customKey | custom_content.customKey | data.customKey |

### Example of Message Delivery

- content.default at the request body of Send Message API is a must.

#### 1. Send to All
Below is an example of sending messages to all registered targets.

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
- If target.type is set 'ALL', messages are sent to all tokens.

#### 2. Send to Specific Users
 Below is an example of sending messages to specific users based on their IDs.

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
- Set UID for target.type and User ID for target.to, and send messages to particular users.

#### 3. Send to Particular Countries or Push-type Users
.Below is an example of sending messages to users of particular countries or devices (like Android or iOS).

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
- Set country code for target.countries and push type for target.pushTypes, and send messages to users who meet conditions.  

#### 4. Convert Messages per Push Type
Below is an example of conversion rule, applied for each push type for sending messages:

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

##### Messages Received at GCM (Android)
```json
{
	"data": {
		"title": "title",
		"body": "body",
		"customKey": "value"
	}
}
```
##### Messages Received at APNS (iOS)
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
##### Messages Received at TENCENT (Android)
```json
 {
    "title": "title",
    "body": "body",
    "custom_content": {
        "customKey": "value"
    }
}
```

##### Messages Received at ADM (Fire OS)
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
- Messages entered at Content are converted for each push type before delivered.
- Reserved words, such as title or body, are set at specified locations when converted to messages for each push type before they're delivered.
  Other user-defined fields are set at Custom Key locations of each push type.
- Reserved words defined at particular push types only, such as badge or consolidationKey, are excluded from other push types.
  For instance, badge is set for APNS (iOS) messages only, but not for GCM, TENCENT or ADM.

#### 5.  Advertising Messages
Below is an example of advertising words to be added for ad messages.

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title": "Special Event for Friday",
            "body": "Now order at 50% off the price!"
        }
    },
    "messageType" : "AD",
    "contact": "1588",
    "removeGuide": "Menu > Set Notifications"
}
```

##### Messages Received for ko (Korean) at GCM(Android)
```json
 {
    "data": {
        "title": "(AD) Special Event for Friday 1588",
        "body": "Now order at 50% off the price!\n Menu > Set notifications"
    }
}
```
##### Messages Received for ko (Korean) at APNS(iOS)
```json
{
    "aps": {
        "alert": {
            "title": "(AD) Special Event for Friday 1588",
            "body": "Now order at 50% off the price!\n Menu > Set notification"
        }
    }
}
```
##### Messages Received for ja (Japanese) for GCM(Android)
```json
 {
    "data": {
        "title": "Special Event for Friday",
        "body": "Now order at 50% off the price!"
    }
}
```
##### Messages Received for ja (Japanese) for APNS(iOS)
```json
{
    "aps": {
        "alert": {
            "title": "Special Event for Friday",
            "body": "Now order at 50% off the price!"
        }
    }
}
```
##### Description
- Set AD (Advertisement) for messsageType to send ad messages, and include contact number and how to withdraw consent to receiving ad messages for contact and removeGuide, respectively.
- When sending messages for each push type, title shall include the word, AD, and contact number, while body include withdrawal method to consent to receiving ad messages.  
- AD is included to ad messages only for the users whose language code is Korean (ko, or ko-). Like the example shows, overseas users (e.g. Japanese) cannot find the word.

#### 6. Messages in Multiple Languages
Below shows an example of sending messages in various languages.

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
            "customKey": "value"
        },
        "ko" : {
            "title": "title",
            "body": "body",
            "customKey": "Set for such language codes starting with'ko' or 'ko-'."
        },
        "ja" : {
            "title": "タイトル",
            "body": "プッシュ・メッセージ"
        }
    },
    "messageType" : "NOTIFICATION"
}
```

##### Messages Received for ko (Korean) at GCM(Android)
```json
{
    "data": {
        "title": "title",
        "body": "body",
        "customKey": "Set for the language codes starting with'ko'or 'ko-'."
    }
}
```
##### Messages Received for ko-KR (Korean) at GCM(Android)
```json
{
    "data": {
        "title": "title",
        "body": "body",
        "customKey": "Set for the language codes starting with'ko'."
    }
}
```
##### Messages Received for ja (Japanese) for GCM(Android)
```json
{
    "data": {
        "title": "タイトル",
        "body": "プッシュ・メッセージ",
        "customKey": "value"
    }
}
```
##### Messages Received for en (English) for GCM(Android)
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
- Enter a message for each language code below Content and it shall be converted to the same or similar language of the token before delivery.
  When there is no matching language code for the code, default message shall be delivered. For English users whose code is en, content.default is delivered.
- Conversion is applied to a closest language code even if it may not perfectly match.
  Users of ko-KR (Korean) shall be delivered with content.ko, even if the request body show content.ko only.
- customKey is delivered with content.default as it is not defined in content.ja: enter common messages to content.defalt.

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

### Get
#### List
※ Only the push messages sent using the console can be retrieved by the List API.
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/messages?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&deliveryType={deliveryType}&messageStatus={messageStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field         | Usage                     | Description                                                  |
| ------------- | ------------------------- | ------------------------------------------------------------ |
| appkey        | Required, String          | Path Variable, Appkey issued on Enable                       |
| pageIndex     | Optional, Number          | Default is 0                                                 |
| pageSize      | Optional, Number          | Default is 25; Max 100                                       |
| from          | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to            | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| deliveryType  | Optional, String          | 'INSTANT'(immediate delivery), <br />'RESERVATION'(scheduled delivery) |
| messageStatus | Optional, String          | 'READY', 'PROCESSING', 'COMPLETE', 'CANCEL_NO_TARGET', 'CANCEL_INVALID_CERTIFICATE', 'CANCEL_INVALID_MESSAGE', 'CANCEL_UNSUPPORTED_MESSAGE_TYPE', 'CANCEL_UNAUTHORIZED', 'CANCEL_UNKNOWN' |

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
        "removeGuide": "menu > setting",
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
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/messages
```

| Field                 | Usage | Description               |
| --------------------- | ----- | ------------------------- |
| createdDateTime       | -     | Date and time of message creation (ISO 8601)    |
| completedDateTime     | -     | Date and time of delivery completed (ISO 8601) |
| targetCount           | -     | Number of target tokens to deliver               |
| sentCount             | -     | Number of tokens actually delivered               |
| provisionedResourceId | -     | Provisioned resource ID of a message delivered       |
| totalCount            | -     | Total number of filtered messages            |

- “messageStatus” displays the status of a message as follows:
  - READY: Request for message delivery has been registered.
  - PROCESSING: Message has been created and is ready for, or on delivery.
  - COMPLETE: Message delivery has been completed.
  - CANCEL_NO_TARGET: Delivery has been cancelled because there is no target. Delivery may be cancelled due to the following reasons:
    When there is no registered token;
    When no user agreed to receive ad push messages;   
    When no user agreed to receive night-time ad push messages (21:00 – 08:00); and,
    When there is no token available as existing ones are deleted.
  - CANCEL_INVALID_CERTIFICATE: Delivery has been cancelled because the certificate is wrong. Need to check the status of certificate.
  - CANCEL_INVALID_MESSAGE: Delivery has been cancelled because the format of message is incorrect.
  - CANCEL_UNSUPPORTED_MESSAGE_TYPE: Delivery has been cancelled because the type of message is incorrect
  - CANCEL_UNAUTHORIZED: Has failed in the process of certificate authorization. Need to check the status of certificate.  
  - CANCEL_UNKNOWN: Error occurred internally.

#### Get
※ Only the push messages sent using the console can be retrieved by the Get API.
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/messages/{message-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field     | Usage            | Description                            |
| --------- | ---------------- | -------------------------------------- |
| appkey    | Required, String | Path Variable, Appkey issued on Enable |
| messageId | Required, Number | Message ID                             |

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
        "removeGuide": "menu > setting",
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
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/messages/{messageId}
```

#### List Failed Messages

Can list messages that are failed in delivery.
However, invalid token (INVALID_TOKEN) is not deemed as a delivery failure.

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/message-errors?messageId={messageId}&messageErrorType={messageErrorType}&messagErrorCause={messageErrorCause}&from={from}&to={to}&limit={limit}
HEADER
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field             | Usage                     | Description                                                |
|-------------------|---------------------------| ---------------------------------------------------------- |
| appkey            | Required, String          | Path Variable, Appkey issued on Enable                     |
| messageId         | Optional, Number          | Message ID                                                 |
| messageErrorType  | Optional, String          | 'CLIENT_ERROR', 'EXTERNAL_ERROR', 'INTERNAL_ERROR'         |
| messageErrorCause | Optional, String          | 'UNSUPPORTED_MESSAGE_TYPE', 'INVALID_MESSAGE', 'INVALID_CERTIFICATE', 'UNAUTHORIZED', 'EXPIRED_TIME_OUT', 'APNS_ERROR', 'GCM_ERROR', 'TENCENT_ERROR', 'AGENT_ERROR', 'ADM_ERROR' |
| from              | Optional, DateTime String | Up to the latest 30 days, or 7 days for default (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to                | Optional, DateTime String | Up to the latest 30 days, or 7 days for default (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| limit             | Optional, Number          | Size to list at once: default and maximum value is 100     |
| pageNumber        | Optional, Number          | Page number, default is 1 |
##### Description
- messageErrorType and messageErrorCause mean the followings:
    - CLIENT_ERROR: Client’s wrong request
        - UNSUPPORTED_MESSAGE_TYPE: Unsupported type of message
        - INVALID_MESSAGE: Invalid message
        - INVALID_CERTIFICATE: Expired or wrong certificate
        - UNAUTHORIZED: Expired or wrong certificate
    - EXTERNAL_ERROR: Error in external services related to push, such as APNS, CGM, Tencent, or ADM
        - APNS_ERROR: Delivery to APNS (iOS) failed
        - GCM_ERROR: Delivery to GCM (Google) failed
        - TENCENT_ERROR: Delivery to Tencent failed
        - ADM_ERROR: Delivery to ADM failed
    - INTERNAL_ERROR: Internal error of push
        - EXPIRED_TIME_OUT: Valid time expired due to delivery delay
        - AGENT_ERROR: Agent Delivery failed due to internal error of Agent

- When header.resultCode is 40010 in the Response Body, query with shorter period (from, to).

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
		"resultMessage" :  "Client Error. Too much:use'from' and 'to' shorten. totalCount<10>",
		"isSuccessful" :  false
	}
}
```
| Field           | Usage | Description                                                  |
| --------------- | ----- | ------------------------------------------------------------ |
| messageId       | -     | ID of failed messages                                        |
| messageIdString | -     | ID of failed messages                                        |
| pushType        | -     | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
| payload         | -     | Messages actually delivered on a device                      |
| tokens          | -     | UID and token of recipient to failed delivery                |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/message-errors
```

#### Get Message Delivery/Receipt Statistics

**This API has been deprecated. Please use API v2.4 or higher.**

Enable Message Delivery Receipt and apply v1.4 or higher SDK, so as to check the status of message delivery/receipt. Can get collected data with statistics API. Go to the [Console] > [Settings] tab to enable the function.

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/statistics/message-delivery-receipts?from={from}&to={to}&event={event}&timeUnit={timeUnit}&messageId={messageId}
HEADER
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field     | Usage                     | Description                                                  |
| --------- | ------------------------- | ------------------------------------------------------------ |
| appkey    | Required, String          | Path Variable, Appkey issued on Enable                       |
| from      | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to        | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| event     | Optional, String          | 'SENT', 'SENT_FAILED', 'RECEIVED', 'OPENED'                  |
| timeUnit  | Optional, String          | 'MINUTES', 'HOURS', 'DAYS'<br> When value is not available, statistics are randomly provided, depending on the retrieved period. <br />A day or more is displayed in Days, between 1 and 24 hours is in Hours, and less than 1 hour is in Minutes. |
| messageId | Optional, Number          | Message ID                                                   |

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

| Field      | Usage                     | Description                              |
| ---------- | ------------------------- | ---------------------------------------- |
| dateTime   | Optional, DateTime String | ISO 8601                                 |
| sent       | Optional, Number          | Number of deliveries from a server       |
| sentFailed | Optional, Number          | Number of failed delivery from a server  |
| received   | Optional, Number          | Number of receipt on a device            |
| opened     | Optional, Number          | Number of user’s click-to-open on device |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/statistics/message-delivery-receipts
```

## Scheduled Messages

## Create

#### Create Schedule for Delivery of Scheduled Messages

##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/schedules
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field  | Usage            | Description                            |
| ------ | ---------------- | -------------------------------------- |
| appkey | Required, String | Path Variable, Appkey issued on Enable |


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

| Field      | Usage                  | Description                                                  |
| ---------- | ---------------------- | ------------------------------------------------------------ |
| type       | Required, String       | 'EVERY_DAY' (daily), 'EVERY_WEEK' (weekly), 'EVERY_MONTH' (monthly) |
| fromDate   | Required, Date String  | Start date of scheduled message (YYYY-MM-DD)                 |
| toDate     | Required, Date String  | End date of scheduled message (YYYY-MM-DD)                   |
| times      | Required, Time String  | Delivery hour/minute of scheduled message (hh:mm)            |
| days       | Optional, Number Array | Set when the type is ‘EVERY_MONTH’ (1, 2, ..., 31: 1day, 2 days, ..., 31days) |
| daysOfWeek | Optional, String Array | Set when the type is ‘EVERY_WEEK’ ('SUNDAY', 'MONDAY', 'TUESDAY', 'WEDNESDAY', 'THURSDAY', 'FRIDAY', 'SATURDAY') |

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

| Field     | Usage | Description                                     |
| --------- | ----- | ----------------------------------------------- |
| schedules | -     | Date and time (ISO 8601, e.g. YYYY-MM-DDThh:mm) |

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/schedules -d '{"type":"EVERY_MONTH","fromDate":"2016-12-30","toDate":"2017-01-02","times":["12:00","17:00"],"days":[1,15],"daysOfWeek":["SUNDAY","MONDAY"]}'
```

#### Create Scheduled Messages
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/reservations
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field  | Usage            | Description                            |
| ------ | ---------------- | -------------------------------------- |
| appkey | Required, String | Path Variable, Appkey issued on Enable |

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
    "removeGuide": "menu > setting",
    "timeToLiveMinute": 1,
	"provisionedResourceId": "id"
}
```

| Field       | Usage                           | Description                                     |
| ----------- | ------------------------------- | ----------------------------------------------- |
| schedules   | Required, DateTime String Array | List of delivery schedule of scheduled messages |
| isLocalTime | Required, Boolean               | Whether to be sent on the local-time basis      |

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

| Field               | Usage  | Description                     |
| ------------------- | ------ | ------------------------------- |
| reservationId       | Number | ID of scheduled message         |
| reservationIdString | String | ID string of scheduled messages |

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations -d '{"schedules":["2016-12-30T12:40","2016-12-31T12:40"],"isLocalTime":false,"target":{"type":"UID","to":["uid"]},"content":{"default":{"title":"title","body":"body"}},"messageType":"AD","contact":"1588-1588","removeGuide":"매뉴 > 설정","timeToLiveMinute":1}'
```

### Get
#### List
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/reservations?pageIndex={pageIndex}&pageSize={pageSize}&reservationStatus={reservationsStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field             | Usage                     | Description                                                  |
| ----------------- | ------------------------- | ------------------------------------------------------------ |
| appkey            | Required, String          | Path Variable, Appkey issued on Enable                       |
| pageIndex         | Optional, Number          | Default is 0                                                 |
| pageSize          | Optional, Number          | Default is 25; Max 100                                       |
| from              | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to                | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| reservationStatus | Optional, String          | 'RESERVED', 'COMPLETE'                                       |

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
					"title" : "Korean title",
					"body" : "Korean body"
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

| Field                         | Usage | Description                                                  |
| ----------------------------- | ----- | ------------------------------------------------------------ |
| reservationIdString           | -     | ID string of scheduled message                               |
| createdDateTime               | -     | Date and time of scheduled message creation (ISO 8601)       |
| updatedDateTime               | -     | Date and time of message modification (ISO 8601)             |
| completedDateTime             | -     | Date and time of message delivery completed; if not completed, show the current time (ISO 8601) |
| reservationStatus             | -     | 'RESERVED', 'COMPLETED'                                      |
| schedules.scheduleId          | -     | Delivery schedule ID of scheduled messages                   |
| schedules.scheduleIdString    | -     | ID String of delivery schedule of a scheduled message        |
| schedules.reservationIdString | -     | ID String of a scheduled message to which message delivery schedule belongs |
| schedules.deliveryDateTime    | -     | Date and time of scheduled message delivery                  |
| schedules.timezoneOffset      | -     | Delivery time zone of scheduled message: to be set when delivered on the local-time basis |
| schedules.scheduleStatus      | -     | Status of delivery schedule of scheduled messages, such as 'READY', 'SENDING', 'CANCELED', and 'DONE'READY', 'SENDING', 'CANCELED', 'DONE' |
| totalCount                    | -     | Number of total registered scheduled messages                |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations
```

#### Get
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/reservations/{reservation-id}
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
				"title" : "Korean title",
				"body" : "Korean body"
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

| Field           | Usage           | Description                                       |
| --------------- | --------------- | ------------------------------------------------- |
| updatedDateTime | DateTime String | Date and time of schedule modification (ISO 8601) |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations/{reservationId}
```

#### List Delivered Scheduled Messages
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/reservations/{reservation-id}/messages?pageIndex={pageIndex}&pageSize={pageSize}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field         | Usage            | Description                            |
| ------------- | ---------------- | -------------------------------------- |
| appkey        | Required, String | Path Variable, Appkey issued on Enable |
| reservationId | Required, Number | ID of scheduled messages               |
| pageIndex     | Optional, Number | Default is 0                           |
| pageSize      | Optional, Number | Default is 25; Max 100                 |

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
					"title" : "Scheduled message for 6:55",
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

| Field      | Usage | Description                        |
| ---------- | ----- | ---------------------------------- |
| totalCount | -     | Total number of delivered messages |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations/{reservationId}/messages
```

### Modify
#### Modify Scheduled Messages
##### Method, URL, Headers
```
PUT /push/v2.0/appkeys/{appkey}/reservations/{reservationId}
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
			"title" : "Korean title",
			"body" : "Korean body"
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
curl -X PUT -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations/{reservationId} -d '{"schedules":["2018-12-30T12:40","2018-12-31T12:40"],"isLocalTime":false,"target":{"type":"UID","to":["uid"]},"content":{"default":{"title":"title","body":"body"}},"messageType":"AD","contact":"1588-1588","removeGuide":"menu > setting","timeToLiveMinute":1}'
```

### Delete
#### Delete Scheduled Messages
##### Method, URL, Headers
```
DELETE /push/v2.0/appkeys/{appkey}/reservations?reservationIds={reservationId,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field          | Usage                  | Description                                |
| -------------- | ---------------------- | ------------------------------------------ |
| appkey         | Required, String       | Path Variable, Appkey issued on Enable     |
| reservationIds | Required, Number Array | Delimited by ',' (e.g. reservationIds=1,2) |

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
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations?reservationIds={reservationId,}
```

## Tags

### Create
#### Create Tags
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/tags
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body

```json
{
    "tagName" :  "Thirty"
}
```

| Field   | Usage            | Description                        |
| ------- | ---------------- | ---------------------------------- |
| tagName | Required, String | Tag name, the maximum length is 255 |

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

| Field | Usage            | Description                     |
| ----- | ---------------- | ------------------------------- |
| tagId | Required, String | Created tag ID, the length of 8 |

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags -d '{"tagName":"thirty"}'
```

#### Create Additional UIDs to a Tag

- Refers to appending UIDs to a tag: by adding UIDs, tags of an UID add up.
- The maximum number of tags to an UID is 16

##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/tags/{tag-id}/uids
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
| Field | Usage                  | Description                                                 |
| ----- | ---------------------- | ----------------------------------------------------------- |
| uids  | Required, String Array | UID string array, 16 to the longest; UID, 64 to the longest |

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
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags/{tagId}/uids -d '{"uids":["uid"]}'
```

#### Set a Tag List to UID

- Refers to replacing tags of an UID: an old tag is deleted and replaced by a new tag.

##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/uids
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
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids -d '{"uid":"uid","tagIds":["TAG_ID"]}'
```

### Get
#### List Tags
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/tags?tagName={tagName}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field   | Usage            | Description |
| ------- | ---------------- | ----------- |
| tagName | Optional, String | Tag name    |

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

| Field           | Usage                      | Description                              |
| --------------- | -------------------------- | ---------------------------------------- |
| createdDateTime | Required, Date Time String | Date and time of creation (ISO 8601)     |
| updatedDateTime | Required, Date Time String | Date and time of modification (ISO 8601) |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags
```

#### Get a Tag
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/tags/{tag-id}
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
        "tagName" :  "thirty",
        "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
        "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
    }
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags/{tagId}
```

#### List UID of a Tag

- Get a list of tagged UIDs

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/tags/{tag-id}/uids?offsetUid={uid}&limit={limit}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field     | Usage            | Description                  |
| --------- | ---------------- | ---------------------------- |
| offsetUid | Optional, String | Get starts next from set UID |
| limit     | Optional, Number | Number of UIDs to list       |

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

| Field           | Usage                      | Description                                                  |
| --------------- | -------------------------- | ------------------------------------------------------------ |
| contacts        | -, Object Array            | List of UID contacts and tokes                               |
| contactType     | -, String                  | Token type, 'TOKEN_GCM', 'TOKEN_APNS', 'TOKEN_APNS_SANDBOX', 'TOKEN_TENCENT', 'TOKEN_ADM' |
| contact         | -, String                  | Token                                                        |
| createdDateTime | Required, Date Time String | Date and time of creation (ISO 8601)                         |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags/{tagId}/uids
```

#### Get UID
- Get a UID.
- When a token is registered, contact is registered
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/uids/{uid}
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
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids/uid
```

### Modify
#### Modify Tags
##### Method, URL, Headers
```
PUT /push/v2.0/appkeys/{appkey}/tags/{tag-id}
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
curl -X PUT -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags/{tagId} -d '{"tagName":"thirty three"}'
```

### Delete
#### Delete Tags
##### Method, URL, Headers
```
DELETE /push/v2.0/appkeys/{appkey}/tags/{tag-id}
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
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags/{tagId}
```

#### Delete UIDs
- When an UID is deleted, contact and token are deleted altogether.

##### Method, URL, Headers
```
DELETE /push/v2.0/appkeys/{appkey}/uids?uids={uid,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage           | Description                                                  |
| ----- | --------------- | ------------------------------------------------------------ |
| uids  | -, Object Array | Delimit list of UIDs to delete by comma (,): can delete up to 16 at once. |

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
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids?uids=uid
```

#### Delete UIDs of a Tag
- Only the tag-UID relation is deleted.
- Contact and Token are not deleted.
##### Method, URL, Headers
```
DELETE /push/v2.0/appkeys/{appkey}/tags/{tagId}/uids?uids={uid,}
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
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-Secret-Key: SECRET_KEY" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tags/{tagId}/uids?uids=uid
```

## UID

### Create

#### Add Tags
- Add tags to a UID with Tag ID.
- No Secret Key is required: call is available from an app.
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/uids/{uid}/tag-ids
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
curl -X POST -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids/uid/tag-ids -d '{"tagIds":["TAG_ID"]}'
```

### Get

#### Get Tag ID of UID
- Retrieve tag  ID of an UID.
- No Secret Key is required: call is available from an app.
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/uids/{uid}/tag-ids
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
curl -X GET -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids/uid/tag-ids
```

### Modify
#### Modify Tags of UID
- Modify tags with a tag ID.
- No Secret Key is required: call is available from an app.
##### Method, URL, Headers
```
PUT /push/v2.0/appkeys/{appkey}/uids/{uid}/tag-ids
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
curl -X PUT -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids/uid/tag-ids -d '{"tagIds":["TAG_ID"]}'
```

### Delete Tags
- Get tag IDs of UID.
- No Secret Key is required: call is available from an app.
##### Method, URL, Headers
```
DELETE /push/v2.0/appkeys/{appkey}/uids/{uid}/tag-ids?tagIds={tagId,}
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

| Field  | Usage                  | Description                                            |
| ------ | ---------------------- | ------------------------------------------------------ |
| tagIds | Required, String Array | Query String, Tag ID to delete: delimited by comma (,) |

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/uids/uid/tag-ids?tagIds=TAG_ID_01,TAG_ID_02
```


* *Document Updates*
    * *(2018.06.26) Added example of message delivery*
    * *(2018.06.26) Added pushType ADM*
    * *(2018.05.29) Added v2.1 Get Token API*
    * *(2018.05.29) Added Guide for API curl*
    * *(2018.04.24) Added description of timeUnit field for v2.0 Message Delivery Receipt API*
    * *(2018.04.24) Added description of DateTime format for v2.0 API*
    * *(2018.03.22) Added v2.0 UID API*
    * *(2018.02.22) Added deliveryType field for v2.0 Get Message API*
    * *(2018.02.22) Added pushType APNS_VOIP, and APNS_SANDBOXVOIP*
    * *(2017.11.23) Modified description of v2.0 Message Error API*
    * *(2017.08.24) Corrected error in description of v2.0 Token, Reservation API*
    * *(2017.07.20) Added v2.0 Tag API Reference*
    * *(2017.07.20) Added Get Failed Message API*
    * *(2017.04.25) Added v2.0 API Reference*
    * *(2017.02.23) Updated List Tokens API*
