# Curl wrapper in Bash

Simple curl wrapper which allows setting most commonly used option while not preventing the user to append his own ones.
Provides a nice output with timings or colored JSON (when commands are prefixed by J)

Can be used to GET content but also to manipulate JSON queries, with JPOST,
JGET, JDELETE, JPUT, JUP/JUPLOAD (for upload)

```
GET https://www.bedis9.net/

Response headers:
HTTP/1.1 200 OK
Server: nginx/1.11.3
Date: Thu, 25 Oct 2018 11:47:34 GMT
Content-Type: text/html
Content-Length: 12065
Last-Modified: Tue, 04 Jul 2017 22:05:03 GMT
ETag: "595c110f-2f21"
Accept-Ranges: bytes
Strict-Transport-Security: max-age=16000000; includeSubDomains; preload;

Remote IP: 37.187.96.49
SSL verify result: 0
Timers:
  DNS lookup:           0,510
  TCP handshake:        0,772
  SSL handshake:        2,397
  Total:                2,639
```


## installation
Simply download GET bash script and run:
  ```
  chmod +x GET
  GET --install
  ```

This step will install GET in your $HOME/bin directory and also create the relevent symlinks.

## Usage

Usually, this script is used during demos or for troubleshooting web applications and APIs.

### Simple GET commands
This is the very first usage of this script:

```
GET https://www.bedis9.net/

Response headers:
HTTP/1.1 200 OK
Server: nginx/1.11.3
Date: Thu, 25 Oct 2018 11:47:34 GMT
Content-Type: text/html
Content-Length: 12065
Last-Modified: Tue, 04 Jul 2017 22:05:03 GMT
ETag: "595c110f-2f21"
Accept-Ranges: bytes
Strict-Transport-Security: max-age=16000000; includeSubDomains; preload;



Remote IP: 37.187.96.49
SSL verify result: 0
Timers:
  DNS lookup:           0,510
  TCP handshake:        0,772
  SSL handshake:        2,397
  Total:                2,639
```

### Using with REST/JSON API

#### JGET
Simply call ```JGET``` with the relevent URL.
The JSON content will be saved locally to the file **out.json** (that you can modify and re-use with orther J* commands)

```
JGET --header "X-Consul-Token: mastertoken" http://dashboard.consulconnect.haproxy.local/v1/catalog/service/consul

Response headers:
HTTP/1.1 200 OK
Content-Type: application/json
Vary: Accept-Encoding
X-Consul-Effective-Consistency: leader
X-Consul-Index: 12
X-Consul-Knownleader: true
X-Consul-Lastcontact: 0
Date: Thu, 25 Oct 2018 11:57:21 GMT
Content-Length: 464

JSON content:
[
  {
    "ID": "ea9053c6-2458-d79f-8b0a-a39a188f774d",
    "Node": "server",
    "Address": "10.42.42.2",
    "Datacenter": "dc1",
    "TaggedAddresses": null,
    "NodeMeta": null,
    "ServiceKind": "",
    "ServiceID": "consul",
    "ServiceName": "consul",
    "ServiceTags": [],
    "ServiceAddress": "",
    "ServiceWeights": {
      "Passing": 1,
      "Warning": 1
    },
    "ServiceMeta": {},
    "ServicePort": 8300,
    "ServiceEnableTagOverride": false,
    "ServiceProxyDestination": "",
    "ServiceConnect": {
      "Native": false,
      "Proxy": null
    },
    "CreateIndex": 12,
    "ModifyIndex": 12
  }
]
```

### JPUT
This command executes a PUT on a JSON/REST API endpoint. It takes the JSON content from the file **in.json** in your current $PATH.

```
cat <<EOF >in.json
{
  "ID": "www1",
  "Name": "publicservices",
  "tags": ["www", "api"],
  "Address": "10.0.0.1",
  "Port": 80,
  "check": {
    "http": "10.0.0.1:80",
    "interval": "10s",
    "timeout": "1s"
   }
}
EOF
JPUT http://consul-server.docker/v1/agent/service/register
```

### JDELETE
This command execute a DELETE on a JSON/REST API endpoint. It expects JSON in return, so it may sometime return an error if the server returns plain text...

```
JDELETE --header "X-Consul-Token: mastertoken" http://dashboard.consulconnect.haproxy.local/v1/catalog/service/consul

Response headers:
HTTP/1.1 405 Method Not Allowed
Allow: OPTIONS,GET
Vary: Accept-Encoding
Date: Thu, 25 Oct 2018 12:03:50 GMT
Content-Length: 25
Content-Type: text/plain; charset=utf-8

JSON content:
parse error: Invalid numeric literal at line 1, column 7
parse error: Invalid numeric literal at line 1, column 7
```

### JPOST
This command execute a POST on a JSON/REST API endpoint. It takes the JSON content from the file **in.json** in your current $PATH.

