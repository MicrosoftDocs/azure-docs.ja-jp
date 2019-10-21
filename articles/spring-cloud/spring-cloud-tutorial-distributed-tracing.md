---
title: チュートリアル:Azure Spring Cloud で分散トレースを使用する | Microsoft Docs
description: Azure Application Insights を通じて Spring Cloud の分散トレースを使用する方法について説明します
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: 95aae47bddffd102c5d6d6dac67f22e6777cc3e7
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038231"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>チュートリアル:Azure Spring Cloud で分散トレースを使用する

Spring Cloud の分散トレース ツールを使用すると、複雑な問題を簡単にデバッグおよび監視できます。 Azure Spring Cloud は、[Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) と Azure の [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) を統合して、Azure portal から強力な分散トレース機能を提供します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Azure portal で分散トレースを有効にする
> * Spring Cloud Sleuth をアプリケーションに追加する
> * マイクロサービス アプリケーションの依存関係マップを表示する
> * さまざまなフィルターを使用してトレース データを検索する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* 既にプロビジョニングされ、実行されている Azure Spring Cloud サービス。  Azure Spring Cloud サービスをプロビジョニングし、起動するには、[このクイックスタート](spring-cloud-quickstart-launch-app-cli.md)を完了します。
    
## <a name="add-dependencies"></a>依存関係を追加する

次の行を application.properties ファイルに追加して、zipkin 送信者が Web に送信できるようにします。

```xml
spring.zipkin.sender.type = web
```

[Azure Spring Cloud アプリケーションを準備するためのガイド](spring-cloud-tutorial-prepare-app-deployment.md)に従った場合は、次の手順をスキップできます。 そうでない場合は、ローカルの開発環境に移動して、Spring Cloud Sleuth の依存関係を含むように `pom.xml` ファイルを編集します。

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-sleuth</artifactId>
            <version>${spring-cloud-sleuth.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-sleuth</artifactId>
    </dependency>
</dependencies>
```

* これらの変更を反映するために、Azure Spring Cloud サービスをもう一度ビルドしてデプロイします。 

## <a name="modify-the-sample-rate"></a>サンプル レートを変更する
サンプル レートを変更すると、テレメトリが収集される頻度を変更できます。 たとえば、サンプリングの頻度を半分にする場合は、`application.properties` ファイルに移動して、次の行を変更します。

```xml
spring.sleuth.sampler.probability=0.5
```

アプリケーションを既にビルドおよびデプロイしてある場合は、Azure CLI またはポータルで上の行を環境変数として追加することで、サンプル レートを変更できます。 

## <a name="enable-application-insights"></a>Application Insights を有効にする

1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。
1. [監視] セクションで **[分散トレース]** を選択します。
1. **[設定の編集]** を選択して、新しい設定を編集または追加します。
1. 新しい Application Insights クエリを作成します。既存の Application Insights クエリを選択することもできます。
1. 監視するログ カテゴリを選択し、保持期間 (日数) を指定します。
1. **[適用]** を選択し、新しいトレースを適用します。

## <a name="view-application-map"></a>アプリケーション マップを表示する

[分散トレース] ページに戻り、 **[View application map]\(アプリケーション マップの表示\)** を選択します。 アプリケーションの視覚的表現と監視設定を確認します。 アプリケーション マップを使用する方法については、[この記事](https://docs.microsoft.com/azure/azure-monitor/app/app-map)を参照してください。

## <a name="search"></a>Search

他の特定のテレメトリ項目に対するクエリを行うには、検索機能を使用します。 **[分散トレース]** ページで、 **[検索]** を選択します。 検索機能を使用する方法の詳細については、[この記事](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)を参照してください。

## <a name="application-insights-page"></a>Application Insights ページ

Application Insights では、アプリケーション マップと検索の他に、監視機能が提供されます。 その他の機能を見つけるには、Azure portal でアプリケーション名を検索し、Application Insights ページを起動してください。 これらのツールの使用方法のガイダンスについては、[ドキュメントを参照](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)してください。


## <a name="disable-application-insights"></a>Application Insights を無効にする

1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。
1. [監視] セクションで **[分散トレース]** をクリックします。
1. Application Insights を無効にするには、 **[使用不可]** をクリックします

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Spring Cloud で分散トレースを有効にして理解する方法を学習しました。 アプリケーションを Azure CosmosDB にバインドする方法について学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [アプリケーションを Azure CosmosDB にバインドする方法を学習](spring-cloud-tutorial-bind-cosmos.md)します。
