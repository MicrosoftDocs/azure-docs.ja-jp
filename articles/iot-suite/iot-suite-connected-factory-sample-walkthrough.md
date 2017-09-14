---
title: "コネクテッド ファクトリの Azure IoT Suite ソリューションのチュートリアル | Microsoft Docs"
description: "Azure IoT コネクテッド ファクトリの事前構成済みソリューションとそのアーキテクチャの説明です。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 517e908a744734139ed0aeee314a4f3b9eda86cc
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>コネクテッド ファクトリ事前構成済みソリューションのチュートリアル

IoT Suite コネクテッド ファクトリの[事前構成済みソリューション][lnk-preconfigured-solutions]は、次の処理を行うエンド ツー エンドの産業ソリューションの実装です。

* シミュレートされた工場生産ラインで OPC UA サーバーを実行しているシミュレートされた産業デバイスと、実際の OPC UA サーバー デバイスの両方に接続します。 OPC UA の詳細については、[コネクテッド ファクトリの FAQ](iot-suite-faq-cf.md) を参照してください。
* このようなデバイスと生産ラインの運用 KPI と OEE を表示します。
* OPC UA サーバー システムを操作するためにクラウドベースのアプリケーションがどのように使用されているかを示します。
* 独自の OPC UA サーバー デバイスを接続できるようにします。
* OPC UA サーバー データを参照および変更できるようにします。
* Azure Time Series Insights (TSI) サービスと統合し、OPC UA サーバーのデータのカスタマイズしたビューを提供します。

このソリューションを独自の実装の出発点として使用し、独自のビジネス要件を満たすように[カスタマイズ][lnk-customize]することができます。

この記事では、コネクテッド ファクトリ ソリューションのしくみを理解できるように、その主な構成要素のいくつかについて説明します。 この知識は以下の作業に役立ちます。

* ソリューションの問題のトラブルシューティングを行う。
* 独自の要件を満たすためにソリューションをカスタマイズする方法を計画する。
* Azure サービスを利用する独自の IoT ソリューションを設計する。

詳細については、[コネクテッド ファクトリの FAQ](iot-suite-faq-cf.md) を参照してください。

## <a name="logical-architecture"></a>論理アーキテクチャ

次の図は、事前構成済みソリューションの論理コンポーネントの概要を示したものです。

![コネクテッド ファクトリの論理アーキテクチャ][connected-factory-logical]

## <a name="communication-patterns"></a>通信パターン

ソリューションは、[OPC UA Pub/Sub 仕様](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/)を使用して、OPC UA テレメトリ データを IoT Hub に JSON 形式で送信します。 この目的のために、ソリューションは [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) IoT Edge モジュールを使用します。

ソリューションには、オンプレミスの OPC UA サーバーとの接続を確立できる Web アプリケーションに統合された OPC UA クライアントもあります。 クライアントは[リバースプロキシ](https://wikipedia.org/wiki/Reverse_proxy)を使用し、IoT Hub からの支援を受けて、オンプレミスのファイアウォールでポートを開かずに接続を確立します。 この通信方式は、[サービス支援通信](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)と呼ばれます。 この目的のために、ソリューションは [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy/) IoT Edge モジュールを使用します。


## <a name="simulation"></a>シミュレーション

工場生産ラインは、シミュレートされたステーションと、シミュレートされた生産実行システム (MES) で構成されます。 シミュレートされたデバイスと OPC Publisher モジュールは、OPC Foundation が公開した [OPC UA .NET 標準][lnk-OPC-UA-NET-Standard]に基づいています。

OPC Proxy と OPC Publisher は、[Azure IoT Edge][lnk-Azure-IoT-Gateway] に基づくモジュールとして実装されます。 シミュレートされた生産ラインごとに、指定されたゲートウェイが接続されています。

シミュレーション コンポーネントはすべて、Azure Linux VM でホストされている Docker コンテナーで実行されます。 このシミュレーションは、シミュレートされた生産ラインを既定で 8 つ実行するように構成されています。

## <a name="simulated-production-line"></a>シミュレートされた生産ライン

生産ラインではパーツを製造します。 これは、組立ステーション、テスト ステーション、梱包ステーションという各種ステーションで構成されます。

このシミュレーションが実行されると、OPC UA ノードを通じて公開されるデータが更新されます。 シミュレートされた生産ラインのステーションはすべて、OPC UA を通じて MES によって調整されます。

## <a name="simulated-manufacturing-execution-system"></a>シミュレートされた生産実行システム

MES は、OPC UA を通じて生産ラインの各ステーションを監視し、ステーションの状態変化を検出します。 これは、OPC UA メソッドを呼び出してステーションを制御し、製品が完成するまでその製品をあるステーションから次のステーションへと渡します。

## <a name="gateway-opc-publisher-module"></a>Gateway OPC Publisher モジュール

OPC Publisher モジュールは、ステーションの OPC UA サーバーに接続し、発行する OPC ノードにサブスクライブします。 このモジュールにより、ノードのデータは JSON 形式に変換されて暗号化され、OPC UA の発行/サブスクライブ メッセージとして IoT Hub に送信されます。

OPC Publisher モジュールは、送信 HTTPS ポート (443) のみを必要とし、既存のエンタープライズ インフラストラクチャで使用できます。

## <a name="gateway-opc-proxy-module"></a>Gateway OPC Proxy モジュール

Gateway OPC UA Proxy モジュールは、バイナリの OPC UA コマンドと制御メッセージをトンネリングし、送信 HTTPS ポート (443) のみを必要とします。 これは、Web プロキシなど、既存のエンタープライズ インフラストラクチャで使用できます。

このモジュールは、IoT Hub デバイスのメソッドを使用して、アプリケーション層でパケット化された TCP/IP データを転送したうえで、SSL/TLS を使用してエンドポイントの信頼、データの暗号化、整合性を確保します。

プロキシ自体を介して中継される OPC UA バイナリ プロトコルは、UA 認証および暗号化を使用します。

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

Gateway OPC Publisher モジュールは、OPC UA サーバー ノードをサブスクライブして、データ値の変更を検出します。 いずれかのノードでデータの変更が検出されると、このモジュールによって、メッセージが Azure IoT Hub に送信されます。

IoT Hub は、Azure TSI にイベント ソースを提供します。 TSI は、メッセージに付随したタイムスタンプに基づいて 30 日間データを保持します。 このデータには次のものが含まれます。

* OPC UA ApplicationUri
* OPC UA NodeId
* ノードの値
* ソースのタイムスタンプ
* OPC UA DisplayName

現在、TSI では、顧客がデータの保有期間をカスタマイズすることは許可されていません。

TSI は、SearchSpan (Time.From, Time.To) を使用してノード データに対してクエリを実行し、OPC UA ApplicationUri、OPC UA NodeId、または OPC UA DisplayName 別に集計します。

OEE および KPI ゲージのデータと時系列のグラフを取得するために、データは、イベントの数、Sum、Avg、Min、Max 別に集計されます。

時系列は、さまざまなプロセスを使用して構築されます。 OEE と KPI は、ステーションのベース データから計算され、アプリケーション内のトポロジ (生産ライン、工場、企業) に対して通知されます。

さらに、表示された期間が準備できているかどうかに関係なく、OEE および KPI トポロジの時系列がアプリで計算されます。 たとえば、日単位の表示は 1 時間ごとに更新されます。

ノード データの時系列表示は、期間の集計を使用して TSI から直接取得されます。

## <a name="iot-hub"></a>IoT Hub
[IoT Hub][lnk-IoT Hub] は、OPC Publisher モジュールからクラウドに送信されたデータを受け取り、Azure TSI サービスから利用できる状態にします。 

ソリューションの IoT Hub は、以下の操作も行います。
- すべての OPC Publisher モジュールとすべての OPC Proxy モジュールの ID を格納する ID レジストリを維持します。
- OPC Proxy モジュールの双方向の通信用の転送プロトコルとして使用されます。

## <a name="azure-storage"></a>Azure Storage
このソリューションでは、Azure Blob Storage を VM のディスク ストレージとして使用したり、デプロイ データの保存に使用したりします。

## <a name="web-app"></a>Web アプリ
事前構成済みソリューションの一部としてデプロイされた Web アプリは、統合された OPC UA クライアント、アラートの処理、テレメトリの視覚化で構成されています。

## <a name="next-steps"></a>次のステップ

引き続き IoT Suite の概要について学習するには、次の記事を参照してください。

* [azureiotsuite.com サイトでのアクセス許可][lnk-permissions]
* [構成済みのコネクテッド ファクトリ ソリューション用のゲートウェイを Windows または Linux 上にデプロイする](iot-suite-connected-factory-gateway-deployment.md)

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-suite-permissions.md

