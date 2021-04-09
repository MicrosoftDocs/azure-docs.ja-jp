---
title: Spring Cloud Resilience4J サーキット ブレーカーのメトリックを収集する
description: Spring Cloud Resilience4J サーキット ブレーカーのメトリックを収集する方法。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9fc8ccc5ba21f02885b2002d5040051baac92068
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877480"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-preview"></a>Spring Cloud Resilience4J サーキット ブレーカーのメトリックを収集する (プレビュー)

このドキュメントでは、Application Insights の java インプロセス エージェントを使用して、Spring Cloud Resilience4j サーキット ブレーカーのメトリックを収集する方法について説明します。  この機能を使用すると、Application Insights から Resilience4j サーキット ブレーカーのメトリックを監視できます。

[spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) を使用して、その仕組みを説明します。

## <a name="prerequisites"></a>[前提条件]

* [Application Insights ガイドの Java インプロセス エージェント](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights)に関する記事から、Java インプロセス エージェントを有効にします。 

* [Application Insights ガイド](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)に関する記事から、Resilience4J メトリックのディメンション コレクションを有効にします。

* 開発用コンピューターでまだ使用したことがない場合は、git、Maven、Java をインストールします。

## <a name="build-and-deploy-apps"></a>アプリをビルドして配置する

次の手順では、アプリをビルドしてデプロイします。

1. デモ リポジトリをクローニングしてビルドします。

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. エンドポイントを使用してアプリケーションを作成します

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. アプリケーションを展開する。

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Resilience4j に必要な依存関係を含めます。
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * お客様のコードでは、`CircuitBreakerFactory` の API を使用する必要があります。これは、`bean` として実装され、Spring Cloud サーキット ブレーカー スターターを含めたときに自動的に作成されます。 詳細については、「[Spring Cloud サーキット ブレーカー](https://spring.io/projects/spring-cloud-circuitbreaker#overview)」を参照してください。
>
> * 次の 2 つの依存関係は、上記の resilient4j パッケージと競合しています。  お客様はこれらを含めないようにしてください。
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> ゲートウェイ アプリケーションから提供された URL に移動し、次のようにして [spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) からエンドポイントにアクセスします。
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>ポータルから Resilence4j メトリックを見つける

1. Azure Spring Cloud ポータルから **[Application Insights]** ブレードを選択し、 **[Application Insights]** をクリックします。

   [ ![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. **[Application Insights]** ページで **[メトリック]** を選択します。  **[メトリック名前空間]** から **[azure. applicationinsights]** を選択します。  また、 **[平均]** で **[resilience4j_circuitbreaker_buffered_calls]** メトリックを選択します。

   [ ![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. **[resilience4j_circuitbreaker_calls]** メトリックと **[平均]** を選択します。

   [ ![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. **[resilience4j_circuitbreaker_calls]** メトリックと **[平均]** を選択します。  **[フィルターの追加]** をクリックし、名前で **[createNewAccount]** を選択します。

   [ ![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. **[resilience4j_circuitbreaker_calls]** メトリックと **[平均]** を選択します。  次に、 **[分割の適用]** をクリックして、 **[種類]** を選択します。

   [ ![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. **[平均]** で **[resilience4j_circuitbreaker_calls]** 、 **[resilience4j_circuitbreaker_buffered_calls]** 、 **[resilience4j_circuitbreaker_slow_calls]** メトリックを選択します。

   [ ![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>関連項目

* [Application Insights](spring-cloud-howto-application-insights.md)
* [分散トレース](spring-cloud-howto-distributed-tracing.md)
* [サーキット ブレーカー ダッシュボード](spring-cloud-tutorial-circuit-breaker.md)