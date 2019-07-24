---
title: CLI を使用したリモート監視ソリューションのデプロイ - Azure | Microsoft Docs
description: この攻略ガイドでは、CLI を使用してリモート監視ソリューション アクセラレータをプロビジョニングする方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: ea96b2b996ea79efacdcda50c6370f25e26e0aa2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58793775"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>CLI を使用してリモート監視ソリューション アクセラレータをデプロイする

この攻略ガイドでは、リモート監視ソリューション アクセラレータをデプロイする方法について説明します。 CLI を使用してソリューションをデプロイします。 azureiotsuite.com で Web ベースの UI を使用してソリューションをデプロイすることもできます。このオプションの詳細については、[リモート監視ソリューション アクセラレータのデプロイ](quickstart-remote-monitoring-deploy.md)に関するクイック スタートのページを参照してください。

## <a name="prerequisites"></a>前提条件

リモート監視ソリューション アクセラレータをデプロイするには、アクティブな Azure サブスクリプションが必要です。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

CLI を実行するには、[Node.js](https://nodejs.org/) がローカル コンピューターにインストールされている必要があります。

## <a name="install-the-cli"></a>CLI をインストールする

CLI をインストールするには、コマンド ライン環境で、次のコマンドを実行します。

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>CLI へのサインイン

ソリューション アクセラレータをデプロイする前に、次のように CLI を使用して Azure サブスクリプションにサインインする必要があります。

```cmd/sh
pcs login
```

画面の指示に従って、サインインを完了します。

## <a name="deployment-options"></a>デプロイ オプション

ソリューション アクセラレータをデプロイするときに、デプロイ プロセスを構成するいくつかのオプションがあります。

| オプション | 値 | 説明 |
| ------ | ------ | ----------- |
| SKU    | `basic`、`standard`、`local` | _基本_ デプロイは、テストおよびデモを目的としており、すべてのマイクロサービスを単一の仮想マシンにデプロイします。 _標準_ デプロイは、実稼働を目的としており、マイクロサービスをいくつかの仮想マシンにデプロイします。 _ローカル_ デプロイは、ローカル コンピューターでマイクロサービスを実行するように Docker コンテナーを構成し、Azure クラウド サービス (ストレージや Cosmos DB など) を使用します。 |
| ランタイム | `dotnet`、`java` | マイクロサービスの言語の実装を選択します。 |

ローカル デプロイの使用方法の詳細については、[リモート監視ソリューションのローカルでの実行](iot-accelerators-remote-monitoring-deploy-local.md)に関するページを参照してください。

## <a name="basic-and-standard-deployments"></a>Basic デプロイと Standard デプロイ

このセクションでは、Basic デプロイと Standard デプロイの主な違いをまとめます。

### <a name="basic"></a>Basic

基本デプロイは、[azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) または CLI を使用して実行できます。

Basic デプロイは、ソリューションを紹介することを目的としています。 コストを削減するために、すべてのマイクロ サービスは単一の仮想マシンにデプロイされます。 このデプロイは、実稼働可能なアーキテクチャを使用しません。

Basic デプロイによって Azure サブスクリプション内に作成されるサービスは次のとおりです。

| Count | Resource                       | Type         | 用途 |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux Virtual Machine](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | マイクロサービスのホスト |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – Standard レベル | デバイス管理との通信 |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | 構成データ、ルール、アラート、その他のコールド ストレージを格納する |  
| 1     | [Azure Storage アカウント](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | VM およびストリーミング チェックポイントのストレージ |
| 1     | [Web アプリケーション](https://azure.microsoft.com/services/app-service/web/)        |                 | フロント エンド Web アプリケーションのホスト |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | ユーザー ID とセキュリティを管理する |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | アセットの場所を表示する |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 ユニット              | リアルタイム分析を有効にする |
| 1     | [Azure デバイス プロビジョニング サービス](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | デバイスの大規模プロビジョニング |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 ユニット              | メッセージ データの格納とテレメトリの詳細分析の有効化 |

### <a name="standard"></a>Standard

標準デプロイは、CLI のみを使用して実行できます。

Standard デプロイは、開発者がカスタマイズして拡張できる運用対応デプロイです。 Standard デプロイ オプションは、スケーリングと拡張性のために構築された運用対応アーキテクチャをカスタマイズする準備ができたときに使用します。 アプリケーションのマイクロサービスは Docker コンテナーとして構築されており、Azure Kubernetes Service を使用してデプロイされています。 Kubernetes オーケストレーターはマイクロ サービスをデプロイ、スケーリング、および管理します。

Standard デプロイによって Azure サブスクリプション内に作成されるサービスは次のとおりです。

| Count | Resource                                     | SKU/サイズ      | 用途 |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| フル マネージドの Kubernetes コンテナー オーケストレーション サービスをぜひご利用ください。既定のエージェントは 3 つです。|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – Standard レベル | デバイス管理、コマンドとコントロール |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | 構成データ、およびルール、アラート、メッセージなどのデバイス テレメトリの格納 |
| 5     | [Azure Storage アカウント](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | VM ストレージ用に 4 つ、およびストリーミング チェックポイント用に 1 つ |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | SSL 経由のアプリケーション ゲートウェイ |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | ユーザー ID とセキュリティを管理する |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | アセットの場所を表示する |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 ユニット              | リアルタイム分析を有効にする |
| 1     | [Azure デバイス プロビジョニング サービス](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | デバイスの大規模プロビジョニング |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 ユニット              | メッセージ データの格納とテレメトリの詳細分析の有効化 |

> [!NOTE]
> これらのサービスの価格情報については、[https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing) をご覧ください。 サブスクリプションの使用量と請求の詳細については、[Azure Portal](https://portal.azure.com/) をご覧ください。

## <a name="deploy-the-solution-accelerator"></a>ソリューション アクセラレータのデプロイ

デプロイ例:

### <a name="example-deploy-net-version"></a>例: .NET バージョンのデプロイ

次の例では、リモート監視ソリューション アクセラレータの基本 .NET バージョンをデプロイする方法を示します。

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>例: Java バージョンのデプロイ

次の例では、リモート監視ソリューション アクセラレータの標準 Java バージョンをデプロイする方法を示します。

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>pcs コマンド オプション

ソリューションをデプロイするため `pcs` コマンドを実行すると、以下が求められます。

- ソリューションの名前。 この名前は一意である必要があります。
- 使用する Azure サブスクリプション。
- 場所。
- マイクロサービスをホストする仮想マシンの資格情報です。 これらの資格情報を使用して、トラブルシューティングのために仮想マシンにアクセスすることができます。

`pcs` コマンドが終了したら、新しいソリューション アクセラレータの URL が表示されます。 `pcs` コマンドでは、作成した IoT Hub の名前などの情報を含むファイル `{deployment-name}-output.json` も作成されます。

コマンド ライン パラメーターの詳細については、次を実行します。

```cmd/sh
pcs -h
```

CLI の詳細については、[CLI の使用方法](https://github.com/Azure/pcs-cli/blob/master/README.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

この攻略ガイドで学習した内容は次のとおりです。

> [!div class="checklist"]
> * ソリューション アクセラレータの構成
> * ソリューション アクセラレータのデプロイ
> * ソリューション アクセラレータへのサインイン

これで、リモート監視ソリューションのデプロイが完了しました。次の手順は[ソリューション ダッシュボードの機能を確認](./quickstart-remote-monitoring-deploy.md)することです。

<!-- Next how-to guides in the sequence -->
