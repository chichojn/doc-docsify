# Lianlian Pay (Thailand) Payment Gateway (WeChat Pay) Integration Guide Version 1.0

## 1. Integration Method
>Lianlian TH provides API integration.

#### 1.1 Interactive Flow
<div align="center">
<img width="700" src="./images/wechat/interactiveFlow.PNG"/>
</div>

## 2. API Definition
### 2.1 Payment
>Merchant order ID needs to be unique, not allowed to initiate repeated submissions.

#### 2.1.1 Payment Request
>Request Parameters:

|<div style="width:122px" align="left"> Sandbox URL </div>|<div style="width:534px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:110px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:340px">Description</div>|
| --- | --- | --- | --- | 
| version | string(2) 		|<center>Y</center>| Interface version, fixed value: v1 | 
| service| string(64) 		|<center>Y</center>| llpth.wechatpay.pay| 
| merchant_id | string(20) 	|<center>Y</center>| Unique identifier for merchant assigned by LianLian | 
|merchant_order_id |string(64）|	<center>Y</center>|Merchant transaction ID | 
|order_amount |	string(8, 2)|<center>Y</center>| Order amount | 
|order_currency	|string(3)	|<center>Y</center>| Currency| 
|order_info|string(128)		|<center>Y</center>|Display info. to buyer| 
|store_id| string(20) 		|<center>N</center>|  Unique ID of the store| 
|payment_method |	string(32)	|<center>Y</center>| Payment method:<br> DYNAMIC_CODE, <br>INAPP_PAYMENT (Official account and mini program), <br>MERCHANT_SCAN；| 
|notify_url | string(256) 		|<center>N</center>| Payment result notification URL| 
|auth_code | string(20)		|<center>C</center> | payment_method: MERCHANT_SCAN| 
|appid | string(20)			|<center>C</center>| AppID of mini program or official account,<br> required when Payment method is INAPP.| 
|openid | string(32) 		|<center>C</center>| openID of mini program or official account,<br> required when Payment method is INAPP.| 

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

| Field  | Type| Required  | Description|
| --- | --- | --- | --- | 
| order_id	| string(20)		|<center>Y</center>| Order ID| 
| order_status	| string(8)		|<center>Y</center>| 	Order status| 
| order_amount	| string (8,2)	|<center>Y</center>| 	Order amount| 
| order_currency| 	string (3)	|<center>Y</center>| 	Order currency| 
| create_time| 	string (19)		|<center>Y</center>| 	Order time| 
| link_url| string (32)			|<center>N</center>| QR code string of the order code returned when it is DYNAMIC_CODE| 
| pay_params| 	text| 			|<center>N</center>| Payment parameter object of the official account or mini program returned when payment_method is INAPP_PAYMENT| 

<b>Request sample</b>
```
{
	"version": "v1",
    "service": "llpth.wechatpay.pay",
    "merchant_id": "142019050800009001",
    "merchant_order_id": "test100072",
    "order_amount": "0.15",
    "order_currency": "THB",
    "order_info": "test",
    "payment_method": "DYNAMIC_CODE",
    "notify_url": "https://www.lianlianpay.co.th/callback"
}
```

<b>Return sample</b>
```
{
	"code": 200000,
	"data": {
		"order_id": "122020120313403001",
		"order_status": "WP",
		"order_amount": "0.15",
		"order_currency": "THB",
		"create_time": "2020-12-03 10:01:42",
		"link_url": "weixin://wxpay/bizpayurl?pr=kl6sj1g00"
	}
	"message": "Success",
	"trace_id": "54e2983e66b738e6"
}
```

<b>APP Request sample</b>
```
{
	"version": "v1",
	"service": "llpth.wechatpay.pay",
    "merchant_id": "142019050800009001",
    "merchant_order_id": "test100088",
    "order_amount": "50.8",
    "order_currency": "THB",
    "order_info": "test",
    "payment_method": "INAPP_PAYMENT",
    "notify_url": "https://www.lianlianpay.co.th/callback",
    "appid": "wx26d9f58adc1e2f7e",
    "openid": "oaJiE1PwlwP_Tee0Z7uzrQFJeRqE"
}
```

<b>APP Return sample</b>
```
{
	"code": 200000,
	"data": {
	"order_id": "122020120913407038",
	"order_status": "WP",
	"order_amount": "50.80",
	"order_currency": "THB",
	"create_time": "2020-12-09 16:10:38",
	"pay_params": {
		"timeStamp": "1607505096",
		"package": "prepay_id=wx091711368479544bc68157c5d6578e0000",
		"paySign": "609ca496c49713b944a4f61f6ca6ed75f45273459767c2dcb42392b4cf51dbc7",
		"appId": "wx26d9f58adc1e2f7e",
		"signType": "HMAC-SHA256",
		"nonceStr": "20201209171136824"
		}
	},
	"message": "Success",
	"trace_id": "54e2983e66b738e6"
}
```

#### 2.1.2 Payment cancel
<b>Request Parameters:</b>

|<div style="width:122px" align="left"> Sandbox URL </div>|<div style="width:534px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:110px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:340px">Description</div>|
| --- | --- | --- | --- | 
| version | string(2) 		|<center>Y</center>| Interface version, fixed value: v1 | 
| service| string(64) 		|<center>Y</center>| llpth.wechatpay.pay.cancel| 
| merchant_id | string(20) 	|<center>Y</center>| Unique identifier for merchant assigned by LianLian | 
|merchant_order_id |string(64）|<center>Y</center>|Merchant transaction ID | 

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:340px">Description</div>|
| --- | --- | --- | --- | 
| merchant_id| string(20)		|<center>Y</center>| Order ID|
| merchant_order_id	| string(64)|<center>Y</center>| Merchant order ID|
| order_id	| string(20)		|<center>Y</center>| Order ID| 
| order_status	| string(8)		|<center>Y</center>Y| 	Order status| 
| order_amount	| string (8,2)	|<center>Y</center>| 	Order amount| 
| order_currency| 	string (3)	|<center>Y</center>| 	Order currency| 
| create_time| 	string (19)		|<center>Y</center>| 	Order time | 

<b>Request sample</b>
```
{
	"version": "v1",
	"service": "llpth.wechatpay.pay.cancel",
    "merchant_id": "142019050800009001",
    "merchant_order_id": "test100080"
}
```
<b>Return sample</b>
```
{
	"code": 200000,
	"data": {
	   	"merchant_id": "142019050800009001",
	    "merchant_order_id": "test100080",
	    "order_id": "122020120813406020",
	    "order_status": "PC",
	    "order_amount": "50.80",
	    "order_currency": "THB",
	    "create_time": "2020-12-08 13:38:39"
	},
	"message": "Success",
	"trace_id": "54e2983e66b738e6"
}
```

#### 2.1.3 Payment result notification
>When the payment confirmation is completed, if the field ‘notify_url’ is not blank in the merchant payment  request, Lianlian Thailand will push the payment result to the "notify_url" which was provided in the merchant payment application. When the merchant returns, the HTTP code must be 200.

Trigger frequency: 10 mins

Max notification times: 13

<b>Notification Parameters (Lianlian Thailand to merchant):</b>

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:340px">Description</div>|
| --- | --- | --- | --- | 
| order_id	| string(20)		|<center>Y</center>| Order ID| 
| merchant_order_id	| string(64)|<center>Y</center>| Merchant order ID|
| order_status	| string(8)		|<center>Y</center>| 	Order status| 
| order_amount	| string (8,2)	|<center>Y</center>| 	Order amount| 
| order_currency| 	string (3)	|<center>Y</center>| 	Order currency| 
| create_time| 	string (19)		|<center>Y</center>| 	Order time | 

<b>Response Parameters (Merchant to Lianlian Thailand):</b>

|<div style="width:123px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:340px">Description</div>|
| --- | --- | --- | --- | 
|code|string(6)|Y | Fixed: 200000|
|message|string(32)|N | Fixed: Success|

<b>Request sample</b>
```
{
	"order_id": "122020040700160021",
    "merchant_order_id": "test_020",
    "order_status": "PS",
    "order_amount": "100.00",
    "order_currency": "THB",
    "complete_time": "2020-04-07 13:19:02"

}
```
<b>Return sample</b>
```
{
	"code": 200000,
	"message": "Success"
}
```

#### 2.1.4 Payment Query

<b>Request Parameters:</b>

|<div style="width:122px" align="left"> Sandbox URL </div>|<div style="width:534px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | GET|

|<div style="width:110px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:340px">Description</div>|
| --- | --- | --- | --- | 
| version | string(2) 		|<center>Y</center>| Interface version, fixed value: v1 | 
| service| string(64) 		|<center>Y</center>| llpth.wechatpay.pay.query| 
| merchant_id | string(20) 	|<center>Y</center>| Unique identifier for merchant assigned by LianLian | 
| merchant_order_id |string(64）|<center>Y</center>|Merchant transaction ID | 

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

|<div style="width:200px"> Response Parameters: Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:340px">Description</div>|
| --- | --- | --- | --- | 
| merchant_id| string(20)		|<center>Y</center>| Order ID|
| merchant_order_id	| string(64)|<center>Y</center>| Merchant order ID|
| order_id	| string(20)		|<center>Y</center>| Order ID| 
| order_status	| string(8)		|<center>Y</center>| 	Order status| 
| order_amount	| string (8,2)	|<center>Y</center>| 	Order amount| 
| order_currency| 	string (3)	|<center>Y</center>| 	Order currency| 
| create_time| 	string (19)		|<center>Y</center>| 	Order time | 
| complete_time| 	string (19)	|<center>Y</center>| Payment completed time | 

<b>Request sample</b>
```
https://sandbox-th.lianlianpay-inc.com/gateway?version=v1&service=llpth.wechatpay.pay.query&merchant_id=142019050800009001&merchant_order_id=test100085
```
<b>Return sample</b>
```
{
	"code": 200000,
	"data": {
        "merchant_id": "142019050800009001",
        "merchant_order_id": "test100085",
        "order_id": "122020120913407003",
        "order_status": "PS",
        "order_amount": "0.15",
        "order_currency": "THB",
        "create_time": "2020-12-09 10:11:16",
        "complete_time": "2020-12-09 10:11:53"
	},
	"message": "Success",
	"trace_id": "c1c2e4fdb9abda7d"
}
```

### 2.2 Refund
>Merchant order ID needs to be unique, not allowed to initiate repeated submissions.

#### 2.2.1 Refund Request
<b>Request Parameters:</b>

|<div style="width:132px" align="left"> Sandbox URL </div>|<div style="width:564px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>|
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:132px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:340px">Description</div>|
| --- | --- | --- | --- | 
| version | string(2) 		|<center>Y</center>| Interface version, fixed value: v1 | 
| service| string(64) 		|<center>Y</center> |llpth.wechatpay.refund| 
| merchant_id | string(20) 	|<center>Y</center>| Unique identifier for merchant assigned by LianLian | 
| merchant_refund_id| String(64)|<center>Y</center>| Merchant refund ID, need to guarantee its uniqueness| 
|merchant_order_id |string(64）	|<center>Y</center>|Merchant transaction ID | 
|refund_amount |	string(8, 2)|<center>Y</center>| Refund amount, it cannot be greater than payment amount | 
|refund_currency	|string(3)	|<center>Y</center>| Refund Currency| 
| notify_url | string(256) 		|<center>N</center>| URL of refund notification | 
| memo | string(80) 			|<center>N</center>| Memo | 

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

|<div style="width:132px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
| --- | --- | --- | --- | 
| merchant_id | string(20) 			|<center>Y</center>| Unique identifier for merchant assigned by LianLian | 
| refund_serial _id| 	string(20)	|<center>Y</center>| Refund order ID| 
| payment_order_id	| string(64)	|<center>Y</center>| Original payment order ID| 
| merchant_order_id| 	string(32)	|<center>Y</center>| Original merchant payment ID| 
| merchant_refund_id| 	string(32)	|<center>Y</center>| Merchant refund ID| 
| refund_amount	| string(8,2)		|<center>Y</center>| Refund amount| 
| refund_currency	| string(3)		|<center>Y</center>|  Refund currency| 
| refund_status	| string(8)			|<center>Y</center>| Refund status| 
| create_time| 	string (19)			|<center>Y</center>| 	Refund time| 
| complete_time	| string(19)		|<center>N</center>| 	Refund completed time| 


<b>Request sample</b>
```
{
	"version": "v1",
	"service": "llpth.wechatpay.refund",
    "merchant_id": "142019050800009001",
    "merchant_order_id": "test100085",
    "merchant_refund_id": "refund100076",
    "refund_amount": "0.05",
    "refund_currency": "THB",
    "notify_url": "https://www.yezhou.cc/callback/notify.php",
    "memo": "merchant refund memo"
}
```

<b>Return sample</b>
```
{
	"code": 200000,
	"data": {
        "refund_serial_id": "122020120913407011",
        "payment_order_id": "122020120913407003",
        "merchant_id": "142019050800009001",
        "merchant_refund_id": "refund100076",
        "merchant_order_id": "test100085",
        "refund_status": "RP",
        "refund_amount": "0.05",
        "refund_currency": "THB",
        "create_time": "2020-12-09 10:18:11"
	},
	"message": "Success",
	"trace_id": "4df57b46fe2af2c3"
}
```

#### 2.2.2 Refund result notification
>Upon completion of refund, LianLian Pay will push refund result to the “notify_url” submitted by the merchant for application. When the merchant returns, the HTTP code must be 200.

Trigger frequency: 10 mins

Max notification times: 13

<b>Notification Parameters (Lianlian Thailand to merchant):</b>

|<div style="width:132px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
| --- | --- | --- | --- | 
| refund_order_id	| string(20)	|<center>Y</center>| Lianlian refund order ID| 
| merchant_refund_id	| string(64)|<center>Y</center>| Merchant refund order ID|
| refund_amount| 	string(8,2)		|<center>Y</center>| Refund amount| 
| refund_currency| 	string (3)		|<center>Y</center>| Refund currency| 
| refund_status	| string (8)		|<center>Y</center>| Refund status| 
| complete_time	| string(19)		|<center>N</center>| Refund complete time| 


<b>Response Parameters (Merchant to Lianlian Thailand):</b>

|<div style="width:132px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
| --- | --- | --- | --- | 
|code|string(6)		|<center>Y</center> | Fixed: 200000|
|message|string(32)	|<center>N</center>| Fixed: Success|

<b>Request sample</b>
```
{
	"refund_order_id": "122020040700160037",
    "merchant_refund_id": "test_refund_022",
    "refund_amount": "100.00",
    "refund_currency": "THB",
    "refund_status": "RS",
    "complete_time": "2020-04-07 14:09:38"
}
```
<b>Return sample</b>
```
{
	"code": 200000,
	"message": "Success"
}
```

#### 2.2.3 Refund Query

<b>Request Parameters:</b>

|<div style="width:132px" align="left"> Sandbox URL </div>|<div style="width:534px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>|
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | GET|

|<div style="width:132px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:340px">Description</div>|
| --- | --- | --- | --- | 
| version | string(2) 		| <center>Y</center>| Interface version, fixed value: v1 | 
| service| string(64) 		|<center>Y</center>| llpth.wechatpay.refund.query| 
| merchant_id | string(20) 	|<center>Y</center> | Unique identifier for merchant assigned by LianLian | 
| merchant_refund_id |string(64）|<center>Y</center>|Merchant refund order ID | 

<b>Field Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

| Response Parameters: Field  | Type| Required  | Description|
| --- | --- | --- | --- | 
| merchant_id| string(20)		|<center>Y</center>| Order ID|
| merchant_refund_id| string(64)|<center>Y</center>| Merchant refund order ID| 
| merchant_order_id	| string(64)|<center>Y</center>| Merchant original order ID| 
| refund_status	| string(20)	|<center>Y</center>| Refund status| 
| refund_amount	| string(8, 2)	|<center>Y</center>| Refund amount, cannot be greater than the payment amount| 
| refund_currency	| string(3)	|<center>Y</center>| Refund currency| 
| create_time| 	string (19)		|<center>Y</center>| 	Order time | 
| complete_time| 	string (19)	|<center>N</center>| Payment completed time | 
| memo | string(1024) 			|<center>N</center>| Refund memo| 


<b>Request sample</b>
```
https://sandbox-th.lianlianpay-inc.com/gateway?version=v1&service=llpth.wechatpay.refund.query&merchant_id=142019050800009001&merchant_refund_id=refund100076
```
<b>Return sample</b>
```
{
	"code": 200000,
    "data": {
        "merchant_id": "142019050800009001",
        "merchant_order_id": "test100085",
        "merchant_refund_id": "refund100076",
        "refund_currency": "THB",
        "refund_amount": "0.05",
        "refund_status": "RS",
        "create_time": "2020-12-09 10:18:11",
        "complete_time": "2020-12-09 10:18:21",
        "memo": "test"
	},
	"message": "Success",
    "trace_id": "78a74d643a9a0106"
}
```

## 3. Appendix<!-- {docsify-ignore} -->
> [see Appendix](./#Appendix)