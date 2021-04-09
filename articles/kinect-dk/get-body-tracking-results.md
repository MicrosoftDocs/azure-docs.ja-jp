---
title: Azure Kinect のボディ トラッキング結果の取得
description: Azure Kinect Body Tracking SDK を使用して、ボディ トラッキングの結果を取得する方法について説明します。
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, センサー, sdk, ボディ, トラッキング, 関節
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277039"
---
# <a name="get-body-tracking-results"></a>体のトラッキングの結果を表示する

Body Tracking SDK は、ボディ トラッカー オブジェクトを使用して Azure Kinect DK のキャプチャを処理し、ボディ トラッキングの結果を生成します。 また、トラッカー、処理キュー、および出力キューのグローバル ステータスも保持されます。 ボディ トラッカーを使用するには、次の 3 つの手順を実行します。

- トラッカーの作成
- Azure Kinect DK を使用した深度と IR 画像のキャプチャ
- キャプチャをエンキューして結果をポップする。

## <a name="create-a-tracker"></a>トラッカーの作成


ボディ トラッキングを使用する最初の手順は、トラッカーを作成することです。これには、センサー調整 [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) 構造体を渡す必要があります。 センサーの調整は、Azure Kinect Sensor SDK [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) 機能を使用してクエリできます。

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>深度と IR 画像のキャプチャ

Azure Kinect DK を使用した画像のキャプチャについては、「[イメージの取得](retrieve-images.md)」ページで説明されています。

>[!NOTE]
> 最適なパフォーマンスと精度を得るには、`K4A_DEPTH_MODE_NFOV_UNBINNED` または `K4A_DEPTH_MODE_WFOV_2X2BINNED` モードを使用することをお勧めします。 `K4A_DEPTH_MODE_OFF` モードまたは `K4A_DEPTH_MODE_PASSIVE_IR` モードは使用しないでください。

サポートされている Azure Kinect DK モードについては、Azure Kinect DK の[ハードウェアの仕様](hardware-specification.md)に関するページと「[k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d)」列挙型を参照してください。

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

## <a name="enqueue-the-capture-and-pop-the-results"></a>キャプチャをエンキューして結果をポップする

トラッカーでは、Azure Kinect DK キャプチャをより効率的に非同期で処理するために、入力キューと出力キューを内部で保持します。 新しいキャプチャを入力キューに追加するには、[k4abt_tracker_enqueue_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e) 関数を使用します。 [k4abt_tracker_pop_result()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b) 関数を使用するか、出力キューから結果をポップします。 タイムアウト値の使用はアプリケーションによって異なり、キューの待機時間を制御します。

### <a name="real-time-processing"></a>リアルタイム処理
このパターンは、リアルタイムの結果を必要とし、フレームの欠落に対応できるシングルスレッド アプリケーションで使用します。 [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) にある `simple_3d_viewer` サンプルは、リアルタイム処理の一例です。

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>同期処理
このパターンは、リアルタイムの結果を必要としないアプリケーションや、フレームの欠落に対応できないアプリケーションに使用します。

処理スループットが制限される場合があります。

[GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) にある `simple_sample.exe` サンプルは、同期処理の一例です。

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[ボディ フレームのデータにアクセスする](access-data-body-frame.md)
