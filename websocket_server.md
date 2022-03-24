# Write a WebSocket Server

## Introduction

### Preamble
The provided code samples come from [Share File Systems](https://github.com/prettydiff/share-file-systems) and may be used in conformance with that project's license: AGPLv3.
For full license text see the completed code at the bottom.

The goal of this document is to provide a step-by-step walk through of writing a WebSocket server application for Node.js.
The example code provided will feature both a WebSocket server and conformance to the WebSocket [client specification](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket).

This guide requires a basic knowledge of TypeScript and Node.js.
No other dependencies, conventions, or tools will be mentioned.
This guide makes use of ECMAScript Module conventions opposed to the older CommonJS *require* convention.

### What are WebSockets?
The WebSocket protocol is a common API for a TCP stream as defined by [RFC 6455](https://datatracker.ietf.org/doc/html/rfc6455) with an interface defined by [WHATWG HTML Specification](https://html.spec.whatwg.org/multipage/web-sockets.html#the-websocket-interface).
WebSockets provide a readable and writable network stream to web browsers.
That solves two problems not solved by HTTP:

1. Provides a stream interface for continuous transmit of data.  This is helpful in the case of binary media such as video and audio where the media source is the browser but intended for distribution across a network.
2. Provides a means to push data into a web browser without requests.  This is helpful to receive information updates in real time without polling a server via unnecessary HTTP requests.

Since the WebSocket protocol is just a primitive TCP stream interface it unlocks the same client capabilities to conveniently stream data across a network in addition to acting as a server for browser-based applications.

## Getting started
Start by creating a new document in your favorite code editor and importing the required Node references.
This new document will serve as a stand-alone service library.

### Build type definitions
In a TypeScript type definition file include the following code.
TypeScript type definition files have file extension: **.d.ts**.

```typescript
// yourTypes.d.ts
import { Server, Socket } from "net";
declare global {
    interface socketClient extends Socket {
        closeFlag: boolean;
        fragment: Buffer[];
        opcode: number;
        sessionId: string;
    }
    interface socketFrame {
        fin: boolean;
        rsv1: string;
        rsv2: string;
        rsv3: string;
        opcode: number;
        mask: boolean;
        len: number;
        maskKey: Buffer;
        payload: Buffer;
        startByte: number;
    }
    interface websocket {
        broadcast: (data:Buffer|string) => void;
        clientList: socketClient[];
        send: (socket:socketClient, data:Buffer|string) => void;
        server: (config:websocketServer) => Server;
    }
    interface websocketServer {
        address: string;
        callback: (port:number) => void;
        cert: {
            cert: string;
            key: string;
        };
        port: number;
    }
}
```

### Import the required types into your library file
This the ingredients we will need to ensure 100% type coverage.

```typescript
import { createHash, Hash } from "crypto";
import { AddressInfo, createServer as netServer, Server } from "net";
import { createServer as tlsServer } from "tls";
```

The import statements import only what we need.
We import the `createServer` types from both the *net* and *tls* Node libraries and then alias them to avoid a naming conflict.
We also import the `AddressInfo` and `Server` types from *net* as well, which we will use later.

The *crypto* library will be used for the connection handshake further down.

## Create a library file
The first thing to do in the code is to create library file that will contain all our server code. 

```typescript
const websocket:websocket = {
    broadcast: function (data:Buffer|string):void {},
    clientList: socketClient[],
    send: function (socket:socketClient, data:Buffer|string):void {},
    server: function (config:websocketServer):Server {}
};

export default websocket;
```

This code represents the outline of our library.
* Method *broadcast* will allow sending a message to all open socket clients.
* List *clientList* stores each connected client socket.
* Object *convert* stores two tiny methods for converting decimal to a binary string and back.
* Method *send* builds a websocket data frame and sends a payload on a specified socket.
* Method *server* creates a websocket server listening on a specified port.

## Create the server
The server needs to support both secure and insecure WebSocket services.
A secure server requires a valid certificate, but otherwise places most of the connection burden on the client.

### About certificates
If the code will use a secure server you will need to pass in a TLS certificate whether from a trusted certificate authority or [self signed](https://www.linode.com/docs/guides/create-a-self-signed-tls-certificate/).
Node requires certificates in PEM format and comprised by the certificate itself and its corresponding private key.
At the time of this writing Node ships with OpenSSL to power its *crypto* library but does not expose an API for creating certificates which OpenSSL can do.
This means that certificates can be created with Node, but require direct access to the OpenSSL library as a child process, but beware certificate storage and access varies wildly by operating system.

If the secure server should perform authentication against the client certificate pass in `requestCert:true` to the TLS server options along with the certificate details.

Aside from the certificate the insecure and secure servers are otherwise identical in the remainder of the application code.  Our server logic will look like the following:

### Basic server code
```typescript
    server: function (config:websocketServer):Server {
        // create the server
        const handshake = function (socket:socketClient, data:string, callback:(key:string) => void):void {},
            dataHandler = function (socket:socketClient):void {},
            connection = function (socket:socketClient):void {
                const handshakeHandler = function (data:Buffer):void {
                    const handshakeCallback = function (key:string):void {

                        // modify the socket for use in the application
                        socket.closeFlag = false;          // closeFlag - whether the socket is (or about to be) closed, do not write
                        socket.fragment = [];              // storehouse of data received for a fragmented data package
                        socket.opcode = 0;                 // stores opcode of fragmented data page (1 or 2), because additional fragmented frames have code 0 (continuity)
                        socket.sessionId = key;            // a unique identifier on which to identify and differential this socket from other client sockets
                        socket.setKeepAlive(true, 0);      // standard method to retain socket against timeouts from inactivity until a close frame comes in
                        websocket.clientList.push(socket); // push this socket into the list of socket clients

                        // change the listener to process data
                        socket.removeListener("data", terminal_commands_websocket_connection_handshakeHandler);
                        dataHandler(socket);
                    };
                    // handshake
                    handshake(socket, data.toString(), handshakeCallback);
                };
                socket.on("data", handshakeHandler);
                socket.on("error", function (errorItem:Error) {
                    if (socket.closeFlag === false) {
                        console.error(errorItem.toString());
                    }
                });
            },
            wsServer:Server = (config.cert === null)
                ? netServer()
                : tlsServer({
                    cert: config.cert.cert,
                    key: config.cert.key,
                    requestCert: true
                }, connection);

        // create the listener (activates the server)
        wsServer.listen({
            host: config.address,
            port: config.port
        }, function ():void {
            const addressInfo:AddressInfo = wsServer.address() as AddressInfo;
            config.callback(addressInfo.port);
        });
        // server is listening

        // assign an event handler to the "connection" event
        if (config.cert === null) {
            wsServer.on("connection", connection);
        }

        // returns the server object for external access
        return wsServer;
    }
```

There are a couple of things happening in the logic above.
1. We create a server assigned to variable *wsServer*.  The server launches in secure automatically if no certificate is provided in the passed in config object.
    * Please note the connection handler is passed in directly upon server creation for a secure server, but assigned to the "connection" event for the unsecure server.
2. We define two functions: *handshake* and *dataHandler*.  These two demand specific instruction further below.
3. We spawn a listener on our new server.  The callback provided to the passed in *config* object executes in the listener's callback.
4. We assign an event handler to the server's "connection" event listener.  This handler does two things:
   * Initiates the required *Handshake* event by listening on the socket's *data* event.
   * Provides some basic error handling.

The handshake function receives a callback that extends the socket for ease of management later.

### TLS Connection Explanation
As noted above the "connection" event handler is assigned in different ways depending whether the server is secure.
A TLS server receives two connection events: "connection" and "secureConnection".
The TLS server inherits the "connection" event from `net.createServer` method, but it receives no events.
The "secureConnection" event replaces the "connection" event after secure connection establishment, which is an additional step not represent on the unsecure server.
Since both servers have a "connection" event, but the unsecure server does not have a "secureConnection" event and that event is not immediately available to the secure server these servers must receive the event handler in different ways.

## Handshake
RFC 6455 defines the handshake in section 4, but I find the [MDN reference](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers) a clearer explanation.

In order for a client to initiate access to a WebSocket server it sends an HTTP request with a few HTTP headers:

```
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Cache-Control: no-cache
Connection: Upgrade
Host: localhost:81
Origin: http://localhost
Pragma: no-cache
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
Sec-WebSocket-Key: XLv1J5/+Jp2lsQep9ayoMg==
Sec-WebSocket-Version: 13
Upgrade: websocket
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/93.0.4577.63 Safari/537.36
```

This is a typical request taken from my own browser.
The only thing we need in that is the `Sec-WebSocket-Key`, which is randomly generated by the browser.
We will perform this logic in the *handshake* function.
Here is an example of such a function at its most minimal:

```typescript
    handshake = function (socket:socketClient, data:string, callback:(key:string) => void):void {
        const headers:string[] = data.split("\r\n"),
            output:string[] = [],
            hash:Hash = createHash("sha1");
        let a:number = headers.length,
            key:string = "";
        do {
            a = a - 1;
            if (headers[a].indexOf("Sec-WebSocket-Key") > -1) {
                // the search for whitespace is mostly unnecessary,
                // but this is thorough because network devices can mutilate data across the wire
                key = headers[a].replace(/\s*/g, "").replace("Sec-WebSocket-Key:", "");
                break;
            }
        } while (a > 0);
        createHash();
        output.push("HTTP/1.1 101 Switching Protocols");
        output.push("Upgrade: websocket");
        output.push("Connection: Upgrade");
        output.push(`Sec-WebSocket-Accept: ${hash.update(key + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11").digest("base64")}`);
        output.push("");
        socket.write(output.join("\r\n"));
        callback(key);
    }
```

1. We split the data block by line.  HTTP headers are one per line with CRLF character sequence as the line terminator.
2. We loop through the headers until we find the one named *Sec-WebSocket-Key*.
3. We extract the key value and break the loop.
4. The algorithm to modify the key is:
   1. Concatenate the key with magic string *258EAFA5-E914-47DA-95CA-C5AB0DC85B11*.
   2. Hash that new string with SHA-1 hash algorithm.
   3. Output the hash as base64.  The output digest must be from the hash computation directly.  Converting a different format to base64 will not work.
5. Write the output as HTTP headers for the response.
   * The output must be line terminated with sequence CRLF just like the input.
   * The output must terminate with an empty line.
6. Execute the passed in callback, which is the function *handshakeCallback* from the prior section's code example.  This callback also removes the *data* event handler.  We don't need that event handler anymore because the handshake is complete.

## Receiving data
Upon completion of the handshake the specified socket will receive binary frames that do not resemble HTTP headers.
These frames must be decoded before we can access the contained data as defined in RFC 6455, Section 5.

```typescript
   dataHandler = function (socket:socketClient):void {
       const processor = function (data:Buffer):void {};
       socket.on("data", processor);
   }
```

The above logic is written for portability within the library file.
The data processor needs access to the socket and this means of expression provides access to the socket by lexical scope without declaring additional references or constraining this logic to the body of the *handshakeCallback* function since it is no longer related to the handshake.

### Data gram definition
From RFC 6455, Section 5.2.
```
      0                   1                   2                   3
      0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
     +-+-+-+-+-------+-+-------------+-------------------------------+
     |F|R|R|R| opcode|M| Payload len |    Extended payload length    |
     |I|S|S|S|  (4)  |A|     (7)     |             (16/64)           |
     |N|V|V|V|       |S|             |   (if payload len==126/127)   |
     | |1|2|3|       |K|             |                               |
     +-+-+-+-+-------+-+-------------+ - - - - - - - - - - - - - - - +
     |     Extended payload length continued, if payload len == 127  |
     + - - - - - - - - - - - - - - - +-------------------------------+
     |                               |Masking-key, if MASK set to 1  |
     +-------------------------------+-------------------------------+
     | Masking-key (continued)       |          Payload Data         |
     +-------------------------------- - - - - - - - - - - - - - - - +
     :                     Payload Data continued ...                :
     + - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - +
     |                     Payload Data continued ...                |
     +---------------------------------------------------------------+
```

Before we get to the code a few things much be clear:
* This data gram is bit encoded binary, so we have to work directly with bits and bytes from a buffer.

#### Byte 0
The first byte contains a few things:
* fin - This first bit, the *finish* bit, determines if the data is finished.
* rsv1/2/3 - The next three bits are optional flags for extension features that do not appear to be use by web browsers.
* opcode - The last 4 bits determine what kind of frame this is.  Read about additional opcode definitions in RFC 6455, Section 5.2.
   - 0 - A **continuation** represents a data fragment.
   - 1 - A **text** - data frame indicating all bytes of the payload are UTF8 characters and probably not includes invisible control characters.
   - 2 - A **binary** - data frame that is isn't only text.
   - 8 - A **close** - control frame that indicates the socket must close.
   - 9 - A **ping** - control frame used as a heartbeat.
   - 10 - A **pong** control frame used as a response to a ping.

#### Byte 1
Bit 0 contains the mask flag.
This bit will always be a *"1"* coming from the browser and should be on from all client messages.

The remaining 7 bits indicate payload length according to a formula:
* If the total payload size is **less than 126 bytes** the actual payload size is represented here.
* Payload sizes of 126 - 65535 bytes are represented here as value **126**.
* Payload sizes greater than 65535 bytes are represented here as value **127**.

#### Extended Payload Length
If the payload length from byte 1 is value 126 the next two bytes indicate the actual payload size.
If the payload length from byte 1 is 127 the next 8 bytes indicate the actual payload size.

#### Mask key
The mask key, 4 bytes, only occurs if the make flag from bit 0 of byte 1 is **"1"** and occurs after the extended payload length bytes whether there are 0, 2, or 8 of them.

#### Payload data
All remaining bytes are the actual payload data.

### Receiving data, the actual instructions
```typescript
    const processor = function (data:Buffer):void {
        if (data.length < 3) {
            return null;
        }
        if (buf !== null) {
            // a firefox defect workaround part 2
            data = Buffer.concat([buf, data]);
            buf = null;
        }

        // decode the frame header
        const toBin = function terminal_server_transmission_websocket_listener_processor_convertBin(input:number):string {
                return (input >>> 0).toString(2);
            },
            toDec = function terminal_server_transmission_websocket_listener_processor_convertDec(input:string):number {
                return parseInt(input, 2);
            },
            frame:socketFrame = (function terminal_server_transmission_transmitWs_listener_processor_frame():socketFrame {
                const bits0:string = toBin(data[0]), // bit string - convert byte number (0 - 255) to 8 bits
                    mask:boolean = (data[1] > 127),
                    len:number = (mask === true)
                        ? data[1] - 128
                        : data[1],
                    extended:number = (function terminal_server_transmission_transmitWs_listener_processor_frame_extended():number {
                        if (len < 126) {
                            return len;
                        }
                        if (len < 127) {
                            return data.slice(2, 4).readUInt16BE(0);
                        }
                        return data.slice(4, 10).readUIntBE(0, 6);
                    }()),
                    frameItem:socketFrame = {
                        fin: (data[0] > 127),
                        rsv1: bits0.charAt(1),
                        rsv2: bits0.charAt(2),
                        rsv3: bits0.charAt(3),
                        opcode: toDec(bits0.slice(4)),
                        mask: mask,
                        len: len,
                        extended: extended,
                        maskKey: null,
                        payload: null,
                        startByte: (function terminal_server_transmission_transmitWs_listener_processor_frame_startByte():number {
                            const keyOffset:number = (mask === true)
                                ? 4
                                : 0;
                            if (len < 126) {
                                return 2 + keyOffset;
                            }
                            if (len < 127) {
                                return 4 + keyOffset;
                            }
                            return 10 + keyOffset;
                        }())
                    };
                if (frameItem.mask === true) {
                    frameItem.maskKey = data.slice(frameItem.startByte - 4, frameItem.startByte);
                }
                frameItem.payload = data.slice(frameItem.startByte);

                return frameItem;
            }()),
            opcode:number = (frame.opcode === 0)
                ? socket.opcode
                : frame.opcode;

        if (frame.fin === true && data.length === frame.startByte) {
            // a Firefox defect workaround part 1
            buf = data;
            return;
        }
        if (frame === null) {
            // frame will be null if less than 5 bytes, so don't process it yet
            return;
        };
        // decoding complete

        // unmask payload
        if (frame.mask === true) {
            /*
                RFC 6455, 5.3.  Client-to-Server Masking
                j                   = i MOD 4
                transformed-octet-i = original-octet-i XOR masking-key-octet-j
            */
            frame.payload.forEach(function terminal_commands_websocket_dataHandler_unmask(value:number, index:number):void {
                frame.payload[index] = value ^ frame.maskKey[index % 4];
            });
        }
        // unmask payload complete

        socket.fragment.push(frame.payload);

        // store payload or write response
        if (frame.fin === true) {
            // complete data frame
            const opcode:number = (frame.opcode === 0)
                    ? socket.opcode
                    : frame.opcode,
                control = function terminal_commands_websocket_dataHandler_control():void {
                    if (opcode === 8) {
                        // remove closed socket from client list
                        let a:number = websocket.clientList.length;
                        do {
                            a = a - 1;
                            if (websocket.clientList[a].sessionId === socket.sessionId) {
                                websocket.clientList.splice(a, 1);
                                break;
                            }
                        } while (a > 0);
                        socket.closeFlag = true;
                    } else if (opcode === 9) {
                        // respond to "ping" as "pong"
                        data[0] = websocket.convert.toDec(`1${frame.rsv1 + frame.rsv2 + frame.rsv3}1010`);
                    }
                    data[1] = websocket.convert.toDec(`0${websocket.convert.toBin(frame.payload.length)}`);
                    socket.write(Buffer.concat([data.slice(0, 2), frame.payload]));
                    if (opcode === 8) {
                        // end the closed socket
                        socket.end();
                    }
                };

            // write frame header + payload
            if (opcode === 1 || opcode === 2) {
                // text or binary
                const result:string = Buffer.concat(socket.fragment).slice(0, frame.extended).toString();

                // handle the result here !!!!

                // reset socket
                socket.fragment = [];
                socket.opcode = 0;
            } else {
                control();
            }
        } else {
            // fragment, must be of type text (1) or binary (2)
            if (frame.opcode > 0) {
                socket.opcode = frame.opcode;
            }
        }
    };
```

The above code is everything we need to properly interpret websocket data on the server, and comes in three parts:
1. Heading decode
2. Payload unmask
3. Data management

#### Heading decode
Untangles the data gram to that each specified part is uniquely identified including the payload data.

#### Unmask
All communications from a websocket client to a websocket server are to be masked, according to the protocol specification, which means all websocket messages coming from web browsers will be masked.
This masking occurs regardless of encryption in order to bypass modification by network equipment in route whether physical like routers/switches or logical like proxies, firewalls, or intermediary servers.

Unmasking is defined using a simple algorithm by RFC 6455, Section 5.3:
```
   Octet i of the transformed data ("transformed-octet-i") is the XOR of
   octet i of the original data ("original-octet-i") with octet at index
   i modulo 4 of the masking key ("masking-key-octet-j"):

     j                   = i MOD 4
     transformed-octet-i = original-octet-i XOR masking-key-octet-j

   The payload length, indicated in the framing as frame-payload-length,
   does NOT include the length of the masking key.  It is the length of
   the "Payload data", e.g., the number of bytes following the masking
   key.
```

Unmasking must only occur if the *maskKey* bit is value *1*.
The applied algorithm from the prior code sample:

```typescript
    frame.payload.forEach(function terminal_commands_websocket_dataHandler_unmask(value:number, index:number):void {
        frame.payload[index] = value ^ frame.maskKey[index % 4];
    });
```

#### Data management
Before we can handle the unmasked data we must understand fragmentation.
Fragmentation will only occur on data frames, which is opcodes: *text*, *binary*, or custom defined opcodes.
Control frames must not be fragmented and may be sent irrespective of completion of fragmented data.
Control frames are *close*, *ping*, and *pong*.

Fragmented data is observed by one of two characteristics:
1. A data frame with the *fin* bit switched off.  In this case the frame will have a data type opcode for the first fragment, but all remaining fragments will have an opcode of **0** indicating continuation.
2. A data frame, regardless of *fin* bit, having an opcode of **0**.  Receiving a data frame with *fin* bit on and opcode **0** indicates the last frame of a series of fragments.

Since control frames must not be fragmented we segment the code above by fin bit.
If the fin bit is on then either the frame represents data that never fragmented, completion of fragmented data, or a control data frame.
For unfinished data we only store the opcode if not 0 and the current unmasked data payload.

Looking at the data handling logic above we separate the control logic out into a child function.
The logic for close frames, *opcode 8*, sends a close response acknowledging receipt of the control frame.
It also removes the given socket from the `websocket.clientList` array and closes the socket killing the connection from the server.

There is only 1 line of code dedicated for receipt of *ping* frames.
The opcode is converted from 9 to 10, thereby transforming the frame from *ping* to *pong*, and responds with the frame otherwise identical but unmasked.
You can see there isn't any logic supplied for receipt of *pong* type frames.

For data frames the above login only performs a `console.log` on the completed payload, which concatenates stored data from socket.fragment and frame.payload.
Additional logic will go there for actually handling data in your own application.
After this the properties socket.fragment and socket.opcode are reset to await the next fragmented data frame.

Looking at the code there are two comments about a Firefox defect workaround.
It seems in some cases Firefox will send two separate network frames for a single websocket frame.
The first is the frame header and the second is the frame payload.
This is identifiable if the frame size is exactly the length of the frame header only and yet claims to the be larger to sufficiently describe the frame payload size.
The solution is to save the frame header and concat it to the next incoming frame.
The solution in the code sample uses a variable named *buf* that must be declared outside the *processor* function so as to retain the value of the *data* argument.

## Send (write) operations
In order to send data on a websocket channel a properly formed frame heading must be supplied.
The protocol specification mentions that messages from a client to a server MUST be masked, but messages from a server to a client MUST NOT be masked.
This means if communicating to a browser the messages must not be masked, but messages from one server to another should be masked if sent in the capacity of a client otherwise the remote server should close the socket.
That said all sent data in this tutorial will be unmasked.

```typescript
    send: function (socket:socketClient, data:Buffer|string):void {
        // data is fragmented above 1 million bytes and sent unmasked
        if (socket.closeFlag === true) {
            return;
        }
        let len:number = 0,
            dataPackage:Buffer = null,
            // first two frames are required header, simplified headers because its all unmasked
            // * payload size smaller than 126 bytes
            //   - 0 allocated bytes for extended length value
            //   - length value in byte index 1 is payload length
            // * payload size 126 - 65535 bytes
            //   - 2 bytes allocated for extended length value (indexes 2 and 3)
            //   - length value in byte index 1 is 126
            // * payload size larger than 65535 bytes
            //   - 8 bytes allocated for extended length value (indexes 2 - 9)
            //   - length value in byte index 1 is 127
            stringData:string = null,
            fragment:Buffer = null,
            frame:Buffer = null;
        const socketData:socketData = payload as socketData,
            isBuffer:boolean = (socketData.service === undefined),
            // fragmentation is disabled by assigning a value of 0
            fragmentSize:number = 1e6,
            op:1|2|8|9 = (opcode === undefined)
                ? (isBuffer === true)
                    ? 2
                    : 1
                : opcode,
            writeFrame = function terminal_server_transmission_transmitWs_send_writeFrame(finish:boolean, firstFrame:boolean):void {
                const size:number = fragment.length;
                // frame 0 is:
                // * 128 bits for fin, 0 for unfinished plus opcode
                // * opcode 0 - continuation of fragments
                // * opcode 1 - text (total payload must be UTF8 and probably not contain hidden control characters)
                // * opcode 2 - supposed to be binary, really anything that isn't 100& UTF8 text
                // ** for fragmented data only first data frame gets a data opcode, others receive 0 (continuity)
                frame[0] = (finish === true)
                    ? (firstFrame === true)
                        ? 128 + op
                        : 128
                    : (firstFrame === true)
                        ? op
                        : 0;
                // frame 1 is length flag
                frame[1] = (size < 126)
                    ? size
                    : (size < 65536)
                        ? 126
                        : 127;
                if (size > 125) {
                    if (size < 65536) {
                        frame.writeUInt16BE(size, 2);
                    } else {
                        frame.writeUIntBE(size, 4, 6);
                    }
                }
                socket.write(Buffer.concat([frame, fragment]));
            },
            fragmentation = function terminal_server_transmission_transmitWs_send_fragmentation(first:boolean):void {
                if (len > fragmentSize && fragmentSize > 0) {
                    fragment = (isBuffer === true)
                        ? dataPackage.slice(0, fragmentSize)
                        : Buffer.from(stringData.slice(0, fragmentSize), "utf8");
                    // fragmentation
                    if (first === true) {
                        // first frame of fragment
                        writeFrame(false, true);
                    } else if (len > fragmentSize) {
                        // continuation of fragment
                        writeFrame(false, false);
                    }
                    if (isBuffer === true) {
                        dataPackage = dataPackage.slice(fragmentSize);
                        len = dataPackage.length;
                    } else {
                        stringData = stringData.slice(fragmentSize);
                        len = Buffer.byteLength(stringData, "utf8")
                    }
                    terminal_server_transmission_transmitWs_send_fragmentation(false);
                } else {
                    // finished, not fragmented if first === true
                    fragment = (isBuffer === true)
                        ? dataPackage
                        : Buffer.from(stringData, "utf8");
                    writeFrame(true, first);
                }
            };
        if (isBuffer === false) {
            stringData = JSON.stringify(payload);
        }
        dataPackage = (isBuffer === true)
            ? payload as Buffer
            : Buffer.from(stringData, "utf8");
        len = dataPackage.length;
        frame = (len < 126)
            ? Buffer.alloc(2)
            : (len < 65536)
                ? Buffer.alloc(4)
                : Buffer.alloc(10);
        fragmentation(true);
    }
```

In the code example provided all data payloads larger than 1 million bytes are fragmented.
Fragmentation is optional, but it reduces latency and risk of failure due to network transmission limitations.
Large data frames may also be arbitrarily rejected due to the settings of some receiving servers.
The fragmentation logic uses recursion to continually send the first million bytes of a payload until the payload has less than a million bytes left to transmit.

There are two interesting parts of building the frame header:
1. Accounting for the first two bytes
2. Accounting for extend length bytes, if necessary.

This process is a bit simplified compared to receiving data frames, because masking is not applied and we are not sending control frames.
The first two bytes a defined using decimal integers as a personal preference because I lack intimate familiarity with binary and bitwise operators.
To understand how these headers are built we must remember the data gram graph from the RFC.

The first byte (`frame[0]`) is as follows:
* fin bit
* rsv1/2/3 bits
* opcode (4 bits)

That first bit occupies the 128 decimal value position of an 8 bit byte.
If the fin bit is set this first byte must be at least 128.
We ignore the rsv bits, so those are 0.
The opcode will be 0 for a fragment, 1 for a text frame that is either not fragmented or the first fragment, or 2 for a binary frame that is either not fragmented or the first fragment.
We simply add those values to get 128, 129, or 130.

The second byte (`frame[1]`) has no mask so the first bit is ignored.
The payload link follows the same formula mentioned earlier:
* The actual length of payload in bytes if less than 126 bytes.
* 126 if payload byte length is 126 - 65535
* 127 if payload byte length is greater than 65535.

If that second byte's value is less than 126 we ignore extended length bytes and instead just write the payload starting at buffer index 2.
Otherwise we write two bytes of extended payload length if the less than 65535 bytes long or 8 bytes of payload length.

It must be noted that JavaScript cannot support 64bit numbers.
The maximum number value supported by JavaScript is (2**53) - 1.
Because of that we instead write a 48 bit value into the last 6 bytes of the extended size block, which means offsetting the buffer index by 4 instead of 2: `frame.writeUIntBE(size, 4, 6);`.

Please also bear in mind that text must be fragmented differently than binary.
This is because text characters are multiple bytes in JavaScript, so fragmenting a binary representation of text could result in splitting a character between its respective bytes.
This will break receiving agents that interpret text type payloads, such as web browsers.
In the code above the different fragmentation occurs by slicing a string before converting it to binary, where as binary data is sliced directly.

This completes the frame header and the payload immediately follows to complete the data frame.

## Completed code library
The following code represents the prior code samples combined into a single complete library file along with license text.

```typescript
/*
    Share File Systems is a privacy driven peer-to-peer application.
    Copyright (C) 2021  Austin Cheney

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU Affero General Public License as
    published by the Free Software Foundation, either version 3 of the
    License, or (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU Affero General Public License for more details.

    You should have received a copy of the GNU Affero General Public License
    along with this program.  If not, see <https://www.gnu.org/licenses/>.

Full license: https://github.com/prettydiff/share-file-systems/blob/master/license

*/
const websocket:websocket = {
    broadcast: function (data:Buffer|string):void {},
    clientList: socketClient[],
    send: function (socket:socketClient, data:Buffer|string):void {
        // data is fragmented above 1 million bytes and sent unmasked
        if (socket.closeFlag === true) {
            return;
        }
        let len:number = 0,
            dataPackage:Buffer = null,
            // first two frames are required header, simplified headers because its all unmasked
            // * payload size smaller than 126 bytes
            //   - 0 allocated bytes for extended length value
            //   - length value in byte index 1 is payload length
            // * payload size 126 - 65535 bytes
            //   - 2 bytes allocated for extended length value (indexes 2 and 3)
            //   - length value in byte index 1 is 126
            // * payload size larger than 65535 bytes
            //   - 8 bytes allocated for extended length value (indexes 2 - 9)
            //   - length value in byte index 1 is 127
            stringData:string = null,
            fragment:Buffer = null,
            frame:Buffer = null;
        const socketData:socketData = payload as socketData,
            isBuffer:boolean = (socketData.service === undefined),
            // fragmentation is disabled by assigning a value of 0
            fragmentSize:number = 1e6,
            op:1|2|8|9 = (opcode === undefined)
                ? (isBuffer === true)
                    ? 2
                    : 1
                : opcode,
            writeFrame = function terminal_server_transmission_transmitWs_send_writeFrame(finish:boolean, firstFrame:boolean):void {
                const size:number = fragment.length;
                // frame 0 is:
                // * 128 bits for fin, 0 for unfinished plus opcode
                // * opcode 0 - continuation of fragments
                // * opcode 1 - text (total payload must be UTF8 and probably not contain hidden control characters)
                // * opcode 2 - supposed to be binary, really anything that isn't 100& UTF8 text
                // ** for fragmented data only first data frame gets a data opcode, others receive 0 (continuity)
                frame[0] = (finish === true)
                    ? (firstFrame === true)
                        ? 128 + op
                        : 128
                    : (firstFrame === true)
                        ? op
                        : 0;
                // frame 1 is length flag
                frame[1] = (size < 126)
                    ? size
                    : (size < 65536)
                        ? 126
                        : 127;
                if (size > 125) {
                    if (size < 65536) {
                        frame.writeUInt16BE(size, 2);
                    } else {
                        frame.writeUIntBE(size, 4, 6);
                    }
                }
                socket.write(Buffer.concat([frame, fragment]));
            },
            fragmentation = function terminal_server_transmission_transmitWs_send_fragmentation(first:boolean):void {
                if (len > fragmentSize && fragmentSize > 0) {
                    fragment = (isBuffer === true)
                        ? dataPackage.slice(0, fragmentSize)
                        : Buffer.from(stringData.slice(0, fragmentSize), "utf8");
                    // fragmentation
                    if (first === true) {
                        // first frame of fragment
                        writeFrame(false, true);
                    } else if (len > fragmentSize) {
                        // continuation of fragment
                        writeFrame(false, false);
                    }
                    if (isBuffer === true) {
                        dataPackage = dataPackage.slice(fragmentSize);
                        len = dataPackage.length;
                    } else {
                        stringData = stringData.slice(fragmentSize);
                        len = Buffer.byteLength(stringData, "utf8")
                    }
                    terminal_server_transmission_transmitWs_send_fragmentation(false);
                } else {
                    // finished, not fragmented if first === true
                    fragment = (isBuffer === true)
                        ? dataPackage
                        : Buffer.from(stringData, "utf8");
                    writeFrame(true, first);
                }
            };
        if (isBuffer === false) {
            stringData = JSON.stringify(payload);
        }
        dataPackage = (isBuffer === true)
            ? payload as Buffer
            : Buffer.from(stringData, "utf8");
        len = dataPackage.length;
        frame = (len < 126)
            ? Buffer.alloc(2)
            : (len < 65536)
                ? Buffer.alloc(4)
                : Buffer.alloc(10);
        fragmentation(true);
    },
    server: function (config:websocketServer):Server {
        // create the server
        const handshake = function (socket:socketClient, data:string, callback:(key:string) => void):void {
                const headers:string[] = data.split("\r\n"),
                    output:string[] = [],
                    hash:Hash = createHash("sha1");
                let a:number = headers.length,
                    key:string = "";
                do {
                    a = a - 1;
                    if (headers[a].indexOf("Sec-WebSocket-Key") > -1) {
                        // the search for whitespace is mostly unnecessary,
                        // but this is thorough because network devices can mutilate data across the wire
                        key = headers[a].replace(/\s*/g, "").replace("Sec-WebSocket-Key:", "");
                        break;
                    }
                } while (a > 0);
                createHash();
                output.push("HTTP/1.1 101 Switching Protocols");
                output.push("Upgrade: websocket");
                output.push("Connection: Upgrade");
                output.push(`Sec-WebSocket-Accept: ${hash.update(key + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11").digest("base64")}`);
                output.push("");
                socket.write(output.join("\r\n"));
                callback(key);
            },
            dataHandler = function (socket:socketClient):void {
                let buf:Buffer = null;
                const processor = function (data:Buffer):void {
                    // decode the frame header
                    const toBin = function terminal_server_transmission_websocket_listener_processor_convertBin(input:number):string {
                            return (input >>> 0).toString(2);
                        },
                        toDec = function terminal_server_transmission_websocket_listener_processor_convertDec(input:string):number {
                            return parseInt(input, 2);
                        },
                        frame:socketFrame = (function terminal_server_transmission_transmitWs_listener_processor_frame():socketFrame {
                            const bits0:string = toBin(data[0]), // bit string - convert byte number (0 - 255) to 8 bits
                                mask:boolean = (data[1] > 127),
                                len:number = (mask === true)
                                    ? data[1] - 128
                                    : data[1],
                                extended:number = (function terminal_server_transmission_transmitWs_listener_processor_frame_extended():number {
                                    if (len < 126) {
                                        return len;
                                    }
                                    if (len < 127) {
                                        return data.slice(2, 4).readUInt16BE(0);
                                    }
                                    return data.slice(4, 10).readUIntBE(0, 6);
                                }()),
                                frameItem:socketFrame = {
                                    fin: (data[0] > 127),
                                    rsv1: bits0.charAt(1),
                                    rsv2: bits0.charAt(2),
                                    rsv3: bits0.charAt(3),
                                    opcode: toDec(bits0.slice(4)),
                                    mask: mask,
                                    len: len,
                                    extended: extended,
                                    maskKey: null,
                                    payload: null,
                                    startByte: (function terminal_server_transmission_transmitWs_listener_processor_frame_startByte():number {
                                        const keyOffset:number = (mask === true)
                                            ? 4
                                            : 0;
                                        if (len < 126) {
                                            return 2 + keyOffset;
                                        }
                                        if (len < 127) {
                                            return 4 + keyOffset;
                                        }
                                        return 10 + keyOffset;
                                    }())
                                };
                            if (frameItem.mask === true) {
                                frameItem.maskKey = data.slice(frameItem.startByte - 4, frameItem.startByte);
                            }
                            frameItem.payload = data.slice(frameItem.startByte);

                            return frameItem;
                        }()),
                        opcode:number = (frame.opcode === 0)
                            ? socket.opcode
                            : frame.opcode;

                    if (frame.fin === true && data.length === frame.startByte) {
                        // a Firefox defect workaround part 1
                        buf = data;
                        return;
                    }
                    if (frame === null) {
                        // frame will be null if less than 5 bytes, so don't process it yet
                        return;
                    };
                    // decoding complete

                    // unmask payload
                    if (frame.mask === true) {
                        /*
                            RFC 6455, 5.3.  Client-to-Server Masking
                            j                   = i MOD 4
                            transformed-octet-i = original-octet-i XOR masking-key-octet-j
                        */
                        frame.payload.forEach(function terminal_commands_websocket_dataHandler_unmask(value:number, index:number):void {
                            frame.payload[index] = value ^ frame.maskKey[index % 4];
                        });
                    }
                    // unmask payload complete

                    socket.fragment.push(frame.payload);

                    // store payload or write response
                    if (frame.fin === true) {
                        // complete data frame
                        const opcode:number = (frame.opcode === 0)
                                ? socket.opcode
                                : frame.opcode,
                            control = function terminal_commands_websocket_dataHandler_control():void {
                                if (opcode === 8) {
                                    // remove closed socket from client list
                                    let a:number = websocket.clientList.length;
                                    do {
                                        a = a - 1;
                                        if (websocket.clientList[a].sessionId === socket.sessionId) {
                                            websocket.clientList.splice(a, 1);
                                            break;
                                        }
                                    } while (a > 0);
                                    socket.closeFlag = true;
                                } else if (opcode === 9) {
                                    // respond to "ping" as "pong"
                                    data[0] = websocket.convert.toDec(`1${frame.rsv1 + frame.rsv2 + frame.rsv3}1010`);
                                }
                                data[1] = websocket.convert.toDec(`0${websocket.convert.toBin(frame.payload.length)}`);
                                socket.write(Buffer.concat([data.slice(0, 2), frame.payload]));
                                if (opcode === 8) {
                                    // end the closed socket
                                    socket.end();
                                }
                            };

                        // write frame header + payload
                        if (opcode === 1 || opcode === 2) {
                            // text or binary
                            const result:string = Buffer.concat(socket.fragment).slice(0, frame.extended).toString();

                            // !!! process data here !!!

                            // reset socket
                            socket.fragment = [];
                            socket.opcode = 0;
                        } else {
                            control();
                        }
                    } else {
                        // fragment, must be of type text (1) or binary (2)
                        if (frame.opcode > 0) {
                            socket.opcode = frame.opcode;
                        }
                    }
                };
                socket.on("data", processor);
            },
            connection = function (socket:socketClient):void {
                const handshakeHandler = function (data:Buffer):void {
                    const handshakeCallback = function (key:string):void {

                        // modify the socket for use in the application
                        socket.closeFlag = false;          // closeFlag - whether the socket is (or about to be) closed, do not write
                        socket.fragment = [];              // storehouse of data received for a fragmented data package
                        socket.opcode = 0;                 // stores opcode of fragmented data page (1 or 2), because additional fragmented frames have code 0 (continuity)
                        socket.sessionId = key;            // a unique identifier on which to identify and differential this socket from other client sockets
                        socket.setKeepAlive(true, 0);      // standard method to retain socket against timeouts from inactivity until a close frame comes in
                        websocket.clientList.push(socket); // push this socket into the list of socket clients

                        // change the listener to process data
                        socket.removeListener("data", terminal_commands_websocket_connection_handshakeHandler);
                        dataHandler(socket);
                    };
                    // handshake
                    handshake(socket, data.toString(), handshakeCallback);
                };
                socket.on("data", handshakeHandler);
                socket.on("error", function (errorItem:Error) {
                    if (socket.closeFlag === false) {
                        console.error(errorItem.toString());
                    }
                });
            },
            wsServer:Server = (config.cert === null)
                ? netServer()
                : tlsServer({
                    cert: config.cert.cert,
                    key: config.cert.key,
                    requestCert: true
                }, connection);
            // server created

        // create the listener (activates the server)
        wsServer.listen({
            host: config.address,
            port: config.port
        }, function ():void {
            const addressInfo:AddressInfo = wsServer.address() as AddressInfo;
            config.callback(addressInfo.port);
        });
        // server is listening

        // assign an event handler to the "connection" event
        if (config.cert === null) {
            wsServer.on("connection", connection);
        }

        // returns the server object for external access
        return wsServer;
    }
};

export default websocket;
```

This is the custom TypeScript type definitions taken from above.

```typescript
// yourTypes.d.ts
import { Server, Socket } from "net";
declare global {
    interface socketClient extends Socket {
        closeFlag: boolean;
        fragment: Buffer[];
        opcode: number;
        sessionId: string;
    }
    interface socketFrame {
        fin: boolean;
        rsv1: string;
        rsv2: string;
        rsv3: string;
        opcode: number;
        mask: boolean;
        len: number;
        maskKey: Buffer;
        payload: Buffer;
        startByte: number;
    }
    interface websocket {
        broadcast: (data:Buffer|string) => void;
        clientList: socketClient[];
        send: (socket:socketClient, data:Buffer|string) => void;
        server: (config:websocketServer) => Server;
    }
    interface websocketServer {
        address: string;
        callback: (port:number) => void;
        cert: {
            cert: string;
            key: string;
        };
        port: number;
    }
}
```