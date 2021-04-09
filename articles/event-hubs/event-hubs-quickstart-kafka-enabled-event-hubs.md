---
title: クイック スタート:Kafka プロトコルを使用した Azure Event Hubs によるデータ ストリーミング
description: クイック スタート:この記事では、Kafka のプロトコルおよび API を使用して、Azure Event Hubs にストリーム配信する方法に関する情報を示します。
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 2020534a3984453bcd6eff7ad0f5c02d9e7a29ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368351"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>クイック スタート:Kafka プロトコルを使用した Event Hubs によるデータ ストリーミング
このクイックスタートでは、プロトコル クライアントを変更したり独自のクラスターを実行したりせずに Event Hubs にストリーム配信する方法を紹介します。 プロデューサーとコンシューマーを使用して、アプリケーションの構成変更だけで Event Hubs と通信する方法を説明します。 

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) で入手できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次の前提条件を満たしている必要があります。

* [Apache Kafka 用の Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) に関する記事を読む。
* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
* [Java Development Kit (JDK) 1.7 以降](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* Maven バイナリ アーカイブの[ダウンロード](https://maven.apache.org/download.cgi)と[インストール](https://maven.apache.org/install.html)
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します
**Standard** レベルの Event Hubs 名前空間を作成すると、名前空間の Kafka エンドポイントが自動的に有効になります。 Kafka プロトコルを使用するアプリケーションから Standard レベルの Event Hubs にイベントをストリーム配信できます。 **Standard** レベルの Event Hubs 名前空間を作成するには、[Azure portal を使用したイベント ハブの作成](event-hubs-create.md)に関するページの手順に従います。 

> [!NOTE]
> Kafka 用の Event Hubs は、**Standard** および **Dedicated** レベルでのみ使用できます。 **Basic** レベルでは、Event Hubs 上の Kafka はサポートされません。

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Event Hubs で Kafka を使用してメッセージを送受信する

1. [Kafka 用 Azure Event Hubs リポジトリ](https://github.com/Azure/azure-event-hubs-for-kafka)を複製します。

2. `azure-event-hubs-for-kafka/quickstart/java/producer` に移動します。

3. `src/main/resources/producer.config` で次のようにプロデューサーの構成の詳細を更新します。

    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    
    > [!IMPORTANT]
    > `{YOUR.EVENTHUBS.CONNECTION.STRING}` を Event Hubs 名前空間への接続文字列に置き換えます。 接続文字列を取得する手順については、「[Event Hubs の接続文字列の取得](event-hubs-get-connection-string.md)」を参照してください。 たとえば、`sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";` のように構成します。

    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    サンプル ハンドラー クラス CustomAuthenticateCallbackHandler のソース コードは、GitHub の[こちら](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java)にあります。
4. プロデューサー コードを実行し、Event Hubs にイベントをストリーム配信します。
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. `azure-event-hubs-for-kafka/quickstart/java/consumer` に移動します。

6. `src/main/resources/consumer.config` で次のようにコンシューマーの構成の詳細を更新します。
   
    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    > [!IMPORTANT]
    > `{YOUR.EVENTHUBS.CONNECTION.STRING}` を Event Hubs 名前空間への接続文字列に置き換えます。 接続文字列を取得する手順については、「[Event Hubs の接続文字列の取得](event-hubs-get-connection-string.md)」を参照してください。 たとえば、`sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";` のように構成します。

    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    サンプル ハンドラー クラス CustomAuthenticateCallbackHandler のソース コードは、GitHub の[こちら](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java)にあります。

    Kafka 用 Event Hubs の OAuth サンプルはすべて[こちら](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)にあります。
7. Kafka クライアントを使用してイベント ハブからコンシューマー コードを実行し、イベントを処理します。

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Event Hubs Kafka クラスターにイベントがある場合は、コンシューマーからそれらの受信を開始します。

## <a name="next-steps"></a>次のステップ
この記事では、プロトコル クライアントを変更したり独自のクラスターを実行したりせずに Event Hubs にストリーム配信する方法を紹介しました。 詳細については、「[Azure Event Hubs のための Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)」を参照してください。