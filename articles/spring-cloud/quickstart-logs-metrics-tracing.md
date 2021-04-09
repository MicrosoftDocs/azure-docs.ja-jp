---
title: クイックスタート - ログ、メトリック、およびトレースを使用した Azure Spring Cloud アプリの監視
description: ログ ストリーミング、ログ分析、メトリック、およびトレースを使用して、Azure Spring Cloud の Piggymetrics サンプル アプリを監視します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 75c50e6342a00ef37215d9e961503dd7ffb0d205
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877584"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>クイック スタート:ログ、メトリック、およびトレースを使用した Azure Spring Cloud アプリの監視

::: zone pivot="programming-language-csharp"
Azure Spring Cloud の組み込み監視機能を使用すると、複雑な問題をデバッグおよび監視できます。 Azure Spring Cloud は、Steeltoe [分散トレース](https://steeltoe.io/docs/3/tracing/distributed-tracing)と Azure の [Application Insights](../azure-monitor/app/app-insights-overview.md) を統合します。 この統合により、Azure portal から強力なログ、メトリック、および分散トレース機能を利用できます。

次の手順では、前のクイックスタートでデプロイしたサンプル アプリで、ログ ストリーミング、Log Analytics、メトリック、分散トレースを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

* このシリーズの先行する次のクイックスタートを完了しておきます。

  * 「[Azure Spring Cloud サービスのプロビジョニング](spring-cloud-quickstart-provision-service-instance.md)」。
  * [Azure Spring Cloud の構成サーバーを設定する](spring-cloud-quickstart-setup-config-server.md)
  * [アプリをビルドおよびデプロイする](spring-cloud-quickstart-deploy-apps.md)。

## <a name="logs"></a>ログ

Azure Spring Cloud でログを表示するには、次の 2 つの方法があります。アプリ インスタンスごとのリアルタイム ログの **ログ ストリーミング**、または高度なクエリ機能を使用して集計されたログの **ログ分析**。

### <a name="log-streaming"></a>ログ ストリーミング

ログ ストリーミングを使用するには、Azure CLI で次のコマンドを実行します。

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

次の例のような出力が表示されます。

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> さらに多くのパラメーターとログ ストリーム機能を調べるには、`az spring-cloud app logs -h` を使用します。

### <a name="log-analytics"></a>Log Analytics

1. Azure portal で **[service | Overview]\(サービス | 概要\)** ページに移動し、 **[Monitoring]\(監視\)** セクションの **[Logs]\(ログ\)** を選択します。 Azure Spring Cloud のいずれかのサンプル クエリで **[Run]\(実行\)** を選択します。

   [ ![Logs Analytics のエントリ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. クエリを編集し、表示を警告とエラーのログに制限する Where 句を削除します。

1. その後、[`Run`] を選択すると、ログが表示されます。 クエリの記述の詳細なガイダンスについては、[Azure Log Analytics のドキュメント](../azure-monitor/logs/get-started-queries.md)を参照してください。

   [ ![Log Analytics クエリ - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

## <a name="metrics"></a>メトリック

1. Azure portal で **[service | Overview]\(サービス | 概要\)** ページに移動し、 **[Monitoring]\(監視\)** セクションの **[Metrics]\(メトリック\)** を選択します。 最初のメトリックを追加するには、 **[メトリック]** ドロップダウンで **[Performance (.NET)]\(パフォーマンス (.NET)\)** または **[Request (.NET)]\(要求 (.NET)\)** の下の .NET メトリックのいずれかを選択します。また、そのメトリックのタイムラインを表示するには、 **[集計]** で `Avg` を選択します。

   [ ![メトリック エントリ - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. ツール バーで **[Add filter]\(フィルターの追加\)** をクリックし、`App=solar-system-weather` を選択して、**solar-system-weather** アプリのみの CPU 使用率を表示します。

   [ ![メトリックでフィルターを使用する - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. 前の手順で作成したフィルターを破棄し、 **[Apply Splitting]\(分割の適用\)** を選択します。 **[値]** で `App` を選択して、さまざまなアプリごとの CPU 使用率を表示します。

   [ ![メトリックで分割を適用する - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>分散トレース

1. Azure portal で **[service | Overview]\(サービス | 概要\)** ページに移動し、 **[Monitoring]\(監視\)** セクションの **[Distributed tracing]\(分散トレース\)** を選択します。 次に、右側の **[View application map]\(アプリケーション マップの表示\)** タブを選択します。

   [ ![分散トレースのエントリ - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. これで、アプリ間の呼び出しの状態を確認できるようになりました。 

   [ ![分散トレースの概要 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. **solar-system-weather** と **planet-weather-provider** の間のリンクを選択すると、HTTP メソッドごとの最低速の呼び出しなどの詳細が表示されます。

   [ ![分散トレース - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. 最後に、 **[パフォーマンスの調査]** を選択して、より強力な組み込みのパフォーマンス分析を調べます。

   [ ![分散トレースのパフォーマンス - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud の組み込み監視機能を使用すると、複雑な問題をデバッグおよび監視できます。 Azure Spring Cloud は、[Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) と Azure の [Application Insights](../azure-monitor/app/app-insights-overview.md) を統合します。 この統合により、Azure portal から強力なログ、メトリック、および分散トレース機能を利用できます。 次の手順では、デプロイされた PiggyMetrics アプリでログ ストリーミング、ログ分析、メトリック、および分散トレースを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

以下の先行する手順を完了してください。 

* [Azure Spring Cloud のインスタンスをプロビジョニングする](spring-cloud-quickstart-provision-service-instance.md)
* [構成サーバーを設定する](spring-cloud-quickstart-setup-config-server.md)
* [アプリをビルドおよびデプロイする](spring-cloud-quickstart-deploy-apps.md)。

## <a name="logs"></a>ログ

Azure Spring Cloud でログを表示するには、次の 2 つの方法があります。アプリ インスタンスごとのリアルタイム ログの **ログ ストリーミング**、または高度なクエリ機能を使用して集計されたログの **ログ分析**。

### <a name="log-streaming"></a>ログ ストリーミング

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

ログ ストリーミングを使用するには、Azure CLI で次のコマンドを実行します。

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

次のようなログが表示されます。

[ ![Azure CLI からのログ ストリーミング](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> さらに多くのパラメーターとログ ストリーム機能を調べるには、`az spring-cloud app logs -h` を使用します。

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Azure Toolkit for IntelliJ を使用してログを取得するには:

1. **Azure Explorer** を選択し、 **[Spring Cloud]** を選択します。

1. 実行中のアプリを右クリックします。

1. ドロップダウン リストから **[Streaming Logs]\(ストリーミング ログ\)** を選択します。

   ![ストリーミング ログの選択](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. **[Instance]\(インスタンス\)** を選択します。

   ![インスタンスの選択](media/spring-cloud-intellij-howto/select-instance.png)
    
1. ストリーミング ログが出力ウィンドウに表示されます。

   ![ストリーミング ログの出力](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. **[service | Overview]\(サービス | 概要\)** ページに移動し、 **[Monitoring]\(監視\)** セクションの **[Logs]\(ログ\)** を選択します。 Azure Spring Cloud のいずれかのサンプル クエリで **[Run]\(実行\)** をクリックします。 

   [ ![Logs Analytics のエントリ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. そうすると、フィルター処理されたログが表示されます。 クエリの記述の詳細なガイダンスについては、[Azure Log Analytics のドキュメント](../azure-monitor/logs/get-started-queries.md)を参照してください。

   [ ![Logs Analytics のクエリ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>メトリック

1. **[service | Overview]\(サービス | 概要\)** ページに移動し、 **[Monitoring]\(監視\)** セクションの **[Metrics]\(メトリック\)** を選択します。 **[Metric]\(メトリック\)** で `system.cpu.usage` を選択し、 **[Aggregation]\(集計\)** で `Avg` を選択して、最初のメトリックを追加し、全体的な CPU 使用率のタイムラインを表示します。

   [ ![メトリックのエントリ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. 上のツール バーの **[Add filter]\(フィルターの追加\)** をクリックし、`App=Gateway` を選択して、**ゲートウェイ** アプリのみの CPU 使用率を表示します。

   [ ![メトリックでフィルターを使用する](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. 上で作成したフィルターを破棄し、 **[Apply Splitting]\(分割の適用\)** をクリックします。 **[値]** で `App` を選択して、さまざまなアプリごとの CPU 使用率を表示します。

   [ ![メトリックで分割を適用する](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>分散トレース

1. **[service | Overview]\(サービス | 概要\)** ページに移動し、 **[Monitoring]\(監視\)** セクションの **[Distributed tracing]\(分散トレース\)** を選択します。 次に、右側の **[View application map]\(アプリケーション マップの表示\)** タブをクリックします。

   [ ![分散トレースのエントリ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. これで、Piggymetrics アプリ間の呼び出しの状態を確認できるようになりました。 

   [ ![分散トレースの概要](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. **gateway** と **account-service** 間のリンクをクリックすると、HTTP メソッドによる最低速の呼び出しなどの詳細が表示されます。

   [ ![分散トレース](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. 最後に、 **[パフォーマンスの調査]** をクリックして、より強力な組み込みのパフォーマンス分析を調べます。

   [ ![分散トレースのパフォーマンス](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

これらのクイックスタートでは、サブスクリプションに残っていると課金が継続される Azure リソースを作成しました。 今後これらのリソースが必要になることが予想されない場合は、ポータルを使用するか、Cloud Shell で次のコマンドを実行して、リソース グループを削除します。

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

前のクイックスタートでは、既定のリソース グループ名も設定しました。 次のクイックスタートに進まない場合は、次の CLI コマンドを実行して、既定値をクリアします。

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>次の手順

Azure Spring Cloud の監視機能の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [診断サービス](diagnostic-services.md)
>
> [分散トレース](spring-cloud-howto-distributed-tracing.md)
>
> [リアルタイムでログをストリームする](spring-cloud-howto-log-streaming.md)
