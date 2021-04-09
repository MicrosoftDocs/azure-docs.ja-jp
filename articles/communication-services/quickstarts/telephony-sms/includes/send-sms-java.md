---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/12/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: 2739079b67d80f3e4a9f367aaa58f6dcbbb650ca
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622350"
---
Communication Services Java SMS クライアント ライブラリを使用して SMS メッセージを送信することによって、Azure Communication Services の使用を開始します。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java Development Kit (JDK)](/java/azure/jdk/) バージョン 8 以降。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- SMS が有効になっている電話番号。 [電話番号を取得します](../get-phone-number.md)。

### <a name="prerequisite-check"></a>前提条件のチェック

- ターミナルまたはコマンド ウィンドウで `mvn -v` を実行して、maven がインストールされていることを確認します。
- Communication Services リソースに関連付けられている電話番号を表示するには、[Azure portal](https://portal.azure.com/) にサインインし、Communication Services リソースを見つけて、左側のナビゲーション ペインから **[電話番号]** タブを開きます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-java-application"></a>新しい Java アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、Java アプリケーションを作成するディレクトリに移動します。 次のコマンドを実行して、maven-archetype-quickstart テンプレートから Java プロジェクトを生成します。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

"generate" 目標により、artifactId と同じ名前のディレクトリが作成されます。 このディレクトリの下の **src/main/java** ディレクトリにはプロジェクトのソース コードが、**src/test/java** ディレクトリにはテスト ソースがそれぞれ含まれており、**pom.xml** ファイルはプロジェクトのプロジェクト オブジェクト モデル (POM) です。

### <a name="install-the-package"></a>パッケージをインストールする

テキスト エディターで **pom.xml** ファイルを開きます。 依存関係のグループに、次の dependency 要素を追加します。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.4</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

`azure-core-http-netty` 依存関係を **pom.xml** ファイルに追加します。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.8.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core</artifactId>
    <version>1.13.0</version> <!-- {x-version-update;com.azure:azure-core;dependency} -->
</dependency>
```

テキスト エディターで **/src/main/java/com/communication/quickstart/App.java** を開き、import ディレクティブを追加して、`System.out.println("Hello world!");` ステートメントを削除します。

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>オブジェクト モデル

Java 用 Azure Communication Services SMS クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                                             | 説明                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | このクラスによって、SmsClient が作成されます。 これには、エンドポイント、資格情報、および http クライアントを指定します。 |
| SmsClient                    | このクラスは、すべての SMS 機能に必要となります。 これを使用して、SMS メッセージを送信します。                |
| SmsSendResult                | このクラスには、SMS サービスからの結果が含まれます。                                          |
| SmsSendOptions               | このクラスには、カスタム タグを追加し、配信レポートを構成するためのオプションが用意されています。 deliveryReportEnabled が true に設定されている場合、配信が成功したときにイベントが生成されます|                           |

## <a name="authenticate-the-client"></a>クライアントを認証する

接続文字列を使用して `SmsClient` をインスタンス化します。 (資格情報は Azure portal の `Key` です。 [リソースの接続文字列を管理する](../../create-communication-resource.md#store-your-connection-string)方法について確認してください。

`main` メソッドに次のコードを追加します。

```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .httpClient(httpClient)
                .buildClient();
```

`com.azure.core.http.HttpClient` インターフェイスを実装する任意のカスタム HTTP クライアントを使用して、クライアントを初期化できます。 上記のコードは、`azure-core` によって提供される [Azure Core Netty HTTP クライアント](/java/api/overview/azure/core-http-netty-readme)の使用方法を示しています。

エンドポイントとアクセス キーを指定する代わりに、connectionString() 関数を使用して接続文字列全体を指定することもできます。 
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .httpClient(httpClient)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>1:1 の SMS メッセージを送信する

1 人の受信者に SMS メッセージを送信するには、1 人の受信者の電話番号を使用して SmsClient から `send` メソッドを呼び出します。 また、オプションのパラメーターを渡して、配信レポートを有効にするかどうか、およびカスタム タグを設定するかどうかを指定することもできます。

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```
## <a name="send-a-1n-sms-message-with-options"></a>オプションを使用して 1:N の SMS メッセージを送信する
受信者の一覧に SMS メッセージを送信するには、受信者の電話番号の一覧を使用して `send` メソッドを呼び出します。 また、オプションのパラメーターを渡して、配信レポートを有効にするかどうか、およびカスタム タグを設定するかどうかを指定することもできます。
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.send(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE);

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

`<from-phone-number>` は Communication Services リソースに関連付けられている、SMS が有効になっている電話番号で置き換え、`<to-phone-number>` はメッセージの送信先の電話番号または電話番号の一覧で置き換える必要があります。

## <a name="optional-parameters"></a>省略可能のパラメーター

`deliveryReportEnabled` パラメーターは、配信レポートを構成するために使用できる省略可能なパラメーターです。 これは、SMS メッセージが配信されたときにイベントを生成する場合に便利です。 SMS メッセージの配信レポートを構成するには、[SMS イベントの処理](../handle-sms-events.md)に関するクイックスタートを参照してください。

`tag` パラメーターは、配信レポートにタグを適用するために使用できる省略可能なパラメーターです。

## <a name="run-the-code"></a>コードの実行

**pom.xml** ファイルが格納されているディレクトリに移動し、`mvn` コマンドを使用してプロジェクトをコンパイルします。

```console

mvn compile

```

次に、パッケージをビルドします。

```console

mvn package

```

次の `mvn` コマンドを実行して、アプリを実行します。

```console

mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false

```