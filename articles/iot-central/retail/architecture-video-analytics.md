---
title: Azure IoT Central ビデオ分析オブジェクトとモーション検出 | Microsoft Docs
description: IoT Central で、ビデオ分析 - オブジェクトとモーション検出アプリケーション テンプレートを使用して IoT Central アプリケーションを構築する方法について説明します。 このテンプレートでは、ライブ ビデオ分析と接続されたカメラを使用します。
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 48808f762536390287bae40e8af3849da20b81c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91874290"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>ビデオ分析 - オブジェクトとモーション検出アプリケーションのアーキテクチャ

**ビデオ分析 - オブジェクトとモーション検出** アプリケーション テンプレートにより、ライブ ビデオ分析機能を含む IoT ソリューションを構築できます。

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="ビデオ分析オブジェクトとモーション検出コンポーネントの概要の図。":::

ビデオ分析ソリューションの主要なコンポーネントは次のとおりです。

## <a name="live-video-analytics-lva"></a>ライブ ビデオ分析 (LVA)

LVA では、エッジとクラウドにまたがるインテリジェントなビデオ アプリケーションを構築するためのプラットフォームを提供します。 このプラットフォームにより、エッジとクラウドにまたがるインテリジェントなビデオ アプリケーションを構築できます。 このプラットフォームは、ライブ ビデオをキャプチャ、録画、分析し、その結果 (ビデオやビデオ分析など) を Azure サービスに発行する機能を備えています。 Azure サービスは、クラウドやエッジで実行されている場合があります。 このプラットフォームは、ビデオ分析を使用して IoT ソリューションを強化するために使用できます。

詳細については、GitHub の「[Live Video Analytics](https://github.com/Azure/live-video-analytics)」を参照してください。

## <a name="iot-edge-lva-gateway-module"></a>IoT Edge LVA ゲートウェイ モジュール

IoT Edge LVA ゲートウェイ モジュールでは、カメラを新しいデバイスとしてインスタンス化し、IoT デバイス クライアント SDK を使用して、それらを直接 IoT Central に接続します。

この参照実装では、デバイスはエッジからの対称キーを使用してソリューションに接続します。 デバイスの接続の詳細については、「[Azure IoT Central に接続する](../core/concepts-get-connected.md)」を参照してください

## <a name="media-graph"></a>メディア グラフ

メディア グラフでは、メディアのキャプチャ元、処理方法、および結果の配信場所を定義できます。 メディア グラフを構成するには、必要な方法でコンポーネントまたはノードを接続します。 詳細については、GitHub の「[メディア グラフ](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph)」を参照してください。

## <a name="next-steps"></a>次の手順

推奨される次の手順は、[ビデオ分析 - 物体とモーションの検出アプリケーション テンプレートを使用して IoT Central アプリケーションをデプロイする方法](tutorial-video-analytics-deploy.md)を確認することです。
