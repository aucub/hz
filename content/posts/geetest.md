+++
title = "自动完成极验验证"
date = 2024-02-25T17:55:28+08:00
lastmod = 2024-04-01T17:55:28+08:00
tags = ["tasteless"]
+++

使用 SeleniumBase 自动完成极验验证
<!-- more -->

克服 CAPTCHA 的挑战是自动化和网络爬虫中反复出现的话题。极验 CAPTCHA 是一种众所周知的类型，它要求用户识别并点击与特定提示匹配的图像。本文介绍了一种使用 SeleniumBase 和其他软件工具来自动执行此过程的方法。

## 捕获验证图像
第一步是捕获极验 CAPTCHA 展示的图像。使用 SeleniumBase，主要有两种方法：

 - 元素截图：这使我们能够对页面上的特定元素进行截图。

```python
self.save_element_as_image_file("[class*='geetest_tip_img']", "tip_image")
self.save_element_as_image_file("[class*='geetest_item_wrap']", "img_image")
```

 - URL 提取：有时，可以直接提取验证图像的 URL。对于极验，图像大小标准化为 344x384 像素。40x150 像素的底部区域提供了有关选择哪些图像的提示。


```python
element = self.find_element("[class*='geetest_item_wrap']")
style_attribute = element.get_attribute("style")
match = re.search(r"url\(\"(.*?)\"\)", style_attribute)
image_url = match.group(1)
response = requests.get(image_url)
with open("captcha.png", "wb") as file:
    file.write(response.content)
image = Image.open("captcha.png")
width, height = image.size
top_image = image.crop((0, 0, width, 344))
top_image.save("img_image.png")
bottom_image = image.crop((0, 344, 150, height))
bottom_image.save("tip_image.png")
```

## 提取图像特征
获得图像后，我们需要分析它们以将提示与正确的图片匹配。我们可以从要检查的图像和提示图像中提取特征对象。潜在的方法包括：

 - 直接使用 OCR：像 ddddocr 这样的工具可以用于直接从图像中识别对象。

```python
det = ddddocr.DdddOcr(det=True)
with open(image_path, "rb") as f:
    image = f.read()
poses = det.detection(image)
im = cv2.imread(image_path)
i = 0
if sort:
    poses = sorted(poses, key=lambda box: box[0])
for box in poses:
    x1, y1, x2, y2 = box
    cv2.imwrite(path + str(i) + ".jpg", im[y1:y2, x1:x2])
    i += 1
return poses
```

 - 训练自定义模型：对于更定制的方法，可以使用 YOLO 或其他机器学习框架训练模型。

然后可以根据这些对象的“x”位置对它们进行排序。

## 排序和匹配
识别特征对象及其位置后，下一步是将验证代码对象与提示对象匹配。这里我们可以采用几种技术：

 - 图像分类模型：一种方法是，可以使用 microsoft/resnet-50 等模型来获取分类结果，然后计算图像与提示之间的余弦距离。



```python
with Image.open(image_path) as img:
    byte_arr = io.BytesIO()
    img.save(byte_arr, format="JPEG")
    byte_arr = byte_arr.getvalue()
files = {"image": (image_path, byte_arr, "image/jpeg")}
url = os.getenv("IMAGE_CLASSIFICATION_URL")
response = requests.post(url, files=files)
if response.ok:
    return response.json()
```

 - 视觉模型：或者，可以通过 GPT-4-Vision 等模型进行图像识别来识别物体。

```python
with open(image1_path, "rb") as image_file:
    base64_image1 = base64.b64encode(image_file.read()).decode("utf-8")
with open(image2_path, "rb") as image_file:
    base64_image2 = base64.b64encode(image_file.read()).decode("utf-8")
response = client.chat.completions.create(
    model="gpt-4-vision-preview",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "text",
                    "text": "Are the objects in these two pictures similar? Please provide a 'true' o'false' result without any explanation",
                },
                {
                    "type": "image_url",
                    "image_url": {
                        "url": f"data:image/jpeg;base64,{base64_image1}"
                    },
                },
                {
                    "type": "image_url",
                    "image_url": {
                        "url": f"data:image/jpeg;base64,{base64_image2}"
                    },
                },
            ],
        }
    ],
    max_tokens=300,
)
result = response.choices[0].message.content
result = result.lower()
if "true" in result:
    return 1.0
else:
    return -0.9
```

## 执行点击
根据余弦距离或模型计算对图像进行排序后，我们继续模拟点击。需要考虑的一个重要方面是坐标转换。我们必须准确地将图像坐标转换为网页元素的坐标，以确保与 CAPTCHA 精确交互。

```python
for click in click_lists:
    x1, y1, x2, y2 = click
    self.click_with_offset(
        "[class*='geetest_item_wrap']",
        (x1 + x2) / 2 / scale_width,
        (y1 + y2) / 2 / scale_height,
    )
self.click(
    "body > div.geetest_panel.geetest_wind > div.geetest_panel_box.geetest_no_loggeetest_panelshowclick > div.geetest_panel_next > div > div > div.geetest_panel > a"
)
```

通过仔细选择和使用图像捕获、特征提取、排序、匹配和交互技术的正确组合，可以有效地自动化通过极验验证的过程。 

