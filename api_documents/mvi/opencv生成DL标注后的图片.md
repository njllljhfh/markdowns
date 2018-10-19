# OpenCV 生成DL标注后的图片

## 1、OpenCV输入参数

```python
参数1： img_array    # 原图像的数组
参数2： [
    {
    "bbox":[x,y,w,h],  # bbox
    "color":[r,g,b],  # bbox颜色
    "line_width":5  # bbox的线宽
    },
    {
    "bbox":[x,y,w,h],
    "color":[r,g,b],
    "line_width":5
    },
]
```

img_data = cv2.imdecode(np.fromstring(img_data_byte, np.uint8), -1)