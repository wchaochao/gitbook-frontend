# 图片格式

标签（空格分隔）： 前端知识体系

---

## 分类

* 无压缩：不对图片数据进行压缩处理，如bmp
* 无损压缩：对图片所有数据进行压缩，在保证图片质量的同时降低图片的尺寸，如png
* 有损压缩：对图片数据压缩时除去人眼无法识别的图片细节，在同等图片质量下大幅降低图片尺寸，如jpg

![图片选择](https://segmentfault.com/img/bV5bmb?w=605&h=347)

## 颜色编码

### RGB

R（红）、G（绿）、B（蓝）叠加得到各种各样的颜色

* RGB24格式：每个像素使用3个字节表示，R、G、B各占一个字节
* ARGB32格式：每个像素使用4个字节表示，R、G、B、A（透明度）各占一个字节

### 索引色

使用调色板存储RGB颜色表

* 黑白色
* 16色
* 256色

### YUI

颜色使用亮度、色调、饱和度来表示

* 亮度Y：RGB信号的特定部分叠加到一起
* sediao

## BMP

Bitmap，不压缩的位图

* 编码简单
* 图片尺寸大

### 文件格式

* 位图文件头：14字节
* 位图信息头：40字节
* 调色板：索引色对应的颜色索引表，可选
* 图形数据

### 位图文件头

| 字段 | 说明 | 占用空间 |
| --- | --- | --- |
| bfType | 文件类型，为`BM` | 2字节 |
| bfSize | 文件大小，单位为字节 | 4字节 |
| bfReserved1 | 保留字1，为0 | 2字节 |
| bfReserved2 | 保留字2，为0 | 2字节 |
| bfOffbits | 图像数据的偏移数，位图文件头+位图信息头+调色板的大小 | 4字节 |

### 位图信息头

| 字段 | 说明 | 占用空间 |
| --- | --- | --- |
| biSize | 位图信息头大小，为`40` | 4字节 |
| biWidth | 位图宽度，单位为像素 | 4字节 |
| biHeight | 位图高度，单位为像素<br/>为正时，自底向顶扫描，倒像<br/>为负时，自顶向底扫描，正像 | 4字节 |
| biPlanes | 调色板数目，为`1` | 2字节 |
| biBitCount | 像素的比特数 1-黑白图，4-16色，8-256色，24-真彩色，32-带alpha通道 | 2字节 |
| biCompression | 压缩方式，为0，不压缩 | 4字节 |
| biSizeImage | 位图占用的字节数，不压缩时可设为0 | 4字节 |
| biXPelsPerMeter | 水平分辨率（像素/米） | 4字节 |
| biYPelsPerMeter | 垂直分辨率（像素/米） | 4字节 |
| biClrUsed | 位图使用颜色数，0代表2的biBitCount次方 | 4字节 |
| biClrImportant | 位图中重要的颜色数，0代表都重要 | 4字节 |

### 调色板

颜色索引表，二维数组

* 每个索引对应颜色的R、G、B、A数组

### 图形数据

* 使用索引色时，每个字节对应一个像素的颜色索引
* 使用RGB24时，每三个字节对应一个像素的BGR
* 使用ARGB32时，每四个字节对应一个像素的BGRA

### 对齐规则

BMP要求每行的数据是四的倍数，实际每行的数据为

```javascript
Math.ceil((BitsPerPixel * ImageWidth) / 32) * 4
```

## gif

无损的基于索引色的图片格式

* 支持透明和动画
* 只支持256种颜色
* 适合色彩简单的logo、icon、动图

## jpg

有损的基于真彩色的图片格式

* 色彩丰富
* 反复保存图片质量下降明显
* 适合色彩丰富图片、渐变图片

## png

无损的位图格式

* 支持透明，简单图片尺寸小
* 不支持动画，色彩丰富的图片尺寸大
* 适合logo、icon、透明图

## webp

* 支持有损、无损压缩，支持透明和动画
* 兼容性不好
* 适合支持webp格式的app和webview