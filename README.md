# SafeUM Decompiled

Every activity of the application connects to a websocket

The address of these websockets can be
"193.200.173.45", "185.65.206.12", "195.13.182.217", "195.13.182.213", "180.210.203.183", all on port 8080

Using wireshark the application connects to the first address on boot

However all these hosts refuse a manual connection to them for an unknown reason

Every activity has its own path like host:port/Auth for the authentication and creates a single websocket connection for every activity


## Login Request (Not confirmed yet)

### Auth Invoker

The App initates an ALTLoginRequest (I don't know yet what is the meaning of ALT here)
The ALTLoginRequest creates a json payload with the following format first in its constructor

I Don't know if the following payload is the same with every request but I have to find out

```json
{
    "action": "login",
    "subaction": "alt",
    "deviceuid": "[YOUR PHONES UID]",
    "softwareversion": "[CURRENT VERSION]"
}
```

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

Has the list of all the hosts
```java 
static List<String> ADDRESS_LISTS = Arrays.asList(new String[] { "193.200.173.45", "185.65.206.12", "195.13.182.217", "195.13.182.213", "180.210.203.183" });
```

connects to the port 8080 with the path "Bal"

The Balance Client iterates over the whole ADDRESS_LIST and connects to the last one, which refuses my manual connection


## 3rd Partied Used:

Firebase ??

Jcipher also used