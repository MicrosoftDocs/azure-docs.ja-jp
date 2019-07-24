---
title: リモート監視ソリューション アクセラレータの概要 - Azure | Microsoft Docs
description: リモート監視ソリューション アクセラレータの概要。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: af09ea39f373d518d5600e3fa46adc378fd9236d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176138"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>リモート監視ソリューション アクセラレータの概要

リモート監視[ソリューション アクセラレータ](../iot-accelerators/about-iot-accelerators.md)は、遠隔地で実行されている複数のコンピューターを対象としたエンド ツー エンドの監視ソリューションを実装します。 このソリューションは、主要な Azure サービスを組み合わせることで、ビジネス シナリオの汎用的な実装を実現したものです。 このソリューションを実装の出発点として使用し、お客様固有のビジネス要件を満たすように[カスタマイズ](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)することができます。

この記事では、リモート監視ソリューションのしくみについて理解しやすいように、その主な構成要素をいくつか取り上げて説明します。 この知識は以下の作業に役立ちます。

* ソリューションの問題のトラブルシューティングを行う。
* 独自の要件を満たすためにソリューションをカスタマイズする方法を計画する。
* Azure サービスを利用する独自の IoT ソリューションを設計する。

リモート監視ソリューション アクセラレータのコードは GitHub で入手できます:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>論理アーキテクチャ

次の図は、[IoT アーキテクチャ](../iot-fundamentals/iot-introduction.md)と重ね合わせたリモート監視ソリューション アクセラレータの論理コンポーネントの概要です。

![論理アーキテクチャ](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>マイクロサービスについて

クラウドのアーキテクチャは、Microsoft がはじめてソリューション アクセラレータをリリースした当時から進化し続けてきました。 [マイクロサービス](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)は、開発速度を犠牲にすることなく、拡張性と柔軟性を達成できることが実証された方法として登場してきました。 Microsoft のサービスは、このアーキテクチャ パターンを内部的に使用し、優れた信頼性とスケーラビリティの成果を出しています。 最新のソリューション アクセラレータには、これらの成果が取り込まれているため、その成果を活用できます。

> [!TIP]
> マイクロサービス アーキテクチャの詳細については、「[.NET Application Architecture (.NET アプリケーション アーキテクチャ)](https://www.microsoft.com/net/learn/architecture)」および「[Microservices:An application revolution powered by the cloud. (マイクロサービス: クラウドによって実現されるアプリケーションの革命)](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)」を参照してください。

## <a name="device-connectivity"></a>デバイスの接続性

ソリューションには、論理アーキテクチャのデバイス接続部分に、次のコンポーネントが含まれています。

### <a name="real-devices"></a>実際のデバイス

ソリューションには、実際のデバイスを接続できます。 Azure IoT device SDK を使用して、シミュレートされたデバイスの動作を実装できます。

ソリューション ポータルのダッシュボードから実際のデバイスをプロビジョニングできます。

### <a name="device-simulation-microservice"></a>デバイス シミュレーション マイクロサービス

ソリューションには、ソリューション内のフローのエンドツーエンド試験用にシミュレートされたデバイス プールを、ソリューション ポータルから管理するための[デバイス シミュレーション マイクロサービス](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation)が含まれています。 シミュレートされたデバイスは、以下を実行します。

* デバイスとクラウド間の利用統計情報を生成します。
* IoT Hub から送信された、クラウドとデバイス間で通信するためのメソッド呼び出しに応答します。

マイクロサービスは、シミュレーションを作成、開始、および停止するための RESTful エンドポイントを提供します。 各シミュレーションは、利用統計情報を送信したり、メソッドの呼び出しに応答するための、さまざまな種類の仮想デバイスで構成されます。

ソリューション ポータルのダッシュ ボードからシミュレートされたデバイスをプロビジョニングすることができます。

### <a name="iot-hub"></a>IoT Hub

[IoT ハブ](../iot-hub/index.yml)では、実際のデバイスとシミュレートされたデバイスの両方からクラウドに送信されたテレメトリが取り込まれます。 IoT ハブは、そのテレメトリを IoT ソリューション バックエンドでサービスが処理のために使用できるようにします。

ソリューションの IoT ハブは、以下の操作も行います。

* ポータルへの接続を許可されているすべてのデバイスの ID と認証キーを格納する ID レジストリを維持します。
* ソリューション アクセラレータに代わって、デバイスのメソッドを呼び出します。
* すべての登録済みデバイスのデバイス ツインを保持します。 デバイス ツインは、デバイスによって報告されたプロパティの値を格納します。 また、デバイスが次回の接続時に取得できるように、ソリューション ポータルで設定された必要なプロパティも格納します。
* 複数のデバイスのプロパティを設定したり、複数のデバイスに対してメソッドを呼び出したりするジョブをスケジュールします。

## <a name="data-processing-and-analytics"></a>データ処理と分析

ソリューションには、論理アーキテクチャのデータ処理および分析部分に、次のコンポーネントが含まれています。

### <a name="iot-hub-manager-microservice"></a>IoT Hub マネージャー マイクロサービス

ソリューションには、IoT ハブとの次のような交信を処理するための [IoT Hub マネージャー マイクロサービス](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)が含まれます。

* IoT デバイスの作成と管理
* デバイス ツインの管理
* デバイスでのメソッドの呼び出し
* IoT 資格情報の管理

このサービスは、ユーザー定義のグループに属するデバイスを取得するための IoT Hub クエリも実行します。

マイクロサービスは、デバイスおよびデバイス ツインの管理、メソッドの呼び出しや IoT Hub クエリを実行するための RESTful エンドポイントを提供します。

### <a name="device-telemetry-microservice"></a>デバイス テレメトリ マイクロサービス

[デバイス テレメトリ マイクロサービス](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry)は、Time Series Insights に格納されているデバイス テレメトリへの読み取りアクセスに RESTful エンドポイントを提供します。 RESTful エンドポイントは、ルールでの CRUD 操作とストレージからのアラーム定義への読み取り/書き込みアクセスも可能にします。

### <a name="storage-adapter-microservice"></a>ストレージ アダプター マイクロサービス

[ストレージ アダプター マイクロサービス](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter)は、キーと値のペアの管理、ストレージ サービス セマンティクスの抽象化、および Azure Cosmos DB を使用して任意の形式のデータを格納するシンプルなインターフェイスの提供を行います。

値はコレクションにまとめられます。 個別の値を使用することも、コレクション全体をフェッチすることもできます。 複雑なデータ構造は、クライアントによってシリアル化され、単純なテキスト ペイロードとして管理されます。

このサービスは、キーと値のペアに対する CRUD 操作に RESTful エンドポイントを提供します。 値

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

ソリューション アクセラレータのデプロイでは、[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) を使用して、ルール、アラート、構成設定、その他のすべてのコールド ストレージが格納されます。

### <a name="azure-stream-analytics-manager-microservice"></a>Azure Stream Analytics マネージャー マイクロサービス

[Azure Stream Analytics マネージャー マイクロサービス](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager)は、Azure Stream Analytics (ASA) ジョブを管理します。これには、ASA ジョブの構成の設定、開始と停止、および状態の監視が含まれます。

ASA ジョブは、2 つの参照データ セットによってサポートされます。 1 つのデータ セットはルールを定義し、もう 1 つはデバイス グループを定義します。 ルールの参照データは、デバイス テレメトリ マイクロサービスによって管理されている情報から生成されます。 Azure Stream Analytics マネージャー マイクロサービスは、テレメトリ ルールをストリーム処理ロジックに変換します。

デバイス グループの参照データは、テレメトリの受信メッセージに適用するルールのグループを識別するために使用されます。 デバイス グループは、構成マイクロサービスによって管理され、Azure IoT Hub デバイス ツイン クエリを使用します。

ASA ジョブは、Time Series Insights に接続されたデバイスからのテレメトリをストレージと分析のために提供します。

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) は、デバイスからの大量のデータ ストリームを分析することができるイベント処理エンジンです。

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) は、ソリューション アクセラレータに接続されたデバイスからのテレメトリを格納します。 また、ソリューション Web UI でのデバイス テレメトリの視覚化とクエリを実行することもできます。

> [!NOTE]
> Azure China クラウドでは、現在、Time Series Insights はご利用になれません。 Azure China クラウドでの新しいリモート監視ソリューション アクセラレータのデプロイでは、すべてのストレージに Cosmos DB を使用します。

### <a name="configuration-microservice"></a>構成マイクロサービス

[構成マイクロサービス](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)は、ソリューション アクセラレータでのデバイス グループ、ソリューション設定、およびユーザー設定に対する CRUD 操作に RESTful エンドポイントを提供します。 これはストレージ アダプター マイクロサービスと連携して、構成データを保持します。

### <a name="authentication-and-authorization-microservice"></a>認証および承認マイクロサービス

[認証および承認マイクロサービス](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth)は、ソリューション アクセラレータにアクセスする権限を持つユーザーを管理します。 ユーザー管理は、[OpenId Connect](https://openid.net/connect/) をサポートする任意の ID サービス プロバイダーを使用して行うことができます。

### <a name="azure-active-directory"></a>Azure Active Directory

ソリューション アクセラレータのデプロイでは、OpenID Connect プロバイダーとして [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) が使用されます。 Azure Active Directory は、ユーザー情報を格納し、JWT トークン署名を検証する証明書を提供します。

## <a name="presentation"></a>プレゼンテーション

ソリューションには、論理アーキテクチャのプレゼンテーション部分に、次のコンポーネントが含まれています。

[Web ユーザー インターフェイスは、React Javascript アプリケーション](https://github.com/Azure/pcs-remote-monitoring-webui)です。 アプリケーションでは

* Javascript React 　のみを使用し、すべてブラウザー内で実行されます。
* CSS スタイルを使用します。
* AJAX 呼び出しを通してパブリックのマイクロサービスと交信します。

ユーザー インターフェイスは、ソリューション アクセラレータのすべての機能を提供し、次のような他のマイクロサービスと交信します。

* ユーザー データを保護する認証および承認マイクロサービス。
* IoT デバイスを一覧表示して管理する IoT Hub マネージャー マイクロサービス。

ユーザー インターフェイスには、デバイス テレメトリのクエリと分析を可能にする Azure Time Series Insights エクスプローラーが統合されています。

構成マイクロサービスは、ユーザー インターフェイスで構成設定を保存および取得できるようにします。

## <a name="next-steps"></a>次の手順

ソース コードと開発者のマニュアルを参照する場合は、次の 2 つの GitHub リポジトリのいずれかを参照してください。

* [Azure IoT を使用するリモート監視のソリューション アクセラレータ (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)。
* [Azure IoT を使用するリモート監視のソリューション アクセラレータ (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)。

詳細なソリューションのアーキテクチャ図:
* [リモート監視ソリューション アクセラレータのアーキテクチャ](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture)。

リモート監視ソリューション アクセラレータの概念の詳細については、[ソリューション アクセラレータのカスタマイズ](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)に関するページを参照してください。
