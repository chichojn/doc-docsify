# Lianlian Pay (Thailand) Payment Gateway (Alipay QR) Integration Guide Version 1.1



## 1. Integration Method
>Lianlian TH provides API integration. 

### 1.1 2.1.	Interactive Flow 
<div align="center">
<img width="600" src="./images/alipay/qr-flow.PNG"/>
</div>

## 2. API Definition
### 2.1 Payment
>Merchant order ID needs to be unique, not allowed to initiate repeated submissions.
### 2.1.1 Payment Request

<b>Request Parameters</b>

|<div style="width:130px" align="left"> Sandbox URL </div>|<div style="width:554px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>|  
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:130px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- | --- | --- | 
|version	|string(2)			|<center>Y</center>|Interface version number, fixed value: v1 |
|service	|string(64)			|<center>Y</center>|Interface name, fixed value: llpth.alipay.offline.pay|
|merchant_id	|String(20)		|<center>Y</center>|Unique identifier for merchant assigned by LianLian |
|merchant_order_id	|string(64）|<center>Y</center>|Merchant transaction ID |
|order_amount	|string(8, 2)	|<center>Y</center>|Order amount |
|order_currency	|string(3)		|<center>Y</center>|Currency|
|order_info	|string(512)		|<center>Y</center>|Display info. to buyer|
|store_id	|string(18)			|<center>Y</center>|Store ID. Unique identifier of the store|
|payment_type	|string(32)		|<center>Y</center>|Payment type: MERCHANT_SCAN, DYNAMIC_CODE|
|buyer_identity_code	|string(32)	|<center>Y</center>|User payment barcode|
|notify_url	|string(256)		|<center>N</center>|Payment result notification URL|


<b>Response Parameters:</b>

 >see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

|<div style="width:130px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- | --- | --- | 
|order_id	|string(20)			|<center>Y</center>|Order ID|
|order_status	|string(8)		|<center>Y</center>|Order status|
|order_amount	|string (8,2)	|<center>Y</center>|Order amount|
|order_currency	|string (3)		|<center>Y</center>|Order currency|
|create_time	|string (19)	|<center>Y</center>|Order created time|
|link_url	|text				|<center>N</center>|QR code of the order, leave it blank if the payment type is ‘MERCHANT_SCAN’|


<b>Request sample</b>
```
{
	"version": "v1",
	"service": "llpth.alipay.offline.pay",
	"merchant_id": "142019050800009001",
	"merchant_order_id": "test_020",
	"order_amount": "100.12",
	"order_currency": "THB",
	"order_info": "display your order info",
	"payment_type ": "MERCHANT_SCAN",
	"store_id": "142020050900009001",
	"buyer_identity_code": "280481234296932218",
	"notify_url": "https://www.lianlianpay.co.th/callback"
}
```

<b>Return sample</b>
```
{
	""code": 200000,
	"data": {
	"order_id": "122020040700160021",
	"order_status": "WP",
	"order_amount": "100.12",
	"order_currency": "THB",
	"create_time": "2020-04-07 13:19:02",
	"link_url": ""
    },
    "message": "Success",
    "trace_id": "54e2983e66b738e6"
}
```

#### 2.1.2 Payment cancel

<b>Request Parameters:</b>

|<div style="width:130px" align="left"> Sandbox URL </div>|<div style="width:554px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:130px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- | --- | --- | 
|version	|string(2)		|<center>Y</center>|Interface version number, fixed value: v1|
|service	|string(64)	|<center>Y</center>|Interface name, fixed value：llpth.alipay.offline.cancel|
|merchant_id	|String(20)	|<center>Y</center>|Unique identifier for merchant assigned by LianLian |
|merchant_order_id	|string(64）	|<center>Y</center>|Merchant transaction ID |

<b>Response Parameters (Merchant to Lianlian Thailand):</b>
 >see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field


<b>Request sample</b>
```
{
	"version": "v1",
	"service": "llpth.alipay.offline.cancel",
	"merchant_id": "142019050800009001",
	"merchant_order_id": "test_020"
}
```
<b>Return sample</b>
```
{
	"code": 200000,
    "message": "Success",
    "trace_id": "54e2983e66b738e6"
}
```

#### 2.1.3 Payment result notification

>When the payment confirmation is completed, if the field ‘notify_url’ is not blank in the merchant payment  request, Lianlian Thailand will push the payment result to the "notify_url" which was provided in the merchant payment application. When the merchant returns, the HTTP code must be 200.

Trigger frequency: 10 mins

Max notification times: 13


<b>Notification Parameters (Lianlian Thailand to merchant):</b>

|<div style="width:130px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- | --- | --- | 
| order_id	| string(20)		|<center>Y</center>| Order ID| 
| merchant_order_id	| string(64)|<center>Y</center>| Merchant order ID|
| order_status	| string(8)		|<center>Y</center>| 	Order status| 
| order_amount	| string (8,2)	|<center>Y</center>| 	Order amount| 
| order_currency| 	string (3)	|<center>Y</center>| 	Order currency| 
| complete_time| 	string (19)	|<center>Y</center>| Payment Complete time | 

<b>Response Parameters (Merchant to Lianlian Thailand):</b>

|<div style="width:130px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- | --- | --- | 
|code|string(6)		|<center>Y</center>| Fixed: 200000|
|message|string(32)	|<center>N</center> | Fixed: Success|

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

|<div style="width:130px" align="left"> Sandbox URL </div>|<div style="width:554px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | GET|

|<div style="width:130px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- | --- | --- | 
| version | string(2) 		|<center>Y</center>| Interface version, fixed value: v1 | 
| service| string(64) 		|<center>Y</center>| Interface name, fixed value: llpth.alipay.offline.pay.query| 
| merchant_id | string(20) 	|<center>Y</center>| Merchant-id provided by Lianlian Thailand | 
| merchant_order_id |string(64）|<center>Y</center>|Merchant order ID | 

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

|<div style="width:200px">Response Parameters: Field </div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:290px">Description</div>|
| --- | --- | --- | --- | 
| merchant_id| string(20)		|<center>Y</center>| Order ID|
| merchant_order_id	| string(64)|<center>Y</center>| Merchant order ID|
| order_id	| string(20)		|<center>Y</center>| Order ID| 
| order_status	| string(8)		|<center>Y</center>| 	Order status| 
| order_amount	| string (8,2)	|<center>Y</center>| 	Order amount| 
| order_currency| 	string (3)	|<center>Y</center>| 	Currency| 
|create_time	|string(19)		|<center>Y</center>|	Order create time|
|complete_time	|string(19)		|<center>N</center>|Payment complete time|



<b>Request sample</b>
```
https://sandbox-th.lianlianpay-inc.com/gateway?version=v1&service=llpth.alipay.offline.pay.query&merchant_id=142019050800009001&merchant_order_id=test_019
```
<b>Return sample</b>
```
{
    "code": 200000,
	"data": {
		"merchant_id": "142019050800009001",
	    	"merchant_order_id": "test_019",
	"order_id": "122020040700160016",
	"order_status": "PS",
	"order_amount": "100.00",
	"order_currency": "THB",
	"create_time": "2020-04-07 10:38:18",
	"complete_time": "2020-04-07 10:41:26"
    },
    "message": "Success",
    "trace_id": "c1c2e4fdb9abda7d"
}
```

### 2.2 Refund

#### 2.2.1 Refund Request
<b>Request Parameters:</b>

|<div style="width:140px" align="left"> Sandbox URL </div>|<div style="width:564px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:140px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- | --- | --- | 
|version	|string(2)			|<center>Y</center>|Interface version number, fixed value: v1||
|service	|String(64)			|<center>Y</center>|Interface name, fixed value: llpth.alipay.offline.refund|
|merchant_id	|string(20)		|<center>Y</center>|Unique identifier for merchant assigned by LianLian Pay |
|merchant_refund_id	|String(64)	|<center>Y</center>|Merchant refund ID, need to guarantee its uniqueness|
|merchant_order_id	|string(64)	|<center>Y</center>|Original transaction ID of merchant |
|refund_amount	|string(8, 2)	|<center>Y</center>|Refund amount, it cannot be greater than payment amount|
|refund_currency	|string(3)	|<center>Y</center>|Refund currency|
|notify_url	|string(256)		|<center>N</center>|URL of refund notification|


<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

|<div style="width:140px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:365px">Description</div>|
| --- | --- | --- | --- | 
|merchant_id	|string(20)		|<center>Y</center>|Unique identifier for merchant assigned by LianLian |
|refund_serial _id	|string(20)	|<center>Y</center>|Refund order ID|
|payment_order_id	|string(64)	|<center>Y</center>|Original payment order ID|
|merchant_order_id	|string(32)	|<center>Y</center>|Original merchant payment ID|
|merchant_refund_id	|string(32)	|<center>Y</center>|Merchant refund ID|
|refund_amount	|string(8,2)	|<center>Y</center>|Refund amount|
|refund_currency	|string(3)	|<center>Y</center>|Refund currency|
|refund_status	|string(8)		|<center>Y</center>|Refund status|
|memo	|string(256)			|<center>N</center>|Remark information|
|create_time	|string(19)		|<center>Y</center>|Refund initiate time|


<b>Request sample</b>
```
{
	"version": "v1",
	"service": " llpth.bankcard.refund",
	"merchant_id": "142019050800009001",
	"merchant_refund_id": "merchant_refund_022",
	"merchant_order_id": "merchant_payment_003",
	"refund_amount": "100.00",
	"refund_currency": "THB",
	"notify_url": "http://www.lianlanpay.co.th/callback",
	"memo": "merchant refund demo"
}
```

<b>Return sample</b>
```
{
	"code": 200000,
    "message": "Success",
    "data": {
		"merchant_id": "142019050800009001",
		"refund_serial_id": "122019060500018005",
		"payment_order_id": "122019060500019005",
		"merchant_refund_id": "merchant_refund_022",
		"merchant_order_id": "merchant_payment_003",
		"refund_amount": "100.00",
		"refund_currency": "THB",
		"refund_status": "RP",
		"memo": "merchant refund demo",
		"creation_time": "2020-08-12 23:34:12"
    	},
	"trace_id": "4df57b46fe2af2c3"
}
```

#### 2.2.2 Refund result notification
>Upon completion of refund, LianLian Pay will push refund result to the “notify_url” submitted by the merchant for application. When the merchant returns, the HTTP code must be 200.

Trigger frequency: 10 mins

Max notification times: 13

<b>Notification Parameters (Lianlian Thailand to merchant):</b>

|<div style="width:140px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- | --- | --- | 
| refund_order_id	| string(20)	|<center>Y</center>| Lianlian refund order ID| 
| merchant_refund_id	| string(64)|<center>Y</center>| Merchant refund order ID|
| refund_amount| 	string(8,2)		|<center>Y</center>| Refund amount| 
| refund_currency| 	string (3)		|<center>Y</center>| Refund currency| 
| refund_status	| string (8)		|<center>Y</center>| Refund status| 
| complete_time	| string(19)		|<center>N</center>	| Refund complete time| 


<b>Response Parameters (Merchant to Lianlian Thailand):</b>

|<div style="width:140px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- | --- | --- | 
|code|string(6)		|<center>Y</center>| Fixed: 200000|
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

|<div style="width:140px" align="left"> Sandbox URL </div>|<div style="width:566px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | GET|

|<div style="width:140px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- | --- | --- | 
| version | string(2) 		|<center>Y</center>| Interface version, fixed value: v1 | 
| service| string 			|<center>Y</center>| Interface name: fixed value: llpth.alipay.offline.refund.query| 
| merchant_id | string(20) 	|<center>Y</center>| Unique identifier for merchant assigned by LianLian | 
| merchant_refund_id |string(64）|<center>Y</center>|Merchant refund order ID | 

<b>Field Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

| Response Parameters: Field  | Type| Required  | Description|
| --- | --- | --- | --- |  
| merchant_id| string(32)		|<center>Y</center>|Unique identifier for merchant assigned by LianLian |
| merchant_refund_id| string(64)|<center>Y</center>| Merchant refund order ID| 
| merchant_order_id	| string(64)|<center>Y</center>| Merchant original order ID| 
|refund_status	|string(8)		|<center>M</center>|Refund status|
|refund_amount	|string(8, 2)	|<center>Y</center>|Refund amount, cannot be greater than the payment amount|
|refund_currency	|string(3)	|<center>Y</center>|Refund currency|
|create_time	|string(19)		|<center>Y</center>|Refund initiate time|
|complete_time	|string(19)		|<center>N</center>|Refund complete time|
|memo	|string(1024)			|<center>N</center>|Remark information|



<b>Request sample</b>
```
https://sandbox-th.lianlianpay-inc.com/gateway?version=v1&service=llpth.alipay.offline.refund.query&merchant_id=142019050800009001&merchant_refund_id=test_refund_022
```
<b>Return sample</b>
```
{
    "code": 200000,
	"data": {
		"merchant_id": "142019050800009001",
		"merchant_refund_id": "test_refund_022",
		"merchant_order_id": "test_019",
		"refund_status": "RS",
		"refund_amount": "100.00",
		"refund_currency": "THB",
	    "create_time": "2020-04-07 14:09:38",        
	    "complete_time": "2020-04-07 14:09:40",
		"memo": "refund memo"
    },
    "message": "Success",
    "trace_id": "78a74d643a9a0106"
}
```

## 3. Appendix<!-- {docsify-ignore} -->
> [see Appendix](./#Appendix)