+++
date = '2025-03-03T12:00:00+0200'
draft = false
title = 'munin3: bare-bones parsing a GET request'
category = 'munin'
+++

The next step in munin is to parse the HTTP request and ensure it is properly formatted, rather than just replying with a HTTP response no matter what.

Working with just GET requests for now (and still just HTTP 1.0).

Chapter 5 in the HTTP 1.0 Standard deals with requests. There is some backwards compatibility with HTTP 0.9 which means one can use a so called `Simple-Request`, but I'll ignore that for now. Even though a 1.0 server must respond with a 0.9 Simple-Response to a Simple-Request, my server is not really compliant yet.

## request format

A `Full-request`is defined as:

```
Full-Request   = Request-Line                    ; Section 5.1
                        *( General-Header        ; Section 4.3
                         | Request-Header        ; Section 5.2
                         | Entity-Header )       ; Section 7.1
                        CRLF
                        [ Entity-Body ]          ; Section 7.2
```

We only care about the `Request-Line` for now. From the standard:

> Request-Line = Method SP Request-URI SP HTTP-Version CRLF

In our case, method is "GET". Request-URI is whatever (we will assume it is "/"), HTTP-Version is HTTP/1.0. Easy enough.

We ignore the headers for now, and assume the body is empty and don't read it.

That is, we read what is sent by the client, and return based on what is sent:
- starts with "GET / HTTP/1.0" => reply with "200 OK" and the minimal HTML dummy response.
- starts with "X Y HTTP/1.0" => reply with "501 Not Implemented"
- anything else => reply with "400 Bad Request"

## parsing the request

I can define the status codes as an enums:

```c
// HTTP status codes
enum status_code {
    OK = 200,
    BAD_REQUEST = 400,
    NOT_IMPLEMENTED = 501,
};
```

And then split the first line of the request using `strtok()`, to check for the token:

```c
// get first line
const char *end = strstr(client_buffer, "\r\n");
size_t line_length = end - client_buffer; // pointer arithmetic
if (end == NULL || line_length >= REQUEST_LINE_MAX_LENGTH) {
    return BAD_REQUEST; // No CRLF found or first line too long
}
char line[REQUEST_LINE_MAX_LENGTH];
strncpy(line, client_buffer, line_length);
line[line_length] = '\0'; 

// extract method, request URI and protocol.
char *method = strtok(line, " ");
char *request_uri = strtok(NULL, " ");
char *http_version = strtok(NULL, " ");

// BAD REQUEST if any tokens missing
if (method == NULL || request_uri == NULL || http_version == NULL) {
    return BAD_REQUEST;
}
```

And then check if the request is valid:

```c
// check for valid GET request.
if (strcmp(http_version, "HTTP/1.0") != 0) {
    return BAD_REQUEST;
} else if (strcmp(method, "GET") != 0) {
    return NOT_IMPLEMENTED;
} else if (strcmp(request_uri, "/") == 0) {
    return OK;
}

return BAD_REQUEST;
```

This was all in the function

```c
enum status_code get_response_status_code(char *client_buffer)
```

Now, we simple build the HTTP response based on the request:

```c
// in handle_client()

// Parse request Status-Line
enum status_code status_code_result = get_response_status_code(client_buffer);

char http_response[RESPONSE_BUFFER_SIZE];

// build http response based on status code result
switch (status_code_result) {
    case OK: 
        get_ok_http_response(http_response, sizeof(http_response));
        break;
    case BAD_REQUEST:
        get_bad_request_http_response(http_response, sizeof(http_response));
        break;
    case NOT_IMPLEMENTED:
        get_not_implemented_http_response(http_response, sizeof(http_response));
        break;
}    
```

Testing with netcat:

```sh
~/code/munin main*
$ printf "BADREQUEST" | nc localhost 12345     
HTTP/1.0 400 BAD REQUEST

~/code/munin main*
$ printf "GET / HTTP/1.0\r\n" | nc localhost 12345
HTTP/1.0 200 OK
Content-Type: text/html
Content-Length: 37

<html><body>hello world</body></html>
~/code/munin main*
$ printf "POST / HTTP/1.0\r\n" | nc localhost 12345
HTTP/1.0 501 NOT IMPLEMENTED
```

Nice! (doesn't work with browser though, as that uses HTTP 1.1, oh well).

## next steps
- actually parse the request URI as well
- return the specified file (e.g. index.html), or 404.
- refactor to have one "HTTP response" builder function.