---
title: Azure Spring Cloud についてよく寄せられる質問 | Microsoft Docs
description: この記事では、Azure Spring Cloud についてよく寄せられる質問に回答します。
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 1cf29438d3785a3406aa8ce3b75929a5d5261121
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800372"
---
# <a name="azure-spring-cloud-faq"></a>Azure Spring Cloud に関する FAQ

この記事では、Azure Spring Cloud についてよく寄せられる質問に回答します。

## <a name="general"></a>全般

### <a name="why-azure-spring-cloud"></a>Azure Spring Cloud が必要なのはなぜですか?

Azure Spring Cloud は、Spring Cloud 開発者のためのサービスとしてのプラットフォーム (PaaS) を提供します。 Azure Spring Cloud がアプリケーション インフラストラクチャを管理するため、開発者はアプリケーション コードやビジネス ロジックに焦点を絞ることができます。 Azure Spring Cloud に組み込まれているコア機能には、Eureka、構成サーバー、サービス レジストリ サーバー、Pivotal Build Service、ブルーグリーン デプロイなどが含まれます。 このサービスにより、開発者はアプリケーションを Azure Cosmos DB、Azure Database for MySQL、Azure Cache for Redis などの他の Azure サービスにバインドすることもできます。

Azure Spring Cloud は、Azure Monitor、Application Insights、および Log Analytics を統合することによって、開発者やオペレーターのアプリケーション診断エクスペリエンスを拡張します。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud のセキュリティはどの程度でしょうか?

セキュリティとプライバシーは、Azure や Azure Spring Cloud のお客様にとっての最優先事項です。 Azure では、アプリケーション データ、ログ、または構成を確実に暗号化することにより、お客様だけがこれらのすべてのデータにアクセスできるようにしています。 

* Azure Spring Cloud のサービス インスタンスは、相互に分離されています。
* Azure Spring Cloud には、TLS/SSL と証明書の包括的な管理機能が用意されています。
* OpenJDK と Spring Cloud のランタイムに重要なセキュリティ パッチがあるときは、可能な限り早期に Azure Spring Cloud に適用されます。

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure Spring Cloud はどのリージョンで使用できますか?

米国東部、米国西部 2、西ヨーロッパ、東南アジアです。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure Spring Cloud の既知の制限事項はどのようなものですか?

プレビュー リリース中、Azure Spring Cloud には次の既知の制限事項があります。

* `spring.application.name` が、各アプリケーションを作成するために使用されるアプリケーション名によって上書きされます。
* `server.port` の既定値は、ポート 80/443 です。 他の値が適用されている場合は、80/443 に上書きされます。
* Azure portal と Azure Resource Manager テンプレートがアプリケーション パッケージのアップロードをサポートしていません。 Azure CLI 経由でアプリケーションをデプロイすることによってのみ、アプリケーション パッケージをアップロードできます。

### <a name="what-pricing-tiers-are-available"></a>利用可能な価格レベルを教えてください。 
どれを使用すればよいでしょうか? また、各レベルにはどのような制限がありますか?
* Azure Spring Cloud には、2 つの価格レベルがあります。Basic と Standard です。 Basic レベルは、Dev/Test、および Azure Spring Cloud の試用を目的としています。 Standard レベルは、汎用の運用トラフィックを実行するために最適化されています。 制限と機能レベルの比較については、[Azure Spring Cloud の価格の詳細](https://azure.microsoft.com/pricing/details/spring-cloud/)に関する記事を参照してください。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>フィードバックの提供や問題の報告はどのようにするのでしょうか?

Azure Spring Cloud で問題が発生した場合は、[Azure サポート要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)を作成してください。 機能要求を送信するか、またはフィードバックを提供するには、[Azure フィードバック](https://feedback.azure.com/forums/34192--general-feedback)に移動してください。

## <a name="development"></a>開発

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>私は Spring Cloud 開発者ですが、Azure は初めてです。 Azure Spring Cloud アプリケーションの開発方法を学習するための最もすばやい方法は何ですか?

Azure Spring Cloud の使用を開始するための最もすばやい方法として、「[クイック スタート: Azure portal を使用して Azure Spring Cloud アプリケーションを起動する](spring-cloud-quickstart-launch-app-portal.md)」の手順に従ってください。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud はどの Java ランタイムをサポートしていますか?

Azure Spring Cloud は、Java 8 および 11 をサポートしています。 「[Java ランタイムと OS バージョン](#java-runtime-and-os-versions)」を参照してください。

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Spring Cloud アプリケーションのログとメトリックはどこで表示できますか?

[アプリの概要] タブと [[Azure Monitor]](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) タブでメトリックを探してください。

Azure Spring Cloud は、Spring Cloud アプリケーションのログとメトリックの Azure Storage、EventHub、および [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries) へのエクスポートをサポートしています。 Log Analytics でのテーブル名は *AppPlatformLogsforSpring* です。 これを有効にする方法については、[診断サービス](diagnostic-services.md)に関するページを参照してください。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud は分散トレースをサポートしていますか?

はい。 詳細については、[Azure Spring Cloud で分散トレースを使用する](spring-cloud-tutorial-distributed-tracing.md)」を参照してください。

### <a name="what-resource-types-does-service-binding-support"></a>サービス バインディングでサポートされるリソースの種類は何ですか?

現時点でサポートされているサービスは次の 3 種類です:
* Azure Cosmos DB
* Azure Database for MySQL
* Azure Cache for Redis

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>アプリケーション内から永続ボリュームを表示、追加、または移動できますか?

はい。

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Azure Spring Cloud サービス インスタンスを削除または移動すると、その拡張機能リソースも削除または移動されますか?

拡張リソースが所有するリソース プロバイダーのロジックによって異なります。 `Microsoft.AppPlatform` インスタンスの拡張リソースは同じ名前空間に属していないため、動作はリソース プロバイダーによって異なります。 たとえば、削除または移動操作は、**診断設定**リソースにカスケードされません。 新しい Azure Spring Cloud インスタンスが、削除されたものと同じリソース ID でプロビジョニングされる場合、または以前の Azure Spring Cloud インスタンスが戻った場合は、以前の**診断設定**リソースが引き続きそれを拡張します。

## <a name="java-runtime-and-os-versions"></a>Java ランタイムと OS バージョン

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Azure Spring Cloud では、どのバージョンの Java ランタイムがサポートされていますか?

Azure Spring Cloud では、2020 年 6 月現在での最新ビルドである Java 8 ビルド 252 と Java 11 ビルド 7 が含まれる Java LTS バージョンがサポートされています。 「[Azure 用の JDK および Azure Stack をインストールする](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)」を参照してください。

### <a name="who-built-these-java-runtimes"></a>これらの Java ランタイムはだれがビルドしたのですか?

Azul Systems です。 Azul Zulu for Azure - Enterprise Edition JDK ビルドは、Microsoft および Azul Systems によってサポートされる、無料でマルチプラットフォーム対応かつ実稼働可能な、Azure と Azure Stack 用の OpenJDK のディストリビューションです。 これらには、Java SE アプリケーションを構築および実行するためのすべてのコンポーネントが含まれています。

### <a name="how-often-will-java-runtimes-get-updated"></a>Java ランタイムはどのくらいの頻度で更新されますか?

LTS と MTS の JDK リリースには、四半期ごとのセキュリティ更新プログラムとバグ修正が含まれ、必要に応じて、重要なアウトオブバンドの更新プログラムと修正プログラムも含まれます。 このサポートには、Java 11 などの新しいバージョンの Java で報告された、セキュリティ更新プログラムとバグ修正の Java 7 および 8 へのバックポートが含まれています。

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Java 8 LTS バージョンと Java 11 LTS バージョンのサポート期間を教えてください。

[Azure および Azure Stack の Java 長期サポート](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)に関する記事を参照してください。

* Java 8 LTS は 2030 年 12 月までサポートされます。
* Java 11 LTS は 2027 年 9 月までサポートされます。

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>サポートされている Java ランタイムをローカル開発用にダウンロードするにはどうすればよいですか?

「[Azure 用の JDK および Azure Stack をインストールする](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)」を参照してください。

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>古い Java ランタイムの削除ポリシーとは何ですか?

古いランタイム バージョンが廃止される 12 か月前に公開通知が送信されます。 新しいバージョンへの移行に 12 か月の猶予があることになります。

* サブスクリプション管理者は、Java バージョンがインベントリから削除される時点で電子メール通知を受け取ります。
* インベントリからの削除に関する情報は、ドキュメントに記載されます。

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>Java ランタイム レベルの問題についてサポートを受けるにはどうすればよいですか?

Azure サポートでサポート チケットを開くことができます。  [Azure サポート リクエストを作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)に関する記事を参照してください。

### <a name="what-is-the-operation-system-to-run-my-apps"></a>アプリを実行するためのオペレーティング システムは何ですか?

最新の Ubuntu LTS バージョンが使用されており、現在は、[Ubuntu 20.04 LTS (Focal Fossa)](https://releases.ubuntu.com/focal/) が既定の OS となっています。

### <a name="how-often-will-os-security-patches-be-applied"></a>OS セキュリティ パッチはどのくらいの頻度で適用されますか?

Azure Spring Cloud に適用されるセキュリティ パッチは、月単位で運用環境に展開されます。
Azure Spring Cloud に適用される重要なセキュリティ パッチ (CVE score 9 以上) は、可能な限り迅速に展開されます。

## <a name="deployment"></a>デプロイ

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring Cloud はブルーグリーン デプロイをサポートしていますか?
はい。 詳細については、[ステージング環境の設定](spring-cloud-howto-staging-environment.md)に関するページを参照してください。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Kubernetes にアクセスしてアプリケーション コンテナーを操作することはできるのでしょうか?

いいえ。  Azure Spring Cloud は、基盤となるアーキテクチャの管理作業から開発者を解放し、開発者がアプリケーションのコードとビジネス ロジックに専念できるようにするためのものです。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud でソースからコンテナーを構築することはできますか?

はい。 詳細については、「[ソース コードから Spring Cloud アプリケーションを起動する](spring-cloud-launch-from-source.md)」を参照してください。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud はアプリ インスタンス内での自動スケーリングをサポートしていますか?

いいえ。

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>既存の Spring Cloud マイクロサービスを Azure Spring Cloud に移行するためのベスト プラクティスはどのようなものですか?

既存の Spring Cloud マイクロサービスを Azure Spring Cloud に移行しようとしている場合は、次のベスト プラクティスを確認することをお勧めします。
* アプリケーションの依存関係をすべて解決しておく必要があります。
* 構成エントリ、環境変数、および JVM パラメーターを準備しておき、それらを Azure Spring Cloud でのデプロイと比較できるようにします。
* サービス バインドを使用する場合は、Azure サービスをチェックし、適切なアクセス許可を設定していることを確認してください。
* Azure Spring Cloud によって管理されるサービスと競合する可能性のある組み込みサービス (サービス検出サービスや構成サーバーなど) をすべて削除するか、または無効にすることをお勧めします。
* 公式の、安定した Pivotal Spring ライブラリを使用することをお勧めします。 非公式版、ベータ版、またはフォーク済みバージョンの Pivotal Spring ライブラリにはサービス レベル アグリーメント (SLA) のサポートがありません。

移行したら、CPU/RAM のメトリックやネットワーク トラフィックを監視して、アプリケーション インスタンスが適切にスケーリングされていることを確認します。

## <a name="trouble-shooting"></a>トラブルシューティング

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>サービス レジストリがまれに利用できなくなると、どのような影響がありますか?

あまり起こることではありませんが、次のようなエラーが発生することがあります。 
```
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```
これはアプリケーションのログに記録されたエラーです。 ネットワークが不安定などの問題により、速度が極端に低下した Spring フレームワークによって発生した問題です。 

Eureka クライアントには、それに対処するハートビートと再試行ポリシーの両方が備わっているため、ユーザー エクスペリエンスに影響はありません。 一時的なエラーと考えられるので、無視してかまいません。

この点については、今後ユーザーのアプリケーションからこのエラーが返されないよう改善する予定です。


## <a name="next-steps"></a>次のステップ

さらに質問がある場合は、[Azure Spring Cloud のトラブルシューティング ガイド](spring-cloud-troubleshoot.md)に関するページを参照してください。
