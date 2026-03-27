# LoadBalancer
This repository was made for study purposes about how to implement a Load Balancer, using rust.

## The idea

### The problem
The problem is well known: imagine that I'm a company with a server that can process 30 request simultaneously. But in the reality, when my server receive about 20 requests, it already starts to slow-down.

### The solution
To solve this problem, we should split the 30 request processing capacity of one server into 6 servers that can process 5 request each one. This way we are granting that each server can process its maximum request capacity, simultaneously, with maximum eficiency.
But, this alternative have its own problem: **How to split the connection of clientes into the servers?**

### The magic
A proxy. That's the answer we are looking for.
We create a proxy that runs in front between the client's connection with the server. The proxy now receives the client's request, and **it decides for what server the client will be attatched**. That approach grant us to big benefits:
* No server will become overfilled with request, since when its full, the proxy always will attach clients to other avaiable server.
* Since the client now connects with the proxy, and not with the server, the server is hidden from the client, and that means a strong layer of security.

The reverse proxy designed specifficaly for this functionallity is named **Load Balancer**.

## How I've implemented it

### Server side

* First the server starts, it already has its IP defined and the maximum amount of request that it can process simultaneously.
* Right after it's started, the server tries to do a **handshake** with the proxy, to set - in the proxy - the server IP and how much requests it can handle at the same time. It will keep trying to make the handshake until it succeeds, or else the server will not process ANY request.
* When the handshake ends, mandatorily in a success, it starts focusing on processing common requests that will be forwarded from proxy.

### Proxy side

* First the proxy starts, defining important variables that will hold the information about the relashionships about clients and server. It can receive requests, but will not forward they for anyone while the handshake not occurs.
* When a specific request, routed at *`/server-register`* arrives, the proxy start a server registration process, gathering its IP and max capacity, for its own logistic. The proxy is the one who calculates for which server each client will be attatched.
* After sending the positive response for the handshake, the proxy start receiving the clients requests.
* When a client send a normal request, proxy will verify its own data about the server to select the least full server of all the registered ones, and if it found one, it will attatch that server to that client, and that means that **all the requests sent by that same client, will be processed only by that server it was attatched**.
* After doing its logistics, the proxy forward the clients request to its attatched server and awaits a response.
* When the server response arrives, it forward it to the client, completing the comunication.

### Client side
* For the client, nothing is different, it accesses the website (or send the request) and use it normaly.


