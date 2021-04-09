---
title: クイック スタート:Azure Application Insights を使用した Java Web アプリの分析
description: 'Application Insights を使用した Java Web アプリのアプリケーション パフォーマンス監視 '
ms.topic: conceptual
ms.date: 11/22/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: cdad5a0ec158f216a63ae7e1e2abc10d2841b3bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593727"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>クイック スタート:Java Web プロジェクトで Application Insights を使う


> [!CAUTION]
> 2020 年 11 月以降、Java アプリケーションの監視には、Azure Monitor Application Insights Java 3.0 エージェントを使用した自動インストルメンテーションをお勧めします。 開始方法について詳しくは、「[Application Insights Java 3.0 エージェント](./java-in-process-agent.md)」を参照してください。

このクイックスタートでは、Application Insights SDK を使用して、要求のインストルメント化、依存関係の追跡、およびパフォーマンス カウンターの収集を行い、パフォーマンスの問題と例外を診断し、ユーザーによるアプリの操作内容を追跡するコードを作成します。

Application Insights は、ライブ アプリケーションのパフォーマンスと使用状況を把握するのに役立つ、Web 開発者向けの拡張可能な分析サービスです。 Application Insights は、Linux、Unix、Windows で動作する Java アプリをサポートします。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* 機能している Java アプリケーション。

## <a name="get-an-application-insights-instrumentation-key"></a>Application Insights のインストルメンテーション キーを取得する

> [!IMPORTANT]
> 新しい Azure リージョンでは、インストルメンテーション キーの代わりに接続文字列を使用する **必要** があります。 [接続文字列](./sdk-connection-string.md?tabs=java)により、利用統計情報と関連付けるリソースが識別されます。 また、リソースでテレメトリの宛先として使用するエンドポイントを変更することもできます。 接続文字列をコピーし、アプリケーションのコードまたは環境変数に追加する必要があります。
1. [Azure portal](https://portal.azure.com/) にサインインします。
2. Azure portal で、Application Insights のリソースを作成します。 アプリケーションの種類を [Java Web アプリケーション] に設定します。

3. 新しいリソースのインストルメンテーション キーを見つけます。 このキーは、後でコード プロジェクトに貼り付けます。

    ![新しいリソース概要で、[プロパティ] をクリックし、インストルメンテーション キーをコピーします](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>Application Insights SDK for Java をプロジェクトに追加する

*プロジェクトの種類を選択します。*

# <a name="maven"></a>[Maven](#tab/maven)

プロジェクトが既に Maven を使用してビルドする設定になっている場合は、*pom.xml* ファイルに次のコードをマージします。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.6.2</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

プロジェクトが既に Gradle を使用してビルドする設定になっている場合は、*build.gradle* ファイルに次のコードをマージします。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.6.2'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

---

### <a name="questions"></a>疑問がある場合
* *`-web-auto`、`-web`、および `-core` コンポーネントの関係はどのようなものですか。*
  * `applicationinsights-web-auto` は、実行時に Application Insights サーブレット フィルターを自動的に登録することによって、HTTP サーブレットの要求数と応答時間を追跡するメトリックを提供します。
  * `applicationinsights-web` も HTTP サーブレットの要求数と応答時間を追跡するメトリックを提供しますが、アプリケーションでの Application Insights サーブレット フィルターの手動の登録が必要です。
  * `applicationinsights-core` は、ベア API のみを提供します (アプリケーションがサーブレット ベースでない場合など)。
  
* *SDK を最新バージョンに更新するにはどうすればよいですか。*
  * 2020 年 11 月以降、Java アプリケーションの監視には、Azure Monitor Application Insights Java 3.0 エージェントを使用した自動インストルメンテーションをお勧めします。 開始方法について詳しくは、「[Application Insights Java 3.0 エージェント](./java-in-process-agent.md)」を参照してください。

## <a name="add-an-applicationinsightsxml-file"></a>*ApplicationInsights.xml* ファイルを追加する
*ApplicationInsights.xml* をプロジェクトのリソース フォルダーに追加するか、プロジェクトのデプロイ クラス パスに追加されていることを確認します。 次の XML をファイルにコピーします。

インストルメンテーション キーを Azure portal から受け取ったものに置き換えます。

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
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

必要に応じて、構成ファイルをアプリケーションがアクセスできる任意の場所に置くことができます。  システム プロパティ `-Dapplicationinsights.configurationDirectory` に、*ApplicationInsights.xml* があるディレクトリを指定します。 たとえば、`E:\myconfigs\appinsights\ApplicationInsights.xml` にある構成ファイルは、プロパティ `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` を使用して構成されます。

* インストルメンテーション キーは、テレメトリのすべての項目と共に送信されます。インストルメンテーション キーを受け取った Application Insights は、リソース内にこのキーを表示します。
* HTTP 要求コンポーネントはオプションです。 このコンポーネントは、要求と応答時間に関するテレメトリをポータルに自動的に送信します。
* イベントの関連付けは、HTTP 要求コンポーネントに対する追加の操作です。 サーバーによって受信された各要求に識別子が割り当てられます。 次に、この識別子をプロパティとして (プロパティ 'Operation.Id' として) テレメトリのすべての項目に追加します。 これにより、 [診断検索][diagnostic]でフィルターを設定して、テレメトリを各要求に関連付けることができます。

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>インストルメンテーション キーの他の設定方法
Application Insights SDK は、次の順序でキーを探します。

1. システム プロパティ: -DAPPINSIGHTS_INSTRUMENTATIONKEY=your_ikey
2. 環境変数:APPINSIGHTS_INSTRUMENTATIONKEY
3. 構成ファイル:*ApplicationInsights.xml*

これは [コードで設定する](./api-custom-events-metrics.md#ikey)こともできます。

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>エージェントを追加する

[Java エージェントをインストール](java-agent.md)して、送信 HTTP 呼び出し、JDBC クエリ、アプリケーションのログ記録、およびより適切な操作の名前付けをキャプチャします。

## <a name="run-your-application"></a>アプリケーションを実行する
開発用コンピューターでデバッグ モードで実行するか、サーバーに発行します。

## <a name="view-your-telemetry-in-application-insights"></a>Application Insights でのテレメトリを表示する
[Microsoft Azure ポータル](https://portal.azure.com)の Application Insights リソースに戻ります。

HTTP 要求データが概要ブレードに表示されます (表示されない場合は、数秒待ってから [最新の情報に更新] をクリックします)。

![概要サンプル データのスクリーンショット](./media/java-get-started/overview-graphs.png)

[メトリックの詳細についてはこちらをご覧ください。][metrics]

任意のグラフをクリックして、より詳細な集計メトリックを表示します。

![チャート付きの Application Insights の [失敗] ペイン](./media/java-get-started/006-barcharts.png)

### <a name="instance-data"></a>インスタンス データ
個々のインスタンスを表示するには、特定の要求の種類をクリックします。

![特定のサンプル ビューをドリルダウンする](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analytics:強力なクエリ言語
より多くのデータが蓄積されると、データを集計するためのクエリと、個々のインスタンスを検索するためのクエリの両方を実行できます。  [Analytics](../logs/log-query-overview.md) は、パフォーマンスと使用状況を把握したり、診断を行ったりするための強力なツールです。

![Example of Analytics](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>サーバーへのアプリのインストール
次に、サーバーにアプリを発行してユーザーがアプリを使用できるようにし、ポータルに表示されるテレメトリを監視します。

* アプリケーションがこれらのポートにテレメトリを送信できるようにファイアウォールが設定されていることを確認します。

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* 送信トラフィックをファイアウォール経由でルーティングする必要がある場合は、システム プロパティの `http.proxyHost` と `http.proxyPort` を定義します。

* Windows サーバーに次のものをインストールします。

  * [Microsoft Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/download/details.aspx?id=40784)

    (このコンポーネントにより、パフォーマンス カウンターが有効になります。)

## <a name="azure-app-service-aks-vms-config"></a>Azure App Service、AKS、VM の構成

Azure リソース プロバイダーのいずれかで実行されているアプリケーションを監視する際に最適で最も簡単な方法は、[Java 3.0 エージェント](./java-in-process-agent.md)を介して Application Insights の自動インストルメンテーションを使用することです。


## <a name="exceptions-and-request-failures"></a>例外と要求エラー
ハンドルされない例外と要求エラーは、Application Insights Web フィルターによって自動的に収集されます。

他の例外に関するデータを収集するには、[コードに trackException() への呼び出しを挿入する][apiexceptions]ことができます。

## <a name="monitor-method-calls-and-external-dependencies"></a>メソッドの呼び出しと外部依存関係の監視
[Java エージェントをインストール](java-agent.md) して、JDBC を通じて指定された内部メソッドと実行された呼び出しをタイミング データと共にログに記録します。

また、自動的な操作の名前付けの場合。

## <a name="w3c-distributed-tracing"></a>W3C 分散トレース

Application Insights Java SDK では、[W3C 分散トレース](https://w3c.github.io/trace-context/)がサポートされるようになりました。

受信 SDK の構成の詳細については、[相関関係](correlation.md)に関する記事をご覧ください。

送信 SDK の構成は、[AI-Agent.xml](java-agent.md) ファイル内で定義されます。

## <a name="performance-counters"></a>パフォーマンス カウンター
**[調査]** 、 **[メトリック]** の順に開くと、一連のパフォーマンス カウンターが表示されます。

![プロセス プライベート バイトが選択されているメトリック ペインのスクリーンショット](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>パフォーマンス カウンター コレクションをカスタマイズする
パフォーマンス カウンターの標準セットのコレクションを無効にするには、*ApplicationInsights.xml* ファイルのルート ノードの下に次のコードを追加します。

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>追加のパフォーマンス カウンターを収集する
収集する追加のパフォーマンス カウンターを指定できます。

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX カウンター (Java 仮想マシンによって公開されます)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` - Application Insights ポータルに表示される名前。
* `objectName` - JMX オブジェクトの名前。
* `attribute` - 取得する JMX オブジェクト名の属性
* `type` (省略可能) - JMX オブジェクトの属性の型。
  * 既定値: int、long などの単純型。
  * `composite`: パフォーマンス カウンター データは、"Attribute.Data" 形式です。
  * `tabular`: パフォーマンス カウンター データは、テーブル行形式です。

#### <a name="windows-performance-counters"></a>Windows パフォーマンス カウンター
それぞれの [Windows パフォーマンス カウンター](/windows/win32/perfctrs/performance-counters-portal) は、(フィールドがクラスのメンバーであるのと同様に) カテゴリのメンバーです。 カテゴリについては、グローバルに設定することも、数字または名前付きインスタンスを設定することもできます。

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName - Application Insights ポータルに表示される名前。
* categoryName - このパフォーマンス カウンターが関連付けられているパフォーマンス カウンターのカテゴリ (パフォーマンス オブジェクト)。
* counterName - パフォーマンス カウンターの名前。
* instanceName - パフォーマンス カウンター カテゴリ インスタンスの名前、または空の文字列 ("") (カテゴリにインスタンスが 1 つ含まれている場合)。 categoryName が Process であり、アプリが実行されている現在の JVM プロセスからパフォーマンス カウンターを収集する場合は、 `"__SELF__"`を指定します。

### <a name="unix-performance-counters"></a>Unix パフォーマンス カウンター
* [Application Insights プラグインを使用して collectd をインストール](java-collectd.md) し、さまざまな種類のシステムとネットワークに関するデータを取得します。

## <a name="get-user-and-session-data"></a>ユーザーとセッションのデータを取得する
Web サーバーからテレメトリを送信しようとしているところです。 ここで、アプリケーションの状態を完全に把握するために、監視を追加することもできます。

* [Web ページにテレメトリを追加][usage] して、ページ ビューやユーザー メトリックを監視します。
* [Web テストを設定][availability] して、アプリケーションが動作していて応答できることを確認します。

## <a name="send-your-own-telemetry"></a>独自のテレメトリを送信する
SDK をインストールすると、API を使用して独自のテレメトリを送信できるようになります。

* アプリケーションでのユーザーの行動を把握するには、[カスタム イベントおよびメトリックを追跡][api]します。
* [イベントおよびログを検索][diagnostic] します。

## <a name="availability-web-tests"></a>可用性 Web テスト
Application Insights では、Web サイトを定期的にテストして、Web サイトが正常に動作および応答していることを確認できます。

[可用性 Web テストを設定する方法][availability]の詳細を確認してください。

## <a name="questions-problems"></a>疑問がある場合 問題が発生した場合
[Java のトラブルシューティング](java-troubleshoot.md)

## <a name="next-steps"></a>次のステップ
* [依存関係の呼び出しを監視する](java-agent.md)
* [Unix パフォーマンス カウンターを監視する](java-collectd.md)
* [Web ページに監視機能](javascript.md)を追加して、ページの読み込み時間、AJAX 呼び出し、ブラウザーの例外を監視する
* [カスタム テレメトリ](./api-custom-events-metrics.md)を書き込んで、ブラウザーまたはサーバーでの使用状況を追跡する
* [Analytics](../logs/log-query-overview.md) を使用して、アプリからのテレメトリに対して強力なクエリを実行する
* 詳細については、「[Azure for Java developers (Java 開発者向けの Azure)](/java/azure)」を参照してください。

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#trackexception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md
[usage]: javascript.md