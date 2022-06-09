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

 ## REGISTRATION PROCESS :

- **registration request** : when a phone wants to inform its location, it send a registration to a Location Database to a SIP register (in the packet there's also the source and destination socket) 
> N.B: if you want to inform the location you **MUST** register

<br>

- **registration authentication sequence** :
1. UAC send the registration request
2. the SIP Proxy refuse it and send an error packet (401 unauthorized) that contains information (which are useful for authorize header)
3. UAC resend the registration request with the authorize header (with all the information to authenticate it)
4. the SIP Proxy send back the 200/OK packet

<br>

- **record expiration** : UAC suggests a expiration time, and the UAS decides to agree expiration time

<br>

- **typical SIP proxy configurations** : usually the proxy has a Minimun expires, a Maximum expires and a Default expires. If the cloud don't send a expires the default value becomes the expiration time. 

<br>

- **registration details** : the address of record (AOR) can have more than one contact. The registration time mustn't be too small, because too many close request can cause problems. So if you miss some registration maybe you need to increase the registration time.
> N.B: UAC and the UAS clock have to be synchronized

<br><br>

 ## SESSION SETUP AND MANAGEMENT :
- the *SIP ladder* and the *SIP trapezoid* show the same thing, but the first shows the simuation and the other shows the topology.

- **Simulation of a call** : <br> *phone1 <--> proxy1 <--> proxy2 <--> phone2* 

>1. phone1 --- invite ---> proxy1
>2. proxy1 --- trying ---> phone1 **
>3. proxy1 --- invite ---> proxy2
>4. proxy2 --- trying ---> proxy1
>5. proxy2 --- invite ---> phone2
>6. phone2 --- ringing ---> proxy2
>7. proxy2 --- ringing ---> proxy1
>8. proxy1 --- ringing ---> phone1
>9. phone2 --- OK ---> proxy2
>10. proxy2 --- OK ---> proxy1
>11. proxy1 --- OK ---> phone1
>12. phone1 --- ACK ---> phone2
>13. phone2 --- BYE ---> phone1
>14. phone1 --- OK ---> phone2

** *before the invite3 the proxy1 send a request at a DNS server to learn the IP address*

<br><br>

 ## SIP SERVER ARCHITECTURES :

There are 3 types of server architectures: 
1. **Proxy** : it's very similar to HTTP proxy, it's very fast and scalable. It's often use in telephony server provider.
2. **Back-to-Back User Agent** (B2BUA): is more overhead compare to a SIP proxy, but it's more flexable to implement. Most PBXs use this architecture.
3. **Redirect** : it's very light but without much control. Redirect servers are many time used for local number portability.

<br>

- **SIP PROXY ARCHITECTURE** : *One-Leg Call* <br> You have only one Call-ID, the SIP proxy don't change much the request before sending ahead, except the address of the request.

- **SIP REDIRECT ARCHITECTURE** : *Independent Leg Calls* <br> It's more simplar than Proxy, phone1 generate an Invite and the server create a *302 moved temporarily* whith the address at the end of the contact, then phone1 send only the *302* to the phone2. Not all phones support the 'moved temporarily'.ù

- **SIP B2BUA ARCHITECTURE** : *Two Leg Calls* <br> You have two leg = Two Call-ID. The connection remains between the phone and the B2BUA server, not direct between the 2 phones. There's 2 completely different calls connected by the B2BUA server. You can connect 2 phones directly configuring the server in the right mode.

<br><br>

 ## SIP ADDRESSING, HEADERS AND BODY : 
*How to identify components of a SIP message*

<br>

- **Addresses and Aliases** : <br> SIP Addresses are very similar to mail address. They are referred by URI (uniform resources identify) and they determine where the request is going to be send. Aliases should be add at the addresses before sending.

- **Mandatory Headers** : 
> - To
> - From
> - Cseq
> - Call-ID
> - Max-Forwards
> - VIA

>>> All these header fields are mandatory in all SIP requests.

<br>

- Some SIP messages don't have a body, like register.

<br><br>

## SIP Proxy Types :
There are two types of SIP Proxy :
- **Stateful** : the proxy keeps a transaction history, you can manage the replies by the branch number. With SIP Proxy is possible to absorb retransmission. It's possible route using transaction information.
- **Stateless** : the proxy doesn't keeps the transaction in memory, it's slightly faster than Stateful and it has less memory intensive.

<br>

- **Transaction** : are every packet between UAC and UAS. <br> Excpet the special case : *ACK*
> - for negative replies it's part of an existing transaction
> - for positive replies it's a new transaction (To-tag)

<br>

> - There are 6 transaction **states** :
>> 1. Calling : *client started a new call creating a new transaction*
>> 2. Trying : *used for transactions not based on INVITE*
>> 3. Proceeding : *the client/server received/sent a new provisional reply*
>> 4. Completed : *the client/server received/sent a final reply (3xx)*
>> 5. Confirmed : *the client/server sent/received an ACK*
>> 6. Terminated : *after receiving the ACK*

<br>

- A Stateful Proxy stores transaction data, with all information attached to the transaction. The match is based on Branch ID (from VIA header). <br> This lets you to do parallel and serial forking. Suppose that you have 3 phones connected at the same account: with parallel forking the call will be sent at all the device at the same time and every transaction has an independent branch id; with serial forking the call will be forward at one device, then if the proxy receives a negative reply it create a new branch id and send the call to another device.

<br> <br>
