---
title: Java を使用してイベントを送信する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs にイベントを送信する Java アプリケーションの作成に関するチュートリアルを提供します。
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 4da89e0f99832c429091e0a028fafd8943df811a
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55300117"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Java を使用して Azure Event Hubs にイベントを送信する

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このチュートリアルでは、Java で記述されたコンソール アプリケーションを使用して、イベント ハブへのイベントを送信する方法について説明します。 

> [!NOTE]
> このクイック スタートをサンプルとして [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) からダウンロードし、`EventHubConnectionString` と `EventHubName` の文字列を対象のイベント ハブの値に置き換え、実行します。 または、このチュートリアルの手順に従って独自のものを作成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

* Java 開発環境。 このチュートリアルでは、[Eclipse](https://www.eclipse.org/) を使用します。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 名前空間とイベント ハブを作成する
最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順に従います。

次の記事の手順に従って、イベント ハブ用のアクセス キーの値を取得します:[接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 このチュートリアルの後半で記述するコードで、このアクセス キーを使用します。 既定のキー名は次のとおりです:**RootManageSharedAccessKey**。

このチュートリアルでは、以下の手順に進みます。

## <a name="add-reference-to-azure-event-hubs-library"></a>Azure Event Hubs ライブラリへの参照を追加する

[Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) の Maven プロジェクトで Event Hub 用の Java クライアント ライブラリを使用できます。 Maven プロジェクト ファイル内で次の依存関係宣言を使用して、このライブラリを参照できます。

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

ビルド環境の種類に応じて、[Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) から最新リリースの JAR ファイルを明示的に取得できます。  

単純なイベント パブリッシャーの場合は、Event Hubs クライアント クラス用の *com.microsoft.azure.eventhubs* パッケージと、Azure Service Bus メッセージング クライアントと共有される一般的な例外などのユーティリティ クラス用の *com.microsoft.azure.servicebus* パッケージをインポートします。 

## <a name="write-code-to-send-messages-to-the-event-hub"></a>イベント ハブにメッセージを送信するコードの記述

次のサンプルでは、最初に、好みの Java 開発環境でコンソール/シェル アプリケーション用の新しい Maven プロジェクトを作成します。 `SimpleSend` という名前のクラスを追加し、このクラスに次のコードを追加します。

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>接続文字列を作成する

ConnectionStringBuilder クラスを使用して、Event Hubs クライアント インスタンスに渡す接続文字列の値を作成します。 プレースホルダーは、名前空間とイベント ハブの作成時に取得した値に置き換えます。

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("speventhubns") 
                .setEventHubName("speventhub")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("2+WMsyyy1XmUtEnRsfOmTTyGasfJgsVjGAOIN20J1Y8=");
```

### <a name="send-events"></a>送信イベント

文字列を UTF-8 バイト エンコードに変換して単数のイベントを作成します。 その後、接続文字列から新しい Event Hubs クライアント インスタンスを作成し、メッセージを送信します。   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/SSL connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

プログラムをビルドして実行し、エラーがないことを確認します。

お疲れさまでした。 メッセージをイベント ハブに送信しました。

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>付録:EventHub パーティションへのメッセージのルーティング動作

コンシューマーがメッセージを取得するためには、あらかじめパブリッシャーがそのメッセージをパーティションに発行する必要があります。 com.microsoft.azure.eventhubs.EventHubClient オブジェクトの sendSync() メソッドを使ってイベント ハブに同期的にメッセージを発行するとき、パーティション キーが指定されているかどうかに応じて、そのメッセージは特定のパーティションに送信されるか、または利用可能なすべてのパーティションにラウンド ロビン方式で分散されます。

パーティション キーを表す文字列が指定されている場合、そのキーをハッシュすることによって、イベントの送信先となるパーティションが決定されます。

パーティション キーが設定されていない場合は、利用可能なすべてのパーティションに対してラウンド ロビン式にメッセージが分散されます

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Java を使用してメッセージをイベント ハブに送信しました。 Java を使用してイベント ハブからイベントを受信する方法については、[Java を使用してイベント ハブからイベントを受信する方法](event-hubs-java-get-started-receive-eph.md)に関するページを参照してください。

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

