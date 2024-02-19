## Notification> Push > Release Notes

### February 27, 2024
#### [Console]
##### Added Features
* Added FCM Service Account Credential Authentication
    * On June 20, 2024, the FCM Legacy API will be terminated. As a result, you must use the FCM HTTP (V1) API to send FCM messages, and API authentication requires **Service Account Credential**instead of a **Server Key**.
        * <a href="https://firebase.google.com/docs/cloud-messaging/migrate-v1" target="_blank">Go to the FCM Migration Guide</a>
        * Go to <a href="https://docs.nhncloud.com/ko/Notification/Push/ko/console-guide/#_1">Console Guide</a>
    * After you enroll for **Service Account Credential**, FCM messages are sent via the FCM HTTP V1 API. To continue sending with FCM after June 20, 2024, you must register **Service Account Credential**in the console.

### October 31, 2023.
#### [Console]
##### Feature Updates
* Added a SecretKey when setting up the Logging feature
    * Starting October 31, 2023, when activating the Logging feature, you must add SecretKey of the Log&Crash Search service.
    * If you are already using the feature before October 31, 2023, SecretKey input is not required as transition is scheduled.
    
### March 14, 2023
#### [API]
##### Added Features
* Added the Query Token List API
    * Added API to query token lists (v2.4).

### December 13, 2022
#### [API]
##### Added Features
* Added a paging feature when viewing general logs
  * Added pageNumber to the field list.
##### Updates
* Changed the maximum limit from 1000 to 100 when viewing failed messages

### May 10, 2022
#### [API]
##### Bug Fixes
* Fixed an issue where an error occurred if there was no `X-SECRET-KEY` header when calling v2.2 API
    * Fixed the issue so that API authentication can be done with `X-User-Access-Key-ID` and `X-Secret-Access-Key` headers.

### March 29, 2022
#### [Console]
##### Added Features
* Added a feature to provide a compressed file after splitting the file if the number of tokens exceeds 1 million when using the token file download
    * When downloading stored tokens as a file using the **Token File Download** feature in the **Token** tab, a feature to provide a compressed file after splitting the file if the number of tokens exceeds 1 million has been added.

### February 15, 2022
#### [Console]
##### Added Features
* Added a token file download feature
    * Added a feature to download stored tokens as a file using the **Token File Download** feature in the **Token** tab.
##### Bug Fixes
* Fixed an error where a UID was saved as 'UNKNOWN' while saving the delivery history
    * Fixed an error where a UID was saved as 'UNKNOWN' in the expired token history when using the **Logging** feature in the **Setting** tab.
* Fixed an error where duplicate authentication failure notification emails were sent
    * Fixed an error where, when authentication failed when sending a push message, duplicate notification emails were sent

### January 11, 2022
#### [API]
##### Added Features
* Added a receipt/confirmation feature to the Amazon Device Messaging (ADM) push type
    * Added a feature that enables receipt/confirmation when sending ADM.
##### Bug Fixes
* Fixed an error where a failure to send an iOS message with FCM was handled as a success
    * Fixed an error where, when sending a message after setting an incorrect APNS certificate to Firebase, it was handled as a success.

### October 26, 2021
#### [Console]
##### Added Features
* Added a pop-up for modifying token date and time
    * Added a pop-up that allows you to modify the date and time of the registered token.
    * It can be used to change the consent date and time of the token in order to test features such as reserving the advertisement opt-in notification message.

#### [API]
##### Added Features
* Added a cause of failure to the 'extra2' field of the statistics query API delivery failure event.
* Added a query condition to v2.4 scheduled message query API
    * Added 'deliveryFrom' and 'deliveryTo' conditions that let you query messages based on the scheduled delivery date and time.
    * Scheduled messages with schedules that fall between 'deliveryFrom' and 'deliveryTo' are retrieved.
##### Bug Fixes
* Fixed a query condition processing error in v2.4 scheduled message query API
    * Fixed an error where the 'from' and 'to' query conditions were not processed in the v2.4 scheduled message query API.

### July 27, 2021
### [Console]
#### Added Features
* Added the feature of reserving a guide message for the ad opt-in acceptance
    * Added a feature of sending a guide message to tokens that have reached two years since their last acceptance of ad opt-in. 
    * Every month at a specific time set by the user, a guide message will be sent to the target tokens.
    * The guide message must contain the information about the user's opt-in acceptance and the time of opt-in and how to set ad opt-in.
    * If you place the temporary replacer for opting in to receive advertisement messages (###AD_AGREEMENT_DATE_TIME###) in the body, when sending a message, its time will be replaced with the opt-in acceptance time of the token.
    * This can be set in **Reserve Message for Acceptance of Ad Opt-in** under the **Settings** tab.

### December 29, 2020
### [API]
* Added v2.4 Statistics Total API
     * Added a Total API to sum up the retrieved statistical data.
        <a href="https://docs.toast.com/en/Notification/Push/en/api-guide/#stats-total-api" target="_blank">Go to</a>

### June 9, 2020
### [Console]
#### Added Features 
* Added APNS JWT Authentication
    * Added JWT as method of authentication for sending APNS push messages. You can register Key ID, Team ID, Topic, or Encryption Key required to authenticate JWT on the **Certificate** tab of console.
    * With the registration of APNS JWT certificate information, registered certificate is deleted. 
    * <a href="https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/establishing_a_token-based_connection_to_apns" target="_blank">Go to Apple Developer Guide</a>

### [Doc]
#### Added Guide
* Added a guide for **Getting Authentication Information for APNS JWT**
    * <a href="https://docs.toast.com/ko/Notification/Push/ko/console-guide/#get-apns-jwt" target="_blank">Direct link</a>


### March 24, 2020
### [Console]
#### Added Features
* Updated Statistics
    * Added the **Statistics Event Key Management** tab. You can add a new statistics event key on console and set it up for message delivery. With messages sent, statistical data are collected as of configured statistics event key, and then you can search from the new statistics tab. 
        * <a href="https://docs.toast.com/en/Notification/Push/en/console-guide/#stats-event-key" target="_blank">Direct Link</a>

### [API]
* Added v2.4 API
    * Added a statistics API to query with statistics event key. Statistics APIs of v2.3 are no longer provided. 
        * <a href="https://docs.toast.com/en/Notification/Push/en/api-guide/#stats-api" target="_blank">Direct Link</a>
* Added Multi-tenant Tokens 
   * Added the multi-tenant feature allowing a token to be shared by many UIDs. You may attach '#tenant=Tenant_Information' at the end of a token for a token registration. Even if many UIDs share a same token, the token can be maintained if it has different tenant information.  


### October 29, 2019
#### [API]
##### Added Features 
* Added Badge Attribute for Android  
    * The badge attribute can be delivered to Android, as well as iOS. 
    With TOAST SDK, the badge delivered onto app icon is automatically displayed. 
* Display Messages excluding HTML for iOS
    * Currently, iOS does not show HTML within push messages, unlike Android. 
    By setting 'True' for 'content.default.style.useHtmlStyle', HTML can be removed from messages before delivered for iOS. 
* Updated Rich Messages to Separate Media for Android and iOS
    * Added 'richMessage.androidMedia' and 'richMessage.iosMedia', other than 'richMessage.media. 
    Rich message has been updated to enable separate media setting for Android and iOS.  
* Changed the 'sourceType' and 'extension' to be Optional for Rich Message Media 
    * 'richMessage.media.sourceType' and 'richMessage.media.extension' have been changed to be optional, not required. 
    No setting is required for media extension, be it external or internal, to deliver rich messages.  
* Action Definition Enabled at Click of Push Messages 
    * Actions (e.g. URL or Scheme) to be executed by the click of a push message can be defined at 'content.default.clickAction'. 
    With TOAST SDK, action is executed automatically.  

### September 24, 2019 
#### [API]
##### Bug Fixes 
* Fixed delivery error of iOS rich messages 
    * Fixed error in which image is not properly displayed on iOS if a rich message is delivered while Receive/Confirm Messages is not enabled.  


#### [Console]
##### Added Features 
* Querying Token List from Token Tab 
    * Updated to allow query of tokens from token tab without search conditions. 
* Sending Messages to iOS via FCM 
    * Messages can be sent to an iOS app which applies FCM SDK. 
        *  <a href="https://firebase.google.com/docs/cloud-messaging/http-server-ref" target="_blank">Go to FCM Guide</a>
    * By using attributes such as 'notification', 'content_available', or 'mutual_content', messages can be sent to iOS apps via FCM. 

### May 28, 2019 
#### [API]
* Improved Receive/Confirm Message data collection performance
    * We have improved message receiving/confirmation data collection performance.

### March 26, 2019 
#### [API]
##### Bug Fixes
* Fixed an error where the from (to, to) setting does not apply in the invalid token lookup API
    * Invalid token lookup API, there was an error that ignores the settings unless both from and to are set.
    * Fixed to apply period setting even if only one of from and to is set.

#### [Console]
##### Added Features
* Added duplicate message prevention function
    * Even if the exact same message is sent several times, it will not be sent for the set time.
    * Unsent messages will fail. The reason for the dispatch failure is "DUPLICATED_MESSAGE_TOKEN".
    * Duplicate criterion is message type, content (content), outgoing contact, reception agreement setting guide, advertisement display position, token.
    * Settings tab "Duplicate message prevention settings" can be set.

### February 26, 2019 
#### [API]
##### Added Features
* Added v2.3 API
    * Added Token Delete API. Can be called without Secret Key.
    * Added new push type 'FCM'. You must use 'FCM' instead of 'GCM' when making API calls.

#### [Console]
##### Bug Fixes
Fix broken, typo, link errors
    * Certificates tab Corrected errors and typo in some tooltips.
    * Fixed setting tab typo.
    * Fixed incorrect SDK guide link.

##### Added Features
* User console added.

### December 18, 2018
#### [API]
##### Bug Fixes
* Fixed an error that invalid VoIP token was not deleted normally.
    * Fixed an error that prevents APNS_VOIP, APNS_SANDBOXVOIP token from being deleted when sending a message.

### October 30, 2018 
#### [Console]
##### Added Features
* Rich message feature added to message sending page
    * You can send a rich message from the Send Message page.
        * <a href="https://docs.toast.com/en/Notification/Push/en/console-guide/#_3" target="_blank"> Go to Console Guide  </a>
    * Provide preview functionality to see how rich messages are displayed on Android and iOS.
* Added the ability to set the position of the advertisement marker
    * Added the ability to set whether to display the text that indicates that it is an advertising message in the title or content part.
    * You can set it in the setting tab "Ad display position setting".

##### Bug Fixes
* Fixed an error where token lookups are displayed in UTC
    * There was an error displaying the time in UTC when searching for tokens. Corrected to display in your browser's local time.


#### [API]
##### Added Features
* Rich message feature added to message dispatch API
    * Added the ability to display buttons, media (images, movies, sounds) in push messages.
         * <a href="https://docs.toast.com/en/Notification/Push/en/api-guide/#7" target="_blank">Go to API Guide </a>
    * Available in v2.0 message delivery APIs and in apps with the latest SDKs.

#### [SDK]
##### Android
* Added rich message function
    * Rich messages such as buttons, images, large icons, groups can be transmitted.
* Added API for reply function of rich message
    * Listener class registration API has been added to handle reply button.
* Android 9 compatible
    * Fixed bug where receive / open metrics are not collected on Android 9 devices when target version is 28 (Android 9) or higher.

##### iOS
* Added rich message function
    * Rich messages such as buttons, images, and movies can be transmitted.
* Added category setting function
    * If you set the category in the initialization and set it as the category identifier of your own in the message, you can receive the corresponding category action.
* Added indicator collection methodology
    * It is possible to collect verification indices without initialization by inputting the indicator collection information (AppKey) in the application's info.plist file.
    * It is possible to automatically transmit the reception indices by inputting the four index acquisition information (AppKey) of the info.plist file of the user Notification Service Extension. (TCPushServiceExtension extension required)
* Improved token registration
    * Only the system token is registered when the token registration request is made without initialization, and the issued token can be freely registered through the API from the service server.

##### Bug Fixes
* Error that data is missing from 1 second to 59 seconds when querying from list query API to minutes
    * As an example, if you look up data by 10:11, there is an error that the data of 11 minutes 59 seconds is missing.
    In this case, we improved to include 59 seconds.

### August 28, 2018 
#### [API]
##### Added Features
* Added Logging API
    * Added API to inquire saved data with Logging function that can be activated in Console.
    * Provides two types of APIs: general query, bulk query.
         * <a href="https://docs.toast.com/en/Notification/Push/en/api-guide/#_18" target="_blank"> Go to Log view </a>
* v2.2 API Update
    * Updated Logging API to update the latest API version to v2.2.
    * As of v2.2, API security setting is used for API authentication.
         * <a href="https://toast.com/account/api_settings" target="_blank"> Go to API Security Settings </a>
    * Supported API versions: v1.3, v2.0, v2.1, v2.2

##### Bug Fixes
* Errors that the APNS_VOIP token is deleted when the app type is set to 'Single Token' in the token setting
    * The APNS_VOIP token is a token for VoIP, so it must be managed separately from the GCM, APNS, etc. for push messages,
    When set to a single token, there was an error that APNS_VOIP was managed the same as other tokens and the APNS_VOIP token was deleted.
    * Modified so that the APNS_VOIP tokens and the GCM, APNS, and TENCENT tokens are managed separately.
* Improved error in statistics API timeout in some projects
    * There was a timeout error in some projects. Fixed timeout not to occur through optimization.

### July 24, 2018 
#### [API]
##### Updates
* Improved response message
    * Improved to better understand the cause of failure in header.resultMessage of Response Body by adding more details.

#### [SDK]
##### Android
* Amazon Device Messaging support

##### iOS
* VoIP type support
* Some API changes due to VoIP type addition

<br>

### June 26, 2018 
#### [Console]
##### Added Features
* Add Amazon Device Messaging (ADM) push type
    * Added ADM push type to send push messages to Amazon device (Kindle Fire).
    * You can register your app on the Amazon developer site, get a Client ID, Client Secret, and register it.
     <a href="https://docs.toast.com/en/Notification/Push/en/console-guide/#adm-client-id-client-secret" target="_blank"> ADM Guide Shortcut </ a>


#### [API]
##### Added Features
* Add Amazon Device Messaging (ADM) push type

##### Bug fixes
* Fixed an error that caused some targets to be missing when sending a promotional push message
    * Fixed on May 30, 2018 as a hotfix.
    * Fixed an error where some destinations were missing when sending a push message with a shipping logic error.
* Fixed an error that duplicate reception when using local time function when sending reservation message
    * Fixed an error sending a reservation message in a non-existent time zone when using the local time feature.

#### [SDK]
##### Android
* Apply latest Tencent SDK (3.2.3)
* API improvements

##### iOS
* Improvement of indicator collection and transmission function
Automate message check indicator collection and transmission

<br>

### May 29, 2018 
#### [Console]
##### Updates
* Add message ID
    * Added message ID to the details part of popup when selecting message.

#### [API]
##### Added Features
* Added v2.1 token lookup API
    * You can check the device ID you collect when you register the token.
    * You can check the date of the most recent registration request for this token.

##### Updates
* Advertising
