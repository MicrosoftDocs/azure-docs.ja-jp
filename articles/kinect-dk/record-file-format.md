---
title: Azure Kinect Sensor SDK を使用してファイル形式を記録する
description: Azure Kinect Sensor SDK を使用してファイル形式を記録する方法について説明します。
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: Kinect, Azure, センサー, SDK, 深度, RGB, 記録, 再生, Matroska, mkv
ms.openlocfilehash: f4fa14b0841cb76b2ba191310ecbca312d29f805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97654593"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>Azure Kinect Sensor SDK を使用してファイル形式を記録する

センサー データを記録するために、Matroska (.mkv) コンテナー形式が使用されます。この形式では、幅広いコーデックを使用して複数のトラックを格納できます。 記録ファイルには、カラー、深度、IR 画像と IMU を格納するためのトラックが含まれています。

.mkv コンテナー形式の低レベルの詳細については、[Matroska の Web サイト](https://www.matroska.org/index.html)を参照してください。

| トラック名 | コーデック形式                          |
|------------|---------------------------------------|
| COLOR      | モードによって異なる (MJPEG、NV12、または YUY2) |
| DEPTH      | b16g (16 ビット グレースケール、ビッグ エンディアン)   |
| IR         | b16g (16 ビット グレースケール、ビッグ エンディアン)   |
| IMU        | カスタム構造。後の「[IMU のサンプル構造](record-file-format.md#imu-sample-structure)」を参照してください。 |

## <a name="using-third-party-tools"></a>サード パーティ製ツールの使用

[MKVToolNix](https://mkvtoolnix.download/) ツールキットの `ffmpeg` または `mkvinfo` コマンドなどのツールを使用すると、記録ファイルの情報を表示したり抽出したりできます。

たとえば、次のコマンドは、深度トラックを同じフォルダーへの 16 ビット PNG のシーケンスとして抽出します。

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

`-map 0:1` パラメーターは、ほとんどの記録で深度になるトラック インデックス 1 を抽出します。 記録にカラー トラックが含まれていない場合は、`-map 0:0` が使用されます。

`-vsync 0` パラメーターは、ffmpeg で 30 fps、15 fps、または 5 fps のフレーム レートに一致させるのではなく、強制的にフレームをそのまま抽出するようにします。

## <a name="imu-sample-structure"></a>IMU のサンプル構造

IMU データが再生 API を使用せずにファイルから抽出された場合、そのデータはバイナリ形式になります。
IMU データの構造を次に示します。 すべてのフィールドがリトル エンディアンです。

| フィールド                        | Type     |
|------------------------------|----------|
| 加速度計のタイムスタンプ (µs) | uint64   |
| 加速度計のデータ (x、y、z) | float[3] |
| ジャイロスコープのタイムスタンプ (µs)     | uint64   |
| ジャイロスコープのデータ (x、y、z)     | float[3] |

## <a name="identifying-tracks"></a>トラックの識別

カラー、深度、IR などがどのトラックに含まれているかを識別することが必要になる場合があります。 トラックの識別は、サード パーティ製ツールを操作して Matroska ファイルを読み取る場合に必要です。
トラック番号は、カメラ モードや有効になっているトラックの組み合わせによって異なります。 タグは、各トラックの意味を識別するために使用されます。

次のタグの一覧はそれぞれ特定の Matroska 要素に添付されており、対応するトラックまたは添付ファイルを検索するために使用できます。

これらのタグは、`ffmpeg` や `mkvinfo` などのツールで表示できます。
タグの完全な一覧は、[記録と再生](record-playback-api.md)に関するページに一覧表示されています。

| タグ名             | タグのターゲット             | タグ値             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | カラー トラック            | Matroska トラック UID    |
| K4A_DEPTH_TRACK      | 深度トラック            | Matroska トラック UID    |
| K4A_IR_TRACK         | IR トラック               | Matroska トラック UID    |
| K4A_IMU_TRACK        | IMU トラック              | Matroska トラック UID    |
| K4A_CALIBRATION_FILE | 較正の添付ファイル | 添付ファイル名   |

## <a name="next-steps"></a>次のステップ

[記録と再生](record-playback-api.md)
