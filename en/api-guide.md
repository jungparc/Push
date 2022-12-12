## Notification > Push > API v2.4 Guide

### Overview of v2.4 API

#### Add
- Added 'Statistics' API. 

### Basic Information 
#### Endpoint
```
API Endpoint: https://api-push.cloud.toast.com
Endpoint for collecting message delivery receipt/checking status: https://collector-push.cloud.toast.com
```
### Secret Key
- Available on console. 
- To call APIs requiring a secret key, configure the header as below: 
```
Header
X-Secret-Key: [a-zA-Z0-9]{8}
```
Go to [CONSOLE] > [Notification] > [Push] > [URL & AppKey] to check and create one.  

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
		"resultMessage" : "success"
	}
}
```

## Tokens
### Create
- Can be queried from client. 

##### Method, URL
```
POST /push/v2.4/appkeys/{appkey}/tokens
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
  "pushType": "FCM",
  "timezoneId": "Asia/Seoul",
  "uid": "uid",
  "country": "KR",
  "language": "ko",
  "deviceId": "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD"
}
```

|Field|	Usage | Description |
|---|---|---|
|token|	Required, String|	Token, no more than 1,600 characters, no Korean allowed |
|oldToken|	Optional, String|	Existing token, up to 1,600 characters |
|pushType|	Required, String| 'FCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
|isNotificationAgreement|	Required, Boolean|	true or false|
|isAdAgreement|	Required, Boolean|	true or false|
|isNightAdAgreement| Required, Boolean|	true or false|
|timezoneId|	Required, String|	Area/Name. IANA time zone database.|
|country|	Required, String| ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3 characters |
|language|	Required, String| ISO 639-1, ISO 639-2, iOS (language code + script code), 8 characters |
|uid|	Required, String| User ID, up to 64 characters, no emojis allowed |
|deviceId|	Required, String| Device ID, up to 36 characters |


##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tokens' \
-H 'Content-Type: application/json;charset=UTF-8' \
-d '{
    "oldToken": "oldToken",
    "token": "token",
    "isNotificationAgreement": true,
    "isAdAgreement": true,
    "isNightAdAgreement": true,
    "pushType": "FCM",
    "timezoneId": "Asia/Seoul",
    "uid": "uid",
    "country": "KR",
    "language": "ko",
    "deviceId": "deviceId"
}'
```

##### Response Body

```
{
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "success"
	}
}
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
#### Query Tokens by Token
- Can be queried from client. 
##### Method, URL

```
GET /push/v2.4/appkeys/{appkey}/tokens/{token}?pushType={pushType}
Content-Type: application/json;charset=UTF-8
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| pushType | Required, String | 'FCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |

##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tokens/'"${TOKEN}"'?pushType='"${PUSH_TYPE}" \
-H 'Content-Type: application/json;charset=UTF-8'
```

##### Response Body

```
{
    "token" : {
        "pushType" : "FCM",
        "isNotificationAgreement" : true,
        "isAdAgreement" : true,
        "isNightAdAgreement" : true,
        "timezoneId" : "Asia/Seoul",
        "country" : "KR",
        "language" : "ko",
        "uid" : "User ID",
        "token" : "Token",
        "updatedDateTime" : "2017-08-12T01:04:18.000+09:00",
        "adAgreementDateTime" : "2017-08-12T01:04:19.000+09:00",
        "nightAdAgreementDateTime" : "2017-08-12T01:04:19.000+09:00",
        "deviceId" : "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD",
        "activatedDateTime" : "2017-08-12T01:04:19.000+09:00"
    },
    "header" : {
        "isSuccessful" : true,
        "resultCode" : 0,
        "resultMessage" : "success"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| updatedDateTime | -, DateTime String | Date and time of token update |
| adAgreementDateTime | -, DateTime String | Date and time of consent to receive ad push messages |
| nightAdAgreementDateTime | -, DateTime String | Date and time of consent to receive night-time ad push messages |
| deviceId | -, String | Device ID |
| activatedDateTime | -, Datetime String | Date and time of request for recent token registration |

#### Query Tokens by User ID
- The API requires a secret key and must be called from a server.
##### Method, URL

```
GET /push/v2.4/appkeys/{appkey}/tokens?uid={uid}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| uid | Required, String | User ID to query |

##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tokens?uid='"${USER_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body

```
{
	"tokens": [{
		"pushType" : "FCM",
		"isNotificationAgreement": true,
		"isAdAgreement": true,
		"isNightAdAgreement": true,
		"timezoneId" : "Asia/Seoul",
		"country": "KR",
		"language": "ko",
		"uid" : "User ID",
		"token" : "Token",
        "updatedDateTime": "2017-08-12T01:04:18.000+09:00",
        "adAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
        "nightAdAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
        "deviceId" : "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD",
        "activatedDateTime" : "2017-08-12T01:04:19.000+09:00"
	}],
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "success"
	}
}
```

#### Query Invalid Tokens
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/invalid-tokens?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&messageId={messageId}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| pageIndex | Optional, Number | Default is 0 |
| pageSize | Optional, Number | Default is 25; max is 100 |
| from | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| messageId | Optional, Number | Message ID in which invalid token occurs |

##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/invalid-tokens' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "success",
		"isSuccessful" : true
	},
	"invalidTokens" : [{
			"messageId" : 0,
			"uid" : "uid",
			"token" : "invalid-token",
			"pushType" : "FCM",
			"createdDateTime" : "2017-02-08T19:39:04.000+09:00"
		}
	]
}
```

### Delete 
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/tokens/{token}?pushType={pushType}
Content-Type: application/json;charset=UTF-8
```
| Field | Usage | Description |
| - | - | - |
| token | Required, String | Token of device |
| pushType | Optional, String | Push type of a token: when no value is available, delete tokens for all push types |

##### Request Body
```
N/A 
```

##### cURL
```
curl -X DELETE \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tokens/'"${TOKEN}"'?pushType='"${PUSH_TYPE}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" : true,
        "resultCode" : 0,
        "resultMessage" : "Success."
    }
}
```

## Messages
### Send
※ Push messages sent using the API cannot be retrieved in the console or by the Get or List API. For push messages sent using the API, use the Query Logs API after enabling the Logging function.
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/messages
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
    "removeGuide": "Menu > Setting",
    "timeToLiveMinute": 1,
    "provisionedResourceId": "id",
    "adWordPosition": "TITLE"
}
```

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/messages' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "target": {
        "type": "ALL"
    },
    "content": {
        "default": {
            "title": "title",
            "body": "body",
            "customKey1": "It is default"
        },
        "ko": {
            "title": "제목",
            "body": "내용",
            "customKey2": "한국어 입니다."
        }
    },
    "messageType": "AD",
    "contact": "1588-1588",
    "removeGuide": "매뉴 > 설정"
}'
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
        "resultMessage" : "success"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| target.type | Required, String | Type of receiving targets, such as 'ALL', 'UID', or 'TAG' |
| target.to | Optional, String Array | target.type as UID list for recipients (up to 10,000) or as TAG condition |
| target.pushTypes | Optional, String Array | 'FCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
| target.countries | Optional, String Array | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3 (up to 3 characters) |
| content | Required, Map | To be delivered to recipients (up to 8,192 characters) |
| content.default | Required, Map | See 'common message type as below for more details |
| content.default.title | Optional, String |  |
| content.default.body | Optional, String |  |
| content.default.notification | Optional, Object | Notification field for FCM |
| content.default.style.useHtmlStyle | Optional, Boolean | With 'true', iOS shows messages excluding HTML. |
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

### Common Messages 
When messages are written for "content" as described in the below table, messages are created and delivered to suit for each push type. 

|Reserved Word|	Platform|	Usage|	FCM|	APNS|	TENCENT| ADM |
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
|notification| Android | Optional, Object | notification | - | - | - |
|content_available| FCM(iOS) | Optional, Boolean | content_available | - | - | - |
|mutable_content| FCM(iOS) | Optional, Boolean | content_available | - | - | - |

Reserved words are set at the right location for each platform when a message is created. User cannot change data type or location on his own. 
Other user-defined words are included to Custom Key/Value as follows: 

|Word|	Platform|	Usage|	FCM|	APNS|	TENCENT| ADM|
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
        "pushTypes": ["FCM", "APNS"]
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

##### On FCM (Android)
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
            "body": "Order Now at 50% Discount Prices!"
        }
    },
    "messageType" : "AD",
    "contact": "1588",
    "removeGuide": "Menu > Notification Setting"
}
```

##### On FCM (Android), ko (Korean)
```json
 {
    "data": {
        "title": "(AD) 1588 for Friday Special Event",
        "body": "Order Now at 50% Discount Prices!\n Menu > Notification Setting"
    }
}
```
##### APNS (iOS), ko (Korean)
```json
{
    "aps": {
        "alert": {
            "title": "(AD) 1588 for Friday Special Event",
            "body": "Order Now at 50% Discount Prices!\n Menu > Notification Setting"
        }
    }
}
```
##### FCM (Android), ja (Japanese)
```json
 {
    "data": {
        "title": "Friday Special Event",
        "body": "Order Now at 50% Discount Prices!"
    }
}
```
##### On APNS (iOS), ja (Japanese)
```json
{
    "aps": {
        "alert": {
            "title": "Friday Special Event",
            "body": "Order Now at 50% Discount Prices!"
        }
    }
}
```
##### Description
- To send ad messages, set AD for messageType, as well as contact and how to withdraw consent of receiving, each for contact and removeGuide. 
- When a message is delivered for each push time, title and body are added: ad phrase and contact for Title, and how to withdraw consent of receiving for Body. 
- Ad messages display Ad Phrase for those users whose language code is Korean (ko, ko-) only. Other language users (e.g. Japanese) shall not find ad phrases. 

#### 6.  Multiple-Language Messages

The example describes how to send messages in multiple languages.

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
            "title": "Title",
            "body": "Body",
            "customKey": "'Set for language codes starting with ko' or 'ko-'"
        },
        "ja" : {
            "title": "タイトル",
            "body": "プッシュ・メッセージ"
        }
    },
    "messageType" : "NOTIFICATION"
}
```

##### On FCM (Android), ko (Korean)
```json
{
    "data": {
        "title": "title",
        "body": "body",
        "customKey": "Set for language codes starting with 'ko'or'ko-'."
    }
}
```
##### On FCM (Android), ko-KR (Korean)
```json
{
    "data": {
        "title": "title",
        "body": "body",
        "customKey": "Set for language codes starting with'ko'or 'ko-'."
    }
}
```
##### On FCM (Android), ja (Japanese)
```json
{
    "data": {
        "title": "タイトル",
        "body": "プッシュ・メッセージ",
        "customKey": "value"
    }
}
```
##### On FCM (Android), en (English)
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
                        "buttonType" : "REPLY | DEEP_LINK | OPEN_APP | OPEN_URL | DISMISS",
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
| richMessage.buttons | Optional, Object Array | Buttons added for rich messages: up to 3 |
| richMessage.button.name | Required, String | Button name |
| richMessage.button.buttonType | Required, String | Button type: REPLY, DEEP_LINK, OPEN_APP, OPEN_URL, DISMISS |
| richMessage.button.link | Required, String | Link connected at the press of the button |
| richMessage.button.hint | Required, String | Hint for a button |
| richMessage.media | Optional, Object | Media added for rich messages |
| richMessage.media.sourceType | Required, String | Media location: REMOTE or LOCAL |
| richMessage.media.source | Required, String | Address where media is located |
| richMessage.media.mediaType | Required, String | Media Type: IMAGE, GIF, VIDEO, or AUDIO. Android supports IMAGE only |
| richMessage.media.extension | Required, String | Extension of media file |
| richMessage.media.expandable | Required, Boolean | Click to expand media on Android |
| richMessage.largeIcon | Optional, Object | Large icons added for rich messages: only on Android |
| richMessage.androidMedia | Optional, Object | Media used by Android devices. Format is the same as media |
| richMessage.iosMedia | Optional, Object | Media used by iOS devices. Format is the same as media |
| richMessage.largeIcon.sourceType | Required, String | Location of large icons: REMOTE or LOCAL |
| richMessage.largeIcon.source | Required, String | Address where media is located |
| richMessage.group | Optional, Object | Bind many messages into a group: only on Android |
| richMessage.group.key | Required, String | Key of a group |
| richMessage.group | Required, String | Description of a group |

#### 8. Messages with FCM Notification 
With the 'notification' field defined in 'content', messages can be delivered to suit for the FCM type. 

##### Request Body
```
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "notification" : {
                "title" : "Title",
                "body" : "Body",
                "android_channel_id": "Android Channel",
                "sound" : "Sound",
                "click_action" : "Click-to-action Message",
                "title_loc_key" : "String key",
                "title_loc_args" : [
                    "String"
                ],
                "body_loc_key" : "String key",
                "body_loc_args" : [
                    "String"
                ],
                "icon" : "notification_off",
                "color" : "#4286f4",
                "tag" : "tag"
            }
        }
    },
    "messageType" : "NOTIFICATION"
}
```

| Reserved Word | Platform | Usage | FCM | Description |
| - | - | - | - | - |
| notification | Android, iOS | Optional, Object | notification | |
| notification.title | Android, iOS | Optional, String | notification.title | Set notification.title, and data.title is ignored. |
| notification.body | Android, iOS | Optional, String | notification.body | Set notification.body, and data.body is ignored. |
| notification.sound | Android, iOS | Optional, String | notification.sound | Notification sound when message is received: not supported on Android Oreo (8.0) or higher. |
| notification.clickAction | Android, iOS | Optional, String | notification.click_action | Activity is to move, at the click of a message. |
| notification.titleLocKey | Android, iOS | Optional, String | notification.title_loc_key | String resource which can be applied to localize messages. |
| notification.titleLocArgs | Android, iOS | Optional, String | notification.title_loc_args | String for replacement, which can be applied to localize messages. |
| notification.bodyLocKey | Android, iOS | Optional, String | notification.body_loc_key | String resource which can be applied to localize messages. |
| notification.bodyLocArgs | Android, iOS | Optional, String Array | notification_body_loc_args | String for replacement, which can be applied to localize messages. |
| notification.icon | Android, iOS | Optional, String | notification.icon | Notification icon. |
| notification.color | Android, iOS | Optional, String | notification.color | Color of notification icon. |
| notification.tag | Android, iOS | Optional, String | notification.tag | Same tagged messages are received and replace existing messages. |
| notification.badge | Android, iOS | Optional, Number | notification.badge | Number of new unconfirmed notifications. |

##### On FCM (Android)
```json
{
    "registration_ids" : [
        "Token"
    ],
    "notification" : {
        "title" : "Title",
        "body" : "Body",
        "android_channel_id": "Android Channel",
        "sound" : "Sound",
        "click_action" : "Click-to-Action Message",
        "title_loc_key" : "String Key",
        "title_loc_args" : [
            "String Key"
        ],
        "body_loc_key" : "String Key",
        "body_loc_args" : [
            "String Key"
        ],
        "icon" : "notification_off",
        "color" : "#4286f4",
        "tag" : "tag"
    }
}
```

### Query
#### List
※ Only the push messages sent using the console can be retrieved by the List API. For push messages sent using the API, use the Query Logs API after enabling the Logging function.
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/messages?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&deliveryType={deliveryType}&messageStatus={messageStatus}
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
| deliveryType | Optional, String | 'INSTANT'(immediate delivery), 'RESERVATION'(scheduled delivery) |
| messageStatus | Optional, String | 'READY', 'PROCESSING', 'COMPLETE', 'CANCEL_NO_TARGET', 'CANCEL_INVALID_CERTIFICATE', 'CANCEL_INVALID_MESSAGE', 'CANCEL_UNSUPPORTED_MESSAGE_TYPE', 'CANCEL_UNAUTHORIZED', 'CANCEL_UNKNOWN' |

##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/messages' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```


##### Response Body
```json
{
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "success"
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
    "totalCount": 1
}
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
※ Only the push messages sent using the console can be retrieved by the Get API. For push messages sent using the API, use the Query Logs API after enabling the Logging function.
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/messages/{message-id}
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

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/messages/'"${MESSAGE_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
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
        "resultMessage" : "success"
    }
}
```


#### List Failed Messages
List messages that are failed in delivery. 
However, if there is no token available (INVALID_TOKEN), it is not deemed as delivery failure. 

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/message-errors?messageId={messageId}&messageErrorType={messageErrorType}&messageErrorCause={messageErrorCause}&from={from}&to={to}&limit={limit}
HEADER
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| messageId | Optional, Number | Message ID |
| messageErrorType | Optional, String | 'CLIENT_ERROR', 'EXTERNAL_ERROR', 'INTERNAL_ERROR' |
| messageErrorCause | Optional, String | 'UNSUPPORTED_MESSAGE_TYPE', 'INVALID_MESSAGE', 'INVALID_CERTIFICATE', 'UNAUTHORIZED', 'EXPIRED_TIME_OUT', 'APNS_ERROR', 'FCM_ERROR', 'TENCENT_ERROR', 'AGENT_ERROR', 'ADM_ERROR', 'DUPLICATED_MESSAGE_TOKEN'  |
| from | Optional, DateTime String | Up to the latest 30 days, or before 7 days for default (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | Up to the latest 30 days, or now for default (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| limit | Optional, Number | Size to list at once: 100 for default and max |
| pageNumber | Optional, Number | Page number, default 1 |

##### Description
- messageErrorType and messageErrorCause have the following meaning: 
    - CLIENT_ERROR: Invalid request of client 
        - UNSUPPORTED_MESSAGE_TYPE: Unsupported type of messages 
        - INVALID_MESSAGE: Invalid messages
        - INVALID_CERTIFICATE: Expired or invalid certificate 
        - UNAUTHORIZED: Expired or invalid certificate 
        - DUPLICATED_MESSAGE_TOKEN: Failed because the same message was requested for delivery in duplicate with the token.
    - EXTERNAL_ERROR: Error in external services connected with push, such as APNS, GCM, Tencent, or ADM 
        - APNS_ERROR: Delivery failed to APNS (iOS)
        - GCM_ERROR: Delivery failed to GCM (Google)
        - TENCENT_ERROR: Delivery failed to Tencent
        - ADM_ERROR: Delivery failed to ADM
    - INTERNAL_ERROR: Error occurred within push 
        - EXPIRED_TIME_OUT: Message validity expired due to delivery delays 
        - AGENT_ERROR: Delivery failed due to internal errors of agent  
- If header.resultCode is 40010 at the response body, reduce query period (from, to) and query again.

#### Response Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/message-errors' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```


##### Response Body
```
{
	"messageErrors" : [{
			"messageId" : 0,
			"messageIdString" : "0",
			"pushType" : "FCM",
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
| pushType | - | 'FCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', or 'ADM' |
| payload | - | Message actually delivered on device |
| tokens | - | UID and token of recipients for failed delivery |


### Query Logs 
- Query Logs API can be called only when Logging is enabled. 
- Logging can be enabled on  [Console] > [Notification] > [Push] > [Setting]. 

#### Query General Logs 
- Can query up to 100. 

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/logs/message?messageId={messageId}&uid={uid}&token={token}&pushType={pushType}&from={from}&to={to}&limit={limit}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| messageId | Optional, Number | Message ID |
| uid | Optional, String | User ID |
| token | Optional, String | User token |
| pushType | Optional, String | Push type |
| from | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| limit | Optional, Number | Number of maximum queries: default is 100 |
| pageNumber | Optional, Number | Page numbr, default is 1 |

##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/logs/message?limit=10' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body

```
{
    "header" : {
        "resultCode" : 0,
        "resultMessage" : "success",
        "isSuccessful" : true
    },
    "data" : {
        "count" : 0,
        "logs" : [{
                "logType" : "message-result",
                "logSource" : "tc-push",
                "messageId" : "1",
                "body" : "{\"tokens\":[{\"uid\":\"gimbimloki\",\"token\":\"1\"}],\"payload\":{\"aps\":{\"alert\":{\"title\":\"title\",\"body\":\"body\"},\"mutable-content\":1}}}",
                "logTime" : "1",
                "pushType" : "APNS",
                "sendTime" : "1",
								"searchKey1": "1746041784729856",
						    "searchKey2": "FCM",
						    "searchKey3": "SENT",
                "sentResult" : "SENT",
                "host" : "127.0.0.1",
                "appkey" : "APP_KEY",
                "logVersion" : "v2",
                "isNeedStored" : "bulk",
                "projectName" : "L&CS_APP_KEY",
                "SinkVersion" : "-",
                "projectVersion" : "v2.2"
            }
        ]
    }
}
```

#### Query Mass Log Counts 
- Find the number of logs that are searched by search conditions. 
- 'User Access Key ID' is required, unlike other v2.4 APIs. 
Go to [Member Profile] > [API Security Setting] to create one. 

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appKey}/bulk-logs/message/count?from={from}&to={to}&messageId={messageId}&pushType={pushType}&sendResult={sendResult}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/bulk-logs/message/count?from='"${FROM}"'&to='"${TO}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-User-Access-Key-ID: '"${USER_ACCESS_KEY_ID}"'' \
-H 'X-Secret-Access-Key: '"${SECRET_ACCESS_KEY}"''
```


##### Response Body
```
{
    "header" : {
        "resultCode" : 0,
        "resultMessage" : "success",
        "isSuccessful" : true
    },
    "data" : {
        "count" : 0
    }
}
```


#### Query Mass Logs 
- Query logs of massive amount. 
- Respond with application/stream+json.

```
GET /push/v2.4/appkeys/{appKey}/bulk-logs/message?from={from}&to={to}&messageId={messageId}&pushType={pushType}&sendResult={sendResult}
Content-Type: application/json;charset=UTF-8
Accept: application/stream+json
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| from | Required, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Required, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| messageId | Optional, String | Message ID to query |
| pushType | Optional, String | Push type to query |
| sendResult | Optional, String | Result of delivery: 'SENT', or 'SENT_FAILED' |

#### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/bulk-logs/message?from='"${FROM}"'&to='"${TO}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'Accept: application/stream+json' \
-H 'X-User-Access-Key-ID: '"${USER_ACCESS_KEY_ID}" \
-H 'X-Secret-Access-Key: '"${SECRET_ACCESS_KEY}"
```

#### Response Body
```
{
    "body": "{\"tokens\":[{\"uid\":\"gimbimloki\",\"token\":\"1\"}],\"payload\":{\"aps\":{\"alert\":{\"title\":\"title\",\"body\":\"body\"},\"mutable-content\":1}}}",
    "host": "10.161.240.23",
    "appkey": "eCHQcPuPAiI6TgY8",
    "logTime": "1533802967999",
    "logType": "message-result",
    "pushType": "FCM",
    "sendTime": "1533802967999",
    "logSource": "tc-push",
    "messageId": "1746041784729856",
    "logVersion": "v2",
    "searchKey1": "1746041784729856",
    "searchKey2": "FCM",
    "searchKey3": "SENT",
    "sentResult": "SENT",
    "projectName": "4x7ybimqlRZImbfV",
    "isNeedStored": "bulk",
    "projectVersion": "v2.2"
}
```

## Scheduled Messages

### Create
#### Create Delivery Schedule for Scheduled Messages
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/schedules
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
| daysOfWeek | Optional, String Array | To configure, if the type is 'EVERY_WEEK' ('SUNDAY', 'MONDAY', 'TUESDAY', 'WEDNESDAY', 'THURSDAY', 'FRIDAY', 'SATURDAY') |

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/schedules' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
	"type": "EVERY_MONTH",
	"fromDate": "2016-12-30",
	"toDate": "2017-01-02",
	"times": ["12:00", "17:00"],
	"days": [1, 15],
	"daysOfWeek": ["SUNDAY", "MONDAY"]
}'
```

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "success",
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


#### Create Scheduled Messages 
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/reservations
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

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/reservations' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "schedules": ["2020-12-30T12:40", "2020-12-31T12:40"],
    "isLocalTime": false,
    "target": {
        "type": "UID",
        "to": ["uid"]
    },
    "content": {
        "default": {
            "title": "title",
            "body": "body"
        }
    },
    "messageType": "NOTIFICATION"
}'
```

##### Response Body

```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "success",
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


### Query
#### List
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/reservations?pageIndex={pageIndex}&pageSize={pageSize}&reservationStatus={reservationsStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| pageIndex | Optional, Number | Default is 0 |
| pageSize | Optional, Number | Default is 25: max is 100 |
| from | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| deliveryFrom | Optional, DateTime String | Delivery schedule date start value (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD). The list of delivery schedules inside the queried reservation is displayed without filtering. |
| deliveryTo | Optional, DateTime String | Delivery schedule date end value (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD). The list of delivery schedules inside the queried reservation is displayed without filtering. |
| reservationStatus | Optional, String | 'RESERVED', 'COMPLETE' |

##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/reservations' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body

```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "success",
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

#### Get
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/reservations/{reservation-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/reservations/'"${RESERVATION_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

#### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "success",
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


#### Query Scheduled Messages 
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/reservations/{reservation-id}/messages?pageIndex={pageIndex}&pageSize={pageSize}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, appkey issued on product use |
| reservationId | Required, Number | ID of scheduled message |
| pageIndex | Optional, Number | Default is 0 |
| pageSize | Optional, Number | Default is 25: max is 100 |

##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/reservations/'"${RESERVATION_ID}"'/messages' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
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
				"pushTypes" : ["FCM", "APNS", "APNS_SANDBOX", "TENCENT", "ADM"]
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

### Modify
#### Modify Scheduled Messages 
##### Method, URL, Headers
```
PUT /push/v2.4/appkeys/{appkey}/reservations/{reservationId}
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

##### cURL
```
curl -X PUT \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/reservations/'"${RESERVATION_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "schedules": ["2020-12-30T10:05", "2020-12-31T12:40"],
    "target": {
        "type": "UID",
        "to": ["uid"]
    },
    "content": {
        "default": {
            "title": "default title",
            "body": "default body"
        },
        "ko": {
            "title": "한국어 제목",
            "body": "한국어 내용"
        }
    },
    "isLocalTime": false,
    "messageType": "NOTIFICATION"
}'
```

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "success",
		"isSuccessful" : true
	}
}
```

### Delete
#### Delete Scheduled Messages 
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/reservations?reservationIds={reservationId,}
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

##### cURL
```
curl -X DELETE \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/reservations?reservationIds='"${RESERVATION_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```


##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "success",
		"isSuccessful" : true
	}
}
```

## Tags

### Create
#### Create Tags
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/tags
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

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "tagName" :  "서른"
}'
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "success"
    },
    "tag" : {
        "tagId" :  "12345678"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| tagId | Required, String | Created tag ID, length is 8 |

##### Description
- You can create up to 2,048 tags.

#### Append UIDs to Tag
- Append UID to a tag: adding existing UIDs ends up with more tags of UID 
- The maximum number of tags for a UID is 16.

##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/tags/{tag-id}/uids
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

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}"'/uids' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "uids" : [
         "uid-01",
         "uid-02"
    ]
}'
```

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}"'/uids' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "uids" : [
         "uid-01",
         "uid-02"
    ]
}'
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "success"
    }
}
```


#### Set Tag List on Uid

- By replacing tags of UID, existing tags are deleted and replaced by new tags. 

##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/uids
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

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "uid" :  "uid-01",
    "tagIds" : [
         "12345678",
         "23456789"
    ]
}'
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "success"
    }
}
```

### Query
#### List Tags
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/tags?tagName={tagName}
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

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "success"
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

#### Get Tags
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "success"
    },
    "tag" : {
        "tagId" :  "12345678",
        "tagName" :  "thirty",
        "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
        "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
    }
}
```

#### List UIDs of Tag
- List tagged UIDs.

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/tags/{tag-id}/uids?offsetUid={uid}&limit={limit}
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

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "success"
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
                    "contactType" :  "TOKEN_FCM",
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
| contactType | -, String | Token Type: 'TOKEN_FCM', 'TOKEN_APNS', 'TOKEN_APNS_SANDBOX', 'TOKEN_TENCENT', 'TOKEN_ADM' |
| contact | -, String | Token |
| createdDateTime | Required, Date Time String | Date and time of creation (ISO 8601) |

#### Query UIDs
- Query UID.
- Token is registered, along with contact.

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/uids/{uid}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids/'"${USER_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
"header" : {
"isSuccessful" :  true,
"resultCode" :  0,
"resultMessage" :  "success"
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
                "contactType" :  "TOKEN_FCM",
                "contact" :  "token",
                "createdDateTime" :  "2017-07-07T07:07:07.777+09:00"
            }
        ]
    }
}
```

### Modify
#### Modify Tags
##### Method, URL, Headers
```
PUT /push/v2.4/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```json
{
    "tagName" :  "30s"
}
```

##### cURL
```
curl -X PUT \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "tagName": "삼십대"
}'
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "success"
    }
}
```

### Delete
#### Delete Tags
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
N/A
```

##### cURL
```
curl -X DELETE \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "success"
    }
}
```


#### Delete UIDs
- UIDs are deleted, along with contact and token. 

##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/uids?uids={uid,}
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

##### cURL
```
curl -X DELETE \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids?uids='"${USER_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "success"
    }
}
```

#### Delete UIDs of Tag
- Delete tag-UID relation only. 
- Contact or token is not deleted. 

##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/tags/{tagId}/uids?uids={uid,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
N/A
```

##### cURL
```
curl -X DELETE \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tags/'"${TAG_ID}"'/uids?uids='"${USER_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "success"
    }
}
```

## UIDs

### Create

#### Add Tags
- Add tags to UID with tag ID. 
- No secret key is required: call is available from app. 

##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```json
{
    "tagIds": ["TAG_ID01"]
}
```

##### cURL
```
curl -X POST \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids/'"${USER_ID}"'/tag-ids' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
    "tagIds": ["12345678"]
}'
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "success"
    }
}
```


### Query

#### Query Tag IDs of UID
- Query tag IDs of UID. 
- No secret key is required: call is available from app.
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids/'"${USER_ID}"'/tag-ids' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "success"
    },
    "tagIds": ["TAG_ID01"]
}
```


### Modify 
#### Modify Tags of UID
- Modify tags of UID with tag ID. 
- No secret key is required: call is available from app.
##### Method, URL, Headers
```
PUT /push/v2.4/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```json
{
    "tagIds": ["TAG_ID02"]
}
```

##### cURL
```
curl -X PUT \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids/'"${USER_ID}"'/tag-ids' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"'' \
-d '{
	"tagIds": ["12345678"]
}'
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" : "success"
    }
}
```


### Delete Tags 
- Query tag IDs of UID. 
- No secret key is required: call is available from app.
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/uids/{uid}/tag-ids?tagIds={tagId,}
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```
N/A
```

##### cURL
```
curl -X DELETE \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/uids/'"${USER_ID}"'/tag-ids?tagIds='"${TAG_ID}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" : "success"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| tagIds | Required, String Array | Query String, tag ID to delete, delimited by comma (,) |

<span id="stats-api"></span>
## Statistics
### Query Statistics
- You can query statistics by statistics event key. 

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/stats?eventCategory={eventCategory}&statisticsType={statisticsType}&timeUnit={timeUnit}&from={from}&to={to}&extra1s={extra1,}&messageId={messageId}&statsIds={statsId,}
Content-Type: application/json;charset=UTF-8
```
| Field | Usage | Description |
| - | - | - |
| eventCategory | Required, String | Event category. MESSAGE, TOKEN_REGISTRATION, TOKEN_LANGUAGE, TOKEN_COUNTRY, TOKEN_AGREEMENT |
| statisticsType | Optional, String | Representation type of searched statistical data. NORMAL(default), MINUTELY, HOURLY, DAILY, BY_DAY |
| timeUnit | Optional, String | Time unit of statistical data. Default value depends on query period, MINUTES, HOURS, DAYS |
| from | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| extra1s | Optional, String Array | When the eventCategory is MESSAGE, filter by push type is available. FCM, APNS, APNS_SANDBOX, APNS_VOIP, APNS_SANDBOXVOIP, ADM, TENCENT |
| messageId | Optional, String | Message ID |
| statsIds | Optional, String Array | Statistics Event Key ID |
| statsCriteria	| Optional, String Array | Statistics criteria for summing up. If not set, the total is calculated based on the default value. EVENT(the default), EXTRA_1, EXTRA_2, EXTRA_3, TEMPLATE_ID |

##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/stats?eventCategory='"${EVENT_CATEGORY}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```
{
	"header": {
		"resultCode": 0,
		"resultMessage": "success",
		"isSuccessful": true
	},
	"stats": [{
			"eventDateTime": "2020-08-12T00:00:00.000+09:00",
			"events": {
				"RECEIVED": 0,
				"SENT_FAILED": 0,
				"SENT": 0,
				"OPENED": 0
			}
		}
	]
}
```

### Query Statistics Total
- A total API has been added to sum up the retrieved statistic data.

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/stats?eventCategory={eventCategory}&statisticsType={statisticsType}&timeUnit={timeUnit}&from={from}&to={to}&extra1s={extra1,}&messageId={messageId}&statsIds={statsId,}&statsCriteria={statsCriterion,}
Content-Type: application/json;charset=UTF-8
```
| Field | Usage | Description |
| - | - | - |
| eventCategory | Required, String | Event category. MESSAGE, TOKEN_REGISTRATION, TOKEN_LANGUAGE, TOKEN_COUNTRY, TOKEN_AGREEMENT |
| statisticsType | Optional, String | Representation type of searched statistical data. NORMAL(default), MINUTELY, HOURLY, DAILY, BY_DAY |
| timeUnit | Optional, String | Time unit of statistical data. Default value depends on query period, MINUTES, HOURS, DAYS |
| from | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | Up to the latest 30 days (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| extra1s | Optional, String Array | When the eventCategory is MESSAGE, filter by push type is available. FCM, APNS, APNS_SANDBOX, APNS_VOIP, APNS_SANDBOXVOIP, ADM, TENCENT |
| messageId | Optional, String | Message ID |
| statsIds | Optional, String Array | Statistics Event Key ID |
| statsCriteria	| Optional, String Array | Statistics criteria for summing up. If not set, the total is calculated based on the default value. EVENT(the default), EXTRA_1, EXTRA_2, EXTRA_3, TEMPLATE_ID |

##### Request Body
```
N/A
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/stats/total?eventCategory='"${EVENT_CATEGORY}" \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body
```
{
	"header": {
		"resultCode": 0,
		"resultMessage": "success",
		"isSuccessful": true
	},
	"total" : {
        "SENT" : 120,
        "SENT_FAILED" : 50,
        "SENT": 0,
        "OPENED": 0
    }
}
```


* *Document Updates*
    * *(March 24, 2020) Added Statistics API*
    * *(2020.12.29) Added Statistics Total API*