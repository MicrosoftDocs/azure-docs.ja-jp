---
title: Azure IoT Hub の概要 | Microsoft Docs
description: Azure IoT Hub について説明します。 この IoT サービスは、スケーラブルなデータ インジェスト、デバイスの管理、およびセキュリティを目的として作成されています。
author: nberdy
ms.author: nberdy
ms.date: 07/04/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-hub
services: iot-hub
manager: briz
ms.openlocfilehash: 6dadd746bccd028a2b81a980d99ab47ec9e6e2a3
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726739"
---
# <a name="what-is-azure-iot-hub"></a>Azure IoT Hub とは

IoT Hub は、クラウド内でホストされているマネージド サービスであり、IoT アプリケーションとそれが管理するデバイスの間の双方向通信に対する中央メッセージ ハブとして機能します。 Azure IoT Hub を使って IoT ソリューションを構築し、何百万もの IoT デバイスとクラウドでホストされたソリューション バックエンドの間に、信頼性が高く、セキュリティで保護された通信を提供できます。 IoT Hub には、ほぼすべてのデバイスを接続できます。

IoT Hub は、デバイスからクラウドへと、クラウドからデバイスへの、両方の通信をサポートします。 IoT Hub は、デバイスとクラウドの間のテレメトリ、デバイスからのファイルのアップロード、クラウドからデバイスを制御するための要求/応答メソッドなど、複数のメッセージング パターンをサポートします。 IoT Hub の監視は、デバイスの作成、デバイスの障害、デバイスの接続などのイベントを追跡することにより、ソリューションの正常性を維持するのに役立ちます。

IoT Hub の機能を使うと、製造で使われる産業機器の管理、医療での価値の高い資産の追跡、オフィス ビルの使用状況の監視など、スケーラブルで機能を完備した IoT ソリューションを構築できます。

## <a name="scale-your-solution"></a>ソリューションをスケール調整する

IoT Hub は、数百万のデバイスの同時接続、および IoT ワークロードをサポートするための毎秒数百万のイベントに対応するようにスケーリングします。 IoT Hub では、スケーラビリティのニーズに最適になるように、複数のサービス階層が提供されます。 詳細については、[価格](https://azure.microsoft.com/pricing/details/iot-hub/)に関するページを参照してください。

## <a name="secure-your-communications"></a>通信のセキュリティ保護

IoT Hub が提供するセキュリティで保護された通信チャネルを使用して、デバイスはデータを送信できます。

* デバイスごとの認証により、各デバイスは IoT Hub に安全に接続でき、各デバイスを安全に管理できます。

* ユーザーは、デバイスのアクセスを完全に制御でき、デバイスごとのレベルで接続を制御できます。

* [IoT Hub Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/) は、デバイスの初期起動時に、正しい IoT ハブに対してデバイスを自動的にプロビジョニングします。

* 複数の認証の種類により、さまざまなデバイス機能がサポートされています。

  * IoT ソリューションを短時間で使用できるようにする SAS トークン ベースの認証。

  * セキュリティで保護された、標準ベースの認証のための、個別の X.509 証明書認証。

  * 簡単な標準ベースの登録用の X.509 CA 認証。

## <a name="route-device-data"></a>デバイス データのルーティング

組み込まれているメッセージ ルーティング機能により、ルールに基づく自動メッセージ ファンアウトを柔軟に設定できます。

* メッセージ ルーティングを使って、ハブがデバイスのテレメトリを送信する場所を制御します。

* 複数のエンドポイントにメッセージをルーティングしても、追加コストは発生しません。

* カスタム メッセージ ディスパッチャー コードの代わりに、コードのないルーティング ルールが使われます。

## <a name="integrate-with-other-services"></a>他のサービスとの統合

IoT Hub を他の Azure サービスと統合して、完全なエンド ツー エンドのソリューションを構築できます。 たとえば、次のようなサービスを使います。

* [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) を使うと、信頼性が高く、スケーラブルかつ安全な方法で重要なイベントに迅速に対応できます。

* [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) は、ビジネス プロセスを自動化します。

* [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/) は、機械学習と AI モデルをソリューションに追加します。

* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) は、デバイスからのデータ ストリーミングに対してリアルタイムの分析計算を実行します。

## <a name="configure-and-control-your-devices"></a>デバイスの構成と制御

さまざまな組み込み機能を利用して、IoT Hub に接続されたデバイスを管理できます。

* すべてのデバイスのデバイス メタデータと状態情報を保存、同期、照会します。

* デバイスごとに、またはデバイスの共通特性に基づいて、デバイスの状態を設定します。

* メッセージ ルーティング統合により、デバイスで報告された状態の変化に自動的に対応します。

## <a name="make-your-solution-highly-available"></a>ソリューションの高可用性化

[IoT Hub のサービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/iot-hub/)は 99.9% です。 完全な [Azure SLA](https://azure.microsoft.com/support/legal/sla/) では、全体としての Azure の可用性の確保について説明します。

## <a name="connect-your-devices"></a>デバイスの接続

[Azure IoT device SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) のライブラリを使うと、デバイス上で実行して IoT Hub と対話するアプリケーションを作成できます。 複数の Linux ディストリビューション、Windows、リアルタイム オペレーティング システムなどのプラットフォームがサポートされています。 以下の言語がサポートされています。

* C
* C#
* Java
* Python
* Node.js.

IoT Hub と device SDK は、次のプロトコルを使ったデバイスの接続をサポートしています。

* HTTPS
* AMQP
* AMQP over WebSocket
* MQTT
* WebSocket 経由の MQTT

ソリューションで device ライブラリを使用できない場合、デバイスは MQTT v3.1.1、HTTPS 1.1、または AMQP 1.0 プロトコルを使って、ハブにネイティブに接続できます。

ソリューションがサポートされているどのプロトコルも使用できない場合は、カスタム プロトコルをサポートするように IoT Hub を拡張できます。

* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) を使って、エッジでプロトコル変換を実行するためのフィールド ゲートウェイを作成します。

* [Azure IoT プロトコル ゲートウェイ](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)をカスタマイズして、クラウドでのプロトコル変換を実行します。

## <a name="quotas-and-limits"></a>クォータと制限

各 Azure サブスクリプションにはサービスの悪用を防ぐために既定のクォータ制限が設けられており、これらの制限が IoT ソリューションの範囲に影響する可能性があります。 現在、サブスクリプションごとの IoT ハブの数は 50 個に制限されています。 クォータの増加を要求する場合は、サポートに連絡してください。 クォータ制限の詳細については、次を参照してください。

* [Azure サブスクリプション サービスの制限](../azure-subscription-service-limits.md)

* [IoT Hub のスロットリング](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="next-steps"></a>次の手順

エンド ツー エンドの IoT ソリューションを試すには、IoT Hub のクイック スタートをご覧ください。

* [クイック スタート:デバイスから IoT ハブへの利用統計情報の送信](quickstart-send-telemetry-node.md)