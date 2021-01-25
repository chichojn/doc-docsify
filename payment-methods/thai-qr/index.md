# Lianlian Pay (Thailand) Payment Gateway (Thai QR) Integration Guide Version 1.1


## 1. Integration Method
>There are two ways to integrate with LianLian Pay Thailand, Direct API and Checkout page. 

#### 1.1 Direct API 
<div align="center">
<img width="700" src="./images/thai-qr/direct-api.PNG"/>
</div>

#### 1.2 Checkout Page
<div align="center">
<img width="700" src="./images/thai-qr/checkout-page.PNG"/>
</div>


## 2. Object Definition
<a name="Product" id="product"></a>
### 2.1 Product
|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- | --- | --- | 
|name	|string(128)		|<center>Y</center>|Product name| 
|description	|string(128)|<center>N</center>|Product description, no special characters are permitted |
|unit_price	|string(15,2)	|<center>Y</center>|Product price, keep 2 decimals |
|quantity	|string(10)		|<center>Y</center>|Product quantity, positive integer |
|category	|string (64)	|<center>N</center>|Product category |
|show_url	|string(256)	|<center>N</center>|Product URL, it’s mandatory when payment method is credit card |

<a name="Address" id="address"></a>
### 2.2 Address 
|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:407px">Description</div>|
| --- | --- |--- |--- |
|street_name	|string(128)|<center>N</center>|Street name of customer|
|house_number	|string(32)	|<center>N</center>|House number of customer|
|district	|string(32)		|<center>N</center>|District|
|city	|string(64)			|<center>N</center>|City|
|state	|string(2)			|<center>N</center>|State/province, abbreviations |
|country	|string(2)		|<center>N</center>|Country abbreviations |
|postal_code	|string(16)	|<center>N</center>|Zip code|

<a name="Customer" id="customer"></a>
### 2.3. Customer 
|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:407px">Description</div>|
| --- | --- |--- |--- |
|merchant_user_id|	string(64)	|<center>Y</center>|The unique symbol of buyer in the merchant|
|customer_type	|string(1)		|<center>N</center>|Customer identity type, currently support individual only|
|first_name	|string(64)			|<center>N</center>|Only for individual|
|last_name	|string(64)			|<center>N</center>|Only for individual|
|full_name	|string(128)		|<center>Y</center>|Full name of individual or company|
|gender	|string(16)				|<center>N</center>|Gender: MALE, FEMALE, UNKNOWN|
|id_type	|string(16)			|<center>N</center>|Identity type|
|id_no	|string(32)				|<center>N</center>|Identify number|
|email	|string(64)				|<center>N</center>|Email address of customer|
|phone	|string(32)				|<center>N</center>|Format:"+country or district code-mobile number"|
|company	|string(128)		|<center>N</center>|Company name where the individual works|
|address	|Address 			|<center>N</center>|Address information, [see 2.2](#Address)|

## 3. API Definition
### 3.1 Payment
>Merchant order ID needs to be unique, Non-PI status orders are not allowed to initiate repeated submissions.

For repeated submissions of merchant payment order requests, due to information security considerations, it is necessary to ensure that the following fields are the same as the initial submission :"customer", "merchant_order_id"，"merchant_id", "order_amount", "order_currency".

Other information still uses the information submitted for the first time

#### 3.1.1 Direct API Payment Request

<b>Request Parameters:</b>

|<div style="width:122px" align="left"> Sandbox URL </div>|<div style="width:564px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:110px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
| --- | --- | --- | --- | 
|version 	|String			|<center>Y</center>|v1|
|service	|String			|<center>Y</center>|llpth.thaiqr.pay|
|merchant_id	|String(20)	|<center>Y</center>|Unique identifier for merchant assigned by LianLian |
|merchant_order_id	|string(64）	|<center>Y</center>|Merchant transaction ID |
|order_amount	|string(8, 2)	|<center>Y</center>|Order amount |
|order_currency	|string(3)	|<center>Y</center>|Currency|
|order_desc	|string(256)	|<center>Y</center>|Display info. to buyer|
|payment_method	|string(32)	|<center>Y</center>|Payment method:THAI_QR|
|customer |Customer 		|<center>Y</center>|[see 2.3](#Customer) for customer information |
|products	|List<[Product](#Product)> |<center>N</center>|Product info.|
|notify_url	|string(256)	|<center>Y</center>|Payment result notification URL|
|redirect_url	|string(256)|<center>Y</center>|URL that user is redirected when payment is done. |

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
| --- | --- | --- | --- | 
|merchant_id	|string(20)		|<center>Y</center>|Merchant id|
|merchant_order_id	|string(64）	|<center>Y</center>|Merchant transaction ID |
|order_id	|string(20)			|<center>Y</center>|Order ID|
|order_status	|string(8)		|<center>Y</center>|Order status|
|order_amount	|string (8,2)	|<center>Y</center>|Order amount|
|order_currency	|string (3)		|<center>Y</center>|Order currency|
|qr_code	|string				|<center>N</center>|Base64|
|qr_code_expire_sec	|string(10)	|<center>N</center>|QR valid time (in second)|
|create_time	|string (19)	|<center>Y</center>|Order time|
|link_url	|string(256)		|<center>Y</center>|	Issuing bank address|

<b>Request sample</b>
```
{
	"version": "v1",
	"service": "llpth.thaiqr.pay",
	"merchant_id": "142019050800009001",
	"merchant_order_id": "test_020",
	"order_amount": "100.00",
	"order_currency": "THB",
	"order_desc": "display your order info",
	"payment_method": "THAI_QR",
	"customer": {
		"merchant_user_id": "m_user_01",
		"full_name": "coba"
	},
	"notify_url": "https://merchant_notify_url",
	"redirect_url": "https://merchant_redirect_url"
}
```

<b>Return sample</b>
```
{
	"code": 200000,
	"data": {
		"merchant_id": "142019050800009001",
		"merchant_order_id": "test_020",
		"order_id": "122020040700160021",
		"order_status": "PI",
		"order_amount": "100.00",
		"order_currency": "THB",
		"qr_code": "",
		"qr_code_expire_sec": "",
		"create_time": "2020-04-07 13:19:02",
		"link_url": "bank_url"
    },
    "message": "Success",
    "trace_id": "54e2983e66b738e6"
}
```

#### 3.1.2 Checkout Page Payment Request
<b>Request Parameters:</b>

|<div style="width:122px" align="left"> Sandbox URL </div>|<div style="width:564px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:110px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
| --- | --- | --- | --- | 
|version	|String			|<center>Y</center>|v1|
|service	|String			|<center>Y</center>|llpth.checkout.apply|
|merchant_id	|String(20)	|<center>Y</center>|Unique identifier for merchant assigned by LianLian|
|merchant_order_id	|string (64）	|<center>Y</center>|Merchant order ID |
|order_amount	|string (8, 2)		|<center>Y</center>|Order amount|
|order_currency	|string (3)	|<center>Y</center>|currency|
|order_desc	|string (256)	|<center>Y</center>|Checkout page display info.|
|customer	|Customer 		|<center>Y</center>|[see 2.3](#Customer) for customer information |
|products	|List<[Product](#Product)> |<center>N</center>|Product info.|
|notify_url	|string(256)	|<center>Y</center>|Payment result notification URL|
|redirect_url	|string(256)|<center>Y</center>|URL that user is redirected when payment is done.|

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
| --- | --- | --- | --- | 
|merchant_id	|string(20)		|<center>Y</center>|Merchant id|
|merchant_order_id	|string(64）	|<center>Y</center>|Merchant transaction ID |
|order_id	|string(20)			|<center>Y</center>|Order ID|
|order_status	|string(8)		|<center>Y</center>|Order status|
|order_amount	|string (8,2)	|<center>Y</center>|Order amount|
|order_currency	|string (3)		|<center>Y</center>|currency|
|create_time	|string (19)	|<center>Y</center>|Order create time|
|link_url	|string(256)		|<center>Y</center>|	Checkout page URL|

```
{
	"version": "v1",
	"service": "llpth.checkout.apply",
	"merchant_id": "142019050800009001",
	"merchant_order_id": "test_020",
	"order_amount": "100.00",
	"order_currency": "THB",
	"order_desc": "display your order info",
	"payment_method": "THAI_QR",
	"customer": {
		"merchant_user_id": "m_user_01",
		"full_name": "coba"
	},
	"notify_url": "https://merchant_notify_url",
	"redirect_url": "https://merchant_redirect_url"
}
```

<b>Return sample</b>
```
{
	"code": 200000,
	"data": {
		"merchant_id": "142019050800009001",
		"merchant_order_id": "test_020",
		"order_id": "122020040700160021",
		"order_status": "PI",
		"order_amount": "100.00",
		"order_currency": "THB",
		"create_time": "2020-04-07 13:19:02",
		"link_url": " https://sandbox-checkout.lianlianpay-inc.com?nonce=prjbcmxdght4e0i1l&pms=Y2FyZF8x"
    },
    "message": "Success",
    "trace_id": "54e2983e66b738e6"
}
```

#### 3.1.3 ThaiQR Checkout Page Payment Request
<b>Request Parameters:</b>

|<div style="width:122px" align="left"> Sandbox URL </div>|<div style="width:564px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:110px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
| --- | --- | --- | --- | 
|version	|String			|<center>Y</center>|v1|
|service	|String			|<center>Y</center>|llpth.thaiqr.checkout.apply|
|merchant_id	|String(20)	|<center>Y</center>|Unique identifier for merchant assigned by LianLian|
|merchant_order_id	|string (64）|<center>Y</center>|Merchant order ID |
|order_amount	|string (8, 2)	|<center>Y</center>|Order amount|
|order_currency	|string (3)	|<center>Y</center>|currency|
|order_desc	|string (256)	|<center>Y</center>|Checkout page display info.|
|payment_method	|string(32)	|<center>Y</center>|Payment method:THAI_QR|
|customer	|Customer 		|<center>Y</center>|[see 2.3](#Customer) for customer information |
|products	|List<[Product](#Product)> |<center>N</center>|Product info.|
|notify_url	|string(256)	|<center>Y</center>|Payment result notification URL|
|redirect_url	|string(256)|<center>Y</center>|URL that user is redirected when payment is done.|

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
| --- | --- | --- | --- | 
|merchant_id	|string(20)		|<center>Y</center>|Merchant id|
|merchant_order_id	|string(64）	|<center>Y</center>|Merchant transaction ID |
|order_id	|string(20)			|<center>Y</center>|Order ID|
|order_status	|string(8)		|<center>Y</center>|Order status|
|order_amount	|string (8,2)	|<center>Y</center>|Order amount|
|order_currency	|string (3)		|<center>Y</center>|Order currency|
|create_time	|string (19)	|<center>Y</center>|Order time|
|link_url	|string(256)		|<center>Y</center>|	Checkout page URL|

```
{
	"version": "v1",
	"service": "llpth.thaiqr.checkout.apply",
	"merchant_id": "142019050800009001",
	"merchant_order_id": "test_020",
	"order_amount": "100.00",
	"order_currency": "THB",
	"order_desc": "display your order info",
	"payment_method": "THAI_QR",
	"customer": {
		"merchant_user_id": "m_user_01",
		"full_name": "coba"
	},
	"notify_url": "https://merchant_notify_url",
	"redirect_url": "https://merchant_redirect_url"
}
```

<b>Return sample</b>
```
{
	"code": 200000,
	"data": {
		"merchant_id": "142019050800009001",
		"merchant_order_id": "test_020",
		"order_id": "122020040700160021",
		"order_status": "PI",
		"order_amount": "100.00",
		"order_currency": "THB",
		"create_time": "2020-04-07 13:19:02",
		"link_url": " https://sandbox-checkout.lianlianpay-inc.com?nonce=prjbcmxdght4e0i1l&pms=Y2FyZF8x"
	},
    "message": "Success",
    "trace_id": "54e2983e66b738e6"
}
```

#### 3.1.4 Payment result notification
>When the payment confirmation is completed, Lianlian Thailand will push the payment result to the "notify_url" which was provided in the merchant payment application. When the merchant returns, the HTTP code must be 200.

Trigger frequency: 10 mins

Max notification times: 13

<b>Notification Parameters (Lianlian Thailand to merchant):</b>

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
| --- | --- | --- | --- | 
|merchant_id	|String(20)		|<center>Y</center>|Merchant ID|
|order_id	|string(20)			|<center>Y</center>|Order ID|
|merchant_order_id	|string(64)	|<center>Y</center>|Merchant order ID|
|order_status	|string (8)		|<center>Y</center>|Order status|
|order_amount	|string(8,2)	|<center>Y</center>|Order amount|
|order_currency	|string (3)		|<center>Y</center>|Currency|
|complete_time	|string (19)	|<center>N</center>|Create time|

<b>Response Parameters (Merchant to Lianlian Thailand):</b>

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
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

#### 3.1.5 Payment Query

<b>Request Parameters:</b>

|<div style="width:122px" align="left"> Sandbox URL </div>|<div style="width:564px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | GET|

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
| --- | --- | --- | --- | 
| version | string 			|<center>Y</center>| Interface version, fixed value: v1 | 
| service| string 			|<center>Y</center>| llpth.thaiqr.pay.query| 
| merchant_id | string(20) 	|<center>Y</center>| Merchant-id provided by Lianlian Thailand | 
| merchant_order_id |string(64）|<center>Y</center>|Merchant order ID | 

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

| Response Parameters: Field  | Type| Required  | Description|
| --- | --- | --- | --- | 
| merchant_id| string(20)		|<center>Y</center>| Unique identifier for merchant assigned by LianLian |
| merchant_order_id	| string(64)|<center>Y</center>| Payment ID|
| order_id	| string(20)		|<center>Y</center>| Order ID| 
| order_status	| string(8)		|<center>Y</center>| 	Order status| 
| order_amount	| string (8,2)	|<center>Y</center>| 	Order amount| 
| order_currency| 	string (3)	|<center>Y</center>| 	Order currency| 
| order_desc	| string(256) 	|<center>Y</center>| 	Checkout page display info.| 
|product_code	|string(64)		|<center>Y</center>|Product code|
|payment_method	|string(64)		|<center>Y</center>| Payment method|
|customer	|Customer 			|<center>Y</center>|[see 2.3](#Customer) for customer information |
|products	|List<[Product](#Product)> |<center>N</center>|Product info.|
|create_time	|string(19)		|<center>Y</center>|	Order create time|
|complete_time	|string(19)		|<center>N</center>|Payment complete time|

<b>Request sample</b>
```
https://sandbox-th.lianlianpay-inc.com/gateway?version=v1&service=llpth.thaiqr.pay.query&merchant_id=142019050800009001&merchant_order_id=test_019
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
		"order_desc": "display your order info",
		"product_code": "QR_PROMPT",
		"payment_method": "THAI_QR",
		"customer": {
			"merchant_user_id": "m_user_01",
			"full_name": "coba"
		},
		"products": [{
		"name": "mobile phone shell",
		"unit_price": "100.00",
		"quantity": "1",
		}],
		"create_time": "2020-04-07 10:38:18",
		"complete_time": "2020-04-07 10:41:26"
    },
    "message": "Success",
    "trace_id": "c1c2e4fdb9abda7d"
}
```

#### 3.1.6 Checkout Page Redirection
>When the payment is completed, Lianlian Thailand will redirect back to the "redirect_url" which was provided by merchant in the payment application. The redirect method is HTTP POST form. Add signature to other parameters except “sign_type” and “sign”. The final status of the order shall be subject to asynchronous notification or inquiry.                         

<b>Parameters (Lianlian Thailand to merchant):</b>

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:370px">Description</div>|
| --- | --- | --- | --- | 
| merchant_id	| String(20)	|<center>Y</center>| 	Merchant ID| 
| merchant_order_id	| string(64)|<center>Y</center>| Merchant payment order ID| 
| order_id	| string(20)		|<center>Y</center>| 	Lianlian order ID| 
| order_status	| string(8)		|<center>Y</center>| Order status| 
| order_amount	| string(8,2)	|<center>N</center>| Order amount| 
| order_currency	| string(3)	|<center>N</center>|  currency| 
| sign_type	| string(3)			|<center>Y</center>| RSA| 
| sign	| String(256)			|<center>Y</center>| signature| 

## 4. Appendix<!-- {docsify-ignore} -->
> [see Appendix](./#Appendix)