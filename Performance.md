# Performance
Like security, performance requires trades off and sacrifices.
This may mean writing code in a less popular way.
Performance certainly means testing and measuring things otherwise not tested or measured.
Also like security everybody claims to want performance, but rarely are they willing to work to achieve it.

Generally technical discussions of performance imply execution performance, or more specifically time to execute specific measured tasks.
From a managerial or product perspective performance can cover many things from execution time to hours of effort for a human to complete a given task.
While discussions of performance may cover many different topics all discussions of performance contain one important consideration: measures.
Measures ensure performance discussions contain objectivity, reproducibility, and veracity.

## Premature Optimization
> We should forget about small efficiencies, say about 97% of the time: premature optimization is the root of all evil. [Knuth, 1974]

I felt it necessary to include this quote because it is so widely misunderstood and used out of context for developers to qualify performance penalties they don't understand.
This section of an old academic paper speaks specifically to *Efficiency* and is subtitled as such.
In this case efficiency refers to effective use of developer time writing code opposed to software execution time.
More specifically this section directly describes performance penalties that aren't yet known at the time of writing.
When premature optimization is mention it directly describes carefully writing code to avoid these unknown states.
Once performance is measured no longer is it unknown, and thus no longer premature because the developer doesn't have to guess at what the correct course of action is.
Even still the paragraph of the article provides exceptions where such guessing, if well reasoned, is still valuable:

> mature optimization is the root of all evil.
> Yet we should not pass up our opportunities in that critical 3%.
> A good programmer will not be lulled into complacency by such reasoning, he will be wise to look carefully at the critical code; but only after that code has been identified.
> It is often a mistake to make a priori judgments about what parts of a program are really critical, since the universal experience of programmers who have been using measurement tools has been that their intuitive guesses fail.
> After working with such tools for seven years, I've become convinced that all compilers written from now on should be designed to provide all programmers with feedback indicating what parts of their programs are costing the most; indeed, this feedback should be supplied automatically unless it has been specificMly turned off.

### Summary
*Premature optimization* is **only** about guessing at performance of non-critical code.

---

## DOM Access
I put together a small [micro-benchmark](https://jsbench.github.io/#b39045cacae8d8c4a3ec044e538533dc) to measure and compare various different means of accessing the [DOM].
Looking at the numbers a few things stand out.

* The old static DOM methods that do not require string parsing are several orders of magnitude faster than other means of access.
* Query selectors are the fastest method of accessing the DOM only when there is not a static method equivalent, such as access elements by attribute name or value.
* Custom DOM methods are the slowest means of accessing the DOM.
* Perhaps most interesting is that Chrome appears to have a performance ceiling of about 45 million operations per second irrespective of hardware.  Firefox has no such performance ceiling.  In fact, after measuring these numbers on various different hardware it appears the maximum performance numbers for Firefox are a direct reflection of memory speed and bus speed irrespective of CPU speed or core count.

The most important consideration from any such micro-benchmark are not the raw numbers, but the differences between those numbers.
Achieving more than 4 billion operations per second in Firefox DOM access sounds impressive, but more important is that its about half a million times faster than access by equivalent query selectors.
Keep in mind all measures returned in this micro-benchmark represent base-case scenarios, which means the differences between the numbers will increase proportionally to frequency of access.
That means if a query selector executes at 25,000 operations per second and 1,000 query selectors execute in a given page total performance for DOM access via query selector is reduced to 25 operations per second as the most optimistic measure.
If, on the other hand a different but equivalent means of DOM access executes at 45 million operations per second and there are 1,000 such points of access in a given page total performance for DOM access is reduced to 45,000 operations per second as compared to 25 operations per second for the query selector scenario.

These differences in measure are more important for execution time than many developers consider because the quantity of DOM access points is often substantially higher than most developers are aware.
The lack of awareness flows from the distance developers remove themselves from the DOM via layers of abstractions.
More so, when developers are far removed from what actually occurs at the DOM API there may likely be a greater quantity of access points, inefficiency, than is necessary to achieve a desired result.

Second and third order consequences to performance exist for each point of DOM access.
DOM modifications frequently result in partial or full visual rendering changes to the page each of which imposes a performance penalty.
Furthermore code dependent upon DOM modification cannot execute until the DOM is modified and increasing JavaScript execution speed without consideration for speed of access to the DOM may result in race conditions or intermittent blocking of execution.

### Summary
If user interface performance is critical to product quality it is necessary to understand the performance penalties associated with DOM access by comparing numbers.
Failure to measure and compare performance differences will likely result in a sluggish experience even though the technologies are capable of executing extremely quickly, because the potential performance implications are several orders of magnitude.

---

## File System Access
Like the DOM the file system is a tree model.
This means many of the same performance rules apply.
My personal project of the moment [Share File Systems](https://github.com/prettydiff/share-file-systems) creates an OS GUI in the browser that connects to a Node.js microservice application running on the terminal.
This application displays file system details in the browser similar to OSX Finder or Windows Explorer.
The raw file system display performs a bit more slowly than the equivalent interfaces natively supplied by the operating system.
Despite that my Share File Systems application performs file system search dramatically faster than the operating system with better results.

I don't know how the operating system code works.
Because of that ignorance I cannot definitively determine why file search in the operating system is so astonishingly slower than my Share File Systems application.
From my knowledge of tree models my assumption is that any query that requires string parsing to determine a qualified result will be several orders of magnitude slower.
Operating systems support a variety of conditions like [Advanced Query Search](https://docs.microsoft.com/en-us/windows/win32/lwef/-search-2x-wds-aqsreference), [Windows Search](https://support.microsoft.com/en-us/windows/search-for-anything-anywhere-b14cc5bf-c92a-1e73-ea18-2845891e6cc8), and [Finder Search](https://support.apple.com/guide/mac-help/narrow-search-results-mh15155/mac).
Each of those search methods support a variety of advanced syntax rules, such as wildcards, that require parsing against a rule set and it is the parsed result that is compared with a file system artifact to determine a search result.

To make things more complicated the operating systems know file search is slow so they support caching.
Caching is a trade off.
Caching requires a one time performance hit to write results into a remote location so that subsequent identical searches may refer to this cache to quickly display results.
Caching is problematic since file systems are living entities that can change under a variety of conditions, which will invalidate any previous cache.

The Share File Systems approach to file system search is primitive.
There is no cache.
At each search the file system is walked recursively as if preparing a list of all file system contents of all contained directories.
Only the file system artifacts that match the search criteria are populated into output.
Likewise, the search token is also primitive.
The Share File Systems application only supports string tokens, negative strings, and regular expressions for search.
The string token and negative string tokens do not require any parsing to perform a comparison.
The regular expression requires a one time parse step and all file system artifacts are tested against that parsed regular expression.

### Summary
I am able to achieve dramatically faster file system search using a JavaScript application from the browser.
I suspect the performance improvements and better results are the result of doing less work.

---

## Transmission
Web services typical refer to the transactions of data across a network between web technology, typically a browser as the client and a remote application running on a web server.
The common transmission standard for web technologies is HTTP 1.1. [R Fielding, et al]
Transmission schemes available are typically dictated by support in the browser and also include:
* HTTP/2 [M Belshe, et al]
* HTTP/3 (QUIK) [M Bishop, et al]
* WebSockets [T Fette, et al]
* Server-Sent Events (SSE) [HTML5 Specification]

Each of these 5 transmission schemes possess different strengths and weaknesses that impact performance.

### HTTP 1.1
The common HTTP scheme is the most portable and durable transmission scheme listed because its fully session-less.
In networking session means that two or more end points agree to connect and share data.
In a fully session-less scheme no agreement is required and no identity is implied.
This means an anonymous client is connecting to a known server without authentication and the server will accept that connection if it can understand it.

### HTTP/2
HTTP/2 offers substantial speed improvements over HTTP 1.1, but sacrifices portability to do so.
HTTP/2 requires use of TLS, which requires signed certificates to establish trust.
It should be noted the TLS requirement for HTTP/2 is a de facto browser standard not defined in the HTTP/2 protocol.

HTTP/2 is also is partly session-oriented.
Anonymous clients connect to a server without preconditions using an HTTP request just as with HTTP 1.1, but upon connecting the server establishes a TCP tunnel to the client whereby many resources are pushed to the client.

HTTP/2 introduces a new technology called [HTTP Server Push](https://en.wikipedia.org/wiki/HTTP/2_Server_Push).
HTTP/2 is a performance improvement in that multiple resources may be pushed into a single HTTP response.
HTTP push cannot be initiated by a client and it is not a notification system outside a response to a request.

HTTP/2 and HTTP/3 achieve the highest throughput of web transfer mechanisms by separating sockets from sessions.
A single session is established between a client and a server, but each session may have as many sockets as the server has ports available.
Numerous simultaneous sockets allows HTTP/2 and HTTP/3 to achieve a higher quantity of simultaneous transmissions than HTTP 1.1 allows.

### HTTP/3
HTTP/3 uses a new technology called QUIK.
HTTP/3 follows a similar architecture to HTTP/2.
HTTP/3 occurs over [UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol) as opposed to all other web transfer mechanisms that occur over [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol).
TCP has built-in integrity checks, commonly referred to as *connection-oriented*, such as: the 3-way handshake, packet ordering, and so forth.
UDP is a faster transmission mechanism because it has no integrity checks of any kind, referred to as *connection-less*.
The common traditional use case for UDP is live media streaming where dropped packets are unrecoverable lost data.
Using UDP as a file transmission mechanism requires an alternate form of integrity check to determine if data is corrupt or incomplete so that it may be resent until complete.

### WebSockets
Unlike the various levels of HTTP, WebSockets are fully session-oriented.
This means a session must be established before any data is sent in either direction.
As a result WebSockets, when executed from the browser, require the use of a HTTP server on the remote end as well as a WebSocket server.
The requirement for an HTTP server when connecting outside a browser is determined by the WebSocket server implementation.
Most server implementations lean on HTTP servers, but I have written a WebSocket service that makes no use of HTTP servers, so it is possible.

Unlike HTTP/2 and HTTP/3 WebSockets do not distinguish between session and socket.
The WebSocket protocol also indicates data frames may not intermingle between separate transmission.
That means WebSockets cannot support multiple simultaneous transmission.

My personal experience with WebSockets indicates if two data transfers are executed from within the same function a race condition occurs that determines which data will send, or both, or neither.
There is close to a 100% chance the first transmission executed will make it to the remote end.
There is about a 50% chance the second transmission executed will make it to the remote end.
There is close to a 0% chance neither transmission will make it to the remote end.
Once I discovered this problem I solved it in my application by ensuring the least priority transmission is delayed with a *setTimeout* of 5ms.

WebSockets are the only web transmission mechanism to achieve full bi-directional communication, also referred to as [full duplex](https://en.wikipedia.org/wiki/Duplex_(telecommunications)#Full_duplex).
This means clients may arbitrarily send data to the server and the server may arbitrarily send data to the client.
It also means WebSockets are a simplified and faster alternative to the browser's [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) object, sometimes referred to as AJAX.

Aside from the limitations mentioned above regarding no parallel transmissions, WebSockets achieve the fastest communication between nodes.
WebSockets do not have a separate mechanism for meta data unlike HTTP, which has headers.
WebSockets also do not have the request/response round trip of HTTP.
WebSockets only have a *send* operation.

### Server-Sent Events
Server-Sent Events allow for an arbitrary notification system without a full roundtrip like WebSockets, but only from a webserver.
Server-Sent Events is limited in the number of simultaneous transfers as determined by the server's available ports and the version of HTTP service it is executing.

Server-Sent Events are similar in performance and handling to WebSockets.
WebSockets are a better choice if the client is frequently sending data to the server, but Server-Sent Events are a better choice if the server is executing a notification service without any messaging from the client.

### Summary
Determining the best performing transmission type is a matter of determining your application needs whether your primary needs are parallel transfers, 

---

## Services
Achieving high performance services requires an understanding of available transmission options as well as their limitations and overhead.
Service application speed and resource organization also dictate service performance.
Even when all the ingredients that comprise a service architecture are fast there there still exists low hanging fruit to improve performance.

At the large-scale commercial/enterprise level service speed is secondary to availability.
Service speed becomes irrelevant when the server fails.
The primary solutions to ensure service uptime are redundancy and distribution, which are generally solved by load-balancers and content distribution networks respectively.

A load balancer is a traffic distribution machine sitting between incoming traffic and a pool of available servers.
Load balancing ensures traffic is directed away from machines with low resource availability and directed towards machines with high resource availability.
Resource availability refers to hardware usage versus total hardware capability, such as: memory, CPU speed, disk write speed, disk capacity, and so forth.
This means load balancers must be aware of incoming traffic payload size, various server resource availability metrics, and various other variables in near real time.

In order to get the greatest benefit of a load balancer perform research to determine which incoming service messages associate with levels of resource consumption.
That sort of research requires normalizing many variables as necessary to focus upon the fewest key considerations to achieve research with higher levels of confidence.
Normalizing service data from any kind of parsing scheme may result in the greatest level of noise reduction.
A way to achieve this form of normalization requires wrapping all incoming data in a primitive object that maps the data to a service identifier, for example:

```javascript
{
    "data": {},
    "service": "name_of_service_here"
}
```

In this code example the data property represents the incoming data of any shape or size.
The service property refers to the name of the service expected to consume that data.
In most cases services consume data of a uniform expected shape, which in TypeScript may be defined as an *interface*.
Knowing this solves several problems immediately:

1. A single entry point can consume all incoming data expecting only a single identifier and no additional metadata.
1. The primitive nature of the single identifier allows service identification at the load balancer.
1. Service identification before distribution by the load balancer allows the developers the opportunity to identify heuristics to connect specific service data with a pool of specifically designed hardware resources.

Orchestrating service management in this way also speeds developer maintenance time, because there is only a single funnel at each connection point to troubleshoot connectivity and service health.

---

## References
* [Knuth, 1974](https://pic.plover.com/knuth-GOTO.pdf)
* [DOM](https://dom.spec.whatwg.org/)
* [R Fielding, et al](https://datatracker.ietf.org/doc/html/rfc2616)
* [M Belshe, et al](https://datatracker.ietf.org/doc/html/rfc7540)
* [M Bishop, et al](https://quicwg.org/base-drafts/draft-ietf-quic-http.html)
* [T Fette, et al](https://datatracker.ietf.org/doc/html/rfc6455)
* [HTML5](https://html.spec.whatwg.org/multipage/server-sent-events.html#server-sent-events)

[Knuth, 1974]: https://pic.plover.com/knuth-GOTO.pdf
[DOM]: https://dom.spec.whatwg.org/
[R Fielding, et al]: https://datatracker.ietf.org/doc/html/rfc2616
[M Belshe, et al]: https://datatracker.ietf.org/doc/html/rfc7540
[M Bishop, et al]: https://quicwg.org/base-drafts/draft-ietf-quic-http.html
[T Fette, et al]: https://datatracker.ietf.org/doc/html/rfc6455
[HTML5 Specification]: https://html.spec.whatwg.org/multipage/server-sent-events.html#server-sent-events