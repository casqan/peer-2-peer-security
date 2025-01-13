# Intro

- Partly due to the corona pandemic, surge in video conferencing platforms, online collaboration, multiplayer gaming
- they were faced with a problem.
- The amount of data that had to be transmitted became evermore larger
- started growing reliant on peer-to-peer infrastructure to alleviate bandwidth
- peer-to-peer making one less stop over server infrastructure

- at the forefront of this WebRTC, a web standard enabling audio, video and data transmission directly between browsers or clients without central servers*
- enables Cross-Platform peer-to-peer connections -> Discord being available on Mobile and Desktop f.e.
- developed by Google and adopted by platforms like Big Blue Button, Zoom and Discord -> the way, to do calls over the internet

# Why use Peer-To-Peer?
- decentralization -> big buzz word in regards to things like Block-Chain etc. however
- reducing single points of failure and increases reliability
- reduced latency -> direct connections minimize delays
- scalability -> scales itself as clients do most of the heavy lifting
- Cost efficiency -> less infrastructure reduces cost and makes infrastructure more movable -> not necessarily vendor locked
  
# Presentation Goals  

- Understand WebRTC’s architecture and security challenges.

- Understand how these challenges are solved.

- Highlight the risks involved with peer-to-peer.  

- Explore security mechanisms and mitigation strategies.

# Core components

- **NAT (Network Address Translation):** Method of mapping an IP address space into another by modifying network address information in the IP header of packets.
  - When connecting to any server using your router at home and IPv4, you connect using your router's ip address, not your client's
  - makes peer-to-peer difficult 

- **SDP (Session Description Protocol):** Describes session information.

- **STUN (Session Traversal Utilities for NAT):** Discovers public IP and port.
  - finds a public IP address and port combo that can be connected to -> IPv6 mostly

- **TURN (Traversal Using Relays around NAT):** Relays traffic when direct connections fail.  
  - falls back to standard client-server architecture, should a peer-to-peer connection not be possible
  - f.e. in a business environment

- **ICE (Interactive Connectivity Establishment):** Selects the best connection path.  
   - candidates 

- **DTLS (Datagram Transport Layer Security):** Encrypts data streams.  

- **SRTP (Secure Real-time Transport Protocol):** Secures media streams.
  - sits ontop of DTLS -> often used together as DTLS-SRTP

# How WebRTC Works

- Signaling Phase
  - SDP and ICE candidates exchanged between clients
- Use STUN to go around NAT and use TURN as a fallback
  - UDP as underlying communications protocol
  - DTLS handshake for encryption
  - SRTP/SCTP for secure media and transmission.

- now that we know, how to connect to someone, how can we know we can trust them? can't they just pretend to be whoever they want to be?

# How to know who to trust?

> On the internet, nobody knows you're a dog.
- 1993 Peter Steiner

in the Peer-To-Peer fashion, challenges arise from
- a lack of a central authority
- anonymous nature of WebRTC
- Impersonation risks

WebRTC employs a few strategies to mitigate these issues

# The trust model in WebRTC

- Decentralized Trust Model:
- lacks central authority to verify peers -> actively not defined in the documentation
- relies on cryptographic Protocols

**Goal:**
- Ensure the integrity, authenticity and confidentiality of communication

# Authenticated Entities

- **Identity Providers (IdPs):**  
  - Provide credentials to verify the identity of peers.  
  - Use tokens to establish mutual trust.  

- **Signaling Server Trust:**  
  - Facilitates SDP and ICE exchange.  
  - Does not participate in media transmission.  
  - Secure signaling via HTTPS or WSS (WebSockets).
  - HTTPS offers Certificates to ensure the Signaling server can be trusted

# Unauthenticated Entities

- **Challenges of Unauthenticated Entities:**
  - No direct verification of peer identity in many cases.  
  - Vulnerable to impersonation or unauthorized access.  

- **Examples:**
  - Public peer-to-peer gaming lobbies. -> opening up Krunker.io in the browser or any game not requiring an authentication 
  - Ad hoc WebRTC-based communication tools. -> Zoom, or BigBlueButton with guest signin

- **Mitigation Strategies:**
  - Use of tokens or passphrases for peer verification. 
    - employ default signin procedures like OICD or 0auth during Signaling  
  - Regularly monitor TURN server activity.
    - watch what traffic runs over your turn servers -> Slack's a good example for this  
  - Implement rate-limiting to prevent abuse.
    - ensure people cannot just connect to your turn or signaling servers

## Authentication ≠ Trust
- **Verifying identity** (e.g., Dr. Evil owns `example.org`) does not imply trustworthiness.  

- **User Decision:** Users must decide whether to grant access based on the authenticated entity.  

- **Temporary Trust:** Access to sensitive resources (e.g., camera/mic) should be *limited to context-specific use* (e.g., a single call).  

- **Identification as Prerequisite for Trust:** Policies depend on proper identification of network elements.  Identification enables informed trust decisions and policy application.

## Identification

**Challenges in Identification**

- **Dynamic Networks**: NATs and changing IPs hinder peer identification.

- **No Central Authority**: Decentralized model lacks built-in verification.

- **Impersonation Risks**: Malicious entities can mimic legitimate peers.

- **Context Matters**: Trust varies across use cases (e.g., public vs. private).

- **Security vs. Usability**: Balancing ease of use with strong identity checks.

## Role of Identity Providers (IdPs)

- Identity Verification: Cryptographic credentials ensure authenticity.

- Token-based Trust: Tokens exchanged for secure signaling.

- User Convenience: Trusted logins simplify identity management.

- Supports Decentralization: Maintains WebRTC’s P2P architecture.

- Enables Trust Decisions: Verified identities guide user trust.

- now that we know, who to trust and how we can identify them, how do we know the data arrives properly in one piece on the other end?

# Authenticity and Data Integrity

- WebRTC uses UDP as it's default protocol with TCP as a fallback
  - UDP being a connectionless Protocol brings it's own challenges
- End-to-End encryption by default.

- Protocols used for data security:
  - **DTLS**
  - **SRTP**

## DTLS

- **Encryption:** Ensures that data transmitted between peers is encrypted to prevent unauthorized access. Uses symmetric encryption (e.g., AES, ChaCha20) for fast and secure communication.

- **Authentication:** Verifies the identity of both parties during the handshake process. Uses digital certificates or pre-shared keys (PSKs).

- **Integrity:** Protects against data tampering by using cryptographic hash functions (e.g., HMAC).

- **Replay Protection:** Prevents attackers from re-sending captured packets by assigning sequence numbers and timeouts.

## SRTP (Secure Real-time Transport Protocol)
- **Encryption for Confidentiality**: Encrypts media content using AES in Counter Mode.
- **Message Authentication**: Uses HMAC-SHA1 to verify integrity and prevent tampering.
- **Replay Protection**: Includes sequence numbers to prevent replay attacks.

- Relies on DTLS for exchanging cryptographic keys during the WebRTC handshake
