---
title: リモート監視ソリューションのアーキテクチャの選択 - Azure | Microsoft Docs
description: この記事では、リモート監視に対して行ったアーキテクチャとテクノロジの選択について説明します
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464223"
---
# <a name="remote-monitoring-architectural-choices"></a>リモート監視のアーキテクチャの選択

Azure IoT リモート監視ソリューション アクセラレータは、オープン ソースで MIT ライセンスのソリューション アクセラレータです。 IoT 開発プロセスを短縮できるように、次のような一般的な IoT シナリオを紹介します。

- デバイスの接続性
- デバイス管理
- ストリーム処理

リモート監視ソリューションは、推奨の [Azure IoT リファレンス アーキテクチャ](https://aka.ms/iotrefarchitecture)に従っています。

この記事では、各リモート監視サブシステムで行ったアーキテクチャとテクノロジの重要な選択について説明します。 ただし、リモート監視ソリューションで Microsoft が行ったテクノロジの選択は、リモート監視 IoT ソリューションを実装する唯一の方法ではありません。 この技術的な実装は、成功するアプリケーションを構築するための基準として考え、次のために変更する必要があります。

- 組織内で利用できるスキルと経験に合わせる。
- 垂直アプリケーションのニーズに合わせる。

## <a name="architectural-choices"></a>アーキテクチャの選択

Microsoft が IoT アプリケーションに推奨するアーキテクチャは、クラウド ネイティブ、マイクロサービス、そしてサーバーレス ベースです。 IoT アプリケーションの各サブシステムは、独立してデプロイおよび拡張できる個別のサービスとして構築するようにします。 そうすることで、個々のサブシステムの更新のスケーラビリティと柔軟性が向上し、各サブシステムに適したテクノロジを柔軟に選択できます。

マイクロサービスは複数のテクノロジを使用して実装できます。 たとえば、次のいずれかのオプションを選択してマイクロサービスを実装できます。

- Azure Functions などのサーバーレス テクノロジと共に Docker などのコンテナー テクノロジを使用します。
- Azure App Services などの PaaS サービスでマイクロサービスをホストします。

## <a name="technology-choices"></a>テクノロジの選択

このセクションでは、リモート監視ソリューションの各コア サブシステムに対して行ったテクノロジの選択について詳しく説明します。

![コア ダイアグラム](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>クラウド ゲートウェイ

Azure IoT Hub は、リモート監視ソリューションのクラウド ゲートウェイとして使用されます。 [IoT Hub](https://azure.microsoft.com/services/iot-hub/) は、デバイスとの安全な双方向通信を提供します。

IoT デバイスの接続性のために、以下を使用できます。

- デバイスのネイティブ クライアント アプリケーションを実装するための [IoT Hub デバイス SDK](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)。 この SDK は、IoT Hub REST API のラッパーを提供し、再試行などのシナリオを処理します。
- Azure IoT Edge と統合することで、デバイス上のコンテナーで実行されるカスタム モジュールをデプロイおよび管理します。
- 接続されたデバイスを一括管理するための、IoT Hub での自動デバイス管理との統合。

### <a name="stream-processing"></a>ストリーム処理

ストリーム処理については、リモート監視ソリューションでは複雑なルールの処理に Azure Stream Analytics を使用します。 シンプルなルールを希望する場合は、シンプルなルールの処理をサポートするカスタム マイクロサービスがありますが、そのセットアップは既定のデプロイには含まれていません。 リファレンス アーキテクチャでは、シンプルなルールの処理には Azure Functions、複雑なルールの処理には Azure Stream Analytics をお勧めします。

### <a name="storage"></a>Storage

ストレージに対しては、リモート監視ソリューション アクセラレータは Azure Time Series Insights と Azure Cosmos DB の両方を使用します。 Azure Time Series Insights は、接続されたデバイスから IoT Hub を介して送信されるメッセージを格納します。 このソリューション アクセラレータでは、コールド ストレージ、ルール定義、アラート、構成設定など、その他のすべてのストレージに Azure Cosmos DB を使用します。

Azure Cosmos DB は、IoT アプリケーション向けの推奨される汎用ウォーム ストレージ ソリューションです。 ただし、多くのユース ケースには Azure Time Series Insights や Azure Data Lake などのソリューションが適しています。 Azure Time Series Insights を使用すると、傾向や異常を特定して時系列のセンサー データの詳細な分析情報を得ることができます。 この機能により、根本原因を分析し、コストのかかるダウンタイムを回避できます。

> [!NOTE]
> Azure China クラウドでは、現在、Time Series Insights はご利用になれません。 Azure China クラウドでの新しいリモート監視ソリューション アクセラレータのデプロイでは、すべてのストレージに Cosmos DB を使用します。

### <a name="business-integration"></a>ビジネス統合

リモート監視ソリューションのビジネス統合は、ウォーム ストレージに配置されるアラートの生成に制限されています。 より深いビジネス統合シナリオを実装するには、ソリューションを Azure Logic Apps と接続します。

### <a name="user-interface"></a>ユーザー インターフェイス

Web UI は JavaScript React で構築されています。 React は、業界で広く使用されている Web UI フレームワークを提供し、Angular などの他の一般的なフレームワークに似ています。

### <a name="runtime-and-orchestration"></a>ランタイムとオーケストレーション

リモート監視ソリューションは、Docker コンテナーを使用し、水平スケールのオーケストレーターとして Kubernetes を含むサブシステムを実行します。 このアーキテクチャにより、各サブシステムの個別のスケール定義が可能になります。 ただし、このアーキテクチャの場合、仮想マシンとコンテナーを最新かつセキュリティで保護された状態に保つために DevOps のコストが発生します。

Docker の代替としては、Azure App Service などの PaaS サービスでマイクロサービスをホストする方法があります。 Kubernetes の代替としては、Service Fabric、DC/OS、Swarm などのオーケストレーターがあります。

## <a name="next-steps"></a>次の手順

* [ここに](https://www.azureiotsolutions.com/)リモート監視ソリューション アクセラレータをデプロイする
* GitHub のコードについては、こちら ([C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) および [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/)) をご覧ください。  
* IoT リファレンス アーキテクチャの詳細については、[こちら](https://aka.ms/iotrefarchitecture)をご覧ください。
