---
layout: post
title:  "munin1: a HTTP server in C"
date:   2025-02-26 12:00:00 +0200
category: munin
---

I want to improve my (very low-level) low-level programming skills. A HTTP server in C sounds like a good project.

Munin is one of the raven's that bring information to Odin in norse mythology, so that seems like a fitting name for the project.

## plan

Following the recommendations in [this reddit comment](https://www.reddit.com/r/C_Programming/comments/kbfa6t/comment/gfh8kid/).

I'll rely on [Beej's guide on network programming](https://beej.us/guide/bgnet/). I have already gone through his guide on C programming to get the basics down.

Also, I will need the [HTTP/1.0 standard](https://datatracker.ietf.org/doc/html/rfc1945).

## creating a raw TCP server

First goal: create a TCP server that listens on a port, prints any received data, replies a message and then closes.

First I learn about the `addrinfo` struct and the `getaddrinfo()` function. Now to create the socket. 

I need to create a socket using `socket()`, and then also bind it to a port using `bind()`. I then `listen()`, and `accept()`.

to receive, I create a buffer and use `recv()`. I print whatever is received, send a message back, and then close everything down using `close()` and `freeaddrinfo()`.

With this, I have a minimum viable server, without HTTP formatting:

```c
#define BACKLOG 1
#define BUFFER_SIZE 1024

int main(int argc, char *argv[])
{
    struct addrinfo hints, *res;   
    struct sockaddr_storage client_addr;
    socklen_t addr_size;
    int sockfd, connection_sockfd, status;

    if (argc != 2) {
        fprintf(stderr,"usage: main [port number]\n");
        exit(1);
    }

    // load address structs
    memset(&hints, 0, sizeof hints);
    hints.ai_family = AF_UNSPEC;
    hints.ai_socktype = SOCK_STREAM;
    hints.ai_flags = AI_PASSIVE; // fill in IP

    status = getaddrinfo(NULL, argv[1], &hints, &res);

    if (status != 0) {
        fprintf(stderr, "getaddrinfo: %s\n", gai_strerror(status));
        return 2;
    }

    // make socket
    sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);

    // bind socket to port
    bind(sockfd, res->ai_addr, res->ai_addrlen);

    // listen on port
    listen(sockfd, BACKLOG);
    printf("Server is listening on port %s...\n", argv[1]);

    // accept connection
    addr_size = sizeof client_addr;
    connection_sockfd = accept(sockfd, (struct sockaddr *)&client_addr, &addr_size);

    char buffer[BUFFER_SIZE];

    int bytes_received = recv(connection_sockfd, buffer, BUFFER_SIZE - 1, 0);
    if (bytes_received > 0) {
        buffer[bytes_received] = '\0';  // null-terminate received data
        printf("Received: %s\n", buffer);
    }

    char *msg = "hi!";
    send(connection_sockfd, msg, strlen(msg), 0);

    close(connection_sockfd);
    close(sockfd);

    freeaddrinfo(res);
}
```

cool!

## next steps
- use http
- implement an accept() loop
- error handling