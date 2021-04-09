---
title: Azure Kinect IMU サンプルを取得する
description: Azure Kinect SDK を使用して Azure Kinect IMU サンプルを取得する方法について説明します。
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: Kinect, Azure, 構成, 深度, 色, RBG, カメラ, センサー, SDK, IMU, モーション センサー, モーション, ジャイロスコープ, ジャイロ, 加速度計, FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276687"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>Azure Kinect IMU サンプルを取得する

Azure Kinect デバイスは、加速度計型およびジャイロスコープ型の両方を含む慣性測定装置 (IMU) へのアクセスを提供します。 IMU サンプルにアクセスするには、まずデバイスを開いて構成してから、IMU データをキャプチャする必要があります。 詳細については、[デバイスを検索して開く](find-then-open-device.md)に関するページを参照してください。

IMU サンプルは、イメージよりもはるかに高い頻度で生成されます。 サンプルは、サンプリングされるよりも低い速度でホストに報告されます。 IMU サンプルを待機しているときは、複数のサンプルが同時に使用できるようになることがよくあります。

IMU レポート速度の詳細については、Azure Kinect DK の[ハードウェア仕様](hardware-specification.md)をご覧ください。

## <a name="configure-and-start-cameras"></a>カメラを構成して起動する

> [!NOTE]
> IMU センサーは、カラー カメラまたは深度カメラが実行されている場合にのみ機能します。 IMU センサーは、単独では機能しません。

カメラを起動するには、[k4a_device_start_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a) を使用します。

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start cameras\n");
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_start_imu(device))
{
    printf("Failed to start imu\n");
    goto Exit;
}
```

## <a name="access-imu-samples"></a>IMU サンプルにアクセスする

 各 [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details) には、ほぼ同時にキャプチャされた加速度計とジャイロスコープの読み取り値が含まれます。

イメージ キャプチャを取得したものと同じスレッドで、または個別のスレッドで、IMU サンプルを取得できます。

IMU サンプルが使用できるようになったらすぐに取得するには、独自のスレッドで [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) を呼び出す必要があります。 API には十分な内部キューもあり、各イメージ キャプチャが返された後にサンプルだけを確認できます。

IMU サンプルの内部キューが複数あるので、データを削除せずに次のパターンを使用できます。

1. すべてのフレーム レートでキャプチャを待機します。
2. キャプチャを処理します。
3. キューに格納されているすべての IMU サンプルを取得します。
4. 次のキャプチャの待機を繰り返します。

現在キューに格納されているすべての IMU サンプルを取得するには、関数が `K4A_WAIT_RESULT_TIMEOUT` を返すまで、ループで `timeout_in_ms` を 0 にして [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) を呼び出すことができます。 `K4A_WAIT_RESULT_TIMEOUT` は、キューに登録されたサンプルがなく、指定されたタイムアウトに到達したものがなかったことを示します。

## <a name="usage-example"></a>使用例

```C
k4a_imu_sample_t imu_sample;

// Capture a imu sample
switch (k4a_device_get_imu_sample(device, &imu_sample, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a imu sample\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a imu sample\n");
    goto Exit;
}

// Access the accelerometer readings
if (imu_sample != NULL)
{
    printf(" | Accelerometer temperature:%.2f x:%.4f y:%.4f z: %.4f\n",
            imu_sample.temperature,
            imu_sample.acc_sample.xyz.x,
            imu_sample.acc_sample.xyz.y,
            imu_sample.acc_sample.xyz.z);
}

```

## <a name="next-steps"></a>次のステップ

これで、IMU サンプルを扱う方法がわかったので、次のことが行えます
>[!div class="nextstepaction"]
>[マイクの入力データにアクセスする](access-mics.md)
