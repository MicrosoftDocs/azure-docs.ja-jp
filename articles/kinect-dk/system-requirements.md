---
title: Azure Kinect Sensor SDK のシステム要件
description: Windows および Linux の Azure Kinect Sensor SDK のシステム要件について説明します。
author: tesych
ms.author: tesych
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/12/2020
ms.topic: article
keywords: azure, kinect, システム要件, CPU, GPU, USB, セットアップ, セットアップ, 最小, 要件
ms.openlocfilehash: 5cf313114b62532ee3f2b3d7a5142f79218954c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85277079"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Azure Kinect Sensor SDK のシステム要件

このドキュメントでは、Sensor SDK をインストールし、Azure Kinect DK を正常にデプロイするために必要なシステム要件について詳しく説明します。

## <a name="supported-operating-systems-and-architectures"></a>サポートされているオペレーティング システムとアーキテクチャ

- Windows 10 April 2018 (バージョン 1803、OS ビルド 17134) リリース (x64) 以降のバージョン
- Linux Ubuntu 18.04 (x64)、OpenGLv4.4 以降のバージョンを使用する GPU ドライバー

Sensor SDK は、ネイティブの C/C++ Windows アプリケーション用の Windows API (Win32) で使用できます。 現在、SDK は UWP アプリケーションでは使用できません。 Azure Kinect DK は、S モードの Windows 10 ではサポートされていません。

## <a name="development-environment-requirements"></a>開発環境の要件

Sensor SDK の開発に貢献するには、[GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK) にアクセスしてください。

## <a name="minimum-host-pc-hardware-requirements"></a>ホスト PC の最小ハードウェア要件

PC ホストのハードウェア要件は、ホスト PC で実行されるアプリケーション/アルゴリズム/センサーのフレーム レート/解像度によって異なります。 Windows で推奨される最小 Sensor SDK 構成は次のとおりです。

- 第 7 世代 Intel&reg; CoreTM i3 プロセッサ (HD620 GPU 以上のデュアル コア 2.4 GHz)
- 4 GB メモリ
- 専用の USB3 ポート
- OpenGL 4.4 または DirectX 11.0 のグラフィックス ドライバーのサポート

よりローエンドな CPU または古い CPU も、ユースケースによっては動作する場合があります。

パフォーマンスは、Windows/Linux オペレーティング システムと使用しているグラフィックス ドライバーの間でも異なります。

## <a name="body-tracking-host-pc-hardware-requirements"></a>ボディ トラッキング ホスト PC のハードウェア要件

ボディ トラッキング PC のホスト要件は、一般的な PC ホスト要件よりも厳格です。 Windows で推奨される最小 Body Tracking SDK 構成は次のとおりです。

- 第 7 世代 Intel&reg; CoreTM i5 プロセッサ (クアッド コア 2.4 GHz 以上)
- 4 GB メモリ
- NVIDIA GEFORCE GTX 1070 以上
- 専用の USB3 ポート

推奨される最小構成では、K4A_DEPTH_MODE_NFOV_UNBINNED 深度モードが 30fps で 5 人をトラッキングしていることを想定しています。 よりローエンドな CPU や古い CPU、および NVIDIA GPU も、ユースケースによっては動作する場合があります。

## <a name="usb3"></a>USB3

USB ホスト コントローラーには、互換性に関する既知の問題があります。 詳細については、[トラブルシューティングのページ](troubleshooting.md#usb3-host-controller-compatibility)を参照してください

## <a name="next-steps"></a>次のステップ

- [Azure Kinect DK の概要](about-azure-kinect-dk.md)

- [Azure Kinect DK を設定する](set-up-azure-kinect-dk.md)

- [Azure Kinect のボディ トラッキングを設定する](body-sdk-setup.md)
