---
title: Azure Kinect デバイスの同期をキャプチャする
description: Azure Kinect Sensor SDK を使用して Azure Kinect キャプチャ デバイスを同期する方法について説明します。
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, センサー, SDK, 深度, RGB, 内部, 外部, 同期, デイジー チェーン, フェーズ オフセット
ms.openlocfilehash: ce0c72d3d708d5696a9775b3885f278f0c23cac1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034314"
---
# <a name="capture-azure-kinect-device-synchronization"></a>Azure Kinect デバイスの同期をキャプチャする

Azure Kinect ハードウェアは、カラー画像と深度画像のキャプチャ時間を整合させることができます。 同じデバイス上のカメラ間の整合は **内部同期** です。 接続されている複数のデバイスにまたがるキャプチャ時間の整合は **外部同期** です。 マイク配列は、カラーおよび深度カメラとは関係なく動作します。

## <a name="device-internal-synchronization"></a>デバイスの内部同期

個々のカメラ間の画像キャプチャは、ハードウェアで同期されます。 カラー センサーと深度センサーの両方からの画像を含むすべての [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) では、画像のタイムスタンプはハードウェアの動作モードに基づいて整合されます。 既定では、キャプチャの画像は露出の中心に整合されます。 深度とカラーのキャプチャの相対的なタイミングは、[k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) の `depth_delay_off_color_usec` フィールドを使用して調整できます。

## <a name="device-external-synchronization"></a>デバイスの外部同期

ハードウェアの設定については、[外部同期の設定](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)に関するページを参照してください。

接続されている各デバイスのソフトウェアは、**マスター** または **下位** モードで動作するように構成されている必要があります。 この設定は、[k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) で構成されます。

外部同期を使用している場合は、タイムスタンプを正しく整合させるために、下位カメラを常にマスターの前に起動する必要があります。

### <a name="subordinate-mode"></a>下位モード

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>マスター モード

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>同期ジャックの状態の取得

同期入力および同期出力ジャックの現在の状態をプログラムで取得するには、[k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) 関数を使用します。

## <a name="next-steps"></a>次のステップ

これで、デバイスの同期を有効にしてキャプチャする方法がわかりました。 また、次の使用方法を確認することもできます。 

>[!div class="nextstepaction"]
>[Azure Kinect Sensor SDK の記録および再生 API](record-playback-api.md)
