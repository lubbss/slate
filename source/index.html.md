---
title: Fairlay API

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: protocol

toc_footers:
  - <a href='http://fairlay.com'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: true
---

# Private API Introduction

> This is a sample GETBALANCE Request:

```shell
uiskPc9GqWAdUt1hJ+4VeuxyCNtbs0iY3kSbGQZ7HvwHkCe5H2n9UcX5v1Gl7Mb99XnhBiVzkne5bdkgmLdnlzzEBPLYnt8zwWHcqxXvIYiDpLQhsVfdkvyYwH1zgrUwAZu08VmY0ax34zxjXJdK68TVs9Y1m7akXkw5/NvIwXk=|635972931146604657|100012|22|<ENDOFDATA>
```

> The response is usually JSON-encoded:

```shell
rfV2ylPLbDbBKEy0yi71xZS/fZbyozd7+smpit1cR8ZeB0qo+stRDttTkCxqF0towkS0bf3lkg4amSbvka6K//QX/F1BHdFNqpkFXQHB9jh2eyR2WgXblKeGRbgw+mma+1P/kKNBtf3qhOUiqOgzONwlTEusiLzqHil6HLTQTKY=|635972681796848020|66|{"PrivReservedFunds":334.049,"MaxFunds":0.0,"PrivUsedFunds":207.12,"AvailableFunds":126.946}
````

> If there is an error, the server returns an error message starting with XError: for general errors or YError: if there was an error in a subtask of a bulk change order request.

The Fairlay Private API allows you to POST requests (creating/changing markets and orders, transfering funds, etc.), costs 0.1mBTC per 100.000 requests, see below on how to create your developer API account. 

This is the low level documentation, if you need a working sample or are more comfortable reading code rather than specifications, head over to [https://github.com/Fairlay/FairlayDotNetClient] (https://github.com/Fairlay/FairlayDotNetClient)

All requests must be send via TCP in the following format

 
`signature|nonce|userid|requestID|requestData<ENDOFDATA>`

Every request must end with 

`<ENDOFDATA>`

You must sign `nonce|userid|requestID|requestData` with your RSA key and provide your signature with all requests.

**Remember to use (YOUR API ID*1000)+Request ID on requests. Example: If you create your API accont on Fairlay, site will set an ID to it, usually first API ID is 1. So a request to Get Orderbook with this account will be 1001.**

# Private API Calls

## Get Public Key

```shell
signature|nonce|id|GETPUBLICKEY
```

Response: returns the server's public key in XML format.

## Create Account

```shell
<Exponent>AQAB</Exponent>
</RSAKeyValue>|999999
```

Returns: User info object

Note: Use [https://superdry.apphb.com/tools/online-rsa-key-converter](https://superdry.apphb.com/tools/online-rsa-key-converter) to convert PEM RSA keys to XML format.

## Get Me

```shell
signature|nonce|userid|21signa
```

Returns: User info object containing last connections, withdraw adresses, market makers, funds, API Accounts, screen name and ID.

### Get My Balance

```shell
signature|nonce|userid|22
```
Returns: JSON object containing account balance information:

**PrivReservedFunds:** Total account funds in mBTC. 
**PrivUsedFunds:** Amount of balance placed on opened markets in mBTC.
**AvailableFunds:** Amount of balance available to use in mBTC. 
**SettleUsed:** Amount retained due to settlement in mBTC. 
**CreatorUsed:** Amount retained by market creation in mBTC. 
**RemainingRequests:** Requests remaining before new charge.

## Set API Account to Readonly

```shell
signature|nonce|userid|5049|
````
This will permanently set your API Account #5 to Read Only. This cannot be undone.

You can also set your API Account #0 to read only. But be careful with it:

```shell
signature|nonce|userid|49|
```
## Get Server Time

```shell
signature|nonce|userid|1002
```
Response: returns the server's time in ticks.

`636451764120705300`

Note: the server time can also be interfered from the nonce in every response.

## Get Orderbook

```shell
signature|nonce|userid|1|marketID
```
Response: An array containing one object with orderbooks for each runner in market.

`[{ "Bids": [ [3.954, 3.75] ], "Asks": [ [7.203, 2.06156] ], "S": 1 }, { "Bids": [ [5.627,2.63] ], "Asks": [], "S": 1}]`

This is a return from a market with 2 runners.

## Get Orderbooks

```shell
signature|nonce|userid|4|[marketID1, marketID2]
signature|nonce|userid|4|[121046999588, 121046400020]
```
Response: An array of objects containing orderbooks for each runner in market.

`[{ "Bids": [ [3.954, 3.75] ], "Asks": [ [7.203, 2.06156] ], "S": 1 }, { "Bids": [ [5.627,2.63] ], "Asks": [], "S": 1},{ "Bids": [ [3.954, 3.75] ], "Asks": [ [7.203, 2.06156] ], "S": 1 }, { "Bids": [ [5.627,2.63] ], "Asks": [], "S": 1}]`

This is a return from 2 markets with 2 runners.

## Get Markets

```shell
signature|nonce|userid|7|{
"Cat":0,
"RunnerAND":["Arsenal","Chelsea"],
"TitleAND":null,
"TitleNOT":["Corners","Throwin"],
"Comp":"Premier Leag",
"TypeOR":null,
"PeriodOR":[1],
"SettleOR":null,
"ToSettle":false,
"OnlyMyCreatedMarkets":false,
"Descr":null,
"ChangedAfter":"2016-01-01T22:01:01",
"SoftChangedAfter":"0001-01-01T00:00:00",
"OnlyActive":false,
"MinPop":0.0,
"MaxMargin":103.0,
"NoZombie":false,
"FromClosT":"2016-05-01T00:00:00",
"ToClosT":"0001-01-01T00:00:00",
"FromID":0,
"ToID":100,
"SortPopular":false}
```

Returns all markets that apply to the given filter.

**Cat:** is the Category, 0 queries all Categories. 

**RunnerAND:** All strings provided must be contained in the names of the runners of the market. 

**TitleNOT:** None of the strings may appear in the title of the market 

**TypeOr:** Only the Market Types given will be returned. If set to null, all market types will be returned. See A2) for Market Types 

**PeriodOr:** Similiar to TypeOr See A2) for Market Periods 

**SettleOr:** See A2) for Settlement Types 

**ToSettle:** If set to true, it will only return unsettled markets where the resolution time has passed. 

**NoZombie:** No empty markets (without any open order) 

**Descr:** The given string must appear in the market description. 

**ChangedAfter:** Only returns markets, where the meta data was changed after the given date. Usually the Closing and Settlement Dates of a market is the only data that changes. 
**SoftChangedAfter:** Returns all markets, where either the the meta data or the orderbook has changed since the given date.

Returns: a list of market objects

## Get Market

```shell
signature|nonce|userid|1006|marketid
```

Returns: market object

## Get Markets Orderbook

```shell
signature|nonce|userid|67|{
"Cat":0,
"RunnerAND":["Arsenal","Chelsea"],
"TitleAND":null,
"TitleNOT":["Corners","Throwin"],
"Comp":"Premier Leag",
"TypeOR":null,
"PeriodOR":[1],
"SettleOR":null,
"ToSettle":false,
"OnlyMyCreatedMarkets":false,
"Descr":null,
"ChangedAfter":"2016-01-01T22:01:01",
"SoftChangedAfter":"0001-01-01T00:00:00",
"OnlyActive":false,
"MinPop":0.0,
"MaxMargin":103.0,
"NoZombie":false,
"FromClosT":"2016-05-01T00:00:00",
"ToClosT":"0001-01-01T00:00:00",
"FromID":0,
"ToID":100,
"SortPopular":false}signa
```

Returns: A long-string Dictionary with the marketID - orderbook key-value pair.

## Create Market

```shell
signature|nonce|userid|11|{
"Comp":"Politics",
"Descr":"This market resolves to Yes, if the Senate confirms Merrick Garland’s Supreme Court nomination before President Obama leaves the office.",
"CatID":15,
"ClosD":"2016-11-01T00:00:00",
"SettlD":"2017-01-20T00:00:00",
"Ru":[{"Name":"Yes","InvDelay":0,"VisDelay":6000},{"Name":"No","InvDelay":0,"VisDelay":6000}],
"_Type":2,
"_Period":1,
"SettlT":0,
"Comm":0.02,
"PrivCreator":784741,
"CreatorName":"USERNAME"}
```

**Comp:** Competition **CatID:** Category (see A2)) **ClosD:** Closing Date **SettlD:** Resolution Date **Ru:** InvDelay must always be 0, VisDelay must always be set to 6000 _Type: See A2) _Period: See A2) **SettlT:** For settlement types see A2) **Comm:** 0.02 - must always be 0.02. **PrivCreator:** must match your userid and will remain privat **CreatorName:** must match your Username and is public

Returns: Market ID (no json)

## Create Order

> Note: (use requestID 61 for market making)

```shell
signature|nonce|userid|62|marketid|runnerid|bidorask|price|amount|type|matchesubuser|pendingperiod
```

> Example:

```shell
signature|nonce|userid|1062|71601335718|0|1|1.56|100.1|2|testorder|6000
```

**Market ID:** on which market the order will be placed Runner **ID:** on which runner the order will be placed: 0 for the 1st runner, 1 for the 2nd runner of the market and so on. **BidorAsk:** 0 places a bid order, against the runner. 1 places a bet on the runner to win. **price:** the decimal odds at which the order is placed **amount:** the amount in mBTC **matchedsubuser:** custom string **type:** see unmatched order types in **A2)**
**pendingperiod:**s should be set to 6000ms

Returns: unmatched order object https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/UnmatchedOrder.cs

## Cancel Order

```shell
signature|nonce|userid|75|marketid|runnerid|orderid
```

Returns: a list of all corresponding matched orders objects for the cancelled unmatched order.

## Cancel Market Orders

```shell
signature|nonce|userid|10
```
Returns: how many unmatched orders were cancelled:

`2 Orders were cancelled``

## Cancel All Orders

```shel
signature|nonce|userid|16
```

Returns: how many unmatched orders were cancelled:

`5 Orders were cancelled`

## Change Order

```shell
signature|nonce|userid|17|marketid|runnerid|orderid|price|amount
```

Is a combination of "cancel order" and "create order"

Returns: unmatched order object https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/UnmatchedOrder.cs

## BULK CHANGE ORDER MAKER

Changes, cancels and creates many orders at once.

```shell
signature|nonce|userid|109|Array of Request ChangeOrder object
````

[https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/REQChangeOrder.cs](https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/REQChangeOrder.cs)

> Example

```shell
[{"Mid":73911957931,"Rid":13,"Oid":635980470611670494,"Type":1,"Am":1665.0,"Pri":5.587,"Sub":null,"Boa":0,"Mct":0},{"Mid":73395769791,"Rid":0,"Oid":635980470611720522,"Am":9355.0,"Pri":0.0,"Sub":null,"Boa":1,"Mct":0},{"Mid":73569399509,"Rid":0,"Oid":6359804706645670494,"Am":193.0,"Pri":49.800,"Boa":0,"Mct":0},{"Mid":73570791053,"Rid":2,"Oid":-1,"Am":1514.0,"Pri":6.954,"Sub":"myLine","Boa":1,"Mct":6000}]
```

Returns: an array of REQChangeOrder objects. The Res property of each object contains the status of each bet. Res is either a serialized UnmatchedOrder order object (https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/UnmatchedOrder.cs) or contains an error message like (Res = ) "YError: Market closed" for example.

## Get New Orders

```shell
signature|nonce|userid|24|TimeinTicks
```

**TimeinTicks:** Time given in ticks, all orders after the given tick will be returned

Returns: all unmatched orders and matched orders that were created, cancelled or changed after the given time.

https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/ReturnUOrder.cs 
https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/ReturnMOrder.cs

## Get Matched Orders

```shell
signature|nonce|userid|27|TimeinTicks
```

**TimeinTicks:** Time given in ticks, all orders after the given tick will be returned

Returns: all matched orders that were created, cancelled or changed after the given time. https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/ReturnMOrder.cs

## Transfer Founds/Withdraw

```shell
signature|nonce|userid|81|transferobject
```

> Example for transfer object:

```shell
{"FromU":777888,"ToU":777889,"Descr":"test transfer","TType":1,"Amount":2.0}
```

**FromU:** must match your userid ToU: must be an existing user TType: custom field, can have any int value

Returns: transferobject if successful

## Get User Transaction

```shel
signature|nonce|userid|82|TimeinTicks
```

Get all user transactions since given time in ticks.

Returns: An array of transactions. [https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/MUserTransfer.cs](https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/MUserTransfer.cs) Every transaction has currency IDs in it. [https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/CurrencyIds.cs](https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/CurrencyIds.cs)

## Set Market Maker 

```shell
signature|nonce|userid|73|lmsrmarketmakerobject
```

This sets up an fully automated LMSR market maker. Google the term to get familiar with the usage.

> example for lmsrmarketmakerobject:

```shell
{"UserID":777888,
"Runner":5,
"Enabled":true,
"InitShareLimit":350.0,
"B":1300.0,
"CancelAll":"2016-06-05T13:34:56", 
"ShareStop":1400.0,
"InitProb":[0.3,0.1,0.2,0.2,0.2],
"DiminishBack":[0.01,0.01,0.01,0.0,0.02],
"DiminishLay":[0.0,0.0,0.0,0.01,0.01],
"coolOffSeconds":36000.0,"coolOffFactor":4.0}
```

[https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/LMSR.cs](https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/LMSR.cs)

> Default values:

```shell
{"UserID":[userid],
"Runner":5,
"Enabled":true,
"InitShareLimit":300.0,
"B":1000.0,
"CancelAll":"2016-06-05T13:34:56", 
"ShareStop":9999.0,
"InitProb":[0.2,0.2,0.2,0.2,0.2],
"DiminishBack":[0.00,0.00,0.00,0.0,0.00],
"DiminishLay":[0.01,0.01,0.01,0.01,0.01],
"coolOffSeconds":1.0,"coolOffFactor":2.0}
```
**UserID:** must match your userid **MarketID:** must be provided **Runner:** # of Runners / must match the # of runners of the market **Enabled:** must be set to true **InitShareLimit** (must be > 1): Shares that are offered in one order. Stake + Winnings from one order are 350mBTC **B** (must be > 10): ~ is about the same as the maxium possible loss of the market maker **CancelAll** (must be set to a future date): Date where the market maker stops. Set to year 2100 if the mm should run forever **ShareStop** (must be > 1): amount of exposure in shares before the market maker stops. Should be set higher than B in regular cases. **InitProb:** the initial probability estimation for all runners **DiminishBack** (must be non-negative): In general the LMSR market maker runs on 0% margin, i.e. it doesn't make any profit. If more margin should be added, you can worsen the odds for each bid orders for every runner. 0.01 worsens bid odds from 80% to 81% (or 1.25 to 1.2345) for example. **DiminishLay:** same for all ask orders.

*Cool off adds temporary additional margin to markets with increased activity and should be applied to markets that can have exogenous shocks or where real probabilities can deviate from the initial probability distribution. *

**coolOffFactor** (must be >=1): if set to 4.0 the odds will worsen 4.0 times more than expected from the usual lmsr market maker.
**coolOffSeconds** (must be >=1): The time after which the coolOff period will be over. If set to 36000 the additional market margin will reduce step by step over an period of 10 hours.

If no cool off is required, set coolOffSeconds to 1.

**Disable a MM by setting Enabled = false and other variables to a valid value.**

## Get Marker Maker

Gets the current LMSR MM from the user for a given market.

```shell
signature|nonce|userid|70|marketid
```

> example for lmsrmarketmakerobject:

```shell{"UserID":777888,"MarketID":61659266392,"Runner":5,"Enabled":true,"InitShareLimit":350.0,"B":1300.0,"CancelAll":"2016-06-05T13:34:56", "ShareStop":1400.0,"InitProb":[0.3,0.1,0.2,0.2,0.2],"DiminishBack":[0.01,0.01,0.01,0.0,0.02],"DiminishLay":[0.0,0.0,0.0,0.01,0.01],"coolOffSeconds":36000.0,"coolOffFactor":4.0}```

## Change Market Closing Time


Changes Closing and Settlement Date

```shell
signature|nonce|userid|84|ChangeTimeObject
```

> Example:

```shell
{"MID":76650963889,
"ClosD":"2016-06-11T01:00:00Z",
"SetlD":"2016-06-11T03:00:00Z"}
```

https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Private/Datatypes/ChangeTimeReq.cs

Returns: MarketTime changed.

## Get Settlements

Gets all bankroll adjustments after a given time.

```shell
signature|nonce|userid|85|timeinticks
```

Returns: A JSON encoded Statement Object:

**long ID:** is not unique globally; is the time in milliseconds from Jan 1th 2016 **Descr:** optional description, contains the market ID in case of a settlement. **T:** 0-99 stands for a transfer (same like ttype in transfer object) includes deposits & cashouts, 100 admin, 200 market settlement w/o commission, 201 market settlement with commission, 250 unsettlement, 300 commission bonus) **Am:** amount credited **Bank:** total bankroll after the adjustment.

## Settle Market

Settles a given market. Note that the total betting volume of the market will be deducted from the available balance for 2 to 3 days. Unless the user has special rights, a user can only settle markets he created.

```shell
signature|nonce|userid|86|SettleRequestObject
```

> Example:

```shell
signature|nonce|userid|86|{
"Mid":77588905280,
"Runner":0,
"Win":1,
"Half":false,
"Dec":0.0,
"ORed":0.0}
```

**Mid:** is the market ID **Runner:** determines the Runner which won (0 means that the 1st Runner won, 1 means that the 2nd Runner won and so on). If a market shall be voided the Runner must be set to -1 **Win:** Must be set to 1 **Half:** should be set to "false". Only needed for +- 0.25 and +-0.75 soccer spread and over/under markets. If a market is half won or half lost, set Half to "true"; **Dec:** If the market is not binary, but has a decimal outcome, this needs to be set to the result. [Not supported yet] **ORed:** Odds reduction [only for Horse racing - not needed in general]

Returns: "Market settled" or some kind of "XError: ..."

# Public API Introduction

This is a free service to scrape all markets on Fairlay. For all POST requests (creating/changing markets and orders) use the paid [Private API](#public-api-introduction).

* The data on the free servers is updated every 5 seconds
* For Professional Users there exist private servers with an update frequency of half a * second. Please contact fairlay+api@protonmail.com to get access.
* You have to accept GZIP, DEFLATE in your HEADER
* Use the SoftChangedAfter parameter for incremental calls. Retrieve the server time and only query markets that have changed after your last request
* 12 incremental calls are allowed per minute (the SoftChangedAfter parameter must be set to not more than 30 seconds in the past)
* 1 call without a proper SoftChangedAfter parameter is allowed per IP per minute
* When using the markets request make sure that your URI is not too long
* All times are UTC (like DateTime.UtcNow, to account for any time difference with your developer machine grab [Server Time](#server-time), in the C# API all calls other than time will do this automatically for you)
* Strings are not case sensitive

For more examples how this API should be used together with our paid API, please take a look at our sample clients in C# and [Python](https://github.com/Fairlay/FairlayPythonClient/blob/master/client.py#L291).

Please feel free to comment and make suggestions.

# Public API Calls

## Free Servers

`http://31.172.83.181:8080`

*used by default for all Public API calls*

`http://31.172.83.53:8080`

*(is an alternate server with the same functionality, also used for all Private API calls)*

## View markets on Fairlay

You can use the market ids to access any market on fairlay directly

`https://fairlay.com/market/MARKETID`

Example

`https://fairlay.com/market/will-donald-trump-be-president-at-year-end-2018/`

## Methods

For examples on how to use each of the API calls presented here, see the [C# Unit Tests](https://github.com/Fairlay/FairlayDotNetClient/blob/master/src/Tests/Public/PublicApiTests.cs).

In all of the examples below you can call /free/(method) calls with /free0/ up to /free9/ to increase the given limits above, a simple way is to either rotate from 1 to 9 or just randomize the free call from 1 to 9.

### Server Time

Returns the server time in ticks.

`http://31.172.83.181:8080/free/time`

Note: You can do the same in the [Private API via getservertime](#get-server-time)

### Markets

Returns JSON encoded list of markets that apply to the given filter.

`http://31.172.83.181:8080/free/markets/JSON-ENCODED-MARKETFILTER`

> MARKETFILTER object looks like this:

```shell
{
        "Cat":0,
        "RunnerAND":["Arsenal","Chelsea"],
        "TitleAND":null,
        "TitleNOT":["Corners","Throwin"],
        "Comp":"Premier League",
        "TypeOR":[0],
        "PeriodOR":[1],
        "SettleOR":null,
        "Descr":null,
        "ChangedAfter":"2016-01-01T22:01:01",
        "SoftChangedAfter":"0001-01-01T00:00:00",
        "OnlyActive":false,
        "NoZombie":false,
        "FromClosT":"2016-05-01T00:00:00",
        "ToClosT":"0001-01-01T00:00:00",
        "FromID":0,
        "ToID":10000
}
```

Cat: Category, see A2) for more information. 0 queries all Categories. **TitleAND**: List of strings which all must appear in the title of the market. **RunnerAND**: List of strings which all must be contained in at least one name of one runner of the market. *TitleNOT: List of strings which none may appear in the title of the market. Comp: Competition name. null to get all competitions. **TypeOr8*: Market Types, see A2) for more information. Only the Market Types given will be returned. If set to null, all market types will be returned. **PeriodOr**: Market Period, see A2) for more information. **SettleOr**: Settlement Type, see A2) for more information. **NoZombie**: If True, no empty markets will be returned (without any open order). **Descr**: The given string must appear in the market description. ••ChangedAfter••: Return markets where the meta data was changed after the given date. Usually the Closing and Settlement Dates of a market is the only data that changes. **SoftChangedAfter**: Return all markets, where either the the meta data or the orderbook has changed since the given date. **FromClosT**: Return markets where the closing time is greater than the given one. **FromID** and **ToID**: Use for paging requests. **ToID** has a default value of 300 if not set.

> Examples:

> Returns the first 100 non-empty soccer markets, where one of the runners is Portugal, the Title does not contain the words "Corners" or "Throwin" and the period of the match is full-time.

```shell
http://31.172.83.181:8080/free/markets/{"Cat":1,"NoZombie":true,"RunnerAND":["Portugal"], "TitleNOT":["Corners","Throwin"], "PeriodOR":[1],"FromID":0,"ToID":100}
```

> Returns all active tennis matches of the type Over/Under or Outright where the odds or market data have changed after June 1st 12:01:30pm.

```shell
http://31.172.83.181:8080/free/markets/{"Cat":2,"TypeOr":[1,2],"SoftChangedAfter":"2016-06-01T12:01:30","OnlyActive":true,"ToID":10000}
```

> Returns all active non-empty markets.

```shell
http://31.172.83.181:8080/free/markets/{"OnlyActive":true,"NoZombie":true,"ToID":100000}
```

### Competitions

Returns all competitions in the selected category.

`http://31.172.83.181:8080/free/comps/CATEGORYID`

Example for all soccer competitions:

`http://31.172.83.181:8080/free/comps/1`

## DATA Fields

```shell
enumenum  MarketPeriodMarketPeriod
        UNDEFINED,
        FT,
        FIRST_SET,
        SECOND_SET,
        THIRD_SET,
        FOURTH_SET,
        FIFTH_SET,
        FIRST_HALF,
        SECOND_HALF,
        FIRST_QUARTER,
        SECOND_QUARTER,
        THIRD_QUARTER,
        FOURTH_QUARTER,
        FIRST_PERIOD,
        SECOND_PERIOD,
        THIRD_PERIOD,

         UNDEFI enum StatusType
        ACTIVE,
        INPLAY,
        SUSPENDED,
        CLOSED,
        SETTLED,
        CANCELLED

enum MarketType
        M_ODDS,
        OVER_UNDER,
        OUTRIGHT,
        GAMESPREAD,
        SETSPREAD,
        CORRECT_SCORE,
        FUTURE,
        BASICPREDICTION,
        RESERVED2,
        RESERVED3,
        RESERVED4,
        RESERVED5,
        RESERVED6

enum SettleType
        BINARY,
        DECIMAL

Category:
        SOCCER = 1;
        TENNIS = 2;
        GOLF = 3;
        CRICKET = 4;
        RUGBYUNION = 5;
        BOXING = 6;
        HORSERACING = 7;
        MOTORSPORT = 8;
        SPECIAL = 10;
        RUGBYLEAGUE = 11;
        BASKETBALL = 12;
        AMERICANFOOTBALL = 13;
        BASEBALL = 14;
        POLITICS = 15;
        FINANCIAL = 16;
        GREYHOUND = 17;
        VOLLEYBALL = 18;
        HANDBALL = 19;
        DARTS = 20;
        BANDY = 21;
        WINTERSPORTS = 22;
        BOWLS = 24;
        POOL = 25;
        SNOOKER = 26;
        TABLETENNIS = 27;
        CHESS = 28;
        HOCKEY = 30;
        FUN = 31;
        ESPORTS = 32;
        RESERVED4 = 34;
        MIXEDMARTIALARTS = 35;
        RESERVED6 = 36;
        RESERVED = 37;
        CYCLING = 38;
        RESERVED9 = 39;
```

# API Examples & Source Code

## A0) Access Information

You need to register your IP to access the server. You will receive the IP and port you can connect to. This is a testserver for general use: 31.172.83.53:18012

> This is the public key from the server. All responses must be signed.

```shell
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC52cTT4XaVIUsmzfDJBP/ZbneO
6qHWFb01oTBYx95+RXwUdQlOAlAg0Gu+Nr8iLqLVbam0GE2OKfrcrSy0mYUCt2Lv
hNMvQqhOUGlnfHSvhJBkZf5mivI7k0VrhQHs1ti8onFkeeOcUmI22d/Tys6aB20N
u6QedpWbubTrtX53KQIDAQAB
-----END PUBLIC KEY-----
```

## A1) Orders

```shell
 class ReturnMOrder
    {
        UserOrder _UserOrder;
        MatchedOrder _MatchedOrder;
        long _UserUMOrderID;   //the corresponding unmatched order id
    }

    class UserOrder
    {
        int BidOrAsk;
        long MarketID;
        int RunnerID;
        long OrderID;
        string MatchedSubUser;
    }

    class MatchedOrder
    {
        int R;  //internal status
        long ID;  
        MOState State;  //See A2)
        decimal Price;
        decimal Amount;
        int MakerCancelTime;  //Pending Period
    }
```

## A2) DATA Field

```shell
 enum MarketPeriod
        {
            UNDEFINED,
            FT,
            FIRST_SET,
            SECOND_SET,
            THIRD_SET,
            FOURTH_SET,
            FIFTH_SET,
            FIRST_HALF,
            SECOND_HALF,
            FIRST_QUARTER,
            SECOND_QUARTER,
            THIRD_QUARTER,
            FOURTH_QUARTER,
            FIRST_PERIOD,
            SECOND_PERIOD,
            THIRD_PERIOD   
        }

        enum StatusType
        {
            ACTIVE,
            INPLAY,
            SUSPENDED,
            CLOSED,
            SETTLED,
            CANCELLED
        }

        enum MarketType
        {
            M_ODDS,
            OVER_UNDER,
            OUTRIGHT,
            GAMESPREAD,
            SETSPREAD,
            CORRECT_SCORE,
            FUTURE,
            BASICPREDICTION,
            RESERVED2,
            RESERVED3,
            RESERVED4,
            RESERVED5,
            RESERVED6
        }

        enum SettleType
        {
            BINARY,
            DECIMAL
        }
```

> for matched orders:

```shell
        enum MOState
        {
            MATCHED,
            RUNNERWON,
            RUNNERHALFWON,
            RUNNERLOST,
            RUNNERHALFLOST,
            MAKERVOIDED,
            VOIDED,
            PENDING,
            DECIMALRESULT
        }
```

> unmatched orders

```shell
        enum Type
        {
            MAKERTAKER,   
            MAKER,  //will not match with an existing order on the orderbook
            TAKER  //will only match with an existing order on the orderbook, after placement the order is immediately cancelled
        }
```

## Categories

```shell
 const int SOCCER = 1;
        const int TENNIS = 2;
        const int GOLF = 3;
        const int CRICKET = 4;
        const int RUGBYUNION = 5;
        const int BOXING = 6;
        const int HORSERACING = 7;
        const int MOTORSPORT = 8;
        const int SPECIAL = 10;
        const int RUGBYLEAGUE = 11;
        const int BASKETBALL = 12;
        const int AMERICANFOOTBALL = 13;
        const int BASEBALL = 14;
        const int HOCKEY = 30;
        const int POLITICS = 15;
        const int FINANCIAL = 16;
        const int GREYHOUND = 17;
        const int VOLLEYBALL = 18;
        const int HANDBALL = 19;
        const int DARTS = 20;
        const int BANDY = 21;
        const int WINTERSPORTS = 22;
        const int BOWLS =24;
        const int POOL = 25;
        const int SNOOKER = 26;
        const int TABLETENNIS = 27;
        const int CHESS = 28;
        const int FUN = 31;
        const int ESPORTS = 32;
        const int INPLAY = 33;
        const int RESERVED4 = 34;
        const int MIXEDMARTIALARTS = 35;
        const int RESERVED6 = 36;
        const int RESERVED = 37;
        const int CYCLING = 38;
        const int RESERVED9 = 39;
        const int BITCOIN = 40;
```

## A3) Sample Python code

```shell
# -*- coding: utf-8 -*-
# Standard library imports
import socket
import json
import gzip
from StringIO import StringIO
from base64 import b64encode, b64decode
from os.path import join
import time
from decimal import Decimal
from logging import getLogger

# Third party imports
from Crypto.PublicKey import RSA
from Crypto.Signature import PKCS1_v1_5
from Crypto.Hash import SHA512

logger = getLogger(__name__)

QUERY_TYPE_MAPPING = {
    "GETPUBLICKEY": "GETPUBLICKEY",
    "GETORDERBOOK": 1,   # market id
    "GETORDERBOOKS": 4,  # Serialized list of long market ids    returns Serialized  Dictionary<long,string>
    "GETSERVERTIME": 2,
    "GETCOMPETITIONS": 3,  # sport category
    "GETMARKET": 6,  # market id
    "GETMARKETS": 7,  # serialized market filter
    "CREATEMARKET": 11,  # serialized market
    "CREATEORDER": 12,  # see example
    "CREATEORDERWITHCANCELTIME": 13,  # See example
    "CREATELAYORDERWITHLIABILITY": 51,  # place lay bet
    "CREATELAYORDERWITHLIABILITYANDCANCELTIME": 51,
    "CANCELORDER": 15,     # MarketID|RunnerID|Unmatched-OrderID
    "CANCELORDERWITHMATCHES": 75,     # MarketID|RunnerID|Unmatched-OrderID
    "CANCELMATCHEDORDER": 9,  # MarketID|RunnerID|Matched-OrderID
    "CONFIRMMATCHEDORDER": 8,  # MarketID|RunnerID|Matched-OrderID
    "CANCELORDERSONMARKET": 10,  # market id
    "CANCELALLORDERS": 16,
    "CHANGEORDER": 17,  # long MarketID|int  RunnerID|long OrderID| decimal Price| decimal amount
    "CHANGEORDERCANCELTIME": 18,  # long MarketID|int  RunnerID|long OrderID| DateTime time
    "REDUCEORDERSTAKE": 19,
    "REGISTER": 20,  # string public key  |  long FundServerID
    "GETME": 21,   # -
    "GETMYBALANCE": 22,
    "GETNEW": 24,  # long time    returns  new UMO, new MO
    "GETNEWSILENT": 30,  # long time    returns  new UMO, new MO
    "GETUNMATCHEDORDERS": 25,  # time     or   time|fromID|toID   for large requests
    "GETMATCHEDORDERS": 27,  # time     or   time|fromID|toID   for large requests
    "SETABSENCECANCELPOLICY": 43,  # ms
    "SETFORCENONCE": 44,  # true or false
    "SETFORCESIGNATURE": 45,  # true or false
    "SETSCREENNAME": 46,  # true or false
    "GETMARKETSORDERBOOK": 67  # serialized market filter
}

CATEGORIES = {
    "SOCCER": 1,
    "TENNIS": 2,
    "GOLF": 3,
    "CRICKET": 4,
    "RUGBYUNION": 5,
    "BOXING": 6,
    "HORSERACING": 7,
    "MOTORSPORT": 8,
    "SPECIAL": 10,
    "RUGBYLEAGUE": 11,
    "BASKETBALL": 12,
    "AMERICANFOOTBALL": 13,
    "BASEBALL": 14,
    "POLITICS": 15,
    # "FINANCIAL": 16,
    # "GREYHOUND": 17,
    # "VOLLEYBALL": 18,
    # "HANDBALL": 19,
    # "DARTS": 20,
    # "BANDY": 21,
    # "WINTERSPORTS": 22,
    # "BOWLS": 24,
    # "POOL": 25,
    # "SNOOKER": 26,
    # "TABLETENNIS": 27,
    # "CHESS": 28,
    "HOCKEY": 30,
    "FUN": 31,
    "ESPORTS": 32,
    "MixedMartialArts": 35,
    "reserved8": 38,  # cycling
}

ORDER_TYPES = {
    "MAKERTAKER": 0,
    "MAKER": 1,
    "TAKER": 2
}

CLIENT_ID = settings.CLIENT_ID
SPORT_BETS_SERVER_IP = "31.172.83.53"
SPORT_BETS_SERVER_PORT = 18012

def create_json_query(comp=None, min_pop=None, no_zombie=None, only_active=None, cat=None, from_id=None, to_id=None,
                      runner_and=None, title=None, type_or=None, period_or=None, to_settle=None,
                      only_my_created_markets=None, changed_after=None, from_close_t=None, to_close_t=None):
    json_dict = {}
    if comp:
        json_dict["Comp"] = comp
    if min_pop:
        json_dict["MinPop"] = min_pop
    if no_zombie:
        json_dict["NoZombie"] = no_zombie
    if only_active:
        json_dict["OnlyActive"] = only_active
    if cat:
        json_dict["Cat"] = cat
    if from_id:
        json_dict["FromID"] = from_id
    if to_id:
        json_dict["ToID"] = to_id
    if runner_and:
        json_dict["RunnerAND"] = runner_and
    if title:
        json_dict["Title"] = title
    if type_or:
        json_dict["TypeOR"] = type_or
    if period_or:
        json_dict["PeriodOR"] = period_or
    if to_settle:
        json_dict["ToSettle"] = to_settle
    if only_my_created_markets:
        json_dict["OnlyMyCreatedMarkets"] = only_my_created_markets
    if changed_after:
        json_dict["ChangedAfter"] = changed_after
    if from_close_t:
        json_dict["FromClosT"] = from_close_t
    if to_close_t:
        json_dict["ToClosT"] = to_close_t
    return json.dumps(json_dict)

def get_message_signature(message):
    private_key_dir = join(settings.SPORT_BETS_KEY_PATH, "private_key.txt")
    private_key = open(private_key_dir, "r").read()
    rsa_key = RSA.importKey(private_key)
    signer = PKCS1_v1_5.new(rsa_key)
    digest = SHA512.new()
    digest.update(message)
    sign = signer.sign(digest)
    return b64encode(sign)

def verify_message(message):
    if message.find('|') == -1:
        return True
    signed_message = message[:message.find('|')]
    original_message = message[message.find('|')+1:]
    public_key_dir = join(settings.SPORT_BETS_KEY_PATH, "public_key.txt")
    public_key = open(public_key_dir, "r").read()
    rsa_key = RSA.importKey(public_key)
    signer = PKCS1_v1_5.new(rsa_key)
    digest = SHA512.new()
    digest.update(original_message)
    if signer.verify(digest, b64decode(signed_message + "=" * ((4 - len(signed_message) % 4) % 4))):
        return True
    return False

def send_request(message, signed=False, tries=0):
    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect((SPORT_BETS_SERVER_IP, SPORT_BETS_SERVER_PORT))
        message_signature = ''
        if signed:
            message_signature = get_message_signature(message)
        message_to_send = '{}|{}<ENDOFDATA>'.format(message_signature, message)
        s.send(message_to_send)
        data = ""
        while True:
            new_data = s.recv(4096)
            if not new_data:
                break
            data += new_data
        s.close()
        result = gzip.GzipFile(fileobj=StringIO(data)).read()
        if not verify_message(result):
            raise ValueError
        return result
    except (socket.timeout, socket.error) as e:        
        if tries < 3:
            return send_request(message, signed, tries + 1)
        else:
                 pass

def get_public_key():
    request_no = 0
    query_type = QUERY_TYPE_MAPPING["GETPUBLICKEY"]
    message = "{}|{}|{}".format(request_no, CLIENT_ID, query_type)
    result = send_request(message, signed=True)
    return result

def get_balance():
    request_no = 0
    query_type = QUERY_TYPE_MAPPING["GETMYBALANCE"]
    message = "{}|{}|{}".format(request_no, CLIENT_ID, query_type)
    result = send_request(message, signed=True)
    balance = result.split('|')[-1]
    try:
        return json.loads(balance)
    except ValueError:
        logger.info(u'Not able to parse response: {}'.format(balance))

def fetch_single_event_odds(event_id):
    request_no = 0
    query_type = QUERY_TYPE_MAPPING["GETORDERBOOK"]
    message = "{}|{}|{}|{}".format(request_no, CLIENT_ID, query_type, event_id)
    result = send_request(message, signed=True)
    order_books_json = result.split('|')[-1]
    if not ('Bids' in order_books_json or 'Asks' in order_books_json):
        return []
    order_books_json = order_books_json.strip('~') if order_books_json else None
    odds = []
    return

def fetch_sport_events():
    request_no = 0
    query_type = QUERY_TYPE_MAPPING["GETMARKETS"]
    events_list = []
    for category_name, category_id in CATEGORIES.iteritems():
        from_id = 0
        events = []
        while True:
            json_query = create_json_query(no_zombie=True,
                                           only_active=True,
                                           cat=category_id,
                                           from_id=from_id,
                                           to_id=from_id+300,
                                           min_pop=MIN_POP_DICT.get(category_name, None))
            message = "{}|{}|{}|{}".format(request_no, CLIENT_ID, query_type, json_query)
            result = send_request(message)
            try:
                new_events = json.loads(result.split("|")[-1])
            except ValueError:
                break
            events += new_events
            if len(new_events) < 300:
                break
            from_id += 300
    return events_list

def update_matches(latest_matched_bet_id, unresolved_events, sport_event_id=None):
    request_no = 0
    query_type = QUERY_TYPE_MAPPING["GETMATCHEDORDERS"]
    timestamp = 1420070400L
    from_id = 0
    new_matched_bets = []
    new_resolved_events = []
    voided_events = set()
    all_new_matches_checked = False
    while True:
        if sport_event_id:
            message = "{}|{}|{}|{}|{}".format(request_no, CLIENT_ID, query_type, timestamp, sport_event_id)
        else:
            message = "{}|{}|{}|{}|{}|{}".format(request_no, CLIENT_ID, query_type, timestamp, from_id, from_id+300)
        result = send_request(message, signed=True)
        matches = json.loads(result.split("|")[-1])
       
def can_sport_bet_be_placed(sport_event_id, sport_bet_outcome_id, outcome_type, odds, amount, maker=False):
    request_no = int(time.time())
    query_type = QUERY_TYPE_MAPPING["CREATEORDER" if outcome_type == 'back' else "CREATELAYORDERWITHLIABILITY"]
    back_or_lay = 1 if outcome_type == "back" else 0
    user_name = "fairlay"
    order_type = ORDER_TYPES["MAKERTAKER"] if not maker else ORDER_TYPES['MAKER']
    message = "{}|{}|{}|{}|{}|{}|{}|{}|{}|{}".format(request_no, CLIENT_ID, query_type,
                                                     sport_event_id, sport_bet_outcome_id, back_or_lay, odds,
                                                     amount * 1000, order_type, user_name)
    try:
        response = send_request(message, signed=True)
    except RequestFailed:
        return False
    logger.debug(u'--- can_sport_bet_be_placed ---')
    logger.debug(u'message -> {}'.format(message))
    logger.debug(u'response -> {}'.format(response))
    result = response.split("|")[-1]

def can_cancel_order(sport_event_id, sport_bet_outcome_id, sport_bet_order_id):
    request_no = 0
    query_type = QUERY_TYPE_MAPPING["CANCELORDERWITHMATCHES"]
    message = "{}|{}|{}|{}|{}|{}".format(request_no, CLIENT_ID, query_type,
                                         sport_event_id, sport_bet_outcome_id, sport_bet_order_id)
    response = send_request(message, signed=True)
    try:
        pass
    except ValueError:
        reason = response.split('|')[-1]
        if reason.startswith(('Order does not exist', 'XError: Market Closed', 'XError: Market does not exist')):
            logger.info(u'Reason: {}'.format(reason))
            can_be_cancelled = True
        else:
            can_be_cancelled = False

def update_odds():
    request_no = 0
    query_type = QUERY_TYPE_MAPPING["GETMARKETSORDERBOOK"]
    from_id = 0
    orders = []
    while True:
        json_filter = create_json_query(from_id=from_id, to_id=from_id+300, only_active=True)
        message = "{}|{}|{}|{}".format(request_no, CLIENT_ID, query_type, json_filter)
        result = send_request(message, signed=True)
       
        if len(new_orders.keys()) < 300:
            break
        from_id += 300
```