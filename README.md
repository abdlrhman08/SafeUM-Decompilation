# SafeUM Decompiled

Every activity of the application connects to a websocket

The address of these websockets can be
"193.200.173.45", "185.65.206.12", "195.13.182.217", "195.13.182.213", "180.210.203.183", all on port 8080

The application connects to the first address on boot, fetches the other server addresses and sorts them and then decides which server to continue its requests on  

Every activity has its own path, Ex: host:port/Auth for the authentication and creates a single websocket connection for every activity. Every response is in gzip compressed format.

### The hosts or the ip addresses for an unknown reason are called "nodes" and in one point of the application when it connects all the activities it gets the IP address to connect to using the following snippet

```java
    SUMApplication.app().getNode()
```
the .app() method is used to return the instance of the current SUMApplication created in the apps runtime

# Balance Client (Server Load Balancer) [extends BaseClient]

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

## Authentication and registration

Before any authenticaton or new account registration the application performs a UniqueKeyRequest. The form of this payload is as follows:
```json
{
  "action": "Login",
  "subaction": "GetKeyUnique",
  "deviceuid": "[DEVICE UID]",
  "deviceuid": "[DEVICE UID]",
  "softwareversion": "[SOFWATRE VERSION]",
  "id": "[ANY RANDOM INTEGER]"
}
```
Any problems and mistakes in the payload form will result in the server closing the connection immediatly

Having two UID is not necessary for the request to work and is probably a mistake by the developers. After sending this payload the server replies with a public unique key which is used for all password hashing functions and some security stuff. The response is in gzip format and after decompression it looks as follows

```json
{
	"action": "Login",
	"subaction": "GetKeyUnique",
	"status": "Success",
	"key": {
		"x": "49058f7106843ae123cc1c1302705d9582bb0d1e84d29643f56d3968dd9de3a4",
		"y": "305942eda292c7f0c62a5157d2413d3c75d02884d7d6f7ed6820d9dff0e780c0",
		"type": "pub"
	},
	"sig": {
		"r": "8d2e061947b5fedc46bbd6ed266be75d57d02b1aaa95d1aded09eb58cc8f072b",
		"s": "e41acd9a2234c8e037d61545d6756a244cd9fadc6957b4a92eaa238fe306009f",
		"type": "sig"
	},
	"id": "3423"
}
```

### Login Request (Not confirmed yet)

#### Auth Invoker

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
   "login": "[USERNAME]",
   "session": "[AN OPTIONAL JSON OBJECT THAT CAN BE SKIPPED]",
   "deviceuid": "[PHONES UID]"  //I don't know why again tho  
}
```

Then the it adds some callbacks
- [ ] TODO

And then executes request
- [ ] todo

## 3rd Partied Used:

[Android-WebSockets](https://github.com/codebutler/android-websockets) by Eric Butler

Firebase ??

