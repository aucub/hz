+++
title = "Automating Geetest Verification"
date = 2024-02-25
updated = 2024-04-01
[taxonomies]
tags = ["tasteless"]
+++
Automating Geetest Verification Using SeleniumBase
<!-- more -->
Overcoming the challenge of CAPTCHAs is a recurring topic in automation and web scraping. One well-known type is the Geetest CAPTCHA, which asks users to identify and click on images matching a specific hint. This article describes an approach to automate this process using SeleniumBase and other software tools.
## Capturing the Verification Images
The initial task is to capture the images presented by the Geetest CAPTCHA. With SeleniumBase, there are two primary methods:

 - Screenshot Element: This allows us to take a screenshot of a specific element on the page.

```python
self.save_element_as_image_file("[class*='geetest_tip_img']", "tip_image")
self.save_element_as_image_file("[class*='geetest_item_wrap']", "img_image")
```

 - URL Extraction: Sometimes, it's possible to extract the URL of the verification images directly.For Geetest, the image size is standardized at 344x384 pixels. The bottom area of 40x150 pixels provides hints about which images to select.


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

## Extracting Image Features
Once we have the images, we need to analyze them to match the hints with the right pictures. We can extract feature objects from both the to-be-checked images and the hint image. Potential methods include:

 - Direct Use of OCR: Tools like ddddocr can be employed for direct object recognition from images.

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

 - Training a Custom Model: For a more tailored approach, one can train a model using YOLO or other machine learning frameworks.

These objects can then be sorted based on their "x" position.

## Sorting and Matching
After identifying the characteristic object and its location, the next step is to match the verification code object with the prompted object. Here we can employ several techniques:

 - Image Classification Models: One is that you can use a model such as microsoft/resnet-50 to obtain the classification results and then calculate the cosine distance between the image and the cue.

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

 - Vision Models: Alternatively, image recognition can be done via models such as GPT-4-Vision for identifying the objects.

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

## Performing the Clicks
After sorting the images based on the cosine distances or model computations, we proceed with simulating clicks. An important aspect to consider is the coordinate transformation.we must accurately translate the image coordinates to the web element's coordinates to ensure precise interaction with the CAPTCHA.

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

By carefully selecting and using the right combination of image capture, feature extraction, sorting, matching, and interaction techniques, one can effectively automate the process of passing the Geetest verification. 