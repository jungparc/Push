## Notification > Push > 콘솔 사용 가이드

Push 서비스를 활용하려면 먼저 타사 푸시 알림 서비스 인증서를 등록해야합니다.

지원하는 푸시 알림 서비스 인증서는 현재 다음과 같습니다:
- GCM - Google Cloud Messaging (Firebase Cloud Messaging)
- APNS - Apple Push Notification Service
- ADM - Amazon Device Messaging

## 인증서 관리

인증서 관리를 하기 위하여 콘솔에서 **Notification > Push > 인증서**를 클릭합니다.


<span id="get-fcm-service-account-credential"></span>

### FCM Service Account Credential
안드로이드 기기에 푸시 알림 메시지를 전송하기 위해서는 **Service Account Credential**이 필요합니다.
**Service Account**(서비스 계정)는 일반적으로 Google Cloud와  A2A(Application to Application) 통신 시 사용하는 특별한 유형의 계정입니다.

#### FCM Service Account Credential JSON 파일 얻기
1. [Google Firebase Console](https://console.firebase.google.com)에 접속합니다.
2. 프로젝트 추가를 통해 새로운 프로젝트를 생성합니다.
3. 생성된 프로젝트로 이동합니다.
4. 페이지 왼쪽 상단 프로젝트 개요 옆 **톱니바퀴 > 프로젝트 설정**을 클릭합니다.
5. **서비스 계정**을 선택합니다.
6. Firbase Admin SDK 항목에서 **새 비공개 키 생성**을 클릭해 새로운 **Service Account Credential** JSON 파일을 다운로드합니다.

#### FCM Service Account Credential JSON 파일 등록
1. 콘솔에서 **Notification > Push > 인증서**를 클릭합니다.
2. 다운로드 받은 JSON 파일을 열어 내용을 복사합니다.
3. 복사한 내용을 **FCM Service Account Credential** 항목에 붙여 넣고 **등록**을 클릭합니다.

<span id="get-apns-jwt"></span>

### APNS JWT 인증 정보 얻기
iOS 기기에 푸시 알림 메시지를 전송하기 위해서는 Apple Developer 사이트에서 발급 받은 암호 키와 키 ID(Key ID), 팀 ID(Team ID, App ID Prefix), 토픽(Topic)이 필요합니다.

#### APNS 암호 키 얻기
1. **Apple Developer 콘솔**에서 **Certificates, IDs & Profiles**로 이동합니다.
2. **Keys** 선택합니다.
3. **Create a key** 선택합니다.
4. **Register a New Key**에서 키 이름 입력, **ENABLE** 항목에서 **Apple Push Notifictions service (APNs)** 선택 후 **Continue**로 계속 진행합니다.
5. 내용 확인 후 **Register** 선택합니다.
6. **Download** 선택해 암호 키 파일을 받습니다.

#### 키 ID 얻기
1. **Apple Developer 콘솔**에서 **Certificates, IDs & Profiles**로 이동합니다.
2. 발급 받은 키(Key)를 선택합니다.
3. **View Key Details** 항목에서 확인할 수 있습니다.

#### 팀 ID 얻기
1. **Apple Developer 콘솔**에서 **Certificates, IDs & Profiles**로 이동합니다.
2. **Identifiers** 선택합니다.
3. **Edit your App ID Configuration** 항목에서 확인할 수 있습니다.

#### 토픽
JWT를 이용한 인증을 위해서는 토픽(Topic)이 필요한데, 토픽은 앱의 번들 아이디(Bundle ID)입니다.

### APNS 인증서
iOS 기기에 푸시 알림 메시지를 전송하기 위해서는 Apple Developer 사이트에서 발급받은 APNS 인증서가 필요합니다.

#### APNS 인증서 생성, 가져오기

1. Mac에서 **키체인**을 실행합니다.
2. **체인 접근 > 인증서 지원 > 인증 기관**에서 **인증서 요청** 버튼을 클릭합니다.
3. **사용자 이메일 주소**에 Apple Developer ID 등 필요한 정보를 입력합니다. **디스크 지정됨**을 선택하고, **본인이 키 쌍 정보 저장**을 선택한 후 **계속**을 클릭합니다.
4. **키 쌍 정보**에서 키 크기, 알고리즘 선택 후 **계속**을 클릭합니다. CSR(Certificate Signing Request) 파일이 생성됩니다.
5. [Apple Developer Certificates](https://developer.apple.com/account/ios/certificate/certificateList.action)로 이동합니다.
6. 오른쪽 상단에서 **+**(Add) 버튼을 클릭해 Certificate 발급 페이지로 이동합니다.
7. **Select Type > Request > Generate** 탭에서 **키체인**을 통해서 만든 CSR 파일을 업로드합니다. **Generate** 버튼을 클릭합니다.
8. 인증서를 생성하고 다운로드할 수 있습니다. 만약 승인 권한이 없다면 승인을 받고 다운로드할 수 있습니다.
9. 인증서를 다운로드한 후, **키체인 > 인증서**에서 인증서를 선택합니다.
10. 인증서에서 마우스 오른쪽 버튼을 클릭하고 **보내기**를 클릭합니다.
11. 저장 위치를 설정하고 **저장**을 클릭합니다(파일 형식: .p12).
12. 보낸 항목을 보호하는 데 사용할 암호를 입력하면 APNS 인증서 생성이 완료됩니다.

#### APNS 인증서 등록

1. 콘솔에서 **Notification > Push > 인증서**를 클릭합니다.
2. **APNS 인증서** 아래 **인증서**에서 **파일 선택**을 클릭합니다.
3. **비밀번호**에 인증서 비밀번호를 입력합니다.
4. **등록**을 클릭합니다.

#### APNS 주의 사항

##### APNS(Production), APNS_SANDBOX(Development) 차이
- Production Provisioning Profile로 빌드한 앱은 APNS(Production)를 이용해야 하며, Development Provisioning Profile로 빌드한 앱은 APNS_SANDBOX(Development)를 이용해야 합니다.
  이를 지키지 않으면, 푸시 메시지를 정상적으로 수신할 수 없습니다.
- Push에 등록한 APNS 인증서와 앱을 빌드할 때 사용한 Provisioning Profile이 달라 메시지 발송에 실패한 경우가 대부분입니다.   
   APNS(Production) = Production Provisioning Profile  
   APNS_SANDBOX(Development) = Development Provisioning Profile  
- 더 자세한 설명은 다음 링크를 참고하시기 바랍니다.  
   [APNs Overview](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)
   [App Distribution Quick Start](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppStoreDistributionTutorial/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013839)

##### APNS 메시지 발송 실패 원인
- 인증서가 만료되었을 때  
- 잘못된 인증서를 등록했을 때  
- 앱에서 푸시 메시지 수신을 거부했을 때  
- 디바이스가 인터넷에 연결되어 있지 않을 때

### ADM 자격 증명

Kindle Fire 앱에 푸시 알림 메시지를 전송하기 위해서는 앱의 Client ID와 Client Secret이 필요합니다.

#### ADM 애플리케이션 및 프로파일 등록(Client Id, Client Secret 획득)
1. [ADM 개발자 콘솔](https://developer.amazon.com/home.html)에 접속합니다.
2. 페이지 왼쪽 상단에서 **APP & SERVICES**를 클릭한 후, 하단에 **Add a New App** 버튼을 클릭합니다.
3. 앱이 생성되면 중간 탭에 있는 **Device Messaging**을 클릭하고 **Create a New Security Profile** 버튼을 클릭합니다.
4. 프로필 생성 완료 후, 중간 탭에 있는 **Security Profiles**을 클릭하고 **View Security Profile** 버튼을 클릭합니다.
5. **General** 탭에서 Client ID와 Client Secret 값을 확인할 수 있습니다.

#### ADM Kindle 설정 정보 등록(API key 획득)
1. **Security Profiles** 탭을 클릭한 후 중간에 있는 **Android/Kindle Setting** 탭을 클릭합니다.
2. App Key Name, Package, MD5 Signature, SHA256 Signature 정보를 입력합니다.
3. 아래와 같은 명령어로 MD5, SHA256 정보를 조회할 수 있습니다.
```
> keytool -list -v -keystore {keystoreFileName}

키 저장소 비밀번호 입력:
키 저장소 유형: JKS
키 저장소 제공자: SUN

키 저장소에 1개의 항목이 포함되어 있습니다.

별칭 이름: androiddebugkey
생성 날짜: 2018. 5. 9
항목 유형: PrivateKeyEntry
인증서 체인 길이: 1
인증서[1]:
소유자: C=US, O=Android, CN=Android Debug
발행자: C=US, O=Android, CN=Android Debug
일련 번호: 1
적합한 시작 날짜: Wed May 09 19:59:46 KST 2018 종료 날짜: Fri May 01 19:59:46 KST 2048
인증서 지문:
         MD5:  xxxx
         SHA1: xxxx
         SHA256: xxxx
서명 알고리즘 이름: SHA1withRSA
주체 공용 키 알고리즘: 1024비트 RSA 키
버전: 1
```
- 등록 완료 후 **Show** 버튼을 클릭하면 API key 정보를 조회할 수 있습니다.


## 메시지 전송

인증서와 토큰을 정상적으로 등록한 후, 콘솔에서 **Notification > Push > 메시지**를 클릭하고 **추가** 버튼을 클릭합니다. 양식을 작성해 메시지를 즉시 전송할 수 있습니다.

즉시 전송 양식 구성은 다음과 같습니다.

### 대상

| 이름                     | 내용                                       |
| ---------------------- | ---------------------------------------- |
| 대상 유형                  | ALL, UID, TAG 중에서 하나를 선택할 수 있습니다.        |
| To                | UID를 입력할 수 있습니다. Target > Type에서 UID를 선택해 UID로 전환할 수 있습니다. 쉼표(',')를 구분자로 여러 개를 입력할 수 있습니다. |
| UIDs              | 쉼표(',')를 구분자로 여러 개를 입력할 수 있습니다.          |
| 태그                | **선택** 버튼을 클릭해 태그를 선택할 수 있습니다.           |
| 선택된 태그            | 선택된 태그가 표시됩니다. 다시 클릭해 선택에서 제외할 수 있습니다.   |
| 국가 코드             | 국가 코드를 입력할 수 있습니다. 쉼표(',')를 구분자로 여러 개를 입력할 수 있습니다. |
| 푸시 유형             | GCM, APNS, APNS Sandbox, ADM 복수로 선택할 수 있습니다. |

### 옵션

| 이름                     | 내용                                       |
| ---------------------- | ---------------------------------------- |
| TTL(Time To Live) | 메시지 발송 유효 시간입니다. 설정한 시간 동안 발송이 지연되는 경우, 자동으로 실패 처리됩니다. 단위는 분입니다. 0이면 발송 지연으로 실패 처리되지 않습니다. |

### 메시지

| 이름                     | 내용                                       |
| ---------------------- | ---------------------------------------- |
| 편집기 유형           | SIMPLE, JSON 두 가지 유형이 있습니다. JSON을 선택하면 직접 메시지를 작성할 수 있습니다. |
| 메시지 유형           | NOTIFICATION, AD 두 가지 유형이 있습니다. AD 경우, 광고성 푸시 메시지 수신에 동의한 사용자에게만 메시지가 발송됩니다. |

### 리치 메시지 작성
푸시 메시지에 버튼, 이미지 등을 넣어 다양한 형태로 푸시 메시지를 발송할 수 있습니다.
입력한 메시지가 Android와 iOS 스마트폰 어떻게 보이는지 미리 확인할 수 있습니다.
v1.7 이상 SDK가 적용된 곳에서만 사용할 수 있는 기능입니다.

![push_04_201812](https://static.toastoven.net/prod_push/12-10/push_04_201812.png)

#### 1. 버튼

|이름|내용|
|---|---|
| 이름 | 버튼의 이름 |
| 유형 | 버튼의 유형, 응답(REPLY), 앱 열기(OPEN_APP), URL 열기(OPEN_URL), 닫기(DISMISS) |
| 전송 버튼 이름 | 버튼 유형이 응답 버튼이면 iOS에서 전송 버튼 이름을 설정할 수 있습니다. |
| 링크 | 버튼을 눌렀을때 이동하거나 실행할 링크입니다. 버튼 유형이 URL 열기이면 해당됩니다. |
| 힌트 | 버튼에대한 설명입니다. |

##### 버튼의 유형
- 응답
    - 다이렉트 답장 기능을 실행합니다.
    - 사용자가 전송 버튼을 터치했을 때 액션 리스너로 사용자 입력 텍스트가 전달 됩니다.
- 앱 열기
    - 앱이 실행됩니다.
    - 액션 리스너를 통해 메시지 전문이 전달됩니다. 메시지 내에 정보를 입력해 특정 페이지로 이동 등의 기능을 구현할 수 있습니다.
- URL 열기
    - 링크 항목에 입력된 URL(https://...) 혹은 Scheme(scheme://...)을 실행합니다.
    - URL을 입력하면 웹 브라우저가 실행되고 해당 URL을 로드합니다.
    - 스킴(Scheme)을 입력하면 앱에 미리 정의해 둔 스킴을 실행합니다.
- 닫기
    - 해당 알림을 닫습니다.

#### 2. 미디어

|이름|내용|
|---|---|
| 위치 | 미디어의 위치한 곳, 'REMOTE' 또는 'LOCAL' |
| 주소 | 미디어가 위치한 주소, URL, URI 등이 될수 있습니다. |
| 유형 | 이미지, GIF, 동영상, 소리를 선택할 수 있습니다. (Android는 이미지만 가능) |
| 확장자 | 미디어의 확장자 | .png, .avi 등 미디어의 확장자입니다. |
| 펼치기 | 미디어 펼짐 기능, Android에서만 가능합니다. |

##### 미디어 파일 지정
- 외부
    - 입력한 URL에 해당하는 미디어 파일을 다운로드하여 사용합니다.
    - Android
        - Android Pie 이상에서 HTTP를 사용하려면 <a href="http://docs.toast.com/ko/TOAST/ko/toast-sdk/push-android/#android-p">network-security-config</a>를 설정해야 합니다.
    - iOS
        - iOS 9 이상에서 HTTP를 사용하려면 Info.plist 파일내에 ATS(App Trasport Secuirty)를 설정해야 합니다.
        - 실제 미디어 파일의 확장자 정보를 extension 항목에 입력해야 합니다. (예: jpg, png, mp4, wav, ...)
- 내부
    - 앱 내에 포함되어 있는 리소스를 사용합니다.
    - Android
        - 파일은 'res > drawable'에 미리 추가해야 합니다.
        - 리소스 식별자를 통해 접근하므로 메시지 작성시 'richMessage.media.source'에 확장자를 제외한 파일 이름을 입력합니다.
        - Android에서는 파일 이름이 리소스의 식별자로 사용되기 때문에 확장자가 다르더라도 동일한 파일 이름을 사용할 수 없습니다.
        지원하는 이미지 포멧은 png, jpg, gif 입니다. (현재 비디오, 오디오 형식의 미디어는 지원하지 않습니다.)
    - iOS
        - 리소스는 리치 메시지를 생성하는 <a href="http://docs.toast.com/ko/TOAST/ko/toast-sdk/push-ios/#notification-service-extension">Notificaiton Service Extension</a> 프로젝트에 미리 추가해야 합니다.
        - XCode에서 파일 혹은 디렉토리를 'NotificationServiceExtension' 프로젝트에 추가합니다.
        - 'Build Phases > TARGETS'에서 파일이 정상적으로 추가되었는지 확인합니다.
        - 번들 리소스를 통해 접근하므로 확장자를 포함한 전체 파일명이 필요합니다.
        - 메시지 작성시 'richMessage.media.source'에 추가한 파일 이름을 입력합니다.

##### 미디어 유형
- 이미지

| | Android | iOS |
| - | - | - |
| 지원 형식 | JPEG, PNG, GIF | JPEG, PNG, GIF |
| GIF 애니메이션 | 지원 안 함 | 지원함 |
| 파일 크기 | 제한 없음 | 10MB |
| 권장 사항 | 2:1 비율의 가로 이미지 권장<br>Small: 512x256<br>Medium: 1024x512<br>Large: 2048x1024 | 가로 이미지 권장<br>최대 크기: 1038x1038 |

- 동영상

| | Android | iOS |
| - | - | - |
| 지원 형식 | 지원 안 함 | MPEG, MPEG3Video, MPEG4, AVIMovie |
| 파일 크기 | 지원 안 함 | 50MB |

- 소리

| | Android | iOS |
| - | - | - |
| 지원 형식 | 지원 안 함 | WaveAudio, MP3, MPEG4Audio |
| 파일 크기 | 지원 안 함 | 5MB |

#### 3. 큰 아이콘
Android에서만 제공하는 기능입니다. 알림에 큰 아이콘을 지정합니다. 파일 지정 방법은 미디어 파일 지정 방법과 동일합니다.

|이름|내용|
|---|---|
| 위치 | 위치한 곳, 'REMOTE' 또는 'LOCAL' |
| 주소 | 이미지가 위치한 주소, URL, URI 등이 될수 있습니다. |

#### 4. 그룹
Android에서만 제공하는 기능입니다. 알림에 그룹을 설정하고 그룹 키가 동일한 알림은 모아서 표현합니다.

|이름|내용|
|---|---|
| 키 | 그룹의 키 |
| 설명 | 그룹에대한 설명 |

#### 5. 알림음
| | Android | iOS |
| - | - | - |
| 지원 형식 | MP3, PCM/WAVE, Vorbis | Linear PCM, MP4(IMA/ADPCM), μ-law, aLaw |
| 확장자 | .mp3, .wav, .ogg | .aiff, .wav, .caf |
| 플레이 시간 | 제한 없음 | 30 초 |

- 앱 내에 포함되어 있는 리소스만 지정 가능합니다. (외부 URL 사용 불가)
- Android
    - 리소스는 'res > raw' 폴더에 미리 추가해야 합니다.
    - 리소스 식별자를 통해 접근하므로 파일 확장자는 무시됩니다.
    - Android Oreo 미만에서만 동작합니다.
- iOS
    - 리소스는 앱 프로젝트의 번들 리소스로 미리 추가해야 합니다.
    - 번들 리소스를 통해 접근하므로 확장자를 포함한 전체 파일명이 필요합니다.


## 예약 전송

Push의 예약 전송 기능을 사용하면 원하는 시간에 메시지를 전송할 수 있습니다. 메시지를 예약 전송 하려면 콘솔에서 **Notification > Push > 예약**을 클릭합니다.

예약 전송 양식은 다음과 같습니다.

| 이름           | 내용                                       |
| ------------ | ---------------------------------------- |
| 일정           | EVERY_DAY: StartDate, EndDate 사이 매일 지정된 시간에 메시지를 보냅니다. <br/> EVERY_WEEK: Reservation Condition에 요일을 지정해 매주 저정한 요일에 메시지를 보냅니다. <br/> EVERY_MONTH: Reservation Condition에 날짜를 지정해 매달 지정한 날짜에 메시지를 보냅니다. |
| 요일  | 요일을 선택할 수 있습니다.    |
| 일 | 쉼표(',')를 구분자로 날짜(월)를 입력할 수 있습니다. |
| 예약 시간        | 메시지를 전송할 시간을 입력합니다. 시간 형식은 'HH:mm'입니다.   |
| 시작일          | 메시지를 전송할 시작 날짜를 입력합니다. 달력에서 날짜를 선택할 수 있습니다. 형식은 'YYYY-MM-DD'입니다. |
| 종료일          | 메시지를 전송하는 마지막 날짜를 입력합니다. 달력에서 날짜를 선택할 수 있습니다. 형식은 'YYYY-MM-DD'입니다. |
| 현지 시간        | true로 설정하면 현지 시간 기준으로 메시지를 전송합니다.        |

### 토큰 조회

푸시 유형을 선택 후 토큰을 입력해 조회 및 삭제가 가능합니다.
토큰 삭제 기능은 현재 콘솔 API탭과 토큰 탭에서만 지원됩니다.

### Public APIs

Public API에서 제공되는 API 호출입니다.
토큰 생성, 메시지 전송, 피드백 조회 API 호출이 제공됩니다.
API 버전을 선택하여 버전별로 요청 할 수 있습니다.
자세한 API 가이드는 [API 가이드](./api-guide/) 에서 확인하실 수 있습니다.

## 토큰

토큰을 특정 UID에 추가할 수 있습니다.
UID, 토큰별로 검색할 수 있습니다.

콘솔에서 **Notification > Push > 토큰**을 클릭합니다.

### 토큰 추가

![push_11_201812](https://static.toastoven.net/prod_push/12-10/push_11_201812.png)

1. **추가** 버튼 클릭합니다.
2. 추가 모달창에서 UID 와 토큰을 입력합니다.
3. 푸시유형 및 나머지 옵션을 확인 후 **확인** 버튼을 눌러 토큰을 추가합니다.

### 토큰 검색

**검색 유형**을 **TOKEN**으로 선택하면 나타나는 목록에서 원하는 푸시 유형을 선택하고 토큰을 검색할 수 있습니다.

![push_12_201812](https://static.toastoven.net/prod_push/12-10/push_12_201812.png)

- **Details** 버튼 클릭 시 상세 항목을 확인할 수 있습니다.

### UID 검색

**검색 유형**을 **UID**로 선택하고 UID를 검색할 수 있습니다.

![push_13_201812](https://static.toastoven.net/prod_push/12-10/push_13_201812.png)

- 일부만 일치되더라도 검색 결과에 나타납니다.
- **Details** 버튼 클릭 시 상세 항목을 확인할 수 있습니다.

## 태그

UID에 태그를 추가해 UID를 관리할 수 있습니다. 메시지 발송 시 태그를 선택해 발송 대상을 지정할 수 있습니다. 태그는 Notification 서비스 전역으로 사용됩니다. 태그는 최대 2,048개까지 생성할 수 있습니다. 하나의 UID에 태그를 16개까지 추가할 수 있습니다.

콘솔에서 **Notification > Push > 태그**를 클릭합니다.

### 태그 추가

![push_15_201812](https://static.toastoven.net/prod_push/12-10/push_15_201812.png)

1. **추가** 버튼 클릭 후 태그 이름을 입력합니다.
2. **확인** 버튼을 눌러 태그를 추가 합니다.

### 태그가 붙은 UID 관리
태그를 클릭하면, 태그가 붙은 UID 목록을 확인할 수 있습니다.

![push_16_201812](https://static.toastoven.net/prod_push/12-10/push_16_201812.png)

1.  **추가** 버튼을 클릭합니다.
2. UID를 입력 후 **확인** 버튼을 클릭합니다.
	- UID는 한 번에 1,000개까지 추가할 수 있습니다.

<span id="stats-event-key"></span>

## 통계 이벤트 키 관리
**통계 이벤트 키 관리** 탭에서 통계 이벤트 키를 관리할 수 있습니다. 생성 시 이름, 설명, 이벤트 수집 기간을 설정할 수 있습니다. 이벤트 수집 기간을 설정하지 않으면 수집 기간은 무기한으로 설정됩니다. 생성된 통계 이벤트 키는 메시지 발송 시 설정할 수 있습니다. 통계 이벤트 키가 설정된 메시지들의 발송 결과는 **통계** 탭에서 통계 이벤트 키로 검색할 수 있습니다.

<span id="stats"></span>

## 통계
최근 30일 내 수집된 메시지 수신/확인 데이터를 검색할 수 있습니다. 콘솔에서 **통계**를 선택합니다. 메시지, 토큰 등록이나 삭제에 대한 통계를 검색할 수 있습니다.

1. 기간을 검색 조건으로 설정 할 수 있습니다.
    - 간편 기간 설정 버튼 외 수동 설정 시 1분을 최소 기간, 30일을 최대 기간으로 설정할 수 있습니다.
2. 유형으로 그래프가 표시되는 방식을 설정 할 수 있습니다.
    - 기본은 설정된 기간에 따라 자동으로 최적화된 유형으로 그래프를 표시합니다.
3. 메시지 통계에서는 통계 이벤트 키, 푸시 타입으로 검색할 수 있습니다.

## 설정

푸시 서비스를 설정할 수 있습니다.

콘솔에서 **Notification > Push > 설정**을 클릭합니다.

### 광고 표시 문구 위치 설정

- 광고성 메시지 발송 시 표시되는 광고 표시 문구 위치를 설정할 수 있습니다.
- 제목(title)
    - 기본 설정입니다.
    - 제목에 '(광고)' 표시와 연락처가 표시됩니다.
- 내용(body)
    - 내용에 '(광고)', 연락처, 수신 동의 철회 방법이 표시됩니다.
- 기능 설정 시 실제 반영까지 대략 수분이 걸릴 수 있습니다.

### 토큰 설정

- 토큰 만료 기간 설정
    - 설정한 기간 동안 등록 요청이 없는 토큰들을 메시지 발송 대상에서 제외합니다.
    - 기본값은 12개월입니다.
    - 설정한 기간 동안 앱을 사용하지 않는 사용자들의 토큰이 메시지 발송 대상에서 자동으로 제외되어 발송 요금을 절약할 수 있습니다.

- 앱 유형 설정
    - 연동된 앱의 유형에 따라 토큰을 관리합니다.
    - 다중 토큰
        - 기본 설정입니다.
        - 앱의 사용자가 여러 기기에 설치된 앱을 동시에 사용할 수 있는 앱이며, 한 사용자는 여러 개의 토큰을 가질 수 있습니다.
        - 예를 들어 사용자가 휴대폰과 태블릿을 사용한다면, 두 개의 토큰을 가질 수 있고, 휴대폰과 태블릿, 두 곳으로 푸시 메시지를 발송합니다.
    - 단일 토큰
        - 사용자가 한 번에 한 기기에서만 앱을 사용할 수 있습니다. 한 사용자는 하나의 토큰만 가질 수 있습니다.
        - 예를 들어 사용자가 휴대폰과 태블릿을 사용한다면, 한 개의 토큰만 가질 수 있고, 둘 중 한 곳으로 푸시 메시지를 발송합니다.

- 기능 설정 시 실제 반영까지 대략 수분이 걸릴 수 있습니다.

<span id="message-delivery-receipient"></span>
### 메시지 수신/확인

- 메시지 수신 및 확인 데이터 수집(Message Delivery Receipt) 기능을 활성화할 수 있습니다.
- 활성화된 기능 동작을 위해 클라이언트 SDK v1.4 이상이 적용되어야 합니다. 안드로이드는 SDK 적용만으로 기능이 동작합니다. iOS는 추가적인 처리가 필요합니다.
    - <a href="https://docs.toast.com/ko/TOAST/ko/toast-sdk/push-ios/#_19"  target="_blank">iOS 클라이언트 SDK 가이드 바로가기</a>
- 수집된 데이터는 **통계** 탭에서 확인할 수 있습니다.
- 데이터의 수집 시간은 기기의 시간을 기준으로 합니다.
- 기능 설정 시 실제 반영까지 대략 수분이 걸릴 수 있습니다.

<span id="low-received-event-rates"></span>
#### 메시지 수신 데이터 지표가 낮은 이유
1. 앱의 메시지 알림을 허용하지 않을 때
사용자가 앱 최초 실행 시 OS(iOS, Android) 레벨 설정으로 메시지 알림을 허용하지 않으면, 메시지 수신 이벤트를 수집할 수 없습니다. (2018년 기준 평균 알림 허용 비율 iOS 43.9%, Android 91.1% <a href="https://www.accengage.com/press-release-accengage-releases-the-push-notification-benchmark-2018/" target="_blank">출처 accengage</a>)
2. 삭제된 앱의 토큰 만료가 지연되는 경우
앱이 삭제되고 토큰 만료가 바로 진행되지 않는 경우가 있습니다. 토큰 만료가 지연되면 메시지는 발송되었지만 앱은 삭제되어 수신 이벤트를 수집할 수 없습니다.
3. 인터넷 연결이 오랜 시간 동안 없을 때
휴대폰 꺼짐, 절전 모드, 네트워크 음영 지역 등 다양한 이유로 인터넷 연결이 안 되면 메시지 수신과 수신 이벤트를 수집할 수 없습니다.
4. 수신 이벤트 수집 실패
실제로 메시지를 수신했지만, 수신 이벤트를 수집 서버로 전송할 때 OS나 네트워크 환경에 따라 수집하지 못할 수도 있습니다.

### 발송 내역 저장
- 메시지 발송 내역을 지정한 Log & Crash Search에 전송하는 기능입니다.
- **AppKey**에는 사용하는 Log & Crash Search의 AppKey를 입력합니다.
- **SecretKey**에는 사용하는 Log & Crash Search의 SecretKey를 입력합니다.
- **Log Source**는 내역 저장 시 같이 남길 값을 입력합니다. 다른 로그와 구분하는 값입니다.
- **Log Level**은 발송 내역 중 특정 내역만 남길 수 있게 합니다.
     - **ALL**: 발송 성공, 실패 등 모든 내역을 남깁니다.
     - **INFO**: 만료된 토큰 발송, 발송 실패 내역만 남깁니다. 발송 성공은 남기지 않습니다.
     - **ERROR**: 발송 실패에 내역만 남깁니다.
- 연동 후 메시지 발송 내역은 콘솔에서 **Analytics > Log & Crash Search > 로그 검색**에서 확인할 수 있습니다.
- 전송되는 메시지 발송 내역은 [Log & Crash Search의 요금 정책](https://toast.com/service/analytics/log_crash_search/#price)을 따릅니다.

#### 발송 내역 로그 형식
##### Body
```
{
	"tokens" : [{
			"UID" : "User Id",
			"token" : "Device Token",
            "newToken": "New Deivce Token",
            "message": "Result Message"
		}
	],
	"payload" : {
		"priority" : "high",
		"data" : {
			"messageDeliveryReceipt" : true,
			"title" : "타이틀",
			"body" : "내용",
			"messageDeliveryReceiptData" : {
				"messageId" : 963854842757578,
				"sentDateTime" : "2017-11-06T10:41:55.619+09:00"
			}
		},
		"registration_ids" : ["토큰"]
	}
}
```
- tokens: 발송된 토큰 정보
    - UID: 사용자 아이디
    - token: 토큰
    - newToken: 새로 발급된 토큰(새로운 토큰이 있을 때만 표시)
    - message: 결과 메시지(비정상 응답일 때만 표시)
- payload: 실제 FCM이나, APNS, ADM로 발송된 메시지 내용(푸시 타입에 따라 내용이 다름)

##### Fields
- Appkey: 메시지를 발송한 푸시 앱키
- messageId: 메시지 아이디
- pushType: 푸시 타입(FCM, APNS, APNS_SANDBOX, ADM)
- sentResult: 발송 결과(SENT, INVALID_TOKEN, ERROR)
- messageErrorType: 발송 실패 유형
    - CLIENT_ERROR: 클라이언트 오류로 잘못된 발송 요청으로 발송 실패
    - EXTERNAL_ERROR: 외부 오류로, 발송 시 Google이나 Apple, Amazon 서버에서 비정상 응답으로 발송 실패
    - INTERNAL_ERROR: 내부 오류로 발송 실패
- messageErrorCause: 발송 실패 원인
    - SKIP: 잘못된 토큰이나 인증서
    - NO_TOKEN: 발송 대상이 없음
    - INVALID_TOKEN: 잘못된 토큰으로 발송 요청
    - INVALID_MESSAGE: 잘못된 메시지로 발송 요청
    - INVALID_CERTIFICATE: 인증서 만료
    - UNAUTHORIZED: 인증서 만료
    - EXPIRED_TIME_OUT: 발송 요청한 메시지가 만료
    - APNS_ERROR: APNS에서 비정상 응답
    - GCM_ERROR: FCM에서 비정상 응답
    - AGENT_ERROR: Google, Apple, Amazon 서버로 통신이 비정상
    - UNKOWN: 내부에서 알 수 없는 오류 발생

### 중복 메시지 방지 설정
- 동일한 메시지를 동일한 사용자에게 중복 요청하는 경우 발송을 제한하는 기능입니다.
- 중복 판단 기준은 메시지 타입, 내용(콘텐츠), 발신 연락처, 수신 동의 설정 가이드, 광고 표시 문구 위치, 토큰입니다.
기준에 대항하는 값이 모두 같으면 중복으로 판단하고 메시지를 발송하지 않습니다.
- 발송되지 않은 메시지는 실패 처리 됩니다. 발송 실패 원인은 'DUPLICATED_MESSAGE_TOKEN'으로 남습니다.
- 설정 탭 "중복 메시지 방지 설정"에서 설정할 수 있습니다.
- 기능 설정 시 실제 반영까지 대략 수분이 걸릴 수 있습니다.

### 광고 수신 동의 사실 안내 메시지 예약
- 광고 메시지 수신을 동의한지 만 2년이 된 토큰들에 안내 메시지를 발송하는 기능이 추가되었습니다. 
- 매달 설정한 일시에 안내 메시지가 대상 토큰들에 발송됩니다.
- 안내 메시지는 수신 동의 사실과 일시, 수신 동의 설정에 대한 방법을 반드시 포함하고 있어야 합니다.
- 광고성 메시지 수신 동의 일시 치환자(###AD_AGREEMENT_DATE_TIME###)를 본문에 넣으면 메시지 발송 시 해당 토큰의 동의 일시로 치환됩니다.

## 개인정보 수탁사 고지 안내

'고객'이 NHN Cloud Push 상품을 이용할 때, '고객' - '당사' 간 개인정보 처리에 관한 업무 위수탁 관계가 발생하는 바 정보통신망법 및 개인정보보호법에 따라 위탁자인 '고객'은 개인정보처리방침을 통해 '당사'에 개인정보를 위탁한 현황(수탁자 및 업무의내용)을 공개하여야 합니다. 이에, '당사'에서는 '고객'이 NHN Cloud Push 서비스를 이용함에 있어 관련 법령을 준수하고, 위탁 현황 미공개로 인하여 과태료 등의 불이익을 받지 않도록 아래와 같이 가이드할 수 있습니다.

[개인정보 수탁사 고지 안내] Push 서비스 이용 시 고객사에서 운영하시는 '개인정보처리방침' > 위탁 현황에 다음의 내용을 표기해주세요.
```
수탁사 : 엔에이치엔 클라우드
업무의 내용 : 푸시 메시지 발송 대행
```

* *문서 수정 내역*
    * *(2020.01.21) 메시지 수신 데이터 지표가 낮은 이유 추가*
    * *(2018.09.19) 광고 표시 문구 설명 추가*
    * *(2018.07.24) APNS 인증서 설명 추가*
    * *(2018.06.15) GCM에서 Firebase로 옮기는 설명 추가*
    * *(2017.11.23) 메시지 발송 내역 저장 설명 추가*
    * *(2017.09.21) 즉시 전송 설명 수정*
    * *(2017.07.20) 개인정보 수탁사 고지 안내 추가*
