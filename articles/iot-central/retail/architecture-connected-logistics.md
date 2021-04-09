---
title: IoT コネクテッド ロジスティクスのアーキテクチャ | Microsoft Docs
description: IoT Central 用の IoT コネクテッド ロジスティクス アプリケーション テンプレートのアーキテクチャ
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d1e17dce80c313bf726451c36ec06dd393549600
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832421"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>IoT Central のコネクテッド ロジスティクス アプリケーション テンプレートのアーキテクチャ



パートナーと顧客は、アプリ テンプレートと以下のガイダンスを使用して、エンド ツー エンドの **コネクテッド ロジスティクス** ソリューションを開発することができます。

> [!div class="mx-imgBorder"]
> ![コネクテッド ロジスティクスのダッシュボード](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. ゲートウェイ デバイスにテレメトリ データを送信している IoT タグのセット
2. テレメトリと集計された分析情報を IoT Central に送信しているゲートウェイ デバイス
3. データは、操作のために目的の Azure サービスにルーティングされます
4. ASA や Azure Functions などの Azure サービスを使用して、データ ストリームの形式を再設定し、目的のストレージ アカウントに送信できます
5. エンド ユーザーのビジネス アプリケーションでさまざまなビジネス ワークフローを利用できます

## <a name="details"></a>詳細
次のセクションでは、IoT タグとゲートウェイからのテレメトリ インジェストの概念アーキテクチャの各部分の概要について説明します

## <a name="iot-tags"></a>IoT タグ
IoT タグを使うと、温度、湿度、ショック、チルト、光など、物理的、アンビエント、環境センサーの機能が提供されます。 IoT タグは、通常、Zigbee (802.15.4) を通じてゲートウェイ デバイスに接続されます。 タグはより安価なセンサーであるため、リバース ロジスティクスでの課題を避けるため、一般的なロジスティクス過程の最後に破棄することができます。

## <a name="gateway"></a>Gateway
また、ゲートウェイは、アンビエント検出機能を備えた IoT タグとして機能することもできます。 ゲートウェイにより、携帯電話の Wi-Fi チャネルを使用して、アップストリームの Azure IoT クラウド接続 (MQTT) が有効になります。  IoT タグとのダウンストリーム通信には、Bluetooth、NFC、および 802.15.4 ワイヤレス センサー ネットワーク (WSN) モードが使用されます。 ゲートウェイでは、セキュリティで保護されたエンド ツー エンドのクラウド接続、IoT タグのペアリング、センサー データの集計、データ保持、アラームしきい値の構成機能が提供されます。

## <a name="device-management-with-iot-central"></a>IoT Central によるデバイス管理 
Azure IoT Central はソリューション開発プラットフォームであり、IoT デバイスの接続、構成、管理が簡単になります。 このプラットフォームを使うと、IoT デバイスの管理、運用、関連開発の負担とコストが大幅に削減されます。 顧客とパートナーは、エンドツーエンドのエンタープライズ ソリューションを構築し、ロジスティクスでデジタル フィードバック ループを実現できます。

## <a name="business-insights-and-actions-using-data-egress"></a>データ エグレスを使用したビジネスの分析情報とアクション 
IoT Central プラットフォームでは、継続的データ エクスポート (CDE) と API を通じて豊富な機能拡張オプションが提供されます。 一般に、テレメトリ データ処理または未加工のテレメトリに基づくビジネス分析情報は、優先される基幹業務アプリケーションにエクスポートされます。 Webhook、サービス バス、イベント ハブ、または BLOB ストレージを使用して、機械学習モデルを構築、トレーニング、デプロイし、分析情報をさらに補強することによって、これを実現できます。

## <a name="next-steps"></a>次のステップ
* [コネクテッド ロジスティクス ソリューション テンプレート](./tutorial-iot-central-connected-logistics.md)のデプロイ方法を学習します
* [IoT Central 小売りテンプレート](./overview-iot-central-retail.md)についてさらに詳しく学習します
* [IoT Central の概要](../core/overview-iot-central.md)に関する記事を参照し、IoT Central についてさらに詳しく学習します
