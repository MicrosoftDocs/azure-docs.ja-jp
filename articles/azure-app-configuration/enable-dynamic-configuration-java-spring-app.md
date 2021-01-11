---
title: Spring Boot アプリで動的な構成を使用する
titleSuffix: Azure App Configuration
description: Spring Boot アプリの構成データを動的に更新する方法について説明します
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.custom: devx-track-java
ms.author: lcozzens
ms.openlocfilehash: 653fcb6f6590fd503a97800ec8196025cf14a3b9
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121563"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>チュートリアル:Java Spring アプリで動的構成を使用する

App Configuration Spring Boot クライアント ライブラリでは、アプリケーションを再起動せずに、必要に応じて一連の構成設定を更新できます。 クライアント ライブラリでは、構成ストアへの呼び出しが多くなりすぎないようにするため、個々の設定がキャッシュされます。 構成ストア内の値が変更されたとしても、キャッシュされた値の有効期限が切れるまでは、更新操作で値は更新されません。 各要求の既定の有効期限は 30 秒です。 その設定は、必要に応じてオーバーライドできます。

`AppConfigurationRefresh` の `refreshConfigurations()` メソッドを呼び出すことで、更新された設定をオンデマンドでチェックすることができます。

または、`spring-web` に依存する `spring-cloud-azure-appconfiguration-config-web` パッケージを使用して自動更新を処理することもできます。

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
