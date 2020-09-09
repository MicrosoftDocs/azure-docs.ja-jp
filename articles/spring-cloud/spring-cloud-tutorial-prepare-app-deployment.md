---
title: Azure Spring Cloud にデプロイするように Java Spring アプリケーションを準備する方法
description: Azure Spring Cloud にデプロイするように Java Spring アプリケーションを準備する方法について説明します。
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b2ae94da3d9b2dee62bc031c4a32d17b43be00a6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87021275"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Azure Spring Cloud で Java Spring アプリケーションをデプロイ用に準備する

このトピックでは、Azure Spring Cloud にデプロイできるように既存の Java Spring アプリケーションを準備する方法について説明します。 適切に構成すると、Azure Spring Cloud によって Java Spring Cloud アプリケーションの監視、スケーリング、更新を行う堅牢なサービスが実現します。

POM ファイルが構成されているときにアプリケーションを Azure Spring Cloud にデプロイする方法については、その他の例で説明しています。 
* [Azure portal を使用してアプリを起動する](spring-cloud-quickstart-launch-app-portal.md)
* [Azure CLI を使用してアプリを起動する](spring-cloud-quickstart-launch-app-cli.md)

この記事では、必要な依存関係とそれらを POM ファイルに追加する方法について説明します。

## <a name="java-runtime-version"></a>Java ランタイム バージョン

Azure Spring Cloud で稼働できるのは、Spring または Java アプリケーションのみです。

Azure Spring Cloud は、Java 8 と Java 11 の両方をサポートしています。 ホスティング環境には最新バージョンの Azure 用 Azul Zulu OpenJDK が含まれます。 Azure 用 Azul Zulu OpenJDK の詳細については、[JDK のインストール](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)に関するページを参照してください。

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot と Spring Cloud のバージョン

既存の Spring Boot アプリケーションを Azure Spring Cloud にデプロイできるよう準備するには、以降のセクションに示したように、アプリケーションの POM ファイルに Spring Boot と Spring Cloud の依存関係を追加します。

Azure Spring Cloud でサポートされるのは、Spring Boot バージョン 2.1 とバージョン 2.2 のどちらかの Spring Boot アプリのみです。 サポートされている Spring Boot と Spring Cloud の組み合わせを次の表に示します。

Spring Boot のバージョン | Spring Cloud のバージョン
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.RELEASE
2.3 | Hoxton.SR5

### <a name="dependencies-for-spring-boot-version-21"></a>Spring Boot バージョン 2.1 の依存関係

Spring Boot バージョン 2.1 の場合は、アプリケーションの POM ファイルに次の依存関係を追加します。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Spring Boot バージョン 2.2 の依存関係

Spring Boot バージョン 2.2 の場合は、アプリケーションの POM ファイルに次の依存関係を追加します。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
### <a name="dependencies-for-spring-boot-version-23"></a>Spring Boot バージョン 2.3 の依存関係

Spring Boot バージョン 2.3 の場合は、アプリケーションの POM ファイルに次の依存関係を追加します。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR5</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud クライアントの依存関係

Spring Cloud のコンポーネントは、Azure Spring Cloud によってホストおよび管理されます。 たとえば、Spring Cloud Service Registry や Spring Cloud Config Server などのコンポーネントです。 自分の Azure Spring Cloud サービス インスタンスと通信できるよう、Azure Spring Cloud クライアント ライブラリを依存関係に含めます。

Spring Boot と Spring Cloud を使用するアプリの正しい Azure Spring Cloud バージョンを次の表に示します。

Spring Boot のバージョン | Spring Cloud のバージョン | Azure Spring Cloud のバージョン
---|---|---
2.1 | Greenwich.RELEASE | 2.1
2.2 | Hoxton.RELEASE | 2.2
2.3 | Hoxton.SR5 | 2.3

次のいずれかの依存関係を pom.xml ファイルに含めます。 Azure Spring Cloud バージョンがお使いのものと一致する依存関係を選択します。

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Azure Spring Cloud バージョン 2.1 の依存関係

Spring Boot バージョン 2.1 の場合は、アプリケーションの POM ファイルに次の依存関係を追加します。

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Azure Spring Cloud バージョン 2.2 の依存関係

Spring Boot バージョン 2.2 の場合は、アプリケーションの POM ファイルに次の依存関係を追加します。

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.1</version>
</dependency>
```

Spring Boot バージョン 2.3 の場合は、アプリケーションの POM ファイルに次の依存関係を追加します。

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.3.0</version>
</dependency>
```

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Azure Spring Cloud の機能を有効にするためのその他の推奨される依存関係

サービス レジストリから分散トレースまでの Azure Spring Cloud の組み込み機能を有効にするには、アプリケーションに次の依存関係も含める必要があります。 特定のアプリに対応する機能が不要な場合は、これらの依存関係の一部を削除できます。

### <a name="service-registry"></a>サービス レジストリ

マネージド Azure Service Registry サービスを使用するには、次に示すように、pom.xml ファイルに `spring-cloud-starter-netflix-eureka-client` 依存関係を含めます。

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

サービス レジストリ サーバーのエンドポイントは、アプリで環境変数として自動的に挿入されます。 その後、アプリケーションによって、アプリケーション自体がサービス レジストリ サーバーに登録され、他の依存マイクロサービスが検出されます。

#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient 注釈

アプリケーションのソース コードに次の注釈を追加します。
```java
@EnableDiscoveryClient
```
たとえば、前出の例の piggymetrics アプリケーションを見てみましょう。
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>分散構成

分散構成を有効にするには、pom.xml ファイルの依存関係セクションに次の `spring-cloud-config-client` の依存関係を含めます。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> ブートストラップ構成で `spring.cloud.config.enabled=false` を指定しないでください。 そうしないと、アプリケーションと Config Server の連携が停止します。

### <a name="metrics"></a>メトリック

次に示すように、pom.xml ファイルの依存関係セクションに `spring-boot-starter-actuator` の依存関係を含めます。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 メトリックは JMX エンドポイントから定期的に取得されます。 メトリックを視覚化するには、Azure portal を使用します。

 > [!WARNING]
 > 構成プロパティに `spring.jmx.enabled=true` を指定してください。 そうしないと、Azure portal でメトリックを視覚化することはできません。

### <a name="distributed-tracing"></a>分散トレース

pom.xml ファイルの依存関係セクションに次の `spring-cloud-starter-sleuth` および `spring-cloud-starter-zipkin` の依存関係を含めます。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 さらに、自分の Azure Spring Cloud サービス インスタンスと連携できるよう、Azure Application Insights インスタンスを有効にする必要があります。 Azure Spring Cloud と共に Application Insights を使用する方法については、[分散トレースに関するドキュメント](spring-cloud-tutorial-distributed-tracing.md)を参照してください。

## <a name="see-also"></a>関連項目
* [アプリケーションのログとメトリックを分析する](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [構成サーバーを設定する](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Azure Spring Cloud で分散トレースを使用する](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Spring クイックスタート ガイド](https://spring.io/quickstart)
* [Spring Boot のドキュメント](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>次のステップ

このトピックでは、Java Spring Cloud アプリケーションを Azure Spring Cloud へのデプロイ用に構成する方法について学習しました。 Config Server インスタンスを設定する方法については、[Config Server インスタンスの設定](spring-cloud-tutorial-config-server.md)に関するページを参照してください。

その他のサンプルを GitHub で入手できます ([Azure Spring Cloud のサンプル](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples))。
