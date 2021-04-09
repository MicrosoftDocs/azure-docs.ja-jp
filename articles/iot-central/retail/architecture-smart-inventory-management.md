---
title: IoT スマート インベントリの管理のアーキテクチャ | Microsoft Docs
description: IoT Central 用の IoT スマート インベントリの管理テンプレートのアーキテクチャ
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d7939193b0dad1ed74dccd0ace0b0f0323b17124
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832642"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>IoT Central のスマート インベントリの管理アプリケーション テンプレートのアーキテクチャ

パートナーと顧客は、アプリ テンプレートと以下のガイダンスを使用して、エンド ツー エンドの **スマート インベントリの管理** ソリューションを開発することができます。

> [!div class="mx-imgBorder"]
> ![スマート インベントリの管理](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. ゲートウェイ デバイスにテレメトリ データを送信している IoT センサーのセット
2. テレメトリと集計された分析情報を IoT Central に送信しているゲートウェイ デバイス
3. データは、操作のために目的の Azure サービスにルーティングされます
4. ASA や Azure Functions などの Azure サービスを使用して、データ ストリームの形式を再設定し、目的のストレージ アカウントに送信できます 
5. 処理されたデータは、ほぼリアルタイムのアクションのためにホット ストレージに格納されるか、ML やバッチ分析に基づいて分析情報をさらに拡充するためにコールド ストレージに格納されます。 
6. Logic Apps を使用すると、エンド ユーザーのビジネス アプリケーションでさまざまなビジネス ワークフローを利用できます

## <a name="details"></a>詳細
次のセクションでは、無線自動識別 (RFID) タグと Bluetooth Low Energy (BLE) タグからのテレメトリ インジェストの概念アーキテクチャの各部分の概要を説明します

## <a name="rfid-tags"></a>RFID タグ
RFID タグは、アイテムに関するデータを無線で送信します。 通常、RFID タグは、指定されている場合を除き、バッテリーを持っていません。 タグは、リーダーによって生成された無線波からエネルギーを受け取り、RFID リーダーに対して信号を送り返します。

## <a name="ble-tags"></a>BLE タグ
エネルギー ビーコンは、一定の間隔でデータのパケットをブロードキャストします。 ビーコン データは、BLE リーダーまたはスマートフォンにインストールされたサービスによって検出され、クラウドに転送されます。

## <a name="rfid--ble-readers"></a>RFID および BLE リーダー
RFID リーダーは、無線波をより使いやすい形式のデータに変換します。 タグから収集された情報は、ローカル エッジ サーバーに格納されるか、JSON-RPC 2.0 over MQTT を使用してクラウドに送信されます。
アクセス ポイント (AP) とも呼ばれる BLE リーダーは、RFID リーダーに似ています。 これにより、近くの Bluetooth 信号が検出され、そのメッセージが JSON-RPC 2.0 over MQTT を使用してローカル Azure IoT Edge またはクラウドに中継されされます。
多くのリーダーは、RFID およびビーコン信号を読み取ることができ、温度、湿度、加速度計、およびジャイロスコープに関連する追加のセンサー機能を提供します。

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge ゲートウェイ
Azure IoT Edge サーバーを使うと、クラウドに送信される前のデータをローカルに前処理できます。 また、クラウド ワークロードの人工知能、Azure およびサードパーティのサービス、ビジネス ロジックを、標準のコンテナーを使用してデプロイすることもできます。

## <a name="device-management-with-iot-central"></a>IoT Central によるデバイス管理 
Azure IoT Central はソリューション開発プラットフォームであり、IoT デバイスの接続、構成、管理が簡単になります。 このプラットフォームを使うと、IoT デバイスの管理、運用、関連開発の負担とコストが大幅に削減されます。 顧客とパートナーは、エンドツーエンドのエンタープライズ ソリューションを構築し、インベントリ管理でデジタル フィードバック ループを実現できます。

## <a name="business-insights--actions-using-data-egress"></a>データ エグレスを使用したビジネスの分析情報とアクション 
IoT Central プラットフォームでは、継続的データ エクスポート (CDE) と API を通じて豊富な機能拡張オプションが提供されます。 一般に、テレメトリ データ処理または未加工のテレメトリに基づくビジネス分析情報は、優先される基幹業務アプリケーションにエクスポートされます。 Webhook、サービス バス、イベント ハブ、または BLOB ストレージを使用して、機械学習モデルを構築、トレーニング、デプロイし、分析情報をさらに補強することによって、これを実現できます。

## <a name="next-steps"></a>次のステップ
* [スマート インベントリの管理テンプレート](./tutorial-iot-central-smart-inventory-management.md)のデプロイ方法を学習します
* [IoT Central 小売りテンプレート](./overview-iot-central-retail.md)についてさらに詳しく学習します
* [IoT Central の概要](../core/overview-iot-central.md)に関する記事を参照し、IoT Central についてさらに詳しく学習します
