+++
title = "SeleniumBase QR Code Remote Login: Retrieve Text and Print to Terminal"
date = 2024-04-19T17:55:28+08:00
draft = true
tags = ["tasteless"]
+++
When faced with the need to scan a QR code to log in, you can use SeleniumBase and the qrcode library to automatically retrieve the QR code and print it to the terminal, making it easier for users to scan and log in
<!-- more -->
## Retrieve QR Code Decoding Text
### HTML Analysis

In the HTML content, we can find the img element of the QR code:
```HTML
<div class="qr-img-box"><!----><img src="/wapi/zpweixin/qrcode/getqrcode?content=bosszp-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&amp;w=200&amp;h=200"></div>
```
By analyzing the HTML content, we can decode the QR code to get the `content` parameter of the URL in the `src`.

### Retrieve the content Parameter

Using SeleniumBase, we can use the following Python code to retrieve the `content` parameter:
```python
qr_element: WebElement = self.find_element(selector="qr-img-box", by=By.CLASS_NAME)
qr_src = qr_element.find_element(by=By.TAG_NAME, value="img").get_attribute("src")
content = parse_qs(urlparse(qr_src).query).get("content")[0]
```
Here, we use the `find_element` method to locate the element with the class name `qr-img-box`, then use the `find_element` method to locate the img element and get its `src` attribute. Finally, we use the `parse_qs` and `urlparse` functions to parse the URL and retrieve the `content` parameter.

## Generate QR Code and Print to TTY

Using the qrcode library, we can generate a QR code and print it to TTY:
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
Here, we create a `QRCode` object and set its version, error correction level, box size, and border width. Then, we add the `content` parameter to the QR code and use the `make` method to generate the QR code. Finally, we use the