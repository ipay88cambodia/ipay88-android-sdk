# IPay88 (Cambodia) Plc.
We accept online payments from various methods, such as:
* Credit or Debit cards (Visa, Mastercard, JCB, Diners Club & UnionPay, etc.)
* e-Wallets (KHQR, Wing, PiPay, eMoney, Alipay, WeChat Pay, etc.)
* Online Banking (ACLEDA XPay, CAMPU Direct Debit, Chip Mong Pay, AMK Online Card, etc.)
* [Appendix I (1. PaymentId)](#1-paymentid)
* Download the Demo App from [here](/app)

## IPay88 SDK for Android
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/kh.com.ipay88.sdk/ipay88-android-sdk/badge.svg)](https://maven-badges.herokuapp.com/maven-central/kh.com.ipay88.sdk/ipay88-android-sdk)

### SDK Payment Flow
1. Your application initializes the library.
2. After buyers complete their payments, the library returns a callback to your application with the status of the payment and the transaction ID.
3. After the library flow is complete, an activity result will be posted to be received by your application.


## Table of Contents
### 1. Requirements
1. Please share your App Information with the IPay88 team or submit this form [Mobile App Information](https://forms.gle/HZNb8JokStT3HFRe7):
- AppId/Package Name (E.g: `kh.com.ipay88.sdk.demo`) inside module `build.gradle` or `AndroidManifest.xml`
```gradle
android {
    defaultConfig {
        applicationId "xxx.xxx.xxx"
    }
}
```
- App Name (Optional)
    - For showing on the return button on some banks app. Eg: `RETURN TO LEGEND CINEMA`
2. Then IPay88 team will set up your AppId in the IPay88 system to allow you to use the IPay88 Mobile Payment channel. 
3. And then IPay88 team will share these credentials:
    + Merchant Code (KHxxxxxx)
    + Merchant Key  (XXxxxxxx)
    + ClientAppSecret (IPAY88-xxxxxxxxxxxxxxxxxxxxxxxxx). Please refer to [2.2 ClientAppSecret inside AndroidManifest.xml](#22-clientappsecret-inside-androidmanifestxml)
    + WeChat Pay AppId (wxxxxxxxxxxxxxxxxx) (Optional). Please refer to [1. WeChat In-App Payment Configuration](#1-wechat-in-app-payment-configuration)

### 2. Setup
#### 2.1 Dependencies
##### What's the `-SNAPSHOT` version?
- It's working fine with the Partner Bank's UAT Environment which has an invalid SSL Certificate.
- Do not use the `-SNAPSHOT` version for your Production App.

##### Step 1. Add the Nexus repository (for `-SNAPSHOT` version only)
+ Add it in your `settings.gradle` at the end of repositories:
```gradle
dependencyResolutionManagement {
    repositories {
        ...
        maven {
            url "https://s01.oss.sonatype.org/content/repositories/snapshots/"
        }
    }
}
```
##### Step 2. Add the IPay88 Payment SDK dependency
+ Add it to your module `build.gradle` inside dependencies:
```gradle
dependencies {
    implementation 'kh.com.ipay88.sdk:ipay88-android-sdk:2.0.0-SNAPSHOT'
}
```

#### 2.2 ClientAppSecret inside `AndroidManifest.xml`
```xml
<manifest>
    <application>
        <meta-data
            android:name="kh.com.ipay88.sdk.ClientAppSecret"
            android:value="IPAY88-xxxxxxxxxxxxxxxxxxxxxxxxx" />
    </application>
</manifest>
```

#### 2.3 Permissions inside AndroidManifest.xml
```xml
<manifest>
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE"/> 
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
</manifest>
```


### 3. Payment Request & Response
#### 3.1 Payment Request
##### 3.1.1 Payment Request Object
```java
public class kh.com.ipay88.sdk.models.IPay88PayRequest
```

##### 3.1.2 Payment Request Methods
Method Name                         |  Required         |   Description
-------------                       | :---------------: | -------------
setEnvironment(Environment value)   |                   |   Swtich between SANDBOX (default) & PRODUCTION environment. Please refer to [3.1.3 Environment](#313-environment)
setMerchantCode(String value)       |   &#10003;        |   Merchant Code that provided by IPay88, E.g. KH00001
setMerchantKey(String value)        |   &#10003;        |   Merchant Key that provided by IPay88.
setPaymentId(int value)             |   &#10003;        |   PaymentId is the value to request payment method to appear on IPay88 landing page. Please refer to [Appendix I (1. PaymentId)](#1-paymentid)
setRefNo(String value)              |   &#10003;        |   Reference number for merchant reference purposes, should be unique for each transaction.
setAmount(double value)             |   &#10003;        |   Final Amount to pay and in 2 decimal point, E.g. 10.90
setCurrency(Currency currency)      |   &#10003;        |   Currency of transaction. Please refer to [3.1.4 Currency](#314-currency)
setProdDesc(String value)           |   &#10003;        |   Simple Product Description. Note: Special characters is not allowed.
setUserName(String value)           |   &#10003;        |   Customer name in merchant's system. E.g. John Woo
setUserEmail(String value)          |   &#10003;        |   Customer email address in merchant's system with valid email format, E.g. johnwoo@yahoo.com
setUserContact(String value)        |   &#10003;        |   Customer contact number in merchant's system, E.g. 60123436789
setRemark(String value)             |                   |   Remark for particular transaction. Note: Special characters is not allowed.
setBackendURL(String value)         |   &#10003;        |   Specify a valid merchant callback URL when payment success (Please refer to [6. Backend Post Feature](#6-backend-post-feature)). E.g. http://www.myshop.com/backend_page.php

##### 3.1.3 Environment
```java
public enum Environment {
    SANDBOX,
    PRODUCTION
}
```

##### 3.1.4 Currency
```java
public enum Currency {
    KHR,
    USD
}
```

#### 3.2 Payment Response
##### 3.2.1 Payment Response Object
```java
public class kh.com.ipay88.sdk.models.IPay88PayResponse
```

##### 3.2.2 Payment Response Methods
Method Name                         |  Required         |   Description
-------------                       | :---------------: | -------------
String getMerchantCode()            |   &#10003;        |   Merchant Code provided by iPay88 and use to uniquely identify the Merchant. E.g. KH00001
int getPaymentId()                  |   &#10003;        |   Please refer to [Appendix I (1. PaymentId)](#1-paymentid) for possible PaymentId value return to BackendPostURL.
String getRefNo()                   |   &#10003;        |   The request Reference number for merchant reference purposes, should be unique for each transaction.
double getAmount()                  |   &#10003;        |   Payment amount with two decimals
String getCurrency()                |   &#10003;        |   Currency code that based on standard ISO (KHR or USD)
String getRemark()                  |                   |   Remark for particular transaction.
String getTransId()                 |   &#10003;        |   iPay88 Transaction ID. E.g. T019988877700
String getAuthCode()                |                   |   Bank reference number. Note: Sometime bank may not return reference number to gateway.
int getStatus()                     |   &#10003;        |   Use to indicate payment status “1” – Success OR “0” – Fail
String getErrDesc()                 |                   |   Payment status description. Please refer to [Appendix I (3. Error Description)](#3-error-description)
String getSignature()               |   &#10003;        |   SHA1 signature. Please refer to [5. Signature Response](#5-signature-response)


### 4. How to Make Payment
#### 4.1 Create Payment Request Object
```java
// MARK: - Import SDK to your activity
import kh.com.ipay88.sdk.constants.IPay88Constants;
import kh.com.ipay88.sdk.models.IPay88PayRequest;
import kh.com.ipay88.sdk.models.IPay88PayResponse;
import kh.com.ipay88.sdk.utils.IPay88SDK;

// MARK: - Create 2 static variables to receive IPay88 payment response
static IPay88PayResponse payResponse;
static String errorMsg;

TextView tvStatus;

// MARK: - IPay88 Checkout
IPay88PayRequest payRequest = new IPay88PayRequest();
payRequest.setEnvironment(IPay88PayRequest.Environment.SANDBOX);
payRequest.setMerchantCode("KH00001");
payRequest.setMerchantKey("Apple88KEY");
payRequest.setPaymentId(1);
payRequest.setRefNo("ORD1188");
payRequest.setAmount(1.00);
payRequest.setCurrency(IPay88PayRequest.Currency.USD);
payRequest.setProdDesc("Top Up (SDK)");
payRequest.setUserName("Tola KUN");
payRequest.setUserEmail("tola.kun@ipay88.com.kh");
payRequest.setUserContact("017847800");
payRequest.setRemark("aOS");
payRequest.setBackendURL("http://www.myshop.com/backend_page.php");
```

#### 4.2 Create Payment Callback
+ Create a Java class and implement these 2 interfaces:
```java
public interface kh.com.ipay88.sdk.utils.IPay88Callback
public interface java.io.Serializable
```
+ For Example:
```java
public class MainActivityCallback implements IPay88Callback, Serializable {
    @Override
    public void onResponse(IPay88PayResponse payResponse) {
        MainActivity.payResponse = payResponse;
    }

    @Override
    public void onFailure(String message) {
        MainActivity.errorMsg = message;
    }
}
```

#### 4.3 Invoke Checkout Function
```java
IPay88SDK.getInstance().checkout(MainActivity.this, payRequest, new MainActivityCallback());
```

#### 4.4 Handle Payment Response Object
+ Need to override 
```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
}
```
+ For Example:
```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    // MARK: - IPay88 Payment Response
    if (requestCode == IPay88Constants.PAY_REQUEST_CODE
            && resultCode == IPay88Constants.PAY_RESULT_CODE_OK
            && MainActivity.payResponse != null) {
        
        tvStatus.setText(MainActivity.payResponse.getStatus() == 1 ? "SUCCESS" : "FAILED");
        
    } else if (requestCode == IPay88Constants.PAY_REQUEST_CODE
            && resultCode == IPay88Constants.PAY_RESULT_CODE_CANCELED
            && MainActivity.errorMsg != null) {
        
        tvStatus.setText(MainActivity.errorMsg);
        
    }
}
```


### 5. Signature Response
+ If the Merchant request is successful the response message will contain SHA1 hashed signature. 
+ The hash signature for the response is a hash of the following fields:
    ```
    1. MerchantKey (Provided by IPay88 and share between IPay88 and merchant only)
    2. MerchantCode
    3. PaymentId
    4. RefNo
    5. Amount
    6. Currency
    7. Status
    ```         
+ The fields must be set in the following order, (MerchantKey & MerchantCode & PaymentId & RefNo & Amount & Currency & Status)
+ For Example:
    ```
    MerchantKey = "Apple88KEY"
    MerchantCode = "KH00001"
    PaymentId = "1"
    RefNo = "ORD1188"
    Amount = "1.00" (Note: Remove the “.” and “,” in the string before hash)
    Currency = "KHR"
    Status = "1"
    ```
         
    - The hash would be calculated on the following string:
    ```
    Apple88KEYKH00001ORD1188100KHR1
    ```
    - The resulting has a signature value equal to (using the SHA1 algorithm)
    ```
    KiCecyU86ZOFk15jXwOAvHdw/1M=
    ```
+ Test URL: <a href="https://payment.ipay88.com.kh/epayment/testing/TestSignature_response.asp" target="_blank">https://payment.ipay88.com.kh/epayment/testing/TestSignature_response.asp</a>


### 6. Backend Post Feature
The Backend POST feature is server-to-server technology. It does not depend on the `IPay88Sdk's Payment Screen` to return payment response data to the `Merchant App`. With this feature implemented, your system still can get the payment status on the backend (asynchronously) even if the `IPay88Sdk's Payment Screen` fails to get status from IPay88 System which may be due to a closed `IPay88Sdk's Payment Screen`, internet connection timeout and etc.

#### 1. Prerequisite
- This Backend post feature will ONLY return status if the transaction is a payment `success`. No status will return if the payment is `failed`.
- The Backend page should implement checking on some fields such as `Signature` (Please refer to [5. Signature Response](#5-signature-response)) and `Amount` to prevent users hijack the merchant system.
- The backend page should not have session-related code so that merchant systems are still able to accept payment status from the IPay88 System even if the user is logged out or the session is expired.
- You need to implement a check to determine the "backend page" to update the order so it won't update order status in the merchant system more than 1 time.
- After receiving the payment success status, IPay88 OPSG will simultaneously return payment status to the SDK's Callback and "backend page".

#### 2. Implementation
On the merchant website, create a page to accept backend post response parameters from the IPay88 System.

The Backend Post response parameters are the same as [3.2.2 Payment Response Methods](#322-payment-response-methods) but just remove the `get` prefix and bracket `()` postfix (Eg. `getMerchantCode()` -> `MerchantCode`).

- Step1. Specify your "backend page" in [3.1.2 Payment Request Methods](#312-payment-request-methods) on a field named `backendURL`.
- Step2. On your "backend page", you have to write out the word `RECEIVEOK` as an acknowledgment when you get the payment success status from the IPay88 System. And IPay88 System will re-try send the payment status to the "backend page" up to 3 times on different intervals if no `RECEIVEOK` acknowledgement is detected.

  Note:
  > Make sure just the word `RECEIVEOK` only on your "backend page" without any HTML tag on the page.

#### 3. Sample Code
ASP Classic
```vbScript
<%
    MerchantCode    = Request.Form("MerchantCode")
    PaymentId       = Request.Form("PaymentId")
    RefNo           = Request.Form("RefNo")
    Amount          = Request.Form("Amount")
    eCurrency       = Request.Form("Currency")
    Remark          = Request.Form("Remark")
    TransId         = Request.Form("TransId")
    AuthCode        = Request.Form("AuthCode")
    eStatus         = Request.Form("Status")
    ErrDesc         = Request.Form("ErrDesc")
    Signature       = Request.Form("Signature")

    If eStatus = 1 Then
        ' COMPARE Return Signature with Generated Response Signature 
        ' And Update order to PAID
        response.write "RECEIVEOK"
    Else
        ' Update order to FAIL
    End If
%>
```

PHP
```php
<?php
    $merchantcode   = $_REQUEST["MerchantCode"];
    $paymentid      = $_REQUEST["PaymentId"];
    $refno          = $_REQUEST["RefNo"];
    $amount         = $_REQUEST["Amount"];
    $ecurrency      = $_REQUEST["Currency"];
    $remark         = $_REQUEST["Remark"];
    $transid        = $_REQUEST["TransId"];
    $authcode       = $_REQUEST["AuthCode"];
    $estatus        = $_REQUEST["Status"];
    $errdesc        = $_REQUEST["ErrDesc"];
    $signature      = $_REQUEST["Signature"];

    if ($estatus == 1) {
        // COMPARE Return Signature with Generated Response Signature 
        // And Update order to PAID
        echo "RECEIVEOK";
    }
    else {
        // Update order to FAIL
    }
?>
```


### Appendix I
#### 1. PaymentId
+ Set PaymentId = 0, it will show all registered payment methods to the customer.
##### 1.1 Credit & Debit Card
Payment Method Name |  PaymentId (USD)| Logo
-------------       | :-------------: | :-------------:
Credit Card         |   1             |   <img width="35" src="https://epay.ipay88.com.kh/prevapi/assets/icon/CAMPUCARD.svg" />
UnionPay            |   15            |   <img width="35" src="https://epay.ipay88.com.kh/prevapi/assets/icon/MPGS_UPI.svg" />

##### 1.2 eWallet
Payment Method Name |  PaymentId (USD)|  PaymentId (KHR)| Logo
-------------       | :-------------: | :-------------: | :-------------:
eMoney              |   9             |     10          |   <img width="35" src="https://epay.ipay88.com.kh/prevapi/assets/icon/EW_EMONEY.svg" />
Pi Pay              |   11            |                 |   <img width="35" src="https://epay.ipay88.com.kh/prevapi/assets/icon/QR_PIPAY.svg" />
Alipay              |   233           |                 |   <img width="35" src="https://epay.ipay88.com.kh/prevapi/assets/icon/alipay.svg" />
Wing                |   235           |     236         |   <img width="35" src="https://epay.ipay88.com.kh/prevapi/assets/icon/EW_WING.svg" />
WeChat Pay          |   240           |                 |   <img width="35" src="https://epay.ipay88.com.kh/prevapi/assets/icon/QR_WECHAT.svg" />
KHQR                |   248           |     249         |   <img width="35" src="https://epay.ipay88.com.kh/prevapi/assets/icon/QR_KHQR.svg" />

##### 1.3 Online Banking
Payment Method Name |  PaymentId (USD)|  PaymentId (KHR)| Logo
-------------       | :-------------: | :-------------: | :-------------:
Acleda XPAY         |   3             |     4           |   <img width="35" src="https://epay.ipay88.com.kh/prevapi/assets/icon/ACLEDAXPAYLOGO.svg" />
Chip Mong Pay       |   238           |     239         |   <img width="35" src="https://epay.ipay88.com.kh/prevapi/assets/icon/OB_CHIPMONG.svg" />
Campu Direct Debit  |   242           |     243         |   <img width="35" src="https://epay.ipay88.com.kh/prevapi/assets/icon/OB_CAMPUDIRECTDEBIT.svg" />
AMK Online Card     |   246           |     247         |   <img width="35" src="https://epay.ipay88.com.kh/prevapi/assets/icon/OB_AMKCARD.svg" />

#### 2. Currency
Currency Code   |  Description
-------------   | -------------
KHR             | Cambodia Riel
USD             | US Dollar

#### 3. Error Description
Message                     |  Description
-------------               | -------------
Duplicate reference number  |   Reference number must be unique for each transaction.
Invalid merchant            |   The merchant code does not exist.
Invalid parameters          |   Some parameter posted to IPay88 is invalid or empty.
Overlimit per transaction   |   You exceed the amount value per transaction.
Payment not allowed         |   The Payment method you requested is not allowed for this merchant code, please contact IPay88 Support to verify what payment method available for the merchant account.
Permission not allow        |   Your AppId or the shared credentials is not match with the information registered in IPay88 merchant account. Please contact IPay88 team.


### SDK Version Updates
#### Version >= 1.0.1
##### 1. WeChat In-App Payment Configuration
- Step1. Create a class named `WXPayEntryActivity` which extends `android.app.Activity` and place it under a package named `wxapi`, then put it under your App's package name (e.g.: `kh.com.ipay88.sdk.demo.wxapi`).

- Step2. Add source codes below  to your class named `WXPayEntryActivity`
```java
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;

import kh.com.ipay88.sdk.utils.IPay88Factory;

public class WXPayEntryActivity extends Activity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        IPay88Factory.WeChatPay.handleIntent(getIntent(), this);
    }

    @Override
    protected void onNewIntent(Intent intent) {
        super.onNewIntent(intent);
        setIntent(intent);
        IPay88Factory.WeChatPay.handleIntent(intent, this);
    }
}
```

- Step3. Add the below XML to your `AndroidManifest.xml`, and set the value of WeChat Pay AppId which you received from the IPay88 Team.
```xml
<manifest>
    <application>
        <activity
            android:name=".wxapi.WXPayEntryActivity"
            android:theme="@android:style/Theme.Translucent.NoTitleBar"
            android:exported="true"
            android:taskAffinity = "${applicationId}"
            android:launchMode="singleTask">
        </activity>
        
        <meta-data
            android:name="kh.com.ipay88.sdk.WxPayAppId"
            android:value="wxxxxxxxxxxxxxxxxx" />
    </application>
</manifest>
```