<a name='menu'></a>

## Bechtel API REST Services

This document discusses the basics of using Bechtel's APIs, a collection
of RESTFul services providing access to Bechtel data. This guide serves
only as an introduction to web services and usage information common to
all of Bechtel's APIs. Detailed documentation for each services is
located under the [API List][1].


The remainder of this guide discusses techniques and basic steps
involved with API service requests and using the reponses. Each API may
have it's own particular behavior and authorization requirements. The
information in this guide should be supplemented with each services
[detailed documentation][1].

  [1]:#/apis/
  
1. [What is a RESTFul Web Service?](#WebService)

2. [SSL Access](#ssl)


[Authentication](#Authentication)

[Application Keys](#keys)

1.  [How Do I Get My Application Key?](#getkey)
2.  [Tracking Usage with your Application Key](#tracking)

[ Obtaining an OAuth Token](#token)

1.  [ Redirect to Bechtel Login](#ssoredirect)
2.  [ Retrieve user **REF**erence](#getREF)
3.  [ Obtain user information](#getUser)


[Using Responses](#apiresponse)

 

1.  [Using XML in Objective-C](#usingXML)
2.  [Using JSON in Javascript](#usingJSON)


[Example applications](#examples)

[Top](#menu)

<a name="WebService"></a>
### What is a RESTFul Web Service?


REST stands for Representational State Transfer. It is the architectural
style of the World Wide Web, as defined by the primary architect of HTTP
1.1 and co-author of the URI specification, Roy Fielding.

A RESTful web service (also called a RESTful web API) is a simple web
service implemented using HTTP and the principles of REST. It is a
collection of resources, with three defined aspects:

-   The base URI for the web service, such as
    http://example.com/resources/
-   The Internet media type of the data supported by the web service.
    This is often JSON, XML or YAML but can be any other valid Internet
    media type.
-   The set of operations supported by the web service using HTTP
    methods (e.g., POST, GET, PUT or DELETE).

### HTTP Methods

`GET`: Requests a representation of the specified resource. Requests
using GET SHOULD NOT have the significance of taking an action other
than retrieval.

`POST`: Submits data to be processed (e.g., from an HTML form) to the
identified resource. The data is included in the body of the request.
This may result in the creation of a new resource or the update of
existing resources or both.

`PUT`: Uploads a representation of an already existing resource.

`DELETE`: Deletes the specified resource.

[Top](#menu)
<a name="ssl"></a>
### SSL Access

HTTPS is the use of Secure Socket Layer (SSL) or Transport Layer
Security (TLS) as a sub-layer under regular HTTP application layering.
HTTPS encrypts and decrypts user page requests as well as the pages that
are returned by the Web server.

The main idea of HTTPS is to create a secure channel over an insecure
network. This ensures reasonable protection from eavesdroppers and
man-in-the-middle attacks, provided that adequate cipher suites are used
and that the server certificate is verified and trusted.

The trust inherent in HTTPS is based on major certificate authorities
that come pre-installed in browser software (this is equivalent to
saying "I trust certificate authority (e.g. VeriSign/Microsoft/etc.) to
tell me whom I should trust"). Therefore an HTTPS connection to a
website can be trusted if and only if all of the following are true:
-   The user trusts that their browser software correctly implements
    HTTPS with correctly pre-installed certificate authorities.
-   The user trusts the certificate authority to vouch only for
    legitimate websites without misleading names.
-   The website provides a valid certificate, which means it was signed
    by a trusted authority.
-   The certificate correctly identifies the website (e.g., when the
    browser visits "https://example", the received certificate is
    properly for "Example Inc." and not some other entity).
-   Either the intervening hops on the Internet are trustworthy, or the
    user trusts that the protocol's encryption layer (TLS/SSL) is
    sufficiently secure against eavesdroppers.

### Difference from HTTP

 As opposed to HTTP URLs that begin with "http://" and use port 80 by
default, HTTPS URLs begin with "https://" and use port 443 by default.

HTTP is unsecured and is subject to man-in-the-middle and eavesdropping
attacks, which can let attackers gain access to website accounts and
sensitive information. HTTPS is designed to withstand such attacks and
is considered secure against such.

### HTTPS and Bechtel

 Bechtel requires the use of HTTPS for all access to their APIs.
 
 [Top](#menu)
<a name="Authentication"></a>
### Authentication

[need text!!]
<a name="keys"></a>
### Application Keys

[need text!!]
<a name="getkey"></a>
### How Do I Get My Application Key?

Create an application from the [App Center][]. It's very
quick and easy to do. Registering your application allows us to identify
your application and provide usage analytics.

The key request system does not require approval from an administrator.

  [App Center]: #/appcenter/
<a name="tracking"></a>
### Tracking Usage with your Application Key

Once you have an approved application key, and a released
application, you can track usage by visiting your '[Analytics Drilldown][]' page.
Here, you can see time based analytics and trends for each application.

  [Analytics Drilldown]: #/analytics/drill/
<a name="token"></a>  
### Obtaining an OAuth Token

Before you can consume one of the APIs in the developer API marketplace,
you must first get a user token by logging into your application via
OAuth. [This is a good visual reference on the flow of the OAuth
steps][].

The example below demonstrates how to implement OAuth using Ruby and
Sinatra:

  [This is a good visual reference on the flow of the OAuth steps]: https://portal.mypsn.com/static/oauth_token_flow.pdf
<a name="ssoredirect"></a>  
#### 1. Redirect to Bechtel Login

Direct user to the following URL to initiate the Bechtel Login. Note:
The TargetResource contained in the URL is specific to your application
and is called via a GET request to provide your application with a
Reference ID (i.e. params["REF"])

    https://sso.mypsn.com/sp/startSSO.ping?PartnerIdpId=PSN2-SAML2-Entity&TargetResource=http://localhost:70993/callback
<a name="getREF"></a>
#### 2. Retrieve user **REF**erence

Once you have received the "REF" parameter from your login request via a
callback, you will need to include it in a follow up HTTPS call in the
querystring. Here's an example:

    @url = "https://sso.mypsn.com/ext/ref/pickup?REF=#{params["REF"]}"
                    @jdata = JSON.parse(RestClient.get @url, {"ping.uname"=>"PSN_Username", "ping.pwd"=>"PSN_Password", "ping.instanceId"=>"PSN_Instance"})

Note: This HTTPS call requires the credentials that were provided by
Alan Goho specifically for your application including: ping.uname,
ping.pwd, and PSN2AgentlessDemo

<a name="getUser"></a>
#### 3. Obtain user information

This HTTPS call will return a JSON object of user information about the
person logging into your application including the OAuth token for
subsequent API calls. The OAuth token is only good for 8 hours so I
recommend that you store it in session/state rather than writing it to
the database. Here is an example of storing a few JSON data elements
into a Sinatra session based on the @jdata response from the above
example:

    session["token"] = @jdata["OAuthToken"]
                    session["username"]  = @jdata["BechtelUserName"]
                    session["BechtelEmailAddress"] = @jdata["BechtelEmailAddress"]
                    session["firstname"] = @jdata["FirstName"]
                    session["lastname"] = @jdata["LastName"]
                    session["title"] = @jdata["Title"]
Once you have your session variables created, the user is now
authenticated and logged into your application. You can now redirect the
user to your application's main menu or index page. Remember to clear
these session variables on Logout!
[Top](#menu)
<a name="apiresponse"></a>
### Using API Responses


As the exact format of individual responses with a web service request
is not guaranteed (some elements may be missing or in multiple
locations), you should never assume that the format returned for any
given response will be the same for different queries. Instead, you
should process the response and select appropriate values via
expressions. This section discusses how to extract these values
dynamically from web service responses.

Web Services provide responses which are easy to understand, but not
exactly user friendly. When performing a query, rather than display a
set of data, you probably want to extract a few specific values.
Generally, you will want to parse responses from the web service and
extract only those values which interest you.

The parsing scheme you use depends on whether you are returning output
in XML or JSON. JSON responses, being already in the form of Javascript
objects, may be processed within Javascript itself on the client; XML
responses should be processed using an XML processor and an XML query
language to address elements within the XML format.

<a name="usingXML"></a>
### Using XML in Objective-C

Bechtel APIs will return data in either XML or JSON formats. iOS
supports parsing XML natively. It does not support parsing JSON
natively; however, there are easy-to-use, open source JSON parsers
available on the internet.

The NSXMLParser class will parse XML data in an event-driven manor. It
also defines a protocol called NSXMLParserDelegate that can be used to
implement parsing delegate methods.

##### Prepare for Parsing:
 
 To begin parsing the XML, create an instance of NSXMLParser, assign its
delegate, and send it the parse message. Initialize the parser with the
data that was previously downloaded from the API. Once the parse message
is sent to the parser, the current thread will wait until the parser has
finished before continuing.


    // Create the parser using the downloaded XML data
                NSXMLParser *parser = [[NSXMLParser alloc] initWithData:returnedXMLData];
            
                // Set the delegate of the parser
                [parser setDelegate:self];
            
                // Start parsing – Will wait here until parsing finishes
                [parser parse];

The delegate of the parser will implement the methods defined by the
NSXMLParserDelegate protocol. The class that is assigned as the delegate
must indicate this in its header file.

    @interface SomeClass : NSObject <NSXMLParserDelegate> {
        // ...
    }

##### Parsing the XML:

 There are a number of delegate methods defined in the
NSXMLParserDelegate protocol, but the three that are most commonly
delegated are
`parser:didStartElement:namespaceURI:qualifiedName:attributes:, parser:foundCharacters:`,
and `parser:didEndElement:namespaceURI:qualifiedName:`. These methods
are event-driven and should be implemented by the delegate that was set
previously.

##### Elements:
 
 Each time an XML element tag is found by the parser, it will call the
`parser:didStartElement:namespaceURI:qualifiedName:attributes:` method.
The name of the element is passed as a parameter and can be used to
check which element was found.

	- (void)parser:(NSXMLParser *)parser
                    didStartElement:(NSString *)elementName
                      namespaceURI:(NSString *)namespaceURI
                     qualifiedName:(NSString *)qName
                        attributes:(NSDictionary *)attributeDict {
                        if ([elementName isEqual:@"Element1"]) {
                            // Create mutable string to hold the element's value
                            elementContents = [[NSMutableString alloc] init];
                        }
                        else if ([elementName isEqual:@"Element2"]) {
                            // ...
                        }
                        // etc.
                    }

The `parser:foundCharacters:` method is called one or more times when
the value of an XML element is being parsed. The element value is read
in chunks, and these chunks should be concatenated together using an
NSMutableString object. This object should be an instance variable so
that it can be accessed across multiple methods.

    - (void)parser:(NSXMLParser *)parser foundCharacters:(NSString *)string {
                        if (elementContents) {
                                [elementContents appendString:string];
                        }
                    }

When the closing tag of an element is read, the parser will call the
parser:didEndElement:namespaceURI:qualifiedName: method. The name of the
element is again passed as a parameter and can be used to check which
element tag was just parsed.

    - (void)parser:(NSXMLParser *)parser
                    didEndElement:(NSString *)elementName
                    namespaceURI:(NSString *)namespaceURI
                    qualifiedName:(NSString *)qName {   
                    if ([elementName isEqual:@"Element1"]) {
                        // Add the parsed element value to a collection
                        [parsedElementsArray addObject:elementContents];
                    }
                    else if ([elementName isEqual:@"Element2"]) {
                        // ...
                    }
                    // etc.
        
                    // Clean up the mutable string
                    [elementContents release];
                    elementContents = nil;
                    }

##### Attributes:
 Attributes of an element can be found in the NSDictionary that is
passed to
`parser:didStartElement:namespaceURI:qualifiedName:attributes:` as a
parameter.

    - (void)parser:(NSXMLParser *)parser
                    didStartElement:(NSString *)elementName
                        namespaceURI:(NSString *)namespaceURI
                        qualifiedName:(NSString *)qName
                        attributes:(NSDictionary *)attributeDict {  
                        if ([elementName isEqual:@"Element1"]) {
                            // Check the attribute dictionary for a specific attribute
                            NSString *attribute = [attributeDict objectForKey:@"Attribute1"];
                            if (attribute) {
                                // ...
                            }
                        }
                    }
More information on XML Parsing in Objective-C visit the [Apple
Developer documentation][]

<a name="usingJSON"></a>
### Using JSON in Javascript

JSON is a subset of the object literal notation of JavaScript. Since
JSON is a subset of JavaScript, it can be used in the language with no
muss or fuss.

    var myJSONObject = {"bindings": [
                        {"ircEvent": "PRIVMSG", "method": "newURI", "regex": "^http://.*"},
                        {"ircEvent": "PRIVMSG", "method": "deleteURI", "regex": "^delete.*"},
                        {"ircEvent": "PRIVMSG", "method": "randomURI", "regex": "^random.*"}
                    ]
                };

  [Apple Developer documentation]: http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/XMLParsing/XMLParsing.html

In this example, an object is created containing a single member
"bindings", which contains an array containing three objects, each
containing "ircEvent", "method", and "regex" members.

Members can be retrieved using dot or subscript operators.

    myJSONObject.bindings[0].method    // "newURI"

To convert a JSON text into an object, you can use the eval() function.
eval() invokes the JavaScript compiler. Since JSON is a proper subset of
JavaScript, the compiler will correctly parse the text and produce an
object structure. The text must be wrapped in parens to avoid tripping
on an ambiguity in JavaScript's syntax.

    var myObject = eval('(' + myJSONtext + ')');
The eval function is very fast. However, it can compile and execute any
JavaScript program, so there can be security issues. The use of eval is
indicated when the source is trusted and competent. It is much safer to
use a JSON parser. In web applications over XMLHttpRequest,
communication is permitted only to the same origin that provide that
page, so it is trusted. But it might not be competent. If the server is
not rigorous in its JSON encoding, or if it does not scrupulously
validate all of its inputs, then it could deliver invalid JSON text that
could be carrying dangerous script. The eval function would execute the
script, unleashing its malice.

jQuery contains a JSON parser that takes a well-formed JSON string and
returns the resulting JavaScript object.

    var obj = jQuery.parseJSON('{"name":"John"}');
                alert( obj.name === "John" );
Passing in a malformed JSON string may result in an exception being
thrown. For example, the following are all malformed JSON strings:

-   {test: 1} (test does not have double quotes around it).
-   {'test': 1} ('test' is using single quotes instead of double
    quotes).

Additionally if you pass in nothing, an empty string, null, or
undefined, 'null' will be returned from parseJSON.

<a name="examples"></a>
### Example applications
`The following links are only reachable when connected internally`

- Boilerplate is [here](https://gitlab.becpsn.com/bsi/app-boiler)
- Buzz application (built on top of the boilerplate) is [here](https://gitlab.becpsn.com/bsi/buzz)
- Documents application (built on top of the boilerplate) is [here](https://gitlab.becpsn.com/bsi/documents)

[Top](#menu)
