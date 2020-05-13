# JSON-IRC
JSON-IRC describes how the IRC(X/3/4/5/6/7/8/v3) protocols can be delivered over JSON.

This work is a draft, and SHOULD NOT be used on any production issues.

## Rationale

* Transport

IRC is traditionally transported over raw TCP sockets. Since web applications have become common, there has been an introduction of various proxies being used, such as:
  * WebSocket
  * SocketIO
  * Primus
  * EngineIO
  * etc.

The techniques used to gather data vary wildly, some of these are:
  * iframes / refreshing
  * long-polling
  * native object such as XMLHttpRequest
  * flash, java or other non-native objects
  * Websockets

WebSockets is now widely supported on browsers, but there has been no change to the existing protocol for handling IRC connections.

* Conflicting numerics

There are conflicts on named numerics, JSON-IRC may attempt to solve these issues in the future by using named commands.

* Irrelevant information

  * Prefixes: While the prefix is ignored from client to server, currently the server needlessly sends it's prefix at every opportunity.
  * Targets: Where raw numerics are sent from a server to client, the target of the numeric is specified and should always be the nickname of the client.
  
* Various escaping methods

The following use various different types of encoding/escaping:

  * IRC Protocol (Latin1)
  * IRCv3 tags (UTF-8)
  * IRCX Auth (IRCX escaping)
  * IRCv3 tags (values only) / WEBIRC options (IRCv3 tags escaping)
  * IRCv3 AUTH (base64 encoding)

## Considerations

The names of the following have been shortened for use in JSON-IRC:

* IRCv3 `Tags` is `tags`
* RFC1459 `prefix` is `src`
* RFC1459 `command` is `cmd`
* RFC1459 `parameters` is `args`

The names in JSON-IRC were chosen to be kept succinct while maintaining (human) readability.

### Protocol

JSON-IRC commands should uses a single newline as a delimiter as per the [ND-JSON](http://ndjson.org/) format.
The protocol should be compatible with IRCv3, including tags, prefix, command and parameters.

If the decoded JSON object is an array, it should be assumed that the array contains multiple JSON-IRC messages.
If the decoded JSON object is an object, it should be assumed that it is a JSON-IRC message.
A message is required to contain a command (cmd) object at a minimum, or the message should be ignored.

There is no limit to the length of a JSON object, however each message should be limited to a particular length.

#### Tags (tags)

Tags in IRC are formatted as: `tag1;tag2=hello\sworld`
As JSON already handles escaping in a consistent manner, the following JSON-IRC tags would be considered equivalent:

```json
{"tag1":null,"tag2":"hello world"}
```

#### Prefix (src)

The server SHOULD omit a JSON-IRC prefix where it refers only to the current server the client is connected to. (new)
A client SHOULD omit a JSON-IRC prefix, and a server MUST ignore a prefix from a client (as per IRC)

#### Command (cmd)

Named commands require no changes at this time.
Raw numerics should be sent as an integer over JSON-IRC, and the first (target) parameter MUST NOT be sent to clients with the exception of the raw numeric 1 ("001" in IRC) as most clients use the target in 001 to determine the initial nickname on registration.

#### Parameters (args)

As JSON is not a protocol delimited by spaces, it seems appropriate to allow spaces to be used in JSON-IRC. To support backwards compatibility, conversion must be attempted on many clients to determine suitability.

Note: hen converting paramters to an IRC compatible message (for backwards compatibility), the following table should be used:
```
\b            " "
\0            NUL
\\            "\"          
\r            CR          
\n            LF
\t            TAB
```
A "\" shall not trigger the requirement for encoding on it's own. Likewise, a " " in a trailing (final) parameter should not trigger the requirement for encoding. This is because the standard IRC protocols allows these to be used.

An encoded parameter should be prefixed with a %, with the exception of nicknames. A nickname should only allow a " " or "\", and should always be escaped using the above table. (IRC Compatible)

A channel name such as `#Lobby` would stay as `#Lobby` (IRC Compatible)
A channel name with a space such as `#The Lobby` would become `%#The\bLobby` (IRCX Compatible)

```javascript
TODO: Consideration needs to be given to where binary messages need to not be modified from LATIN1 due to JSON's UTF-8 encoding. 
```

### Issues

The MSN based raws 821 and 822 are incompatible with JSON-IRC. The raw numerics may continue to be used

## Delivery methods

It is RECOMMENDED that JSON-IRC SHOULD be delivered using the Websocket Protocol [[RFC6455](https://tools.ietf.org/html/rfc6455)].

The IRC protocol has traditionally been delivered over raw TCP sockets, and a server MAY choose to deliver JSON-IRC over raw TCP sockets.

A server MUST only deliver JSON-IRC using a security mechanism such as Transport Layer Security (TLS), independant of delivery method chosen.

## Ports

No standardised ports have been defined at this time.

## Compression

JSON-IRC compatible clients and servers SHOULD NOT use compression techniques at the JSON-IRC level. Compression should be defined by the the method of transport, such as:

#### Websockets:

* The Websocket Per-Message Deflate [[RFC7692](https://tools.ietf.org/html/rfc7692)] is a registered websocket extension (since 2018) which provides compatible compression, but is out of the scope of this protocol.

## Examples

A simple example showing the difference between the IRC protocols and the JSON-IRC protocol.

#### Example (IRC, 1506 bytes)
```irc
:irc.efnet.nl 001 JD :Welcome to the EFNet Internet Relay Chat Network JD
:irc.efnet.nl 002 JD :Your host is irc.efnet.nl[185.100.59.59/6667], running version ircd-ratbox-3.0.10
:irc.efnet.nl 003 JD :This server was created Sun Oct 2 2016 at 04:55:27 CEST
:irc.efnet.nl 004 JD irc.efnet.nl ircd-ratbox-3.0.10 oiwszcrkfydnxbauglZCD biklmnopstveIrS bkloveI
:irc.efnet.nl 005 JD CHANTYPES=&# EXCEPTS INVEX CHANMODES=eIb,k,l,imnpstS CHANLIMIT=&#:80 PREFIX=(ov)@+ MAXLIST=beI:100 MODES=4 NETWORK=EFNet KNOCK STATUSMSG=@+ CALLERID=g :are supported by this server
:irc.efnet.nl 005 JD SAFELIST ELIST=U CASEMAPPING=rfc1459 CHARSET=ascii NICKLEN=9 CHANNELLEN=50 TOPICLEN=160 ETRACE CPRIVMSG CNOTICE DEAF=D MONITOR=60 :are supported by this server
:irc.efnet.nl 005 JD FNC ACCEPT=20 MAP TARGMAX=NAMES:1,LIST:1,KICK:1,WHOIS:1,PRIVMSG:10,NOTICE:10,ACCEPT:,MONITOR: :are supported by this server
:irc.efnet.nl 251 JD :There are 5551 users and 10260 invisible on 22 servers
:irc.efnet.nl 252 JD 112 :IRC Operators online
:irc.efnet.nl 254 JD 8898 :channels formed
:irc.efnet.nl 255 JD :I have 861 clients and 1 servers
:irc.efnet.nl 265 JD 861 1981 :Current local users 861, max 1981
:irc.efnet.nl 266 JD 15811 19586 :Current global users 15811, max 19586
:irc.efnet.nl 250 JD :Highest connection count: 1982 (1981 clients) (521309 connections received)
:irc.efnet.nl 375 JD :- irc.efnet.nl Message of the Day - 
:irc.efnet.nl 372 JD :- <Removed for example>
:irc.efnet.nl 376 JD :End of /MOTD command.
```

#### Example (JSON-IRC, 1561 bytes)
```json
[{"cmd":1,"args":["JD","Welcome to the EFNet Internet Relay Chat Network JD"]},{"cmd":2,"args":["Your host is irc.efnet.nl[185.100.59.59/6667], running version ircd-ratbox-3.0.10"]},{"cmd":3,"args":["This server was created Sun Oct 2 2016 at 04:55:27 CEST"]},{"cmd":4,"args":["irc.efnet.nl","ircd-ratbox-3.0.10","oiwszcrkfydnxbauglZCD","biklmnopstveIrS","bkloveI"]},{"cmd":5,"args":["CHANTYPES=&#","EXCEPTS","INVEX","CHANMODES=eIb,k,l,imnpstS","CHANLIMIT=&#:80","PREFIX=(ov)@+","MAXLIST=beI:100 MODES=4","NETWORK=EFNet","KNOCK","STATUSMSG=@+","CALLERID=g","are supported by this server"]},{"cmd":5,"args":["SAFELIST","ELIST=U","CASEMAPPING=rfc1459","CHARSET=ascii","NICKLEN=9","CHANNELLEN=50","TOPICLEN=160","ETRACE","CPRIVMSG","CNOTICE","DEAF=D","MONITOR=60","are supported by this server"]},{"cmd":5,"args":["FNC","ACCEPT=20","MAP","TARGMAX=NAMES:1,LIST:1,KICK:1,WHOIS:1,PRIVMSG:10,NOTICE:10,ACCEPT:,MONITOR:","are supported by this server"]},{"cmd":251,"args":["There are 5551 users and 10260 invisible on 22 servers"]},{"cmd":252,"args":[112,"IRC Operators online"]},{"cmd":254,"args":[8898,"channels formed"]},{"cmd":255,"args":["I have 861 clients and 1 servers"]},{"cmd":265,"args":[861,1981,"Current local users 861, max 1981"]},{"cmd":266,"args":[15811,19586,"Current global users 15811, max 19586"]},{"cmd":250,"args":["Highest connection count: 1982 (1981 clients) (521309 connections received)"]},{"cmd":375,"args":["- irc.efnet.nl Message of the Day -"]},{"cmd":372,"args":["- <Removed for example>"]},{"cmd":376,"args":["End of /MOTD command."]}]
```

#### Expanded JSON-IRC for readability
```json
[
  {
    "cmd": 1,
    "args": [
      "Welcome to the EFNet Internet Relay Chat Network JD"
    ]
  },
  {
    "cmd": 2,
    "args": [
      "Your host is irc.efnet.nl[185.100.59.59/6667], running version ircd-ratbox-3.0.10"
    ]
  },
  {
    "cmd": 3,
    "args": [
      "This server was created Sun Oct 2 2016 at 04:55:27 CEST"
    ]
  },
  {
    "cmd": 4,
    "args": [
      "irc.efnet.nl",
      "ircd-ratbox-3.0.10",
      "oiwszcrkfydnxbauglZCD",
      "biklmnopstveIrS",
      "bkloveI"
    ]
  },
  {
    "cmd": 5,
    "args": [
      "CHANTYPES=&#",
      "EXCEPTS",
      "INVEX",
      "CHANMODES=eIb,k,l,imnpstS",
      "CHANLIMIT=&#:80",
      "PREFIX=(ov)@+",
      "MAXLIST=beI:100 MODES=4",
      "NETWORK=EFNet",
      "KNOCK",
      "STATUSMSG=@+",
      "CALLERID=g",
      "are supported by this server"
    ]
  },
  {
    "cmd": 5,
    "args": [
      "SAFELIST",
      "ELIST=U",
      "CASEMAPPING=rfc1459",
      "CHARSET=ascii",
      "NICKLEN=9",
      "CHANNELLEN=50",
      "TOPICLEN=160",
      "ETRACE",
      "CPRIVMSG",
      "CNOTICE",
      "DEAF=D",
      "MONITOR=60",
      "are supported by this server"
    ]
  },
  {
    "cmd": 5,
    "args": [
      "FNC",
      "ACCEPT=20",
      "MAP",
      "TARGMAX=NAMES:1,LIST:1,KICK:1,WHOIS:1,PRIVMSG:10,NOTICE:10,ACCEPT:,MONITOR:",
      "are supported by this server"
    ]
  },
  {
    "cmd": 251,
    "args": [
      "There are 5551 users and 10260 invisible on 22 servers"
    ]
  },
  {
    "cmd": 252,
    "args": [
      112,
      "IRC Operators online"
    ]
  },
  {
    "cmd": 254,
    "args": [
      8898,
      "channels formed"
    ]
  },
  {
    "cmd": 255,
    "args": [
      "I have 861 clients and 1 servers"
    ]
  },
  {
    "cmd": 265,
    "args": [
      861,
      1981,
      "Current local users 861, max 1981"
    ]
  },
  {
    "cmd": 266,
    "args": [
      15811,
      19586,
      "Current global users 15811, max 19586"
    ]
  },
  {
    "cmd": 250,
    "args": [
      "Highest connection count: 1982 (1981 clients) (521309 connections received)"
    ]
  },
  {
    "cmd": 375,
    "args": [
      "- irc.efnet.nl Message of the Day -"
    ]
  },
  {
    "cmd": 372,
    "args": [
      "- <Removed for example>"
    ]
  },
  {
    "cmd": 376,
    "args": [
      "End of /MOTD command."
    ]
  }
]
```
