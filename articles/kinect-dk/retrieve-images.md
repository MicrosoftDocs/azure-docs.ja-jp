---
title: Azure Kinect 画像データを取得する
description: Kinect Sensor SDK を使用して Azure Kinect 画像データを取得する方法について説明します。
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, 取得, センサー, カメラ, SDK, 深度, RGB, 画像, カラー, キャプチャ, 解像度, バッファー
ms.openlocfilehash: fed5c7340d287d9103ba35f0fd3d80c0fff6e3ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "87538916"
---
# <a name="retrieve-azure-kinect-image-data"></a>Azure Kinect 画像データを取得する

このページでは、Azure Kinect から画像を取得する方法に関する詳細情報を提供します。 この記事では、デバイスのカラーと深度の間で調整された画像をキャプチャしてアクセスする方法を示します。 画像にアクセスするには、まずデバイスを開いて構成する必要があります。その後に、画像をキャプチャできます。
画像を構成してキャプチャする前に、[デバイスを検索して開く](find-then-open-device.md)必要があります。

また、この記事にある関数を使用する方法を示す [SDK ストリーミングの例](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming)を参照することもできます。

次の関数を扱っています。

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>デバイスを構成して起動する

Kinect デバイスで使用できる 2 つのカメラは、複数のモード、解像度、出力形式をサポートしています。 完全な一覧については、Azure Kinect 開発キットの[ハードウェアの仕様](hardware-specification.md)を参照してください。

ストリーミング構成は、[`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 構造体の値を使用して設定されます。

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;
config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start device\n");
    goto Exit;
}
```

カメラは起動された後、[`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) が呼び出されるか、またはデバイスが閉じられるまで引き続きデータをキャプチャします。

## <a name="stabilization"></a>安定化

複数デバイスの同期機能を使用してデバイスを起動する場合は、それを固定された露出設定を使用して行うことを強くお勧めします。
手動露出が設定されている場合は、画像とフレーム レートが安定するまでにデバイスからの最大 8 キャプチャが必要になることがあります。 自動露出の場合は、画像とフレーム レートが安定するまでに最大 20 キャプチャが必要になることがあります。

## <a name="get-a-capture-from-the-device"></a>デバイスからキャプチャを取得する

画像は、関連付けられた方法でデバイスからキャプチャされます。 キャプチャされた各画像には、深度画像、IR 画像、カラー画像、または画像の組み合わせが含まれます。

既定では、API は、ストリーミング モードで要求されたすべての画像を受信した後でしかキャプチャを返しません。 [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) の [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) パラメーターをクリアすることによって、深度またはカラー画像のみを含む部分的なキャプチャを、それが使用可能になったらすぐに返すように API を構成できます。

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

API がキャプチャを正常に返した後、キャプチャ オブジェクトの使用を完了したら [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) を呼び出す必要があります。

## <a name="get-an-image-from-the-capture"></a>キャプチャから画像を取得する

キャプチャされた画像を取得するには、画像の種類ごとに適切な関数を呼び出します。 つぎのいずれかです。

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

この画像の使用を完了したら、これらの関数によって返されたすべての [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) ハンドルで [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f) を呼び出す必要があります。

## <a name="access-image-buffers"></a>画像バッファーにアクセスする

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) には、画像のプロパティを取得するための多くのアクセサー関数があります。

画像のメモリ バッファーにアクセスするには、[k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e) を使用します。

次の例は、キャプチャされた深度画像にアクセスする方法を示しています。 この同じ原則が他の画像の種類にも適用されます。 ただし、image-type 変数を正しい画像の種類 (IR や color など) に確実に置き換えてください。

```C
// Access the depth16 image
k4a_image_t image = k4a_capture_get_depth_image(capture);
if (image != NULL)
{
    printf(" | Depth16 res:%4dx%4d stride:%5d\n",
            k4a_image_get_height_pixels(image),
            k4a_image_get_width_pixels(image),
            k4a_image_get_stride_bytes(image));

    // Release the image
    k4a_image_release(image);
}

// Release the capture
k4a_capture_release(capture);
```

## <a name="next-steps"></a>次のステップ

これで、Azure Kinect デバイスを使用してカメラの画像をキャプチャし、カラーと深度の間で調整する方法がわかりました。 また、次のことも可能です。

>[!div class="nextstepaction"]
>[IMU サンプルを取得する](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[マイクにアクセスする](access-mics.md)
