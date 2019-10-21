---
title: Azure Spring Cloud についてよく寄せられる質問 | Microsoft Docs
description: Azure Spring Cloud に関する FAQ を確認します
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 09970468a277dcaf3f28b4f5065572568089a12e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038491"
---
# <a name="frequently-asked-questions"></a>よく寄せられる質問

この記事では、Azure Spring Cloud についてよく寄せられる質問にお答えします。 

## <a name="general"></a>全般

### <a name="why-azure-spring-cloud"></a>Azure Spring Cloud が必要なのはなぜですか?

Azure Spring Cloud は、Spring 開発者のためのサービスとしてのプラットフォーム (PaaS) です。 Azure Spring Cloud がアプリケーションのインフラストラクチャを管理してくれるので、開発者がアプリケーションのコードとビジネス ロジックに専念できるようになります。 Azure Spring Cloud に組み込まれているコア機能には、Eureka、構成サーバー、サービス レジストリ サーバー、Pivotal ビルドサービス、ブルーグリーン デプロイなどがあります。 また、このサービスでは、開発者がアプリケーションを CosmosDB、MySQL、Azure Cache for Redis などの Azure サービスにバインドすることもできます。

Azure Spring Cloud は、Azure Monitor、Application Insights、Log Analytics を統合することによって、アプリケーション診断に関する開発者やオペレーターのエクスペリエンスを強化しています。

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure Spring Cloud にはどのようなサービス プランがあるのでしょうか?

Azure Spring Cloud は、プレビュー期間中は無料でご利用いただけます。

リソース | 金額
------- | -------
vCPU | 4
メモリ | 8 G バイト
Spring アプリケーションあたりのアプリ インスタンスの数 | 20
Azure Spring Cloud サービス インスタンスあたりのアプリ インスタンスの合計数 | 50*
サブスクリプション 1 件、1 リージョンあたりの Azure Spring Cloud サービス インスタンスの数 | 2*
永続ボリューム | 10 x 50 G バイト

*_制限を引き上げるための[サポート チケット](https://azure.microsoft.com/support/faq/)を開いてください。_

詳細については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud のセキュリティはどの程度でしょうか?

セキュリティとプライバシーは、Azure と Azure Spring Cloud のどちらのお客様にとっても特に優先度の高い事項の 1 つです。 Azure では、お客様だけがアプリケーションのデータ、ログ、構成にアクセスできるように、そのようなデータのすべてを安全に暗号化しています。 Azure Spring Cloud のサービス インスタンスはすべて、相互に分離されています。

Azure Spring Cloud には、SSL と証明書の包括的な管理機能が用意されています。

OpenJDK と Spring Cloud のランタイムに重要なセキュリティ パッチがあるときは、可能な限り早期に Azure Spring Cloud に適用されます。

### <a name="which-regions-azure-spring-cloud-are-available"></a>Azure Spring Cloud を利用できるのはどのリージョンですか?

米国東部、米国西部 2、西ヨーロッパ、東南アジアです。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure Spring Cloud の既知の制約事項は何ですか?

プレビュー期間中の Azure Spring Cloud の既知の制約事項は次のとおりです。

* `spring.application.name` が、各アプリケーションの作成時に使用されるアプリケーション名によって上書きされます。
* Git リポジトリから入手する構成ファイルで、`server.port` が許可されていません。 これを構成ファイルに追加すると、他のアプリケーションやインターネットからアプリケーションにアクセスできなくなる可能性があります。
* Azure portal と Resource Manager テンプレートで、アプリケーション パッケージのアップロードがサポートされていません。 これを行うには、Azure CLI を使ってアプリケーションをデプロイする方法を使用する必要があります。
* クォータに関する制約は、「[Azure Spring Cloud にはどのようなサービス プランがあるのでしょうか?](#what-service-plans-does-azure-spring-cloud-offer)」を参照してください。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>フィードバックの提供や問題の報告はどのようにするのでしょうか?

Azure Spring Cloud 内で Spring サービス インスタンスの作成が済んでいる場合には、[Azure サポート リクエスト](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)を作成できます。 Azure Spring Cloud にまだオンボードしていない場合には、[Azure のフィードバック](https://feedback.azure.com/) ページにアクセスすると、機能をリクエストしたり、フィードバックを提供したりすることができます。

## <a name="development"></a>開発

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Spring の開発者ですが、まだ Azure を使ったことがありません。Azure Spring Cloud アプリケーションの開発方法を最も早く学べる方法は何ですか?

[こちらのクイック スタート](spring-cloud-quickstart-launch-app-portal.md)に従うと、Azure Spring Cloud の使い方を最も早く身に付けることができます。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud はどの Java ランタイムをサポートしていますか?

Azure Spring Cloud は、Java 8 および 11 をサポートしています。

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>Spring アプリケーションのログとメトリックはどこで確認できますか?

[アプリの概要] タブと [[Azure Monitor]](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) タブでメトリックを探してください。

Azure Spring Cloud では、Spring アプリケーションのログとメトリックを Azure Storage、EventHub、[Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries) にエクスポートすることができます。 Log Analytics でのテーブル名は `AppPlatformLogsforSpring` です。 これを有効にするには、[診断サービス](diagnostic-services.md)に関するこちらの記事を参照してください。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud は分散トレースをサポートしていますか?

はい。詳細については、[分散トレース](spring-cloud-tutorial-distributed-tracing.md)に関するページを参照してください。

### <a name="what-resource-types-does-service-binding-support"></a>サービス バインディングでサポートされるリソースの種類は何ですか?

現時点でサポートされているサービスは次の 3 種類です: Azure Cosmos DB、Azure Database for MySQL、Azure Cache for Redis。

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>アプリケーション内から永続的ボリュームを表示、追加、移動することはできますか?
はい。

## <a name="deployment"></a>Deployment

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring Cloud はブルーグリーン デプロイをサポートしていますか?
はい。詳細については、[ステージング環境に関するガイド](spring-cloud-howto-staging-environment.md)のページを参照してください。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Kubernetes にアクセスしてアプリケーション コンテナーを操作することはできるのでしょうか?

No.  Azure Spring Cloud は、基盤となるアーキテクチャの管理作業から開発者を解放し、開発者がアプリケーションのコードとビジネス ロジックに専念できるようにするためのものです。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud でソースからコンテナーを構築することはできますか?

はい。詳細については、[ソースからのデプロイ](spring-cloud-launch-from-source.md)に関するページを参照してください。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud はアプリ インスタンス内での自動スケーリングをサポートしていますか?

No.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>既存の Spring マイクロサービスを Azure Spring Cloud に移行する際のベスト プラクティスはどのようなものですか?

既存の Spring マイクロサービスを Azure Spring Cloud に移行する前に、次のことを行ってください。
* アプリケーションの依存関係をすべて解決しておく必要があります。
* Azure Spring Cloud のデプロイとの間で比較できるように、構成エントリ、環境変数、JVM パラメーターを用意しておきます。
* サービス バインディングを使用する場合には、お使いの Azure サービスを確認し、適切なアクセス許可を設定しておきます。
* サービス検出サービス、構成サーバーなど、Azure Spring Cloud が管理するサービスと競合するおそれのある埋め込みサービスがあれば、削除するか、無効にしておくことをお勧めします。
*-* 公式の安定した Pivotal Spring ライブラリを使用することをお勧めします。 非公式、ベータ版、またはフォークされたバージョンの Pivotal Spring ライブラリは、SLA の対象外となります。

移行後は、CPU や RAM のメトリックとネットワーク トラフィックを監視して、アプリケーション インスタンスが適切にスケーリングされていることを確認します。

## <a name="next-steps"></a>次の手順

[このほかにご不明な点がある場合には、トラブルシューティング ガイドをご確認ください](spring-cloud-troubleshoot.md)。