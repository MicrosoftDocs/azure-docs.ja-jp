---
title: Azure Spring Cloud の概要
description: Azure で Java Spring アプリケーションをデプロイして管理するための、Azure Spring Cloud の機能とメリットついて説明します。
author: jpconnock
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: jeconnoc
ms.openlocfilehash: 87bcea5e9ada4f095896c1ebe4cbe1928f158eb0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895427"
---
# <a name="what-is-azure-spring-cloud"></a>Azure Spring Cloud とは

Azure Spring Cloud では、Spring Boot ベースのマイクロサービス アプリケーションを、コードの変更なしで簡単に Azure にデプロイできます。  Spring Cloud アプリケーションのライフサイクルは Azure Spring Cloud によって管理されるので、開発者は自分のコードに専念することができます。  Spring Cloud は、包括的な監視と診断、構成管理、サービス検出、CI/CD 統合、Blue-Green デプロイなどを使用して、ライフサイクル管理を提供します。

Azure エコシステムの一部として、Azure Spring Cloud は、ストレージ、データベース、監視などの他の Azure サービスに簡単にバインドできます。

Azure Spring Cloud は現時点ではパブリック プレビューとして提供されています。 パブリック プレビュー オファリングにより、お客様は公式リリースの前に新機能を試すことができます。  パブリック プレビューの機能とサービスは、運用環境での使用を目的としたものではありません。  プレビュー期間中のサポートの詳細については、[FAQ](https://azure.microsoft.com/support/faq/) のページを確認するか、詳細について[サポート リクエスト](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)を提出してください。

作業を開始するには、[Azure CLI](spring-cloud-quickstart-launch-app-cli.md)、[Azure portal](spring-cloud-quickstart-launch-app-portal.md)、または [Maven](spring-cloud-quickstart-launch-app-maven.md) を使用して、Spring Cloud クイックスタートを完了してください。

その他のサンプルを GitHub で入手できます ([Azure Spring Cloud のサンプル](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql))。

## <a name="application-configuration"></a>[Application Configuration (アプリケーションの構成)]

### <a name="spring-cloud-config-server"></a>Spring Cloud Config Server

Azure Spring Cloud Config Server は、サーバー側とクライアント側の両方のサポートを備えた分散システムで、外部化された構成を提供します。  Config Server は、すべての環境にわたってアプリケーションのプロパティを管理するための一元的な場所を提供します。  詳細については、[Spring Cloud Config Server のリファレンス](https://spring.io/projects/spring-cloud-config.md)を参照し、チュートリアルを完了してください。

### <a name="enable-bluegreen-deployments"></a>Blue-Green デプロイの有効化

Azure Spring Cloud では、運用環境に対してコードのリリースと更新を行うための Blue-Green デプロイをサポートしています。  この変更管理パターンを利用すると、開発者は必要になった場合に即時にフォールバックを実行できるセキュリティ状態で、機能とコードの変更を実装できます。  Azure により、複数の運用環境が管理され、アプリケーションを中断することなくコードの変更を簡単に更新またはロールバックできるようになるため、開発者はコードの記述に専念できます。  ステージング環境と Blue-Green デプロイの詳細については、この[ハウツー記事](spring-cloud-howto-staging-environment.md)を参照してください。

### <a name="automate-cicd-pipelines"></a>CI/CD パイプラインの自動化

Azure Spring Cloud では、Azure CLI を使用した Azure DevOps との統合が提供されます。  Azure DevOps を使用すると、コードの統合と Spring アプリケーションへのデプロイを自動化できます。  詳細については、この[記事](spring-cloud-howto-cicd.md)を参照してください。

### <a name="scale-your-application"></a>アプリケーションのスケーリング

Azure Spring Cloud では、Azure Spring Cloud ダッシュボードで、マイクロサービスを簡単にスケーリングできます。  要件に合わせて、マイクロサービスで使用可能な vCPU の数とメモリの量の両方をスケールアップまたはスケールダウンできます。  スケーリングは数秒で有効になり、コードの変更や再デプロイは不要です。  詳細を学習するには、この[チュートリアル](spring-cloud-tutorial-scale-manual.md)を完了してください。

## <a name="application-monitoring"></a>アプリケーションの監視

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>分散トレースと Azure App Insights を使用してアプリケーションを監視する

Spring Cloud の分散トレース ツールを使用すると、開発者はアプリケーション内のマイクロサービス間の複雑な相互接続をデバッグおよび監視できます。  [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) と Azure の [Application Insights](../azure-monitor/insights/insights-overview.md) が統合され、Azure portal から強力な分散トレース機能を利用できます。  詳細を学習するには、この[チュートリアル](spring-cloud-tutorial-distributed-tracing.md)を完了してください。

## <a name="next-steps"></a>次の手順

- [CLI から Spring Cloud アプリを起動する](spring-cloud-quickstart-launch-app-cli.md)
