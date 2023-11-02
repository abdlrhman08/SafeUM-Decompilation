# SafeUM Decompiled

Every activity of the application connects to a websocket

The address of these websockets can be
"193.200.173.45", "185.65.206.12", "195.13.182.217", "195.13.182.213", "180.210.203.183", all on port 8080

Using wireshark the application connects to the first address on boot

I managed to establish a connection using Postman but I have to figure a payload to send

Every activity has its own path like host:port/Auth for the authentication and creates a single websocket connection for every activity. Every response is in gzip compressed format.

### The hosts or the ip addresses for an unknown reason are called "nodes" and in one point of the application when it connects all the activities it gets the IP address to connect to using the following snippet

```java
    SUMApplication.app().getNode()
```

the .app() method is used to return the instance of the current SUMApplication created in the apps runtime

## Login Request (Not confirmed yet)

### Auth Invoker

The App initates an ALTLoginRequest (I don't know yet what is the meaning of ALT here)
The ALTLoginRequest creates a json payload with the following format first in its constructor

```json
{
    "action": "login",
    "subaction": "alt",
    "deviceuid": "[YOUR PHONES UID]",
    "softwareversion": "[CURRENT VERSION]"
}
```

```json
{
    "action": "[]",
    "subaction": "[]"
}
```
The two key-value pairs are found in every request and the tell the server what I am current doing or requesting.

Then the following key and value pairs are added manually

```json
{
   "login": "[LOGIN STRING THAT I THINK IS PROBABLY A CERTAIN TOCKEN]",
   "session": "[AN OPTIONAL JSON OBJECT THAT CAN BE SKIPPED]",
   "deviceuid": "[PHONES UID]"  //I don't know why again tho  
}
```

Then the it adds some callbacks
- [ ] TODO

And then executes request
- [ ] todo

## Balance Client (extends BaseClient)

The BalanceClient class is probably a Server Balancer which tells the client which server to connect to and login to

It has the list of all the hosts
```java 
static List<String> ADDRESS_LISTS = Arrays.asList(new String[] { "193.200.173.45", "185.65.206.12", "195.13.182.217", "195.13.182.213", "180.210.203.183" });
```

connects to the port 8080 with the path "Bal"

The Balance Client has an iterator for the ADDRESS_LIST and and it creates a websocket object giving it the name "BalanceClient", all the callbacks for this connection are handled by the BalanceClientListener Class, there are also extra headers added, which is a cookie given the value session=abcd and its useless.

```java
this.webSocketConnection = new WebSocketClient("BalanceClient", new BalanceClientListener(), this.extraHeaders, this);
```

After connection the server replies with a gzip compressed binary response that has the server addresses attached to a key-pair that is valued by priority to the client. The response decompressed looks as follows.
```json
{
	"action": "Balancer",
	"subaction": "Query",
	"status": "Success",
	"nodes": {
		"0": "n01-h02-sg.sum.im",
		"1731": "n04-h02-lv.sum.im",
		"1732": "n02-h02-lv.sum.im",
		"2133": "n01-h01-ua.sum.im",
		"01731": "195.13.182.213",
		"02133": "193.200.173.45",
		"00": "51.79.208.190",
		"01732": "195.13.182.217"
	},
	"id": "0"
}
```
If we try to connect using a VPN the Keys change to different number indicating that connecting to another server is probably wiser. That explains why using a vpn results in different country numbers.

The hexdump of the uncompresses response:
![](https://github.com/abdlrhman08/SafeUM-Decompilation/blob/main/res/hex.png?raw=true)

## 3rd Partied Used:

Firebase ??

Jcipher also used
