---
title: Azure Kinect Sensor SDK について
description: Azure Kinect Sensor ソフトウェア開発キット (SDK)、その機能、ツールの概要。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Azure, Kinect, RGB, IR, 記録, センサー, SDK, アクセス, 深度, ビデオ, カメラ, IMU, モーション, センサー, オーディオ, マイク, Matroska, センサー SDK, ダウンロード
ms.openlocfilehash: 17c1b33120eacb5d0c6d3c02e692d1488ef474e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276729"
---
# <a name="about-azure-kinect-sensor-sdk"></a>Azure Kinect Sensor SDK について

この記事では、Azure Kinect Sensor ソフトウェア開発キット (SDK)、その機能、ツールの概要について説明します。

## <a name="features"></a>特徴

Azure Kinect Sensor SDK では、次のような Azure Kinect デバイス構成およびハードウェア センサー ストリーム用のクロスプラットフォームの低レベル アクセスを提供します。

- 深度カメラへのアクセスとモード制御 (パッシブ IR モードに加えて、広視野と狭視野の深度モード) 
- RGB カメラへのアクセスと制御 (露出やホワイト バランスなど) 
- モーション センサー (ジャイロスコープと加速度計) へのアクセス 
- 深度カメラと RGB カメラの同期ストリーミング (カメラ間の遅延を設定可能) 
- 外部デバイスの同期制御 (デバイス間の遅延オフセットを設定可能) 
- カメラ フレームのメタデータ (画像の解像度やタイムスタンプなど) へのアクセス 
- デバイス較正データへのアクセス 

## <a name="tools"></a>ツール

- デバイスのデータ ストリームを監視したり、さまざまなモードを構成したりするための [Azure Kinect ビューアー](azure-kinect-viewer.md)。
- [Matroska コンテナー形式](record-file-format.md)を使用する [Azure Kinect レコーダー](azure-kinect-recorder.md)と再生リーダー API。
- Azure Kinect DK [ファームウェア更新ツール](azure-kinect-firmware-tool.md)。

## <a name="sensor-sdk"></a>Sensor SDK

- [Sensor SDK をダウンロードします](sensor-sdk-download.md)。
- Sensor SDK は [GitHub でのオープンソース](https://github.com/microsoft/Azure-Kinect-Sensor-SDK)で入手できます。
- 使用方法の詳細については、[Sensor SDK API ドキュメント](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/index.html)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

これで Azure Kinect Sensor SDK について学習したので、次の作業も行うことができます。
>[!div class="nextstepaction"]
>[Sensor SDK コードをダウンロードする](sensor-sdk-download.md)

>[!div class="nextstepaction"]
>[デバイスを見つけて開く](find-then-open-device.md)
