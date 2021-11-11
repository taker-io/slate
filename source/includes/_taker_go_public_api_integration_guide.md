# DSP Integration Guide

## 1 - DSP Introduction

The Public API is a tool that allows to integrate with Taker Go and helps make food delivery a fast and comfy for restaurants and end users.The diagram of communication Taker go with the restaurant business and DSP shown below.

![Block Schema 1](/images/block-schema-1.png "Block Schema 1")

## 2 - Base Urls

- Production API Base URL: <a href="https://production.go.taker.io/" target="_blank">https://production.go.taker.io/</a>
- Sandbox API Base URL: <a href="https://sandbox.go.taker.io/" target="_blank">https://sandbox.go.taker.io/</a>

## 3 - Creating Account

Developer Applications and Sandbox accounts are created by Taker Go team after obtaining some needed information from the developerThe needed information include the following:

## 3.1 - Redirect URL

This is the URL to which you want Taker Go user to be redirected after successfully authorizing your App.You need to provide a direct link for your production application, not a link to the local host for your Sandbox application.

## 3.2 - Webhook URL & Webhook Events

When changing the order status, we expect a Webhook from the DSP (for more information, see section 8.4).

## 3.3 - Install URL and Token

To register your application, we need to get an url-address and Token from you.The authorization processis alwaysinitiated from your App sideWhen your App gets officially listed in Taker Go Marketplace, Taker Go user will be able to install your App directly from the marketplaceWhen user clicks on install, user will be taken to the Install URL that you will use to know that a Taker Go user wants to install your Taker Go AppFrom there, you should initiate the authorization process with Taker Go authorization server

Details should be sent to <strong>support@takergo.com</strong>

## 4 - Security of data transfer

To ensure the security of both systems, the Bearer token is used.

## 5 - Error

If the request is not executed, the API returns Error -422 (E-422)

> The above command returns JSON structured like this:

```json
{
  "error_key": [
    "Error text"
  ]
}
```

## 6 - Order Status

The list of order statuses

```json
const STATUSES = [
    'NO_STATUS'        => 0,
    'PENDING'          => 11,
    'ASSIGNED'         => 12,
    'WAY_TO_STORE'     => 13,
    'ARRIVED_AT_STORE' => 14,
    'WAY_TO_CUSTOMER'  => 15,
    'NEAR_CUSTOMER'    => 16,
    'DELIVERED'        => 21,
    'CANCELED'         => 31,
    'RETURNED'         => 32,
    'DSP_ISSUE'        => 33,
    'NOT_SENT'         => 34
];
```
## 7 - Payment Types from Taker Go

You can make order in the mobile application, on the website, by phone number or order at a kiosk and pay for the order as follows:

```json
const PAYMENT_TYPE = [
    'CASH'      =>  0,
    'ONLINE'    =>  1,
    'POINTS'    =>  2,
    'TERMINAL'  =>  3
];
```

## 8 - Work with external API

`DSP_URL` - Installed from the side of the Taker.

`DSP_TOKEN` - Installed from the side of the Taker.

`TAKER_TOKEN` - Installed from the side of the Taker.

`id` – Taker Go Order ID.

## 8.1 - Public Create Order (in DSP)

The request for a Public Create Order (in DSP) must contain the following data:

**Endpoint**: `POST - DSP_URL/orders`

> Headers

```json
{
  "Authorization" : "Bearer DSP_TOKEN",
  "Content-Type": "application/json"
}
```

> Body

```json
{
  "id": integer,
  "pickup_details": {
    "name": string,
    "phone": string(ex: "+966555633706"),
    "coordinate": {
      "latitude": float,
      "longitude": float
    },
    "address": string
  },
  "delivery_details": {
    "name": string,
    "phone": string(ex: "+966555633706"),
    "coordinate": {
      "latitude": float,
      "longitude": float
    },"address": string
  },
  "order": {
    "reference": string,
    "payment_type": int (PAYMENT_TYPE),
    "currency": string(ex: "SAR"), 
    "total": float,
    "notes": {
      "ar": string,
      "en": string
    }
  },
  "business": {
    "name": string,
    "phone": string(ex: "+966555633706"),
    "logo": url
  }
}
```

**Expected Response**

Expected “Status Code” - `200`

> Expected Response Body

```json
{
  "dsp_order_id":  string, // DSP order ID
  "status": STATUSES
}
```

## 8.2 - Public Get Order (in DSP)

The request for a Public GetOrder (in DSP) must contain the following data:

**Endpoint**: `GET - DSP_URL/orders/{ID}`

> Headers

```json
{
  "Authorization" : "Bearer DSP_TOKEN",
  "Content-Type": "application/json"
}
```

> Body

```json
{}
```

**Expected Response**

Expected “Status Code” - `200`

> Expected Response Body

```json
{
  "dsp_order_id", string,
  "status": STATUSES,
// IF Status "ASSIGNED" or more
  "driver":? {
      "name": string,
      "phone": string(ex: "+966555633706"),
      "image":? url,
      "rating":? float (ex: 4.32)
  }
}
```

## 8.3 - Public Delete Order (in DSP)

The request for a Public DeleteOrder (in DSP) must contain the following data:

**Endpoint**: `DELETE - DSP_URL/orders/{ID}`

> Headers

```json
{
  "Authorization" : "Bearer DSP_TOKEN",
  "Content-Type": "application/json"
}
```

> Body

```json
{}
```
**Expected Response**

Expected “Status Code” - `200`

> Expected Response Body

```json
{}
```

## 8.4 - Expected Webhook (from DSP)

The request for Expected Webhook (from DSP)must contain the following data:

**Endpoint**: `POST - /webhooks/public`

**Status** - <span style="color: green">Success</span>

> Headers

```json
{
  "Authorization" : "Bearer DSP_TOKEN",
  "Content-Type": "application/json"
}
```

> Body

```json
{
  "dsp_order_id", string,
  "status": STATUSES,
// IF Status "ASSIGNED" or more
  "driver":? {
      "name": string,
      "phone": string(ex: "+966555633706"),
      "image":? url,
      "rating":? float (ex: 4.32)
  }
}
```

**Expected Response**

Expected “Status Code” - `200`

> Expected Response Body

```json
{}
```

**Status** - <span style="color: red">Error</span>

**Expected Response**

Expected “Status Code” - `422`

> Expected Response Body

```json
{}
```
