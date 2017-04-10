---
title: "Scheduler の概念、用語、およびエンティティ | Microsoft Docs"
description: "Azure Scheduler の概念、用語、およびエンティティ階層構造 (ジョブやジョブ コレクションなど)。  スケジュールされたジョブの全体をまとめた例を示します。"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 0f035b58ccd140a5481703df7e184206da2ed651


---
# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Scheduler の概念、用語集、エンティティ階層構造
## <a name="scheduler-entity-hierarchy"></a>Scheduler エンティティの階層構造
次の表では、Scheduler API で公開または使用される主要なリソースについて説明します。

| リソース | Description |
| --- | --- |
| **ジョブ コレクション** |ジョブ コレクションはジョブのグループを含み、コレクション内のジョブで共有される設定、クォータ、調整を保持します。 ジョブ コレクションは、サブスクリプションの所有者によって作成され、使用状況やアプリケーションの境界に基づいてジョブをグループ化します。 ジョブ コレクションは、1 つのリージョンに制限されます。 ジョブ コレクションでは、クォータを適用して、そのコレクション内のすべてのジョブの使用量を制限することもできます。 クォータには、MaxJobs と MaxRecurrence が含まれます。 |
| **ジョブ** |ジョブは、単純または複雑な実行方法による単一の反復的な操作を定義します。 操作には、HTTP、ストレージ キュー、Service Bus キュー、Service Bus トピックの要求が含まれます。 |
| **ジョブ履歴** |ジョブ履歴は、ジョブの実行の詳細を表します。 応答の詳細と、ジョブの実行結果 (成功または失敗) が含まれます。 |

## <a name="scheduler-entity-management"></a>Scheduler エンティティの管理
大まかに言うと、Scheduler およびサービス管理 API は、リソースに対する次の操作を公開します。

| 機能 | 説明と URI アドレス |
| --- | --- |
| **ジョブ コレクションの管理** |ジョブ コレクションとジョブ コレクション内のジョブを作成と変更するための GET、PUT、および DELETE のサポート。 ジョブ コレクションはジョブのコンテナーであり、クォータと共有設定にマップされます。 後で説明するクォータの例としては、ジョブの最大数や最小の繰り返し間隔があります。 <p>PUT および DELETE: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>GET: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p> |
| **ジョブの管理** |ジョブを作成と変更するための GET、PUT、POST、PATCH、および DELETE のサポート。 すべてのジョブは既に存在するジョブ コレクションに属する必要があるため、暗黙的な作成はありません。 <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p> |
| **ジョブ履歴の管理** |ジョブの経過時間やジョブの実行結果など、60 日分のジョブの実行履歴を取得するための GET のサポート。 状態およびステータスに基づいてフィルター処理を行うためのクエリ文字列パラメーターのサポートを追加します。 <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p> |

## <a name="job-types"></a>ジョブの種類
ジョブには、HTTP ジョブ (SSL をサポートする HTTPS ジョブを含む)、ストレージ キュー ジョブ、Service Bus キュー ジョブ、Service Bus トピック ジョブなど、さまざまな種類が存在します。 HTTP ジョブは、既存のワークロードやサービスのエンドポイントがある場合に最適です。 ストレージ キュー ジョブを使用すればストレージ キューにメッセージを送信できるため、このジョブはストレージ キューを使用するワークロードに最適です。 同様に、Service Bus キューと Service Bus トピックを使用するワークロードには、Service Bus ジョブが最適です。

## <a name="the-job-entity-in-detail"></a>"ジョブ" エンティティの詳細
基本的なレベルでは、スケジュールされたジョブは、次のような要素で構成されます。

* ジョブ タイマーが起動するときに実行するアクション  
* (省略可能) ジョブを実行する時間  
* (省略可能) ジョブを繰り返し実行するタイミングと頻度  
* (省略可能) プライマリ アクションが失敗した場合に実行するアクション  

内部的には、スケジュールされたジョブには、システム指定のデータ (次のスケジュールされた実行時間など) も含まれています。

次のコードは、スケジュールされたジョブの全体をまとめた例です。 詳細については、後続のセクションで説明します。

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

上記のスケジュールされたジョブの例に示すように、ジョブ定義はいくつかの要素から構成されます。

* 開始時刻 ("startTime")  
* アクション ("action")。エラー時のアクション ("errorAction") が含まれています。
* 繰り返し ("recurrence")  
* 状態 ("state")  
* ステータス ("status")  
* 再試行ポリシー ("retryPolicy")  

以降では、これらについて詳しく説明します。

## <a name="starttime"></a>startTime
"startTime" は開始時刻を示します。呼び出し元は、ネットワークにおけるタイム ゾーン オフセットを [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) 形式で指定できます。

## <a name="action-and-erroraction"></a>action と errorAction
"action" は、それぞれの実行時に呼び出されるアクションであり、サービスの呼び出しの種類を示します。 アクションは、指定されたスケジュールに従って実行されます。 Scheduler は、HTTP、ストレージ キュー、Service Bus トピック、Service Bus キューの操作をサポートしています。

上の例のアクションは HTTP アクションです。 ストレージ キュー アクションの例を次に示します。

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Service Bus トピックの操作の例を次に示します。

  "action": { "type": "serviceBusTopic", "serviceBusTopicMessage": { "topicPath": "t1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, }

Service Bus キューの操作の例を次に示します。

  "action": { "serviceBusQueueMessage": { "queueName": "q1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": {  
        "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message",  
      "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, "type": "serviceBusQueue" }

"errorAction" はエラー ハンドラーであり、プライマリ アクションが失敗したときに呼び出されるアクションを示します。 この変数を使用すると、エラー処理エンドポイントを呼び出したり、ユーザー通知を送信したりできます。 これは、(たとえば、エンドポイントのサイトの障害が原因で) プライマリが使用できない場合にセカンダリ エンドポイントにアクセスしたり、エラー処理エンドポイントに通知したりするために使用できます。 エラー アクションは、プライマリ アクションと同じように、単純なロジックにすることも、他のアクションに基づいた複合型のロジックにすることもできます。 SAS トークンを作成する方法については、「 [Shared Access Signature の作成と使用](https://msdn.microsoft.com/library/azure/jj721951.aspx)」を参照してください。

## <a name="recurrence"></a>recurrence
recurrence には、次の要素が含まれます。

* frequency: minute、hour、day、week、month、または year。  
* interval: 指定した頻度で繰り返しジョブを実行する間隔。  
* 所定の schedule: 繰り返しジョブを実行する minutes (分)、hours (時)、weekdays (曜日)、months (月)、および monthdays (日にち) を指定します。  
* count: 実行回数。  
* endTime: 指定された終了日時以降、ジョブは実行されません。  

JSON 定義に定期実行オブジェクトが指定されている場合、ジョブは定期的に実行されます。 count と endTime の両方を指定した場合、最初に実行される完了ルールが優先されます。

## <a name="state"></a>state
ジョブの状態は、enabled、disabled、completed、faulted のいずれかになります。 PUT ジョブまたは PATCH ジョブを使用して、ジョブの状態を enabled または disabled に更新できます。 completed または faulted のジョブの状態は、更新できない最終的な状態です (ただし、ジョブを削除することはできます)。 state プロパティの例を次に示します。

        "state": "disabled", // enabled, disabled, completed, or faulted
completed または faulted のジョブは、60 日後に削除されます。

## <a name="status"></a>status
Scheduler ジョブが開始すると、ジョブの現在のステータスに関する情報が返されます。 このオブジェクトは、ユーザーが設定することはできず、システムによって設定されます。 ただし、このオブジェクトは (別個のリンクされたリソースではなく) ジョブ オブジェクトに含まれるため、ジョブのステータスを簡単に取得できます。

ジョブのステータスには、前回の実行の時間 (存在する場合)、次回のスケジュールされた実行の時間 (進行中ジョブの場合)、ジョブの実行回数が含まれます。

## <a name="retrypolicy"></a>retryPolicy
Scheduler ジョブが失敗した場合、再試行ポリシーを指定して、アクションを再試行するかどうかと再試行の方法を指定できます。 そのためには、**retryType** オブジェクトを使用します。再試行ポリシーがない場合は、上の例に示すように、このオブジェクトを **none** に設定します。 再試行ポリシーがある場合は、このオブジェクトを **fixed** に設定します。

再試行ポリシーを設定する場合、再試行間隔 (**retryInterval**) と再試行回数 (**retryCount**) の 2 つの追加の設定を指定できます。

**retryInterval** オブジェクトを使用して指定する再試行間隔は、再試行の間隔を示します。 既定値は 30 秒、最小構成可能値は 15 秒で、最大値は 18 か月です。 無料ジョブ コレクションのジョブの最小構成可能値は、1 時間です。  この値は、ISO 8601 形式で定義します。 同様に、 **retryCount** オブジェクトを使用して指定する再試行回数は、再試行を行う回数を示します。 既定値は 4 で、最大値は 20です\.**retryInterval** と **retryCount** は、どちらも省略可能です。 **retryType** を **fixed** に設定し、値を明示的に指定しない場合、これらの設定では既定値が使用されます。

## <a name="see-also"></a>関連項目
 [What is Scheduler? (Scheduler とは)](scheduler-intro.md)

 [Azure ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [Azure Scheduler で複雑なスケジュールと高度な定期実行を構築する方法](scheduler-advanced-complexity.md)

 [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)

 [Azure Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)

 [Azure Scheduler 送信認証](scheduler-outbound-authentication.md)




<!--HONumber=Dec16_HO2-->


