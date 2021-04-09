---
title: Azure Kinect DK Windows の比較
description: Azure Kinect DK と Kinect for Windows v2 のハードウェアおよびソフトウェアの違い
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect, Windows, v2, Azure Kinect, 比較, SDK, 相違点, ハードウェア, ソフトウェア
ms.openlocfilehash: 0a8d399370f354524858bdd658ffd65c0494dd4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87031577"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Azure Kinect と Kinect Windows v2 の比較

Azure Kinect DK ハードウェアおよびソフトウェア開発キットには、Kinect for Windows v2 とは異なる点があります。 既存の Kinect for Windows v2 アプリケーションは、Azure Kinect DK では直接機能せず、新しい SDK への移植が必要になります。  

## <a name="hardware"></a>ハードウェア

次の表に、Azure Kinect 開発キットと Kinect for Windows v2 の高レベルの相違点を示します。

| 特徴量 | Type | Azure Kinect DK | Kinect for Windows v2 |
| ------- | ---- | --------------- | --------------------- |
| **オーディオ** | 詳細  | 7 つのマイクの循環配列 | 4 つのマイクの線形段階的配列 |
| **モーション センサー** | 詳細 | 3 軸加速度計 3 軸ジャイロ | 3 軸加速度計 |
| **RGB カメラ**    | 詳細 | 3840 x 2160 px @30 fps | 1920 x 1080 px @30 fps |
| **深度カメラ**  | Method   | Time-of-Flight | Time-of-Flight |
|                   | 解決方法 | 640 x 576 px @30 fps | 512 x 424 px @ 30 fps |
|                   |            | 512 x 512 px @30 fps |                       |
|                   |            | 1024 x 1024 px @15 fps |                       |
| **接続** | Data | タイプが USB-C の USB3.1 Gen 1  | USB 3.1 Gen 1|
|  | Power | 外付けの PSU または USB-C | 外付けの PSU |
|  | Synchronization | RGB & 深度内部、外部デバイスからデバイス| RGB & 深度内部のみ |
| **機械的** | Dimensions | 103 x 39 x 126 mm | 249 x 66 x 67 mm |
|  | 質量 | 440 g | 970 g |
| | 取り付け | ¼-20 UNC 1 つ。 4 つの内部スクリュー ポイント | ¼-20 UNC 1 つ |

詳細については、[Azure Kinect DK ハードウェア](hardware-specification.md)に関するドキュメントをご覧ください。

## <a name="sensor-access"></a>センサーへのアクセス

次の表は、低レベルのデバイス センサー アクセス機能の比較を示しています。

| **機能**| **Azure Kinect DK** | **Kinect for Windows** | **メモ** |
|---------|---------|------------|---------|
| **[奥行]** | ✔️ | ✔️ |    |   |
| **IR** | ✔️ | ✔️ |  |
| **Color** | ✔️ | ✔️ | 色形式は差に対応しており、Azure Kinect DK は次のカメラ コントロールをサポートします。露出、ホワイト バランス、輝度、コントラスト、彩度、シャープネス、およびゲイン制御 |
| **オーディオ** | ✔️ | ✔️ | Azure Kinect DK のマイクには、Speech SDK または Windows ネイティブ API を使用してアクセスします |
| **IMU** | ✔️ |  | Azure Kinect DK には完全な 6 軸の IMU があり、Kinect for Windows では 1 軸しか提供されません |
| **調整データ** | ✔️ | ✔️ | OpenCV 互換カメラモデルの調整 |
| **深度-RGB 内部同期** | ✔️ | ✔️ |  |
| **外部同期**| ✔️|  | Azure Kinect DK では、外部同期のプログラミング可能な遅延が可能です |
| **複数のクライアントとのアクセスの共有** | | ✔️ | Azure Kinect Sensor SDK はデバイスへのアクセスに WinUSB/libUSB を使用しており、複数のプロセスでデバイスアクセスを共有できるようにするサービスは実装されていません |
| **ストリーム レコード/再生ツール** | ✔️ | ✔️ | Azure Kinect DK は、オープンソースの Matroska コンテナーベースの実装を使用します |

## <a name="features"></a>特徴

Azure Kinect SDK 機能セットは、以下で詳しく説明されているように、Kinect for Windows v2 とは異なります。

| **Kinect v2 機能** | **Kinect v2 データ型** | **Azure Kinect SDK/サービス** |
|--------|--------|------|
| センサー データ アクセス |DepthFrame| [Sensor SDK - イメージの取得](retrieve-images.md) 
| |InfraredFrame | [Sensor SDK - イメージの取得](retrieve-images.md) 
| | ColorFrame | [Sensor SDK - イメージの取得](retrieve-images.md) | 
| | AudioBeamFrame |現在、サポートされていません 
| 体の追跡 | BodyFrame | Body Tracking SDK |
| | BodyIndexFrame | Body Tracking SDK  |
| 座標マッピング|CoordinateMapper| [Sensor SDK - イメージ変換](use-image-transformation.md) |
|顔追跡 | FaceFrame | [Cognitive Services:Face](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    音声認識    |    該当なし                      |    [Cognitive Services:音声](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>次のステップ

[Kinect for Windows 開発者ページ](https://developer.microsoft.com/windows/kinect)
