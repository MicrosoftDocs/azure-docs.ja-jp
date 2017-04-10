---
title: "Visual Studio での Azure コードの最適化 | Microsoft Docs"
description: "Visual Studio の Azure コード最適化ツールにより、コードの堅牢性とパフォーマンスを向上させる方法について説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: ed48ee06-e2d2-4322-af22-07200fb16987
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 01623fa76175091439d5a571fb8b8f96aee01c4c
ms.openlocfilehash: a07496a2d5eb52dd3c4092105e45b70b6e9f69cd


---
# <a name="optimizing-your-azure-code"></a>Azure コードの最適化
Microsoft Azure を使用するアプリケーションをプログラミングする場合、クラウド環境でのアプリケーションのスケーラビリティ、動作、パフォーマンスに関する問題の回避に役立つコーディングの手法がいくつかあります。 Microsoft では、このような一般的に発生する問題の一部を認識および特定し、その解決を支援する Azure コード分析ツールを提供しています。 このツールは、NuGet を使用して Visual Studio でダウンロードできます。

## <a name="azure-code-analysis-rules"></a>Azure コード分析規則
Azure コード分析ツールでは、パフォーマンスに影響する既知の問題を検出したときに、以下の規則を使用して Azure コードに自動的にフラグを設定します。 検出された問題は警告またはコンパイラ エラーとして表示されます。 多くの場合、警告またはエラーを解決するためのコード修正や提案事項は電球アイコンで示されます。

## <a name="avoid-using-default-in-process-session-state-mode"></a>既定 (インプロセス) のセッション状態モードを使用しない
### <a name="id"></a>ID
AP0000

### <a name="description"></a>Description
クラウド アプリケーションの既定 (インプロセス) のセッション状態モードを使用すると、セッション状態が失われる可能性があります。

[Azure コード分析のフィードバック](http://go.microsoft.com/fwlink/?LinkId=403771)のページでアイデアやフィードバックを共有してください。

### <a name="reason"></a>理由
既定では、web.config ファイルで指定されているセッション状態モードはインプロセスです。 また、構成ファイルに指定されたエントリがない場合、セッション状態モードは既定でインプロセスに設定されます。 インプロセス モードでは、Web サーバー上のメモリにセッション状態が格納されます。 インスタンスが再起動されたときや、負荷分散またはフェールオーバーのサポートに新しいインスタンスが使用されたときに、Web サーバー上のメモリに格納されているセッション状態は保存されません。 この場合、クラウドでアプリケーションのスケーラビリティを確保できなくなります。

ASP.NET セッション状態では、セッション状態データのさまざまなストレージ オプション (InProc、StateServer、SQLServer、Custom、Off) がサポートされます。 [Redis の Azure セッション状態プロバイダー](http://go.microsoft.com/fwlink/?LinkId=401521)など、外部のセッション状態ストアでデータをホストするときは Custom モードを使用することをお勧めします。

### <a name="solution"></a>解決策
推奨されるソリューションの 1 つとして、Managed Cache Service でセッション状態を保存します。 [Redis の Azure セッション状態プロバイダー](http://go.microsoft.com/fwlink/?LinkId=401521) を使用してセッション状態を保存する方法を確認します。 また、クラウドでアプリケーションのスケーラビリティを確保するために、他の場所にセッション状態を保存することもできます。 代替ソリューションの詳細については、「 [セッション状態モード](https://msdn.microsoft.com/library/ms178586)」をご覧ください。

## <a name="run-method-should-not-be-async"></a>Run メソッドを非同期にしない
### <a name="id"></a>ID
AP1000

### <a name="description"></a>説明
非同期メソッド ([await](https://msdn.microsoft.com/library/hh156528.aspx) など) を [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) メソッドの外に作成し、[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) から非同期メソッドを呼び出します。 [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) メソッドを非同期として宣言すると、worker ロールが再起動ループに入ります。

[Azure コード分析のフィードバック](http://go.microsoft.com/fwlink/?LinkId=403771)のページでアイデアやフィードバックを共有してください。

### <a name="reason"></a>理由
[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) メソッド内で非同期メソッドを呼び出すと、クラウド サービスのランタイムが worker ロールを再利用します。 worker ロールが起動すると、プログラムのすべての実行が [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) メソッド内で行われます。 [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) メソッドを終了すると、worker ロールが再起動します。 worker ロールのランタイムは、非同期メソッドを検出すると、非同期メソッドの後のすべての操作をディスパッチしてから制御を戻します。 これにより、worker ロールは [[[[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) メソッドから抜けて再起動します。 実行の次の反復では、worker ロールが非同期メソッドを再度検出して再起動することで、worker ロールももう一度再利用されます。

### <a name="solution"></a>解決策
すべての非同期操作を [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) メソッドの外に配置します。 次に、 [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) メソッド内からリファクターされた非同期メソッド (RunAsync().wait など) を呼び出します。 Azure コード分析ツールにより、この問題を解決できます。

次のコード スニペットは、この問題のコード修正を示しています。

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Service Bus の Shared Access Signature 認証を使用する
### <a name="id"></a>ID
AP2000

### <a name="description"></a>Description
認証に Shared Access Signature (SAS) を使用します。 Service Bus の認証に、Access Control Service (ACS) は使用されなくなります。

[Azure コード分析のフィードバック](http://go.microsoft.com/fwlink/?LinkId=403771)のページでアイデアやフィードバックを共有してください。

### <a name="reason"></a>理由
セキュリティを強化するために、Azure Active Directory では、ACS 認証に代わって SAS 認証が使用されるようになります。 移行計画については、「 [Azure Active Directory is the future of ACS (Azure Active Directory が ACS の将来)](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) 」をご覧ください。

### <a name="solution"></a>解決策
アプリで SAS 認証を使用します。 次の例は、既存の SAS トークンを使用して Service Bus の名前空間またはエンティティにアクセスする方法を示しています。

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

詳細については、次のトピックを参照してください。

* 概要については、「 [Service Bus での共有アクセス署名認証](https://msdn.microsoft.com/library/dn170477.aspx)
* [Service Bus での共有アクセス署名認証の使用方法](https://msdn.microsoft.com/library/dn205161.aspx)
* サンプル プロジェクトについては、「 [Using Shared Access Signature (SAS) authentication with Service Bus Subscriptions (Service Bus サブスクリプションでの Shared Access Signature (SAS) 認証の使用)](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>"受信ループ" を回避するために OnMessage メソッドの使用を検討する
### <a name="id"></a>ID
AP2002

### <a name="description"></a>説明
"受信ループ" に入らないようにするには、メッセージを受信するために **Receive** メソッドを呼び出すよりも、**OnMessage** メソッドを呼び出す方がソリューションとして優れています。 ただし、 **Receive** メソッドを使用する必要があり、既定値以外のサーバー待機時間を指定した場合は、サーバー待機時間が 1 分を超えていることを確認します。

[Azure コード分析のフィードバック](http://go.microsoft.com/fwlink/?LinkId=403771)のページでアイデアやフィードバックを共有してください。

### <a name="reason"></a>理由
**OnMessage**を呼び出すと、クライアントは、キューまたはサブスクリプションを常にポーリングする内部メッセージ ポンプを起動します。 このメッセージ ポンプには、メッセージを受信する呼び出しを発行する無限ループが含まれています。 呼び出しがタイムアウトすると、新しい呼び出しが発行されます。 タイムアウト間隔は、使用されている [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx) の [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) プロパティの値によって決まります。

**Receive** に比べ、**OnMessage** を使用する利点は、メッセージのポーリング、例外の処理、複数のメッセージの並列処理、メッセージの完了をユーザーが手動で行う必要がないことです。

既定値を使用せずに **Receive** を呼び出す場合は、必ず *ServerWaitTime* 値が 1 分を超えるようにしてください。 *ServerWaitTime* を 1 分を超える値に設定すると、メッセージが完全に受信されるまでサーバーはタイムアウトしなくなります。

### <a name="solution"></a>解決策
推奨される使用法については、以下のコード例をご覧ください。 詳細については、[QueueClient.OnMessage メソッド (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx) および [QueueClient.Receive メソッド (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx) に関するページをご覧ください。

Azure メッセージング インフラストラクチャのパフォーマンスを向上させるには、設計パターンの「 [Asynchronous Messaging Primer (非同期メッセージングの基本)](https://msdn.microsoft.com/library/dn589781.aspx)」をご覧ください。

**OnMessage** を使用してメッセージを受信する例を次に示します。

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

既定のサーバー待機時間で **Receive** を使用する例を次に示します。

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

既定値以外のサーバー待機時間を指定して **Receive** を使用する例を次に示します。

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Service Bus の非同期メソッドの使用を検討する
### <a name="id"></a>ID
AP2003

### <a name="description"></a>Description
仲介型メッセージングでパフォーマンスを向上させるには、Service Bus の非同期メソッドを使用します。

[Azure コード分析のフィードバック](http://go.microsoft.com/fwlink/?LinkId=403771)のページでアイデアやフィードバックを共有してください。

### <a name="reason"></a>理由
各呼び出しの実行時にメイン スレッドはブロックされないため、非同期メソッドを使用することで、アプリケーション プログラムの同時実行が可能になります。 Service Bus メッセージング メソッドを使用すると、操作 (送信、受信、削除など) の実行に時間がかかります。 この時間には、要求と応答の待機時間だけでなく、Service Bus サービスによる操作の処理時間も含まれます。 時間あたりの操作数を増やすには、操作を同時に実行する必要があります。 詳細については、「[サービス バスの仲介型メッセージングを使用するパフォーマンス改善のベスト プラクティス](https://msdn.microsoft.com/library/azure/hh528527.aspx)」をご覧ください。

### <a name="solution"></a>解決策
推奨される非同期メソッドの使用方法については、 [QueueClient クラス (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) に関するページをご覧ください。

Azure メッセージング インフラストラクチャのパフォーマンスを向上させるには、設計パターンの「 [Asynchronous Messaging Primer (非同期メッセージングの基本)](https://msdn.microsoft.com/library/dn589781.aspx)」をご覧ください。

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Service Bus のキューとトピックのパーティション分割を検討する
### <a name="id"></a>ID
AP2004

### <a name="description"></a>Description
Service Bus メッセージングでパフォーマンスを向上させるには、Service Bus のキューとトピックをパーティション分割します。

[Azure コード分析のフィードバック](http://go.microsoft.com/fwlink/?LinkId=403771)のページでアイデアやフィードバックを共有してください。

### <a name="reason"></a>理由
Service Bus のキューとトピックをパーティション分割すると、パフォーマンスのスループットとサービスの可用性が向上します。これは、パーティション分割されたキューまたはトピックの全体的なスループットが、1 つのメッセージ ブローカーまたはメッセージング ストアのパフォーマンスによって制限されなくなったためです。 また、メッセージング ストアが一時的に停止しても、パーティション分割されたキューまたはトピックが使用できなくなることはありません。 詳細については、「[メッセージング エンティティのパーティション分割](https://msdn.microsoft.com/library/azure/dn520246.aspx)」を参照してください。

### <a name="solution"></a>解決策
次のコード スニペットは、メッセージング エンティティをパーティション分割する方法を示しています。

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

詳細については、「[Partitioned Service Bus Queues and Topics (Service Bus のパーティション分割されたキューとトピック)](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/)」(Microsoft Azure のブログ) をご覧ください。また、[Microsoft Azure Service Bus のパーティション分割されたキュー](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f)のサンプルもご覧ください。

## <a name="do-not-set-sharedaccessstarttime"></a>SharedAccessStartTime を設定しない
### <a name="id"></a>ID
AP3001

### <a name="description"></a>Description
共有アクセス ポリシーを即座に開始するために、現在の時刻に設定された SharedAccessStartTime を使用しないようにします。 このプロパティを設定する必要があるのは、共有アクセス ポリシーを後で開始する場合だけです。

[Azure コード分析のフィードバック](http://go.microsoft.com/fwlink/?LinkId=403771)のページでアイデアやフィードバックを共有してください。

### <a name="reason"></a>理由
クロックの同期によって、データセンター間でわずかな時間差が生じます。 たとえば、論理的に考えると、DateTime.Now などのメソッドを使用して、ストレージの SAS ポリシーの開始時刻を現在の時刻として設定した場合、SAS ポリシーが即座に有効になることになります。 ただし、データセンター間のわずかな時間差によって、時刻が開始時刻よりもわずかに遅れているデータセンターもあれば、進んでいるデータセンターもあるため、問題が発生する場合があります。 結果として、SAS ポリシーの有効期間の設定が短すぎると、短時間で (または即座に) 有効期限が切れる可能性があります。

Azure Storage での Shared Access Signature の使用方法に関する詳しいガイダンスについては、「 [Introducing Table SAS (Shared Access Signature), Queue SAS and update to Blob SAS (テーブル SAS (Shared Access Signature)、キュー SAS、および BLOB SAS の更新の概要)](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)」(MSDN Blogs - Microsoft Azure Storage Team Blog) をご覧ください。

### <a name="solution"></a>解決策
共有アクセス ポリシーの開始時刻を設定するステートメントを削除します。 Azure コード分析ツールにより、この問題を解決できます。 セキュリティ管理の詳細については、設計パターンの「 [Valet Key Pattern (バレット キー パターン)](https://msdn.microsoft.com/library/dn568102.aspx)」をご覧ください。

次のコード スニペットは、この問題のコード修正を示しています。

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>共有アクセス ポリシーの有効期限は 5 分より長くする必要がある
### <a name="id"></a>ID
AP3002

### <a name="description"></a>Description
"時刻のずれ" と呼ばれる状態により、さまざまな場所にあるデータセンター間でクロックの時間差が 5 分になる場合があります。 SAS ポリシー トークンが予定よりも早く期限切れになるのを防ぐには、5 分より長い有効期限を設定します。

[Azure コード分析のフィードバック](http://go.microsoft.com/fwlink/?LinkId=403771)のページでアイデアやフィードバックを共有してください。

### <a name="reason"></a>理由
世界中のさまざまな場所にあるデータセンターは、クロック信号によって同期されます。 さまざまな場所へのクロック信号の伝達に時間がかかるため、すべてが同期されていると思われていても、地理的に異なる場所にあるデータセンター間で時間の差異が生じている可能性があります。 この時間差は、共有アクセス ポリシーの開始時刻と有効期限の間隔に影響する場合があります。 したがって、共有アクセス ポリシーが即座に有効になるようにするには、開始時刻を指定しないでください。 また、早期タイムアウトを防ぐために、有効期限が 5 分を超えていることを確認します。

Azure Storage での Shared Access Signature の使用方法の詳細については、「 [Introducing Table SAS (Shared Access Signature), Queue SAS and update to Blob SAS (テーブル SAS (Shared Access Signature)、キュー SAS、および BLOB SAS の更新の概要)](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)」(MSDN Blogs - Microsoft Azure Storage Team Blog) をご覧ください。

### <a name="solution"></a>解決策
セキュリティ管理の詳細については、設計パターンの「 [Valet Key Pattern (バレット キー パターン)](https://msdn.microsoft.com/library/dn568102.aspx)」をご覧ください。

共有アクセス ポリシーの開始時刻を指定しない場合の例を次に示します。

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

ポリシーの有効期間が 5 分より長い共有アクセス ポリシーの開始時刻を指定する例を次に示します。

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

詳細については、「 [共有アクセス署名の作成と使用](https://msdn.microsoft.com/library/azure/jj721951.aspx)」をご覧ください。

## <a name="use-cloudconfigurationmanager"></a>CloudConfigurationManager を使用する
### <a name="id"></a>ID
AP4000

### <a name="description"></a>説明
Azure Web サイトや Azure Mobile Services などのプロジェクトで [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) クラスを使用すると、実行時の問題が発生しなくなります。 ただし、すべての Azure クラウド アプリケーションの構成管理の統一された方法として、Cloud[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) を使用することをお勧めします。

[Azure コード分析のフィードバック](http://go.microsoft.com/fwlink/?LinkId=403771)のページでアイデアやフィードバックを共有してください。

### <a name="reason"></a>理由
CloudConfigurationManager は、アプリケーション環境に適した構成ファイルを読み取ります。

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>解決策
[CloudConfigurationManager クラス](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)を使用するようにコードをリファクターします。 この問題のコード修正は、Azure コード分析ツールによって提供されます。

次のコード スニペットは、この問題のコード修正を示しています。 Replace

`var settings = ConfigurationManager.AppSettings["mySettings"];`

を以下に置き換えることができます。

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

App.config ファイルまたは Web.config ファイルに構成設定を保存する方法の例を次に示します。 設定は、構成ファイルの appSettings セクションに追加します。 前のコード例の Web.config ファイルを次に示します。

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>ハードコーディングされた接続文字列を使用しない
### <a name="id"></a>ID
AP4001

### <a name="description"></a>Description
ハードコーディングされた接続文字列を使用しており、それらを後で更新する必要がある場合、ソース コードを変更し、アプリケーションを再コンパイルする必要があります。 ただし、構成ファイルに接続文字列を保存すると、構成ファイルを更新するだけで、接続文字列を後で変更できます。

[Azure コード分析のフィードバック](http://go.microsoft.com/fwlink/?LinkId=403771)のページでアイデアやフィードバックを共有してください。

### <a name="reason"></a>理由
接続文字列をすばやく変更する必要があるときに問題が生じるため、接続文字列のハードコーディングは望ましくありません。 また、プロジェクトをソース管理にチェックインする必要がある場合、ソース コードで文字列が表示される可能性があるため、ハードコーディングされた接続文字列ではセキュリティの脆弱性が生じます。

### <a name="solution"></a>解決策
構成ファイルまたは Azure 環境に接続文字列を保存します。

* スタンドアロン アプリケーションでは、app.config を使用して接続文字列の設定を保存します。
* IIS がホストする Web アプリケーションでは、web.config を使用して接続文字列を保存します。
* ASP.NET vNext アプリケーションでは、configuration.json を使用して接続文字列を保存します。

web.config や app.config などの構成ファイルの使用方法については、「[ASP.NET Web 構成のガイドライン](https://msdn.microsoft.com/library/vstudio/ff400235\(v=vs.100\).aspx)」をご覧ください。 Azure の環境変数の機能については、「 [Microsoft Azure Web Sites: How Application Strings and Connection Strings Work (Microsoft Azure Web サイト: アプリケーション文字列と接続文字列の機能)](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)」をご覧ください。 ソース管理での接続文字列の保存については、 [ソース コード リポジトリに保存されているファイルに接続文字列などの機密情報を含めないようにする](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)ことに関する記事をご覧ください。

## <a name="use-diagnostics-configuration-file"></a>診断構成ファイルを使用する
### <a name="id"></a>ID
AP5000

### <a name="description"></a>Description
Microsoft.WindowsAzure.Diagnostics プログラミング API を使用するなどして、コードで診断設定を構成するのではなく、diagnostics.wadcfg ファイル  (Azure SDK 2.5 を使用する場合は diagnostics.wadcfgx) で診断設定を構成します。 これにより、コードを再コンパイルしなくても診断設定を変更できます。

[Azure コード分析のフィードバック](http://go.microsoft.com/fwlink/?LinkId=403771)のページでアイデアやフィードバックを共有してください。

### <a name="reason"></a>理由
Azure SDK 2.5 (Azure 診断 1.3 を使用) より前では、Azure 診断 (WAD) を複数の方法 (ストレージの構成 BLOB への追加、命令型コード、宣言型の構成、または既定の構成の使用) で構成できました。 ただし、推奨される診断構成方法は、アプリケーション プロジェクトで XML 構成ファイル (diagnostics.wadcfg、または SDK 2.5 以降では diagnositcs.wadcfgx) を使用する方法です。 この方法では、diagnostics.wadcfg ファイルで構成をすべて定義し、このファイルを自由に更新して再デプロイできます。 diagnostics.wadcfg 構成ファイルを使用する方法と、[DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx) クラスまたは [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx) クラスを使用したプログラムによる構成設定の方法を混在させると、混乱を招く可能性があります。 詳細については、「 [Azure 診断構成の初期化または変更](https://msdn.microsoft.com/library/azure/hh411537.aspx) 」をご覧ください。

WAD 1.3 (Azure SDK 2.5 に付属) 以降では、コードを使用して診断を構成することはできなくなりました。 そのため、構成を提供できるのは診断拡張機能の適用時または更新時だけとなります。

### <a name="solution"></a>解決策
診断構成デザイナーを使用して、診断設定を診断構成ファイル (diagnositcs.wadcfg、または SDK 2.5 以降では diagnositcs.wadcfgx) に移動します。 また、 [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) をインストールし、最新の診断機能を使用することをお勧めします。

1. 構成するロールのショートカット メニューで [プロパティ] をクリックし、[構成] タブをクリックします。
2. **[診断]** セクションで、**[診断の有効化]** チェック ボックスがオンになっていることを確認します。
3. **[構成]** をクリックします。

   ![[診断の有効化] オプションへのアクセス](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

   詳細については、「 [Azure Cloud Services および Virtual Machines 用の診断の構成](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) 」をご覧ください。

## <a name="avoid-declaring-dbcontext-objects-as-static"></a>DbContext オブジェクトを静的として宣言しない
### <a name="id"></a>ID
AP6000

### <a name="description"></a>Description
メモリを節約するために、DBContext オブジェクトを静的として宣言しないようにします。

[Azure コード分析のフィードバック](http://go.microsoft.com/fwlink/?LinkId=403771)のページでアイデアやフィードバックを共有してください。

### <a name="reason"></a>理由
DBContext オブジェクトでは、各呼び出しのクエリ結果が保持されます。 静的 DBContext オブジェクトは、アプリケーション ドメインがアンロードされるまで破棄されません。 そのため、静的 DBContext オブジェクトが大量のメモリを消費する可能性があります。

### <a name="solution"></a>解決策
DBContext をローカル変数または非静的インスタンス フィールドとして宣言し、これをタスクで使用して、使用後に破棄されるようにします。

次の MVC コントローラー クラスの例は、DBContext オブジェクトの使用方法を示しています。

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>次のステップ
Azure アプリケーションの最適化とトラブルシューティングの詳細については、「[Visual Studio を使用した Azure App Service のトラブルシューティング](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)」をご覧ください。



<!--HONumber=Dec16_HO2-->


