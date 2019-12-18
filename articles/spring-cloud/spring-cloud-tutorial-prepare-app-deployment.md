---
title: チュートリアル - Azure Spring Cloud で Spring アプリケーションをデプロイ用に準備する
description: このチュートリアルでは、Java Spring アプリケーションをデプロイ用に準備します。
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e112fdc9e6f518e2ea3c72161e8978118cf19335
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890316"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>チュートリアル:Azure Spring Cloud で Java Spring アプリケーションをデプロイ用に準備する

このクイックスタートでは、既存の Java Spring Cloud アプリケーションを Azure Spring Cloud へのデプロイ用に準備する方法について説明します。  適切な構成により、Azure Spring Cloud は Spring Cloud アプリケーションの監視、スケーリング、更新を行う堅牢なサービスを実現します。 

## <a name="java-runtime-version"></a>Java ランタイム バージョン

Azure Spring Cloud で稼働できるのは、Spring または Java アプリケーションのみです。

Java 8 と Java 11 のどちらもサポートされます。 ホスティング環境には最新の Azure 用 Azul Zulu OpenJDK が含まれます。 Azure 用 Azul Zulu OpenJDK の詳細については、[こちらの記事](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)を参照してください。 

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot と Spring Cloud のバージョン

Azure Spring Cloud でサポートされるのは Spring Boot アプリのみです。 Spring Boot 2.0 と 2.1 の両方がサポートされます。 サポートされる Spring Boot と Spring Cloud の組み合わせを下の表に示します。

Spring Boot のバージョン | Spring Cloud のバージョン
---|---
2.0.x | Finchley.RELEASE
2.1.x | Greenwich.RELEASE

ご利用のバージョンに応じて、`pom.xml` ファイルに Spring Boot と Spring Cloud の依存関係が含まれていることを確認してください。

### <a name="version-20"></a>バージョン 2.0:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>バージョン 2.1:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud クライアントの依存関係

Azure Spring Cloud によって、Spring Cloud コンポーネント (Spring Cloud サービス レジストリや Spring Cloud 構成サーバーなど) がホストされ、自動的に管理されます。 自分の Azure Spring Cloud サービス インスタンスと通信できるよう、Azure Spring Cloud のクライアント ライブラリを依存関係に含めます。

以下の表に、Spring Boot および Spring Cloud アプリの適切なバージョンを示します。

Spring Boot のバージョン | Spring Cloud のバージョン | Azure Spring Cloud のバージョン
---|---|---
2.0.x | Finchley.RELEASE | 2.0.x
2.1.x | Greenwich.RELEASE | 2.1.x

`pom.xml` に、次のいずれかのスニペットを含めます。  ご自身のものとバージョンが一致するスニペットを選択します。

### <a name="version-20x"></a>バージョン 2.0.x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>バージョン 2.1.x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>その他の必要な依存関係

Azure Spring Cloud の組み込み機能を有効にするには、アプリケーションに次の依存関係が含まれている必要があります。 これにより、アプリケーションで自動的に各コンポーネントとの構成が正常に行われます。  

### <a name="service-registry"></a>サービス レジストリ

マネージド Azure サービス レジストリ サービスを使用するには、以下に示すように `spring-cloud-starter-netflix-eureka-client` を `POM.xml` に含めます。

サービス レジストリ サーバーのエンドポイントは、アプリで環境変数として自動的に挿入されます。 アプリケーションはサービス レジストリ サーバーに自動的に登録され、他の依存マイクロサービスを検出できるようになります。

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>分散構成

分散構成を有効にするには、`pom.xml` の依存関係セクションに `spring-cloud-config-client` を含めます。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> アプリケーションと構成サーバーの連携が停止するため、ブートストラップ構成で `spring.cloud.config.enabled=false` を指定しないでください。

### <a name="metrics"></a>メトリック

pom.xml の依存関係セクションに `spring-boot-starter-actuator` を含めます。 メトリックは JMX エンドポイントから定期的にプルされます。これは Azure portal を使用して視覚化できます。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>分散トレース

以下のとおり、pom.xml の依存関係セクションに `spring-cloud-starter-sleuth` と `spring-cloud-starter-zipkin` を含めます。 さらに、自分の Azure Spring Cloud サービス インスタンスと連携できるよう、Azure App Insights インスタンスを有効にする必要があります。 Azure Spring Cloud を使用して App Insights を有効にする方法については、[こちら](spring-cloud-tutorial-distributed-tracing.md)をお読みください

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

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Java Spring Cloud アプリケーションを Azure Spring Cloud へのデプロイ用に構成する方法について学習しました。  構成サーバーを有効にする方法を学習する場合は、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [構成サーバーの設定方法を学習する](spring-cloud-tutorial-config-server.md)。

その他のサンプルを GitHub で入手できます ([Azure Spring Cloud のサンプル](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql))。
