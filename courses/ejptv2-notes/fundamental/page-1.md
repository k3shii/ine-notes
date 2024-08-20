# Page 1

## HTTP Protocol Basics <a href="#http-protocol-basics" id="http-protocol-basics"></a>

> ⚡ P.T. Usage:
>
> * Ability to _exploit_ web applications and find _vulnerabilities_ in web servers and services
> * Web app technology is used market-wide also by desktop and mobile apps

**`HTTP`** (**H**yper**T**ext **T**ransfer **P**rotocol) is one of the most used application protocol on the Internet, built on top of TCP.

* It is the **client-server protocol** used to transfer web pages and web application data.
* Is is a **stateless** protocol.
* The client (a web browser) connects to a web server (usually Microsoft IIS, Apache HTTP Server, or others) by sending HTTP **requests** to the server and getting back HTTP **responses**.
* First a TCP connection is established, then the HTTP messages are sent.

![](https://blog.syselement.com/\~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-510a671fafcce38e02ce1224396fcd1e04f83381%252Fimage-20220407181153473.png%3Falt%3Dmedia\&width=768\&dpr=4\&quality=100\&sign=12063bd2\&sv=1)HTML Request - image by hpbn.co

### HTTP Requests <a href="#http-requests" id="http-requests"></a>

| _The structure of an HTTP message_ |
| ---------------------------------- |
| "**Start line**\r\n"               |
| "**Headers**\r\n"                  |
| "\r\n"                             |
| "**Message Body**\r\n"             |

* **`"\r\n"`** (carriage return & newline) are used to the lines in HTTP.
* Each start-line contains 3 elements:
  1. an `HTTP method` (verb/noun) that states the type of the request, like: GET, PUT, POST, HEAD, OPTIONS
  2. the `request target` (URL/path), which resource the browser is asking for
  3. the _protocol version_ (`HTTP/1.x`)which defines the structure of the remaining message, telling the server how to communicate with the browser
* ```
  GET / HTTP/1.1
  ```
* Each header is represented in a single line, composed of a String followed by a colon (**:**) and a value:
  * _Header-name: value_
* ```
  Host: example.com
  Upgrade-Insecure-Requests: 1
  User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.131 Safari/537.36
  Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
  Accept-Encoding: gzip, deflate
  Accept-Language: en-US,en;q=0.9
  Connection: close

  (body)
  ```
  * The `Host` value is obtained from the URI (**U**niform **R**esource **I**dentifier) of the resource.
  * `User-Agent` - reveals the client software issuing the request (Chrome, Safari, Firefox, mobile app ...) and the operating system version.
  * `Accept` - expected document type.

### HTTP Responses <a href="#http-responses" id="http-responses"></a>

The web server _processes the request and sends an HTTP response_ back to the client:

* The start-line, called the **`status-line`**, contains the protocol version, a status code and a status text.
* HTTP headers same as above, different headers may appear, such as:
  * `Cache-Control` - informs the client about cached content (saves bandwidth)
  * `Content-Type` - lets the client know how to interpret the body
  * `Server` - header of the web server, software running (useful in pentesting!)
  * `Content-Length` - length in bytes of the message body
* The `body` is separated from the header by 2 empty lines (\r\n\r\n).

```
HTTP/1.1 200 OK
Accept-Ranges: bytes
Age: 259261
Cache-Control: max-age=604800
Content-Type: text/html; charset=UTF-8
Date: Thu, 07 Apr 2022 17:07:05 GMT
Etag: "3147526947"
Expires: Thu, 14 Apr 2022 17:07:05 GMT
Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT
Server: ECS (dcb/7EA5)
Vary: Accept-Encoding
X-Cache: HIT
Content-Length: 1256
Connection: close

<!doctype html>
<html>
...
</html>
```

* _HTTP Request / Response example_

![](https://blog.syselement.com/\~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-a09e050c3ae990b11537fd615b0a3d30e391aee8%252Fimage-20220407192415082.png%3Falt%3Dmedia\&width=768\&dpr=4\&quality=100\&sign=15e3a08e\&sv=1)

* Common status codes:

| Status code                   | Meaning                                                                                                                                                                          |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **200** OK                    | the request has succeeded (_**Successful codes 2xx**_)                                                                                                                           |
| **301** Moved Permanently     | the target resource has been assigned a new permanent URI (_**Redirection codes 3xx**_)                                                                                          |
| **302** Found                 | the target resource resides temporarily under a different URI (Redirection codes 3xx)                                                                                            |
| **403** Forbidden             | the server understood the request but refuses to authorize it, client doesn't have enough privileges (_**Client Error codes 4xx**_)                                              |
| **404** Not Found             | the origin server did not find a current representation for the target resource or is not willing to disclose that one exists (Client Error codes 4xx)                           |
| **500** Internal Server Error | the server encountered an unexpected condition that prevented it from fulfilling the request (_**Server Error codes 5xx**_)                                                      |
| **502** Bad Gateway           | the server, while acting as a gateway or proxy, received an invalid response from an inbound server it accessed while attempting to fulfill the request (Server Error codes 5xx) |

> 📌 For more in depth info refer to [RFC 9110 - HTTP Semantics](https://httpwg.org/specs/rfc9110.html)

***

## HTTPS Protocol Basics <a href="#https-protocol-basics" id="https-protocol-basics"></a>

HTTP (a clear-text protocol) can be protected using an **encryption** layer, by using a _cryptographic protocol_ like SSL/TLS.

**`HTTPS`** (HTTP Secure) is a method to run HTTP over SSL/TLS.

* This encryption layer protects data exchanged between the client and the server, but it does _not protect against web application flaws_. XSS and SQL injections attack will still work.
* It provides **confidentiality**, **integrity protection** and **authentication** to the HTTP protocol.
* Application layer communication cannot be sniffed or altered by an eventual attacker.
* Traffic can be sniffed and inspected by a user, but he cannot know HTTP headers, body, target domain or what data is exchanged.
  * Target IP address and target port can be recongnized
  * DNS (or similar protocols) may disclose which domain user tries to resolve

![](https://blog.syselement.com/\~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-a04b647b8d80d0331868182ce13c54937edd6316%252Fimage-20220411201109560.png%3Falt%3Dmedia\&width=768\&dpr=4\&quality=100\&sign=c6cff4\&sv=1)TLS Handshake - image by hpbn.co

> 📌 Refer to the [High Performance Browser Networking](https://hpbn.co/transport-layer-security-tls/) book for more in depth information.

### Command Line / GUI Tools <a href="#command-line-gui-tools" id="command-line-gui-tools"></a>

#### Netcat <a href="#netcat" id="netcat"></a>

**`netcat`** or **`nc`** is a tool that reads and writes data accross network connections (opens a raw connection to a service port for example), using TCP or UDP protocol.

* considered as a Swiss army knife of networking tools
* used to debug and monitor network connections, scan for open ports, transfer data, listen for incoming connections (reverse shells) and more

**`nc -h`**

![](https://blog.syselement.com/\~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-72623fb115c365d5978fcaee7c3251d4f4b0b32a%252Fimage-20220411221502839.png%3Falt%3Dmedia\&width=768\&dpr=4\&quality=100\&sign=4999d502\&sv=1)

* Establishing a connection to a server and communicate via HTTP:

> * **`nc -v example.com 80`**

```bash
$ nc -v example.com 80
Warning: inverse host lookup failed for 93.184.216.34: Unknown host
example.com [93.184.216.34] 80 (http) open
GET / HTTP/1.1			# Input Request
Host: example.com		# 2 lines \r\n after this line

HTTP/1.1 200 OK			# Response - status line
Accept-Ranges: bytes
Age: 414218
Cache-Control: max-age=604800
Content-Type: text/html; charset=UTF-8
Date: Mon, 11 Apr 2022 20:05:16 GMT
Etag: "3147526947"
Expires: Mon, 18 Apr 2022 20:05:16 GMT
Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT
Server: ECS (dcb/7F84)
Vary: Accept-Encoding
X-Cache: HIT
Content-Length: 1256

<!doctype html>
<html>
<head>
    ...
</head>

<body>
...
</body>
</html>
```

* **`nc -v my.ine.com 80`**

> ```bash
> $ nc -v my.ine.com 80                                                     
> DNS fwd/rev mismatch: my.ine.com != server-108-138-36-10.muc50.r.cloudfront.net
> DNS fwd/rev mismatch: my.ine.com != server-108-138-36-101.muc50.r.cloudfront.net
> DNS fwd/rev mismatch: my.ine.com != server-108-138-36-96.muc50.r.cloudfront.net
> DNS fwd/rev mismatch: my.ine.com != server-108-138-36-29.muc50.r.cloudfront.net
> my.ine.com [108.138.36.10] 80 (http) open
> GET / HTTP/1.1			# Input Request
> Host: my.ine.com		# 2 lines \r\n after this line
>
> HTTP/1.1 301 Moved Permanently	# Response - status line
> Server: CloudFront
> Date: Mon, 11 Apr 2022 20:18:56 GMT
> Content-Type: text/html
> Content-Length: 183
> Connection: keep-alive
> Location: https://my.ine.com/	# Index page moved here
> X-Cache: Redirect from cloudfront	# X- headers are for non-standard usage
> Via: 1.1 210c8ad3e752d602af05a2de06eb2ff8.cloudfront.net (CloudFront)
> X-Amz-Cf-Pop: MUC50-P2	
> X-Amz-Cf-Id: 7zDSRlBNtqmIjJuUwda9jFQkwxA0lzp4w0Lv3R0vNlkzdapfvQPRcQ==
>
> <html>
> <head><title>301 Moved Permanently</title></head>
> <body bgcolor="white">
> <center><h1>301 Moved Permanently</h1></center>
> <hr><center>CloudFront</center>
> </body>
> </html>
>
> HEAD / HTTP/1.1			# Input HEAD Request
> Host: my.ine.com		# 2 lines \r\n after this line
>
> ...
> ```

* Check if a port is opened:

> * **`nc -zv HOST PORT#`**
>   * **-z** performs a port scan against a given host and port or port range.

> ```bash
> $ nc -zv my.ine.com 443 
> DNS fwd/rev mismatch: my.ine.com != server-143-204-98-103.fra50.r.cloudfront.net
> DNS fwd/rev mismatch: my.ine.com != server-143-204-98-77.fra50.r.cloudfront.net
> DNS fwd/rev mismatch: my.ine.com != server-143-204-98-59.fra50.r.cloudfront.net
> DNS fwd/rev mismatch: my.ine.com != server-143-204-98-6.fra50.r.cloudfront.net
> my.ine.com [143.204.98.103] 443 (https) open	# Port 443 open
> ```

### Burp Suite <a href="#burp-suite" id="burp-suite"></a>

**`Burp Suite`** is an integrated platform and graphical tool for performing security testing of web apps, by testing, mapping and analyze the application's attack surface.

* it is written in Java, developed by [PortSwigger](https://portswigger.net/).
* can be used as proxy server, scanner, intruder, repeater, spider, decoder, comparer, sequencer, extender and more.

Using the _**Repeater**_ tool:

* Example of a GET request on port 80:

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-a4e91d2b788de311ca0067465f3e39eb94116a89%252Fimage-20220412002111740.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=ca32833e&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* Example of a _301 Moved Permanently_ response and redirection:
  * 301 response received:

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-e25a9adcf1915645cefda419172c629f1881bb1c%252Fimage-20220412002857935.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=d280b23e&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* By clicking the **`Follow redirection`** button, it follows the redirection (to the _Location_) in the current response without manually modifying the request to the target domain. I this case it got redirected to the HTTPS page:

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-848baa73b6416dfe41a26accdd58312e1fb8c8e0%252Fimage-20220412003409074.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=4fa2b1c0&#x26;sv=1" alt=""><figcaption></figcaption></figure>

### OpenSSL <a href="#openssl" id="openssl"></a>

**`OpenSSL`** is a full-featured toolkit for general-purpose cryptography and secure communication, a widely used implementation of the **T**ransport **L**ayer **S**ecurity (**TLS**) protocol.

> 📕 Check the [OpenSSL Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/) by Ivan Ristić.

* Establish a HTTPS connection using the _**s\_client**_ subcommand:
  * _the SSL handshake is only done at the beginning of the session._

> * **`openssl s_client -connect my.ine.com:443`**

```bash
$ openssl s_client -connect my.ine.com:443

CONNECTED(00000003)
depth=2 C = US, O = Amazon, CN = Amazon Root CA 1
verify return:1
depth=1 C = US, O = Amazon, OU = Server CA 1B, CN = Amazon
verify return:1
depth=0 CN = *.ine.com
verify return:1
---				# Certificate info exchanged
Certificate chain
 0 s:CN = *.ine.com
   i:C = US, O = Amazon, OU = Server CA 1B, CN = Amazon
 1 s:C = US, O = Amazon, OU = Server CA 1B, CN = Amazon
   i:C = US, O = Amazon, CN = Amazon Root CA 1
 2 s:C = US, O = Amazon, CN = Amazon Root CA 1
   i:C = US, ST = Arizona, L = Scottsdale, O = "Starfield Technologies, Inc.", CN = Starfield Services Root Certificate Authority - G2
 3 s:C = US, ST = Arizona, L = Scottsdale, O = "Starfield Technologies, Inc.", CN = Starfield Services Root Certificate Authority - G2
   i:C = US, O = "Starfield Technologies, Inc.", OU = Starfield Class 2 Certification Authority
---
Server certificate
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
subject=CN = *.ine.com

issuer=C = US, O = Amazon, OU = Server CA 1B, CN = Amazon

---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: RSA-PSS
Server Temp Key: X25519, 253 bits
---				# Client-server handshake
SSL handshake has read 5463 bytes and written 376 bytes
Verification: OK
---
New, TLSv1.3, Cipher is TLS_AES_128_GCM_SHA256
Server public key is 2048 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 0 (ok)
---
```

* **`openssl s_client -connect HOTS:PORT -debug`** - to analyze the handshake and the encrypted communication.
* **`openssl s_client -connect HOTS:PORT -state`** - to print the state of the handshake.
* **`openssl s_client -connect HOTS:PORT -quiet`** - no s\_client output.

> ```
> $ openssl s_client -connect my.ine.com:443 -quiet
> depth=2 C = US, O = Amazon, CN = Amazon Root CA 1
> verify return:1
> depth=1 C = US, O = Amazon, OU = Server CA 1B, CN = Amazon
> verify return:1
> depth=0 CN = *.ine.com
> verify return:1
> GET / HTTP/1.1		# GET Request manually sent, same as with nc
> Host: my.ine.com
>
> HTTP/1.1 200 OK		# Server Response
> Content-Type: text/html
> Content-Length: 6656
> Connection: keep-alive
> Date: Tue, 12 Apr 2022 16:50:43 GMT
> Last-Modified: Mon, 11 Apr 2022 15:29:44 GMT
> ETag: "2de37bd7e8c98aff5172792c663c4881"
> Cache-Control: no-store
> x-amz-version-id: rob8do2yVAbhoKDnnTj.UcTtzRk.DTov
> Accept-Ranges: bytes
> Server: AmazonS3
> Strict-Transport-Security: max-age=31536000; includeSubdomains; preload
> Content-Security-Policy: frame-ancestors 'none';
> X-Content-Type-Options: nosniff
> X-XSS-Protection: 1; mode=block
> Referrer-Policy: same-origin
> Vary: Accept-Encoding
> X-Cache: Miss from cloudfront
> Via: 1.1 39070ec61414daba1536aa06ac19ebb0.cloudfront.net (CloudFront)
> X-Amz-Cf-Pop: MUC51-C1
> X-Amz-Cf-Id: X4re8iPqni_Dsa8p6gIiFWtCCiMWPJ1HS81oxdWJlHo1uDvDRm0ziQ==
>
> <!DOCTYPE html><html lang=en><head>
> ...
> </body></html>
> ```

* In Burp Suite, HTTPS can be flagged to be used in the _Repeater target configuration_:
  * no handshake/encryption shown
  * focus on analyzing the request

![](https://blog.syselement.com/\~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-72f548ee46402b1187b97a6af231caa0fdfba775%252Fimage-20220412190752010.png%3Falt%3Dmedia\&width=768\&dpr=4\&quality=100\&sign=501b0f74\&sv=1)![](https://blog.syselement.com/\~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-30a873ac6cff9a031880f9bbe2b7ae7671baaa5a%252Fimage-20220412191055150.png%3Falt%3Dmedia\&width=768\&dpr=4\&quality=100\&sign=9c4a9c96\&sv=1)

> 📌 More on Burp Suite in the [PortSwigger Documentation](https://portswigger.net/burp/documentation/desktop/getting-started)

***

## HTTP Cookies <a href="#http-cookies" id="http-cookies"></a>

> ⚡ P.T. Usage:
>
> * Cookies are foundation of authorization of many applications
> * Lots of exploits rely on stealing cookies

To make HTTP (stateless) work like a **stateful** protocol, cookies were invented by Netscape (in 1994).

* **`Cookies`** are stored in the **cookie jar** of the web browser, a storage space used by the browser.
* To set a cookie, the server must use the `Set-Cookie` HTTP header field in the response message. The cookie will be sent from the server to the user agent:

```bash
HTTP/1.1 200 OK
Date: Thu, 14 Apr 2022 18:30:56 GMT
Content-Length: 0
Connection: close
Cache-Control: no-cache
Set-Cookie: obuid=9f0fec5b-0f7e-49fb-a11a-3f2ae3592dd6; Max-Age=7776000; Expires=Wed, 13 Jul 2022 18:30:56 GMT; Path=/; Domain=.outbrain.com;SameSite=None;Secure
Set-Cookie: adrl=Nzg1YzVjMzMwOTZkYmIyNzljYjMzZWRiYTA4OTkwZTY; Max-Age=1728000; Expires=Wed, 04 May 2022 18:30:56 GMT; Path=/; Domain=.outbrain.com;SameSite=None;Secure
X-TraceId: d009bd334ba3d13cf0f7c47929b642fa
```

### Cookies Format <a href="#cookies-format" id="cookies-format"></a>

Cookies contain the following attributes:

| **Attribute**                             | **Value**                      |
| ----------------------------------------- | ------------------------------ |
| Cookie content - KEY=Value                | `Cookie-NameID=<Cookie-Value>` |
| Expiration date - validity time window    | `Expires=<date>`               |
| Max-Age - Seconds until cookie expiration | `Max-Age=<number_in_seconds>`  |
| Path - in the requested URL               | `Path=<path-value>`            |
| Domain - Host to send the cookie to       | `Domain=<domain-value>`        |
| Optional Flags                            | `Secure; HttpOnly; SameSite;`  |

* Cookies are sent only to the valid domain/path, according to their expiration date and their flags, and browsers use these attributes to choose to send a cookie in a request or not.

### Cookies Domain & Path <a href="#cookies-domain-and-path" id="cookies-domain-and-path"></a>

* The **scope** of the cookie is set by the `Domain` and `Path` fields, so the browser send the cookie only to the right domain.
* After a web server sets the domain field via a cookie, the browser _will use the cookie for every request sent to that domain and all its subdomains_.
  * If domain attribute is not specified, the browser will automatically _set the domain as the server domain_ and set the **`host-only`** flag. The cookie will be _sent only the that precise hostname_.
* The browser will send a cookie to the right domain _and to any subpath of the **path field value**_.

### Other Attributes <a href="#other-attributes" id="other-attributes"></a>

* **Expired** cookies are not sent to the server and session cookies expire with the HTTP session.
* **`HttpOnly`** flag forbids JavaScript from accessing the cookie. It prevents any non-HTML technology (JavaScript, Flash, Java ...) from reading the cookie, mitigating _cross-site scripting attacks_ (cookie stealing via **XSS**).
* **`Secure`** flag creates secure cookies sent only over an HTTP**S** connection. It's more resistant to _man-in-the-middle attacks_ (**MITM**).
* A server can set **multiple values** (key=value) with a single Set-Cookie header.

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-04f599c5e7c53e4eb28e5851864fbe977f8733a4%252Fimage-20220416113822221.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=8a7192a1&#x26;sv=1" alt=""><figcaption><p>Cookie-Based Authentication - image by chameeradulanga.medium.com</p></figcaption></figure>

> 📌 For more in depth info on cookies format and usage, refer to the [RFC 6265 - HTTP State Management Mechanism](https://datatracker.ietf.org/doc/html/rfc6265).

***

### Sessions <a href="#sessions" id="sessions"></a>

Websites can also store information (variables specific for a given visit) on the **server side** instead of the client side, by using **sessions**.

* Each user session is identified by a **`session id`** or **`token`** assigned by the server to the client, which presents this ID for each subsequent request.
  * By that session ID, the server retrieves the state of the client and its associated variables.
  * Session ID can be stored as a cookie, form field or URL.
  * It is a unique number used to identify a logged in user session.
* Application logic is hidden and the cost of cookies data transmission is reduced.

#### Session Cookies <a href="#session-cookies" id="session-cookies"></a>

Session IDs are installed on the web browser by using **session cookies**.

* Each development language has its own default session parameter name: `PHPSESSID`, `JSESSIONID`, custom-names, etc.
* Session IDs are also transmitted via `GET` requests.

Browser activities generating session cookies are:

* logging in
* opening a specific web page
* change settings in the web application

> 📌 For more in depth information check the [Beginner Guide to Understand Cookies and Session Management](https://www.hackingarticles.in/beginner-guide-understand-cookies-session-management/) article.

A web developer tool can be used to analyze web applications, examine, edit, and debug HTML, CSS, and JavaScript, inspect and manipulate Cookies.

* Firefox has a built in set of web developer tools - [Firefox DevTools](https://firefox-source-docs.mozilla.org/devtools-user/index.html).

### Same Origin Policy (SOP) <a href="#same-origin-policy-sop" id="same-origin-policy-sop"></a>

**`SOP`** (**S**ame **O**rigin **P**olicy) is a web browser security mechanism that restricts scripts on one origin from accessing data from another origin. For example, it _prevents_ JavaScript code from getting or setting properties on a resource _coming from a different origin_.

* Web application security is entirely based on Same Origin Policy.
* _**Domain**_ (hostname), _**port**_ and _**protocol**_ must match to determine if JavaScript can access a resource.
* SOP applies to the actual code of a script.
* SOP allows embedding of external resources with HTML tags like `img`, `script`, `iframe`, `video`, etc. Any JS on the page won't be able to read the contents of these resources.
* There some exceptions to the same-origin policy (`location`, `length`, `replace`, others).
* Cookies are often accessible from all subdomains of a site (the is technically a different origin) - check the `HttpOnly` flag.

> 📌 [PortSwigger Academy SOP Explanation](https://portswigger.net/web-security/cors/same-origin-policy)

***

## Burp Suite Tool <a href="#burp-suite-tool" id="burp-suite-tool"></a>

> ⚡ P.T. Usage:
>
> * Web application behavior study and analysis
> * Attacks, finding and test vulnerabilities

**Burp Suite** is an **`intercepting proxy`**, a tool that lets the user _analyze and modify any request and any response_ exchanged between an HTTP client and server.

* Another widely used intercepting tool is OWASP **Z**ed **A**ttack **P**roxy (**ZAP**).
* Intercepting proxy is NOT a web proxy. Proxy servers provides a gateway (a layer of security as web filters, firewalls) between end-users and the web pages they visit online.

Burp Suite will let a user:

* Intercept requests and responses between the browser and the web server
  * Requests can be intercepted and modified before they are sent to the remote server
* Build requests manually
  * Header and body of a message can be manually or automatically altered
* Crawl a website automatically
* Fuzz web apps with valid/invalid inputs to test their behavior

![](https://blog.syselement.com/\~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-c747876a1569470a1765e61a3d45c6867f606817%252Fimage-20220423101209401.png%3Falt%3Dmedia\&width=768\&dpr=4\&quality=100\&sign=6e2dd523\&sv=1)

### Burp Suite - Tools <a href="#burp-suite-tools" id="burp-suite-tools"></a>

* **Proxy** - operates as a web proxy server that let the user _view, intercept, inspect and modify the raw traffic passing in both directions_. Operates in conjunction with Burp's browser (or with external browser with [Burp's CA certificate](https://portswigger.net/burp/documentation/desktop/external-browser-config/certificate) and an addon like [FoxyProxy](https://addons.mozilla.org/it/firefox/addon/foxyproxy-standard/)).

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-4f5a12fbdf257c199ceeafb9660099d448f24ca3%252Fimage-20220423102403009.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=2587a931&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* Intercept and modify headers in the _Raw_ tab or in the _Headers_ tab of the Inspector tool.

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-739e5049c85edd0fdeddc21d3d8cda575dbdd12b%252Fimage-20220423103354656.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=973aadcf&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* Even with interception off, Burp will still collect info on the traffic. This can be checked in the _**Proxy - History** tab_ or in the _**Target - Site Map**_ tab.

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-302d3ce7b1088d04c561dda66e4ac67b118adb74%252Fimage-20220423103745939.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=fab605d0&#x26;sv=1" alt=""><figcaption></figcaption></figure>

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-540f06b292391d39dcd66ea5024ff452aefdf573%252Fimage-20220423103834515.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=b46c3c36&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* **Repeater** - used for manually manipulating and reissuing raw HTTP and WebSocket messages, in order to analyze the application's response.
  * Provides syntax highlighting, raw and rendered responses, integration with other Burp tools.
  * `netcat` or `telnet` can be used to do the same thing too, without the above benefits.
  * Proxy can intercept a request and send it to the Repeater function (**`CTRL+R`**):

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-3134d4701dc195e526d41e2c43fec514c638d90b%252Fimage-20220423111559091.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=aa42828d&#x26;sv=1" alt=""><figcaption></figcaption></figure>

> 📌 For a better overview of all the Burp Suite Tools, check the official documentation [here](https://portswigger.net/burp/documentation/desktop/tools).
>
> 🔬 Check [_Burp Suite Basics - Directory Enumeration_](https://blog.syselement.com/ine/courses/ejpt/exam-preparation-labs/p.t.-prerequisites-labs/burp-suite-basics) lab.

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-b6cb2fbd4436a5ef3cd6f8b254feae173f94cf19%252Fimage-20220423105251006.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=5f4878ed&#x26;sv=1" alt=""><figcaption><p>Burp Suite tools - image by portswigger.net</p></figcaption></figure>
