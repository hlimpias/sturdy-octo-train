Work-in-progress API documentation.

# Definitions
**AJAX_URL**: URL to the client action servlet.  
**LONGPOLL_URL**: URL to the long-poll servlet.  
**Constants**: Any references to "constants" would be referring to the data defined in https://github.com/ajanata/PretendYoureXyzzy/blob/master/src/net/socialgamer/cah/Constants.java. These values are used to express commands and parameters over the wire. Some UI strings are defined here as well (which you are free to replace with something equivalent).  
**CONSTANTS_URL**: URL to the JavaScript version of the wire constants.




Long poll: The client MUST always keep a connection open to LONGPOLL_URL; the server will block until there is something to tell the client. When the client receives a response 