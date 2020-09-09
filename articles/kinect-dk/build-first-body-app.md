---
title: クイック スタート:体をトラッキングする Azure Kinect アプリケーションの作成
description: 体をトラッキングする最初の Azure Kinect アプリケーションを作成するためのステップ バイ ステップの指示
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Kinect, Azure, センサー, SDK, 体, トラッキング, 関節, アプリケーション, 最初
ms.openlocfilehash: bdf8ee7a14bf59a151dfa316b11159830b4f63b8
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "85277842"
---
# <a name="quickstart-build-an-azure-kinect-body-tracking-application"></a>クイック スタート:体をトラッキングする Azure Kinect アプリケーションの作成

Body Tracking SDK へようこそ。 このクイックスタートでは、体のトラッキングを準備し、実行します。 その他の例については、こちらの [Azure-Kinect-Sample リポジトリ](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples)をご覧ください。

## <a name="prerequisites"></a>前提条件

- [Azure Kinect DK を設定する](set-up-azure-kinect-dk.md)
- [Body Tracking SDK を設定する](body-sdk-setup.md)
- [初めての Azure Kinect アプリケーションを作成する](build-first-app.md)方法のクイックスタートを完了する。
- Sensor SDK の次の関数について理解する。
  - [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
  - [k4a_device_start_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
  - [k4a_device_stop_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  - [k4a_device_close()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)
- Body Tracking SDK の次の関数のドキュメントを確認する。
  - [k4abt_tracker_create()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1aa71481b8441def94de11b29e0e3cbc.html#ga1aa71481b8441def94de11b29e0e3cbc)
  - [k4abt_tracker_enqueue_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e)
  - [k4abt_tracker_pop_result()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b)
  - [k4abt_tracker_shutdown()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga94036969ef94cbc414c78b3f6d04bfa5.html#ga94036969ef94cbc414c78b3f6d04bfa5)
  - [k4abt_tracker_destroy()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gab2c9afca092130976cd66077c0557ed1.html#gab2c9afca092130976cd66077c0557ed1)

## <a name="headers"></a>ヘッダー

体のトラッキングでは、1 つのヘッダー `k4abt.h` を使用します。 `k4a.h` に加えてこのヘッダーをインクルードします。 使用するコンパイラが、Sensor SDK と Body Tracking SDK 両方の `lib` および `include` フォルダーにセットアップされていることを確認します。 `k4a.lib` および `k4abt.lib` ファイルにリンクする必要もあります。 アプリケーションを実行するには、`k4a.dll`、`k4abt.dll`、`onnxruntime.dll`、`dnn_model.onnx` がアプリケーションの実行パスに必要です。

```C
#include <k4a/k4a.h>
#include <k4abt.h>
```

## <a name="open-device-and-start-the-camera"></a>デバイスを開いてカメラを起動する

体をトラッキングする最初のアプリケーションでは、1 台の Azure Kinect デバイスが PC に接続されていると想定します。

体のトラッキングは Sensor SDK に基づきます。 体のトラッキングを使用するには、まずデバイスを開いて構成する必要があります。 [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) 関数を使用してデバイスを開いた後、[k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) オブジェクトを使用してそのデバイスを構成します。 最良の結果を得るには、深度モードを `K4A_DEPTH_MODE_NFOV_UNBINNED` または `K4A_DEPTH_MODE_WFOV_2X2BINNED` に設定します。 深度モードが `K4A_DEPTH_MODE_OFF` または `K4A_DEPTH_MODE_PASSIVE_IR` に設定されている場合、体のトラッカーは実行されません。

デバイスを見つけて開く方法の詳細については、[こちらのページ](find-then-open-device.md)を参照してください。

Azure Kinect の深度モードの詳細については、[ハードウェアの仕様](hardware-specification.md)および [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) 列挙型に関するページを参照してください。

```C
k4a_device_t device = NULL;
k4a_device_open(0, &device);

// Start camera. Make sure depth camera is enabled.
k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
k4a_device_start_cameras(device, &deviceConfig);
```

## <a name="create-the-tracker"></a>トラッカーを作成する

体のトラッキング結果を取得するための最初の手順は、体のトラッカーを作成することです。 それには、センサーの較正の [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) 構造体が必要です。 センサーの較正は [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) 関数を使用してクエリできます。

```C
k4a_calibration_t sensor_calibration;
k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration);

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker);
```

## <a name="get-captures-from-the-azure-kinect-device"></a>Azure Kinect デバイスからキャプチャを取得する

画像データの取得について、詳しくは[こちらのページ](retrieve-images.md)をご覧ください。

```C
// Capture a depth frame
k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS);
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>キャプチャをエンキューして結果をポップする

トラッカーでは、Azure Kinect DK キャプチャをより効率的に非同期で処理するために、入力キューと出力キューを内部で保持します。 次の手順では、`k4abt_tracker_enqueue_capture()` 関数を使用して、新しいキャプチャを入力キューに追加します。 出力キューから結果をポップするには、`k4abt_tracker_pop_result()` 関数を使用します。 タイムアウト値はアプリケーションによって異なり、キューの待機時間を制御します。

体をトラッキングする最初のアプリケーションでは、リアルタイム処理パターンを使用します。 他のパターンの詳しい説明は、[体のトラッキング結果の取得](get-body-tracking-results.md)に関するページをご覧ください。

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

## <a name="access-the-body-tracking-result-data"></a>体のトラッキング結果データにアクセスする

体のトラッキング結果は、センサーのキャプチャごとに、体格の [k4abt_frame_t](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/structk4abt__frame__t.html) 構造体に格納されます。 それぞれの体格には、体の構造体のコレクション、2D の体インデックス マップ、入力キャプチャの 3 つの主要コンポーネントが含まれます。

体をトラッキングする最初のアプリケーションでは、検出された体の数のみにアクセスします。 体格のデータの詳しい説明は、[体格のデータへのアクセス](access-data-body-frame.md)に関するページをご覧ください。

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
printf("%zu bodies are detected!\n", num_bodies);
```

## <a name="clean-up"></a>クリーンアップ

最後の手順では、体のトラッカーをシャットダウンし、体をトラッキングするオブジェクトを解放します。 デバイスを停止して閉じる必要もあります。

```C
k4abt_tracker_shutdown(tracker);
k4abt_tracker_destroy(tracker);
k4a_device_stop_cameras(device);
k4a_device_close(device);
```

## <a name="full-source"></a>完全なソース

```C
#include <stdio.h>
#include <stdlib.h>

#include <k4a/k4a.h>
#include <k4abt.h>

#define VERIFY(result, error)                                                                            \
    if(result != K4A_RESULT_SUCCEEDED)                                                                   \
    {                                                                                                    \
        printf("%s \n - (File: %s, Function: %s, Line: %d)\n", error, __FILE__, __FUNCTION__, __LINE__); \
        exit(1);                                                                                         \
    }                                                                                                    \

int main()
{
    k4a_device_t device = NULL;
    VERIFY(k4a_device_open(0, &device), "Open K4A Device failed!");

    // Start camera. Make sure depth camera is enabled.
    k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
    deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
    VERIFY(k4a_device_start_cameras(device, &deviceConfig), "Start K4A cameras failed!");

    k4a_calibration_t sensor_calibration;
    VERIFY(k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration),
        "Get depth camera calibration failed!");

    k4abt_tracker_t tracker = NULL;
    k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
    VERIFY(k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker), "Body tracker initialization failed!");

    int frame_count = 0;
    do
    {
        k4a_capture_t sensor_capture;
        k4a_wait_result_t get_capture_result = k4a_device_get_capture(device, &sensor_capture, K4A_WAIT_INFINITE);
        if (get_capture_result == K4A_WAIT_RESULT_SUCCEEDED)
        {
            frame_count++;
            k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
            k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
            if (queue_capture_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                // It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Add capture to tracker process queue timeout!\n");
                break;
            }
            else if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
            {
                printf("Error! Add capture to tracker process queue failed!\n");
                break;
            }

            k4abt_frame_t body_frame = NULL;
            k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
            if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
            {
                // Successfully popped the body tracking result. Start your processing

                size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
                printf("%zu bodies are detected!\n", num_bodies);

                k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
            }
            else if (pop_frame_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                //  It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Pop body frame result timeout!\n");
                break;
            }
            else
            {
                printf("Pop body frame result failed!\n");
                break;
            }
        }
        else if (get_capture_result == K4A_WAIT_RESULT_TIMEOUT)
        {
            // It should never hit time out when K4A_WAIT_INFINITE is set.
            printf("Error! Get depth frame time out!\n");
            break;
        }
        else
        {
            printf("Get depth capture returned error: %d\n", get_capture_result);
            break;
        }

    } while (frame_count < 100);

    printf("Finished body tracking processing!\n");

    k4abt_tracker_shutdown(tracker);
    k4abt_tracker_destroy(tracker);
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[体のトラッキングの結果を表示する](get-body-tracking-results.md)
