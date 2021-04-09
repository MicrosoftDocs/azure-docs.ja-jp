---
title: Azure Spring Cloud で分散トレースを使用する
description: Azure Application Insights を通じて Spring Cloud の分散トレースを使用する方法について説明します
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: f55a82eeddc8d4515b0f1333b615244976975097
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877431"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Azure Spring Cloud で分散トレースを使用する

Azure Spring Cloud の分散トレース ツールを使用すると、複雑な問題を簡単にデバッグおよび監視できます。 Azure Spring Cloud は、[Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) と Azure の [Application Insights](../azure-monitor/app/app-insights-overview.md) を統合します。 この統合により、Azure portal から強力な分散トレース機能を利用できます。

::: zone pivot="programming-language-csharp"
この記事では、.NET Core Steeltoe アプリで分散トレースを使用できるようにする方法について説明します。

## <a name="prerequisites"></a>前提条件

これらの手順を実行するには、[Azure Spring Cloud にデプロイする準備が既に整っている](how-to-prepare-app-deployment.md) Steeltoe アプリが必要です。

## <a name="dependencies"></a>依存関係

Steeltoe 2.4.4 の場合は、次の NuGet パッケージを追加します。

* [Steeltoe.Management.TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe.Management.ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

Steeltoe 3.0.0 の場合は、次の NuGet パッケージを追加します。

* [Steeltoe.Management.TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)

## <a name="update-startupcs"></a>Startup.cs を更新する

1. Steeltoe 2.4.4 の場合は、`ConfigureServices` メソッドで `AddDistributedTracing` および `AddZipkinExporter` を呼び出します。

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

   Steeltoe 3.0.0 の場合は、`ConfigureServices` メソッドで `AddDistributedTracing` を呼び出します。

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration, builder => builder.UseZipkinWithTraceOptions(services));
   }
   ```

1. Steeltoe 2.4.4 の場合は、`Configure` メソッドで `UseTracingExporter` を呼び出します。

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
        app.UseTracingExporter();
   }
   ```

   Steeltoe 3.0.0 の場合、`Configure` メソッドを変更する必要はありません。

## <a name="update-configuration"></a>構成を更新する

アプリが Azure Spring Cloud で実行されるときに使用される構成ソースに次の設定を追加します。

1. `management.tracing.alwaysSample` を true に設定します。

2. Eureka サーバー、構成サーバー、ユーザー アプリ間で送信されるトレース スパンを確認する場合: `management.tracing.egressIgnorePattern` を "/api/v2/spans|/v2/apps/. */permissions|/eureka/.* |/oauth/.*" に設定します。

たとえば、*appsettings.json* には次のプロパティが含まれます。
 
```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

.NET Core Steeltoe アプリでの分散トレースの詳細については、Steeltoe ドキュメントの[分散トレース](https://steeltoe.io/docs/3/tracing/distributed-tracing)に関するページを参照してください。
::: zone-end
::: zone pivot="programming-language-java"
この記事では、次の方法について説明します。

> [!div class="checklist"]
> * Azure portal で分散トレースを有効にする。
> * Spring Cloud Sleuth をアプリケーションに追加する。
> * マイクロサービス アプリケーションの依存関係マップを表示する。
> * さまざまなフィルターを使用してトレース データを検索する。

## <a name="prerequisites"></a>前提条件

ここで説明する手順の実行には、既にプロビジョニングされ、運用されている Azure Spring Cloud サービスが必要です。 「[初めての Azure Spring Cloud アプリケーションをデプロイする](spring-cloud-quickstart.md)」クイックスタートを完了して、Azure Spring Cloud サービスをプロビジョニングして実行します。

## <a name="add-dependencies"></a>依存関係を追加する

1. application.properties ファイルに次の行を追加します。

   ```xml
   spring.zipkin.sender.type = web
   ```

   この変更が完了すると、Zipkin センダーは Web に送信できるようになります。

1. [Azure Spring Cloud アプリケーションの準備ガイド](how-to-prepare-app-deployment.md)に従っている場合は、この手順をスキップします。 それ以外の場合は、ローカルの開発環境にアクセスし、次の Spring Cloud Sleuth 依存関係を含むように pom.xml ファイルを編集します。

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
::: zone-end

## <a name="enable-application-insights"></a>Application Insights を有効にする

1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。
1. **[監視]** ページで、 **[分散トレース]** を選択します。
1. **[設定の編集]** を選択して、新しい設定を編集または追加します。
1. 新しい Application Insights クエリを作成するか、既存のものを選択します。
1. 監視するログ カテゴリを選択し、保持期間 (日数) を指定します。
1. **[適用]** を選択し、新しいトレースを適用します。

## <a name="view-the-application-map"></a>アプリケーション マップを表示する

**[分散トレース]** ページに戻り、 **[View application map]\(アプリケーション マップの表示\)** を選択します。 アプリケーションの視覚的表現と監視設定を確認します。 アプリケーション マップの使用方法については、「[アプリケーション マップ: 分散アプリケーションのトリアージ](../azure-monitor/app/app-map.md)」を参照してください。

## <a name="use-search"></a>検索を使用する

他の特定のテレメトリ項目に対するクエリを行うには、検索機能を使用します。 **[分散トレース]** ページで、 **[検索]** を選択します。 検索機能の使用方法の詳細については、「[Application Insights の検索の使用](../azure-monitor/app/diagnostic-search.md)」を参照してください。

## <a name="use-application-insights"></a>Application Insights を使用する

Application Insights では、アプリケーション マップと検索機能の他に、監視機能が提供されます。 Azure portal でアプリケーションの名前を検索し、Application Insights ページを開いて監視情報を確認します。 これらのツールの使用方法の詳細については、「[Azure Monitor ログ クエリ](/azure/data-explorer/kusto/query/)」を参照してください。

## <a name="disable-application-insights"></a>Application Insights を無効にする

1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。
1. **[監視]** で **[分散トレース]** を選択します。
1. **[使用不可]** を選択して Application Insights を無効にします。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Spring Cloud で分散トレースを有効にして理解する方法を学習しました。 サービスをアプリケーションにバインドする方法については、[Azure Cosmos DB データベースを Azure Spring Cloud アプリケーションにバインドする方法](spring-cloud-howto-bind-cosmos.md)に関する記事を参照してください。