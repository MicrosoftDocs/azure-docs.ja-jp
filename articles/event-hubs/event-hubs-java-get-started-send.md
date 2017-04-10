---
title: "Java を使用して Azure Event Hubs にイベントを送信する | Microsoft Docs"
description: "Java を使用して Event Hubs への送信を開始する"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: feb466064f2e26a329977240eeafb28148bdf212


---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Java を使用して Azure Event Hubs にイベントを送信する

## <a name="introduction"></a>はじめに
Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。 Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して転送と格納できます。

詳細については、「[Event Hubs の概要][Event Hubs overview]」をご覧ください。

このチュートリアルでは、Java のコンソール アプリケーションを使用して、Event Hub へのイベントを送信する方法について説明します。 Java のイベント プロセッサ ホスト ライブラリを使用してイベントを受信するには、[この記事](event-hubs-java-get-started-receive-eph.md)を参照するか、左側の目次で適切な受信側の言語をクリックしてください。

このチュートリアルを完了するには、以下が必要です。

* Java 開発環境。 このチュートリアルでは、 [Eclipse](https://www.eclipse.org/)を想定しています。
* アクティブな Azure アカウント。 <br/>アカウントがない場合は、無料アカウントを数分で作成することができます。 詳細については、「<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料試用版サイト</a>」を参照してください。

## <a name="send-messages-to-event-hubs"></a>Event Hub へのメッセージ送信
Event Hubs の Java クライアント ライブラリは、 [Maven セントラル リポジトリ](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)の Maven プロジェクトで利用でき、Maven プロジェクト ファイル内の以下の依存関係宣言を使用して参照できます。    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```

ビルド環境の種類に応じて、[Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) または [GitHub のリリース配付ポイント](https://github.com/Azure/azure-event-hubs/releases)から最新リリースの JAR ファイルを明示的に取得できます。  

単純なイベント パブリッシャーの場合は、Event Hubs クライアント クラス用の *com.microsoft.azure.eventhubs* パッケージと、Azure Service Bus メッセージング クライアントと共有される一般的な例外などのユーティリティ クラス用の *com.microsoft.azure.servicebus* パッケージをインポートします。 

次のサンプルでは、最初に、好みの Java 開発環境でコンソール/シェル アプリケーション用の新しい Maven プロジェクトを作成します。 このクラスを ```Send```と呼びます。     

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

名前空間と Event Hub の名前を、Event Hub の作成時に使用した値に置き換えます。

``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

次に、文字列を UTF-8 バイト エンコードに変換して単数のイベントを作成します。 その後、接続文字列から新しい Event Hubs クライアント インスタンスを作成し、メッセージを送信します。   

``` Java 

    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);

    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [EventProcessorHost を使用してイベントを受信する](event-hubs-java-get-started-receive-eph.md)
* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)


<!--HONumber=Feb17_HO1-->


