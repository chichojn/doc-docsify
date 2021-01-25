# Getting Started

<!-- **[1. Overview](#OverView)**
- [Document Purpose](#DocPurpose)
- [Environment Configuration](#EnvCon)
- [Technical Support](#TechSup)

**[2. Message Specification and Security](#SpecSecure)**
- [Message Encoding and Structure](#MessEndcode)
	- [HTTP HEADER](#Http)
	- [GENERAL RESPONSE MESSAGE](#GenResMess)
- [Digital Signature](#DigitSign)
	- [Request Signature](#RequestSign)
	- [Response Message Signature](#ResponseSign)
- [Data type explanation](#DataTypeEx)

**[3. Appendix](#Appendix)**
- [Currency](#Currency)
- [Payment Order status](#POrderStatus)
- [Refund status](#RefundStatus)
- [Return code](#ReturnCode) -->

<a name="OverView" id="overview"></a>
## 1. Overview

<a name="DocPurpose" id="docpurpose"></a>
### 1.1 Document Purpose

>This document is provided by Lianlian Pay Electronic Payment (Thailand) Co., Ltd( Lianlian Thailand), for the purpose of enabling merchant and platform to integrate with LianLian Pay’s Thailand payment gateway. The targeted readers are technical developers of platform or merchant.

<a name="EnvCon" id="envcon"></a>
### 1.2 Environment Configuration
>LianLian Pay will provide the following information to merchant before integration. Meanwhile the merchant also needs to give their public key and IP into LianLian Thailand for configuration, merchants that integrate with Checkout Page must provide their domain names additionally. The following settings are different in test and production environment.

|Name| Description |Remark |
| --- | --- |  --- | 
| merchant_id  | ID assigned to merchant by LianLian Pay||
| public_key  | Public key of LianLian Pay ||
| store_id  | ID assigned to store belonged to merchant by LianLian Pay |for in-store merchants only|

<a name="TechSup" id="techsup"></a>
### 1.3 Technical Support
| Name | email | 
| --- | --- | 
| IT support  | support@lianlianpay.co.th|

<a name="SpecSecure" id="specsecure"></a>
## 2. Message Specification and Security

<a name="MessEndcode" id="messendcode"></a>
### 2.1 Message Encoding and Structure

>All messages of Lianlian Thailand API are communicated through POST/GET of HTTP protocol. The request type of POST is application/json ，UTF-8 code. Please refer to the below examples.

<a name="Http" id="http"></a>
#### 2.1.1. HTTP HEADER

Merchant to Lianlian

|<div style="width:90px">Field</div>|<div style="width:90px">Type</div>|<div style="width:50px">Required</div>|<div style="width:210px">Description</div>|
| --- | --- |---| --- |
| sign-type| string(3)| <center>N</center>| Digital signature type ：RSA |
| sign| string(128) |<center>Y</center>| Digital signature |
| Content-Type| string(32)| <center>N </center>|Application/json for HTTP POST|

Lianlian to Merchant

|<div style="width:90px">Field</div>| <div style="width:90px">Type</div> |<div style="width:50px">Required</div>|<div style="width:210px">Description</div>|
| --- | --- |---| --- |
| sign-type| string(3)|<center>N</center>| Digital signature type ：RSA |
| sign| string(128) |<center>N</center>| Digital signature |

<a name="GenResMess" id="genresmess"></a>
#### 2.1.2. GENERAL RESPONSE MESSAGE

|<div style="width:90px">Field</div>| <div style="width:90px">Type</div> |<div style="width:50px">Required</div>|<div style="width:210px">Description</div>|
| --- | --- |---| --- |
| code| string(6)|<center>Y</center>| [See 3.4](#ReturnCode) |
| message | string(128) |<center>N</center>| [See 3.4](#ReturnCode) |
| data | object |<center>N</center>| Business data |
| trace_id| string(32)| <center>Y</center>|ID Transaction Trace ID|

<a name="DigitSign" id="digitsign"></a>
### 2.2 Digital Signature
>The message must have RSA digital signature, HTTP HEADER won’t participate signature, and the return message will participate signature when it return with success.

<a name="RequestSign" id="requestsign"></a>
#### 2.2.1. Request Signature
>The range for GET requesting signature is : Query，The range for POST requesting signature is : Body.
- Query: In lexicographic order, order key1=value1& key2=value2, mark it as Y
- Body: In the order of key, order as key1=value1& key2=value2, when the value type is JSON, continue to sort in ascending order according to the JSON key, add the result to the sorted string, when value type is JSON array, get the element one by one, and then order as the key element, mark it as Z. 

Extract GET Signature string Request as: Y; extract POST request as Z。And then use SHA1withRSA

Extract the Body signature string as below：
```
{
	"c":{"b":"11","a":"10"},
	"a":"100",
	"b":[{"c":"3","b":"2","a":"1"},{"c":"6","b":"5","a":"4"}]
}
```
- Query: In lexicographic order, order key1=value1& key2=value2, mark it as Y
- Body:

<b>Step 1</b>: Follow the ascending order of outermost layer and get:
<ul><ul>1. "a":"100" </ul></ul>
<ul><ul>2. "b":[{"c":"3","b":"2","a":"1"},{"c":"6","b":"5","a":"4"}]</ul></ul>
<ul><ul>3. "c":{"b":"11","a":"10"}</ul></ul>

<b>Step2</b>: take 1（a）to get current string as a=100,<br>
<b>Step3</b>: take 2（b） as arrayjson
		      And then take the first element of {"c":"3","b":"2","a":"1"} to order c、b、a as a、b、c and get the<code>order string</code>as a=100&a=1&b=2&c=3，<br>
		      Continue to get the second element{"b":"5","c":"6","a":"4"} and do ascending order of b、
		      c、a as a、b、c and get<code>order string</code>as a=100&a=1&b=2&c=3&a=4&b=5&c=6<br>
<b>Step 4</b>: When taking 3(c), its type is json, following the ascending order of the key, the final<code>order string</code>is:
```
a=100&a=1&b=2&c=3&a=4&b=5&c=6&a=10&b=11
```
<a name="ResponseSign" id="responsesign"></a>
#### 2.2.2. Response Message Signature
>Don’t add signature when transaction fails. When transaction succeed, if data is not empty, then
add signature to data. Please refer to the example above to add signature.

<a name="DataTypeEx" id="datatypeex"></a>
### 2.3 Data type explanation
|Data Type Syntax | Description |
| --- | --- |
| string(p,s)| Amount string, p,s are all positive integer, p means precision, s is decimal places|
| string(n)  | n is a positive integer indicating the maximum length of the string |

<a name="Appendix" id="appendix"></a>
## 3. Appendix

<a name="Currency" id="currency"></a>
### 3.1 Currency
|<div style="width:95px">Currency Code</div>|<div style="width:130px">Currency Name</div>|
| --- | --- |
|THB| Thai Baht|

<a name="POrderStatus" id="porderstatus"></a>
### 3.2 Order status
|<div style="width:95px">Order status</div>|<div style="width:130px">Description</div>|
| --- | --- |
| PI| Initialized not paid|
| WP| Waiting for Payment|
| PS| Payment Success|
| PF| Payment Failed |
| PE| Payment Expired|
| PC| Payment Cancelled|

<a name="RefundStatus" id="refundstatus"></a>
### 3.3 Refund status
|<div style="width:95px">Order status</div>|<div style="width:130px">Description</div>|
| --- | --- |
| RP| Refund Processing|
| RS| Refund Success|
| RF| Refund Failed |

<a name="ReturnCode" id="returncode"></a>
### 3.4 Return Code
#### 3.4.1 Common
| Code |<div style="width:330px">Message</div> |
| --- | --- | 
|200000 | Success |  
|400000	|Parameters error message|
|401001	|Merchant signature invalid|
|401003	|Merchant biz access not authorized|
|401006	|IP restricted|
|403000	|Invalid card info|
|403500	|Suspicious transaction|
|404002	|Merchant not found|
|404003	|Payment order not found   |    
|404005	|Merchant config not found|
|404007	|Merchant biz config not found|
|404009	|Merchant config info not found|
|409001	|Merchant status invalid|
|429000	|Too many requests|
|500001	|Internal server error|
|503001	|Service unavailable|
|504001	|Service timeout|

#### 3.4.2 Payment
| Code |<div style="width:330px">Message</div> |
| --- | --- | 
|401008 | Illegal amount | 
|403001	|Expired card |                          
|403002	|Amount exceed|                  
|403003	|Invalid transaction|                
|403004	|Authentication failed    |             
|403005	|Card type not supported|  
|404008	|Card info not found  |                 
|408001	|Session timeout|
|409002	|The order status don't support payment|
|422009	|Payment re-submit|
|429001	|Payment re-submit      |     
|429003	|The payment data is inconsistent|
|429006	|Payment succeed|
|430001	|Payer not exist|
|430002	|Payer invalid|
|430003	|Payer balance not enough|
|430004	|Barcode invalid|
|430005	|Barcode expired|
|430006	|payer mismatch|
|430007	|card not support for payment|

#### 3.4.3 Cancel
| Code |<div style="width:330px">Message</div> |
| --- | --- | 
 422018 | Cancel not allowed due to payment success | 
|422019 | Cancel not allowed due to payment is in progress | 

#### 3.4.3 Refund
| Code |<div style="width:330px">Message</div> |
| --- | --- | 
|404004 | Merchant account not found | 
|404006	|Refund order not found|
|409002	|The order status don't support refund|
|409005	|Refund currency not in accordance with the payment|
|422001	|Refund amount exceed payment amount|
|422002	|Merchant account balance not enough|
|422003	|Refund re-submit|
|422004	|Refund expired|
|422006	|Not support partial refund|
|422007	|Not support UnionPay refund|
|422010	|Refund not allowed due to payment not success|