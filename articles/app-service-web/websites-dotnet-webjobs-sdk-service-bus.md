---
title: "Web ジョブ SDK で Azure Service Bus を使用する方法"
description: "Web ジョブ SDK を使用して Azure Service Bus キューとトピックを操作する方法について説明します。"
services: app-service\web, service-bus
documentationcenter: .net
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: 2114a934-135b-42b8-871c-6cc040214e76
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/01/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: e44199dec6de1e89545f3d122481aa81e97d75ac
ms.lasthandoff: 03/15/2017


---
# <a name="how-to-use-azure-service-bus-with-the-webjobs-sdk"></a>Web ジョブ SDK で Azure Service Bus を使用する方法
## <a name="overview"></a>概要
このガイドでは、Azure Service Bus メッセージを受信したときにプロセスをトリガーする方法を示す C# コード サンプルについて説明します。 コード サンプルでは [WebJobs SDK](websites-dotnet-webjobs-sdk.md) Version 1.x を使用しています。

このガイドは、 [Visual Studio でストレージ アカウントを指定する接続文字列を使って Web ジョブ プロジェクトを作成する方法](websites-dotnet-webjobs-sdk-get-started.md)を理解していることを前提としています。

コード スニペットは、この例のように `JobHost` オブジェクトを作成するコードではなく、関数のみを示しています。

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

[Service Bus の完全なコード例](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs) は、GitHub.com の azure-webjobs-sdk-samples リポジトリにあります。

## <a id="prerequisites"></a> 前提条件
Service Bus を使用するには、他の WebJobs SDK パッケージに加えて、 [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) NuGet パッケージをインストールする必要があります。 

また、ストレージ接続文字列に加えて、AzureWebJobsServiceBus 接続文字列を設定する必要もあります。  次の例で示すように App.config ファイルの `connectionStrings` セクションでこれを行うことができます。

        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
        </connectionStrings>

App.config ファイルに Service Bus の接続文字列を含むサンプル プロジェクトについては、「 [Service Bus の使用例](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus)」を参照してください。 

接続文字列は Azure 実行時環境でも設定できます。Azure で Web ジョブを実行した場合、Azure 実行時環境の設定は App.config 設定よりも優先されます。詳細については、[Azure Web ジョブ SDK の概要](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account)に関するページを参照してください。

## <a id="trigger"></a> Service Bus キュー メッセージを受信したときに関数をトリガーする方法
キュー メッセージを受信したときに WebJobs SDK が呼び出す関数を記述するには、 `ServiceBusTrigger` 属性を使用します。 この属性コンストラクターは、ポーリングするキューの名前を指定するパラメーターを受け取ります。

### <a name="how-servicebustrigger-works"></a>ServiceBusTrigger のしくみ
この SDK では、`PeekLock` モードでメッセージを受信して、関数が正常に終了した場合はメッセージの `Complete` を呼び出し、関数が失敗した場合は、`Abandon` を呼び出します。 関数の実行時間が `PeekLock` タイムアウトよりも長くなると、ロックが自動的に更新されます。

Service Bus には、WebJobs SDK で制御または構成できない有害キュー メッセージ処理があります。 

### <a name="string-queue-message"></a>文字列のキュー メッセージ
以下のコード サンプルでは、文字列を含むキュー メッセージを読み取り、Web ジョブ SDK ダッシュボードにその文字列を書き込みます。

        public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
            TextWriter logger)
        {
            logger.WriteLine(message);
        }

**注:** WebJobs SDK を使用しないアプリケーションでキュー メッセージを作成する場合は、 [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) を必ず "text/plain" に設定してください。

### <a name="poco-queue-message"></a>POCO キュー メッセージ
この SDK では、POCO ( [Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 型の JSON を含むキュー メッセージを自動的に逆シリアル化します。 以下のコード サンプルでは、`BlobName` プロパティを持つ `BlobInformation` オブジェクトを含むキュー メッセージを読み取ります。

        public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
            TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

POCO のプロパティを使って同じ関数の BLOB やテーブルを操作する方法を示すコード サンプルについては、 [この記事のストレージ キュー バージョン](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs)に関するセクションを参照してください。

キュー メッセージを作成するコードで WebJobs SDK を使用しない場合は、次の例に示すようなコードを使用してください。

        var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
        BlobInformation blobInformation = new BlobInformation () ;
        var message = new BrokeredMessage(blobInformation);
        client.Send(message);

### <a name="types-servicebustrigger-works-with"></a>ServiceBusTrigger で使用できる型
`string` と POCO の型に加えて、バイト配列か `BrokeredMessage` オブジェクトで `ServiceBusTrigger` 属性を使用できます。

## <a id="create"></a> Service Bus キュー メッセージの作成方法
新しいキュー メッセージを作成する関数を記述するには、 `ServiceBus` 属性を使用し、属性コンストラクターにキュー名を渡します。 

### <a name="create-a-single-queue-message-in-a-non-async-function"></a>非同期以外の関数での単一のキュー メッセージの作成
次のコード サンプルでは、出力パラメーターを使用し、"inputqueue" という名前のキューで受信したメッセージと同じ内容で、"outputqueue" という名前のキューに新しいメッセージを作成します。

        public static void CreateQueueMessage(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] out string outputQueueMessage)
        {
            outputQueueMessage = queueMessage;
        }

単一のキュー メッセージを作成するための出力パラメーターは、以下のどの型でもかまいません。

* `string`
* `byte[]`
* `BrokeredMessage`
* ユーザーが定義するシリアル化可能な POCO 型。 JSON として自動的にシリアル化されます。

POCO 型のパラメーターの場合は、関数が終了すると、常にキュー メッセージが作成されます。パラメーターが null の場合は、メッセージを受信し、逆シリアル化すると null を返すキュー メッセージが作成されます。 他の型では、パラメーターが null の場合、キュー メッセージは作成されません。

### <a name="create-multiple-queue-messages-or-in-async-functions"></a>(非同期関数での) 複数のキュー メッセージの作成
複数のメッセージを作成するには、以下のコード サンプルに示されているように、`ServiceBus` 属性に `ICollector<T>` または `IAsyncCollector<T>` を指定します。

        public static void CreateQueueMessages(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

`Add` メソッドが呼び出されると、すぐに各キュー メッセージが作成されます。

## <a id="topics"></a>Service Bus トピックの操作方法
Service Bus トピックにメッセージを受信したときに SDK が呼び出す関数を記述するには、以下のコード サンプルに示されているように、 `ServiceBusTrigger` 属性にトピック名とサブスクリプション名を受け取るコンストラクターを指定します。

        public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
            TextWriter logger)
        {
            logger.WriteLine("Topic message: " + message);
        }

トピックにメッセージを作成するには、キュー名の場合と同様に、 `ServiceBus` 属性にトピック名を指定します。

## <a name="features-added-in-release-11"></a>リリース 1.1 で追加された機能
1.1 リリースでは、次の機能が追加されました。

* `ServiceBusConfiguration.MessagingProvider`を使用して、メッセージ処理を詳細にカスタマイズできます。
* `MessagingProvider` は、Service Bus の `MessagingFactory` と `NamespaceManager` のカスタマイズをサポートします。
* `MessageProcessor` 戦略パターンでは、キュー/トピックごとにプロセッサを指定できます。
* メッセージ処理の同時実行が既定でサポートされています。 
* `ServiceBusConfiguration.MessageOptions` を使用して、`OnMessageOptions` を簡単にカスタマイズできます。
* `ServiceBusTriggerAttribute`/`ServiceBusAttribute` で、[AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71) を指定できます (管理権限がない場合)。 Azure Web ジョブは、管理 AccessRights がないと、存在しないキューやトピックを自動的にプロビジョニングできません。

## <a id="queues"></a>ストレージ キューのハウツー記事で紹介されている関連トピック
Service Bus 以外の WebJobs SDK シナリオについては、「 [WebJobs SDK を使用して Azure キュー ストレージを操作する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)」をご覧ください。 

その記事では、以下のようなトピックが紹介されています。

* Async 関数
* 複数のインスタンス
* グレースフル シャットダウン
* 関数本体での Web ジョブ SDK 属性の使用
* コード内での SDK 接続文字列の設定
* コードの Web ジョブ SDK コンストラクター パラメーター値の設定
* 手動での関数のトリガー
* ログの書き込み

## <a id="nextsteps"></a> 次のステップ
このガイドでは、Azure Service Bus を操作するための一般的なシナリオの処理方法を示すコードのサンプルを提供しました。 Azure Web ジョブ および Web ジョブ SDK の使用方法の詳細については、「 [Azure Web ジョブの推奨リソース](http://go.microsoft.com/fwlink/?linkid=390226)」を参照してください。


