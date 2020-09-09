---
title: クイックスタート - 最初の Azure Kinect アプリケーションを構築する
description: このクイックスタートでは、新しいアプリケーションを作成するプロセスを Azure Kinect DK ユーザーに説明します。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Kinect, Azure, センサー, SDK, マイク, マイクへのアクセス, マイク データ
ms.openlocfilehash: 3632145b3f3b63023e0c66e3cf99903231802edf
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "85277822"
---
# <a name="quickstart-build-your-first-azure-kinect-application"></a>クイック スタート:最初の Azure Kinect アプリケーションを構築する

Azure Kinect DK へようこそ。 このクイックスタートでは、デバイスを起動して実行してみましょう。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

次の関数を扱っています。

- [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
- [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
- [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
- [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="prerequisites"></a>前提条件

1. [Azure Kinect DK デバイスを設定します](set-up-azure-kinect-dk.md)。
2. Azure Kinect Sensor SDK を[ダウンロード](sensor-sdk-download.md)してインストールします。

## <a name="headers"></a>ヘッダー

必要なヘッダーは `k4a.h` の 1 つだけです。 使用するコンパイラが SDK の lib および include フォルダーで設定されていることをご確認ください。 `k4a.lib` および `k4a.dll` ファイルがリンクされている必要もあります。 必要に応じて、[プロジェクトへの Azure Kinect ライブラリの追加](add-library-to-project.md)に関するページをご覧ください。

```C
#include <k4a/k4a.h>
```

## <a name="finding-an-azure-kinect-dk-device"></a>Azure Kinect DK デバイスを見つける

複数の Azure Kinect DK デバイスをコンピューターに接続できます。 まず、[`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) 関数を使用して接続数または接続の有無を調べます。 この関数は、追加の設定を行わなくてもすぐに機能します。

```C
uint32_t count = k4a_device_get_installed_count();
```

コンピューターに接続されているデバイスがあることを確認したら、[`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) を使用してデバイスを開くことができます。 開くデバイスのインデックスを指定するか、`K4A_DEVICE_DEFAULT` のみを使用して最初のデバイスを開くことができます。

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
k4a_device_open(K4A_DEVICE_DEFAULT, &device);
```
Azure Kinect ライブラリのほとんどの要素と同様に、要素を開いて操作を終了したらその要素を閉じる必要もあります。 シャットダウンするときは、必ず [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) を呼び出してください。

```C
k4a_device_close(device);
```

デバイスが開いたら、テストを行い、デバイスが機能していることを確認できます。 それでは、デバイスのシリアル番号を読み取ってみましょう。

```C
// Get the size of the serial number
size_t serial_size = 0;
k4a_device_get_serialnum(device, NULL, &serial_size);

// Allocate memory for the serial, then acquire it
char *serial = (char*)(malloc(serial_size));
k4a_device_get_serialnum(device, serial, &serial_size);
printf("Opened device: %s\n", serial);
free(serial);
```

## <a name="starting-the-cameras"></a>カメラの起動

デバイスを開いたら、[`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) オブジェクトを使用してカメラを構成する必要があります。 カメラの較正には、さまざまなオプションがあります。 シナリオに応じて最適な設定を選んでください。

```C
// Configure a stream of 4096x3072 BRGA color data at 15 frames per second
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

// Start the camera with the given configuration
k4a_device_start_cameras(device, &config);

// ...Camera capture and application specific code would go here...

// Shut down the camera when finished with application logic
k4a_device_stop_cameras(device);
```

## <a name="error-handling"></a>エラー処理

簡潔でわかりやすくするために、一部のインラインの例ではエラー処理を示していません。 ただし、エラー処理は常に重要です。 多くの関数では、一般的な成功/失敗型の [`k4a_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga4b419a99aa2220b076a4520dc2afd1e5.html#ga4b419a99aa2220b076a4520dc2afd1e5) か、より具体的で詳しい情報を含む [`k4a_wait_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga44c7c0c1cfba7c879e9e2da1a869e4ee.html#ga44c7c0c1cfba7c879e9e2da1a869e4ee) のようなバリアントを返します。 各関数で予想されるエラー メッセージについては、各関数のドキュメントまたは IntelliSense をご確認ください。

[`K4A_SUCCEEDED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga8e5b48150bc243c6052793bd830c2fcd.html#ga8e5b48150bc243c6052793bd830c2fcd) および [`K4A_FAILED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga7c2e32349135d008b6f836c571d434b4.html#ga7c2e32349135d008b6f836c571d434b4) マクロを使用して、関数の結果を確認できます。 そのため、Azure Kinect DK デバイスを開くだけでなく、次のようにして関数呼び出しをガードできます。

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
if ( K4A_FAILED( k4a_device_open(K4A_DEVICE_DEFAULT, &device) ) )
{
    printf("Failed to open k4a device!\n");
    return;
}
```

## <a name="full-source"></a>完全なソース

```C
#pragma comment(lib, "k4a.lib")
#include <k4a/k4a.h>

#include <stdio.h>
#include <stdlib.h>

int main()
{
    uint32_t count = k4a_device_get_installed_count();
    if (count == 0)
    {
        printf("No k4a devices attached!\n");
        return 1;
    }

    // Open the first plugged in Kinect device
    k4a_device_t device = NULL;
    if (K4A_FAILED(k4a_device_open(K4A_DEVICE_DEFAULT, &device)))
    {
        printf("Failed to open k4a device!\n");
        return 1;
    }

    // Get the size of the serial number
    size_t serial_size = 0;
    k4a_device_get_serialnum(device, NULL, &serial_size);

    // Allocate memory for the serial, then acquire it
    char *serial = (char*)(malloc(serial_size));
    k4a_device_get_serialnum(device, serial, &serial_size);
    printf("Opened device: %s\n", serial);
    free(serial);

    // Configure a stream of 4096x3072 BRGA color data at 15 frames per second
    k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
    config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
    config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

    // Start the camera with the given configuration
    if (K4A_FAILED(k4a_device_start_cameras(device, &config)))
    {
        printf("Failed to start cameras!\n");
        k4a_device_close(device);
        return 1;
    }

    // Camera capture and application specific code would go here

    // Shut down the camera when finished with application logic
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}

```

## <a name="next-steps"></a>次のステップ

Sensor SDK を使用して Azure Kinect DK デバイスを見つけて開く方法を学ぶ
> [!div class="nextstepaction"]
>[デバイスを見つけて開く](find-then-open-device.md)
