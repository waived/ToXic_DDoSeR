   ▄▄▄       ▄▄▄      ▄▄▄      ▄         ▄  ▄▄▄       ▄▄▄      ▄▄▄
   ▓▓██▓██████▓▓   ▓████████   ██▓     ███  █▓██████▓▓█▓▓   █████████
   ███  ▓▓▓  ███  ████   ▓▓▓█   ▓██   █▓█   ▓▓▓  ███  ███  ████   ▓█▓█
        ▓██       █▓▓  █  ███    █▓▓ █▓█         ▓▓█       █▓█ 
        ██▓       ▓▓▓ █▓▓ █▓█     █████          ███       ▓▓█
        ▓██       ▓█▓  █  ███    ███ ███         ██▓       ▓██     
        ███       █▓▓█   █▓██   ██▓   █▓▓        ███       █▓██   ████
       █████       █████▓▓▓█   █▓█     ▓██  █▓█▓██▓▓█████   █████▓█▓█
        ▀▀▀           ▀▀▀      ▀         ▀  ▀▀▀       ▀▀▀      ▀▀▀   

What is this?
    Toxic is a typical DDoS Botnet that can be executed/controlled on Windows NT platforms.
    This style of bot comes from the "host booting" era which took place slightly before
    year 2010. Known host booting programs are such that of: MeTuS, BioZombie, HypoCrite,
    XR Host Booter, DoubleDDOS, Silent DDoSer, and MP DDoser (aka, IP Killer).

    This program is no different. I wrote it more or less ironically as more of a "throw
    back" since programs like these are more or less considered obsolete, whereas on today's
    market threat actors rely on botnets located on C2 servers with ways more capabilities.
    This is more or less a "poor man's botnet" if you will. Simple to setup, with no real
    costs to the user, as it can control machines off of one's home network (which is highly
    discouraged for security reasons, but yenno, this wasn't the most security-conscious era
    where these types of programs came from).

How does it work?
    Like many remote-access software, Toxic makes use of web-sockets and listens on a TCP
    port for incoming traffic. Client-side, Toxic makes use of the builder/stub model when
    generating a payload, which is then dropped onto a victim's machine and executed. At which
    point a connection back to the ToXic controller (your main interface) is made. Toxic also
    makes use of the server/client model, by leveraging a reverse TCP connection between each
    device in order to issue it commands.

What are the general commands?
    Toxic has very simple commands: 

    --- UNINSTALL    this disinfects the device (removes the payload)
    --- DISCONNECT   ends the connection of the device but keeps the payload installed
    --- RECONNECT    kills and re-establishes a connection back to the controller
    
    UNICAST vs BROADCAST
    Unicasting is when ToXic sends a command to one device. So let us assume you only
    want to remove one single device (maybe you accidentally ran the payload on your
    machine) you can do just that, without affecting the other devices. Conversely, the
    Broadcasting option will push a command out to all machines. Maybe your connection
    is buggy or you're having latency issues. You want to send the "reconnect" command
    to all infected devices, so you broadcast it and all of the devices will receive
    the command and refresh their connection to the controller.

What are my DDOS options?
    The Distributed Denial-of-Service options are solely broadcast options with no
    unicasting available. Due to the versatility of the code, this can easily be
    implemented with little modifications.

    Toxic issues out attacks on OSI layers three (Network), layers four (Transport), and
    layers seven (Application). Here are all your options:

    --- UDP  (volumetric flood)
        datagram packets are dispersed at a high volume and each data buffer contains
        randomly assembled junk bytes in a dynamic fashion. Each data buffer per packet
        is of a random length and contains a unique byte scheme.

    --- TCP  (volumetric flood)
        transmission stream packets are dispersed at a high volume and each data buffer
        contains randomly assembled junk bytes in a dynamic fashion. Each data buffer per
        packet is of a random length and contains a unique byte scheme.
        
    --- ICMP (volumetric flood)
        the "ping of death" method of sending out ICMP-echo packets functions as the
        UDP and TCP floods do. Here (since ICMP packets use the Network Layer-3) there
        is no need for a source/destination port to be specified. This is the only flood
        that does not make use of a port.
    
    --- HTTP (volumetric/application flood)
        this flood sends out a volley of HTTP-GET packets. Each packet contains HTTP headers
        where the HTTP query contains random characters and is always of a dynamic length.
        This is specifically used to target a "front-end" of a website.
    
    --- TLS  (protocol flood)
        This flood is specifically designed to target websites using HTTPS, specifically encrypting
        content using either TLS (versions 1.2 and 1.3). It is considered a "TLS Exhaustion" attack
        because instead of establishing a TLS handshake, then sending data over an encrypted channel,
        the TLS handshake gets "re-negotiated" over and over again, quickly consuming server-side
        resources. Each packet contains a custom TLS payload supporting TLSv1.2. It is important
        to note that TLSv1.3 is backwards compatible for TLSv1.2 and can handle either type of
        request. The payload contains code for a TLSv1.3 re-negotiation if the user wants to quickly
        modify the code.

How to regulate power?
    Each attack has a power option which regulates the size of a floods data buffer:

        --- WEAK
            Packet buffer from 100 to 500 bytes. Quick and stable.
            
        --- MODERATE
            Packet buffer from 1000 to 1500 bytes. Stable, and fits in more MTU sizes on
            the destination (victim) network.
            
        --- STRONG
            Packet buffer from 10,000 to 20,000 bytes. Less stable and will more likely get
            fragmented when arriving at destination; takes up resources.
            
        --- INSANE
            Packet buffer from 60,000 to 65,000, nearly reaching the maximum allows packet
            size accepted by any network. Not stable. High impact.

    This option truly applied to either UDP, TCP, and ICMP attacks because of their volumetric
    nature, as they contain data buffers which are designed to bog down a network. Both HTTP
    and TLS attack remain static in their abilities.

Any known bugs?
    Not so far, but I plan to continue with this project in the near future. If found, please
    be sure to issue a bug-report.

Future updates?
    - Threading capability! Generates a larger overhead of traffic per each device.
    - Network meter. Lets the attacker choose what percentage of their network to use for an attack.

    - More flood options:
        --- SLOW-HTTP   Slowloris-type attack designed to eat up sockets)
        --- HOLD        Send a null-byte over many concurrent sockets every few seconds. SYN-cookie bypass
        --- OVERTCP     TCP flood designed to bypass rate-limiting from load-balancers
        --- CC          Challenger-collapsar. Bypass Web Captchas via HTTP
        --- RHEX        Random hex bits over UDP. Good for game floods and OVH servers
        --- VSE         Value source engine (Steam serve) attack
        --- RECOIL      Downloads a file slowly, byte by byte, over many concurrent sockets.
        --- TS3         UDP attack designed for TEAMSPEAK_V3 servers using ts3-query abuse

Legal statement:
    Included in this repository is a legal statement. By downloading, modifying, distributing, and
    installing this software (server/client) you hereby acknowledge the enclosed legal statement.

    Don't be a jerk! Use responsible to educate and protect yourself.



Enjoy <3 Waived

    
