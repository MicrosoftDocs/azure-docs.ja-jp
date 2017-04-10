---
title: "Azure Web ジョブ SDK とは"
description: "Azure Web ジョブ SDK の紹介です。 この SDK の特徴、一般的な用途、コード サンプルについて説明します。"
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: 8281267b-572b-4b14-a328-6704493ea682
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2d21cd34427921ad789b4c95212c07caddd5a966
ms.lasthandoff: 11/17/2016


---
# <a name="what-is-the-azure-webjobs-sdk"></a>Azure Web ジョブ SDK とは
## <a id="overview"></a>概要
この記事では、Web ジョブ SDK の特徴を紹介すると共に、一般的な用途を確認し、コードでの使い方の概要を示します。

[Web ジョブ](websites-webjobs-resources.md) は、Web アプリ、API アプリ、またはモバイル アプリと同じコンテキストでプログラムやスクリプトを実行できる Azure App Service の機能です。 [Web ジョブ SDK](websites-webjobs-resources.md) の目的は、Web ジョブで実行できる一般的な作業 (画像処理、キュー処理、RSS 情報集約、ファイル管理、電子メールの送信など) を単純なコードで記述できるようにすることです。 WebJobs SDK には、Azure Storage や Service Bus の操作、タスクのスケジューリング、エラー処理など、一般的な用途に対応した各種の機能が組み込まれています。 また、拡張できるように設計されています。 [Web ジョブ SDK はオープン ソース](https://github.com/Azure/azure-webjobs-sdk/)であり、[拡張機能のオープン ソース リポジトリ](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)が用意されています。

Web ジョブ SDK には次のコンポーネントが含まれています。

* **NuGet パッケージ**。 Visual Studio コンソール アプリケーション プロジェクトに追加する NuGet パッケージは、Web ジョブ SDK の属性でメソッドを修飾することによって、コードで使用するフレームワークを提供します。
* **ダッシュボード**。 Web ジョブ SDK の一部は、Azure App Service に含まれており、NuGet パッケージを使用するプログラム向けに豊富な監視と診断の機能を提供します。 これらの監視および診断機能を使用するためにコードを記述する必要はありません。

## <a id="scenarios"></a>シナリオ
ここでは、Azure Web ジョブ SDK を使用してより簡単に処理できる標準的なシナリオをいくつか取り上げます。

* 画像処理または CPU 負荷の高い作業。 Web サイトの一般的な機能は、画像やビデオをアップロードする機能です。 アップロード後にコンテンツを操作する必要が生じ、その作業の間ユーザーを待たせたくない場合がよくあります。
* キューの処理。 Web フロントエンドがバックエンド サービスと通信する一般的な方法は、キューを使用することです。 Web サイトは、処理を完了する必要がある場合に、メッセージをキューにプッシュします。 バックエンド サービスは、キューからメッセージをプルし、処理を完了します。 イメージ処理には、キューを使用できます。 たとえば、ユーザーがいくつかのファイルをアップロードした後、それらをバックエンドで取り出して処理できるように、ファイル名をキュー メッセージに格納します。 また、キューを使用してサイトの応答性を改善できます。 たとえば、SQL データベースに直接書き込む代わりに、キューに書き込んで、完了したことをユーザーに通知します。その間、バックエンド サービスでは、待ち時間の長いリレーショナル データベースの作業を処理することができます。 画像処理でのキューの処理の例については、[Web ジョブ SDK の使用に関するチュートリアル](websites-dotnet-webjobs-sdk-get-started.md)を参照してください。
* RSS 情報集約。 RSS フィードのリストを維持するサイトがある場合は、フィードからのすべての記事をバックグラウンド プロセスにプルすることができます。
* ログ ファイルの集計やクリーンアップなどのファイルのメンテナンス。  分析ジョブを実行するために、いくつかのサイトによって作成されたログ ファイルや別々の期間に作成されたログ ファイルを組み合わせる作業が必要になる場合があります。 また、使用していないログ ファイルをクリーンアップするために毎週実行するタスクをスケジュール設定する場合もあります。
* Azure テーブルへの取り込み。 格納したファイルと BLOB を解析して、データをテーブルに格納したいと思っても、その取り込み用の関数を作るには、大量のコードの記述が必要になることがあります (場合によっては数百万行)。 Web ジョブ SDK を利用すれば、この機能を簡単に実装できます。 また、この SDK なら、テーブルに書き込まれた行の数など、進行状況インジケーターのリアルタイム監視も実現できます。
* [電子メールの送信](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure)など、バックグラウンド スレッドで実行したいその他の時間のかかるタスク。 
* 毎晩のバックアップ操作の実行など、スケジュールに従って実行するタスク。

これらのシナリオの多くでは、Web アプリを複数の VM で実行するように拡張することを検討できます (複数の WebJobs を同時に実行するなど)。 一部のシナリオでは、これにより同じデータが複数回処理されることになりますが、WebJobs SDK の組み込みのキュー、BLOB、および Service Bus のトリガーを使用した場合、これは問題にはなりません。 SDK では、関数は各メッセージまたは BLOB に対して 1 回のみ処理されます。

また、Web ジョブ SDK では、一般的なエラー処理シナリオを簡単に処理できます。 関数が失敗したときに通知を送信するようにアラートを設定できます。また、タイムアウトを設定して、指定した時間制限内に関数が完了しない場合に、その関数を自動的にキャンセルすることができます。

## <a id="code"></a> コード サンプル
Azure Storage と組み合わせて動作する標準的なタスクを処理するコードは、シンプルです。 コンソール アプリケーションの `Main` メソッドで、記述したメソッドへの呼び出しを調整する `JobHost` オブジェクトを作成します。 Web ジョブ SDK フレームワークは、メソッド内で使用した Web ジョブ SDK 属性に基づいて、そのメソッドを呼び出すタイミングと、使用するパラメーター値を認識します。 SDK には、関数が呼び出される条件を指定する "*トリガー*" と、メソッドのパラメーターに対して情報を入出力する方法を指定する "*バインダー*" が用意されています。

たとえば、 [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) 属性では、キューでメッセージを受信したときに関数が呼び出され、メッセージの形式がバイト配列またはカスタム型の JSON の場合、そのメッセージは自動的に逆シリアル化されます。 [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) 属性では、Azure ストレージ アカウントで新しい BLOB が作成されたときに常にプロセスがトリガーされます。

次に示すのは、キューをポーリングして、受信したキュー メッセージごとに BLOB を作成する単純なプログラムです。

        public static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

`JobHost` オブジェクトは、バックグラウンドの関数セット用のコンテナーです。 `JobHost` オブジェクトは、関数を監視し、それらをトリガーするイベントを待機して、トリガー イベントが発生した時点でその関数を実行します。 `JobHost` メソッドを呼び出すと、コンテナー プロセスを現在のスレッドまたはバックグラウンド スレッドのどちらで実行するかを指定できます。 例では、 `RunAndBlock` メソッドは、現在のスレッド上でプロセスを継続的に実行しています。

この例の `ProcessQueueMessage` メソッドには `QueueTrigger` 属性があるため、新規キュー メッセージの受信がその関数のトリガーとなります。 `JobHost` オブジェクトは指定されたキュー (このサンプルでは "webjobsqueue") で新規キュー メッセージを待機し、それを見つけた時点で、`ProcessQueueMessage` を呼び出します。 

`QueueTrigger` 属性は、`inputText` パラメーターをキュー メッセージの値にバインドします。 また、`Blob` 属性は、`TextWriter` オブジェクトを "containername" というコンテナー内の "blobname" という BLOB にバインドします。  

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)

その後、関数はこれらのパラメーターを使用して、キュー メッセージの値を BLOB に書き込みます。

        writer.WriteLine(inputText);

Web ジョブ SDK のトリガー機能とバインダー機能は、記述する必要のあるコードを大幅に簡素化します。 キュー、BLOB、ファイルの処理、またはスケジュールされたタスクの開始に必要な低レベルのコードは、Web ジョブ SDK フレームワークによって実行されます。 たとえば、このフレームワークは、まだ存在していないキューの作成、キューのオープン、キュー メッセージの読み取り、処理の完了時のキュー メッセージの削除、まだ存在しない BLOB コンテナーの作成、BLOB への書き込みなどを処理します。

次のコード例は、1 つの Web ジョブ内でさまざまなトリガー (`QueueTrigger`、`FileTrigger`、`WebHookTrigger`、`ErrorTrigger`) を示しています。 

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## <a id="schedule"></a> スケジュール設定
`TimerTrigger` 属性は、スケジュールに基づいて実行する関数をトリガーできます。 Azure を通じて Web ジョブ全体をスケジュールしたり、Web ジョブ SDK の `TimerTrigger`を使用して、Web ジョブの個々の関数をスケジュールしたりすることができます。 コード サンプルを次に示します。

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

その他のサンプル コードについては、GitHub.com の azure-webjobs-sdk-extensions リポジトリ内にある [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) を参照してください。

## <a name="extensibility"></a>機能拡張
Web ジョブ SDK を使用すると、組み込みの機能に制限されずに、カスタムのトリガーやバインダーを記述できるようになります。  たとえば、キャッシュ イベントや定期的なスケジュール向けのトリガーを記述できます。 [オープン ソースのリポジトリ](https://github.com/Azure/azure-webjobs-sdk-extensions)には、[Web ジョブ SDK の拡張に関する詳細なガイド](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)とサンプル コードが含まれており、独自のトリガーやバインダーの記述を始めるときに役立ちます。

## <a id="workerrole"></a>Web ジョブ以外での WebJobs SDK の使用
Web ジョブ SDK を使用するプログラムは標準的なコンソール アプリケーションであるため、場所を問わず実行できます。Web ジョブとして実行する必要はありません。 ローカルの開発用コンピューターでプログラムをテストし、運用段階では、好みに応じてクラウド サービスの worker ロールまたは Windows サービスで実行することができます。 

ただし、ダッシュボードは Azure App Service Web アプリの拡張機能としてのみ使用可能です。 Web ジョブ以外で実行する際にもダッシュボードを使いたい場合は、Web ジョブ SDK ダッシュボードの接続文字列が参照するストレージ アカウントを使用するように Web アプリを構成できます。そうすれば、その Web アプリの Web ジョブ ダッシュボードに、別の場所で実行されているプログラムから関数の実行に関するデータが表示されます。 ダッシュボードには、URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions を使用してアクセスできます。 詳細については、「[Getting a dashboard for local development with the WebJobs SDK (Web ジョブ SDK を使用したローカル開発用ダッシュボードへのアクセス)](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)」を参照してください。ただし、このブログ記事には、既に使用されていない接続文字列名が示されている点に注意してください。 

## <a id="nostorage"></a>ダッシュボードの機能
Web ジョブ SDK には、トリガーやバインダーを使用しない場合でも、次のようないくつかの利点があります。

* ダッシュボードから関数を呼び出すことができる。
* ダッシュボードから関数を再生することができる。
* ダッシュボードで、特定の Web ジョブ (Console.Out、Console.Error、トレースなどを使用して書き込まれる、アプリケーション ログ) にリンクされているか、それらを生成した特定の関数の呼び出しにリンクされているログを表示できます (SDK がパラメーターとして関数に渡す `TextWriter` オブジェクトを使用して書き込まれるログ)。 

詳細については、「[関数を手動でトリガーする方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual)」と「[ログを記述する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)」を参照してください。 

## <a id="nextsteps"></a>次のステップ
Web ジョブ SDK の詳細については、「 [Azure WebJobs Recommended Resources (Azure Web ジョブの推奨リソース)](http://go.microsoft.com/fwlink/?linkid=390226)」を参照してください。

Web ジョブ SDK の最新の機能強化については、「 [リリース ノート](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)」を参照してください。


