Work-in-progress API documentation.

# Definitions
**BASE_URL**: The base URL of the web application. This would typically be http://pyx.socialgamer.net/ if you are using my server.  
**CONFIG_URL**: The URL to the configuration data. This is BASE_URL/js/cah/config.js  
**AJAX_URL**: URL to the client action servlet. This should be parsed out of the data returned from CONFIG_URL.  
**LONGPOLL_URL**: URL to the long-poll servlet. This should be parsed out of the data returned from CONFIG_URL.  
**Constants**: Any references to "constants" would be referring to the data defined in https://github.com/ajanata/PretendYoureXyzzy/blob/master/src/net/socialgamer/cah/Constants.java. These values are used to express commands and parameters over the wire. Some UI strings are defined here as well (which you are free to replace with something equivalent).  
 * Any of the following names refer to an enum in the constants file: AjaxRequest, AjaxResponse, AjaxOperation, ErrorCode, LongPollEvent  
**CONSTANTS_URL**: URL to the JavaScript version of the wire constants. This is BASE_URL/js/cah.constants.js  


# A note about client-server communication
All constant data passed between the server and clients (command codes, parameter names, basically everything but usernames, chat, and card text) are based on token values defined in the Constants file. This documentation will refer to the full names of such things as defined in the Constants file, but all communication to and from the server must be done with the values defined therein. The examples included herein will use the constant values from the time of this writing, which are unlikely to ever change.

The rationale behind this was to give compile-time checking of the constants, and avoid magic strings everywhere. As an added bonus, when this was very early in development and a lot of AJAX debugging was still occurring, it allowed meaningful strings to be used and still be able to easily change to shorter values to reduce bandwidth and latency later.

A client SHOULD always parse the Constants file every time it initiates communication with the server, even though these values SHOULD never change. The enum names/keys will never change. If you can run the file through a JavaScript parser, it would be very simple. If putting the data in another format would make it easier for your to parse on your client, a pull request with a program that does an equivalent to what net.socialgamer.cah.UpdateJsConstants does will be accepted.

# Request Format
All client-initiated actions occur as form-encoded POST requests to AJAX_URL. There must be at least two fields provided, AjaxRequest.OP and AjaxRequest.SERIAL. OP contains one of the values of AjaxOperation (e.g. AjaxOperation.REGISTER), and SERIAL contains a sequence number. The server doesn't care what you use as this value, it simply returns it in the response so you can cross-reference a reply to a request if keeping track of the request threads otherwise is more difficult.

Gzip compression is supported and encouraged using normal HTTP headers.

## Example

Logging in with nickname "my_nickname"

```
POST /AjaxServlet HTTP/1.1
Host: pyx.socialgamer.net
Content-type: application/x-www-form-encoded
Content-length: 21
User-agent: Awesome PYX Client/1.0 (Android 4.1.1)
Cookie: JSESSIONID=abcdef1234567890

o=r&s=2&n=my_nickname
```

# Client Request Response Format
Responses for client requests are simple JSON associative arrays, with some of the values of AjaxResponse as keys. If there was an error in processing the request, AjaxResponse.ERROR will be set to boolean true, and AjaxResponse.ERROR_CODE will contain a value from ErrorCode. Both keys MAY NOT be present if there is no error.

## Examples (one per line)

Successful log in as above: {s:2,n:"my_nickname"}  
Unable to log in due to being banned: {s:2,e:true,ec:"B&"}

# Long Polling
Communication from the server to the client is via long-polling to LONGPOLL_URL. This is a POST(?) request with no parameters (other than the JSESSIONID cookie, of course). As soon as the client is registered with the server, it MUST continuously poll from this URL. The server will block up to a timeout (of 20 seconds plus a random delay of up to 5 additional seconds, but this may change) or until there is data to return to the client. The server attempts to batch multiple notifications into one delivery if they arrive quickly enough (I forget the exact value, something like 50 ms). If there is nothing to tell the client, the server returns LongPollEvent.NOOP.

Once the client receives data from the server, it processes the data and then SHOULD immediately initiate another long-poll request. The client MAY wait a few seconds before doing so in extenuating circumstances, but doing so will delay the game (obviously). The client MUST NOT wait more than 10 seconds before initiating another request, as it WILL miss game events if it does so. The server will remove a client if it doesn't hear from it on LONGPOLL_URL in about 45 seconds.

The client SHOULD time-out its connections to LONGPOLL_URL after about 30-40 seconds (even better would be to check BASE_URL/js/cah.longpoll.js for cah.longpoll.TIMEOUT, in ms) and attempt to connect again. Such error reconnects SHOULD have exponential backoff starting at 500ms.

The client MUST stop long-polling after receiving LongPollEvent.BANNED, LongPollEvent.KICKED, ErrorCode.NO_SESSION, or ErrorCode.SESSION_EXPIRED, or performing AjaxOperation.LOG_OUT.

The server includes its current UNIX timestamp in milliseconds with every long-poll event under the key LongPollResponse.TIMESTAMP. This is not currently used for anything, but is provided in case clients want to use it for ... something.

# Response format
Looking back at the code now while writing this, I realize something rather silly happens in the long-poll handler. If there is a NOOP response, a JSON object is returned. If there are any number of actual events to return (even just one), a JSON array of JSON objects is returned instead. I will probably fix this to always return an array of objects, but even then a client SHOULD be able to deal with both JSON arrays and naked JSON objects.

## Examples (one per line)

No-op: {ts:1234567890,E:"_"}  
New user connecting, and this user sending a chat message immediately. This user is not recognized as an administrator. [{ts:1234567890,E:"np",n:"someone_elses_nickname"},{ts:1234567891,E:"c",f:"someone_elses_nickname",m:"This is a chat message",fa:false}]  


# Connection Sequence
Make a GET request to BASE_URL/game.jsp and store the value of the JSESSIONID cookie. This cookie MUST be included in every subsequent request until either after the user explicitly logs out via AjaxOperation.LOG_OUT (and it must still be sent with the log out request) OR ErrorCode.NO_SESSION or ErrorCode.SESSION_EXPIRED is returned. After any of these events, the user is no longer logged in and the next request should be to BASE_URL/game.jsp to get a new JSESSIONID. This request does not have to be made until immediately before a AjaxOperation.REGISTER request.

IF you are using a persistent cookie jar, after a JESSIONID cookie has been acquired or refreshed, AjaxOperation.FIRST_LOAD should be called. See documentation on it for full details, but it will tell you if your session is new or in the middle of something.

If you never persist your cookie between app runs, you may skip AjaxOperation.FIRST_LOAD and go directly to AjaxOperation.REGISTER.

After a sucessful AjaxOperation.REGISTER, start long-polling.

Congratulations, you are connected, and as long as you keep long-polling, you will stay connected.


# Client Requests (AjaxOperation)


# Server Events (LongPollEvent)
#
