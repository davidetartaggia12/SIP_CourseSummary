<SIP>

# SIP components
- **UAC** : User agent Client
- **UAS** : User agent Server
- **B2BUA** : is a UAS directly connected with a UAC that may replace a SIP proxy or connect to one


- SIP response only for the signaly not for the media
- RTP (real time protocol) : is directly connect between UAC - UAS - B2BUA

<br>

 When you want to connect to :

- **pbx** : private branch excahnge
- **pstn** : public switch telephone network

you need to use a gateway.

<br>

- UAC starts the call 
- UAS anyone answering a call is a UAS (phone or server)

- **IVR** = interactive voice responsive (UAS : server)

<br>

> Server : <br>
*User* : **admin** <br>
*Passwd* : **opensips**

<br>

**Methods** : primary function that a request invoke on a server

RFC defines 6 methods : <br>
- **invite** : session establishment
- **bye** : terminate ad existing session (end the call)
- **cancel** : cancel a pending request (cancel ananswered call)
- **ack** : acknowledge an INVITE (confirm)
- **options** : query the capabilities of the USA (test)
- **register** : register the user in the location table (register phone or other device)

<br>

6 types of response : 
- **provisional response** :
> 1. *100* Trying : stop live retransmission
> 2. *180* Ringing : say that the phone is ringing

- **success** : 
> 1. *200* OK : for invites
> 2. *202* Accepted : for non-invites request

- **redirect** : is used by redirect servers, most of these messages is used on specifics areas

- **client errors** : the code start with 4 (like 400, 401, 402)

- **servers errors** : 
> 1. when you have an *500* error this mean that you have an Internal Server Error (at the logic of your system)
> 2. *503* Service Unavailable : congestion, you forward the request on another server

- **global errors** : you shouldn't do anything to fail over the request, because the problem don't depend on you

<br>

SIP has 5 function (features) : 
1. user location and registration
2. user availability : the server check if the user is available and if the refistration is expired or not
3. session setup : to stablish a session
4. session management : you can complete manage the session
5. discovery of user capabilities : such as codex, streams and many other usage in the SIP negotiation

<br><br>
