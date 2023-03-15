## Notification > Push > API v2.4 Guide
### v2.4 API 소개

#### 추가
- '통계' API가 추가되었습니다.

### 기본 정보
#### Endpoint
```
API Endpoint: https://api-push.cloud.toast.com
메시지 수신/확인 여부 수집 Endpoint: https://collector-push.cloud.toast.com
```
### Secret Key
- 콘솔에서 확인 가능합니다.
- Secret Key가 필요한 API를 호출할 때, 해더에 아래와 같이 설정해서 호출해야 합니다.
```
Header
X-Secret-Key: [a-zA-Z0-9]{8}
```
**CONSOLE > Notification > Push > URL & AppKey** 에서 확인/생성할 수 있습니다.

#### Response

##### Response HTTP Status Code
200 OK.  
모든 API 요청에 대해 200 OK로 응답합니다.  
자세한 응답 결과는 Response Body의 Header에서 알 수 있습니다.

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

## 토큰
### 생성
- 클라이언트에서 조회 가능합니다.

##### Method, URL
```
POST /push/v2.4/appkeys/{appkey}/tokens
Content-Type: application/json;charset=UTF-8
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |

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
|token|	Required, String|	토큰, 최대 1,600 글자, 한글 사용 불가 |
|oldToken|	Optional, String|	기존 토큰, 최대 1,600 글자 |
|pushType|	Required, String| 'FCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
|isNotificationAgreement|	Required, Boolean|	true or false|
|isAdAgreement|	Required, Boolean|	true or false|
|isNightAdAgreement| Required, Boolean|	true or false|
|timezoneId|	Required, String|	Area/Name. IANA time zone database.|
|country|	Required, String|	ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3 글자|
|language|	Required, String|	ISO 639-1, ISO 639-2, iOS(language code + script code), 8 글자|
|uid|	Required, String|	사용자 아이디, emoji 허용 안함, 최대 64 글자|
|deviceId|	Required, String|	디바이스 아이디, 최대 36 글자|

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

- 토큰이 이미 등록되어 있는 경우에 다시 등록하면, 기존 정보를 업데이트 합니다.
- 만약, 토큰이 변경된다면 oldToken에 기존 토큰을, token에 새로운 토큰을 설정하고 등록하면 새로운 토큰으로 업데이트됩니다.
- "isNotificationAgreement"는 푸시 메시지 수신 동의 여부, "isAdAgreement"는 광고성 푸시 메시지 수신 여부, "isNightAdAgreement"는 야간 광고성 푸시 메시지 수신 여부를 나타냅니다.
- 예로, 모든 푸시 메시지 수신을 원할 경우, 필드 3개를 모두 true로 설정하면 됩니다. 푸시 메시지만 수신할 경우, "isNotificationAgreement"만 true로 설정하면 됩니다.
- 수신 동의 여부는 정보통신망법 규정(제50조부터 제50조의 8)을 따른다.  
    - [KISA 가이드 바로 가기](https://spam.kisa.or.kr/spam/na/ntt/selectNttInfo.do?mi=1020&nttSn=1171&bbsId=1002)    
    - [법령 바로 가기](http://www.law.go.kr/lsEfInfoP.do?lsiSeq=123210#)  
- 네트워크 상태가 좋지 않거나 여러 이유로 응답 지연이 발생할 수 있습니다. 모바일 애플리케이션 구동에 영향을 최소화하기 위해 Timeout을 짧게 설정하고, 구동될 때마다 토큰을 등록하는 것이 좋습니다.
- 토큰은 보안적인 이슈, 앱 업데이트, 삭제 등 여러가지 이유로 재발급될 수 있습니다. 자주 변경되는 것은 아니지만, 수신율을 높이기 위해 구동될 때 마다 최신 토큰을 등록하는 것이 좋습니다.
- 앱 삭제 등으로 토큰이 만료되어도 바로 FCM, APNS 서버에 적용되지 않아, 앱 삭제 후 푸시 메시지를 발송했을 때 발송이 성공할 수 있습니다.

### 조회

#### 토큰 목록 조회
##### Method, URL

```
GET /push/v2.4/appkeys/{appkey}/tokens-by-cursor?cursorUid={cursorUid}&cursorToken={cursorToken}&limit={limit}
Content-Type: application/json;charset=UTF-8
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용 시 발급 받은 앱키 |
| cursorUid | Optional, String | 사용자의 UID 커서. 페이지 이동 시 필수 |
| cursorToken | Optional, String | 사용자의 토큰 커서. 페이지 이동 시 필수 |
| limit | Optional, Number | 한 번에 조회할 목록 크기, 기본값과 최댓값은 1,000 |

##### Request Body
```
없음
```

##### cURL
```
curl -X GET \
'https://api-push.cloud.toast.com/push/v2.4/appkeys/'"${APP_KEY}"'/tokens-by-cursor' \
-H 'Content-Type: application/json;charset=UTF-8' \
-H 'X-Secret-Key: '"${SECRET_KEY}"''
```

##### Response Body

```
{
    "tokens" : [{
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
    }],
    "header" : {
        "isSuccessful" : true,
        "resultCode" : 0,
        "resultMessage" : "success"
    }
}
```

##### Description
- 페이지 이동 시 "cursorUid", "cursorToken" 모두 필수입니다. 설정한 "cursorUid", "cursorToken" 다음 순서부터 조회합니다.



#### 토큰으로 토큰 조회
- 클라이언트에서 조회 가능합니다.
##### Method, URL

```
GET /push/v2.4/appkeys/{appkey}/tokens/{token}?pushType={pushType}
Content-Type: application/json;charset=UTF-8
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용 시 발급받은 앱키 |
| pushType | Required, String | 'FCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |

##### Request Body
```
없음
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
| updatedDateTime | -, DateTime String | 토큰 업데이트 일시 |
| adAgreementDateTime | -, DateTime String | 홍보성 푸시 메시지 수신 동의 일시 |
| nightAdAgreementDateTime | -, DateTime String | 야간 홍보성 푸시 메시지 수신 동의 일시 |
| deviceId | -, String | 디바이스 아이디 |
| activatedDateTime | -, Datetime String | 토큰의 최근 등록 요청 일시 |

#### 사용자 아이디로 토큰 조회
- Secret Key가 필요한 API이며, 서버에서 호출되어야 합니다.
##### Method, URL

```
GET /push/v2.4/appkeys/{appkey}/tokens?uid={uid}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| uid | Required, String | 조회할 사용자 아이디 |

##### Request Body
```
없음
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


#### 유효하지 않는 토큰 조회
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/invalid-tokens?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&messageId={messageId}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| pageIndex | Optional, Number | 기본값 0 |
| pageSize | Optional, Number | 기본값 25, 최댓값 100 |
| from | Optional, DateTime String | 최근 30일까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | 최근 30일까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| messageId | Optional, Number | 유효하지 않는 토큰이 발생한 메시지 아이디 |

##### Request Body
```
없음
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

### 삭제
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/tokens/{token}?pushType={pushType}
Content-Type: application/json;charset=UTF-8
```
| Field | Usage | Description |
| - | - | - |
| token | Required, String | 기기의 토큰 |
| pushType | Optional, String | 토큰의 푸시 타입, 값이 없으면 모든 푸시 타입에 해당하는 토큰을 삭제 |

##### Request Body
```
없음
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

## 메시지
### 발송
※ API로 발송한 푸시 메시지는 콘솔과 단건, 목록 조회 API에서 조회할 수 없습니다. API로 발송한 푸시 메시지는 Logging 기능을 활성화한 후 로그 조회 API를 이용해 주세요.
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
    "removeGuide": "매뉴 > 설정",
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
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| target.type | Required, String | 'ALL', 'UID', 'TAG' 수신 타겟 타입 |
| target.to | Optional, String Array | target.type이 수신자 UID 목록(최대 10,000 개) 또는 TAG 조건 |
| target.pushTypes | Optional, String Array | 'FCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
| target.countries | Optional, String Array | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3 (최대 3 글자) |
| content | Required, Map | 수신자에게 전달될 내용 (최대 8,192 글자) |
| content.default | Required, Map | '자세한 내용은 아래 공통 메시지 형식' 참고 |
| content.default.title | Optional, String |  |
| content.default.body | Optional, String |  |
| content.default.notification | Optional, Object | FCM에서 사용하는 notification 필드 |
| content.default.style.useHtmlStyle | Optional, Boolean | 'true'로 설정하면 iOS에서 HTML이 제거된 메시지가 표시됩니다. |
| messageType | Required, String | NOTIFICATION, AD |
| contact | Optional, String | messageType이 AD이면 경우 필수, 숫자(0-9)와 하이픈(Hypen, -)만 가능합니다. |
| removeGuide | Optional, String | messageType이 AD이면 경우 필수 |
| timeToLiveMinute | Optional, Number | 단위는 분입니다. 범위는 1에서 60까지다. 기본값은 10 입니다. |
| provisionedResourceId | Optional, String | 할당 받은 전용 리소스(provisioned Resource) 아이디입니다. 사용 문의 support@cloud.toast.com |
| adWordPosition | Optional, String | 'TITLE', 'BODY' 광고 표시 문구 위치. 기본값은 'TITLE' 입니다. |
| statsId | Optional, String | 통계 이벤트 키입니다. 통계 조회 시 키를 기준으로 검색할 수 있습니다. |

##### Description
- "target.type"에 'UID'로 설정시 "target.to"에 최대 10,000 개까지 UID를 설정할 수 있습니다.
- "target.type"에 'TAG'로 설정시 "target.to"에 태그 아이디와 3개의 조건과 1개의 괄호('()')를 넣은 조건을 설정할 수 있습니다.
    - 예, 남자, 30대 태그가 붙었거나 여자 태그가 붙은 대상에게 메시지를 발송한다면,    
    "target.to=(,남자_ID,AND,30대_ID,),OR,여자_ID"로 설정할 수 있습니다.
- "target.pushTypes" 필드로 특정 푸시 타입으로만 메시지를 발송할 수 있습니다.
만약, 정의하지 않으면 모든 푸시 타입, FCM, APNS, APNS_SANDBOX, TENCENT, ADM로 발송합니다.
- "target.countries" 필드가 "['KR', 'JP']"면 토큰 국가 코드가 "KR" 또는 "JP"인 Token에 발송합니다.
- "content.default" 필드는 필수이며, "content" 필드에 대한 자세한 내용은 아래 [공통 메시지 포맷]을 참고 바랍니다.
- 메시지를 광고 타입, "messageType": "AD", 으로 보낼 경우, "contact", "removeGuide" 필드를 반드시 포함해야 합니다.
"contact" 필드에 연락처를 입력해야 하며, "removeGuide" 필드에 수신 철회 방법에 대해 입력해야 합니다.
- timeToLiveMinute 필드를 설정하면, 설정한 시간 이상 발송이 지연되는 경우 자동으로 실패 처리됩니다.

### 공통 메시지
"content"에 아래 표대로 메시지를 작성하면, 각 푸시 타입에 맞게 메시지가 생성되어 발송됩니다.

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
|badge|	iOS|	Optional, Number| data.badge | aps.badge | custom_content.badge | data.badge |
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

Reserved Word는 메시지 생성 시 Platform별로 알맞는 위치에 설정됩니다. 사용자가 임의로 데이터 타입과 위치 등을 변경할 수 없습니다.
그 외 사용자가 정의한 Word는 다음과 같이 Custom Key/Value 필드에 들어갑니다.

|Word|	Platform|	Usage|	FCM|	APNS|	TENCENT| ADM|
|---|---|---|---|---|---|---|
|customKey|	Android, <br/> iOS, <br/> Tencent|	Optional, <br/> Object, <br/> Array, <br/> String, <br/> Number|	data.customKey|	customKey|	custom_content.customKey| data.customKey|

### 메시지 발송 예제

- 메시지 발송 API의 요청 본문(Request Body)의 content.default은 필수입니다.

#### 1. 전체에게 발송
등록된 모든 대상에게 메시지를 발송하는 예제입니다.

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
- target.type을 'ALL'로 설정하면, 모든 토큰에 메시지를 발송합니다.

#### 2. 특정 사용자에게 발송
사용자 아이디를 입력해 특정 사용자에게 메시지를 발송하는 예제입니다.

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
- target.type을 'UID'로 설정하고, target.to에 사용자 아이디를 설정해 특정 사용자에게 메시지를 발송합니다.

#### 3. 일부 국가나 푸시 타입의 사용자들에게 발송
특정 국가나 기기(Android, iOS, ...)를 사용하는 사용자들에게만 메시지를 발송하는 예제입니다.

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
- target.countries에 국가 코드, target.pushTypes에 푸시 타입을 설정해 조건에 만족하는 사용자에게 메시지를 발송합니다.

#### 4. 푸시 타입별 메시지 변환
메시지를 보내게되면 푸시 타입별로 메시지가 변환되어 발송되는데, 변환되는 규칙을 설명한 예제입니다.

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

##### FCM(Android)에 수신되는 메시지
```json
{
	"data": {
		"title": "title",
		"body": "body",
		"customKey": "value"
	}
}
```
##### APNS(iOS)에 수신되는 메시지
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
##### TENCENT(Android)에 수신되는 메시지
```json
 {
    "title": "title",
    "body": "body",
    "custom_content": {
        "customKey": "value"
    }
}
```

##### ADM(Fire OS)에 수신되는 메시지
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
- content에 입력한 메시지 내용은 각 푸시 타입에 맞게 변환되어 발송됩니다.
- title, body와 같은 예약어들은 푸시 타입에 맞는 메시지로 변환시 지정된 위치에 설정되어 발송됩니다.
그외 사용자가 정의한 필드들은 각 푸시 타입의 Custom Key 위치에 설정됩니다.
- badge, consolidationKey와 같이 특정 푸시 타입에만 정의된 예약어는 다른 푸시 타입에서는 제외됩니다.
예로, badge는 APNS(iOS) 메시지에만 설정되며, FCM, TENCENT, ADM에는 제외됩니다.

#### 5. 광고성 메시지
광고성 메시지로 발송하면 메시지 내용에 추가되는 광고 문구에대한 예제입니다.

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title": "금요일 특별 이벤트",
            "body": "지금 주문하시면 50% 할안된 가격으로!"
        }
    },
    "messageType" : "AD",
    "contact": "1588",
    "removeGuide": "메뉴 > 알림 설정"
}
```

##### FCM(Android), ko(한국어)에 수신되는 메시지
```json
 {
    "data": {
        "title": "(광고) 금요일 특별 이벤트 1588",
        "body": "지금 주문하시면 50% 할안된 가격으로!\n메뉴 > 알림 설정"
    }
}
```
##### APNS(iOS), ko(한국어)에 수신되는 메시지
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
##### FCM(Android), ja(일본어)에 수신되는 메시지
```json
 {
    "data": {
        "title": "금요일 특별 이벤트",
        "body": "지금 주문하시면 50% 할안된 가격으로!"
    }
}
```
##### APNS(iOS), ja(일본어)에 수신되는 메시지
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
- 광고성 메시지를 발송하기 위해서는 messageType을 AD(광고)로 설정하고, contact와 removeGuide에 대표 번호와 수신 동의 철회 방법을 입력해야 됩니다.
- 각 푸시 타입별로 메시지가 발송될때, title에 광고 표시 문구와 대표 번호가, body에 수신 동의 철회 방법이 추가되어 발송됩니다.
- 광고성 메시지는 언어 코드가 한국어(ko, ko-)인 사용자들에게만 광고 문구가 추가됩니다. 위 예처럼 해외 사용자(일본어)들에게는 광고 문구가 추가되지 않습니다.

#### 6. 다국어 메시지
다양한 언어로 메시지를 발송하는 예제입니다.

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
            "title": "제목",
            "body": "내용",
            "customKey": "'ko', 'ko-'로 시작하는 언어 코드에 설정됩니다."
        },
        "ja" : {
            "title": "タイトル",
            "body": "プッシュ・メッセージ"
        }
    },
    "messageType" : "NOTIFICATION"
}
```

##### FCM(Android), ko(한국어)에 수신되는 메시지
```json
{
    "data": {
        "title": "제목",
        "body": "내용",
        "customKey": "'ko', 'ko-'로 시작하는 언어 코드에 설정됩니다."
    }
}
```
##### FCM(Android), ko-KR(한국어)에 수신되는 메시지
```json
{
    "data": {
        "title": "제목",
        "body": "내용",
        "customKey": "'ko', 'ko-'로 시작하는 언어 코드에 설정됩니다."
    }
}
```
##### FCM(Android), ja(일본어)에 수신되는 메시지
```json
{
    "data": {
        "title": "タイトル",
        "body": "プッシュ・メッセージ",
        "customKey": "value"
    }
}
```
##### FCM(Android), en(영어)에 수신되는 메시지
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
- content 하위에 각 언어 코드에 대한 메시지를 입력하면, 토큰의 언어 코드와 일치하거나 유사한 언어의 메시지로 변환되어 발송됩니다.
토큰의 언어 코드와 매칭되는 언어 코드가 없다면, default의 내용이 발송됩니다. 언어 코드가 en(영어)인 사용자에게는 conent.default의 내용이 발송됩니다.
- 토큰의 언어 코드와 완벽히 일치하지 않아도, 언어 코드의 유사도를 비교해 최대한 가까운 언어로 변환합니다.
요청 본문에 content.ko만 입력되어 있지만, 언어 코드가 ko-KR(한국어)인 사용자에게도 content.ko의 내용이 발송됩니다.
- customKey는 content.ja에 정의되어 있지 않기 때문에, content.default의 값으로 발송됩니다. 공통적인 내용은 content.default에 입력할 수 있습니다.

#### 7. 리치 메시지
메시지 발송시 'content'에 'richMessage' 필드를 정의하면 리치 메시지로 메시지를 발송할 수 있습니다.
공통 메시지, 광고성 메시지, 다국어 메시지와 함께 사용할 수 있습니다.
v1.7이상 SDK가 적용된 곳에서만 사용할 수 있습니다.

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
                        "name" : "버튼 이름",
                        "buttonType" : "REPLY | DEEP_LINK | OPEN_APP | OPEN_URL | DISMISS",
                        "link" : "URL | ...",
                        "hint" : "메시지를 입력해주세요."
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
                    "description" : "알림 모음"
                }
            }
        }
    },
    "messageType" : "NOTIFICATION"
}
```

| Field | Usage | Description |
| - | - | - |
| richMessage | Optional, Object | 리치 메시지 사용시 필요 |
| richMessage.buttons | Optional, Object Array | 리치 메시지에 추가되는 버튼, 최대 3개까지 가능 |
| richMessage.button.name | Required, String | 버튼 이름 |
| richMessage.button.buttonType | Required, String | 버튼 타입, REPLY, DEEP_LINK, OPEN_APP, OPEN_URL, DISMISS |
| richMessage.button.link | Required, String | 버튼을 눌렀을때, 연결되는 링크 |
| richMessage.button.hint | Required, String | 버튼에대한 힌트 |
| richMessage.media | Optional, Object | 리치 메시지에 추가되는 미디어 |
| richMessage.media.sourceType | Optional, String | 미디어의 위치, REMOTE, LOCAL |
| richMessage.media.source | Required, String | 미디어의 위치한 곳의 주소 |
| richMessage.media.mediaType | Optional, String | 미디어의 타입, IMAGE, GIF, VEDIO, AUDIO. Android에서는 IMAGE만 지원 |
| richMessage.media.extension | Required, String | 미디어 파일의 확장자 |
| richMessage.media.expandable | Required, Boolean | Android에서 미디어를 클릭 시 펼침 기능 사용 여부 |
| richMessage.androidMedia | Optional, Object | Android 기기에 사용되는 미디어. 형식은 media와 동일 |
| richMessage.iosMedia | Optional, Object | iOS 기기에 사용되는 미디어. 형식은 media와 동일 |
| richMessage.largeIcon | Optional, Object | 리치 메시지에 추가되는 큰 아이콘, Android에서만 지원 |
| richMessage.largeIcon.sourceType | Optional, String | 큰 아이콘의 위치, REMOTE, LOCAL |
| richMessage.largeIcon.source | Required, String | 미디어의 위치한 곳의 주소 |
| richMessage.group | Optional, Object | 여러 개의 메시지를 그룹 단위로 묶는 기능, Android에서만 지원 |
| richMessage.group.key | Required, String | 그룹의 키 |
| richMessage.group | Required, String | 그룹에대한 설명 |

#### 8. FCM notification 필드를 사용한 메시지
메시지 발송시 'content'에 'notification' 필드를 정의하면 FCM 형식에 맞게 메시지를 발송할 수 있습니다.

##### Request Body
```
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "notification" : {
                "title" : "제목",
                "body" : "내용",
                "android_channel_id": "안드로이드 채널",
                "sound" : "소리",
                "click_action" : "메시지 클릭시 액션",
                "title_loc_key" : "문자열 키",
                "title_loc_args" : [
                    "문자열"
                ],
                "body_loc_key" : "문자열 키",
                "body_loc_args" : [
                    "문자열"
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
| notification.title | Android, iOS | Optiontal, String | notification.title | notification.title을 설정하면 data.title은 무시됩니다. |
| notification.body | Android, iOS | Optiontal, String | notification.body | notification.body 설정하면 data.body 무시됩니다. |
| notification.sound | Android, iOS | Optiontal, String | notification.sound | 메시지 수신시 재생되는 알림음입니다. Android Oreo (8.0) 이상에서 동작하지 않습니다. |
| notification.clickAction | Android, iOS | Optiontal, String | notification.click_action | 메시지 클릭시 이동할 Activity 입니다. |
| notification.titleLocKey | Android, iOS | Optiontal, String | notification.title_loc_key | 문자열 리소스 입니다. 메시지를 현지화하는데 사용할 수 있습니다. |
| notification.titleLocArgs | Android, iOS | Optiontal, String | notification.title_loc_args | 문자열에 치환자에 들어갈 문자열 입니다. 메시지를 현지화하는데 사용할 수 있습니다. |
| notification.bodyLocKey | Android, iOS | Optiontal, String | notification.body_loc_key | 문자열 리소스 입니다. 메시지를 현지화하는데 사용할 수 있습니다. |
| notification.bodyLocArgs | Android, iOS | Optiontal, String Array | notification_body_loc_args | 문자열에 치환자에 들어갈 문자열 입니다. 메시지를 현지화하는데 사용할 수 있습니다. |
| notification.icon | Android, iOS | Optiontal, String | notification.icon | 알림 아이콘입니다. |
| notification.color | Android, iOS | Optiontal, String | notification.color | 알림 아이콘 색상입니다. |
| notification.tag | Android, iOS | Optiontal, String | notification.tag | 동일한 태그인 메시지가 수신되면 기존 메시지를 대체합니다. |
| notification.badge | Android, iOS | Optiontal, Number | notification.badge | 확인하지 않은 새로운 알림 개수입니다. |

##### FCM(Android)에 수신되는 메시지
```json
{
    "registration_ids" : [
        "토큰"
    ],
    "notification" : {
        "title" : "제목",
        "body" : "내용",
        "android_channel_id": "안드로이드 채널",
        "sound" : "소리",
        "click_action" : "메시지 클릭시 액션",
        "title_loc_key" : "문자열 키",
        "title_loc_args" : [
            "문자열"
        ],
        "body_loc_key" : "문자열 키",
        "body_loc_args" : [
            "문자열"
        ],
        "icon" : "notification_off",
        "color" : "#4286f4",
        "tag" : "tag"
    }
}
```



### 조회
#### 목록 조회
※ 콘솔로 발송한 푸시 메시지만 목록 조회 API로 조회할 수 있습니다. API로 발송한 푸시 메시지는 Logging 기능을 활성화한 후 로그 조회 API를 이용해 주세요.
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/messages?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&deliveryType={deliveryType}&messageStatus={messageStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| pageIndex | Optional, Number | 기본값 0 |
| pageSize | Optional, Number | 기본값 25, 최댓값 100 |
| from | Optional, DateTime String | 최근 30일까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | 최근 30일까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| deliveryType | Optional, String | 'INSTANT'(즉시 발송), 'RESERVATION'(예약 발송) |
| messageStatus | Optional, String | 'READY', 'PROCESSING', 'COMPLETE', 'CANCEL_NO_TARGET', 'CANCEL_INVALID_CERTIFICATE', 'CANCEL_INVALID_MESSAGE', 'CANCEL_UNSUPPORTED_MESSAGE_TYPE', 'CANCEL_UNAUTHORIZED', 'CANCEL_UNKNOWN' |

##### Request Body
```
없음
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
        "removeGuide": "매뉴 > 설정",
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

| Field | Usage | Description |
| - | - | - |
| createdDateTime | - | 메시지가 생성된 일시 (ISO 8601) |
| completedDateTime | - | 메시지 발송이 완료된 일시 (ISO 8601) |
| targetCount | - | 발송될 타겟 토큰 수 |
| sentCount | - | 실제 발송된 토큰 수 |
| provisionedResourceId | - | 메시지가 발송된 전용 리소스 아이디 |
| totalCount | - | 필터링된 전체 메시지  수 |

- "messageStatus" 필드는 메시지 상태를 나타냅니다. 다음과 같은 상태가 있습니다.
    - READY: 메시지 발송 요청이 등록된 상태입니다.
    - PROCESSING: 메시지 생성이 끝나고, 대기 또는 발송 중입니다.
    - COMPLETE: 메시지 발송이 완료된 상태입니다.
    - CANCEL_NO_TARGET: 메시지 발송 대상이 없어서 취소된 상태입니다. 다음과 같은 이유로 발송이 취소될 수 있습니다.  
 등록된 토큰이 없을 때  
 광고 푸시 메시지의 경우, 수신 동의한 사용자가 없을 때  
 야간 광고 푸시 메시지(21시 ~ 8시)의 경우, 야간 광고 수신 동의한 사용자가 없을 때  
 기존 등록된 토큰들이 삭제되어 토큰이 없을 때    
    - CANCEL_INVALID_CERTIFICATE: 인증서가 잘못되어 취소된 상태입니다. 인증서 상태를 확인해야 합니다.
    - CANCEL_INVALID_MESSAGE: 메시지 형식이 맞지 않아 취소된 상태입니다.
    - CANCEL_UNSUPPORTED_MESSAGE_TYPE: 메시지 형식이 맞지 않아 취소된 상태입니다.
    - CANCEL_UNAUTHORIZED: 인증서 인증 과정에서 실패한 상태입니다. 인증서 상태를 확인해야 합니다.
    - CANCEL_UNKNOWN: 내부 오류가 발생한 상태입니다.

#### 단건 조회
※ 콘솔로 발송한 푸시 메시지만 단건 조회 API로 조회할 수 있습니다. API로 발송한 푸시 메시지는 Logging 기능을 활성화한 후 로그 조회 API를 이용해 주세요.
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/messages/{message-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| messageId | Required, Number | 메시지 아이디 |

##### Request Body
```
없음
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
        "removeGuide": "매뉴 > 설정",
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

#### 실패한 메시지 목록 조회
발송에 실패한 메시지를 조회할 수 있습니다.
단, 토큰이 존재하지 않는 경우(INVALID_TOKEN)는 발송 실패로 판단하지 않습니다.

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/message-errors?messageId={messageId}&messageErrorType={messageErrorType}&messagErrorCause={messageErrorCause}&from={from}&to={to}&limit={limit}
HEADER
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| messageId | Optional, Number | 메시지 아이디 |
| messageErrorType | Optional, String | 'CLIENT_ERROR', 'EXTERNAL_ERROR', 'INTERNAL_ERROR' |
| messageErrorCause | Optional, String | 'UNSUPPORTED_MESSAGE_TYPE', 'INVALID_MESSAGE', 'INVALID_CERTIFICATE', 'UNAUTHORIZED', 'EXPIRED_TIME_OUT', 'APNS_ERROR', 'FCM_ERROR', 'TENCENT_ERROR', 'AGENT_ERROR', 'ADM_ERROR', 'DUPLICATED_MESSAGE_TOKEN'  |
| from | Optional, DateTime String | 최근 30일까지, 기본값은 최근 7일 전(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일까지, 기본값은 현재(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| limit | Optional, Number | 한 번에 조회할 목록 크기, 기본값과 최댓값은 100 |
| pageNumber | Optional, Number | 페이지 번호, 기본값 1 |

##### Description
- messageErrorType와 messageErrorCause는 다음과 같은 뜻을 의미합니다.
    - CLIENT_ERROR: 클라이언트의 잘못된 요청
        - UNSUPPORTED_MESSAGE_TYPE: 지원하지 않는 메시지 타입
        - INVALID_MESSAGE: 비정상적인 메시지
        - INVALID_CERTIFICATE: 인증서 만료 또는 인증서 정보가 올바르지 않음
        - UNAUTHORIZED: 인증서 만료 또는 인증서 정보가 올바르지 않음
        - DUPLICATED_MESSAGE_TOKEN: 해당 토큰으로 동일한 메시지가 중복 발송 요청되어 실패
    - EXTERNAL_ERROR: APNS, FCM, Tencent, ADM 등 푸시와 연결된 외부 서비스 오류
        - APNS_ERROR: APNS(iOS)로 발송 실패
        - FCM_ERROR: FCM(Google)로 발송 실패
        - TENCENT_ERROR: Tencent로 발송 실패
        - ADM_ERROR: ADM로 발송 실패
    - INTERNAL_ERROR: 푸시 내부에서 발생한 오류
        - EXPIRED_TIME_OUT: 발송 지연으로 인한 메시지 유효 시간 만료
        - AGENT_ERROR: Agent 내부 오류로 인한 발송 실패
- Response Body에서 header.resultCode가 40010인 경우, 조회 기간(from, to)을 줄여서 조회해야 합니다.

##### Request Body
```
없음
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
| messageId | - | 실패한 메시지 아이디 |
| messageIdString | - | 실패한 메시지 아이디 |
| pushType | - | 'FCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
| payload | - | 기기에 발송된 실제 메시지 내용 |
| tokens | - | 발송한 실패한 수신자의 uid와 token |

### 로그 조회
- 로그 조회 API는 Logging 기능을 활성화한 상태에서만 호출가능 하다.
- Logging 기능은 **Console > Notification > Push > Setting** 탭에서 활성화 시킬 수 있습니다.

#### 일반 로그 조회
- 최대 100개까지 조회 가능합니다.

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/logs/message?messageId={messageId}&uid={uid}&token={token}&pushType={pusyType}&from={from}&to={to}&limit={limit}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| messageId | Optional, Number | 메시지 아이디 |
| uid | Optional, String | 사용자 아이디 |
| token | Optional, String | 사용자 토큰 |
| pushType | Optional, String | 푸시 타입 |
| from | Optional, DateTime String | 최근 30일까지(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일까지(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| limit | Optional, Number | 최대 조회 개수, 기본값 100 |
| pageNumber | Optional, Number | 페이지 번호, 기본값 1 |

##### Request Body
```
없음
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

#### 대량 로그 카운트 조회
- 검색 조건으로 검색된 로그의 수를 확인할 수 있습니다.
- 다른 v2.4 API와 다르게 'User Access Key ID'를 사용해야 합니다.
**회원정보 > API 보안 설정** 에서 생성할 수 있습니다.

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appKey}/bulk-logs/message/count?from={from}&to={to}&messageId={messageId}&pushType={pushType}&sendResult={sendReesult}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

##### Request Body
```
없음
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


#### 대량 로그 조회
- 대량으로 로그를 조회하는 API입니다.
- 응답시 application/stream+json로 응답합니다.

```
GET /push/v2.4/appkeys/{appKey}/bulk-logs/message?from={from}&to={to}&messageId={messageId}&pushType={pushType}&sendResult={sendReesult}
Content-Type: application/json;charset=UTF-8
Accept: application/stream+json
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| from | Required, DateTime String | 최근 30일까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Required, DateTime String | 최근 30일까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| messageId | Optional, String | 조회할 메시지 아이디 |
| pushType | Optional, String | 조회할 푸시 타입 |
| sendResult | Optional, String | 발송 결과입니다. 'SENT', 'SENT_FAILED' |

#### Request Body
```
없음
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

## 예약 메시지

### 생성
#### 예약 메시지 발송 스케줄 생성
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/schedules
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |


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
| type | Required, String | 'EVERY_DAY' (매일), 'EVERY_WEEK' (매주), 'EVERY_MONTH' (매월) |
| fromDate | Required, Date String | 예약 메시지 시작 년월일 (YYYY-MM-DD) |
| toDate | Required, Date String | 예약 메시지 종료 년월일 (YYYY-MM-DD) |
| times | Required, Time String | 예약 메시지 발송 시분 (hh:mm) |
| days | Optional, Number Array | type이 'EVERY_MONTH'일 때 설정합니다. (1, 2, ..., 31: 1일, 2일, ..., 31일) |
| daysOfWeek | Optional, String Array | type이 'EVERY_WEEK'일 때 설정합니다. ('SUNDAY', 'MONDAY', 'TUESDAY', 'WEDNESDAY', 'THURSDAY', 'FRIDAY', 'SATURDAY') |

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
| schedules | - | 일시 (ISO 8601, e.g. YYYY-MM-DDThh:mm) |


#### 예약 메시지 생성
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/reservations
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |

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
    "removeGuide": "매뉴 > 설정",
    "timeToLiveMinute": 1,
	"provisionedResourceId": "id"
}
```

| Field | Usage | Description |
| - | - | - |
| schedules | Required, DateTime String Array | 예약 메시지 발송 스케줄 목록 |
| isLocalTime | Required, Boolean | 현지 시간 발송 여부 |

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
| reservationId | Number | 예약 메시지 아이디 |
| reservationIdString | String | 예약 메시지 아이디 문자열 |

### 조회
#### 목록 조회
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/reservations?pageIndex={pageIndex}&pageSize={pageSize}&reservationStatus={reservationsStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| pageIndex | Optional, Number | 기본값 0 |
| pageSize | Optional, Number | 기본값 25, 최댓값 100 |
| from | Optional, DateTime String | 최근 30일까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| deliveryFrom | Optional, DateTime String | 발송 스케줄 날짜 시작 값 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD). 조회된 예약 내부의 발송 스케줄 목록은 필터링하지 않고 노출. |
| deliveryTo | Optional, DateTime String | 발송 스케줄 날짜 종료 값 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD). 조회된 예약 내부의 발송 스케줄 목록은 필터링하지 않고 노출. |
| reservationStatus | Optional, String | 'RESERVED', 'COMPLETE' |

##### Request Body
```
없음
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
					"title" : "한국어 제목",
					"body" : "한국어 내용"
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
| reservationIdString | - | 예약 메시지 아이디 문자열 |
| createdDateTime | - | 예약 메시지 등록 일시 (ISO 8601) |
| updatedDateTime | - | 예약 메시지 수정 일시 (ISO 8601) |
| completedDateTime | - | 예약 메시지 발송 완료 일시, 완료가 안되었다면 현재 시간 표시 (ISO 8601) |
| reservationStatus | - | 'RESERVED', 'COMPLETED' |
| schedules.scheduleId | - | 예약 메시지 발송 스케줄 아이디 |
| schedules.scheduleIdString | - | 예약 메시지 발송 스케줄 아이디 문자열 |
| schedules.reservationIdString | - | 예약 메시지 발송 스케줄이 속한 예약 메시지 아이디 문자열 |
| schedules.deliveryDateTime | - | 예약 메시지 발송 일시 |
| schedules.timezoneOffset | - | 예약 메시지 발송 타임존, 현지 시간 발송시 설정 |
| schedules.scheduleStatus | - | 'READY', 'SENDING', 'CANCELED', 'DONE' 예약 메시지 발송 스케줄 상태 |
| totalCount | - | 등록된 전체 예약 메시지 수 |


#### 단건 조회
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/reservations/{reservation-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

##### Request Body
```
없음
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
				"title" : "한국어 제목",
				"body" : "한국어 내용"
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
| updatedDateTime | DateTime String | 예약 수정 일시(ISO 8601) |

#### 발송된 예약 메시지 조회
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/reservations/{reservation-id}/messages?pageIndex={pageIndex}&pageSize={pageSize}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| reservationId | Required, Number | 예약 메시지 아이디 |
| pageIndex | Optional, Number | 기본값 0 |
| pageSize | Optional, Number | 기본값 25, 최댓값 100 |

##### Request Body
```
없음
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
					"title" : "6시 55분 예약 메시지",
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
| totalCount | - | 발송된 전체 메시지  수 |

### 수정
#### 예약 메시지 수정
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
		"2020-12-30T10:05",
		"2020-12-31T12:40"
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
			"title" : "한국어 제목",
			"body" : "한국어 내용"
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

### 삭제
#### 예약 메시지 삭제
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/reservations?reservationIds={reservationId,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| reservationIds | Required, Number Array | ','로 구분, e.g. reservationIds=1,2 |

##### Request Body
```
없음
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

## 태그

### 생성
#### 태그 생성
##### Method, URL, Headers
```
POST /push/v2.4/appkeys/{appkey}/tags
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body

```json
{
    "tagName" :  "서른"
}
```

| Field | Usage | Description |
| - | - | - |
| tagName | Required, String | 태그 이름, 최대 길이 255, 공백(Space) 문자 불가 |

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
| tagId | Required, String | 생성된 태그 아이디, 길이 8 |

##### Description
- 태그는 최대 2,048개까지 생성할 수 있습니다.

#### 태그에 UID 추가 생성
- 태그에 UID를 추가(append)하는 것으로, 기존에 있던 UID를 추가하면 UID의 태그는 늘어납니다.
- 한 UID에 태그를 16개까지 추가할 수 있습니다.

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
| uids | Required, String Array | UID 배열, 최대 길이 16, UID 최대 길이 64 |

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


#### UID에 태그 목록 설정
- UID의 태그를 교체(Replace)하는 것으로, 기존에 설정된 태그는 삭제되고 새로운 태그로 설정됩니다.
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

### 조회
#### 태그 목록 조회
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/tags?tagName={tagName}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| tagName | Optional, String | 태그 이름 |

##### Request Body
```
없음
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
| createdDateTime | Required, Date Time String | 생성 일시 (ISO 8601) |
| updatedDateTime | Required, Date Time String | 수정 일시 (ISO 8601) |

#### 태그 단건 조회
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
없음
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
        "tagName" :  "서른",
        "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
        "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
    }
}
```

#### 태그의 UID 목록 조회
- 태그가 달린 UID 목록을 조회합니다.

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/tags/{tag-id}/uids?offsetUid={uid}&limit={limit}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| offsetUid | Optional, String | 설정된 UID 다음 부터 조회 |
| limit | Optional, Number | 조회할 UID 수 |

##### Request Body
```
없음
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
| contacts | -, Object Array | UID의 연락처, 토큰 정보 목록 |
| contactType | -, String | 토큰 타입, 'TOKEN_FCM', 'TOKEN_APNS', 'TOKEN_APNS_SANDBOX', 'TOKEN_TENCENT', 'TOKEN_ADM' |
| contact | -, String | 토큰 |
| createdDateTime | Required, Date Time String | 생성 일시 (ISO 8601) |

#### UID 조회
- UID를 조회합니다.
- 토큰 등록시 Contact(연락처)가 등록됩니다.

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/uids/{uid}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
없음
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

### 수정
#### 태그 수정
##### Method, URL, Headers
```
PUT /push/v2.4/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```json
{
    "tagName" :  "30대"
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

### 삭제
#### 태그 삭제
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
없음
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


#### UID 삭제
- UID 삭제시 Contact, Token도 같이 삭제됩니다.
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/uids?uids={uid,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| uids | -, Object Array | 삭제할 UID 목록, 쉼표(,)로 구분합니다. 한번에 16개까지 삭제할 수 있습니다. |

##### Request Body
```
없음
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

#### 태그의 UID 삭제
- Tag와 UID 관계만 삭제합니다.
- Contact, Token이 삭제되지는 않습니다.
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/tags/{tagId}/uids?uids={uid,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```
없음
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

## UID

### 생성

#### 태그 추가
- UID에 태그 아이디로 태그를 추가합니다.
- Secret Key가 필요없다. 앱에서 호출 가능합니다.
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


### 조회

#### UID의 태그 아이디 조회
- UID의 태그 아이디를 조회합니다.
- Secret Key가 필요없다. 앱에서 호출 가능합니다.
##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```
없음
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


### 수정
#### UID의 태그 수정
- UID에 태그 아이디로 태그를 수정합니다.
- Secret Key가 필요없다. 앱에서 호출 가능합니다.
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

### 태그 삭제
- UID의 태그 아이디를 조회합니다.
- Secret Key가 필요없다. 앱에서 호출 가능합니다.
##### Method, URL, Headers
```
DELETE /push/v2.4/appkeys/{appkey}/uids/{uid}/tag-ids?tagIds={tagId,}
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```
없음
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
| tagIds | Required, String Array | Query String, 삭제할 태그 아이디, 쉼표(,)로 구분 |

<span id="stats-api"></span>
## 통계
### 통계 조회
- 통계 이벤트 키를 기준으로 통계를 조회할 수 있습니다.

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/stats?eventCategory={eventCategory}&statisticsType={statisticsType}&timeUnit={timeUnit}&from={from}&to={to}&extra1s={extra1,}&messageId={messageId}&statsIds={statsId,}
Content-Type: application/json;charset=UTF-8
```
| Field | Usage | Description |
| - | - | - |
| eventCategory | Required, String | 이벤트의 카테고리. MESSAGE, TOKEN_REGISTRATION, TOKEN_LANGUAGE, TOKEN_COUNTRY, TOKEN_AGREEMENT |
| statisticsType | Optional, String | 검색된 통계 데이터의 표현 형식. NORMAL(기본값), MINUTELY, HOURLY, DAILY, BY_DAY |
| timeUnit | Optional, String | 통계 데이터의 시간 단위. 기본값은 조회 기간에 따라 결정, MINUTES, HOURS, DAYS |
| from | Optional, DateTime String | 최근 30일까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | 최근 30일까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| extra1s | Optional, String Array | eventCategory가 MESSAGE인 경우 푸시 타입으로 필터링 가능. FCM, APNS, APNS_SANDBOX, APNS_VOIP, APNS_SANDBOXVOIP, ADM, TENCENT |
| messageId | Optional, String | 메시지 아이디 |
| statsIds | Optional, String Array | 통계 이벤트 키 아이디 |
| statsCriteria	| Optional, String Array | 합계 시 통계 기준, 설정하지 않으면 기본값으로 합계를 계산. EVENT(기본값), EXTRA_1, EXTRA_2, EXTRA_3, TEMPLATE_ID |

##### Request Body
```
없음
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

### 통계 합계 조회
- 조회한 통계 데이터를 합산하는 합계 API가 추가되었습니다.

##### Method, URL, Headers
```
GET /push/v2.4/appkeys/{appkey}/stats?eventCategory={eventCategory}&statisticsType={statisticsType}&timeUnit={timeUnit}&from={from}&to={to}&extra1s={extra1,}&messageId={messageId}&statsIds={statsId,}&statsCriteria={statsCriterion,}
Content-Type: application/json;charset=UTF-8
```
| Field | Usage | Description |
| - | - | - |
| eventCategory | Required, String | 이벤트의 카테고리. MESSAGE, TOKEN_REGISTRATION, TOKEN_LANGUAGE, TOKEN_COUNTRY, TOKEN_AGREEMENT |
| statisticsType | Optional, String | 검색된 통계 데이터의 표현 형식. NORMAL(기본값), MINUTELY, HOURLY, DAILY, BY_DAY |
| timeUnit | Optional, String | 통계 데이터의 시간 단위. 기본값은 조회 기간에 따라 결정, MINUTES, HOURS, DAYS |
| from | Optional, DateTime String | 최근 30일까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | 최근 30일까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| extra1s | Optional, String Array | eventCategory가 MESSAGE인 경우 푸시 타입으로 필터링 가능. FCM, APNS, APNS_SANDBOX, APNS_VOIP, APNS_SANDBOXVOIP, ADM, TENCENT |
| messageId | Optional, String | 메시지 아이디 |
| statsIds | Optional, String Array | 통계 이벤트 키 아이디 |
| statsCriteria	| Optional, String Array | 합계 시 통계 기준, 설정하지 않으면 기본값으로 합계를 계산. EVENT(기본값), EXTRA_1, EXTRA_2, EXTRA_3, TEMPLATE_ID |

##### Request Body
```
없음
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


* *문서 수정 내역*
    * *(2020.03.24) 통계 API 추가*
    * *(2020.12.29) 통계 합계 API 추가*