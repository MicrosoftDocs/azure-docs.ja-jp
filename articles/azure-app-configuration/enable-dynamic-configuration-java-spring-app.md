---
title: Spring Boot アプリで動的な構成を使用する
titleSuffix: Azure App Configuration
description: Spring Boot アプリの構成データを動的に更新する方法について説明します
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 076ab0bb7dbc85a31b626a24d977e6fea558143e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636540"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>チュートリアル:Java Spring アプリで動的構成を使用する

App Configuration には、Spring  用のライブラリが 2 つあります。 `spring-cloud-azure-appconfiguration-config` は Spring Boot を必要とし、`spring-cloud-context` に依存します。 `spring-cloud-azure-appconfiguration-config-web` は Spring Boot と共に Spring Web を必要とします。 どちらのライブラリも、更新された構成値をチェックするための手動トリガーをサポートしています。 `spring-cloud-azure-appconfiguration-config-web` によって、構成の更新の自動チェックに対するサポートも追加されます。

更新により、アプリケーションを再起動することなく構成値を更新できますが、`@RefreshScope` のすべての Bean が再作成されることになります。 クライアント ライブラリは、現在読み込まれている構成のハッシュ ID をキャッシュすることで、構成ストアへの過剰な呼び出しが行われないようにします。 構成ストア内の値が変更されたとしても、キャッシュされた値の有効期限が切れるまでは、更新操作で値は更新されません。 各要求の既定の有効期限は 30 秒です。 その設定は、必要に応じてオーバーライドできます。

`spring-cloud-azure-appconfiguration-config-web` の自動更新は、アクティビティ (特に Spring Web の `ServletRequestHandledEvent`) に基づいてトリガーされます。 `ServletRequestHandledEvent` がトリガーされない場合は、キャッシュの有効期限が切れても、`spring-cloud-azure-appconfiguration-config-web` の自動更新によって更新がトリガーされることはありません。

## <a name="use-manual-refresh"></a>手動更新を使用する

App Configuration は `AppConfigurationRefresh` を公開します。これは、キャッシュの有効期限が切れているかどうかを確認し、有効期限が切れていれば更新をトリガーするために使用できます。

```java
import com.microsoft.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`AppConfigurationRefresh` の `refreshConfigurations()` は、更新がトリガーされている場合は true、そうでない場合は false である `Future` を返します。 False は、キャッシュの有効期限が切れていないか、変更がなかったか、または別のスレッドが現在更新をチェック中であることを意味します。

## <a name="use-automated-refresh"></a>自動更新を使用する

自動更新を使用するためには、App Configuration を使用する Spring Boot アプリから始めてみましょう。たとえば、[App Configuration 用の Spring Boot クイックスタート](quickstart-java-spring-app.md)で作成したアプリが利用できます。

テキスト エディターで *pom.xml* ファイルを開き、`spring-cloud-azure-appconfiguration-config-web` の `<dependency>` を追加します。

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>アプリをローカルで実行してテストする

1. 自分の Spring Boot アプリケーションを Maven でビルドし、実行します。

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. ブラウザー ウィンドウを開き、`http://localhost:8080` に移動します。  キーに関連付けられているメッセージが表示されます。

    *curl* を使用してアプリケーションをテストすることもできます。次に例を示します。 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. 動的構成をテストするには、アプリケーションに関連付けられている Azure App Configuration ポータルを開きます。 **[構成エクスプローラー]** を選択し、表示されたキーの値を更新します。次に例を示します。
    | Key | 値 |
    |---|---|
    | /application/config.message | Hello - Updated |

1. ブラウザー ページを最新の情報に更新し、新しいメッセージが表示されるのを確認します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、App Configuration から動的に構成設定を更新できるように Spring Boot アプリを設定しました。 App Configuration へのアクセスを効率化する Azure マネージド ID を使用する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)
