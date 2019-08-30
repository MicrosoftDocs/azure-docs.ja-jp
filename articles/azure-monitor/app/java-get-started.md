---
title: Azure Application Insights を使用した Java Web アプリの分析 | Microsoft Docs
description: 'Application Insights を使用した Java Web アプリのアプリケーション パフォーマンス監視 '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: lagayhar
ms.openlocfilehash: 27610280bafa6d8e9e33f84af2d3e9f6c2c9ea5c
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967812"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Java Web プロジェクトで Application Insights を使う

[Application Insights](https://azure.microsoft.com/services/application-insights/) は、ライブ アプリケーションのパフォーマンスと使用状況を把握するのに役立つ、Web 開発者向けの拡張可能な分析サービスです。 [要求の自動的なインストルメント化、依存関係の追跡、パフォーマンス カウンターの収集](auto-collect-dependencies.md#java)、パフォーマンスの問題と例外の診断、ユーザーがアプリで行っていることを追跡するための[コードの作成][api]に使用します。 

![概要サンプル データのスクリーンショット](./media/java-get-started/overview-graphs.png)

Application Insights は、Linux、Unix、Windows で動作する Java アプリをサポートします。

必要なもの:

* JRE バージョン 1.7 または 1.8
* [Microsoft Azure](https://azure.microsoft.com/) サブスクリプション。

Spring フレームワークの方がよければ、[Spring Boot 初期化子アプリを構成して Application Insights ガイドを使用](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)してみてください

## <a name="1-get-an-application-insights-instrumentation-key"></a>1.Application Insights のインストルメンテーション キーを取得する
1. [Microsoft Azure ポータル](https://portal.azure.com)にサインインします。
2. Application Insights リソースを作成します。 アプリケーションの種類を [Java Web アプリケーション] に設定します。

3. 新しいリソースのインストルメンテーション キーを見つけます。 このキーは、後でコード プロジェクトに貼り付けます。

    ![新しいリソース概要で、[プロパティ] をクリックし、インストルメンテーション キーをコピーします](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2.Application Insights SDK for Java をプロジェクトに追加する
*プロジェクトに適した方法を選択してください。*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Maven を使用している場合: <a name="maven-setup" />
プロジェクトが既に Maven を使用してビルドする設定になっている場合は、pom.xml ファイルに次のコードをマージします。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

```XML

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[2.0,)</version>
      </dependency>
    </dependencies>
```

* *ビルド エラーやチェックサム検証エラーが発生した場合は、* 特定のバージョンを試してください (例: `<version>2.0.n</version>`)。 最新バージョンは、[SDK リリース ノート](https://github.com/Microsoft/ApplicationInsights-Java#release-notes)または [Maven アーティファクト](https://search.maven.org/#search%7Cga%7C1%7Capplicationinsights)で確認できます。
* *新しい SDK に更新する必要がありますか?* プロジェクトの依存関係を更新します。

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Gradle を使用している場合: <a name="gradle-setup" />
プロジェクトが既に Gradle を使用してビルドする設定になっている場合は、build.gradle ファイルに次のコードをマージします。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

```gradle

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '2.+'
      // or applicationinsights-core for bare API
    }
```

#### <a name="if-youre-using-eclipse-to-create-a-dynamic-web-project-"></a>Eclipse を使用して動的 Web プロジェクトを作成している場合:
Application Insights SDK for Java プラグインを使用します。 注意: (Maven/Gradle を使用していないとの想定において) このプラグインを使用することで Application Insights をより迅速に実行できるとしても、それは依存関係管理システムではありません。 そのため、プラグインが更新されても、プロジェクトの Application Insights ライブラリは自動的に更新されません。

* *ビルド エラーやチェックサム検証エラーが発生した場合は、* 特定のバージョンを試してください (例: `version:'2.0.n'`)。 最新バージョンは、[SDK リリース ノート](https://github.com/Microsoft/ApplicationInsights-Java#release-notes)または [Maven アーティファクト](https://search.maven.org/#search%7Cga%7C1%7Capplicationinsights)で確認できます。
* "*新しい SDK に更新するには*" プロジェクトの依存関係を更新します。

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>それ以外で、依存関係を手動で管理している場合:
[最新バージョン](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)をダウンロードし、必要なファイルをプロジェクトにコピーして、以前のバージョンを置き換えます。

### <a name="questions"></a>疑問がある場合...
* *`-core` コンポーネントと `-web` コンポーネントはどのような関係ですか。*
  * `applicationinsights-core` は最小限の API を提供します。 このコンポーネントは常に必要です。
  * `applicationinsights-web` HTTP 要求数と応答時間を追跡するメトリックを提供します。 このテレメトリを自動的に収集しない場合、このコンポーネントは省略できます。 たとえば、独自に記述する場合です。
  
* *SDK を最新バージョンに更新するにはどうすればよいですか。*
  * Gradle または Maven を使用している場合:
    * ビルド ファイルを更新して最新バージョンを指定するか、Gradle/Maven のワイルドカード構文を使用して最新バージョンが自動的に含まれるようにします。 その後、プロジェクトの依存関係を更新します。 ワイルドカード構文は、上の例で見ることができます ([Gradle](#gradle-setup) または [Maven](#maven-setup))。
  * 依存関係を手動で管理している場合:
    * 最新の [Application Insights SDK for Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) をダウンロードして、古いものと置き換えます。 変更は [SDK リリース ノート](https://github.com/Microsoft/ApplicationInsights-Java#release-notes)に記載されます。

## <a name="3-add-an-applicationinsightsxml-file"></a>手順 3.ApplicationInsights.xml ファイルを追加する
ApplicationInsights.xml をプロジェクトのリソース フォルダーに追加するか、プロジェクトのデプロイメント クラス パスに追加されていることを確認します。 次の XML をファイルにコピーします。

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
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

必要に応じて、構成ファイルをアプリケーションがアクセスできる任意の場所に置くことができます。  システム プロパティ `-Dapplicationinsights.configurationDirectory` は、ApplicationInsights.xml があるディレクトリを指定します。 たとえば、`E:\myconfigs\appinsights\ApplicationInsights.xml` にある構成ファイルは、プロパティ `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` を使用して構成されます。

* インストルメンテーション キーは、テレメトリのすべての項目と共に送信されます。インストルメンテーション キーを受け取った Application Insights は、リソース内にこのキーを表示します。
* HTTP 要求コンポーネントはオプションです。 このコンポーネントは、要求と応答時間に関するテレメトリをポータルに自動的に送信します。
* イベントの関連付けは、HTTP 要求コンポーネントに対する追加の操作です。 この操作では、サーバーで受信した各要求に識別子を割り当てた後、この識別子をテレメトリのすべての項目に "Operation.Id" プロパティとして追加します。 これにより、 [診断検索][diagnostic]でフィルターを設定して、テレメトリを各要求に関連付けることができます。

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>インストルメンテーション キーの他の設定方法
Application Insights SDK は、次の順序でキーを探します。

1. システムのプロパティ: -DAPPLICATION_INSIGHTS_IKEY=your_ikey
2. 環境変数:APPLICATION_INSIGHTS_IKEY
3. 構成ファイル:ApplicationInsights.xml

これは [コードで設定する](../../azure-monitor/app/api-custom-events-metrics.md#ikey)こともできます。

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

[Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) では、コードからのインストルメンテーション キーの読み取りがサポートされていないことに注意してください。

## <a name="4-add-an-http-filter"></a>4.HTTP フィルターを追加する
最後の構成手順では、HTTP 要求コンポーネントが各 Web 要求をログに記録できるようにします (単に最小限の API が必要な場合はこの手順を行う必要はありません)。

### <a name="spring-boot-applications"></a>Spring Boot アプリケーション
Application Insights の `WebRequestTrackingFilter` を Configuration クラスに登録します。

```Java
package <yourpackagename>.configurations;

import javax.servlet.Filter;

import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.core.Ordered;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import com.microsoft.applicationinsights.TelemetryConfiguration;
import com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter;

@Configuration
public class AppInsightsConfig {

    @Bean
    public String telemetryConfig() {
        String telemetryKey = System.getenv("<instrumentation key>");
        if (telemetryKey != null) {
            TelemetryConfiguration.getActive().setInstrumentationKey(telemetryKey);
        }
        return telemetryKey;
    }

    /**
     * Programmatically registers a FilterRegistrationBean to register WebRequestTrackingFilter
     * @param webRequestTrackingFilter
     * @return Bean of type {@link FilterRegistrationBean}
     */
    @Bean
    public FilterRegistrationBean webRequestTrackingFilterRegistrationBean(WebRequestTrackingFilter webRequestTrackingFilter) {
        FilterRegistrationBean registration = new FilterRegistrationBean();
        registration.setFilter(webRequestTrackingFilter);
        registration.addUrlPatterns("/*");
        registration.setOrder(Ordered.HIGHEST_PRECEDENCE + 10);
        return registration;
    }


    /**
     * Creates bean of type WebRequestTrackingFilter for request tracking
     * @param applicationName Name of the application to bind filter to
     * @return {@link Bean} of type {@link WebRequestTrackingFilter}
     */
    @Bean
    @ConditionalOnMissingBean

    public WebRequestTrackingFilter webRequestTrackingFilter(@Value("${spring.application.name:application}") String applicationName) {
        return new WebRequestTrackingFilter(applicationName);
    }


}
```

> [!NOTE]
> Spring Boot 1.3.8 以前を使用している場合は、次の行で FilterRegistrationBean を置き換えます

```Java
    import org.springframework.boot.context.embedded.FilterRegistrationBean;
```

このクラスにより、`WebRequestTrackingFilter` が http フィルター チェーンの最初のフィルターとして構成されます。 また、取得可能な場合は、オペレーティング システムの環境変数からインストルメンテーション キーがプルされます。

> ここでは Spring MVC 構成ではなく Web http フィルター構成を使用しています。なぜなら、これは Spring Boot アプリケーションであり、独自の Spring MVC 構成があるためです。 Spring MVC に固有の構成については、以降のセクションを参照してください。

### <a name="applications-using-webxml"></a>Web.xml を使用するアプリケーション
プロジェクトの web.xml ファイルを見つけて開きます。アプリケーション フィルターが構成されている web-app ノードの下に次のコードをマージします。

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

   <!-- This listener handles shutting down the TelemetryClient when an application/servlet is undeployed. -->
    <listener>
      <listener-class>com.microsoft.applicationinsights.web.internal.ApplicationInsightsServletContextListener</listener-class>
    </listener>
```

#### <a name="if-youre-using-spring-web-mvc-31-or-later"></a>Spring Web MVC 3.1 以降を使用している場合
Application Insights パッケージを含めるように、*-servlet.xml で次の要素を編集します。

```XML

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>
```

#### <a name="if-youre-using-struts-2"></a>Struts 2 を使用している場合
次の項目を Struts 構成ファイルに追加します (通常は、struts.xml または struts-default.xml)。

```XML

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />
```

既定のスタックにインターセプターが定義されている場合は、インターセプターをそのスタックに追加できます。

## <a name="5-run-your-application"></a>5.アプリケーションを実行する
開発用コンピューターでデバッグ モードで実行するか、サーバーに発行します。

## <a name="6-view-your-telemetry-in-application-insights"></a>6.Application Insights でのテレメトリを表示する
[Microsoft Azure ポータル](https://portal.azure.com)の Application Insights リソースに戻ります。

HTTP 要求データが概要ブレードに表示されます (表示されない場合は、数秒待ってから [最新の情報に更新] をクリックします)。

![概要サンプル データのスクリーンショット](./media/java-get-started/overview-graphs.png)

[メトリックの詳細についてはこちらをご覧ください。][metrics]

任意のグラフをクリックして、より詳細な集計メトリックを表示します。

![チャート付きの Application Insights の [失敗] ペイン](./media/java-get-started/006-barcharts.png)

> Application Insights では、MVC アプリケーションの HTTP 要求の形式として、 `VERB controller/action`が想定されます。 たとえば、`GET Home/Product/f9anuh81`、`GET Home/Product/2dffwrf5`、`GET Home/Product/sdf96vws` は、`GET Home/Product` にグループ化されます。 このグループ化により、要求数や要求の平均実行時間など、要求の意味のある集計を行うことができます。
>
>

### <a name="instance-data"></a>インスタンス データ
個々のインスタンスを表示するには、特定の要求の種類をクリックします。

![特定のサンプル ビューをドリルダウンする](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analytics:強力なクエリ言語
より多くのデータが蓄積されると、データを集計するためのクエリと、個々のインスタンスを検索するためのクエリの両方を実行できます。  [Analytics](../../azure-monitor/app/analytics.md) は、パフォーマンスと使用状況を把握したり、診断を行ったりするための強力なツールです。

![Example of Analytics](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7.サーバーへのアプリのインストール
次に、サーバーにアプリを発行してユーザーがアプリを使用できるようにし、ポータルに表示されるテレメトリを監視します。

* アプリケーションがこれらのポートにテレメトリを送信できるようにファイアウォールが設定されていることを確認します。

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* 送信トラフィックをファイアウォール経由でルーティングする必要がある場合は、システム プロパティの `http.proxyHost` と `http.proxyPort` を定義します。

* Windows サーバーに次のものをインストールします。

  * [Microsoft Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/download/details.aspx?id=40784)

    (このコンポーネントにより、パフォーマンス カウンターが有効になります。)

## <a name="azure-app-service-config-spring-boot"></a>Azure App Service の構成 (Spring Boot)

Windows で動作する Spring Boot アプリでは、 Azure App Services での追加構成の実行を必要とします。 **web.config** を変更して次を追加します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>例外と要求エラー
未処理の例外は、自動的に収集されます。

その他の例外に関するデータを収集するには 2 つのオプションがあります。

* [trackException() への呼び出しをコードに挿入します][apiexceptions]。
* [Java エージェントをサーバーにインストール](java-agent.md)します。 監視するメソッドを指定します。

## <a name="monitor-method-calls-and-external-dependencies"></a>メソッドの呼び出しと外部依存関係の監視
[Java エージェントをインストール](java-agent.md) して、JDBC を通じて指定された内部メソッドと実行された呼び出しをタイミング データと共にログに記録します。

## <a name="w3c-distributed-tracing"></a>W3C 分散トレース

Application Insights Java SDK では、[W3C 分散トレース](https://w3c.github.io/trace-context/)がサポートされるようになりました。

受信 SDK の構成の詳細については、[相関関係](correlation.md#w3c-distributed-tracing)に関する記事をご覧ください。

送信 SDK の構成は、[AI-Agent.xml](java-agent.md) ファイル内で定義されます。

## <a name="performance-counters"></a>パフォーマンス カウンター
**[調査]** 、 **[メトリック]** の順に開くと、一連のパフォーマンス カウンターが表示されます。

![プロセス プライベート バイトが選択されているメトリック ペインのスクリーンショット](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>パフォーマンス カウンター コレクションをカスタマイズする
パフォーマンス カウンターの標準セットのコレクションを無効にするには、ApplicationInsights.xml ファイルのルート ノードの下に次のコードを追加します。

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
* `type` (オプション) - JMX オブジェクトの属性の型。
  * 既定値: int、long などの単純型。
  * `composite`: パフォーマンス カウンター データは、"Attribute.Data" 形式です。
  * `tabular`: パフォーマンス カウンター データは、テーブル行形式です。

#### <a name="windows-performance-counters"></a>Windows パフォーマンス カウンター
それぞれの [Windows パフォーマンス カウンター](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) は、(フィールドがクラスのメンバーであるのと同様に) カテゴリのメンバーです。 カテゴリについては、グローバルに設定することも、数字または名前付きインスタンスを設定することもできます。

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

## <a name="capture-log-traces"></a>ログ トレースをキャプチャする
Application Insights を使用すると、Log4J、Logback、またはその他のログ フレームワークのログをさまざまな側面から分析できます。 ログは、HTTP 要求やその他のテレメトリに関連付けることができます。 方法については、[こちら][javalogs]をご覧ください。

## <a name="send-your-own-telemetry"></a>独自のテレメトリを送信する
SDK をインストールすると、API を使用して独自のテレメトリを送信できるようになります。

* アプリケーションでのユーザーの行動を把握するには、[カスタム イベントおよびメトリックを追跡][api]します。
* [イベントおよびログを検索][diagnostic] します。

## <a name="availability-web-tests"></a>可用性 Web テスト
Application Insights では、Web サイトを定期的にテストして、Web サイトが正常に動作および応答していることを確認できます。

可用性 Web テストのセットアップ方法の詳細については、[こちら][availability]をご覧ください。

## <a name="questions-problems"></a>疑問がある場合 問題が発生した場合
[Java のトラブルシューティング](java-troubleshoot.md)

## <a name="next-steps"></a>次の手順
* [依存関係の呼び出しを監視する](java-agent.md)
* [Unix パフォーマンス カウンターを監視する](java-collectd.md)
* [Web ページに監視機能](javascript.md)を追加して、ページの読み込み時間、AJAX 呼び出し、ブラウザーの例外を監視する
* [カスタム テレメトリ](../../azure-monitor/app/api-custom-events-metrics.md)を書き込んで、ブラウザーまたはサーバーでの使用状況を追跡する
* [Analytics](../../azure-monitor/app/analytics.md) を使用して、アプリからのテレメトリに対して強力なクエリを実行する
* 詳細については、「[Azure for Java developers (Java 開発者向けの Azure)](/java/azure)」を参照してください。

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
