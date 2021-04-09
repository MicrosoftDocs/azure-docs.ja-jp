---
title: Azure Kinect DK レコーダー
description: Azure Kinect レコーダーを使用して、データ ストリームを Sensor SDK からファイルに記録する方法について説明します。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, 記録, 再生, リーダー, Matroska, mkv, ストリーム, 深度, RGB, カメラ, カラー, IMU, オーディオ
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276705"
---
# <a name="azure-kinect-dk-recorder"></a>Azure Kinect DK レコーダー

この記事では、`k4arecorder` コマンド ライン ユーティリティを使用して、データ ストリームを Sensor SDK からファイルに記録する方法について説明します。

>[!NOTE]
>Azure Kinect レコーダーではオーディオは記録されません。

## <a name="recorder-options"></a>レコーダーのオプション

`k4arecorder` には、出力ファイルや記録モードを指定するためのさまざまなコマンド ライン引数があります。

記録は [Matroska .mkv 形式](record-file-format.md)で格納されます。 記録では、カラーと深度のほか、カメラの較正やメタデータなどの追加情報のために複数のビデオ トラックが使用されます。

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>記録ファイル

例 1. 深度 NFOV ビン分割解除 (640x576) モード、RGB 1080p を IMU 付きの 30 fps で記録します。
記録を停止するには、**CTRL-C** キーを押します。

```
k4arecorder.exe output.mkv
```

例 2. WFOV 非ビン分割 (1MP)、RGB 3072p を IMU なしの 15 fps で 10 秒間記録します。

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

例 3. ビン分割済みの WFOV 2x2 を 30 fps で 5 秒間記録し、output.mkv に保存します。

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>[Azure Kinect ビューアー](azure-kinect-viewer.md)を使用すると、記録の前に RGB カメラ コントロールを構成できます (手動のホワイト バランスを設定するためなど)。

## <a name="verify-recording"></a>記録を確認する

[Azure Kinect ビューアー](azure-kinect-viewer.md)を使用して、出力の .mkv ファイルを開くことができます。

トラックを抽出したり、ファイル情報を表示したりするには、[MKVToolNix](https://mkvtoolnix.download/) ツールキットの一部として `mkvinfo` などのツールを使用できます。

## <a name="next-steps"></a>次のステップ

[外部の同期された装置でレコーダーを使用する](record-external-synchronized-units.md)