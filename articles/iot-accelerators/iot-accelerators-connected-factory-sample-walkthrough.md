---
title: 接続済みファクトリ ソリューションのチュートリアル - Azure | Microsoft Docs
description: Azure IoT ソリューション アクセラレータの接続済みファクトリとそのアーキテクチャの説明。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 950d248d2525f053981c8642ee2d39021b9a0494
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490361"
---
# <a name="connected-factory-solution-accelerator-walkthrough"></a>接続済みファクトリ ソリューション アクセラレータのチュートリアル

接続済みファクトリ [ソリューション アクセラレータ][lnk-preconfigured-solutions]は、次の処理を行うエンド ツー エンドの産業ソリューションの実装です。

* シミュレートされた工場生産ラインで OPC UA サーバーを実行しているシミュレートされた産業デバイスと、実際の OPC UA サーバー デバイスの両方に接続します。 OPC UA の詳細については、[接続済みファクトリの FAQ](iot-accelerators-faq-cf.md) を参照してください。
* このようなデバイスと生産ラインの運用 KPI と OEE を表示します。
* OPC UA サーバー システムを操作するためにクラウドベースのアプリケーションがどのように使用されているかを示します。
* 独自の OPC UA サーバー デバイスを接続できるようにします。
* OPC UA サーバー データを参照および変更できるようにします。
* Azure Time Series Insights (TSI) サービスと統合し、OPC UA サーバーのデータのカスタマイズしたビューを提供します。

このソリューションを独自の実装の出発点として使用し、独自のビジネス要件を満たすように[カスタマイズ][lnk-customize]することができます。

この記事では、接続済みファクトリ ソリューションのしくみを理解できるように、その主な構成要素のいくつかについて説明します。 この記事では、ソリューションにおけるデータのフローについても説明します。 この知識は以下の作業に役立ちます。

* ソリューションの問題のトラブルシューティングを行う。
* 独自の要件を満たすためにソリューションをカスタマイズする方法を計画する。
* Azure サービスを利用する独自の IoT ソリューションを設計する。

詳細については、[接続済みファクトリの FAQ](iot-accelerators-faq-cf.md) を参照してください。

## <a name="logical-architecture"></a>論理アーキテクチャ

次の図は、ソリューション アクセラレータの論理コンポーネントの概要を示したものです。

![接続済みファクトリの論理アーキテクチャ][connected-factory-logical]

## <a name="communication-patterns"></a>通信パターン

ソリューションは、[OPC UA Pub/Sub 仕様](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/)を使用して、OPC UA テレメトリ データを IoT Hub に JSON 形式で送信します。 この目的のために、ソリューションは [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) IoT Edge モジュールを使用します。

ソリューションには、オンプレミスの OPC UA サーバーとの接続を確立できる Web アプリケーションに統合された OPC UA クライアントもあります。 クライアントは[リバースプロキシ](https://wikipedia.org/wiki/Reverse_proxy)を使用し、IoT Hub からの支援を受けて、オンプレミスのファイアウォールでポートを開かずに接続を確立します。 この通信方式は、サービス支援通信と呼ばれます。 この目的のために、ソリューションは [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy/) IoT Edge モジュールを使用します。


## <a name="simulation"></a>シミュレーション

工場生産ラインは、シミュレートされたステーションと、シミュレートされた生産実行システム (MES) で構成されます。 シミュレートされたデバイスと OPC Publisher モジュールは、OPC Foundation が公開した [OPC UA .NET 標準][lnk-OPC-UA-NET-Standard]に基づいています。

OPC Proxy と OPC Publisher は、[Azure IoT Edge][lnk-Azure-IoT-Gateway] に基づくモジュールとして実装されます。 シミュレートされた生産ラインごとに、ゲートウェイが接続されています。

シミュレーション コンポーネントはすべて、Azure Linux VM でホストされている Docker コンテナーで実行されます。 このシミュレーションは、シミュレートされた生産ラインを既定で 8 つ実行するように構成されています。

## <a name="simulated-production-line"></a>シミュレートされた生産ライン

生産ラインではパーツを製造します。 これは、組立ステーション、テスト ステーション、梱包ステーションという各種ステーションで構成されます。

このシミュレーションが実行されると、OPC UA ノードを通じて使用可能なデータが更新されます。 シミュレートされた生産ラインのステーションはすべて、OPC UA を通じて MES によって調整されます。

## <a name="simulated-manufacturing-execution-system"></a>シミュレートされた生産実行システム

MES は、OPC UA を通じて生産ラインの各ステーションを監視し、ステーションの状態変化を検出します。 これは、OPC UA メソッドを呼び出してステーションを制御し、製品が完成するまでその製品をあるステーションから次のステーションへと渡します。

## <a name="gateway-opc-publisher-module"></a>Gateway OPC Publisher モジュール

OPC Publisher モジュールは、ステーションの OPC UA サーバーに接続し、発行する OPC ノードにサブスクライブします。 モジュール:

1. ノード データを JSON 形式に変換します。
1. JSON を暗号化します。
1. OPC UA 発行/定期メッセージとして JSON を IoT Hub に送信します。

OPC Publisher モジュールは、送信 HTTPS ポート (443) のみを必要とし、既存のエンタープライズ インフラストラクチャで使用できます。

## <a name="gateway-opc-proxy-module"></a>Gateway OPC Proxy モジュール

Gateway OPC UA Proxy モジュールは、バイナリの OPC UA コマンドと制御メッセージをトンネリングし、送信 HTTPS ポート (443) のみを必要とします。 これは、Web プロキシなど、既存のエンタープライズ インフラストラクチャで使用できます。

このモジュールは、IoT Hub デバイスのメソッドを使用して、アプリケーション層でパケット化された TCP/IP データを転送し、SSL/TLS を使用してエンドポイントの信頼、データの暗号化、整合性を確保します。

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

TSI は、時間ベースの **SearchSpan** を使用してノード データに対してクエリを実行し、**OPC UA ApplicationUri**、**OPC UA NodeId**、または **OPC UA DisplayName** 別に集計します。

OEE および KPI ゲージのデータと時系列のグラフを取得するために、ソリューションは、イベントの数、**Sum**、**Avg**、**Min**、**Max** 別にデータを集計します。

時系列は、さまざまなプロセスを使用して構築されます。 ソリューションは、ステーション ベース データから OEE と KPI の値を計算し、生産ライン、ファクトリ、およびエンタープライズ用に値のバブルを作成します。

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
ソリューション アクセラレータの一部としてデプロイされた Web アプリには、統合された OPC UA クライアント、アラートの処理、テレメトリの視覚化が含まれています。

## <a name="telemetry-data-flow"></a>テレメトリ データ フロー

![テレメトリ データ フロー](./media/iot-accelerators-connected-factory-sample-walkthrough/telemetry_dataflow.png)

### <a name="flow-steps"></a>フローのステップ

1. OPC Publisher が、必要な OPC UA X509 証明書と IoT Hub セキュリティ資格情報をローカル証明書ストアから読み取ります。
    - OPC Publisher は、必要に応じて、不足している証明書や資格情報を作成し、証明書ストアに保存します。

2. OPC Publisher が、IoT Hub に自身を登録します。
    - 構成されているプロトコルを使用します。 IoT Hub クライアント SDK でサポートされる任意のプロトコルを使用できます。 既定値は MQTT です。
    - プロトコルの通信は TLS によって保護されます。

3. OPC Publisher が、構成ファイルを読み取ります。

4. OPC Publisher が、構成されたそれぞれの OPC UA サーバーで OPC セッションを作成します。
    - TCP 接続を使用します。
    - OPC Publisher と OPC UA サーバーが、X509 証明書を使用して相互に認証します。
    - それ以降のすべての OPC UA トラフィックは、構成された OPC UA 暗号化メカニズムによって暗号化されます。
    - OPC Publisher が、構成されているそれぞれの発行間隔の OPC セッションで OPC サブスクリプションを作成します。
    - OPC サブスクリプションで発行するために、OPC ノードの OPC 監視対象項目を作成します。

5. 監視対象の OPC ノード値が変更されると、OPC UA サーバーが OPC Publisher に更新を送信します。

6. OPC Publisher が、新しい値をトランスコードします。
    - バッチ処理が有効な場合、複数の変更をバッチ処理します。
    - IoT Hub メッセージを作成します。

7. OPC Publisher が、IoT Hub にメッセージを送信します。
    - 構成されているプロトコルを使用します。
    - 通信は TLS によって保護されます。

8. Time Series Insights (TSI) が、IoT Hub からメッセージを読み取ります。
    - TCP/TLS で AMQP を使用します。
    - このステップは、データセンターの内部処理です。

9. TSI の保存データ。

10. Azure AppService の接続済みファクトリ WebApp が、TSI に対して必要なデータを照会します。
    - TCP/TLS のセキュリティで保護された通信を使用します。
    - このステップは、データセンターの内部処理です。

11. Web ブラウザーが、接続済みファクトリ WebApp に接続します。
    - 接続済みファクトリ ダッシュボードを表示します。
    - HTTPS で接続します。
    - 接続済みファクトリ アプリにアクセスするには、Azure Active Directory を介したユーザーの認証が必要です。
    - 接続済みファクトリ アプリへのすべての Web API 呼び出しは、偽造防止トークンによって保護されます。

12. データが更新されると、接続済みファクトリ WebApp が、更新されたデータを Web ブラウザーに送信します。
    - SignalR プロトコルを使用します。
    - TCP/TLS によって保護されます。

## <a name="browsing-data-flow"></a>ブラウズ操作のデータ フロー

![ブラウズ操作のデータ フロー](./media/iot-accelerators-connected-factory-sample-walkthrough/browsing_dataflow.png)

### <a name="flow-steps"></a>フローのステップ

1. OPC プロキシ (サーバー コンポーネント) が起動されます。
    - ローカル ストアから共有アクセス キーを読み取ります。
    - 必要に応じて、不足しているアクセス キーをストアに格納します。

2. OPC Proxy (サーバー コンポーネント) が、IoT Hub に自身を登録します。
    - IoT Hub から既知のすべてのデバイスを読み取ります。
    - Socket または Secure WebSocket 上で MQTT over TLS を使用します。

3. Web ブラウザーが、接続済みファクトリ WebApp に接続し、接続済みファクトリ ダッシュボードを表示します。
    - HTTPS を使用します。
    - ユーザーが、接続先の OPC UA サーバーを選択します。

4. 接続済みファクトリ WebApp が、選択された OPC UA サーバーへの OPC UA セッションを確立します。
    - OPC UA スタックを使用します。

5. OPC プロキシ転送によって OPC UA スタックからの要求が受信され、OPC UA サーバーへの TCP ソケット接続が確立されます。
    - このとき、単に TCP ペイロードが取得され、そのまま使用されます。
    - このステップは、接続済みファクトリ WebApp の内部処理です。

6. OPC プロキシ (クライアント コンポーネント) が、IoT Hub デバイス レジストリ内で OPC プロキシ (サーバー コンポーネント) デバイスを検索します。 次に、IoT Hub で OPC プロキシ (サーバー コンポーネント) デバイスのデバイス メソッドを呼び出します。
    - TCP/TLS で HTTPS を使用して、OPC プロキシを検索します。
    - TCP/TLS で HTTPS を使用して、OPC UA サーバーへの TCP ソケット接続を確立します。
    - このステップは、データセンターの内部処理です。

7. IoT Hub が、OPC プロキシ (サーバー コンポーネント) デバイスのデバイス メソッドを呼び出します。
    - Socket または Secure Websocket 接続上で確立された MQTT over TLS を使用して、OPC UA サーバーへの TCP ソケット接続を確立します。

8. OPC Proxy (サーバー コンポーネント) が、TCP ペイロードをショップフロア ネットワークに送信します。

9. OPC UA サーバーが、ペイロードを処理し、応答を返します。

10. 応答が OPC プロキシ (サーバー コンポーネント) のソケットによって受信されます。
    - OPC プロキシが、データをデバイス メソッドの戻り値として IoT Hub および OPC プロキシ (クライアント コンポーネント) に送信します。
    - このデータは、接続済みファクトリ アプリの OPC UA スタックに配信されます。

11. 接続済みファクトリ WebApp が、OPC UA サーバーから受信した OPC UA に固有の情報が付加された OPC ブラウザー UX を、レンダリングのために Web ブラウザーに返します。
    - ユーザーは、OPC アドレス空間を介してブラウズし、OPC アドレス空間内のノードに機能を適用する一方で、OPC ブラウザー UX クライアントが、偽造防止トークンで保護された HTTPS で AJAX 呼び出しを使用して、接続済みファクトリ WebApp からデータを取得します。
    - クライアントは、必要に応じて、ステップ 4 ～ 10 で説明した通信を使用して、OPC UA サーバーと情報を交換します。

> [!NOTE]
> OPC プロキシ (サーバー コンポーネント) および OPC プロキシ (クライアント) コンポーネントは、OPC UA 通信に関連するすべての TCP トラフィックについてステップ 4 ～ 10 を実行します。

> [!NOTE]
> OPC UA サーバーおよび接続済みファクトリ WebApp 内の OPC UA スタックに対して OPC プロキシ通信は透過的であり、認証および暗号化のためのすべての OPC UA セキュリティ機能が適用されます。

## <a name="next-steps"></a>次の手順

引き続き IoT ソリューション アクセラレータの概要について学習するには、次の記事を参照してください。

* [azureiotsolutions.com サイトでのアクセス許可][lnk-permissions]
* [接続済みファクトリ ソリューション アクセラレータ用ゲートウェイを Windows または Linux 上にデプロイする](iot-accelerators-connected-factory-gateway-deployment.md)
* [OPC Publisher のリファレンス実装](https://github.com/Azure/iot-edge-opc-publisher/blob/master/README.md)。

[connected-factory-logical]:media/iot-accelerators-connected-factory-sample-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]:about-iot-accelerators.md
[lnk-customize]: iot-accelerators-connected-factory-customize.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-accelerators-faq.md
