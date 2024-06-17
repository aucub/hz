+++
title = "SeleniumBase 监听网络请求"
date = 2024-04-28T17:55:28+08:00
tags = ["tasteless"]
+++
当我们需要获取浏览器网络请求信息时，SeleniumBase 提供了便捷的方式来实现网络请求监听。本文将介绍如何使用 SeleniumBase 开启 CDP 事件日志、监听网络请求、获取响应内容以及关闭监听。
<!-- more -->
## 开启 CDP 事件日志

首先，我们需要通过以下参数开启 CDP 事件日志：

```
--uc-cdp-events --log-cdp
```

## 开启监听网络

使用 SeleniumBase，我们可以通过简单的 Python 代码开启网络监听功能：

```python
self.execute_cdp_cmd("Network.enable", {})
```

## 添加事件监听器

接下来，我们需要添加事件监听器来捕获网络请求事件。以下代码展示了如何监听 `Network.responseReceived` 事件，即收到响应的事件：

```python
def add_cdp_listener(self):
    self.driver.add_cdp_listener(
        "Network.responseReceived", lambda data: {self.cdp_callback(data)}
    )
```

## 处理事件回调

下面的 `cdp_callback` 函数用于处理监听事件的回调，我们可以从中获取请求的 URL 和 requestId 等信息：

```python
def cdp_callback(self, data):
    params = data["params"]
    url = params.get("response", {}).get("url")
    requestId = params["requestId"]
```

## 获取响应内容

通过 requestId，我们可以使用以下代码获取请求的响应体：

```python
response = self.driver.execute_cdp_cmd(
    "Network.getResponseBody",
    {"requestId": requestId},
)
if not response["base64Encoded"]:
    body = json.loads(response["body"])
```

## 关闭监听网络

最后，我们需要禁用网络功能并删除监听器：

```python
self.driver.clear_cdp_listeners()
self.execute_cdp_cmd("Network.disable", {})
```
