---
title: "既にライブの Java Web アプリ向けの Application Insights"
description: "サーバーで既に実行中の Web アプリケーションの監視を開始する"
services: application-insights
documentationcenter: java
author: harelbr
manager: douge
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 42e682eb8e0a740393648e9fe49244c3a02a9867
ms.openlocfilehash: eb6bce9be34467e472fbae6cbf154f3b789b6ddc


---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>既にライブの Java Web アプリ向けの Application Insights


J2EE サーバーで既に実行されている Web アプリケーションがある場合、 [Application Insights](app-insights-overview.md) を使用すると、コードの変更やプロジェクトの再コンパイルなしで監視を開始できます。 この方法では、サーバーに送信される HTTP 要求、未処理の例外、パフォーマンス カウンターに関する情報が取得されます。

使用を開始するには、 [Microsoft Azure](https://azure.com)のサブスクリプションが必要です。

> [!NOTE]
> このページの手順では、実行時の Web アプリに SDK を追加します。 このライタイム インストルメンテーションは、ソース コードの更新やリビルドを実行したくない場合に便利です。 ただし、できれば、それよりも [SDK をソース コードに追加](app-insights-java-get-started.md) することをお勧めします。 追加すると、ユーザーの利用状況を追跡するためのコードの記述などで選択の幅が広がります。
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1.Application Insights のインストルメンテーション キーを取得する
1. [Microsoft Azure ポータル](https://portal.azure.com)
2. 新しい Application Insights リソースを作成し、アプリケーション タイプを Java Web アプリケーションに設定します。
   
    ![名前を入力し、[Java Web アプリケーション] を選択した後、[作成] をクリックします](./media/app-insights-java-live/02-create.png)

    数秒後にリソースが作成されます。

4. 新しいリソースを開き、インストルメンテーション キーを取得します。 このキーは、後でコード プロジェクトに貼り付けます。
   
    ![新しいリソース概要で、[プロパティ] をクリックし、インストルメンテーション キーをコピーします](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2.SDK のダウンロード
1. [Application Insights SDK for Java](https://aka.ms/aijavasdk)をダウンロードします。 
2. サーバーで、プロジェクトのバイナリの読み込み元のディレクトリに SDK の内容を展開します。 Tomcat を使用している場合、このディレクトリは通常 `webapps/<your_app_name>/WEB-INF/lib` になります。

各サーバー インスタンスと各アプリについてこの手順を繰り返す必要があることに注意してください。

## <a name="3-add-an-application-insights-xml-file"></a>手順 3.Application Insights の xml ファイルを追加する
SDK を追加したフォルダーに ApplicationInsights.xml を作成します。 そのファイルに次の XML を入力します。

インストルメンテーション キーについては、Azure ポータルで入手したキーを使用してください。

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

* インストルメンテーション キーは、テレメトリのすべての項目と共に送信されます。インストルメンテーション キーを受け取った Application Insights は、リソース内にこのキーを表示します。
* HTTP 要求コンポーネントはオプションです。 このコンポーネントは、要求と応答時間に関するテレメトリをポータルに自動的に送信します。
* イベントの関連付けは、HTTP 要求コンポーネントに対する追加の操作です。 この操作では、サーバーで受信した各要求に識別子を割り当てた後、この識別子をテレメトリのすべての項目に "Operation.Id" プロパティとして追加します。 これにより、 [診断検索](app-insights-diagnostic-search.md)でフィルターを設定して、テレメトリを各要求に関連付けることができます。

## <a name="4-add-an-http-filter"></a>4.HTTP フィルターを追加する
プロジェクトの web.xml ファイルを見つけて開きます。アプリケーション フィルターが構成されている web-app ノードの下に次のコード スニペットをマージします。

最も正確な結果を得るためには、他のすべてのフィルターの前にこのフィルターをマップする必要があります。

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## <a name="5-check-firewall-exceptions"></a>5.ファイアウォール例外を確認する
必要に応じて [送信データを送信する例外を設定](app-insights-ip-addresses.md)します。

## <a name="6-restart-your-web-app"></a>6.Web アプリを再起動する
## <a name="7-view-your-telemetry-in-application-insights"></a>7.Application Insights でのテレメトリを表示する
[Microsoft Azure ポータル](https://portal.azure.com)の Application Insights リソースに戻ります。

HTTP 要求に関するテレメトリが概要ブレードに表示されます。 (表示されない場合は、数秒待ってから [最新の情報に更新] をクリックします)。

![サンプル データ](./media/app-insights-java-live/5-results.png)

任意のグラフをクリックして、より詳細なメトリックを表示します。 

![](./media/app-insights-java-live/6-barchart.png)

要求のプロパティを表示すると、その要求に関連付けられているテレメトリ イベント (要求や例外など) が表示されます。

![](./media/app-insights-java-live/7-instance.png)

[メトリックの詳細についてはこちらをご覧ください。](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>次のステップ
* [Web ページにテレメトリを追加](app-insights-web-track-usage.md) して、ページ ビューやユーザー メトリックを監視します。
* [Web テストを設定](app-insights-monitor-web-app-availability.md) して、アプリケーションが動作していて応答できることを確認します。
* [ログ トレースをキャプチャする](app-insights-java-trace-logs.md)
* [イベントおよびログを検索](app-insights-diagnostic-search.md) します。




<!--HONumber=Dec16_HO3-->


