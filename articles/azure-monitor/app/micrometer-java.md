---
title: Azure Application Insights Java SDK で Micrometer を使用する方法
description: Application Insights Spring Boot アプリケーションと非 Spring Boot アプリケーションで Micrometer を使用するステップ バイ ステップ ガイドです。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/01/2018
ms.openlocfilehash: c9f1c6f6742f36d376668422f9c37c6c5a2228a4
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872997"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Azure Application Insights Java SDK で Micrometer を使用する方法
Micrometer のアプリケーション監視では、JVM ベースのアプリケーション コードのメトリックが測定され、好みの監視システムにデータをエクスポートできます。 この記事では、Spring Boot アプリケーションと非 Spring Boot アプリケーションの両方に対して Application Insights で Micrometer を使用する方法を説明します。

## <a name="using-spring-boot-15x"></a>Spring Boot 1.5x の使用
次の依存関係を pom.xml ファイルまたは build.gradle ファイルに追加します。 
* [Application Insights spring-boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter) 2.5.0 以降
* Micrometer Azure Registry 1.1.0 以降
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 以降 (これは、Spring フレームワーク内の自動構成コードをバックポートします)。
* [Application Insights リソース](../../azure-monitor/app/create-new-resource.md )

手順

1. Spring Boot アプリケーションの pom.xml ファイルを更新し、次の依存関係を追加します。

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. 次のプロパティを使用して、Application Insights のインストルメンテーション キーで application.properties または yml ファイルを更新します。

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. アプリケーションをビルドして実行します
2. 集計前のメトリックが Azure Monitor に自動収集されます。 Application Insights Spring Boot スターターを微調整する方法の詳細については、[GitHub の readme](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md) をご覧ください。

## <a name="using-spring-2x"></a>Spring 2.x の使用

次の依存関係を pom.xml ファイルまたは build.gradle ファイルに追加します。

* Application Insights Spring-boot-starter 2.1.2 以降
* Azure-spring-boot-metrics-starters 2.0.7 以降
* [Application Insights リソース](../../azure-monitor/app/create-new-resource.md )

手順:

1. Spring Boot アプリケーションの pom.xml ファイルを更新し、次の依存関係を追加します。

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. 次のプロパティを使用して、Application Insights のインストルメンテーション キーで application.properties または yml ファイルを更新します。

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. アプリケーションをビルドして実行します
4. 集計前のメトリックが Azure Monitor に自動収集されます。 Application Insights Spring Boot スターターを微調整する方法の詳細については、[GitHub の readme](https://github.com/Microsoft/azure-spring-boot/releases/latest) をご覧ください。

既定のメトリック:

*    Tomcat、JVM、Logback メトリック、Log4J メトリック、アップタイム メトリック、プロセッサ メトリック、FileDescriptorMetrics 用に自動構成されるメトリック。
*    たとえば、Netflix Hystrix がクラス パス上に存在する場合は、それらのメトリックも取得されます。 
*    それぞれの Bean を追加することで、次のメトリックを使用できます。 
        - CacheMetrics (CaffeineCache、EhCache2、GuavaCache、HazelcastCache、JCache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 メトリック 
        - Kafka メトリック 

 

自動メトリック収集をオフにする方法: 
 
- JVM メトリック: 
    - management.metrics.binders.jvm.enabled=false 
- Logback メトリック: 
    - management.metrics.binders.logback.enabled=false
- アップタイム メトリック: 
    - management.metrics.binders.uptime.enabled=false 
- プロセッサ メトリック:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.metrics.binders.files.enabled=false 
- Hystrix メトリック (クラスパス上にライブラリがある場合): 
    - management.metrics.binders.hystrix.enabled=false 
- AspectJ メトリック (クラスパス上にライブラリがある場合): 
    - spring.aop.enabled=false 

> [!NOTE]
> Spring Boot アプリケーションの application.properties ファイルまたは application.yml ファイルで、上記のプロパティを指定します

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>非 Spring Boot Web アプリケーションで Micrometer を使用する

次の依存関係を pom.xml ファイルまたは build.gradle ファイルに追加します。

* Application Insights Web Auto 2.5.0 以降
* Micrometer Azure Registry 1.1.0 以降
* [Application Insights リソース](../../azure-monitor/app/create-new-resource.md )

手順:

1. 次の依存関係を pom.xml ファイルまたは build.gradle ファイルに追加します。

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. リソース フォルダーに `ApplicationInsights.xml` ファイルを置く:

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

3. Servlet クラスのサンプル (タイマー メトリックを出力):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

4. 構成クラスのサンプル:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

メトリックの詳細については、[Micrometer のドキュメント](https://micrometer.io/docs/)をご覧ください。

さまざまな種類のメトリックを作成する方法に関する他のサンプル コードは、[Micrometer の公式 GitHub リポジトリ](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples)で入手できます。

## <a name="how-to-bind-additional-metrics-collection"></a>追加のメトリック コレクションをバインドする方法

### <a name="springbootspring"></a>SpringBoot/Spring

それぞれのメトリックのカテゴリの Bean を作成します。 たとえば、Guava キャッシュ メトリックが必要な場合は次のようになります。

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
既定では有効にならないメトリックがいくつかありますが、上記の方法でバインドできます。 完全な一覧については、[Micrometer の公式 GitHub リポジトリ](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder )をご覧ください。

### <a name="non-spring-apps"></a>非 Spring アプリ
構成ファイルに次のバインド コードを追加します。
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>次の手順

* Micrometer の詳細については、公式の [Micrometer ドキュメント](https://micrometer.io/docs)を参照してください。
* Azure での Spring については、公式の [Azure の Spring ドキュメント](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable)を参照してください。
