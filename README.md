# apiUniversalsoftDocs
Documentation about UniversalSoft Api

## Preliminary Action
First of all you have to send us a valid user so we can create a Client ID and you need to send us as well a valid IP address from where your requests will arrive to our servers. Once we have this information we will proceed for the activation of our system for the communication from and to you. Example:

ClientID=`mybetstore`
> IP=https://mybetstore.com or http://159.15.35.45

## List Games
The gamelist command show you all games allowed for your client ID

```
GET http://[universalDomain]/gamelist
Query Params: 
page [number] (default=1)
perPage [number] (default=100)
p [String] (optional)
g [String] (optional) // for search game with part or full name
n [int] (optional) // 1 for list only new games
o [int] (optional) // 1 for list only popular games
t [String] (optional) // type of game
```
Response:
```
Array with the game objects.
```

## List Filters
The gamelist command show you all games allowed for your client ID

```
GET http://[universalDomain]/gamefilter
Headers:
phrase:(string),
```
Response:
```
Array with the objects [brands:[], categories[], types:[]].
```
This list have some important keys:
c = category (tv|slot|virtual|poker|etc)
t = type (Clasicos, Juego de Temporada, Juego Mesa, Raspa y Gana, Ruleta)
m = mode (mobile|web)
g = game name
b = brand name (univerlsalpoker|sportrace|etc.)
id = game id
p = internal category (is for api use, please doesnt filter by this)
## Create User
Is necessary to register your user at the first time he trying to play with us.
```
POST	https://[universalDomain]/api/createuser
Body Params: {
 username: string(20) ^[A-Za-z0-9]+$,
 firstname:string(20),
 lastname:string(20),
 country: text, (PE, BR, PT, etc)
 currency: text, (PEN, USD),
 balance:float,
 gender:M|F
}
```
Response:	
```
{ 
 status: number, 
 err: object,  
 val: { user: string, balance: string } 
}
```
*if user register successfully, response get a status true*
*if the user already exists the status parameter will be false with an error message.*
*others errors return paramter status false. for example wrong currency. it will be specified in the error message*

### Start Session
Before user launch a game, he need get a token session.

```
POST	https://[universalDomain]/api/auth
Body Params: {
 username: string,
 balance:float
}
```
Response: 
```
{
  status: number,
  err: object,
  val: {
    token: string
  }
}
```
** Some errors with httpstatus 500 could not have a json Format. Please take care for this kind of responses.
## Launch Game
To launch a game you need to redirect the url. 
```
GET	https://[universalDomain]/launch
Query Params:	{
 sessionid: alphanumeric,
 gameid: string,
 p: string(2),
 b: string,
 m: string(mb|wb)
}
```
Response:	
```
HTML for game starting.
```

### Insert Bet.
When you bet, our server inform your server via REST, so, you must create the insertBet endpoint.
```
POST	https://[yourserver_ip]/insertBet

Body Params	{
 trxid:string,
 movement:string(BET),
 amount:float,
 sessionid: alphanumeric,
 gameid:string,
 game_name:string,
 custom:string, (optional)
}
```
Response: 
```
{
status:1,
balance:float
}
```
Trxid: the transaction id of us.
Movement: type of movement (BET, WIN, LOSE, REFUND), which in this case will be just bet.
Amount: bet amount.
Sessionid: user session id.
Game: game id

### Bet Result.

When you win, our server inform your server via REST, so, you must create the betResult endpoint
```
POST	https://[yourserver_ip]/betResult

Body Params	{
 trxid:string,
 movement:string(WIN,REFUND),
 amount:float,
 sessionid: alphanumeric,
 gameid: string,
 game_name: string,
 referenceBet:string| null,
 custom: string, (optional)
}
```
if REFUND is about BET, add amount to user. but if REFUND is about WIN, remove amount from user.

Response	
```
{
 status:1,
 balance:float
}
```
Trxid: the transaction id of us.
Movement: type of movement (WIN, REFOUND)
Amount: bet amount.
Sessionid: user session id. 
transactionReference: if you win the bet, the value you will take will be the id of the transaction where you made the bet

### Get User Balance.

Some games will request for user Balance
```
POST	https://[yourserver_ip]/getUserBalance

Body Params	{
 sessionid:string
}
```

Response	
```
{
 status:1,
 balance:float
}
```

### Change Client IP (for dev environment).

This method allow you change constantly your client ip conection when you are on dev phase.
```
PUT	https://[universalDomain]/api/client/security/address

Body Params	{
    phrase: string,
    ip: string
}
```

Response	
```
{
 status:1
}
```

### Transactions Report

This method allow you to show transactions of all users.
```
GET	https://[universalDomain]/api/client/transactions

Headers['phrase'] = string

Query Params	{
    page: string (default=1),
    perPage: string (default=10),
    username: string (optional),
}
```

Response	
```
{
 data: array,
 pagination: { 
  page: number, 
  total: number, 
  perPage: number, 
  previous: number, 
  next: number
 },
 isTotal: boolean
}
```
### Important Information about Transactions
When a win transaction is done, some games have the next logic:
```
p: sl => the transaction send a win calculated (win - bet), and inform the win amount. it dont send a bet transaction.
```

```
p: eb => return a reference Bet. because first save a Bet transaction, then save a Win transaction. 
```

```
p:vg => return a reference Bet. Because first save a Bet transaction, then save a Win transaction. 
```

```
p:tv => return a referente Bet. Because first save a Bet transaction, then save a Win transaction.
```
```
p:ho => return a referente Bet. Because first save a Bet transaction, then save a Win transaction.
```

new games we will documenting soon.

### Extra Info
We have this games categories :
```
tv : TV Games
virtual: Virtual Sports
slot: Slot  Games
slot_live: Slot Live Games
poker: Poker Games
iq: Iq Games  
```

### Error Codes
when you catch an error, you must send Http Status: 500 and a json Message:
```
{
 status:0,
 code:ERROR_CODE,
 message:'',(Optional)
}
```

where error code:
```
INSUFFICIENT_FUNDS: When the bet > than user balance.
TRX_DUPLICATED: when transaction was register before.
USER_NOT_FOUND: when the user is not found.
BETREFERENCE_NOT_FOUND: when bet is not found.
SESSION_NOT_FOUND: when user session is not found.
INTERNAL_ERROR: unknown error.
```



*fin*
