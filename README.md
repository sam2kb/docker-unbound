Unbound (with DNSSEC validation)
===========

# Current versions

- **unbound**: 1.6.6
- **libressl**: 2.6.2

# Running

Use this command to start the container. Unbound will listen on port 53/udp.

```docker run --name unbound -d -p 53:53/udp -p 53:53 folhabranca/unbound:latest```

(optional)
If you want to override the nameserver in the unbound container, you can use:

```docker run --name unbound -d -p 53:53/udp -p 53:53 --dns="127.0.0.1" folhabranca/unbound:latest```

# Configuration
These options can be set via the environment variable -e flag:

- **PORT**: Port number that unbound will listen. This has to be the same of the docker run. (Default: 53, Values: <1-65535>)
- **DO_IPV6**: Enable or disable ipv6. (Default: "yes", Values: "yes, no")
- **DO_IPV4**: Enable or disable ipv4. (Default: "yes", Values: "yes, no")
- **DO_UDP**: Enable or disable udp. (Default: "yes", Values: "yes, no")
- **DO_TCP**: Enable or disable tcp. (Default: "yes", Values: "yes, no")
- **VERBOSITY**: Verbosity number, 0 is least verbose. (Default: "0", Values: "<integer>")
- **SO_REUSEPORT**: Use SO_REUSEPORT to distribute queries over threads. (Default: "no", Values: "yes, no")
- **HIDE_IDENTITY**: Enable to not answer id.server and hostname.bind queries. (Default: "no", Values: "yes, no")
- **HIDE_VERSION**: Enable to not answer version.server and version.bind queries. (Default: "no", Values: "yes, no")
- **QNAME_MINIMISATION**: If enabled only send the full name when needed. (Default: "yes", Values: "yes, no")
- **RRSET_ROUNDROBIN**: If enabled, if there are more the one answer, alternate the replies in a round robing fashion. (Default: "yes", Values: "yes, no")
- **USE_CAPS_FOR_ID**: Use random caps in the query to improve reply verifiacation. (Default: "yes", Values: "yes, no")
- **ENABLE_REMOTE_CONTROL**: Enable or disable the unbound-control command. (Default: "no", Values: "yes, no")
- **USE_LOGFILE**: Use log file instead of stdout (Default: "no", Values: "yes, no")
- **USE_CHROOT**: Use chroot while running unbound. (Default: "yes", Possible values: "yes,no")

# More config control

If you need to use other control commands, just mount a bind dir to
`/opt/unbound/etc/unbound/unbound.conf.d` and put a <.conf> file in there with your configuration.

Note: The default access configuration is in this directory. If you are mounting this directory, you need at
least to include access configuration, otherwise you will get REFUSED reply.

# Docker compose

If using docker-compose, use like this:

```
services:                                 
  unbound:                                                                                                    
    image: folhabranca/unbound:latest                                                                        
    network_mode: bridge                                                                                      
    ports:                                                                                                    
      - "53:53/udp"                                                                                           
      - "53:53"                                                                                               
    volumes:                                                                                                  
      - ./unbound.conf.d:/opt/unbound/etc/unbound/unbound.conf.d                                              
      - ./log:/opt/unbound/etc/unbound/log                                                                    
    environment:                                                                                              
      - PORT=53                                                                                               
      - DO_IPV6=no                                                                                            
      - DO_IPV4=yes                                                                                           
      - DO_UDP=yes                                                                                            
      - DO_TCP=yes                                                                                            
      - VERBOSITY=1                                                                                           
      - NUM_THREADS=1                                                                                         
      - SO_REUSEPORT=yes                                                                                      
      - HIDE_IDENTITY=yes                                                                                     
      - HIDE_VERSION=yes                                                                                      
      - QNAME_MINIMISATION=yes                                                                                
      - RRSET_ROUNDROBIN=yes                                                                                  
      - USE_CAPS_FOR_ID=yes                                                                                   
      - ENABLE_REMOTE_CONTROL=yes                                                                             
      - USE_LOGFILE=no                                                                                        
      - USE_CHROOT=yes                                                                                        
    cap_add:                                                                                                  
      - net_admin                                                                                             
    restart: always
```

Note: `net_admin` capability must be added to the container if you want to change the so-rcvbuf or so-sndbuf.

# Unbound-control

`unbound-control` is available if **ENABLE_REMOTE_CONTROL** is set to **yes**. To access it 
just create a script like this:

```
#!/bin/sh

docker exec unbound_unbound_1  /opt/unbound/sbin/unbound-control $@
```

# Known to work

This image was tested with the latest docker-ce software. Be sure to upgrade it if you are having problem.