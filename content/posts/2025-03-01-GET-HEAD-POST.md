+++
date = '2025-03-01T12:00:00+0200'
draft = false
title = 'munin2: sending a HTTP response'
category = 'munin'
+++

I have added some error handling and did some refactoring of the raw TCP server.

The goal now is that the server should send a dummy HTTP 1.0 "200 OK" response, along with a dummy minimal HTML-encoded message, before closing the connection.

## response format

I have done a quick read of the relevant shorts chapters in [the HTTP standard](https://datatracker.ietf.org/doc/html/rfc1945). The way I understand it, a HTTP 1.0 response contains the following:

```
Status-Line
General-Header | Response-Header | Entity-Header
Entity-Header
CRLF
Entity-Body
```

Let's go through them step-by-step for this minimal response:

- Status-Line

This is the protocol version followed by the HTTP status code, along with the textual phrase for that code. Each element separated by spaces.

So that would be `HTTP/1.1 200 OK`.

- Response Header Fields

> allow the server to pass additional information about the response which cannot be placed in the Status-Line

ok. does not seem necessary here?

- Entity Header Fields

Now these I have seen before, these are `Allow`, `Content-Type`, `Content-Length`, etc.

`Allow` seems unnecessary since we are just sending responses. But I suppose we need the following two lines:

```
Content-Type: text/html
Content-Length: ??
```

The Content-Length it's in "decimal number of octets", is that bytes? So this should be the length of the HTML file?

- CRLF


- Entity-Body

This will be the minimal HTML-encoded message. Something like:

```html
<html><body>hello world</body></html>
```

## sending the response

Time to put this together into code, using `snprintf()` concatenation:

```c
void get_http_response(char *buffer) {
    char *status_line = "HTTP/1.0 200 OK";
    char *content_type_header = "Content-Type: text/html";
    char *html_body = "<html><body>hello world</body></html>";
    int html_body_length = strlen(html_body);

    // create Content-Length Header
    char content_length_header[50];
    snprintf(content_length_header, sizeof(content_length_header), "Content-Length: %d", html_body_length);

    // build response
    snprintf(buffer, sizeof(buffer), "%s\r\n%s\r\n%s\r\n\r\n%s", status_line, content_type_header, content_length_header, html_body);
}
```

Time to test it using netcat:

```sh
$ nc localhost 12345
hello # message I sent
HTTP/1. # response
```

Doesn't work, the rest of the response just cuts off. Why? I have allocated enough memory to the HTTP response buffer, so that can't be it.

Damnit. `buffer` is passed as an argument, so `sizeof(buffer)` does not return the actual allocated buffer size. It just gives the size of a char * (8 bytes). Thus, only 7 bytes + null byte operator is copied into the buffer, resulting in "HTTP/1.".

After changing that:

```sh
$ nc localhost 12345
send me the html please # from the client
HTTP/1.0 200 OK
Content-Type: text/html
Content-Length: 37

<html><body>hello world</body></html>
```

Nice! Testing in a browser reveals the same thing:

![testing munin hello world](/images/munin-hello-world.jpeg)

## next steps
- parse the request
- send 200 OK for properly formatted
- otherwise, respond 400 or 5xx
