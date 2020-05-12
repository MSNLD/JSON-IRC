# JSON-IRC
JSON-IRC describes how the IRC(X/3/4/5/6/7/8/v3) protocols can be delivered over JSON

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

#### Example (JSON-IRC, 1539 bytes)
```json
[{"cmd":1,"prm":["Welcome to the EFNet Internet Relay Chat Network JD"]},{"cmd":2,"prm":["Your host is irc.efnet.nl[185.100.59.59/6667], running version ircd-ratbox-3.0.10"]},{"cmd":3,"prm":["This server was created Sun Oct 2 2016 at 04:55:27 CEST"]},{"cmd":4,"prm":["irc.efnet.nl","ircd-ratbox-3.0.10","oiwszcrkfydnxbauglZCD","biklmnopstveIrS","bkloveI"]},{"cmd":5,"prm":["CHANTYPES=&#","EXCEPTS","INVEX","CHANMODES=eIb,k,l,imnpstS","CHANLIMIT=&#:80","PREFIX=(ov)@+","MAXLIST=beI:100 MODES=4","NETWORK=EFNet","KNOCK","STATUSMSG=@+","CALLERID=g","are supported by this server"]},{"cmd":5,"prm":["SAFELIST","ELIST=U","CASEMAPPING=rfc1459","CHARSET=ascii","NICKLEN=9","CHANNELLEN=50","TOPICLEN=160","ETRACE","CPRIVMSG","CNOTICE","DEAF=D","MONITOR=60","are supported by this server"]},{"cmd":5,"prm":["FNC","ACCEPT=20","MAP","TARGMAX=NAMES:1,LIST:1,KICK:1,WHOIS:1,PRIVMSG:10,NOTICE:10,ACCEPT:,MONITOR:","are supported by this server"]},{"cmd":251,"prm":["There are 5551 users and 10260 invisible on 22 servers"]},{"cmd":252,"prm":[112,"IRC Operators online"]},{"cmd":254,"prm":[8898,"channels formed"]},{"cmd":255,"prm":["I have 861 clients and 1 servers"]},{"cmd":265,"prm":[861,1981,"Current local users 861, max 1981"]},{"cmd":266,"prm":[15811,19586,"Current global users 15811, max 19586"]},{"cmd":250,"prm":["Highest connection count: 1982 (1981 clients) (521309 connections received)"]},{"cmd":375,"prm":["- irc.efnet.nl Message of the Day -"]},{"cmd":372,"prm":["- <Removed for example>"]},{"cmd":376,"prm":["End of /MOTD command."]}]
```

#### Expanded JSON-IRC for readability
```json
[
  {
    "cmd": 1,
    "prm": [
      "Welcome to the EFNet Internet Relay Chat Network JD"
    ]
  },
  {
    "cmd": 2,
    "prm": [
      "Your host is irc.efnet.nl[185.100.59.59/6667], running version ircd-ratbox-3.0.10"
    ]
  },
  {
    "cmd": 3,
    "prm": [
      "This server was created Sun Oct 2 2016 at 04:55:27 CEST"
    ]
  },
  {
    "cmd": 4,
    "prm": [
      "irc.efnet.nl",
      "ircd-ratbox-3.0.10",
      "oiwszcrkfydnxbauglZCD",
      "biklmnopstveIrS",
      "bkloveI"
    ]
  },
  {
    "cmd": 5,
    "prm": [
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
    "prm": [
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
    "prm": [
      "FNC",
      "ACCEPT=20",
      "MAP",
      "TARGMAX=NAMES:1,LIST:1,KICK:1,WHOIS:1,PRIVMSG:10,NOTICE:10,ACCEPT:,MONITOR:",
      "are supported by this server"
    ]
  },
  {
    "cmd": 251,
    "prm": [
      "There are 5551 users and 10260 invisible on 22 servers"
    ]
  },
  {
    "cmd": 252,
    "prm": [
      112,
      "IRC Operators online"
    ]
  },
  {
    "cmd": 254,
    "prm": [
      8898,
      "channels formed"
    ]
  },
  {
    "cmd": 255,
    "prm": [
      "I have 861 clients and 1 servers"
    ]
  },
  {
    "cmd": 265,
    "prm": [
      861,
      1981,
      "Current local users 861, max 1981"
    ]
  },
  {
    "cmd": 266,
    "prm": [
      15811,
      19586,
      "Current global users 15811, max 19586"
    ]
  },
  {
    "cmd": 250,
    "prm": [
      "Highest connection count: 1982 (1981 clients) (521309 connections received)"
    ]
  },
  {
    "cmd": 375,
    "prm": [
      "- irc.efnet.nl Message of the Day -"
    ]
  },
  {
    "cmd": 372,
    "prm": [
      "- <Removed for example>"
    ]
  },
  {
    "cmd": 376,
    "prm": [
      "End of /MOTD command."
    ]
  }
]
```
