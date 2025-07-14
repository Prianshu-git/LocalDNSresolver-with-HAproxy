# DNS-Driven L7 Proxy Infrastructure with HAProxy & BIND

<div align="center">
<img width="500" height="500" alt="Gemini_Generated_Image_d11e2nd11e2nd11e-removebg-preview" src="https://github.com/user-attachments/assets/5d337cb1-2462-40c6-a9c6-88bb2fc441c7" alt="Logo" width="150"/>
</div>

![Alpine Linux](https://img.shields.io/badge/Alpine_Linux-%230D597F.svg?style=for-the-badge&logo=alpine-linux&logoColor=white) 
![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)


This project contains the files for implementing a local containerized infrastructure with internal DNS-based service resolution with BIND and HAProxy acting as a Layer 7 (HTTP) load balancer. This simulates a near production scenario for service discovery and routing setup.
## System Arch

![IMG_6100](https://github.com/user-attachments/assets/1a596698-b432-4f86-8f69-3ee410726b9f)

## Installation  
To implement this project you would only need Docker installed rest are the images that will be pulled by Docker

```bash
 docker network create --subnet=192.168.100.0/24 internal_net
```

This creates a network named internal_net which has the hostnames defined for our servers BIND,Web & HAproxy.

```bash
docker-compose up -d
```
This builds and launches all the containers in detached mode.

This Lists all the running containers,you can verify if all are running

 ```bash
docker ps
 ```

In this infrastructure DNS is used to resolve internal service names like
web1.internal.lan,web2.internal.lan
to real internal IP addresses like 192.168.100.21 or 192.168.100.22.

This decouples service names from IPs, allowing HAProxy to dynamically discover services without hardcoding them.
 For example in the file db.internal.lan
 web1    IN  A   192.168.100.21
 web2    IN  A   192.168.100.22
This tells BIND: “If someone asks for web1.internal.lan, respond with 192.168.100.21.”

In Docker, the bind container has a fixed IP like 192.168.100.10. It listens on port 53 for DNS queries from inside the network.
You can check if your BIND DNS container directly to resolve web1.internal.lan
 
 ```bash
 dig @192.168.100.10 web1.internal.lan
 ```

To test the load balancing feature enter

```bash
curl http://localhost
```

This sends an HTTP request to HAProxy on port 80, which routes it to web1 or web2.When HAProxy needs to connect to web1.internal.lan, it performs a DNS lookup using the dns_resolver configuration.This request goes to BIND over the internal Docker network.
When you enter the curl command

```bash
curl http://localhost
```

the HAproxy accepts the Http request since its operating on L7 mode,it parses the full HTTP request to URL path,HTTP headers,Methods(GET,POST,etc.)

web1-1 and web2-1 running Nginx containers each is listening on port 80 inside the container but not exposed directly to the host (they're only reachable by HAProxy)it resolves web1.internal.lan and web2.internal.lan via BIND9 DNS.This lets you dynamically register, scale, or replace services without hardcoding IPs.
## Support Material

![IMG_6094](https://github.com/user-attachments/assets/82da4a75-29ce-474a-8651-3160287ca179)![IMG_6096](https://github.com/user-attachments/assets/79a6842a-48fe-4382-bc6a-703a02f8413d)

![IMG_6097](https://github.com/user-attachments/assets/80d394a0-851a-40d1-a267-68ee06fc382c)![IMG_6095](https://github.com/user-attachments/assets/5372541a-ade1-4daa-86cc-a456ff41cdbf)


**To check logs**

```bash
docker ps                                                    //List all running containers
docker logs -f haproxy                                       //view live logs for haproxy service
docker exec -it bind tail -f /var/log/named/named.log        //Live DNS logs
```

## Author
### Prianshu-git :purple_heart:
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0) 
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) 
[![License: MPL 2.0](https://img.shields.io/badge/License-MPL_2.0-brightgreen.svg)](https://opensource.org/licenses/MPL-2.0)
