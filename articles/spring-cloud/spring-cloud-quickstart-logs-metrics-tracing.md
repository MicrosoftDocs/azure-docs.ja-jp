---
title: クイックスタート - ログ、メトリック、およびトレースを使用した Azure Spring Cloud アプリの監視
description: ログ ストリーミング、ログ分析、メトリック、およびトレースを使用して、Azure Spring Cloud の Piggymetrics サンプル アプリを監視します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f9f03c355e1e619d004c8ec8c1cc2f91932db744
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046835"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>クイック スタート:ログ、メトリック、およびトレースを使用した Azure Spring Cloud アプリの監視

Azure Spring Cloud の組み込み監視機能を使用すると、複雑な問題をデバッグおよび監視できます。 Azure Spring Cloud は、[Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) と Azure の [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) を統合します。 この統合により、Azure portal から強力なログ、メトリック、および分散トレース機能を利用できます。 次の手順では、デプロイされた PiggyMetrics アプリでログ ストリーミング、ログ分析、メトリック、および分散トレースを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

以下の先行する手順を完了してください。 

* [Azure Spring Cloud のインスタンスをプロビジョニングする](spring-cloud-quickstart-provision-service-instance.md)
* [構成サーバーを設定する](spring-cloud-quickstart-setup-config-server.md)
* [アプリをビルドおよびデプロイする](spring-cloud-quickstart-deploy-apps.md)。

## <a name="logs"></a>ログ

Azure Spring Cloud でログを表示するには、次の 2 つの方法があります。アプリ インスタンスごとのリアルタイム ログの**ログ ストリーミング**、または高度なクエリ機能を使用して集計されたログの**ログ分析**。

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
    
1. そうすると、フィルター処理されたログが表示されます。 クエリの記述の詳細なガイダンスについては、[Azure Log Analytics のドキュメント](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)を参照してください。

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

## <a name="clean-up-resources"></a>リソースをクリーンアップする

前の手順では、リソース グループ内に Azure リソースを作成しました。 今後これらのリソースが必要になることが予想されない場合は、ポータルからリソース グループを削除するか、Cloud Shell で次のコマンドを実行します。

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

前の手順では、既定のリソース グループ名も設定しました。 この既定の設定を解除するには、Cloud Shell で次のコマンドを実行します。

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>次のステップ

Azure Spring Cloud のすぐに使用できる監視機能の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [診断サービス](diagnostic-services.md)
> [分散トレース](spring-cloud-tutorial-distributed-tracing.md)
> [ログをリアルタイムでストリーミングする](spring-cloud-howto-log-streaming.md)
