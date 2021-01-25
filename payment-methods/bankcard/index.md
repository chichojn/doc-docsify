# Lianlian Pay (Thailand) Payment Gateway (Bankcard) Integration Guide Version 1.3.1


## 1. Integration Method
>There are two ways to integrate with LianLian Pay Thailand, Direct API and Checkout page. PCI certificated merchants can use either method. Non-PCI uncertificated merchants can choose checkout page only. 

#### 1.1 Direct API 
<div align="center">
<img width="700" src="./images/bankcard/direct-api.PNG"/>
</div>

#### 1.2 Checkout Page
<div align="center">
<img width="700" src="./images/bankcard/checkout-page.PNG"/>
</div>

!> Note: The merchant domain name or IP information will be verified through the HTTP referer header when redirecting to the Lianlian checkout page

## 2. Object Definition
<a name="Product" id="product"></a>
### 2.1 Product
|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:360px">Description</div>|
| --- | --- | --- | --- | 
|name	|string(128)		|Y	|Product name| 
|description	|string(128)|N	|Product description, no special characters are permitted |
|unit_price	|string(15,2)	|Y	|Product price, keep 2 decimals |
|quantity	|string(10)	|Y	|Product quantity, positive integer |
|category	|string (64)	|N	|Product category |
|show_url	|string(256)	|N	|Product URL, it’s mandatory when payment method is credit card |

<a name="Address" id="address"></a>
### 2.2 Address 
|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:408px">Description</div>|
| --- | --- |--- |--- |
|street_name	|string(128)	|N	|Street name of customer|
|house_number	|string(32)	|N	|House number of customer|
|district	|string(32)	|N	|District|
|city	|string(64)	|N	|City|
|state	|string(2)	|N	|State/province, abbreviations |
|country	|string(2)	|N	|Country abbreviations |
|postal_code	|string(16)	|N	|Zip code|

<a name="Customer" id="customer"></a>
### 2.3. Customer 
|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:408px">Description</div>|
| --- | --- |--- |--- |
|merchant_user_id|	string(64)	|Y	|The unique symbol of buyer in the merchant|
|customer_type	|string(1)	|N	|Customer identity type, currently support individual only|
|first_name	|string(64)|	N	|Only for individual|
|last_name	|string(64)	|N	|Only for individual|
|full_name	|string(128)	|Y	|Full name of individual or company|
|gender	|string(16)	|N	|Gender: MALE, FEMALE, UNKNOWN|
|id_type	|string(16)|	N	|Identity type|
|id_no	|string(32)	|N	|Identify number|
|email	|string(64)|	N	|Email address of customer|
|phone	|string(32)|	N	|Format:"+country or district code-mobile number"|
|company	|string(128)|	N	|Company name where the individual works|
|address	|Address| N	|Address information, [see 2.2](#Address)|

<a name="Card" id="card"></a>
### 2.4. Card 
|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:408px">Description</div>|
| --- | --- |--- |--- |
|holder_name	|string(128)	|Y	|Name of cardholder|
|card_no	|string(32)	|Y	|Card number|
|card_type	|string(2)	|Y	|Cardtype: D=debit card, C=credit card|
|bank_code	|string(32)	|N	|Bank code|
|brand	|string(32)	|N	|Card brand : visa/mastercard/jcb/Union pay|
|exp_year	|string(2)	|Y	|Expiration Year, as shown on the card|
|exp_month	|string(2)	|Y	|Expiration Month, as shown on the card|
|cvv2	|string(3)	|N	|Card verification value|
|billing_address	|Address |N	|[see 2.2](#Address)|


## 3. API Definition
### 3.1 Payment
>Merchant order ID needs to be unique, Non-PI status orders are not allowed to initiate repeated submissions.

For repeated submissions of merchant payment order requests, due to information security considerations, it is necessary to ensure that the following fields are the same as the initial submission :"customer", "merchant_order_id"，"merchant_id", "order_amount", "order_currency".

Non-bank card information still uses the information submitted for the first time.

#### 3.1.1 Direct API Payment Request

<b>Request Parameters:</b>

|<div style="width:122px" align="left"> Sandbox URL </div>|<div style="width:584px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:110px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
|version 	|String	|Y	|Interface version number, fixed value: v1|
|service	|String	|Y	|Interface name, fixed value: llpth.bankcard.pay|
|merchant_id	|String(20)	|Y	|Unique identifier for merchant assigned by LianLian |
|merchant_order_id	|string(64）	|Y	|Merchant transaction ID |
|order_amount	|string(8, 2)	|Y	|Order amount |
|order_currency	|string(3)	|Y	|Currency|
|order_desc	|string(256)	|Y	|Display info. to buyer|
|payment_method	|string(32)	|Y	|Payment method:THAI_QR|
|customer |Customer |Y	|[see 2.3](#Customer) for customer information |
|products	|List<[Product](#Product)> |N	|Product info.|
|card	|[Card](#Card) |N	|Bank card info. Mandatory for bankcard payment |
|notify_url	|string(256)	|Y	|Payment result notification URL|
|redirect_url	|string(256)	|Y	|URL that user is redirected when payment is done. |

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
|merchant_id	|string(20)	|Y	|Unique identifier for merchant assigned by LianLian |
|merchant_order_id	|string(64）|	Y	|Merchant order ID |
|order_id	|string(20)	|Y	|Order ID|
|order_status	|string(8)	|Y	|Order status|
|order_amount	|string (8,2)	|Y	|Order amount|
|order_currency	|string (3)	|Y	|Order currency|
|create_time	|string (19)	|Y	|Order time|
|link_url	|string(256)	|Y|	Issuing bank address|

<b>Request sample</b>
```
{
	"version": "v1",
	"service": "llpth.bankcard.pay",
	"merchant_id": "142019050800009001",
	"merchant_order_id": "test_020",
	"order_amount": "100.00",
	"order_currency": "THB",
	"order_desc": "display your order info",
	"payment_method": "CARD",
	"customer": {
		"merchant_user_id": "m_user_01", 
		"full_name": "coba"
	},
	"card": {
		"holder_name": "user_name", 
		"card_no": "5404888000072727", 
		"card_type": "C", 
		"exp_year": "23", 
		"exp_month": "12",
		"cvv2": "123"
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
		"link_url": "bank_url"
    },
    "message": "Success",
    "trace_id": "54e2983e66b738e6"
}
```

#### 3.1.2 Checkout Page Payment Request
<b>Request Parameters:</b>

|<div style="width:122px" align="left"> Sandbox URL </div>|<div style="width:584px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:110px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
|version	|String	|Y	|Interface version number, fixed value: v1|
|service	|String	|Y	|Interface name, fixed value: llpth.checkout.apply, <br>Recommended for merchants who have access to multiple payment products of Lianlian TH|
|merchant_id	|String(20)	|Y	|Unique identifier for merchant assigned by LianLian|
|merchant_order_id	|string (64）	|Y	|Merchant order ID |
|order_amount	|string (8, 2)	|Y	|Order amount|
|order_currency	|string (3)	|Y	 |currency|
|order_desc	|string (256)	|Y	|Checkout page display info.|
|customer	|Customer |Y	|[see 2.3](#Customer) for customer information |
|products	|List<[Product](#Product)> |N	|Product info.|
|card	|[Card](#Card) |N	|Bank card info. |
|notify_url	|string(256)	|Y	|Payment result notification URL|
|redirect_url	|string(256)	|Y	|URL that user is redirected when payment is done.|

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
|merchant_id	|string(20)	|Y	|Unique identifier for merchant assigned by LianLian |
|merchant_order_id	|string(64）|	Y	|Merchant transaction ID |
|order_id	|string(20)	|Y	|Order ID|
|order_status	|string(8)	|Y	|Order status|
|order_amount	|string (8,2)	|Y	|Order amount|
|order_currency	|string (3)	|Y	|currency|
|create_time	|string (19)	|Y	|Order create time|
|link_url	|string(256)	|Y|	Checkout page URL|

```
{
	"version": "v1",
	"service": "llpth.checkout.apply",
	"merchant_id": "142019050800009001",
	"merchant_order_id": "test_020",
	"order_amount": "100.00",
	"order_currency": "THB",
	"order_desc": "display your order info",
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

#### 3.1.3 Bankcard Checkout Page Payment Request
<b>Request Parameters:</b>

|<div style="width:122px" align="left"> Sandbox URL </div>|<div style="width:584px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:110px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
|version	|String	|Y	|Interface version number, fixed value: v1|
|service	|String	|Y	|Interface name, fixed value: llpth.checkout.apply,<br> Recommended for merchants who have access to multiple payment products of Lianlian TH|
|merchant_id	|String(20)	|Y	|Unique identifier for merchant assigned by LianLian|
|merchant_order_id	|string (64）	|Y	|Merchant order ID |
|order_amount	|string (8, 2)	|Y	|Order amount|
|order_currency	|string (3)	|Y	 |currency|
|order_desc	|string (256)	|Y	|Checkout page display info.|
|payment_method	|string(32)	|Y	|Payment method:<br>CARD(Debit/Credit card) <br>CREDIT_CARD(Credit card) <br>DEBIT_CARD(Debit card)|
|customer	|Customer |Y	|[see 2.3](#Customer) for customer information |
|products	|List<[Product](#Product)> |N	|Product info.|
|card	|[Card](#Card) |N	|Bank card info. |
|notify_url	|string(256)	|Y	|Payment result notification URL|
|redirect_url	|string(256)	|Y	|URL that user is redirected when payment is done.|

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
|merchant_id	|string(20)	|Y	|Unique identifier for merchant assigned by LianLian |
|merchant_order_id	|string(64）|	Y	|Merchant order ID |
|order_id	|string(20)	|Y	|Order ID|
|order_status	|string(8)	|Y	|Order status|
|order_amount	|string (8,2)	|Y	|Order amount|
|order_currency	|string (3)	|Y	|Order currency|
|create_time	|string (19)	|Y	|Order time|
|link_url	|string(256)	|Y|	Checkout page URL|

```
{
	"version": "v1",
	"service": "llpth.bankcard.checkout.apply",
	"merchant_id": "142019050800009001",
	"merchant_order_id": "test_020",
	"order_amount": "100.00",
	"order_currency": "THB",
	"order_desc": "display your order info",
	"payment_method": "CARD",
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

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
|order_id	|string(20)	|Y	|Order ID|
|merchant_order_id	|string(64)	|Y	|Merchant order ID|
|order_status	|string (8)	|Y	|Order status|
|order_amount	|string(8,2)	|Y	|Order amount|
|order_currency	|string (3)	|Y	|Currency|
|complete_time	|string (19)	|N	|Create time|

<b>Response Parameters (Merchant to Lianlian Thailand):</b>

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
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

#### 3.1.5 Payment Query

<b>Request Parameters:</b>

|<div style="width:122px" align="left"> Sandbox URL </div>|<div style="width:584px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>|  
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | GET|

|<div style="width:110px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
| version | string | Y | Interface version, fixed value: v1 | 
| service| string |Y | Interface name, fixed value: llpth.bankcard.pay.query| 
| merchant_id | string(20) | Y | Merchant-id provided by Lianlian Thailand | 
| merchant_order_id |string(64）|	Y|Merchant order ID | 

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

| Response Parameters: Field  | Type| Required  | Description|
| --- | --- | --- | --- | 
| merchant_id| string(20)| Y| Unique identifier for merchant assigned by LianLian |
| merchant_order_id	| string(64)| Y| Payment ID|
| order_id	| string(20)| Y	| Order ID| 
| order_status	| string(8)| 	Y| 	Order status| 
| order_amount	| string (8,2)| Y| 	Order amount| 
| order_currency| 	string (3)| Y| 	Order currency| 
| order_desc	| string(256) | Y| 	Checkout page display info.| 
|product_code	|string(64)	|Y	|Product code|
|payment_method	|string(64)	|Y  | Payment method|
|customer	|Customer |Y	|[see 2.3](#Customer) for customer information |
|products	|List<[Product](#Product)> |N	|Product info.|
|create_time	|string(19)	|Y|	Order create time|
|complete_time	|string(19)	|N	|Payment complete time|

<b>Request sample</b>
```
https://sandbox-th.lianlianpay-inc.com/gateway?version=v1&service=llpth.bankcard.pay.query&merchant_id=142019050800009001&merchant_order_id=test_019
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
		"product_code": "BANKCARD_PAY_ONLINE",
		"payment_method": "CARD",
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

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
| merchant_order_id	| string(64)	| Y	| Merchant payment order ID| 
| order_id	| string(20)	| Y| 	Lianlian order ID| 
| order_status	| string(8)| 	Y	| Order status| 
| order_amount	| string(8,2)	| N	| Order amount| 
| order_currency	| string(3)| 	N	|  currency| 
| sign_type	| string(3)	| Y	| RSA| 
| sign	| String(256)	| Y	| signature| 

### 3.2 Refund
#### 3.2.1 Refund Request

b>Request Parameters:</b>

|<div style="width:129px" align="left"> Sandbox URL </div>|<div style="width:584px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>|  
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | POST|

|<div style="width:110px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
| version | string | Y | Interface version, fixed value: v1 | 
| service| string |Y | Interface name, fixed value: llpth.bankcard.pay.query| 
| merchant_id | string(20) | Y | Unique identifier for merchant assigned by LianLian Pay | 
| merchant_refund_id | string(64) | Y | Merchant refund ID, need to guarantee its uniqueness | 
| merchant_order_id |string(64）|	Y|Original transaction ID of merchan | 
| refund_amount	| string(8, 2)	| Y	| Refund amount, it cannot be greater than payment amount| 
| refund_currency	| string(3)	| Y	| Refund currency| 
| refund_reason| 	string(256)| 	N	| Refund reason| 
| notify_url	| string(256)| 	N	| URL of refund notification| 


<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

| Response Parameters: Field  | Type| Required  | Description|
| --- | --- | --- | --- | 
| merchant_id| string(20)| Y| Unique identifier for merchant assigned by LianLian |
| refund_order _id	|string(20)	|Y	|Refund order ID|
| merchant_refund_id	|string(64)	|Y	|Merchant refund ID|
| refund_amount	|string(8,2)	|Y	|Refund amount|
| refund_currency	|string(3)	|Y	 |Refund currency|
| refund_status	|string(8)	|Y	|Refund status|
| create_time	|string(19)	|Y	|Create time|


<b>Request sample</b>
```
}
	"version": "v1",
	"service": "llpth.bankcard.refund",
	"merchant_id": "142019050800009001",
	"merchant_refund_id": "test_refund_022",
	"merchant_order_id": "test_019",	
	"refund_amount": "100.00",
	"refund_currency": "THB",
	"refund_reason": "refund reason",
	"notify_url": ""
}
```
<b>Return sample</b>
```
{
	"code": 200000,
    "message": "Success",
    "data": {
		"merchant_id": "142019050800009001",
	    "refund_order_id": "122020040700160037",
	    "merchant_refund_id": "test_refund_022",
	    "refund_amount": "100.00",
	    "refund_currency": "THB",
	    "refund_status": "RP",
	    "create_time": "2020-04-07 14:09:38"
    },
	"trace_id": "4df57b46fe2af2c3"
}
```

#### 3.2.2 Refund Request

>Upon completion of refund, LianLian Pay will push refund result to the “notify_url” submitted by the merchant for application. When the merchant returns, the HTTP code must be 200.

Trigger frequency: 10 mins

Max notification times: 13

<b>Notification Parameters (Lianlian Thailand to merchant):</b>

|<div style="width:110px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
|refund_order_id	|string(20)	|Y	|Lianlian refund order ID|
|merchant_refund_id	|string(64)	|Y	|Merchant refund order ID|
|refund_amount	|string(8,2)	|Y	|Refund amount|
|refund_currency	|string (3)	|Y	|Refund currency|
|refund_status	|string (8)	|Y	|Refund status|
|complete_time	|string(19)	|N	|Refund complete time|


<b>Response Parameters (Merchant to Lianlian Thailand):</b>

|<div style="width:130px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
|code|string(6)|Y | Fixed: 200000|
|message|string(32)|N | Fixed: Success|

<b>Request sample</b>
```
{
	"refund_order_id": "122020040700160037",
    "merchant_refund_id": "test_refund_022",
    "refund_amount": "100.00",
    "refund_currency": "THB",
    "refund_status": "RP",
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

#### 3.2.3 Payment Query

<b>Request Parameters:</b>

|<div style="width:129px" align="left"> Sandbox URL </div>|<div style="width:577px" align="left"> https://sandbox-th.lianlianpay-inc.com/gateway </div>| 
| --- | --- | 
| <b>Production URL</b> |https://api.lianlianpay.co.th/gateway | 
|<b>Request method </b> | GET|

|<div style="width:110px">Body Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:390px">Description</div>|
| --- | --- | --- | --- | 
| version | string | Y | Interface version, fixed value: v1 | 
| service| string |Y | llpth.bankcard.refund.query| 
| merchant_id | string(20) | Y | Merchant-id provided by Lianlian Thailand | 
| merchant_order_id |string(64）|	Y|Merchant refund order ID | 

<b>Response Parameters:</b>

> see [GENERAL RESPONSE MESSAGE](./#GenResMess) for generic response field

| Response Parameters: Field  | Type| Required  | Description|
| --- | --- | --- | --- | 
|merchant_id	|string(32)	|Y	|Unique identifier for merchant assigned by LianLian |
|merchant_refund_id	|string(64)	|Y	|Merchant refund order ID|
|merchant_order_id	|string(64)	|Y	|Merchant original order ID|
|order_id	|string(20)	|Y	|Original order ID|
|refund_order_id	|string(20)	|Y	|Order ID|
|refund_status	|string(8)	|M	|Refund status|
|refund_amount	|string(8, 2)	|Y	|Refund amount, cannot be greater than the payment amount|
|refund_currency	|string(3)	|Y	|Refund currency|
|refund_reason	|string(256)	|N	|Refund reason|
|notify_url	|string(256)	|N	|URL of refund notification|
|create_time	|string(19)	|Y	|Refund create time|
|complete_time	|string(19)	|N	|Refund complete time|


<b>Request sample</b>
```
https://sandbox-th.lianlianpay-inc.com/gateway?version=v1&service=llpth.bankcard.refund.query&merchant_id=142019050800009001&merchant_refund_id=test_refund_022
```
<b>Return sample</b>
```
{
	"code": 200000,
	"data": {
		"merchant_id": "142019050800009001",
		"merchant_refund_id": "test_refund_022",
		"merchant_order_id": "test_019",
		"order_id": "122020040700160016",
		"refund_order_id": "122020040700160037",
      	"refund_status": "RS",
		"refund_amount": "100.00",
		"refund_currency": "THB",
		"refund_reason": "refund reason",
		"notify_url": "",        
      	"create_time": "2020-04-07 14:09:38",        
      	"complete_time": "2020-04-07 14:09:40"
    },
    "message": "Success",
    "trace_id": "78a74d643a9a0106"
}
```

## 4 Appendix<!-- {docsify-ignore} -->
> [see Appendix](./#Appendix)