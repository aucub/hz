+++
title = "SeleniumBase Network Request Listening"
date = 2024-04-28T17:55:28+08:00
tags = ["tasteless"]
+++
When we need to get browser network request information, SeleniumBase provides a convenient way to implement network request listening. This article will introduce how to use SeleniumBase to enable CDP event logs, listen to network requests, get response content, and close listening.
<!-- more -->
## Enable CDP Event Logs

First, we need to enable CDP event logs with the following parameters:

```
--uc-cdp-events --log-cdp
```

## Enable Network Listening

Using SeleniumBase, we can enable network listening functionality with simple Python code:

```python
self.execute_cdp_cmd("Network.enable", {})
```

## Add Event Listener

Next, we need to add an event listener to capture network request events. The following code shows how to listen to the `Network.responseReceived` event, which is the event when a response is received:

```python
def add_cdp_listener(self):
    self.driver.add_cdp_listener(
        "Network.responseReceived", lambda data: {self.cdp_callback(data)}
    )
```

## Handle Event Callback

The following `cdp_callback` function is used to handle the callback of the listening event, from which we can get information such as the request URL and requestId:

```python
def cdp_callback(self, data):
    params = data["params"]
    url = params.get("response", {}).get("url")
    requestId = params["requestId"]
```

## Get Response Content

Through requestId, we can use the following code to get the response body of the request:

```python
response = self.driver.execute_cdp_cmd(
    "Network.getResponseBody",
    {"requestId": requestId},
)
if not response["base64Encoded"]:
    body = json.loads(response["body"])
```

## Close Network Listening

Finally, we need to disable network functionality and remove the listener:

```python
self.driver.clear_cdp_listeners()
self.execute_cdp_cmd("Network.disable", {})
```



