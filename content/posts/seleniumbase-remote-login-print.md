+++
title = "SeleniumBase 二维码远程登录：获取文本并打印到终端"
date = 2024-04-19
updated = 2024-04-19
draft = true
[taxonomies]
tags = ["tasteless"]
+++
当遇到需要扫描二维码登录的情况，可以使用 SeleniumBase 和 qrcode 库实现自动获取二维码并打印到终端，方便用户扫描登录
<!-- more -->
## 获得二维码解码文本
### HTML 分析

在 HTML 内容中，我们可以找到二维码的 img 元素：
```HTML
<div class="qr-img-box"><!----><img src="/wapi/zpweixin/qrcode/getqrcode?content=bosszp-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&amp;w=200&amp;h=200"></div>
```
通过分析 HTML 内容，我们可以得到二维码解码为 `src` 的 URL 的 `content` 参数。

### 获取 content 参数

使用 SeleniumBase，我们可以使用以下 Python 代码来获取 `content` 参数：
```python
qr_element: WebElement = self.find_element(selector="qr-img-box", by=By.CLASS_NAME)
qr_src = qr_element.find_element(by=By.TAG_NAME, value="img").get_attribute("src")
content = parse_qs(urlparse(qr_src).query).get("content")[0]
```
这里，我们使用 `find_element` 方法来定位 `qr-img-box` 类名的元素，然后使用 `find_element` 方法来定位 img 元素，并获取其 `src` 属性。最后，我们使用 `parse_qs` 和 `urlparse` 函数来解析 URL 并获取 `content` 参数。

## 生成二维码并打印到 TTY

使用 qrcode 库，我们可以生成二维码并打印到 TTY：
```python
qr = qrcode.QRCode(
    version=1,
    error_correction=qrcode.constants.ERROR_CORRECT_L,
    box_size=10,
    border=4,
)
qr.add_data(content)
qr.make(fit=True)
qr.print_tty()
```
这里，我们创建了一个 `QRCode` 对象，并设置了其版本、错误纠正级别、框大小和边框宽度。然后，我们将 `content` 参数添加到二维码中，并使用 `make` 方法生成二维码。最后，我们使用 `print_tty` 方法将二维码打印到 TTY。
