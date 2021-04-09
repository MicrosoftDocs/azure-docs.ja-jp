---
title: Azure Kinect デバイスを検索して開く
description: Azure Kinect Sensor SDK を使用して Azure Kinect デバイスを検索して開く方法について説明します。
author: cedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, センサー, SDK, 深度, RGB, デバイス, 検索, 開く
ms.openlocfilehash: 67fc93b924d5d663bb43098969c54d1975bd5895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276663"
---
# <a name="find-then-open-the-azure-kinect-device"></a>Azure Kinect デバイスを検索して開く

この記事では、Azure Kinect DK を検索して開く方法について説明します。 この記事では、コンピューターに複数のデバイスが接続されているケースを処理する方法について説明します。

また、この記事にある関数を使用する方法を示す [SDK の列挙の例](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/enumerate)を参照することもできます。

次の関数を扱っています。
 * [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
 * [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
 * [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
 * [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="discover-the-number-of-connected-devices"></a>接続されているデバイスの数を検出する

まず、[`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) を使用して、現在接続されている Azure Kinect デバイスの数を取得します。

```C
uint32_t device_count = k4a_device_get_installed_count();

printf("Found %d connected devices:\n", device_count);
```

## <a name="open-a-device"></a>デバイスを開く

デバイスに関する情報を取得するか、またはそこからデータを読み取るには、まず [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) を使用して、そのデバイスへのハンドルを開く必要があります。

```C
k4a_device_t device = NULL;

for (uint8_t deviceIndex = 0; deviceIndex < device_count; deviceIndex++)
{
    if (K4A_RESULT_SUCCEEDED != k4a_device_open(deviceIndex, &device))
    {
        printf("%d: Failed to open device\n", deviceIndex);
        continue;
    }

    ...

    k4a_device_close(device);
}
```

[`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) の `index` パラメーターは、複数が接続されている場合にどのデバイスを開くかを示します。 1 台のデバイスしか接続されていないことが予測される場合は、`K4A_DEVICE_DEFAULT` または 0 の引数を渡して最初のデバイスを示すことができます。

デバイスを開いたときは常に、そのハンドルの使用を完了したら [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) を呼び出す必要があります。 そのハンドルを閉じるまで、同じデバイスへの他のハンドルを開くことはできません。

## <a name="identify-a-specific-device"></a>特定のデバイスを識別する

デバイスがインデックスで列挙される順序は、デバイスが接続または切断されるまで変更されません。 物理デバイスを識別するには、そのデバイスのシリアル番号を使用する必要があります。

デバイスからシリアル番号を読み取るには、ハンドルを開いた後に [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22) 関数を使用します。

この例は、シリアル番号を格納するための適切な量のメモリを割り当てる方法を示しています。

```C
char *serial_number = NULL;
size_t serial_number_length = 0;

if (K4A_BUFFER_RESULT_TOO_SMALL != k4a_device_get_serialnum(device, NULL, &serial_number_length))
{
    printf("%d: Failed to get serial number length\n", deviceIndex);
    k4a_device_close(device);
    device = NULL;
    continue;
}

serial_number = malloc(serial_number_length);
if (serial_number == NULL)
{
    printf("%d: Failed to allocate memory for serial number (%zu bytes)\n", deviceIndex, serial_number_length);
    k4a_device_close(device);
    device = NULL;
    continue;
}

if (K4A_BUFFER_RESULT_SUCCEEDED != k4a_device_get_serialnum(device, serial_number, &serial_number_length))
{
    printf("%d: Failed to get serial number\n", deviceIndex);
    free(serial_number);
    serial_number = NULL;
    k4a_device_close(device);
    device = NULL;
    continue;
}

printf("%d: Device \"%s\"\n", deviceIndex, serial_number);
```

## <a name="open-the-default-device"></a>既定のデバイスを開く

ほとんどのアプリケーションでは、同じコンピューターに 1 台の Azure Kinect DK だけが接続されます。 1 台の予測されるデバイスにしか接続する必要がない場合は、`K4A_DEVICE_DEFAULT` の `index` で [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) を呼び出して最初のデバイスを開くことができます。

```C
k4a_device_t device = NULL;
uint32_t device_count = k4a_device_get_installed_count();

if (device_count != 1)
{
    printf("Unexpected number of devices found (%d)\n", device_count);
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_open(K4A_DEVICE_DEFAULT, &device))
{
    printf("Failed to open device\n");
    goto Exit;
}
```

## <a name="next-steps"></a>次のステップ

>[!div class="nextstepaction"]
>[画像を取得する](retrieve-images.md)

>[!div class="nextstepaction"]
>[IMU サンプルを取得する](retrieve-imu-samples.md)

