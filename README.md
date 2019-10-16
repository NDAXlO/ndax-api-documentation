Table of Contents
=================

   * [Ndax API Documentation](#ndax-api-documentation)
      * [Websocket API](#websocket-api)
         * [Instruments](#instruments)
         * [Orderbook - Level 1:](#orderbook---level-1)
            * [Snapshot:](#snapshot)
               * [Request:](#request)
               * [Reply](#reply)
            * [Subscribe:](#subscribe)
               * [Request](#request-1)
               * [Reply](#reply-1)
         * [Orderbook - Level 2:](#orderbook---level-2)
            * [Snapshot:](#snapshot-1)
               * [Request:](#request-2)
               * [Reply](#reply-2)
         * [Authentication](#authentication)
            * [Request:](#request-3)
            * [Reply:](#reply-3)
         * [Executed Trades](#executed-trades)
               * [Request:](#request-4)
            * [Reply:](#reply-4)

# Ndax API Documentation 

## Websocket API
The following is documentation based off the websocket protocol. The base endpoint is: **wss://api.ndax.io/WSGateway/**. 

All messages require the following format and must be in json format:
```
{
    "m": 0,
    "i": 0,
    "n":"FUNCTION NAME",
    "o":"PAYLOAD"
}
```
```m``` denotes the message type (0: request)

```i``` denotes the sequence number, which should be none zero and increased with every message. 

```n``` denotes the function name, which is essentially the endpoint for your request.

```o``` contains the payload data in key-value format for your specific request - the payload must be in json format, meaning you must convert the payload as well as the entire request into json.

For example, in python:

```python
payload = {"OMSId":1,
		   "InstrumentId":1}
message = {"m": 0,
		   "i": 1,
           "n":"GetLevel1",
           "o":json.dumps(payload)}
message = json.dumps(message)
socket.send(message)
```

The above is simply for clarification that both the ```o``` part of the message and the entire message itself must be converted to json prior to sending via websocket

### Instruments
InstrumentId | Symbol
------ | ------
1 | BTCCAD
2 | BCHCAD
3 | ETHCAD
4 | XRPCAD
5 | LTCCAD
74 | BTCUSD
75 | EOSCAD
76 | XLMCAD
77 | DOGECAD
78 | ADACAD


### Orderbook - Level 1:
#### Snapshot:
Provides a current Level 1 snapshot (best bid, best offer) of a specific instrument trading on an Order Management System. The Level 1 snapshot does not allow the user to specify the level of market depth information on either side of the bid and ask.

##### Request:

```python
payload = {"OMSId":1,
		   "InstrumentId":1}
message = {"m": 0,
		   "i": 1,
           "n":"GetLevel1",
           "o":json.dumps(payload)}
message = json.dumps(message)
socket.send(message)
```
##### Reply

```python
{"m": 0,
"i": 1,
"n":"GetLevel1",
"o":{"exchangeId":	'integer. The ID of the exchange whose snapshot was taken.',
     "productPairCode": 'integer. The ID of the instrument (the product pair) whose prices, bids, asks, and volumes are being reported - same as instrumentId',
  	 "bestBid": 'real. The current best bid for the instrument (product pair).',
     "bestOffer": 'real. The current best offer for the instrument (product pair).',
  	 "volume": 'real. The unit volume of the instrument traded, either during a true session (with a market open and close),'
	 	        'or in 24-hour markets, it is the period from UTC Midnight to UTC Midnight, regardless of the nominal location of the market.',
  	 "lastTradedPx": 'real. The last-traded price for the instrument.',
  	 "lastTradedVolume": 'real. The last quantity that was traded.',
  	 "lastTradeTime": 'real. The last time that the instrument was traded.',
  	 "timeStamp": 'real. The date and time of this snapshot.',
  	 "bidQty": 'real. The quantity currently being bid.',
  	 "askQty": 'real. The quantity currently being asked.',
  	 "bidOrderCt": 'integer. The count of bid orders.',
  	 "askOrderCt": 'integer. The count of ask orders.',
  	 "sessionOpen": 'real. Opening price as of UTC Midnight',
  	 "sessionHigh": 'real. Highest price during the trading day, either during a true session (openings and closings) or UTC Midnight to UTC Midnight.',
  	 "sessionLow": 'real. Lowest price during the trading day',
  	 "sessionClose": 'real. The closing price as of UTC Midnight',
  	 "currentDayVolume": 'real. The unit volume of the instrument as of UTC Midnight',
  	 "currentDayNumTrades": 'integer. The number of trades during the current day',
  	 "currentDayPxChange": 'real. Change in price',
  	 "rolling24HrVolume": 'real. Unit volume of the instrument over the past 24 hours',
  	 "rolling24NumTrades": 'real. Number of trades during the past 24 hours',
  	 "rolling24HrPxChange": 'real. Price change during the past 24 hours',
  	 "rolling24HrPxChangePercent": 'real. Percent change in price during the past 24 hours'}
}
```
#### Subscribe:
##### Request

```python
payload = {"OMSId":1,
		   "InstrumentId":1}
message = {"m": 0,
		   "i": 1,
           "n":"SubscribeLevel1",
           "o":json.dumps(payload)}
message = json.dumps(message)
socket.send(message)
```

##### Reply

```python
{"m": 0,
"i": 1,
"n":"GetLevel1",
"o":{"OMSId": 1,
     "InstrumentId": 1,
     "BestBid": 6423.57,
     "BestOffer": 6436.53,
     "LastTradedPx": 6423.57,
     "LastTradedQty": 0.96183964,
     "LastTradeTime": 1534862990343,
     "SessionOpen": 6249.64,
     "SessionHigh": 11111,
     "SessionLow": 4433,
     "SessionClose": 6249.64,
     "Volume": 0.96183964,
     "CurrentDayVolume": 3516.31668185,
     "CurrentDayNumTrades": 8529,
     "CurrentDayPxChange": 173.93,
     "Rolling24HrVolume": 4319.63870783,
     "Rolling24NumTrades": 10585,
     "Rolling24HrPxChange": -0.4165607307408487,
     "TimeStamp": "1534862990358"
}

```


### Orderbook - Level 2:
#### Snapshot:
Provides a current Level 2 snapshot of a specific instrument trading on an Order Management System to a user-determined market depth. The Level 2 snapshot allows the user to specify the level of market ```Depth'``` information on either side of the bid and ask.
##### Request:

```python
payload = {"OMSId":1,
		   "InstrumentId":1,
		   "Depth":100}
message = {"m": 0,
		   "i": 1,
           "n":"GetL2Snapshot",
           "o":json.dumps(payload)}
message = json.dumps(message)
socket.send(message)
```
##### Reply
```python
{"m": 0,
"i": 1,
"n":"GetL2Snapshot",
"o":[[0,   # MDUpdateId -- integer. Market Data Update ID. This sequential ID identifies the order in which the update was created.        
      1,   # AccountId -- integer. The ID of the account trading the instrument.
      123, # ActionDateTime in Posix format X 1000 -- long integer. identifies the time and date the snapshot was taken in POSIX format X 1000 (milliseconds since 1 January 1970).
      0,   # ActionType -- 0 (New), 1 (Update), 2(Delete)
      0.0, # LastTradePrice -- real. The price at which the instrument was last traded
      0,   # OrderId -- integer. The ID of the order.
      0.0, # Price -- real. Bid or Ask price for the Quantity (see Quantity below).
      0,   # ProductPairCode -- integer. ProductPairCode is the same value and used for the same purpose as InstrumentID
      0.0, # Quantity -- real. Quantity available at a given Bid or Ask price
      0,   # Side (0 = bid, 1 = ask)
    ],
]
}
```
### Authentication

#### Request:
```python
payload = {"APIKey": "28c68ac3fcfafc3d4e8d653fe57e5baf",
           "Signature": "29c15c42e4fabcc9e229421e148e647903927c503ab4578ada55bb13a63a9636",
           "UserId": "96",
           "Nonce": "2247733562"}
message = {"m": 0,
		   "i": 1,
           "n":"AuthenticateUser",
           "o":json.dumps(payload)}
message = json.dumps(message)
socket.send(message)           
```
#### Reply:
```python
{
  "user":
  { "userId": 0,
    "userName": "",
    "email": "",
    "emailVerified": false,
    "accountId": 0,
    "omsId": 0,
    "use2FA": false
  },
  "authenticated": True,
  "locked": false,
  "requires2FA": false,
  "twoFAType": "",
  "twoFAToken": ""
}
```

### Executed Trades
In order to subscribe to executed trades you must first authenticate using the key, secret pair supplied via the Ndax website following the above instructions
##### Request:

```python
payload = {"OMSId":1,
		   "InstrumentId":1,
		   "IncludeLastCount":100 # integer. Specifies the number of previous trades to retrieve in the immediate snapshot. Default is 100.
           }
message = {"m": 0,
		   "i": 1,
           "n":"SubscribeTrades",
           "o":json.dumps(payload)}
message = json.dumps(message)
socket.send(message)
```
#### Reply:
Numerical keys reduce package transmission load. See commented text for an explanation of each key.
```python
[
     {
         0: 1713390,     # (TradeId) 	integer. The ID of this trade.
         1: 1,           # (ProductPairCode) 	integer. ProductPairCode is the same number and used for IntrumentId
         2: 0.25643269,  # (Quantity) 	real. The quantity of the instrument traded.
         3: 6419.77,     # (Price) 	real. The price at which the instrument traded.
         4: 203100209,   # (Order1) 	integer. The ID of the first order that resulted in the trade, either Buy or Sell.
         5: 203101083,   # (Order2) 	integer. The ID of the second order that resulted in the trade, either Buy or Sell.
         6: 1534863262,  # (Tradetime) 	long integer. UTC trade time in Total Milliseconds. POSIX format.
         7: 2,           # (Direction) 	integer. Effect of the trade on the instrument’s market price. (0=NoChange, 1=UpTick, 2=Downtick)
         8: 1,           # (TakerSide) 	integer. Which side of the trade took liquidity? (0=Buy, 1=Sell)
         9: 0,           # (BlockTrade) 	Boolean. Was this a privately negotiated trade that was reported to the OMS? (0=False, 1=True)
         10: 0           # (order1ClientId or order2ClientId) 	integer. The client-supplied order ID for the trade.
     }
 ]

```
