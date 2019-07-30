---
title: チュートリアル:Python での TensorFlow モデルの実行 - Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Python での TensorFlow モデルの実行
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: areddish
ms.openlocfilehash: ba8cf3392ac2bd3d371e5e1910c6671feba9dedf
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606883"
---
# <a name="tutorial-run-tensorflow-model-in-python"></a>チュートリアル:Python での TensorFlow モデルの実行

Custom Vision Service から [TensorFlow モデルをエクスポート](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)したら、このクイックスタートが、このモデルをローカルで使用して画像を分類する方法を示します。

> [!NOTE]
> このチュートリアルは、イメージ分類のプロジェクトからエクスポートされたモデルにのみ適用されます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを使用するには、次の作業が必要となります。

- Python 2.7 以降または Python 3.5 以降をインストールします。
- pip をインストールします。

続けて次のパッケージをインストールする必要があります。

```
pip install tensorflow
pip install pillow
pip install numpy
pip install opencv-python
```

## <a name="load-your-model-and-tags"></a>モデルとタグを読み込む

ダウンロードした zip ファイルには、model.pb と、labels.txt が含まれています。 これらのファイルは、トレーニング済みモデルと分類ラベルを表します。 最初の手順では、プロジェクトに、モデルを読み込みます。

```Python
import tensorflow as tf
import os

graph_def = tf.GraphDef()
labels = []

# These are set to the default names from exported models, update as needed.
filename = "model.pb"
labels_filename = "labels.txt"

# Import the TF graph
with tf.gfile.GFile(filename, 'rb') as f:
    graph_def.ParseFromString(f.read())
    tf.import_graph_def(graph_def, name='')

# Create a list of labels.
with open(labels_filename, 'rt') as lf:
    for l in lf:
        labels.append(l.strip())
```

## <a name="prepare-an-image-for-prediction"></a>予測の画像を準備する

予測に適した形になるように画像を準備するためのいくつかの手順があります。 これらの手順は、トレーニング中に実行される画像操作に似ています。

### <a name="open-the-file-and-create-an-image-in-the-bgr-color-space"></a>ファイルを開き、BGR 色空間に画像を作成する

```Python
from PIL import Image
import numpy as np
import cv2

# Load from a file
imageFile = "<path to your image file>"
image = Image.open(imageFile)

# Update orientation based on EXIF tags, if the file has orientation info.
image = update_orientation(image)

# Convert to OpenCV format
image = convert_to_opencv(image)
```

### <a name="handle-images-with-a-dimension-1600"></a>ディメンションが 1600 より大きい画像を処理する

```Python
# If the image has either w or h greater than 1600 we resize it down respecting
# aspect ratio such that the largest dimension is 1600
image = resize_down_to_1600_max_dim(image)
```

### <a name="crop-the-largest-center-square"></a>中央の最も大きな四角形をトリミングする

```Python
# We next get the largest center square
h, w = image.shape[:2]
min_dim = min(w,h)
max_square_image = crop_center(image, min_dim, min_dim)
```

### <a name="resize-down-to-256x256"></a>256 x 256 までのサイズを縮小する

```Python
# Resize that square down to 256x256
augmented_image = resize_to_256_square(max_square_image)
```

### <a name="crop-the-center-for-the-specific-input-size-for-the-model"></a>モデルの特定の入力サイズの中央をトリミングする

```Python
# Get the input size of the model
with tf.Session() as sess:
    input_tensor_shape = sess.graph.get_tensor_by_name('Placeholder:0').shape.as_list()
network_input_size = input_tensor_shape[1]

# Crop the center for the specified network_input_Size
augmented_image = crop_center(augmented_image, network_input_size, network_input_size)

```

上記の手順では、次のヘルパー関数を使用します。

```Python
def convert_to_opencv(image):
    # RGB -> BGR conversion is performed as well.
    image = image.convert('RGB')
    r,g,b = np.array(image).T
    opencv_image = np.array([b,g,r]).transpose()
    return opencv_image

def crop_center(img,cropx,cropy):
    h, w = img.shape[:2]
    startx = w//2-(cropx//2)
    starty = h//2-(cropy//2)
    return img[starty:starty+cropy, startx:startx+cropx]

def resize_down_to_1600_max_dim(image):
    h, w = image.shape[:2]
    if (h < 1600 and w < 1600):
        return image

    new_size = (1600 * w // h, 1600) if (h > w) else (1600, 1600 * h // w)
    return cv2.resize(image, new_size, interpolation = cv2.INTER_LINEAR)

def resize_to_256_square(image):
    h, w = image.shape[:2]
    return cv2.resize(image, (256, 256), interpolation = cv2.INTER_LINEAR)

def update_orientation(image):
    exif_orientation_tag = 0x0112
    if hasattr(image, '_getexif'):
        exif = image._getexif()
        if (exif != None and exif_orientation_tag in exif):
            orientation = exif.get(exif_orientation_tag, 1)
            # orientation is 1 based, shift to zero based and flip/transpose based on 0-based values
            orientation -= 1
            if orientation >= 4:
                image = image.transpose(Image.TRANSPOSE)
            if orientation == 2 or orientation == 3 or orientation == 6 or orientation == 7:
                image = image.transpose(Image.FLIP_TOP_BOTTOM)
            if orientation == 1 or orientation == 2 or orientation == 5 or orientation == 6:
                image = image.transpose(Image.FLIP_LEFT_RIGHT)
    return image
```

## <a name="predict-an-image"></a>画像を予測する

画像がテンソルとして準備されたら、予測用モデルを通じて送信できます。

```Python

# These names are part of the model and cannot be changed.
output_layer = 'loss:0'
input_node = 'Placeholder:0'

with tf.Session() as sess:
    try:
        prob_tensor = sess.graph.get_tensor_by_name(output_layer)
        predictions, = sess.run(prob_tensor, {input_node: [augmented_image] })
    except KeyError:
        print ("Couldn't find classification output layer: " + output_layer + ".")
        print ("Verify this a model exported from an Object Detection project.")
        exit(-1)
```

## <a name="view-the-results"></a>結果の確認

モデルを通じて画像テンソルを実行した結果をラベルにマップする必要があります。

```Python
    # Print the highest probability label
    highest_probability_index = np.argmax(predictions)
    print('Classified as: ' + labels[highest_probability_index])
    print()

    # Or you can print out all of the results mapping labels to probabilities.
    label_index = 0
    for p in predictions:
        truncated_probablity = np.float64(np.round(p,8))
        print (labels[label_index], truncated_probablity)
        label_index += 1
```

## <a name="next-steps"></a>次の手順

次に、モバイル アプリケーションにモデルをラップする方法を学びましょう。
* [エクスポートされた Tensorflow モデルを Android アプリケーションで使用する](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [エクスポートされた CoreML モデルを Swift iOS アプリケーションで使用する](https://go.microsoft.com/fwlink/?linkid=857726)
* [エクスポートされた CoreML モデルを Xamarin を使って iOS アプリケーションで使用する](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)
