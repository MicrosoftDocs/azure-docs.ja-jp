---
title: Azure Spring Cloud で分散トレースを使用する
description: Azure Application Insights を通じて Spring Cloud の分散トレースを使用する方法について説明します
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 1ff76c38031ac367bf81f6d152642a4d9a209bb7
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89294001"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Azure Spring Cloud で分散トレースを使用する

Azure Spring Cloud の分散トレース ツールを使用すると、複雑な問題を簡単にデバッグおよび監視できます。 Azure Spring Cloud は、[Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) と Azure の [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) を統合します。 この統合により、Azure portal から強力な分散トレース機能を利用できます。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * Azure portal で分散トレースを有効にする。
> * Spring Cloud Sleuth をアプリケーションに追加する。
> * マイクロサービス アプリケーションの依存関係マップを表示する。
> * さまざまなフィルターを使用してトレース データを検索する。

## <a name="prerequisites"></a>前提条件

ここで説明する手順の実行には、既にプロビジョニングされ、運用されている Azure Spring Cloud サービスが必要です。 Azure Spring Cloud サービスをプロビジョニングし、実行するには、[Azure CLI を使用したアプリのデプロイのクイックスタート](spring-cloud-quickstart.md)を完了してください。
    
## <a name="add-dependencies"></a>依存関係を追加する

1. application.properties ファイルに次の行を追加します。

   ```xml
   spring.zipkin.sender.type = web
   ```

   この変更が完了すると、Zipkin センダーは Web に送信できるようになります。

1. [Azure Spring Cloud アプリケーションの準備ガイド](spring-cloud-tutorial-prepare-app-deployment.md)に従っている場合は、この手順をスキップします。 それ以外の場合は、ローカルの開発環境にアクセスし、次の Spring Cloud Sleuth 依存関係を含むように pom.xml ファイルを編集します。

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

1. これらの変更を反映するために、Azure Spring Cloud サービスをもう一度ビルドしてデプロイします。

## <a name="modify-the-sample-rate"></a>サンプル レートを変更する

サンプル レートを変更すると、テレメトリが収集される頻度を変更できます。 たとえば、半分の頻度でサンプリングする場合は、application.properties ファイルを開き、次の行を変更します。

```xml
spring.sleuth.sampler.probability=0.5
```

アプリケーションの構築とデプロイが既に完了している場合は、サンプル レートを変更できます。 これを行うには、Azure CLI または Azure portal で前の行を環境変数として追加します。

## <a name="enable-application-insights"></a>Application Insights を有効にする

1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。
1. **[監視]** ページで、 **[分散トレース]** を選択します。
1. **[設定の編集]** を選択して、新しい設定を編集または追加します。
1. 新しい Application Insights クエリを作成するか、既存のものを選択します。
1. 監視するログ カテゴリを選択し、保持期間 (日数) を指定します。
1. **[適用]** を選択し、新しいトレースを適用します。

## <a name="view-the-application-map"></a>アプリケーション マップを表示する

**[分散トレース]** ページに戻り、 **[View application map]\(アプリケーション マップの表示\)** を選択します。 アプリケーションの視覚的表現と監視設定を確認します。 アプリケーション マップの使用方法については、「[アプリケーション マップ: 分散アプリケーションのトリアージ](https://docs.microsoft.com/azure/azure-monitor/app/app-map)」を参照してください。

## <a name="use-search"></a>検索を使用する

他の特定のテレメトリ項目に対するクエリを行うには、検索機能を使用します。 **[分散トレース]** ページで、 **[検索]** を選択します。 検索機能の使用方法の詳細については、「[Application Insights の検索の使用](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)」を参照してください。

## <a name="use-application-insights"></a>Application Insights を使用する

Application Insights では、アプリケーション マップと検索機能の他に、監視機能が提供されます。 Azure portal でアプリケーションの名前を検索し、Application Insights ページを開いて監視情報を確認します。 これらのツールの使用方法の詳細については、「[Azure Monitor ログ クエリ](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)」を参照してください。

## <a name="disable-application-insights"></a>Application Insights を無効にする

1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。
1. **[監視]** で **[分散トレース]** を選択します。
1. **[使用不可]** を選択して Application Insights を無効にします。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Spring Cloud で分散トレースを有効にして理解する方法を学習しました。 サービスをアプリケーションにバインドする方法については、[Azure Cosmos DB データベースを Azure Spring Cloud アプリケーションにバインドする方法](spring-cloud-tutorial-bind-cosmos.md)に関する記事を参照してください。
