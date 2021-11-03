# TAKER GO Integration guide Public API for Standalone clients

## 1 - TAKER GO Integration guide Public API for Standalone clients Introduction

The Public API is a tool that allows to integrate with TakerGo service and helps make fooddelivery a fast and comfy for restaurants and end users. The diagram of communicationTakerGo with the restaurant which is not Taker client shown below.

![Block Schema 2](/images/block-schema-2.png "Block Schema 2")

## 2 - Base Urls

- Production API Base URL: <a href="https://production.go.taker.io/" target="_blank">https://production.go.taker.io/</a>
- Sandbox API Base URL: <a href="https://sandbox.go.taker.io/" target="_blank">https://sandbox.go.taker.io/</a>

<aside class="notice">
At first we recommend you create the test environment to test our integration to avoid bugs in production.
</aside>

## 3 - TakerGo Order ID

Be attentive, every delivery in TakerGo has its own ID, so you need to regularly update theID of the order on your side.

## 4 - Creating Account

Sandbox and Production accounts are created by TakerGo team after obtaining some needed information from the restaurant. 

The needed information include the following:

`ORGANIZATION_URL` - Installed from the side of the TakerGo 

This is the URL from which you will send us order details for searching the driver and to which you want TakerGo will send you order statuses. You need to provide a direct link for your production application and a link to your sandbox application for test orders.

`ORGANIZATION_TOKEN` - Installed from the side of the TakerGo

To register your account, we need also to get a Bearer Token from you, Token must include the word Bearer. To ensure the security of both systems, the Bearer token is used.

`TAKERGO_TOKEN` - Installed from the side of the TakerGo

## 5 - Error

If the request is not executed, the webhook returns Error - 4XX (E-4XX)  as follows:

```json
{
  "error_key": [
    "Error text"
  ]
}
```

## 6 - Statuses

The list of order statuses

```json
const STATUSES = [
  'NOT_ASSIGNED'      =>  1,
  'ASSIGNED'          =>  2,
  'COLLECTING_ORDER'  =>  3,
  'ON_THE_WAY'        =>  4,
  'DELIVERED'         =>  5,
  'CANCELED'          =>  6,
  'FAILED_TO_ASSIGN'  =>  7,
  'NEAR_PICK_UP'      =>  8,
  'AT_PICK_UP'        =>  9,
  'NEAR_DELIVERY'     => 10,
  'AT_DELIVERY'       => 11,
  'RETURNING'         => 12,
  'RETURNED'          => 13,
  'RE_ASSIGNING'      => 14
];
```

## 7 - Payment Types

You can make order in the mobile application, on the website, by phone number or order at a kiosk and pay for the order

```json
const PAYMENT_TYPE = [
  'CASH'      =>  0,
  'ONLINE'    =>  1,
  'POINTS'    =>  2,
  'TERMINAL'  =>  3
];
```

## 8 - APIs

List of API endpoints

## 8.1 - Create Order to TakerGo

**Endpoint**: POST - /v2/orders

> Headers

```json
{ 
  "Authorization" : "Bearer TAKERGO_TOKEN", 
  "ContentType": "application /json"
}
```

> Body (New Format)

```json
{
  "pickup_details": { 
    "name": string, 
    "phone": string (ex: "+966555633706"), 
    "coordinate": {
      "latitude": float, 
      "longitude": float
    }, 
    "address": string
  },
  "delivery_details": { 
    "name": string, 
    "phone": string(ex:"+966555633706"), 
    "coordinate": { 
      "latitude": float, 
      "longitude": float
    }, 
    "address": string
  },
  "order": { 
    "id": int, 
    "profile_id": int, 
    "reference": string, 
    "payment_type": int (PAYMENT_TYPE),
    "currency": string (ex: "SAR"), 
    "total": float, 
    "delivery_fee":? float (default: 0), 
    "notes": { 
      "ar": string, 
      "en": string
    }
  }, 
  "branch": { 
    "id": int (ex: 50), 
    "name": string, 
    "phone": string(ex:"+966555633706")
  }, 
  "business": { 
    "id": int (ORGANIZATION_ID), 
    "name": string (ex:"Mathaq Warq Enab; Branch: Twaiq Branch"), 
    "phone": string(ex: "+966555633706"), 
    "logo":url
  }, 
  "customer": { 
    "id": int (ex: 76484), 
    "name": string, 
    "phone_number": string (ex:"+966555633706") 
  }
}
```

> Body (Old Format)

```json
{ 
  "profile_id": int, 
  "organization_id": int,
  "organization_order_id": int, 
  "order_key": string, 
  "branch": { 
    "id": int, 
    "name": string, 
    "phone": string, (ex: "+966538098059"),
    "name_ar": string
  }, 
  "delivery_details": { 
    "building_name": string, 
    "area": string, 
    "coordinate": { 
      "latitude": float, 
      "longitude": float
    }, 
    "street": string, 
    "city": string
  }, 
  "pickup_details": { 
    "building_name": string, 
    "area": string, 
    "coordinate": { 
      "latitude":float, 
      "longitude": float
    }, 
    "street": string, 
    "city": string
  },
  "customer": { 
    "name": string,
    "phone_number": string, (ex: "+966538098059") 
  }, 
  "payment_type": string, (ex: "online","cash", "terminal"),
  "currency": string, (ex: "SAR"),
  "notes": string, 
  "notes_ar": string,
  "restaurant_name": string,
  "restaurant_mobile": string, (ex: "+966538098059"),
  "restaurant_logo": url, 
  "total": 50, 
  "customer_id": int
}
```

**Expected Response**

Expected “Status Code” - `200`

> Expected Response Body

```json
{
  "id": int, 
  "status": int (STATUSES) 
}
```

## 8.2 - Get Order to TakerGo

**Endpoint**: GET - `/v2/orders/{ID}`

> Headers

```json
{ 
  "Authorization" : "Bearer TAKERGO_TOKEN", 
  "ContentType": "application /json"
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
  "id": int, 
  "status": int (STATUSES) 
}
```

## 8.3 - Send order to the next DSP

**Endpoint**: POST - `/v2/orders/{ID}/next_dsp`

> Headers

```json
{ 
  "Authorization" : "Bearer TAKERGO_TOKEN", 
  "ContentType": "application /json"
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
  "id": int, 
  "status": int (STATUSES) 
}
```

## 8.4 - Cancel order

**Endpoint**: DELETE - `/v2/orders/{ID}`

> Headers

```json
{ 
  "Authorization" : "Bearer TAKERGO_TOKEN", 
  "ContentType": "application /json"
}
```

> Body

```json
{}
```

**Expected Response**

Expected “Status Code” - `202`

> Expected Response Body

```json
{}
```

## 8.5 - Webhook Events from the restaurant

When changing the order status, we send you a Webhook from the DSP.

**Endpoint**: POST - `ORGANIZATION_URL/takergo/v2/webhook/order`

> Headers

```json
{ 
  "Authorization" : "Bearer TAKERGO_TOKEN", 
  "ContentType": "application /json"
}
```

> Body

```json
{ 
  "id": int, 
  "dsp_id": int, 
  "dsp_name":? string, 
  "dsp_order_id": string,
  "status": int (STATUSES), 
  "driver":? { 
    "name": string, 
    "phone_number": string, 
    "image_url":? url, 
    "rating":? float 
  }, 
  "profile_id": int, 
  "organization_id": int (ORGANIZATION_ID),
  "organization_order_ id": int
}
```

**Expected Response**

Expected “Status Code” - `200`

> Expected Response Body

```json
{}
```

**Webhook - No attempts**

**Endpoint**: POST - `ORGANIZATION_URL/takergo/v2/webhook/attempts`

> Headers

```json
{ 
  "Authorization" : "Bearer TAKERGO_TOKEN", 
  "ContentType": "application /json"
}
```

> Body

```json
{ 
  "id": int, 
  "dsp_id": int, 
  "dsp_name":? string, 
  "attempts": int (0 or less),
  "profile_id": int, 
  "organization_id": int (ORGANIZATION_ID), 
  "organization_order _id": int
}
```

**Expected Response**

Expected “Status Code” - `200`

> Expected Response Body

```json
{}
```

## 9 - Interface for using events

For using TakerGo service we recommend you to create the interface, which includes the following. The information is given as an example and is for guidance only.

**Dispatching Rules page**:

Profiles, where the restaurant will do settings for branches

The list of DSPs and on/off buttons (all DSPs wich integrated with TakerGo)

The priority fields for DSPs

Branch list (the list of branches with on/off buttons for selected DSPs)

Driver acceptance timeout field (the time for driver searching in each DSP)

![Dispatching rules](/images/dispatching-rules.png "Dispatching rules")

**Order details interface**:

![Order Details](/images/order-details.png "Order Details")

## 10 - Dispatching rules API

<a href="https://taker.atlassian.net/wiki/spaces/TS/pages/746848301/TakerGo+-+APIs+new#Config-APIs" target="_blank">https://taker.atlassian.net/wiki/spaces/TS/pages/746848301/TakerGo+-+APIs+new#Config-APIs</a>
