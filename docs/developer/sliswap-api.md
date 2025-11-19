---
title: Sliswap API
parent: Developer
nav_order: 4
---

# **Sliswap market API**

## **Request common parameters**

- chain: Chain type
  - value 2: Endless

## **Common return results**

- All APIs return 200(OK) Http Status Code.
- All APIs return error code 0: Success.

| **Status code** | **Meaning** | **Description** | **Data model** |
| --------------- | ----------- | --------------- | -------------- |
| 200             | OK          | None            | Json           |

- All API errors are returned in the response body json

```json
{
  "code": "0",
  "msg": "Success",
  "data": []
}
```

| **code** | **msg**           | **data model**           |
| -------- | ----------------- | ------------------------ |
| "0"      | Success           | Value or Object or Array |
| Non-"0"  | Error description | None                     |

## **Pool Statistic**

POST /v1/pool/stats

### **Request parameters**

| **Name**    | **Location** | **Type** | **Option** | **Description** |
| ----------- | ------------ | -------- | ---------- | --------------- |
| chain       | body         | integer  | Y          | Chain type      |
| poolAddress | body         | string   | Y          | Pool address    |

> Body Request parameters

```json
{
  "chain": 2,
  "poolAddress": "string"
}
```

> Response example

```json
{
  "code": "0",
  "msg": "Success",
  "data": {
    "poolId": 5,
    "poolAddress": "7cH9xUhqdYpBuxsEEvJ13pTNWWH3asFrHGtewzXewg2F",
    "baseValue": {
      "address": "5JrMWQjPazxJ2YRBhi9oURzQg5jkVWJBEWALZZUtZkxk",
      "amount": "2.35042242531411543600",
      "priceUsd": "7.13306951939869722200",
      "value": "16.76572655971928",
      "symbol": "PEPE",
      "name": "pepe coin",
      "decimals": 18,
      "iconUrl": "https://api-wallet.endless.link/api/btc/cid-nft-gateway/QmVW7kktavxuGNqDr5NH98DQ6t7qVM54uSqZpRyxJhibmP"
    },
    "quoteValue": {
      "address": "ENDLESSsssssssssssssssssssssssssssssssssssss",
      "amount": "293.13768108",
      "priceUsd": "0.11861900",
      "value": "34.7716985920285200",
      "symbol": "EDS",
      "name": "Endless Coin",
      "decimals": 8,
      "iconUrl": "https://www.endless.link/eds-icon.svg"
    },
    "fee": "0.2401802700",
    "tvl": "51.537425151748",
    "apr": "1.29552349",
    "feeH24": "0.00085406",
    "priceNative": "124.717020193008266940",
    "hasVolume": true,
    "multiplier": null,
    "volumeH24": "0.71171400"
  }
}
```

### **Returns data structures**

| **Name**       | **Type** | **Option** | **Description**                 |
| -------------- | -------- | ---------- | ------------------------------- |
| » code         | string   | true       | API Code                        |
| » msg          | string   | true       | Message                         |
| » data         | object   | true       | Object                          |
| »» poolId      | integer  | true       | Pool Id                         |
| »» poolAddress | string   | true       | Pool Address                    |
| »» baseValue   | object   | true       | Base Token Value                |
| »»» address    | string   | true       | Token address                   |
| »»» amount     | string   | true       | Token amount                    |
| »»» priceUsd   | string   | true       | Token price in usd              |
| »»» value      | string   | true       | Token value(amount \* priceUsd) |
| »»» symbol     | string   | true       | Token symbol                    |
| »»» name       | string   | true       | Token name                      |
| »»» decimals   | integer  | true       | Token decimals                  |
| »»» iconUrl    | null     | true       | Token icon Url                  |
| »» quoteValue  | object   | true       | Quote Token Value               |
| »»» address    | string   | true       | Token address                   |
| »»» amount     | string   | true       | Token amount                    |
| »»» priceUsd   | string   | true       | Token price in usd              |
| »»» value      | string   | true       | Token value(amount \* priceUsd) |
| »»» symbol     | string   | true       | Token symbol                    |
| »»» name       | string   | true       | Token name                      |
| »»» decimals   | integer  | true       | Token decimals                  |
| »»» iconUrl    | null     | true       | Token icon Url                  |
| »» fee         | string   | true       | Fee Tier                        |
| »» tvl         | string   | true       | TVL                             |
| »» apr         | string   | true       | APR                             |
| »» feeH24      | string   | true       | 24H Fee                         |
| »» priceNative | string   | true       | price                           |
| »» hasVolume   | boolean  | true       | Has volume                      |
| »» volumeH24   | string   | true       | 24H VOL                         |

## **Price Line Chart**

POST /v1/chart/line/price

### **Request parameters**

| **Name**    | **Location** | **Type** | **Option** | **Description** |
| ----------- | ------------ | -------- | ---------- | --------------- |
| chain       | body         | integer  | Y          | Chain type      |
| poolAddress | body         | string   | N          | Pool address    |
| interval    | body         | string   | N          | 1H;1D;1W;1M     |

> Body Request parameters

```json
{
  "chain": 2,
  "poolAddress": "string",
  "interval": "string"
}
```

### **Returns data structures**

| **Name**    | **Type** | **Option** | **Description**       |
| ----------- | -------- | ---------- | --------------------- |
| » code      | string   | true       | API Code              |
| » msg       | string   | true       | Message               |
| » data      | [object] | true       | Object Array          |
| »» time     | string   | true       | Date Time             |
| »» price0   | string   | true       | Token0’s price        |
| »» price1   | string   | true       | Token1‘s price        |
| »» isFilled | boolean  | true       | Is filled by pre item |

> Response example

```json
{
  "code": "0",
  "msg": "Success",
  "data": [
    {
      "time": "2025-01-14T00:00:00",
      "price0": "0.99400000000000000000",
      "price1": "1.00603621730382293800",
      "isFilled": false
    },
    {
      "time": "2025-01-15T00:00:00",
      "price0": "0.96207802687649560440",
      "price1": "1.03964280723753081950",
      "isFilled": false
    }
  ]
}
```

## **POST Volume Line Chart**

POST /v1/chart/line/volume

### **Request parameters**

| **Name**    | **Location** | **Type** | **Option** | **Description** |
| ----------- | ------------ | -------- | ---------- | --------------- |
| chain       | body         | integer  | Y          | Chain type      |
| poolAddress | body         | string   | Y          | Pool address    |
| interval    | body         | string   | N          | 1H;1D;1W;1M     |

> Body request parameter

```json
{
  "chain": 0,
  "poolAddress": "string",
  "interval": "string"
}
```

### **Returns data structures**

| **Name**    | **Type** | **Option** | **Description**         |
| ----------- | -------- | ---------- | ----------------------- |
| » code      | string   | true       | API Code                |
| » msg       | string   | true       | Message                 |
| » data      | [object] | true       | Object Array            |
| »» time     | string   | false      | Date Time               |
| »» volume   | string   | false      | Volume                  |
| »» isFilled | boolean  | false      | Is filled by pre volume |

> Return example

```json
{
  "code": "0",
  "msg": "Success",
  "data": [
    {
      "time": "2025-01-14T00:00:00",
      "price0": "0.99400000000000000000",
      "price1": "1.00603621730382293800",
      "isFilled": false
    },
    {
      "time": "2025-01-15T00:00:00",
      "price0": "0.96207802687649560440",
      "price1": "1.03964280723753081950",
      "isFilled": false
    }
  ]
}
```
