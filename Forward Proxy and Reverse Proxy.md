## Forward Proxy

A forward proxy, often called a proxy, proxy server, or web proxy, is a server that sits in front of a group of client machines. When those computers make requests to sites and services on the Internet, the proxy server intercepts those requests and then communicates with web servers on behalf of those clients, like a middleman.

![[Pasted image 20231106144501.png]]

### Benefits of a Forward Proxy

A forward proxy offers numerous benefits to an organisation and its users, including the following:

- **User Privacy:** A forward proxy sits between a user’s computer and the public Internet and makes requests on its behalf. This can help to protect the privacy of the users behind the proxy.
- **Policy Enforcement:** With a forward proxy, all corporate web traffic flows through the proxy. This allows the proxy to inspect the requests and responses and enforce corporate security and acceptable use policies.
- **Traffic Visibility:** All web traffic flows through the forward proxy. This provides insight into how the organization uses cloud infrastructures, applications and other third-party services.
- **Shadow IT Detection:** Devices deployed without IT approval — i.e. “shadow IT” — commonly are designed to call out to cloud-based servers.

## Reverse Proxy

A reverse proxy is a server that sits in front of one or more web servers, intercepting requests from clients. This is different from a forward proxy, where the proxy sits in front of the clients. With a reverse proxy, when clients send requests to the origin server of a website, those requests are intercepted at the network edge by the reverse proxy server. The reverse proxy server will then send requests to and receive responses from the origin server.

![[Pasted image 20231106145430.png]]

### Reverse proxies are used for several purposes:

1. **Load balancing**: Reverse proxies are employed to distribute incoming traffic across multiple back-end servers, enhancing a website's capacity to handle a high volume of requests. This load balancing ensures that even if one server is overwhelmed or experiences issues, traffic can be redirected to other operational servers, maintaining the website's availability.

2. **Caching**: Reverse proxies can cache frequently requested data, such as images and videos. This caching reduces the load on the origin servers and speeds up website performance, especially for businesses with significant multimedia content.

3. **Anonymity and security**: By intercepting incoming requests, reverse proxies provide an additional layer of security for back-end servers. They help protect web servers from potential threats by blocking suspicious traffic from specific IP addresses, thereby enhancing security and safeguarding against malicious actors.