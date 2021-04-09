---
title: Azure App Configuration の使用方法を学習するためのクイックスタート
description: このクイックスタートでは、Azure App Configuration でコードとは別にアプリケーション設定のストレージと管理を一元化して、Java Spring アプリを作成します。
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.custom: devx-track-java
ms.author: alkemper
ms.openlocfilehash: 40c8fae292f465b3ecc8778a21e4903bdf423a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932032"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>クイック スタート:Azure App Configuration を使用して Java Spring アプリを作成する

このクイック スタートでは、コードとは別にアプリケーション設定のストレージと管理を一元化するために、Azure App Configuration を Java Spring アプリに組み込みます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- バージョン 8 を含む、サポートされている [Java Development Kit (JDK)](/java/azure/jdk)。
- [Apache Maven](https://maven.apache.org/download.cgi) バージョン 3.0 以降。

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **[構成エクスプローラー]**  >  **[+ 作成]**  >  **[キー値]** の順に選択して、次のキーと値のペアを追加します。

    | キー | Value |
    |---|---|
    | /application/config.message | こんにちは |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。

8. **[適用]** を選択します。

## <a name="create-a-spring-boot-app"></a>Spring Boot アプリを作成する

[Spring Initializr](https://start.spring.io/) を使用して、新しい Spring Boot プロジェクトを作成します。

1. <https://start.spring.io/> を参照します。

1. 次のオプションを指定します。

   - **Java** で **Maven** プロジェクトを生成します。
   - **Spring Boot** のバージョンとして、2.0 以降を指定します。
   - アプリケーションの **グループ (Group)** と **成果物 (Artifact)** の名前を指定します。
   - **Spring Web** の依存関係を追加します。

1. 前の各オプションを指定してから、 **[プロジェクトの生成]** を選択します。 メッセージが表示されたら、ローカル コンピューター上のパスにプロジェクトをダウンロードします。

## <a name="connect-to-an-app-configuration-store"></a>App Configuration ストアに接続する

1. ファイルをローカル システム上に展開したら、シンプルな Spring Boot アプリケーションの編集を開始できます。 アプリのルート ディレクトリで *pom.xml* ファイルを探します。

1. テキスト エディターで *pom.xml* ファイルを開き、Spring Cloud Azure Config スターターを `<dependencies>` のリストに追加します。

    **Spring Cloud 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.5</version>
    </dependency>
    ```

    **Spring Cloud 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.7</version>
    </dependency>
    ```

1. アプリのパッケージ ディレクトリに、*MessageProperties.java* という名前の新しい Java ファイルを作成します。 次の行を追加します。

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. アプリのパッケージ ディレクトリに、*HelloController.java* という名前の新しい Java ファイルを作成します。 次の行を追加します。

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. メイン アプリケーションの Java ファイルを開き、`@EnableConfigurationProperties` を追加してこの機能を有効にします。

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. アプリの resources ディレクトリに、`bootstrap.properties` という名前の新しいファイルを作成し、そのファイルに以下の行を追加します。 サンプルの値を、App Configuration ストアの適切なプロパティに置き換えます。

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. **APP_CONFIGURATION_CONNECTION_STRING** という名前の環境変数に、App Configuration ストアへのアクセス キーを設定します。 コマンド ラインで次のコマンドを実行してコマンド プロンプトを再起動し、変更が反映されるようにします。

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    macOS または Linux を使用する場合は、次のコマンドを実行します。

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. Spring Boot アプリケーションを Maven でビルドし、実行します。次に例を示します。

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. アプリケーションが実行されたら、*curl* を使用してアプリケーションをテストできます。次に例を示します。

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    App Configuration ストアに入力したメッセージが表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、新しい App Configuration ストアを作成して、Java Spring アプリで使用しました。 詳細については、「[Azure の Spring](/java/azure/spring-framework/)」を参照してください。 Java Spring アプリで構成設定を動的に更新できるようにする方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [動的な構成を有効にする](./enable-dynamic-configuration-java-spring-app.md)