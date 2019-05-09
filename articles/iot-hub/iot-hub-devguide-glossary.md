---
title: Azure IoT Hub 用語集 | Microsoft Docs
description: 開発者ガイド - Azure IoT Hub に関連する一般用語の用語集。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: 6f89e27b06179c33857d581c0c6e3fc78c683d48
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793457"
---
# <a name="glossary-of-iot-hub-terms"></a>IoT Hub 用語集
この記事では、IoT Hub の記事で使用される一般的な用語の一部を示します。

## <a name="advanced-message-queueing-protocol"></a>Advanced Message Queueing Protocol
[Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) は、デバイスと通信するために [IoT Hub](#iot-hub) がサポートするメッセージング プロトコルの 1 つです。 IoT Hub がサポートするメッセージング プロトコルの詳細については、「[IoT Hub でのメッセージの送受信](iot-hub-devguide-messaging.md)」を参照してください。

## <a name="automatic-device-management"></a>自動デバイス管理
Azure IoT Hub の自動デバイス管理は、大規模なデバイス集団のライフサイクル全体を管理する複雑な反復タスクの多くを自動化します。 自動デバイス管理を利用すると、プロパティに基づいて対象のデバイス セットを設定し、必要な構成を定義して、デバイスがスコープ内になったときにいつでも IoT Hub がデバイスを更新できるようにすることができます。  [自動デバイス構成](iot-hub-auto-device-config.md)と [IoT Edge の自動展開](../iot-edge/how-to-deploy-monitor.md)で構成されます。

## <a name="automatic-device-configuration"></a>自動デバイス構成
ソリューション バックエンドは、[自動デバイス構成](iot-hub-auto-device-config.md)を使用することで、[デバイス ツイン](#device-twin)のセットに必要なプロパティを割り当て、システム メトリックとカスタム メトリックを使って状態を報告することができます。 

## <a name="azure-classic-cli"></a>Azure クラシック CLI
[Azure クラシック CLI](../cli-install-nodejs.md) は、Microsoft Azure 上のリソースを作成したり、管理したりするためのクロスプラットフォーム、オープンソース、かつシェルベースのコマンド ツールです。 このバージョンの CLI は、クラシック デプロイのみで使用してください。

## <a name="azure-cli"></a>Azure CLI
[Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) は、Microsoft Azure 上のリソースを作成したり管理したりするための、クロスプラットフォーム、オープンソース、かつシェルベースのコマンド ツールです。


## <a name="azure-iot-device-sdks"></a>Azure IoT device SDK
複数の言語で使用可能な _デバイス SDK_ があり、IoT Hub と対話する[デバイス アプリ](#device-app)を作成できます。 IoT Hub のチュートリアルでは、これらのデバイス SDK を使用する方法を説明します。 デバイス SDK のソース コードと詳細については、この GitHub [リポジトリ](https://github.com/Azure/azure-iot-sdks)を参照してください。

## <a name="azure-iot-service-sdks"></a>Azure IoT サービス SDK
複数の言語で使用可能な _サービス SDK_ があり、IoT Hub と対話する[バックエンド アプリ](#back-end-app)を作成できます。 IoT Hub のチュートリアルでは、これらのサービス SDK を使用する方法を説明します。 サービス SDK のソース コードと詳細については、この GitHub [リポジトリ](https://github.com/Azure/azure-iot-sdks)を参照してください。

## <a name="azure-iot-tools"></a>Azure IoT Tools
[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) は、VS Code で Azure IoT Hub やデバイスを管理するのに役立つ、クロスプラットフォームでかつオープンソースの Visual Studio Code 拡張機能です。 Azure IoT Tools を使用すると、IoT 開発者は VS Code で IoT プロジェクトを容易に開発できます。

## <a name="azure-portal"></a>Azure ポータル
Azure リソースのプロビジョニングと管理は、[Microsoft Azure Portal](https://portal.azure.com) を使って 1 か所で行うことができます。 Azure Portal は、_ブレード_ を使用してそのコンテンツを整理します。

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) は、Windows PowerShell で Azure を管理するために使用できるコマンドレットのコレクションです。 このコマンドレットを使用して、Azure Platform から配信されるソリューションやサービスを、作成、テスト、デプロイ、管理できます。

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) を使用すると、ソリューション内の複数のリソースを 1 つのグループとして作業できます。 ソリューションのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。

## <a name="azure-service-bus"></a>Azure Service Bus
[Service Bus](../service-bus/index.md) は、エンタープライズ メッセージングと中継通信の機能を持つクラウド対応通信で、オンプレミスのソリューションをクラウドに接続するのに役立ちます。 一部の IoT Hub チュートリアルでは、Service Bus [キュー](../service-bus-messaging/service-bus-messaging-overview.md)を利用します。

## <a name="azure-storage"></a>Azure Storage
[Azure Storage](../storage/common/storage-introduction.md) は、クラウド ストレージ ソリューションです。 非構造化オブジェクト データの格納に使用できる Blob Storage サービスが含まれています。 一部の IoT Hub チュートリアルでは、Blob Storage を使用します。

## <a name="back-end-app"></a>バックエンド アプリ
[IoT Hub](#iot-hub) においては、バックエンド アプリは、IoT Hub 上のサービス向けエンドポイントのいずれかに接続するアプリです。 たとえば、バックエンド アプリは[デバイスからクラウド](#device-to-cloud)へのメッセージの取得や、[ID レジストリ](#identity-registry)の管理を行います。 通常、バック エンド アプリはクラウドで実行されますが、チュートリアルの多くでは、バックエンド アプリはローカル開発用コンピューターで実行されるコンソール アプリです。

## <a name="built-in-endpoints"></a>組み込みのエンドポイント
すべての IoT Hub には、Event Hub と互換性がある組み込みの[エンドポイント](iot-hub-devguide-endpoints.md)が含まれています。 Event Hub で動作する任意のメカニズムを使用して、このエンドポイントからクラウドへのデバイスのメッセージを読み取ることができます。

## <a name="cloud-gateway"></a>クラウド ゲートウェイ
クラウド ゲートウェイは、[IoT Hub](#iot-hub) に直接接続できないデバイスの接続を有効にします。 デバイスでローカルに実行される[フィールド ゲートウェイ](#field-gateway)とは異なり、クラウド ゲートウェイはクラウドでホストされます。 クラウド ゲートウェイの一般的なユース ケースは、デバイスにプロトコル変換を実装することです。

## <a name="cloud-to-device"></a>クラウドからデバイスへ
IoT Hub から接続されたデバイスに送信されるメッセージを指します。 多くの場合、これらのメッセージは、デバイスにアクションを実行することを指示するコマンドです。 詳細については、「[IoT Hub でのメッセージの送受信](iot-hub-devguide-messaging.md)」を参照してください。

## <a name="configuration"></a>構成
[自動デバイス構成](iot-hub-auto-device-config.md)のコンテキストでは、IoT Hub 内の構成は、デバイス ツインのセットに必要な構成を定義し、状態と進行状況を報告するためのメトリックのセットを提供します。

## <a name="connection-string"></a>接続文字列
アプリのコード内の接続文字列を使用して、エンドポイントへの接続に必要な情報をカプセル化します。 通常、接続文字列には、エンドポイントのアドレスとセキュリティ情報が含まれますが、接続文字列の形式はサービス間で異なります。 IoT Hub サービスに関連付けられた接続文字列には次の 2 種類があります。
- *デバイス接続文字列* を使用すると、IoT Hub 上のデバイス向けエンドポイントにデバイスを接続できます。
- *接続文字列* を使用すると、IoT Hub 上のサービス向けエンドポイントにバックエンド アプリを接続できます。

## <a name="custom-endpoints"></a>カスタム エンドポイント
[ルーティング ルール](#routing-rules)によって送信されたメッセージを配信するカスタム [エンドポイント](iot-hub-devguide-endpoints.md)を IoT hub に作成できます。 カスタム エンドポイントは、Event hub、Service Bus キュー、または Service Bus トピックに直接接続します。

## <a name="custom-gateway"></a>カスタム ゲートウェイ
ゲートウェイは、[IoT Hub](#iot-hub) に直接接続できないデバイスの接続を有効にします。 Azure IoT Edge を使用して、メッセージを処理するカスタム ロジック、カスタム プロトコルの変換、およびその他の処理を Edge 上で実装するカスタム ゲートウェイを構築できます。

## <a name="data-point-message"></a>データ ポイント メッセージ
データ ポイント メッセージは、風速や温度などの[テレメトリ](#telemetry) データを含む[デバイスからクラウドへの](#device-to-cloud)メッセージです。

## <a name="desired-configuration"></a>必要な構成
[デバイス ツイン](iot-hub-devguide-device-twins.md)においては、必要な構成とは、デバイスと同期する必要がある、デバイス ツイン内のプロパティとメタデータの完全なセットを指します。

## <a name="desired-properties"></a>必要なプロパティ
[デバイス ツイン](iot-hub-devguide-device-twins.md)においては、必要なプロパティは、デバイスの構成や状態を同期するために[報告されるプロパティ](#reported-properties)とともに使用される、デバイス ツインのサブセクションです。 必要なプロパティは[バックエンド アプリ](#back-end-app)のみで設定でき、[デバイス アプリ](#device-app)によって監視されます。

## <a name="device-to-cloud"></a>デバイスからクラウドへ
接続されたデバイスから [IoT Hub](#iot-hub) に送信されるメッセージを指します。 これらのメッセージは、[データ ポイント](#data-point-message)または[対話型](#interactive-message)メッセージの場合があります。 詳細については、「[IoT Hub でのメッセージの送受信](iot-hub-devguide-messaging.md)」を参照してください。

## <a name="device"></a>Device
IoT においては、デバイスは、通常は、データの収集や他のデバイスの制御を実行できる、小規模なスタンドアロン コンピューティング デバイスです。 環境監視デバイス (温室の散水システムや換気システムのコントローラー) はデバイスの一例です。 [デバイス カタログ](https://catalog.azureiotsolutions.com/)は、[IoT Hub](#iot-hub) で機能することが認定されたハードウェア デバイスの一覧を示します。

## <a name="device-app"></a>デバイス アプリ
デバイス アプリは、[デバイス](#device)で実行され、[IoT Hub](#iot-hub) との通信を処理します。 デバイス アプリを実装する場合、通常 [Azure IoT device SDK](#azure-iot-device-sdks) のいずれかを使用します。 IoT チュートリアルの多くでは、利便性のために[シミュレートされたデバイス](#simulated-device)を使用します。

## <a name="device-condition"></a>デバイスの状態
[デバイス アプリ](#device-app)によって報告されるデバイスの状態情報 (現在使用中の接続方法など) を指します。 [デバイス アプリ](#device-app)は、そのデバイスが備えている機能も報告できます。 デバイス ツインを使用して、状態と機能の情報のクエリを実行できます。

## <a name="device-data"></a>デバイス データ
デバイス データとは、IoT Hub の [ID レジストリ](#identity-registry)に格納されている個々のデバイスのデータを指します。 このデータのインポートおよびエクスポートを行うことができます。

## <a name="device-explorer"></a>デバイス エクスプローラー
[Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) は Windows で実行されるツールで、[ID レジストリ](#identity-registry)でデバイスを管理できます。また、デバイスへのメッセージを送受信することもできます。

## <a name="device-identity"></a>デバイス ID
デバイス ID は、[ID レジストリ](#identity-registry)に登録されているすべてのデバイスに割り当てられた、一意の識別子です。

## <a name="device-management"></a>デバイス管理
デバイス管理には、計画、プロビジョニング、構成、監視、インベントリからの削除などを含む IoT ソリューション内のデバイスの管理に関連するライフ サイクル全体が含まれます。

## <a name="device-management-patterns"></a>デバイス管理パターン
[IoT Hub](#iot-hub) では、デバイスでの再起動、工場出荷時リセットの実行、ファームウェア更新プログラムの実行など、一般的なデバイス管理のパターンを使用できます。

## <a name="device-rest-api"></a>デバイス REST API
デバイスから [Device REST API](https://docs.microsoft.com/rest/api/iothub/device) を使用して、デバイスからクラウドへのメッセージを IoT Hub に送信し、[クラウドからデバイスへ](#cloud-to-device)のメッセージを IoT Hub から受信できます。 IoT Hub チュートリアルで示されているように、通常、高レベルの[デバイス SDK](#azure-iot-device-sdks) のいずれかを使用する必要があります。

## <a name="device-provisioning"></a>デバイス プロビジョニング
デバイス プロビジョニングとは、最初の[デバイス データ](#device-data)をソリューション内のストアに追加するプロセスです。 新しいデバイスをハブに接続できるようにするには、デバイスの ID とキーを IoT Hub [ID レジストリ](#identity-registry)に追加する必要があります。 プロビジョニング プロセスの一環として、他のソリューション ストアにあるデバイス固有データの初期化が必要になる場合があります。

## <a name="device-twin"></a>デバイス ツイン
[デバイス ツイン](iot-hub-devguide-device-twins.md)は、デバイスの状態に関する情報 (メタデータ、構成、状態など) を格納する JSON ドキュメントです。 [IoT Hub](#iot-hub) は、IoT Hub でプロビジョニングする各デバイスにデバイス ツインを保持します。 デバイス ツインを使用すると、デバイスとソリューションのバック エンド間で[デバイスの状態](#device-condition)と構成を同期できます。 デバイス ツインにクエリを実行して、特定のデバイスを見つけ、長時間実行されている操作の状態にクエリを実行できます。

## <a name="direct-method"></a>ダイレクト メソッド
[ダイレクト メソッド](iot-hub-devguide-direct-methods.md)は、IoT Hub 上で API を呼び出すことによって、デバイス上で実行するメソッドをトリガーするための方法です。

## <a name="endpoint"></a>エンドポイント
IoT Hub は、アプリの IoT Hub への接続を有効にする複数の[エンドポイント](iot-hub-devguide-endpoints.md)を公開します。 [デバイスからクラウドへ](#device-to-cloud)のメッセージの送信や、[クラウドからデバイスへ](#cloud-to-device)のメッセージの受信といった操作をデバイスで実行できるようにする、デバイス向けのエンドポイントがあります。 [デバイス ID](#device-identity) の管理とデバイス ツインの管理といった操作を[バックエンド アプリ](#back-end-app)で実行できるようにする、サービス向けの管理エンドポイントがあります。 デバイスからクラウドへのメッセージを読み取るためのサービス向けの[組み込みエンドポイント](#built-in-endpoints)があります。 [ルーティング ルール](#routing-rules)によって送信されたデバイスからクラウドへのメッセージを受信するカスタム [エンドポイント](#custom-endpoints)を作成できます。

## <a name="event-hubs-service"></a>Event Hubs サービス
[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) は、拡張性の高いデータ イングレス サービスであり、1 秒間に数百万件のイベントを取り込むことができます。 このサービスを使用すれば、接続デバイスやアプリケーションが生成する膨大な量のデータを処理し、分析することができます。 IoT Hub サービスとの比較については、「[Comparison of Azure IoT Hub and Azure Event Hubs (Azure IoT Hub と Azure Event Hubs の比較)](iot-hub-compare-event-hubs.md)」を参照してください。

## <a name="event-hub-compatible-endpoint"></a>Event Hub と互換性があるエンドポイント
IoT Hub に送信される[デバイスからクラウドへ](#device-to-cloud)のメッセージを読み取るには、ハブ上のエンドポイントに接続し、Event Hub と互換性があるメソッドを使用してそれらのメッセージを読み取ることができます。 Event Hub と互換性があるメソッドには、[Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) と [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) の使用が含まれます。

## <a name="field-gateway"></a>フィールド ゲートウェイ
フィールド ゲートウェイは、[IoT Hub](#iot-hub) に直接接続できないデバイスを接続できるようにします。通常はデバイスにローカルにデプロイされます。 詳細については、「[Azure IoT Hub とは](about-iot-hub.md)」を参照してください。

## <a name="free-account"></a>無料アカウント
[無料の Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成して、IoT Hub チュートリアルを完了し、IoT Hub サービス (および他の Azure サービス) を試すことができます。

## <a name="gateway"></a>Gateway
ゲートウェイは、[IoT Hub](#iot-hub) に直接接続できないデバイスの接続を有効にします。 [フィールド ゲートウェイ](#field-gateway)、[クラウド ゲートウェイ](#cloud-gateway)および[カスタム ゲートウェイ](#custom-gateway)もご確認ください。

## <a name="identity-registry"></a>ID レジストリ
[ID レジストリ](iot-hub-devguide-identity-registry.md)は、IoT Hub に接続することを許可された個々のデバイスに関する情報を保存する IoT Hub の組み込みコンポーネントです。

## <a name="interactive-message"></a>対話型メッセージ
対話型メッセージは、ソリューション バックエンドの即時動作をトリガーする[クラウドからデバイスへ](#cloud-to-device)のメッセージです。 たとえば、デバイスは、CRM システムに自動的に記録される必要がある障害に関するアラームを送信できます。

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
IoT Hub は、何百万ものデバイスとソリューションのバックエンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、フル マネージドの Azure サービスです。 詳細については、「[Azure IoT Hub とは](about-iot-hub.md)」を参照してください。 [Azure サブスクリプション](#subscription)を使用して、IoT メッセージングのワークロードを処理する IoT Hub を作成できます。

## <a name="iot-hub-metrics"></a>IoT Hub メトリック
[IoT Hub メトリック](iot-hub-metrics.md)は、[Azure サブスクリプション](#subscription)内の IoT Hub の状態に関するデータを提供します。 IoT Hub メトリックにより、ユーザーはサービスとそれに接続されたデバイスの全体的な正常性を評価することができます。 IoT Hub メトリックは、IoT Hub で起こっていることを確認するため、また、Azure サポートに連絡することなく問題の根本原因を調査するための有用な情報となります。

## <a name="iot-hub-query-language"></a>IoT Hub クエリ言語
[IoT Hub クエリ言語](iot-hub-devguide-query-language.md)は SQL に似た言語であり、[ジョブ](#job)とデバイス ツインにクエリを実行できます。

## <a name="iot-hub-resource-rest-api"></a>IoT Hub リソース REST API
[IoT Hub リソース REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) を使用して、ハブの作成、更新、削除などの操作を実行する [Azure サブスクリプション](#subscription)の IoT Hub を管理できます。

## <a name="iot-solution-accelerators"></a>IoT ソリューション アクセラレータ
Azure IoT ソリューション アクセラレータは、複数の Azure サービスをソリューションにパッケージ化します。 これらのソリューションを使用すると、一般的な IoT シナリオをエンド ツー エンドで実装して、すぐに使い始めることができます。 詳しくは、[Azure IoT ソリューション アクセラレータの概要](../iot-accelerators/about-iot-accelerators.md)に関するページをご覧ください。

## <a name="the-iot-extension-for-azure-cli"></a>Azure CLI の IoT 拡張機能 
[Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)は、クロスプラットフォームのコマンドライン ツールです。 このツールを使用すると、[ID レジストリ](#identity-registry)でデバイスを管理し、デバイスからメッセージやファイルを送受信し、IoT Hub の操作を監視できます。

## <a name="job"></a>ジョブ
ソリューションのバックエンドは、[ジョブ](iot-hub-devguide-jobs.md)を使用して、IoT Hub に登録されたデバイスでのアクティビティのスケジュール設定と追跡を実行できます。 アクティビティには、デバイス ツインの[必要なプロパティ](#desired-properties)の更新、デバイス ツインの[タグ](#tags)の更新、および[ダイレクト メソッド](#direct-method)の呼び出しが含まれます。 [IoT Hub](#iot-hub) は、ジョブを使用して [ID レジストリ](#identity-registry)との間で[インポートおよびエクスポート](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)も行います。

## <a name="modules"></a>モジュール
デバイス側では、IoT Hub デバイス SDK を使用して、それぞれが IoT Hub への独立した接続を確立する[モジュール](iot-hub-devguide-module-twins.md)を作成できます。 この機能により、デバイスのさまざまなコンポーネントごとに個別の名前空間を使用することができます。

モジュール ID とモジュール ツインは、[デバイス ID](#device-identity) と[デバイス ツイン](#device-twin)と同様の機能を、より細かい粒度で提供します。 このより細かい粒度により、オペレーティング システム ベースのデバイスやファームウェア デバイスなどの複数のコンポーネントで構成され、この機能をサポートするデバイスの各コンポーネントの構成と状態を特定できます。

## <a name="module-identity"></a>モジュール ID
モジュール ID は、デバイスに属しているすべてのモジュールに割り当てられる一意識別子です。 モジュール ID は、[ID レジストリ](#identity-registry)でも登録されます。

## <a name="module-twin"></a>モジュール ツイン
デバイス ツインと同様に、モジュール ツインは、モジュールの状態に関する情報 (メタデータ、構成、状態など) を格納する JSON ドキュメントです。 IoT Hub は、お使いの IoT ハブ内のデバイス ID でプロビジョニングされたモジュール ID ごとにモジュール ツインを永続化します。 モジュール ツインを使用すると、モジュールとソリューションのバックエンド間でモジュールの状態と構成を同期できます。 モジュール ツインにクエリを実行して、特定のモジュールを見つけ、長時間実行されている操作の状態にクエリを実行できます。

## <a name="mqtt"></a>MQTT
[MQTT](https://mqtt.org/) は、デバイスと通信するために [IoT Hub](#iot-hub) がサポートするメッセージング プロトコルの 1 つです。 IoT Hub がサポートするメッセージング プロトコルの詳細については、「[IoT Hub でのメッセージの送受信](iot-hub-devguide-messaging.md)」を参照してください。

## <a name="operations-monitoring"></a>操作の監視
IoT Hub の[操作の監視](iot-hub-operations-monitoring.md)では、IoT Hub に対する操作の状態をリアルタイムで監視することができます。 [IoT Hub](#iot-hub) は、複数のカテゴリにおよぶ操作のイベントを追跡します。 1 つ以上のカテゴリから IoT Hub のエンドポイントにイベントを送信して処理するように選択できます。 データを監視してエラーがないか確認したり、データ パターンに基づいてより複雑な処理をセットアップしたりできます。

## <a name="physical-device"></a>物理デバイス
物理デバイスは、Raspberry Pi など、IoT Hub に接続する実際のデバイスです。 便宜上、多くの IoT Hub チュートリアルでは、[シミュレートしたデバイス](#simulated-device)を使用して、ローカル コンピューター上でサンプルを実行できるようになっています。

## <a name="primary-and-secondary-keys"></a>主キーおよび 2 次キー
IoT Hub 上のデバイス向け、またはサービス向けエンドポイントに接続する場合、[接続文字列](#connection-string)には、アクセスを許可するためのキーが含まれます。 デバイスを [ID レジストリ](#identity-registry)に追加するか、[共有アクセス ポリシー](#shared-access-policy)をハブに追加すると、サービスによって主キーと 2 次キーが生成されます。 2 つのキーがあることにより、キーの更新時に、IoT Hub へのアクセスを失うことなく、1 つのキーから別のキーにロール オーバーできます。

## <a name="protocol-gateway"></a>プロトコル ゲートウェイ
プロトコル ゲートウェイは、通常はクラウドに展開され、[IoT Hub](#iot-hub) に接続するデバイスに対してプロトコル変換サービスを提供します。 詳細については、「[Azure IoT Hub とは](about-iot-hub.md)」を参照してください。

## <a name="quotas-and-throttling"></a>クォータと調整
[IoT Hub](#iot-hub)の使用に適用される[クォータ](iot-hub-devguide-quotas-throttling.md)はさまざまですが、多くのクォータは IoT Hub の層によって異なります。 [IoT Hub](#iot-hub) は、実行時のサービスの使用に[スロットル](iot-hub-devguide-quotas-throttling.md)も適用します。

## <a name="reported-configuration"></a>報告される構成
[デバイス ツイン](iot-hub-devguide-device-twins.md)においては、報告される構成とは、ソリューションのバック エンドに報告する必要がある、デバイス ツイン内のプロパティとメタデータの完全なセットを指します。

## <a name="reported-properties"></a>報告されるプロパティ
[デバイス ツイン](iot-hub-devguide-device-twins.md)においては、報告されるプロパティとは、デバイスの構成や状態を同期するために[必要なプロパティ](#desired-properties)とともに使用される、デバイス ツインのサブセクションです。 報告されるプロパティは[デバイス アプリ](#device-app)のみで設定でき、[バックエンド アプリ](#back-end-app)で読み取りおよびクエリの実行を行うことができます。

## <a name="resource-group"></a>リソース グループ
[Azure Resource Manager](#azure-resource-manager) は、リソース グループを使用して、関連するリソースをグループ化します。 リソース グループを使用して、グループのすべてのリソースに対して同時に操作を実行できます。

## <a name="retry-policy"></a>再試行ポリシー
クラウド サービスに接続する場合、再試行ポリシーを使用して、[一時的なエラー](/azure/architecture/best-practices/transient-faults)を処理します。

## <a name="routing-rules"></a>ルーティング ルール
デバイスからクラウドへのメッセージを、ソリューションのバックエンドで処理するために[組み込みエンドポイント](#built-in-endpoints)または[カスタム エンドポイント](#custom-endpoints)にルーティングする[ルーティング ルール](iot-hub-devguide-messages-read-custom.md)を IoT hub に構成します。

## <a name="sasl-plain"></a>SASL PLAIN
SASL PLAIN は、AMQP プロトコルがセキュリティ トークンを転送するために使用するプロトコルです。

## <a name="service-rest-api"></a>サービス REST API
ソリューションのバックエンドから [Service REST API](https://docs.microsoft.com/rest/api/iothub/service) を使用して、デバイスを管理できます。 API を使用して、[デバイス ツイン](#device-twin)のプロパティの取得と更新、[ダイレクト メソッド](#direct-method)の呼び出し、および[ジョブ](#job)のスケジュールを実行できます。 IoT Hub チュートリアルで示されているように、通常、高レベルの[サービス SDK](#azure-iot-service-sdks) のいずれかを使用する必要があります。

## <a name="shared-access-signature"></a>共有アクセス署名
Shared Access Signature (SAS) は、SHA-256 セキュア ハッシュまたは URI に基づいた認証メカニズムです。 SAS 認証には _共有アクセス ポリシー_ と _Shared Access Signature_ (多くの場合トークンと呼ばれます) という 2 つのコンポーネントがあります。 デバイスは、IoT Hub での認証に SAS を使用します。 [バック エンド アプリ](#back-end-app)も IoT Hub 上のサービス向けエンドポイントでの認証に SAS を使用します。 通常、SAS トークンは、IoT Hub への接続を確立するためにアプリが使用する[接続文字列](#connection-string)に含めます。

## <a name="shared-access-policy"></a>共有アクセス ポリシー
共有アクセス ポリシーは、そのポリシーに関連付けられた、有効な[主キーまたは 2 次キー](#primary-and-secondary-keys)を持つすべてのユーザーに許可する権限を定義します。 ハブの共有アクセス ポリシーとキーは、[ポータル](#azure-portal)で管理できます。

## <a name="simulated-device"></a>シミュレートされたデバイス
便宜上、IoT Hub チュートリアルの多くは、シミュレートされたデバイスを使用して、ローカル コンピューター上でサンプルを実行できるようにしています。 一方、[物理デバイス](#physical-device)は、Raspberry Pi など、IoT Hub に接続する実際のデバイスです。

## <a name="solution"></a>解決策
_ソリューション_ は、1 つまたは複数のプロジェクトを含む Visual Studio ソリューションを指す場合があります。 _ソリューション_ は、デバイス、[デバイス アプリ](#device-app)、IoT Hub、他の Azure サービス、[バックエンド アプリ](#back-end-app)などの要素を含む IoT ソリューションを指す場合もあります。

## <a name="subscription"></a>サブスクリプション
Azure サブスクリプションでは、課金が発生します。 作成する各 Azure リソース、または使用する各 Azure サービスは、1 つのサブスクリプションと関連付けられています。 多くのクォータは、サブスクリプションのレベルで適用されます。

## <a name="system-properties"></a>システム プロパティ
[デバイス ツイン](iot-hub-devguide-device-twins.md)においては、システム プロパティは読み取り専用であり、最後のアクティビティの時刻や接続状態などのデバイスの使用状況に関する情報が含まれています。

## <a name="tags"></a>Tags
[デバイス ツイン](iot-hub-devguide-device-twins.md)においては、タグは、ソリューション バックエンドによって JSON ドキュメントの形式で保存および取得されるデバイスのメタデータです。 タグは、デバイス上のアプリが認識することはありません。

## <a name="telemetry"></a>テレメトリ
デバイスは風速や温度などのテレメトリ データを収集し、データ ポイント メッセージを使用して IoT ハブにテレメトリを送信します。

## <a name="token-service"></a>トークン サービス
トークン サービスを使用して、デバイスに認証メカニズムを実装できます。 このサービスは、**DeviceConnect** アクセス許可が指定された IoT Hub [共有アクセス ポリシー](#shared-access-policy)を使用して、*デバイスを対象とする*トークンを作成します。 これらのトークンにより、デバイスは IoT Hub に接続できるようになります。 デバイスは、カスタム認証機構を使用して、トークン サービスで認証を受けます。 デバイスが正常に認証された場合、トークン サービスはデバイスに対して IoT Hub へのアクセスに使用する SAS トークンを発行します。

## <a name="twin-queries"></a>ツイン クエリ
[デバイスおよびモジュールのツイン クエリ](iot-hub-devguide-query-language.md)は、SQL に似た IoT Hub クエリ言語を使用して、デバイス ツインまたはモジュール ツインから情報を取得します。 同じ IoT Hub クエリ言語を使用して、IoT Hub で実行されている[ジョブ](#job)に関する情報を取得できます。

## <a name="twin-synchronization"></a>ツインの同期
ツインの同期では、デバイス ツインまたはモジュール ツインの[必要なプロパティ](#desired-properties)を使用してデバイスまたはモジュールを構成し、それらから[報告されたプロパティ](#reported-properties)を取得して、ツインに格納します。

## <a name="x509-client-certificate"></a>X.509 クライアント証明書
デバイスは X.509 証明書を使用して [IoT Hub](#iot-hub) で認証を受けることができます。 [SAS トークン](#shared-access-signature)の代わりに X.509 証明書を使用できます。
