<!--
#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
-->

[中文](response-rewrite-cn.md)
# response-rewrite

response rewrite plugin, rewrite the content from upstream.

senario:
1、can set `Access-Control-Allow-*` series field to support CORS(Cross-origin Resource Sharing) .
2、we can set customized `status_code` and `Location` field in header to achieve redirect, you can alse use [redirect](redirect-cn.md) plugin if you just want a redirect function.

### Parameters
|Name    |Required|Description|
|-------         |-----|------|
|status_code   |No| New `status code` to client|
|body          |No| New `body` to client, and the content-length will be reset too.|
|headers             |No| Set the new `headers` for client, can set up multiple. If it exists already from upstream, will rewrite the header, otherwise will add the header. You can set the corresponding value to an empty string to remove a header. |

### Example

#### Enable Plugin
Here's an example, enable the `response rewrite` plugin on the specified route:

```shell
curl http://127.0.0.1:9080/apisix/admin/routes/1 -X PUT -d '
{
    "methods": ["GET"],
    "uri": "/test/index.html",
    "plugins": {
        "response-rewrite": {
            "body": "{\"code\":\"ok\",\"message\":\"new json body\"}",
            "headers": {
                "X-Server-id": 3,
                "X-Server-status": "on"
            }
        }
    },
    "upstream": {
        "type": "roundrobin",
        "nodes": {
            "127.0.0.1:80": 1
        }
    }
}'
```

#### Test Plugin
Testing based on the above examples :
```shell
curl -X GET -i  http://127.0.0.1:9080/test/index.html
```

It will output like below,no matter what kinf of content from upstream.
```
HTTP/1.1 200 OK
Date: Sat, 16 Nov 2019 09:15:12 GMT
Transfer-Encoding: chunked
Connection: keep-alive
X-Server-id: 3
X-Server-status: on

{"code":"ok","message":"new json body"}
```

This means that the `response rewrite` plugin is in effect.
