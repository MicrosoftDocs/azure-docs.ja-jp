---
title: クイックスタート - サンプル アプリの概要 - Azure Spring Cloud
description: この一連のクイックスタートでは、サンプル アプリを使用して Azure Spring Cloud にデプロイする方法について説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: dd36bb18e84ea299195b77286887a3b279f81469
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877578"
---
# <a name="introduction-to-the-sample-app"></a>サンプル アプリの概要

::: zone pivot="programming-language-csharp"
この一連のクイックスタートでは、2 つのマイクロサービスで構成されるサンプル アプリを使用して、Azure Spring Cloud サービスに .NET Core Steeltoe アプリをデプロイする方法を示します。 サービスの検出、構成サーバー、ログ、メトリック、分散トレースなど、Azure Spring Cloud の機能を使用します。

## <a name="functional-services"></a>機能サービス

サンプル アプリは、次の 2 つのマイクロサービスで構成されています。

* `planet-weather-provider` サービスは、惑星名を指定する HTTP 要求に応答して、気象のテキストを返します。 たとえば、彗星について "非常に暖かい" と返される場合があります。 気象データは、構成サーバーから取得します。 構成サーバーは、次の例のように、Git リポジトリの YAML ファイルから気象データを取得します。

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* `solar-system-weather` サービスは、HTTP 要求に応答して 4 つの惑星のデータを返します。 `planet-weather-provider` に 4 つの HTTP 要求を行って、データを取得します。 `planet-weather-provider` の呼び出しには、Eureka サーバーの検出サービスを使用します。 次のような JSON が返されます。

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

次の図に、このサンプル アプリのアーキテクチャを示します。

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="サンプル アプリケーションの図":::

## <a name="code-repository"></a>コード リポジトリ

このサンプル アプリは、GitHub の Azure-Samples/Azure-Spring-Cloud-Samples リポジトリの [steeltoe-sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) フォルダーにあります。

次のクイックスタートの手順では、必要に応じてソース コードを参照しています。

::: zone-end

::: zone pivot="programming-language-java"
このクイックスタートでは、PiggyMetrics と呼ばれる個人財務サンプルを使用して、Azure Spring Cloud サービスにアプリをデプロイする方法を示します。 PiggyMetrics は、マイクロサービス アーキテクチャ パターンを示し、サービスの内訳を強調します。 サービス検出、構成サーバー、ログ、メトリック、分散トレースなどの強力な Azure Spring Cloud 機能を使用すると、どのように Azure にデプロイされているかがわかります。

Azure Spring Cloud のデプロイの例を使用するには、ソース コードの場所だけが必要です。これは、必要に応じて提供されます。

## <a name="functional-services"></a>機能サービス

PiggyMetrics は、3 つのコア マイクロサービスに分解されます。 これらのすべては、個別にデプロイできるアプリケーションであり、ビジネス ドメイン別に整理されています。

* **アカウント サービス (デプロイされる)** : 一般的なユーザー入力ロジックと検証が含まれます。収入と支出の項目、貯蓄、アカウント設定などです。
* **統計サービス (このクイックスタートでは使用しません)** : 主要な統計パラメーターを計算し、各アカウントの時系列をキャプチャします。 データポイントには、基本通貨と期間に正規化された値が含まれます。 このデータは、アカウントの有効期間中のキャッシュ フロー ダイナミクスを追跡するために使用されます。
* **通知サービス (このクイックスタートでは使用しません)** : ユーザーの連絡先情報と通知設定 (リマインダーやバックアップの頻度など) を格納します。 スケジュールされたワーカーは、他のサービスから必要な情報を収集し、サブスクライブした顧客に電子メール メッセージを送信します。

## <a name="infrastructure-services"></a>インフラストラクチャ サービス

コア サービスの動作を支援するために、分散システムにはいくつかの一般的なパターンがあります。 Azure Spring Cloud には、Spring Boot アプリケーションの動作を拡張してこれらのパターンを実装する、強力なツールが用意されています。 

* **構成サービス (Azure Spring Cloud によってホストされます)** : Azure Spring Cloud Config は、水平方向にスケーラブルな、分散システムのための一元化された構成サービスです。 現時点でローカル ストレージ、Git、および Subversion をサポートしている、プラグ可能なリポジトリを使用します。
* **サービス検出 (Azure Spring Cloud によってホストされます)** : これにより、自動スケーリング、障害、およびアップグレードのために動的にアドレスを割り当てられた可能性があるサービス インスタンスのネットワークの場所を自動的に検出できます。
* **承認サービス (デプロイされます)** : 承認責任は、別のサーバーに完全に抽出されます。そのサーバーによって、バックエンド リソース サービスに OAuth2 トークンが付与されます。 承認サーバーはユーザーの承認を行い、境界内のマシン間の通信をセキュリティで保護します。
* **API ゲートウェイ (デプロイされます)** : 3 つのコア サービスは、外部 API をクライアントに公開します。 実際のシステムでは、システムの複雑さによって関数の数が急速に増加する可能性があります。 1 つの複雑な Web ページのレンダリングには、数百のサービスが関係していることがあります。 API ゲートウェイは、システムへの単一のエントリ ポイントです。要求を処理し、適切なバックエンド サービスにルーティングしたり、複数のバックエンド サービスを呼び出したりするために使用され、結果を集計します。 

## <a name="sample-usage-of-piggymetrics"></a>PiggyMetrics の使用例

実装の詳細については、「[PiggyMetrics](https://github.com/Azure-Samples/piggymetrics)」を参照してください。 このサンプルでは、必要に応じてソース コードを参照します。
::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Spring Cloud インスタンスをプロビジョニングする](spring-cloud-quickstart-provision-service-instance.md)
