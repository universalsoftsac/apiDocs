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
Array with the objects [brands:[], categories[]].
```
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
*if the user already exists the status parameter will be false with an error message.*

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
 game:string
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
Movement: type of movement (BET, WIN, LOSE, REFOUND), which in this case will be just bet.
Amount: bet amount.
Sessionid: user session id.
Game: game id

### Bet Result.

When you win, our server inform your server via REST, so, you must create the betResult endpoint
```
POST	https://[yourserver_ip]/betResult

Body Params	{
 trxid:string,
 movement:string(WIN,REFOUND),
 amount:float,
 sessionid: alphanumeric,
 betReference:string| null
}
```

Response	
```
{
 status:1,
 balance:float
}
```
Trxid: the transaction id of us.
Movement: type of movement (WIN, LOSE, REFOUND)
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



*fin*
