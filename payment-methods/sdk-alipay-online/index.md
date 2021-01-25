# LianLian Pay (Thailand) SDK (Alipay-Online) Integration Guide Version 1.0



## 1. Integration Method
> Use SDK to integrate with LianLian Pay Thailand.

### 1.1 SDK
<div align="center">
<img width="600" src="./images/alipay/sdk.PNG"/>
</div>

## 2. SDK Integration
> Mobile payment transaction SDK interface technical documents are written for those who have certain Android/iOS client development ability and understand Android/iOS client development and management.

### 2.1 SDK interaction process
<div align="center">
<img width="600" src="./images/alipay/sdk-interaction-process.PNG"/>
</div>

> Process description (Android example) :

1.  Step 4: Call the payment interface: this message is the payment object LianPayManager provided by the development kit described in this interface, transmitting the signed order information of the merchant to the pay method to be invoked or redirect to the alipay/WeChat cashier desk. For the order format, please refer to the "SDK payment request parameter description".

2. Step 5: Jump to Alipay or WeChat for payment: the Android/iOS payment development kit will send a payment request according to the request parameters provided by the merchant client. After it’s successful, it will be invoked or direct to the Alipay cashier desk for payment confirmation.

3. Step 8: Interface returns payment result: The payment interface invoked by the merchant client in Step 4 will return the final payment result (i.e., synchronization notification), see "SDK Payment Synchronization Return Parameter Explanation".

4. Step 12: Sending payment notification asynchronously: The mobile Alipay payment server sends an asynchronous notification message to the merchant server (Note: The 12th step have to occur after the 6th step, but not necessarily later than the 7th to 11th step), see "Payment Result Notification Parameter Explanation".

### 2.2 SDK interaction process

#### 2.2.1 SDK payment request
> SDK payment request parameter description
<b>Request Parameters:</b>

|<div style="width:130px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- |--- |--- |
| merchant_id	| String(20)	|<center>Y</center>| Unique identifier for merchant assigned by LianLian| 
| merchant_order_id	| string(64)|<center>Y</center>	| Merchant transaction ID| 
| biz_id	| string(20)	    |<center>Y</center>	| Business ID| 
| order_amount	| string(8, 2)	|<center>Y</center>| Order amount| 
| order_currency	| string(3)	|<center>Y</center>| Currency| 
| order_info	| string(256)	|<center>Y</center>| Display info. to buyer| 
| product_code	| string(64)	|<center>Y</center>| ALIPAY_ONLINE| 
| payment_method	| string(32)|<center>Y</center>|  Payment method: APP_PAYMENT| 
| customer	| Customer          |<center>N</center>| [See 4.3](#Customer) for customer information| 
| products	| List<Product>     |<center>N</center>| Product info.| 
| notify_url	| string(256)	|<center>N</center>| Payment result notification URL| 
| redirect_url	| string(256)	|<center>N</center>| URL that user is redirected when payment is done.| 
| sign_type	| string(3)	        |<center>N</center>| RSA| 
| sign	| string(128)	        |<center>Y</center>| Merchant signature| 

#### 2.2.2 SDK payment synchronization return parameter description
> Request Parameters:

|<div style="width:130px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- |--- |--- |
| merchant_id	| String(20)  |<center>Y</center>| Unique identifier for merchant assigned by LianLian| 
|order_id	|string(20）      |<center>Y</center>|Order ID|
|order_amount	|string(8, 2) |<center>Y</center>|Order amount|
|order_currency	|string(3)	  |<center>Y</center>|Currency|
|order_status	|string(8)	  |<center>Y</center>|Order status|
|sign	|tring(256)	          |<center>Y</center>|signature|

#### 2.2.3 SDK return code description
> PayResult Result code definition:

|SDK status | Message |
| --- | --- |
|1000	|	Payment successful|
|1010	|	Order in progress|
|1020	|	Successfully paid|
|2000	|	Configuration parameter error|
|2001	|	SDK internal error|
|2002	|	SDK not available|
|2003	|	Payment method error|
|3000	|	Parameters error|
|3001	|	Signature parameter error|
|3010	|	Network connection error|
|4000	|	Payment failed|
|5000	|	Cancelled by user|
|5005	|	Unknown payment result|
|9999	|	Service is busy|

### 2.3 Add an APP
> Developers need to first complete application creation before accessing to the SDK. Log in the merchant site, select in the left menu bar, and click “Add” an application:

<div align="center">
<img width="600" src="./images/alipay/add-app.PNG"/>
</div>
<b>Required fields include:</b>

1. App name 
2. App type, Android, IOS, and Web supported (Please be noted SDK for Web is not available yet)
3. App description
4. App logo
5. Package name /BundleId/Domain name (Enter the package name if it is an Android application; If it's an iOS application, you can enter Bundle Id; If it is a Web application, enter the domain name, which must be the same as the Web deployment site domain name)

Optional fields include:

1.	APP Signature digest: it is only applicable to Android platform to verify the validity of APP. It supports SHA256, SHA1 and MD5. The APP signature digest configuration of merchant site must be consistent with the front-end signature digest type configuration of SDK (Default SHA256)
2.	Website  URL

Note: Required fields cannot be modified after the approval, while optional fields can still be modified freely. Improper configuration of APP signature digest will lead to unavailable SDK, please  configure it correctly.

After the APP application is completed, it will be submitted to the administrator for review. After the approval, the APP Key will be generated. The developer shall configure the APP Key into the SDK parameters, please refer to the Android and iOS integration steps for more details

### 2.4 Android integration steps

#### 2.4.1 SDK integration import
> Import development resources

1. Put AlipaySDK-< version >.aar and LLPayThaiSDK-< version >.aar package into the libs directory of the merchant application project
2. Add a dependent Gradle configuration to your APP Module

```
{
	repositories {
    flatDir {
        dirs 'lib'
    }
}
dependencies {
    implementation (name: 'AlipaySDK-15.7.7', ext: 'aar')
    implementation (name: 'LLPayThaiSDK-1.0.0', ext: 'aar')
}

}
```

> Add permission declaration
Add permissions to the Androidmanifest.XML for the merchant application project:

```
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> Add rules of confusing
Add the following rules to the 'proguard-project.txt' of the merchant application project:

```
-keep class th.co.lianlianpay.sdk.core.**{ *; }
-keep class th.co.lianlianpay.sdk.constants.**{ *; }
-keep class th.co.lianlianpay.sdk.model.**{ *; }
-keep class th.co.lianlianpay.sdk.callback.**{ *; }
-keep class th.co.lianlianpay.sdk.widget.**{ *; }
-keep class th.co.lianlianpay.sdk.task.**{ *; }
-keep class th.co.lianlianpay.sdk.enums.**{ *; }
 
-keep class com.alipay.android.app.IAlixPay{*;}
-keep class com.alipay.android.app.IAlixPay$Stub{*;}
-keep class com.alipay.android.app.IRemoteServiceCallback{*;}
-keep class com.alipay.android.app.IRemoteServiceCallback$Stub{*;}
-keep class com.alipay.sdk.app.PayTask{ public *;}
-keep class com.alipay.sdk.app.AuthTask{ public *;}
```

Now, the development kit resources import completed.

#### 2.4.2 Steps to start SDK

>Configure merchant APP KEY

It can be configured in the Androidmanifest.xml, or set up through an API
```
<meta-data android:value="C3UBLN6225orgreopddj1" android:name="LIANPAY_APPKEY"/>
```

> SDK initialization

Initialization in Application is recommended
```
public class AppApplication extends Application {
 
    @Override
    public void onCreate() {
        super.onCreate();
        LianPaySDK.init(this).setSandbox(true).setDebug(true);
    }
}
```

Parameter description:

Sandbox: If is true, it can be tested in Lianlian and Alipay sandbox environment. Default is false, and it needs to be used in Lianlian and  Alipay production environment
Debug: Whether the log is in debug status, the log will open under debug status
AppKey: Set appKey manually
digestType: APP signature digest type, which must be consistent with the open platform configuration digest type, can be set to SHA256, SHA1, MD5, and the default is SHA256

SDK supports API mode and component mode to make payment operation, via PayRequest and PayCallback to transmit parameters. PayRequest is the payment order information parameter, and PayCallback is the payment result callback interface.

（1）Construct payment order parameters
```
PayRequest payRequest = new PayRequest();
payRequest.setMerchantId("142019050800009001");  //Merchant ID
payRequest.setBizId("142019111200055026");  //Business ID
payRequest.setMerchantOrderId("P" + System.currentTimeMillis());  //Order ID
payRequest.setOrderCurrency("THB");  //Currency
payRequest.setOrderAmount("101.25");  //Amount
payRequest.setOrderInfo("app payment");  //order description
payRequest.setNotifyUrl("http://www.yezhou.cc/callback/notify.php");  //Notify URL 
payRequest.setRedirectUrl("http://www.yezhou.cc/callback/redirect.php");  // Redirect URL 
payRequest.setPayType(PayType.AliPay);  //Product Code setting below has the same effect with PaymentMethod settings
//payRequest.setProductCode(ProductCode.ALIPAY_ONLINE.name());
//payRequest.setPaymentMethod(PaymentMethod.APP_PAYMENT.name());
 
Customer customer = new Customer();
customer.setFirstName("Joe");
customer.setLastName("Ye");
customer.setEmail("yezhou@yezhou.org");
customer.setPhone("+8610086");
payRequest.setCustomer(customer);
 
//Get the endorsement string (you can directly submit to the merchant server for endorsement)
String preStr = PayHelper.buildSignString(payRequest);
Log.i(LianConstants.TAG, "preStr: " + preStr);
 
//Simulate the calculation of the merchant’s signature (Required be placed on the merchant's server for signature)
String sign = SignUtil.addSign(payRequest, merchantPrivateKey);
Log.i(LianConstants.TAG, "sign: " + sign);
payRequest.setSignType(SignType.RSA.name());
payRequest.setSign(sign);
 
//Get request body
String body = PayHelper.formatPayRequest(payRequest);
Log.i(LianConstants.TAG, "body: " + body);
```
（2）Payment via API
```
LianPayManage lianPayManage = new LianPayManage(MainActivity.this);
lianPayManage.pay(buildPayRequest(), payCallback);
```
（3）Payment via componet
```
<th.co.lianlianpay.sdk.widget.LianPayWidget
    android:id="@+id/layout_lian_pay"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:layout_constraintTop_toBottomOf="@+id/ll_order"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    android:layout_marginTop="20dp"
    android:layout_marginLeft="10dp"
    android:layout_marginRight="10dp"
    android:background="@drawable/shape_layout_round_rect_bg"
    />
```
```
LianPayWidget lianPayWidget = findViewById(R.id.layout_lian_pay);
lianPayWidget.withAliPay(true).withWechatPay(true).defaultPayType(LianPayWidget.ALI_PAY);
        //.setRadioBackground(R.drawable.selector_checkbox_bg);
 
lianPayWidget.startPay(buildPayRequest(), payCallback);
```
（4）Payment results callback
PayCallback callback interface contains three methods:
```
void onPayResult(PayResult payResult)：支付动作结束，主要进行支付结果处理 Payment action is over, and the payment result is processed
void onPayStart()：支付动作开始，可以做一些初始化工作，如加载进度条等 When the payment action starts, some initialization work can be done, such as loading progress bar, etc.
void onPayPrepared(PayType payType)：支付准备完成回调，下一步即将唤起或跳转支付宝/微信 
Payment is ready to complete the callback, the next step will be called or redirected to Alipay/WeChat
```
Example of payment callback:
```
private PayCallback payCallback = new PayCallback() {
    @Override
    public void onPayResult(PayResult payResult) {
        Log.i(TAG, JSON.toJSONString(payResult));
        LoadingDialog.dismiss(MainActivity.this);
        if (LianSdkResult.PAY_SUCCESS.getStatus().equals(payResult.getStatus())) {
            Toast.makeText(MainActivity.this, "支付成功", Toast.LENGTH_SHORT).show();
            //模拟商户验签（建议放在商户服务端验签）Simulate merchant verification (Recommended to verify on merchant server)
            Map<String, Object> result = payResult.getResult();
            String sign = (String) result.get("sign");
            result.remove("sign");
            boolean checkSignResult = SignUtil.checkSign(sign, result, lianlianPublicKey);
            if (checkSignResult) {
                Toast.makeText(MainActivity.this, "验签成功", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(MainActivity.this, "验签失败", Toast.LENGTH_SHORT).show();
            }
        } else {
            Toast.makeText(MainActivity.this, payResult.getMessage(), Toast.LENGTH_SHORT).show();
        }
    }
 
    @Override
    public void onPayStart() {
        LoadingDialog.show(MainActivity.this, "Paying...");
    }
 
    @Override
    public void onPayPrepared(PayType payType) {
        LoadingDialog.dismiss(MainActivity.this);
    }
};
```
>Payment result
Refer to the Demo,  the payment process is as follows:

<div align="center">
<img width="600" src="./images/alipay/payment-result.PNG"/>
</div>

### 2.5 iOS integration

#### 2.5.1 SDK Access to the SDK
1.	LLPayThaiSDK-iOS-v1.0.0.zip
Download the iOS SDK of Mobile Payment of LianlianPay Thailand: llpaythaisdk-IOS-v1.0.0.zip
2.	After decompression, the LlPaythAISdk.Framework for integration will be obtained, as shown in the figure below:
<div align="center">
<img width="600" src="./images/alipay/sdk-access-sdk.PNG"/>
</div>

#### 2.5.2 SDK Import and configure SDK
1.	Start Xcode and copy the llPaythAISdk.Framework just obtained into your project folder, as shown in the figure below:
<div align="center">
<img width="600" src="./images/alipay/sdk-import-config.PNG"/>
</div>

2.	Import into the project, as shown in the figure below:
<div align="center">
<img width="400" src="./images/alipay/import-into-project.PNG"/>
</div>

3.	Add the following dependencies in the Link Binary With Libraries section of the Build Phases TAB:
<div align="center">
<img width="600" src="./images/alipay/add-dependence.PNG"/>
</div>
Please note:
(1)	If the version after Xcode 7.0, need to add libc++. TBD, libz. TBD;
(2)	If the version before Xcode 7.0, you need to add libc++. Dylib, libz.dylib (as shown in the figure below).
<div align="center">
<img width="600" src="./images/alipay/please-note.PNG"/>
</div>

4.	Click the project name, click Info TAB, then click URL Types, click +, and then type LLPayThaiSDKDemo back to URL, as shown below:
<div align="center">
<img width="600" src="./images/alipay/click-project-name.PNG"/>
</div>
Note: If it is not configured, the user will not be able to go back to the merchant App to complete the entire payment process.

#### 2.5.3 DEMO Refer to SDK DEMO to start using SDK
1.	In the AppDelegate. M file, add the reference of header file and add the core code as follows (if you want to see more code, please get the DEMO): 

```
#import <LLPayThaiSDK/LLPayThaiSDK.h>
  
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // Override point for customization after application launch.
    self.window.rootViewController = self.vc;
    [self.window makeKeyAndVisible];
     
    //AppKey is obtained by the merchant registering the App at the merchant portal
    [[LLPayThaiSDK sharedInstance] registerApp:@"C3UBLN6225orgreopddj" productEnvironment:NO];
     
    return YES;
}
 
 
- (BOOL)application:(UIApplication *)application
            openURL:(NSURL *)url
  sourceApplication:(NSString *)sourceApplication
         annotation:(id)annotation {
     
    if ([url.host isEqualToString:@"safepay"]) {
        //Jump to Alipay wallet for payment and process payment results
        [[LLPayThaiSDK sharedInstance] processOrderWithPaymentResult:url standbyCallback:^(NSDictionary *resultDic) {
            NSLog(@"result = %@",resultDic);
            self.vc.payButton.enabled = YES;
             
            UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"Payment Result" message:resultDic[@"message"] preferredStyle:UIAlertControllerStyleAlert];
            [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
                 
            }]];
             
            [self.vc presentViewController:alert animated:YES completion:NULL];
        }];
    }
    return YES;
}
 
// NOTE: Use the new API interface after 9.0
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString*, id> *)options
{
    if ([url.host isEqualToString:@"safepay"]) {
        //Jump to Alipay wallet for payment and process payment results
        [[LLPayThaiSDK sharedInstance] processOrderWithPaymentResult:url standbyCallback:^(NSDictionary *resultDic) {
            NSLog(@"result = %@", resultDic);
            self.vc.payButton.enabled = YES;
             
            UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"Payment Result" message:resultDic[@"message"] preferredStyle:UIAlertControllerStyleAlert];
            [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
                 
            }]];
             
            [self.vc presentViewController:alert animated:YES completion:NULL];
        }];
    }
    return YES;
}
```

2.	In the file you want to initiate the payment, add the header file reference and the core code as follows  (if you want to see more code, please get the DEMO) :

```
#import <LLPayThaiSDK/LLPayThaiSDK.h>
 
 
- (void)pay:(id)sender {
     
    self.payButton.enabled = NO;
    //Merchant’s private key, used to sign business parameters
    NSString *privateKey = @"xxxxxx";
     
    // SDK支付请求对象  SDK payment request object
    LLPayThaiPaymentObject *payObject = [[LLPayThaiPaymentObject alloc] init];
    payObject.merchantId = @"142019050800009001";   //Merchant ID in Lianlian pay
    payObject.bizId = @"142019111200055026";    //Business ID in Lianlian Pay
    payObject.channelCode = ChannelCodeAlipay;  //Selected payment method, only Alipay supported
    payObject.productCode = @"ALIPAY_ONLINE";   //alipay_online Payment product, Alipay: Alipay_online
    payObject.paymentMethod = @"APP_PAYMENT";   //APP_PAYMENT Payment method, Fixed value: APP_PAYMENT
    payObject.merchantOrderId = [self generateTradeNO]; //Order number sent to Lianlian merchants can be generated by the merchants 
    payObject.orderCurrency = @"THB";   //THB Payment currency, currently only THB
    payObject.orderAmount = @"0.05";    //Payment amount, with two decimal places
    payObject.orderInfo = @"sdk demo test order info";  //Merchant payment order information
    payObject.memo = @"test order"; //Merchant payment note remarks
    payObject.notifyUrl = @"https://www.baidu.com"; //Notification address of the merchant server, used to receive the payment success notification
     
    //Generate signature string
    ModelUtil *modelUtil = [[ModelUtil alloc] init];
    NSDictionary *payDict = [modelUtil dicFromObject:payObject];
    NSString *signString = [modelUtil generateSignString:payDict excludeFields:nil];
     
    //Sign with private key to generate business parameter signature
    NSString *sign = [CryptoUtil rsaSignString:[NSString stringWithFormat:@"%@", signString] WithPrivateKey:privateKey];
    payObject.sign = sign;
     
    //Initiate payment
    [[LLPayThaiSDK sharedInstance] payOrder:payObject fromScheme:@"LLPayThaiSDKDemo" callback:^(NSDictionary *resultDic) {
        // 支付结果处理 Payment result handling
        self.payButton.enabled = YES;
         
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"Payment Result" message:resultDic[@"message"] preferredStyle:UIAlertControllerStyleAlert];
        [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
             
        }]];
         
        [self presentViewController:alert animated:YES completion:NULL];
    }];
}
```
Note: In the DEMO, some utility is provided that are used locally for the client where the sample signature resides. For security reasons, please save the private key on the server and check the signature on the server.

3.	Initiate the payment application. If the application is successful, it will be redirected to the cashier desk of Alipay App and users are required to confirm the payment, as shown in the figure below:
<div align="center">
<img width="300" src="./images/alipay/initiate-payment.PNG"/>
</div>

4. After completing the payment, the user will return to the merchant App and the merchant will display the payment result