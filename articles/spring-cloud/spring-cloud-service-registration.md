---
title: サービスの登録と検出を自動化する
description: Spring Cloud Service Registry を使用してサービスの検出と登録を自動化する方法について説明します
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: jeconnoc
ms.openlocfilehash: 72327e116e498ce0f6881a5c585a08e56c8bf8c2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038674"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Spring Cloud サービスを検出して登録する

サービスの検出は、マイクロサービスベースのアーキテクチャの重要な要件です。  各クライアントを手動で構成するのは時間がかかり、人為的なエラーが発生する可能性があります。  Azure Spring Cloud Service Registry では、この問題が解決されます。  構成が済むと、Service Registry サーバーによって、アプリケーションのマイクロサービスに対するサービスの登録と検出が制御されるようになります。 Service Registry サーバーでは、デプロイされたマイクロサービスの登録が管理され、クライアント側の負荷分散が有効にされ、DNS に依存せずにクライアントからサービス プロバイダーが切り離されます。

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Spring Cloud Service Registry を使用してアプリケーションを登録する

アプリケーションで Spring Cloud Service Registry を使用してサービスの登録と検出を管理できるようにするには、先に、アプリケーションの *pom.xml* ファイルに複数の依存関係を含める必要があります。

最初に、*pom.xml* の *repository* セクションにスナップショット リポジトリを追加します

```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
```

## <a name="include-dependencies"></a>依存関係を含める

次に、*spring-cloud-starter-netflix-eureka-client* および *spring-cloud-starter-azure-spring-cloud-client* に対する依存関係を *pom.xml* に含めます

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>最上位のクラスを更新する

最後に、アプリケーションの最上位のクラスに注釈を追加します

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Spring Cloud Service Registry サーバーのエンドポイントが、アプリケーションに環境変数として挿入されます。  マイクロサービスが Service Registry サーバーに自己を登録し、他の依存するマイクロサービスを検出できるようになります。

変更がサーバーからすべてのマイクロサービスに反映されるまでに数分かかる場合があることに注意してください。
