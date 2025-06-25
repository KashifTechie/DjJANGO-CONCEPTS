# HTTP Status Codes

HTTP status codes are three-digit codes that indicate the outcome of an HTTP request. They are grouped into five classes:

* **1xx – Informational**: Request received, continuing process.
* **2xx – Successful**: The request was successfully received, understood, and accepted.
* **3xx – Redirection**: Further action needs to be taken by the client.
* **4xx – Client Error**: The request contains bad syntax or cannot be fulfilled.
* **5xx – Server Error**: The server failed to fulfill a valid request.

---

## 1xx – Informational

* **100 Continue**: Initial part of the request received, client should continue.
* **101 Switching Protocols**: Server agrees to switch protocols.
* **102 Processing** (WebDAV): Server is processing the request.
* **103 Early Hints**: Server returns headers before the final response.

---

## 2xx – Successful

* **200 OK**: Request succeeded.
* **201 Created**: Request fulfilled, new resource created.
* **202 Accepted**: Request accepted, not yet processed.
* **203 Non-Authoritative Information**: Response from a third-party source.
* **204 No Content**: No content to return.
* **205 Reset Content**: Client should reset the document view.
* **206 Partial Content**: Partial resource delivered.
* **207 Multi-Status** (WebDAV): Multiple status codes for sub-requests.
* **208 Already Reported** (WebDAV): Elements already reported.
* **226 IM Used**: Delta encoding used for response.

---

## 3xx – Redirection

* **300 Multiple Choices**: Multiple options for the resource.
* **301 Moved Permanently**: Resource moved permanently.
* **302 Found**: Resource temporarily moved.
* **303 See Other**: Retrieve resource using GET.
* **304 Not Modified**: Resource not modified.
* **305 Use Proxy** (Deprecated): Use a proxy.
* **306 Switch Proxy** (Unused): Reserved.
* **307 Temporary Redirect**: Temporary redirect, same method.
* **308 Permanent Redirect**: Permanent redirect, same method.

---

## 4xx – Client Error

* **400 Bad Request**: Invalid syntax.
* **401 Unauthorized**: Authentication required.
* **402 Payment Required** (Experimental): Reserved for future use.
* **403 Forbidden**: Request understood but not allowed.
* **404 Not Found**: Resource not found.
* **405 Method Not Allowed**: Method not allowed for the resource.
* **406 Not Acceptable**: Resource not acceptable per Accept headers.
* **407 Proxy Authentication Required**: Authenticate with proxy.
* **408 Request Timeout**: Server timed out waiting.
* **409 Conflict**: Conflict with current state.
* **410 Gone**: Resource permanently unavailable.
* **411 Length Required**: Content-Length header missing.
* **412 Precondition Failed**: Precondition failed.
* **413 Payload Too Large**: Request too large.
* **414 URI Too Long**: URI too long.
* **415 Unsupported Media Type**: Unsupported format.
* **416 Range Not Satisfiable**: Cannot fulfill Range header.
* **417 Expectation Failed**: Expect header could not be met.
* **418 I'm a teapot** (RFC 2324): Easter egg for teapots.
* **421 Misdirected Request**: Sent to wrong server.
* **422 Unprocessable Entity** (WebDAV): Semantic errors.
* **423 Locked** (WebDAV): Resource is locked.
* **424 Failed Dependency** (WebDAV): Previous request failed.
* **425 Too Early**: Premature request.
* **426 Upgrade Required**: Client should switch protocols.
* **428 Precondition Required**: Precondition header missing.
* **429 Too Many Requests**: Rate limiting in effect.
* **431 Request Header Fields Too Large**: Headers too large.
* **451 Unavailable For Legal Reasons**: Censorship or legal restriction.

---

## 5xx – Server Error

* **500 Internal Server Error**: Generic server error.
* **501 Not Implemented**: Method not supported.
* **502 Bad Gateway**: Invalid response from upstream server.
* **503 Service Unavailable**: Server overloaded or down.
* **504 Gateway Timeout**: No timely response from upstream.
* **505 HTTP Version Not Supported**: Unsupported HTTP version.
* **506 Variant Also Negotiates**: Negotiation error.
* **507 Insufficient Storage** (WebDAV): No space to store resource.
* **508 Loop Detected** (WebDAV): Infinite loop detected.
* **510 Not Extended**: Further extensions required.
* **511 Network Authentication Required**: Authenticate to access network.


#
# 🔢 Standard REST API Error Codes Reference

## Client-Side Errors (4xx)
These indicate issues with the client's request.

| Code | Name                   | Reason                                                                 |
|------|------------------------|-----------------------------------------------------------------------|
| 400  | Bad Request            | Malformed request syntax or invalid parameters.                      |
| 401  | Unauthorized           | Missing or invalid authentication credentials.                       |
| 403  | Forbidden              | Authenticated user lacks permission for the resource.                |
| 404  | Not Found              | Requested resource doesn't exist.                                    |
| 405  | Method Not Allowed     | HTTP method not supported for this endpoint.                         |
| 406  | Not Acceptable         | Server can't produce response matching client's `Accept` headers.    |
| 408  | Request Timeout        | Server timed out waiting for the request.                            |
| 409  | Conflict               | Resource state conflict (e.g., duplicate data, version mismatch).    |
| 410  | Gone                   | Resource was permanently deleted.                                    |
| 415  | Unsupported Media Type | Unsupported `Content-Type` in request.                               |
| 422  | Unprocessable Entity   | Semantic validation errors (e.g., missing required fields).          |
| 429  | Too Many Requests      | Rate limit exceeded.                                                 |

## Server-Side Errors (5xx)
These indicate server failures while processing valid requests.

| Code | Name                     | Reason                                                                 |
|------|--------------------------|-----------------------------------------------------------------------|
| 500  | Internal Server Error    | Unexpected server error with no specific details.                    |
| 501  | Not Implemented          | Server doesn't support the requested functionality.                  |
| 502  | Bad Gateway              | Invalid response from upstream server.                               |
| 503  | Service Unavailable      | Server temporarily down (maintenance/overload).                      |
| 504  | Gateway Timeout          | Upstream server didn't respond in time.                              |
| 507  | Insufficient Storage     | Server can't store the representation (e.g., disk full).             |

                                                        
