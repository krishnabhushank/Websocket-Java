# Websocket App using Java

A WebSocket is a protocol that provides full-duplex communication channels over a single, long-lived connection between a client (usually a web browser) and a server. This allows for real-time data transfer and is particularly useful for applications that require frequent updates, such as chat applications, live notifications, or gaming.

### How WebSockets Work

1. **Handshake Process**:
    - The WebSocket connection starts with a handshake, which is initiated by the client.
    - The client sends an HTTP request to the server with an `Upgrade` header, indicating that it wants to establish a WebSocket connection.

    Example of a WebSocket handshake request:
    ```http
    GET /ws HTTP/1.1
    Host: example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Sec-WebSocket-Version: 13
    ```

    - The server responds with an HTTP response if it agrees to the upgrade.

    Example of a WebSocket handshake response:
    ```http
    HTTP/1.1 101 Switching Protocols
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
    ```

2. **Full-Duplex Communication**:
    - Once the handshake is complete, the connection is established and both client and server can send messages to each other independently.
    - Unlike HTTP, where each request-response cycle is independent, WebSocket maintains an open connection, allowing for continuous data exchange without the overhead of re-establishing connections.

3. **Message Frames**:
    - Data sent over WebSocket is transmitted in frames. Each frame can carry a text, binary, or control message.
    - The client and server communicate using these frames, which include information about the type and length of the message.

    Example of a WebSocket text frame:
    ```
    0x81 [0x05] "Hello"
    ```

4. **Closing the Connection**:
    - Either the client or the server can initiate the closing handshake to terminate the connection.
    - This is done by sending a close frame, after which the other party sends a close frame in response and the connection is closed.

    Example of a WebSocket close frame:
    ```
    0x88 [0x02] 0x03E8
    ```

### Key Features of WebSockets

1. **Real-Time Communication**:
    - WebSockets enable real-time interaction between the client and server without the need for long-polling or repeated HTTP requests.

2. **Full-Duplex Communication**:
    - Both client and server can send messages independently, providing a two-way communication channel.

3. **Reduced Latency**:
    - WebSockets reduce the latency associated with establishing connections, making them suitable for applications requiring fast updates.

4. **Low Overhead**:
    - Once the connection is established, WebSockets have minimal overhead compared to HTTP, making them efficient for frequent message exchanges.

### Use Cases for WebSockets

- **Chat Applications**: Real-time messaging between users.
- **Live Notifications**: Instant updates for events, such as social media notifications or financial market updates.
- **Online Gaming**: Real-time communication between players and game servers.
- **Collaboration Tools**: Real-time document editing or collaboration applications.
- **Live Streaming**: Broadcasting live video or audio streams.

### Example Scenario

Consider a chat application:

1. **Client**: The user opens the chat application in their web browser.
2. **Connection**: The client sends a WebSocket handshake request to the server.
3. **Server**: The server accepts the handshake and establishes the connection.
4. **Messaging**: The client sends a chat message, which the server receives and then broadcasts to all connected clients.
5. **Real-Time Updates**: All clients receive the new message instantly without needing to refresh the page or make additional HTTP requests.
6. **Closure**: When the user closes the chat application, the WebSocket connection is closed gracefully.

WebSockets provide a powerful mechanism for real-time, bi-directional communication in web applications, enabling rich, interactive user experiences.

---

Let's create a simple WebSocket application using Spring Boot. This application will include a WebSocket server endpoint and a client that can connect to it.

### Plan:
1. **Setup Spring Boot Project**:
    - Create a new Spring Boot project.
    - Add dependencies for WebSocket.

2. **Create WebSocket Configuration**:
    - Create a configuration class to register WebSocket endpoints.

3. **Create WebSocket Handler**:
    - Create a handler to manage WebSocket messages.

4. **Create WebSocket Client**:
    - Optionally, create a client to test the WebSocket server.

### Step-by-Step:

#### 1. Setup Spring Boot Project
First, create a new Spring Boot project using Spring Initializr (https://start.spring.io/), including the WebSocket dependency.

Dependencies:
- Spring Web
- Spring WebSocket

#### 2. Create WebSocket Configuration
Create a configuration class to register WebSocket endpoints.

```java
// src/main/java/com/example/websocket/WebSocketConfig.java
package com.example.websocket;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.config.annotation.EnableWebSocket;
import org.springframework.web.socket.config.annotation.WebSocketConfigurer;
import org.springframework.web.socket.config.annotation.WebSocketHandlerRegistry;

@Configuration
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry.addHandler(new MyWebSocketHandler(), "/ws").setAllowedOrigins("*");
    }
}
```

#### 3. Create WebSocket Handler
Create a handler to manage WebSocket messages.

```java
// src/main/java/com/example/websocket/MyWebSocketHandler.java
package com.example.websocket;

import org.springframework.web.socket.WebSocketHandler;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;
import org.springframework.web.socket.TextMessage;

public class MyWebSocketHandler extends TextWebSocketHandler {

    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        String payload = message.getPayload();
        System.out.println("Received message: " + payload);
        session.sendMessage(new TextMessage("Hello, " + payload + "!"));
    }
}
```

#### 4. Create WebSocket Client (Optional)
To test the WebSocket server, create a simple WebSocket client.

```java
// src/main/java/com/example/websocket/WebSocketClient.java
package com.example.websocket;

import org.springframework.web.socket.client.standard.StandardWebSocketClient;
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;

public class WebSocketClient {

    public static void main(String[] args) throws Exception {
        StandardWebSocketClient client = new StandardWebSocketClient();
        WebSocketSession session = client.doHandshake(new MyWebSocketHandler(), "ws://localhost:8080/ws").get();
        session.sendMessage(new TextMessage("World"));
    }
}
```

### Full Code Block

```java
// src/main/java/com/example/websocket/WebSocketConfig.java
package com.example.websocket;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.config.annotation.EnableWebSocket;
import org.springframework.web.socket.config.annotation.WebSocketConfigurer;
import org.springframework.web.socket.config.annotation.WebSocketHandlerRegistry;

@Configuration
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry.addHandler(new MyWebSocketHandler(), "/ws").setAllowedOrigins("*");
    }
}

// src/main/java/com/example/websocket/MyWebSocketHandler.java
package com.example.websocket;

import org.springframework.web.socket.WebSocketHandler;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;
import org.springframework.web.socket.TextMessage;

public class MyWebSocketHandler extends TextWebSocketHandler {

    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        String payload = message.getPayload();
        System.out.println("Received message: " + payload);
        session.sendMessage(new TextMessage("Hello, " + payload + "!"));
    }
}

// src/main/java/com/example/websocket/WebSocketClient.java
package com.example.websocket;

import org.springframework.web.socket.client.standard.StandardWebSocketClient;
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;

public class WebSocketClient {

    public static void main(String[] args) throws Exception {
        StandardWebSocketClient client = new StandardWebSocketClient();
        WebSocketSession session = client.doHandshake(new MyWebSocketHandler(), "ws://localhost:8080/ws").get();
        session.sendMessage(new TextMessage("World"));
    }
}
```

This setup creates a basic WebSocket server that listens for connections at `/ws` and echoes back a greeting. The client connects to this endpoint and sends a message to demonstrate interaction.

**Next Steps:**
**a.** Add unit tests for the WebSocket handler.  
**b.** Implement additional features like message broadcasting to all connected clients.
