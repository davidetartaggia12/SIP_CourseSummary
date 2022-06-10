<SIP>

# SIP

## SIP COMPONENTS
- **UAC** : User agent Client
- **UAS** : User agent Server
- **B2BUA** : is a UAS directly connected with a UAC that may replace a SIP proxy or connect to one


- SIP response only for the signal, not for the media
- RTP (real time protocol) : is directly connected between UAC - UAS - B2BUA

<br>

 When you want to connect to :

- **PBX** : private branch exchange
- **PSTN** : public switch telephone network

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

**Methods** : the primary function that a request invoke on a server

RFC defines 6 methods : <br>
- **invite** : session establishment
- **bye** : terminate ad existing session (end the call)
- **cancel** : cancel a pending request (cancel unanswered call)
- **ack** : acknowledge an INVITE (confirm)
- **options** : query the capabilities of the USA (test)
- **register** : register the user in the location table (register phone or other devices)

<br>

6 types of response : 
- **provisional response** :
> 1. *100* Trying : stop live retransmission
> 2. *180* Ringing : say that the phone is ringing

- **success** : 
> 1. *200* OK : for invites
> 2. *202* Accepted : for non-invites request

- **redirect** : is used by redirect servers, most of these messages are used in specifics areas

- **client errors** : the code start with 4 (like 400, 401, 402)

- **servers errors** : 
> 1. when you have a *500* error this means that you have an Internal Server Error (at the logic of your system)
> 2. *503* Service Unavailable : congestion, you forward the request to another server

- **global errors** : you shouldn't do anything to fail over the request, because the problem doesn't depend on you

<br>

SIP has 5 functions (features) : 
1. user location and registration
2. user availability : the server checks if the user is available and if the registration is expired or not
3. session setup : to establish a session
4. session management : you can completely manage the session
5. discovery of user capabilities : such as codex, streams, and many other usages in the SIP negotiation

<br><br>

 ## REGISTRATION PROCESS :

- **registration request** : when a phone wants to inform its location, it sends a registration to a Location Database to a SIP register (in the packet there's also the source and destination socket) 
> N.B: if you want to inform the location you **MUST** register

<br>

- **registration authentication sequence** :
1. UAC sends the registration request
2. the SIP Proxy refuses it and sends an error packet (401 unauthorized) that contains information (which is useful for authorize header)
3. UAC resend the registration request with the authorized header (with all the information to authenticate it)
4. the SIP Proxy sends back the 200/OK packet

<br>

- **record expiration** : UAC suggests an expiration time, and the UAS decides to agree expiration time

<br>

- **typical SIP proxy configurations** : usually the proxy has a Minimum expires, a Maximum expires and a Default expires. If the cloud doesn't send an expiration the default value becomes the expiration time. 

<br>

- **registration details** : the address of record (AOR) can have more than one contact. The registration time mustn't be too small, because too many close requests can cause problems. So if you miss some registration maybe you need to increase the registration time.
> N.B: UAC and the UAS clock have to be synchronized

<br><br>

 ## SESSION SETUP AND MANAGEMENT :
- the *SIP ladder* and the *SIP trapezoid* show the same thing, but the first shows the simulation, and the other shows the topology.

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

** *before the invite3 the proxy1 sends a request to a DNS server to learn the IP address*

<br><br>

 ## SIP SERVER ARCHITECTURES :

There are 3 types of server architectures: 
1. **Proxy** : it's very similar to HTTP proxy, it's very fast and scalable. It's often used by telephony service providers.
2. **Back-to-Back User Agent** (B2BUA): is more overhead compared to a SIP proxy, but it's more flexible to implement. Most PBXs use this architecture.
3. **Redirect** : it's very light but without much control. Redirect servers are much time used for local number portability.

<br>

- **SIP PROXY ARCHITECTURE** : *One-Leg Call* <br> You have only one Call-ID, the SIP proxy doesn't change much of the request before sending ahead, except the address of the request.

- **SIP REDIRECT ARCHITECTURE** : *Independent Leg Calls* <br> It's simpler than Proxy, phone1 generates an Invite and the server creates a *302 moved temporarily* with the address at the end of the contact, then phone1 sends only the *302* to the phone2. Not all phones support the 'moved temporarily'.ù

- **SIP B2BUA ARCHITECTURE** : *Two Leg Calls* <br> You have two legs = Two Call-ID. The connection remains between the phone and the B2BUA server, not directly between the 2 phones. There are 2 completely different calls connected by the B2BUA server. You can connect 2 phones directly configuring the server in the right mode.

<br><br>

 ## SIP ADDRESSING, HEADERS, AND BODY : 
*How to identify components of a SIP message*

<br>

- **Addresses and Aliases** : <br> SIP Addresses are very similar to mail addresses. They are referred by URI (uniform resources identify) and they determine where the request is going to be sent. Aliases should be added at the addresses before sending.

- **Mandatory Headers** : 
> - To
> - From
> - Cseq
> - Call-ID
> - Max-Forwards
> - VIA

>>> All these header fields are mandatory in all SIP requests.

<br>

- Some SIP messages don't have a body, like a register.

<br><br>

## SIP PROXY TYPES :
There are two types of SIP Proxy :
- **Stateful** : the proxy keeps a transaction history, you can manage the replies by the branch number. With SIP Proxy is possible to absorb retransmission. It's possible to route using transaction information.
- **Stateless** : the proxy doesn't keep the transaction in memory, it's slightly faster than Stateful and it has less memory intensive.

<br>

- **Transaction** : are every packet between UAC and UAS. <br> Except for the special case : *ACK*
> - for negative : replies it's part of an existing transaction
> - for positive : replies it's a new transaction (To-tag)

<br>

> - There are 6 transaction **states** :
>> 1. Calling : *client started a new call creating a new transaction*
>> 2. Trying : *used for transactions not based on INVITE*
>> 3. Proceeding : *the client/server received/sent a new provisional reply*
>> 4. Completed : *the client/server received/sent a final reply (3xx)*
>> 5. Confirmed : *the client/server sent/received an ACK*
>> 6. Terminated : *after receiving the ACK*

<br>

- A Stateful Proxy stores transaction data, with all information attached to the transaction. The match is based on Branch ID (from the VIA header). <br> This lets you do parallel and serial forking. Suppose that you have 3 phones connected to the same account: with parallel forking, the call will be sent to all the devices at the same time and every transaction has an independent branch id; with serial forking, the call will be forward at one device, then if the proxy receives a negative reply it creates a new branch id and sends the call to another device.

<br> <br>

  ## SIP TIMERS :
- The most important SIP timers are:
> - T1 (500ms) - *Round Trip Time (RTT)* : decrease the value isn't recommended because the proxy needs some time to process a request.
> - T2 (4s) - *Maximum network retransmissions (Non-INVITE/Replies)*
> - TimerA - *INVITE retransmissions time (initially T1)* 
> - TimerB - *INVITE transaction timeout (64\*T1 = 32s)* 
> - TimerH - *Wait for ACK retransmission (64\*T1 = 32s)*

- There are some Proxy timers:
> - TimerC - *Proxy's transaction timeout*
> - FR_TIMER - *Maximum wait time before a provisional reply (100 Trying)*
> - FR_INV_TIMER - *After the provisional reply, how long to wait for the call to be completed*
 
<br> <br>

## SIP DIALOGS :
- Dialogs persist for the duration of a call or subscription : between INVITE <-----> BYE and between SUBSCRIBE <-----> NOTIFY.

- Dialogs are identified by Call-ID, From-Tag and, To-Tag, so the requests on the same Dialog have the same Call-ID, From-Tag, and To-Tag.

- Dialogs are a collection of transactions: you can create a dialog with INVITE or SUBSCRIBE, modify a dialog with RE-INVITE and finish a dialog using BYE or NOTIFY.

- **Initial Requests** : Can be easily identified by the lack of the 2 tags *(es. INVITE)*
- **Sequential (in-dialog) Requests** : have the same Call-ID, From-Tag, and To-Tag, and can be easily identified by the presence of the To-Tag *(BYE, ACK, RE-INVITES)*

- The ACK at the end of transmission contains all the intermediary route information, UAC and UAS store these pieces of information.

<br> <br>

## MEDIA : 
*how the media is encode*

- **PCM** - *Pulse Code Modulation* : is a technique to take an Analog signal and transform it into a Digital signal.

- To transport a conversation we have to generate a lot of packets because we can't send packets bigger than 1500 bytes. To packetize this digital voice we have to use the *Real-Time Transport Protocol (RTP)*.

- **Frame** : 
> 1. Ethernet Destination Address
> 2. Ethernet Source Address
> 3. Ethernet Type
> 4. IP Header
> 5. UDP Header
> 6. RTTP Header
> 7. Voice Payload
> 8. Ethernet Checksum

- To reduce bandwidth we can :
> - increase the voice payload in the frames
> - compress RTP

<br> <br>

## SESSION DESCRIPTION PROTOCOL (*SDP*)
– It's defined on the RFC4566, it's a parameter negotiation for session protocol. SDP exchange capabilities and media contact addresses between 2 peers on the communication. 

- There are 2 types of negotiation : 
> - **Normal Negotiation** : it's an INVITE with SDP offer and 200/OK with SDP answer.
> - **Late Negotiation** : it's an INVITE without SDP, receive a 200/OK with SDP offer and then an ACK with SDP answer *(TPCC = Third Part Call Control)*.

- **SDP Offer** : it contains a lot of information, the most important is the M-Line (*media description*) and the connection pieces of information (*Ip addresses, Codec, Ports*). All the other information maybe will be used from the phone.
> N.B: in the normal negotiation point A decides the codec, in the late negotiation point B will decide it.

- **SDP Answer** : it contains the information used with the specifications.

- **SDP Parts** : we have 3 parts of the session description protocol : 
> - **Session Description** : it contains a lot of information about the system, the most important is the encryption key. *Its use depends on the application*.
> - **Timing Description** : if we want to time the section there are some possibilities to put the time on SDP. *Its use depends on the application*.
> - **Media Description** : the most important pieces of information are the media name and transport address (*m*) and the connection information (*c*). *The importance of bandwidth and encryption key depends on the application*.

<br> <br>

## REAL-TIME PROTOCOL (*RTP*):
- RTP is defined in RFC3550 and it is responsible for the media packetization. The main features of RTP the payload identification, delivering monitoring, and timestamping.
> N.B: if you receive a packet with a sequence number out of sequence there's nothing you can do to use it. You only have to discard the packet.

- **RTP Control Protocol** : RTCP is a protocol for voice feedback, so if you want to have the quality of the call you have to activate RTCP, it's not activated by default. There are some secondary functions applicate to video and audio conferences.

- **RTCP packet types** : 
> 1. Sender Report (SR) : *statistics of the activated participants*
> 2. Received Report (RR) : *statistics for inactive participants*
> 3. SDES : *items source descriptions such as CNAME*
> 4. BYE : *end of participation (conference)*
> 5. APP : *application specific functions*
>> N.B: the most important are SR and RR

<br> <br>

## VOICE QIALITY IN VoIP CALLS :
- Quality voice is based on 3 parameters : Jitter (*<20ms*), Packet Loss (*0%*), and Latency (*<150ms*). To measure these parameters you can capture all RTP packets or you can use RTCP feedback.

- If you want to improve your voice quality : first you have to control your network (*jitter, latency, and packet loss*). The second thing is to choose the right codec. The last thing is the end user device: *like the headset, and the phone*.

<br><br>
