## Notification > Push > コンソール使用ガイド

Pushサービスを活用するには、先に他社プッシュ通知サービスの証明書を登録する必要があります。

現在サポートしているプッシュ通知サービス証明書は次の通りです。
- GCM - Google Cloud Messaging (Firebase Cloud Messaging)
- APNS - Apple Push Notification Service
- ADM - Amazon Device Messaging

## 証明書管理

証明書を管理するためにコンソールで**Notification > Push > 証明書**をクリックします。


<span id="get-fcm-service-account-credential"></span>

### FCM Service Account Credential
Android端末にプッシュ通知メッセージを送信するためには**Service Account Credential**が必要です。
**Service Account**(サービスアカウント)は、一般的にGoogle CloudとA2A(Application to Application)通信する時に使用する特別なタイプのアカウントです。

#### FCM Service Account Credential JSONファイルを取得する

1. [Google Firebase Console](https://console.firebase.google.com)にアクセスします。
2. プロジェクト追加を行い、新しいプロジェクトを作成します。
3. 作成されたプロジェクトに移動します。
4. ページ左上のプロジェクト概要の横にある**歯車 > プロジェクト設定**をクリックします。
5. **サービスアカウント**を選択します。
6.  Firbase Admin SDK項目で**新しい秘密鍵の作成**をクリックして、新しい**Service Account Credential** JSONファイルをダウンロードします。

#### FCM Service Account Credential JSONファイルの登録
1. コンソールで**Notification > Push > 証明書**をクリックします。
2. ダウンロードしたJSONファイルを開いて内容をコピーします。
2. コピーした内容を**FCM Service Account Credential**項目に貼り付けて**登録**をクリックします。

<span id="get-apns-jwt"></span>

### APNS JWT認証情報の取得
iOS端末にプッシュ通知メッセージを送るにはApple Developerサイトで発行された暗号キーとキーID(Key ID)、チームID(Team ID、App ID Prefix)、トピック(Topic)が必要です。

#### APNS暗号キーの取得
1. **Apple Developerコンソール**から**Certificates, IDs & Profiles**へ移動します。
2. **Keys**を選択します。
3. **Create a key**を選択します。
4. **Register a New Key**で、キー名を入力、**ENABLE**項目で**Apple Push Notifictions service (APNs)**を選択し、**Continue**を押します。
5. 内容を確認し、**Register**を選択します。
6. **Download**を選択して暗号キーファイルをダウンロードします。

#### キーIDの取得
1. **Apple Developerコンソール**から**Certificates, IDs & Profiles**へ移動します。
2. 発行されたキー(Key)を選択します。
3. **View Key Details**項目で確認できます。

#### チームIDの取得
1. **Apple Developerコンソール**から**Certificates, IDs & Profiles**へ移動します。
2. **Identifiers**を選択します。
3. **Edit your App ID Configuration**項目で確認できます。

#### トピック
JWTを利用して認証を行うにはトピック(Topic)が必要です。トピックはアプリのバンドルID(Bundle ID)です。

### APNS証明書

iOS端末にプッシュ通知メッセージを送信するには、Apple Developerサイトで発行したAPNS証明書が必要です。

#### APNS証明書の作成、インポート

1. Macで**キーチェーン**を実行します。
2. **チェーンアクセス > 証明書のサポート > 認証機関**で**証明書リクエスト**ボタンをクリックします。
3. **ユーザーメールアドレス**にApple Developer IDなどの必要な情報を入力します。**ディスク指定済**を選択し、**本人がキーペア情報保存**を選択して**続ける**をクリックします。
4. **キーペア情報**でキーサイズ、アルゴリズムを選択し、**続ける**をクリックします。 CSR(Certificate Signing Request)ファイルが作成されます。
5. [Apple Developer Certificates](https://developer.apple.com/account/ios/certificate/certificateList.action)に移動します。
6. 右上にある**+**(Add)ボタンをクリックして、Certificate発行ページに移動します。
7. **Select Type > Request > Generate**タブで、**キーチェーン**で作成したCSRファイルをアップロードします。**Generate**ボタンをクリックします。
8. 証明書を作成してダウンロードできます。承認権限がない場合は、承認を受けてダウンロードできます。
9. 証明書をダウンロードした後、**キーチェーン > 証明書**から証明書を選択します。
10. 証明書で右クリックして、**送信**をクリックします。
11. 保存位置を設定し、**保存**をクリックします(ファイル形式：.p12)。
12. 送信した項目を保護するのに使用するパスワードを入力すると、APNS証明書の作成が完了します。

#### APNS証明書登録

1. コンソールで**Notification > Push > 証明書**をクリックします。
2. **APNS証明書**の下にある**証明書**で**ファイル選択**をクリックします。
3. **パスワード**に証明書パスワードを入力します。
4. **登録**をクリックします。

#### APNS注意事項

##### APNS(Production)とAPNS_SANDBOX(Development)の違い
- Production Provisioning Profileでビルドしたアプリは、APNS(Production)を利用する必要があり、Development Provisioning Profileでビルドしたアプリは、APNS_SANDBOX(Development)を利用する必要があります。
 これを守らなければ、プッシュメッセージを正常に受信できません。
- Pushに登録したAPNS証明書とアプリをビルドする時に使用したProvisioning Profileが異なるため、メッセージ送信に失敗するケースがほとんどです。
   APNS(Production) = Production Provisioning Profile  
   APNS_SANDBOX(Development) = Development Provisioning Profile  
- さらに詳細な説明は、次のリンクを参照してください。  
   [APNs Overview](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)  
   [App Distribution Quick Start](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppStoreDistributionTutorial/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013839)

##### APNSメッセージの送信失敗原因
- 証明書が満了している時
- 無効な証明書を登録した時
- アプリでプッシュメッセージの受信を拒否した時
- デバイスがインターネットに接続されていない時

### ADMの資格証明

Kindle Fireアプリにプッシュ通知メッセージを送信するには、アプリのClient IDとClient Secretが必要です。

#### ADMアプリケーションおよびプロファイル登録(Client Id, Client Secret獲得)
1. [ADM開発者コンソール](https://developer.amazon.com/home.html)にアクセスします。
2. ページ左上にある**APP & SERVICES**をクリックし、下にある**Add a New App**ボタンをクリックします。
3. アプリが作成されたら、中間タブにある**Device Messaging**をクリックし、**Create a New Security Profile**ボタンをクリックします。
4. プロフィール作成完了後、中間タブにある**Security Profiles**をクリックし、**View Security Profile** ボタンをクリックします。
5. **General**タブでClient IDとClient Secret値を確認できます。

#### ADM Kindle設定情報の登録(API keyの取得)
1. **Security Profiles**タブをクリックし、中間にある**Android/Kindle Setting**タブをクリックします。
2. App Key Name、Package、MD5 Signature、SHA256 Signature情報を入力します。
3. 下記のコマンドでMD5、SHA256情報を照会できます。
```
> keytool -list -v -keystore {keystoreFileName}

キー保存場所のパスワード入力：
キー保存場所のタイプ：JKS
キー保存場所の提供者：SUN

キー保存場所に1個の項目が含まれています。

エイリアス名：androiddebugkey
作成日：2018. 5. 9
項目タイプ：PrivateKeyEntry
証明書チェーンの長さ：1
証明書[1]:
所有者：C=US, O=Android, CN=Android Debug
発行者：C=US, O=Android, CN=Android Debug
一連番号：1
適した開始日：Wed May 09 19:59:46 KST 2018終了日：Fri May 01 19:59:46 KST 2048
証明書の指紋：
         MD5：xxxx
         SHA1：xxxx
         SHA256：xxxx
署名アルゴリズム名：SHA1withRSA
主体公開鍵アルゴリズム：1024bit RSA
バージョン：1
```
- 登録完了後、**Show**ボタンをクリックするとAPI key情報を照会できます。


## メッセージ送信

証明書とトークンを正常に登録した後、コンソールで**Notification > Push > メッセージ**をクリックし、**追加**ボタンをクリックします。フォーマットを作成して、メッセージを即時に送信できます。

即時送信のフォーマット構成は次のとおりです。

### 対象

| 名前           | 内容                             |
| ---------------------- | ---------------------------------------- |
| 対象タイプ        | ALL、UID、TAGのうち1つを選択できます。        |
| To                | UIDを入力できます。 Target > TypeでUIDを選択してUIDに転換できます。カンマ(',')をセパレータにして、複数入力できます。 |
| UIDs              | カンマ(',')をセパレータにして、複数入力できます。          |
| タグ      | **選択**ボタンをクリックして、タグを選択できます。           |
| 選択されたタグ  | 選択されたタグが表示されます。再度クリックして選択を解除できます。   |
| 国コード   | 国コードを入力できます。カンマ(',')をセパレータにして複数入力できます。 |
| プッシュタイプ   | GCM、APNS、APNS Sandbox、ADM複数選択できます。 |

### オプション

| 名前           | 内容                             |
| ---------------------- | ---------------------------------------- |
| TTL(Time To Live) | メッセージの送信有効時間です。設定した時間中に送信が遅延する場合、自動的に失敗処理されます。単位は分です。0の場合、送信遅延で失敗処理されません。 |

### メッセージ

| 名前           | 内容                             |
| ---------------------- | ---------------------------------------- |
| エディタのタイプ | SIMPLE、JSONの2つのタイプがあります。JSONを選択すると、直接メッセージを作成できます。 |
| メッセージタイプ | NOTIFICATION、ADの2つのタイプがあります。ADの場合は、広告性プッシュメッセージ受信に同意したユーザーにのみメッセージが送信されます。 |

### リッチメッセージ作成
プッシュメッセージにボタン、イメージなどを入れて多様な形式でプッシュメッセージを送信できます。
入力したメッセージがAndroidとiOSスマートフォンでどのように表示されるかを確認できます。
v1.7以上のSDKが適用された場所でのみ使用できる機能です。

![push_04_201812](https://static.toastoven.net/prod_push/12-10/push_04_201812_en.png)

#### 1. ボタン

|名前|内容|
|---|---|
| 名前 | ボタンの名前 |
| タイプ | ボタンのタイプ、レスポンス(REPLY)、アプリを開く(OPEN_APP)、URLを開く(OPEN_URL)、閉じる(DISMISS) |
| 送信ボタン名 | ボタンタイプがレスポンスボタンの場合、iOSで送信ボタン名を設定できます。 |
| リンク | ボタンを押した時に移動したり実行するリンクです。ボタンタイプがURLを開く場合に該当します。 |
| ヒント | ボタンについての説明です。 |

##### ボタンのタイプ

- レスポンス
    - ダイレクト返信機能を実行します。
    - ユーザーが転送ボタンをタップした時、アクションリスナーにユーザーが入力したテキストが伝達されます。
- アプリを開く
    - アプリが実行されます。
    - アクションリスナーを通してメッセージ全文が伝達されます。メッセージ内に情報を入力し、特定ページに移動などの機能を実装できます。
- URLを開く
    - リンク項目に入力されたURL(https://...)またはScheme(scheme://...)を実行します。
    - URLを入力すると、Webブラウザが実行され、該当URLをロードします。
    - スキーム(Scheme)を入力すると、アプリにあらかじめ定義しておいたスキームを実行します。
- 閉じる
    - 通知を閉じます。

#### 2. メディア

|名前|内容|
|---|---|
| 位置 | メディアがある場所。'REMOTE'または'LOCAL' |
| アドレス | メディアがあるアドレス。URL、URIなど。 |
| タイプ | イメージ、 GIF、動画、音声を選択できます。 (Androidはイメージのみ可能) |
| 拡張子 | メディアの拡張子 | .png, .aviなど、メディアの拡張子です。 |
| 広げる | メディアの広げる機能、Androidでのみ可能です。 |

##### メディアファイルの指定

- 外部
    - 入力したURLに該当するメディアファイルをダウンロードして使用します。
    - Android
        - Android Pie以上でHTTPを使用するには、<a href="http://docs.toast.com/ja/TOAST/ja/toast-sdk/push-android/#android-p">network-security-config</a>を設定する必要があります。
    - iOS
        - iOS 9以上でHTTPを使用するには、Info.plistファイル内にATS(App Trasport Secuirty)を設定する必要があります。
        - 実際のメディアファイルの拡張子情報をextension項目に入力する必要があります。(例：jpg、png、mp4、wav…)
- 内部
    - アプリ内に含まれているリソースを使用します。
    - Android
        - ファイルは'res > drawable'にあらかじめ追加する必要があります。
        - リソース識別子を通してアクセスするため、メッセージ作成時、'richMessage.media.source'に拡張子を除いたファイル名を入力します。
        - Androidでは、ファイル名がリソースの識別子として使用されるため、拡張子が異なっても同じファイル名を使用できません。サポートするイメージフォーマットは、png、jpg、gifです。(現在ビデオ、オーディオ形式のメディアはサポートしません。)
    
    - iOS
        - リソースは、リッチメッセージを作成する <a href="http://docs.toast.com/ja/TOAST/ja/toast-sdk/push-ios/#notification-service-extension">Notificaiton Service Extension</a>プロジェクトにあらかじめ追加する必要があります。
        - Xcodeでファイルまたはディレクトリを'NotificationServiceExtension'プロジェクトに追加します。
        - 'Build Phases > TARGETS'で、ファイルが正常に追加されているかを確認します。
        - バンドルリソースを通してアクセスするため、拡張子を含めたファイル名が必要です。
        - メッセージ作成時、'richMessage.media.source'に追加したファイル名を入力します。

##### メディアタイプ
- イメージ

| | Android | iOS |
| - | - | - |
| サポート形式 | JPEG、PNG、GIF | JPEG、PNG、GIF |
| GIFアニメーション | サポートしない | サポートする |
| ファイルサイズ | 制限なし | 10MB |
| 推奨事項 | 2：1比率の横イメージ推奨<br>Small：512x256<br>Medium：1024x512<br>Large：2048x1024 | 横イメージ推奨<br>最大サイズ：1038x1038 |

- 動画

| | Android | iOS |
| - | - | - |
| サポート形式 | サポートしない | MPEG、MPEG3Video、MPEG4、AVIMovie |
| ファイルサイズ | サポートしない | 50MB |

- 音声

| | Android | iOS |
| - | - | - |
| サポート形式 | サポートしない | WaveAudio、MP3、MPEG4Audio |
| ファイルサイズ | サポートしない | 5MB |

#### 3. 大アイコン

Androidでのみ提供する機能です。

|名前|内容|
|---|---|
| 位置 | 存在する場所。'REMOTE'または'LOCAL' |
| アドレス | イメージが存在するアドレス。URL、URIなど。 |

#### 4. グループ

Androidでのみ提供する機能です。

|名前|内容|
|---|---|
| キー | グループのキー |
| 説明 | グループについての説明 |

#### 5. 通知音
| | Android | iOS |
| - | - | - |
| サポート形式 | MP3、PCM/WAVE、Vorbis | Linear PCM、MP4(IMA/ADPCM)、μ-law、aLaw |
| 拡張子 | .mp3、.wav、.ogg | .aiff、.wav、.caf |
| プレイ時間 | 制限なし | 30秒 |

- アプリ内に含まれているリソースのみ指定可能です。(外部URL使用不可)
- Android
    - リソースは'res > raw'フォルダにあらかじめ追加する必要があります。
    - リソース識別子を通してアクセスするため、ファイル拡張子は無視されます。
    - Android Oreo未満でのみ動作します。
- iOS
    - リソースは、アプリプロジェクトのバンドルリソースにあらかじめ追加する必要があります。
    - バンドルリソースを通してアクセスするため、拡張子を含めたファイル名が必要です。

## 予約送信

Pushの予約送信機能を使用すると、希望する時間にメッセージを送信できます。メッセージを予約送信するには、コンソールで**Notification > Push > 予約**をクリックします。

予約送信のフォーマットは次のとおりです。

| 名前 | 内容                             |
| ------------ | ---------------------------------------- |
| 日程        | EVERY_DAY：StartDateからEndDateまで、毎日指定された時間にメッセージを送信します。<br/> EVERY_WEEK：Reservation Conditionに曜日を指定して、毎週指定した曜日にメッセージを送信します。<br/> EVERY_MONTH：Reservation Conditionに日付を指定して、毎月指定した日にメッセージを送信します。 |
| 曜日 | 曜日を選択できます。    |
| 日 | カンマ(',')をセパレータにして日付(月)を入力できます。 |
| 予約時間      | メッセージを送信する時間を入力します。時間形式は'HH:mm'です。   |
| 開始日       | メッセージの送信を開始する日付を入力します。カレンダーで日付を選択できます。形式は'YYYY-MM-DD'です。 |
| 終了日       | メッセージを送信する最後の日付を入力します。カレンダーで日付を選択できます。形式は'YYYY-MM-DD'です。 |
| 現地時間      | trueに設定すると、現地時間基準でメッセージを送信します。        |

### トークン照会

プッシュタイプを選択し、トークンを入力して照会および削除ができます。
トークン削除機能は、現在コンソールAPIタブとトークンタブでのみサポートされます。

### Public APIs

Public APIで提供されるAPI呼び出しです。
トークン作成、メッセージ送信、フィードバック照会APIの呼び出しが提供されます。
APIのバージョンを選択して、バージョンごとにリクエストできます。
APIの詳細は、[APIガイド](./api-guide/)で確認できます。

## トークン

トークンを特定UIDに追加できます。
UID、トークンごとに検索できます。

コンソールで**Notification > Push > トークン**をクリックします。

### トークンの追加

![push_11_201812](https://static.toastoven.net/prod_push/12-10/push_11_201812_en.png)

1. **追加**ボタンをクリックします。
2. 追加モーダルウィンドウでUIDとトークンを入力します。
3. プッシュタイプおよび残りのオプションを確認し、**確認**ボタンを押してトークンを追加します。

### トークンの検索

**検索タイプ**を**TOKEN**に選択すると現れるリストから、希望するプッシュタイプを選択してトークンを検索できます。

![push_12_201812](https://static.toastoven.net/prod_push/12-10/push_12_201812_en.png)

- **Details**ボタンをクリックすると、詳細項目を確認できます。

### UID検索

**検索タイプ**を**UID**に選択して、UIDを検索できます。

![push_13_201812](https://static.toastoven.net/prod_push/12-10/push_13_201812_en.png)

- 一部のみ一致しても検索結果に表示されます。
- **Details**ボタンをクリックすると、詳細項目を確認できます。

## タグ

UIDにタグを追加してUIDを管理できます。メッセージ送信時、タグを選択して送信対象を指定できます。タグはNotificationサービス全域で使用されます。タグは最大2,048個まで作成できます。1つのUIDにタグを16個まで追加できます。

コンソールで**Notification > Push > タグ**をクリックします。

### タグの追加

![push_15_201812](https://static.toastoven.net/prod_push/12-10/push_15_201812_en.png)

1. **追加**ボタンをクリックしてタグ名を入力します。
2. **確認** ボタンをクリックしてタグを追加します。

### タグがついたUIDの管理
タグをクリックすると、タグがついたUIDのリストを確認できます。

![push_16_201812](https://static.toastoven.net/prod_push/12-10/push_16_201812_en.png)

1.  **追加**ボタンをクリックします。
2. UIDを入力して**確認**ボタンをクリックします。
	- UIDは一度に1,000個まで追加できます。

<span id="stats-event-key"></span>

## 統計イベントキー管理
**統計イベントキー管理**タブで統計イベントキーを管理できます。作成時に名前、説明、イベント収集期間を設定できます。イベント収集期間を設定しなかった場合、収集期間は無期限に設定されます。作成された統計イベントキーはメッセージ送信時に設定できます。統計イベントキーが設定されたメッセージの送信結果は、**統計**タブにおいて統計イベントキーで検索できます。

<span id="stats"></span>

## 統計
過去30日間に収集されたメッセージ受信/確認データを検索できます。コンソールで**統計**を選択します。メッセージ、トークンの登録や削除に対する統計を検索できます。

1. 検索条件に期間を設定できます。
    - 簡単期間設定または手動設定時、1分を最短期間、 30日を最長期間に設定できます。
2. グラフが表示される方式をタイプに設定できます。
    - デフォルトでは設定された期間に応じて自動的に最適化されたタイプでグラフを表示します。
3. メッセージ統計では統計イベントキー、プッシュタイプで検索できます。

## 設定

プッシュサービスを設定できます。

コンソールで**Notification > Push > 設定**をクリックします。

### 広告表示文言位置設定

- 広告性メッセージ送信時に表示される広告表示文言の位置を設定できます。
- タイトル(title)
    - 基本設定です。
    - タイトルに'(広告)'表示と連絡先が表示されます。
- 内容(body)
    - 内容に'(広告)'、連絡先、受信同意撤回方法が表示されます。
- 機能を設定すると、実際の反映まで数分かかることがあります。

### トークン設定

- トークン満了期間設定
    - 設定した期間に登録リクエストがないトークンをメッセージ送信対象から除外します。
    - デフォルト値は12ヶ月です。
    - 設定した期間にアプリを使用しないユーザーのトークンがメッセージ送信対象から自動的に除外され、送信料金を節約できます。

- アプリタイプ設定
    - 連携したアプリのタイプに応じて、トークンを管理します。
    - 多重トークン
        - 基本設定です。
        - アプリのユーザーが複数の端末にインストールされたアプリを同時に使用できるアプリで、一人のユーザーは複数のトークンを持つことができます。
        - 例えば、ユーザーが携帯電話とタブレットを使用する場合、2つのトークンを持つことができ、携帯電話とタブレットにプッシュメッセージを送信します。
    - 単一トークン
        - ユーザーが一度に1つの端末でのみアプリを使用できます。一人のユーザーは1つのトークンのみ持つことができます。
        - 例えば、ユーザーが携帯電話とタブレットを使用する場合、1つのトークンのみ持つことができ、どちらか一方にプッシュメッセージを送信します。

- 機能を設定すると、実際の反映まで数分かかることがあります。

<span id="message-delivery-receipient"></span>
### メッセージ受信/確認

- メッセージの受信および確認、データ収集(Message Delivery Receipt)機能を有効にできます。
- 有効になった機能を動作させるにはクライアントSDK v1.4以上が適用されている必要があります。AndroidはSDKを適用するだけで機能が動作します。iOSは追加の処理が必要です。
    - <a href="https://docs.toast.com/ja/TOAST/ko/toast-sdk/push-ios/#_19"  target="_blank">iOSクライアントSDKガイド</a>
- 収集されたデータは、**統計**タブで確認できます。
- データの収集時間は、端末の時間を基準にします。
- 機能を設定すると、実際の反映まで数分かかることがあります。

<span id="low-received-event-rates"></span>
#### メッセージ受信データ指標が低い理由
1. アプリのメッセージ通知を許可していない時
ユーザーがアプリを初めて実行した時、OS(iOS、Android)レベル設定でメッセージ通知を許可していない場合、メッセージ受信イベントを収集できません。(2018年の平均通知許可比率iOS 43.9%、Android 91.1% <a href="https://www.accengage.com/press-release-accengage-releases-the-push-notification-benchmark-2018/" target="_blank">出典accengage</a>)
2. 削除されたアプリのトークンの無効化が遅延した場合
アプリが削除されてもトークンの無効化がすぐに行われない場合があります。トークンの無効化が遅延すると、メッセージは送信されますが、アプリが削除されているため受信イベントを収集できません。
3. インターネット接続が長時間ない時
携帯電話の電源がオフ、節電モード、電波が届かない地域など、さまざまな理由でインターネット接続がない場合、メッセージの受信と受信イベントの収集ができません。
4. 受信イベント収集失敗
メッセージを受信しても、受信イベントを収集サーバーに転送する時、OSやネットワーク環境によって収集できない場合があります。

### 送信履歴の保存
- メッセージ送信履歴を指定したLog & Crash Searchに転送する機能です。
- **Appkey**には、使用するLog & Crash SearchのAppKeyを入力します。
- **SecretKey**には使用するLog & Crash SearchのSecretKeyを入力します。
- **Log Source**は、履歴保存時に一緒に残す値を入力します。他のログと区別する値です。
- **Log Level**は、送信履歴のうち特定の履歴のみを残せるようにします。
     - **ALL**：送信成功、失敗など、すべての履歴を残します。
     - **INFO**：満了したトークン送信、送信失敗履歴のみを残します。送信成功は残しません。
     - **ERROR**：送信失敗履歴のみを残します。
- 連携後、メッセージ送信履歴はコンソールの**Analytics > Log & Crash Search > ログ検索**で確認できます。
- 送信されるメッセージ送信履歴は[Log & Crash Searchの料金ポリシー](https://toast.com/service/analytics/log_crash_search/#price)に従います。

#### 送信履歴のログ形式
##### Body
```
{
	"tokens" : [{
			"uid" : "User Id",
			"token" : "Device Token",
            "newToken": "New Deivce Token",
            "message": "Result Message"
		}
	],
	"payload" : {
		"priority" : "high",
		"data" : {
			"messageDeliveryReceipt" : true,
			"title" : "タイトル",
			"body" : "内容",
			"messageDeliveryReceiptData" : {
				"messageId" : 963854842757578,
				"sentDateTime" : "2017-11-06T10:41:55.619+09:00"
			}
		},
		"registration_ids" : ["トークン"]
	}
}
```
- tokens：送信されたトークンの情報
    - uid：ユーザーID
    - token：トークン
    - newToken：新たに発行されたトークン(新しいトークンがある時のみ表示)
    - message：結果メッセージ(異常レスポンスの時のみ表示)
- payload：実際にFCMや、APNS、ADMに送信されたメッセージ内容(プッシュタイプに応じて内容が異なる)

##### Fields
- Appkey：メッセージを送信したプッシュアプリケーションキー
- messageId：メッセージID
- pushType：プッシュタイプ(FCM、APNS、APNS_SANDBOX、ADM)
- sentResult：送信結果(SENT、INVALID_TOKEN、ERROR)
- messageErrorType：送信失敗タイプ
    - CLIENT_ERROR：クライアントエラー。無効な送信リクエストにより送信失敗
    - EXTERNAL_ERROR：外部エラー。送信時にGoogleやAppleサーバーで異常レスポンスにより送信失敗
    - INTERNAL_ERROR：内部エラーで送信失敗
- messageErrorCause：送信失敗の原因
    - SKIP：無効なトークンや証明書
    - NO_TOKEN：送信対象なし
    - INVALID_TOKEN：無効なトークンに送信をリクエスト
    - INVALID_MESSAGE：無効なメッセージで送信をリクエスト
    - INVALID_CERTIFICATE：証明書の満了
    - UNAUTHORIZED：証明書の満了
    - EXPIRED_TIME_OUT：送信リクエストしたメッセージが満了
    - APNS_ERROR：APNSで異常レスポンス
    - GCM_ERROR：FCMで異常レスポンス
    - AGENT_ERROR：Google、Apple、ADMサーバーへの通信が異常
    - UNKNOWN：内部で不明なエラーが発生

### 重複メッセージ防止設定
- 同じメッセージを同じユーザーに重複リクエストする場合に送信を制限する機能です。
- 重複判断基準はメッセージタイプ、内容(コンテンツ)、発信連絡先、受信同意設定ガイド、広告表示文言位置、トークンです。
基準については、値がすべて同じであれば重複と判断し、メッセージを送信しません。
- 送信されなかったメッセージは失敗処理されます。送信失敗原因は'DUPLICATED_MESSAGE_TOKEN'に残ります。
- 設定タブ"重複メッセージ防止設定"で設定できます。
- 機能を設定すると、実際の反映まで数分かかることがあります。

### 広告受信同意事実案内メッセージ予約
- 広告メッセージの受信に同意してから満2年が経ったトークンに案内メッセージを送信する機能が追加されました。 
- 毎月設定した日時に案内メッセージが対象トークンに送信されます。
- 案内メッセージは受信同意事実と日時、受信同意設定の方法を必ず含める必要があります。
- 広告性メッセージ受信同意日時識別子(###AD_AGREEMENT_DATE_TIME###)を本文に挿入すると、メッセージ送信時に該当トークンの同意日時に置換されます。

## 個人情報受託会社の告知案内

顧客'がNHN Cloud Pushサービスを利用する時、'顧客'と'当社'の間で個人情報処理に関する業務の委託/受託関係が発生しますので、韓国情報通信網法および個人情報保護法に従って、委託者である'顧客'は個人情報処理方針を通して'当社'に個人情報を委託した状況(受託者および業務の内容)を公開する必要があります。よって'当社'では'顧客'がNHN Cloud Pushサービスを利用するにおいて関連法令を遵守し、委託状況の未公開により過料などの不利益を被らないように下記のように案内できます。

[個人情報受託会社の告知案内] Pushサービス利用時、顧客の会社で運営する'個人情報処理方針' > 委託状況に次の内容を表記してください。
```
受託会社：NHN Cloud
業務の内容：プッシュメッセージ送信代行
```

* *文書修正履歴*
    * *(2018.09.19)広告表示文言の説明を追加*
    * *(2018.07.24) APNS証明書の説明を追加*
    * *(2018.06.15) GCMからFirebaseに移行する説明を追加*
    * *(2017.11.23)メッセージ送信履歴の保存説明を追加*
    * *(2017.09.21)即時送信の説明を修正*
    * *(2017.07.20)個人情報受託会社の告知案内を追加*
    
