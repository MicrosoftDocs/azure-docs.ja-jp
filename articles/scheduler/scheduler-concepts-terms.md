---
title: "スケジューラの概念、用語、およびエンティティ |Microsoft ドキュメント"
description: "Azure スケジューラの概念、用語、およびジョブとジョブ コレクションを含む、エンティティの階層です。  スケジュールされたジョブの包括的な例を示します。"
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
ms.openlocfilehash: 0f035b58ccd140a5481703df7e184206da2ed651
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>スケジューラの概念、用語、+ エンティティの階層構造
## <a name="scheduler-entity-hierarchy"></a>スケジューラ エンティティの階層構造
次の表では、スケジューラ API で公開または使用の主要なリソースについて説明します。

| リソース | 説明 |
| --- | --- |
| **ジョブ コレクション** |ジョブ コレクションは、ジョブのグループが含まれてし、設定、クォータ、および、コレクション内のジョブで共有されるスロットルを保持します。 ジョブ コレクションは、サブスクリプションの所有者によって作成され、一緒に使用状況やアプリケーション境界に基づいてジョブをグループ化します。 これは、1 つの地域に制限します。 また、そのコレクション内のすべてのジョブの使用を制限するクォータの強制こともできます。 クォータは MaxJobs と maxrecurrence が含まれます。 |
| **ジョブ** |ジョブでは、実行のため単純または複雑な戦略に、1 つの定期的なアクションを定義します。 操作には、HTTP、ストレージ キュー、service bus キューまたは service bus トピックの要求があります。 |
| **ジョブ履歴** |ジョブ履歴は、ジョブの実行の詳細を表します。 成功応答の詳細と同様に、エラーとが含まれています。 |

## <a name="scheduler-entity-management"></a>スケジューラ エンティティの管理
大まかに言えば、スケジューラは、およびサービス管理 API は、リソースでは、次の操作を公開します。

| 機能 | 説明と URI アドレス |
| --- | --- |
| **ジョブ コレクションの管理** |GET、PUT、の作成とジョブ コレクションとそれに含まれるジョブの変更のサポートを削除します。 ジョブ コレクションは、クォータと共有の設定に対応してジョブのコンテナーです。 後で説明されているクォータの例では、ジョブと最小の繰り返し間隔の最大数です。 <p>PUT や DELETE:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>取得：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p> |
| **ジョブの管理** |GET、PUT、POST、PATCH、およびの作成とジョブの変更のサポートを削除します。 暗黙の作成がないように、すべてのジョブが既に存在する、ジョブ コレクションに属していなければなりません。 <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p> |
| **ジョブ履歴の管理** |60 日間に経過時間のジョブとジョブの実行結果など、ジョブの実行履歴をフェッチするためのサポートを取得します。 状態とステータスに基づいたフィルターのクエリ文字列パラメーターのサポートを追加します。 <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p> |

## <a name="job-types"></a>ジョブの種類
ジョブの複数の種類があります。 HTTP ジョブ (SSL をサポートする HTTPS ジョブを含む)、ストレージ キュー ジョブ、サービス バス キューのジョブ、およびサービス バス トピック ジョブです。 HTTP ジョブは、既存のワークロードやサービスのエンドポイントがある場合に適しています。 ストレージ キュー ジョブを使用すると、それらのジョブは、ストレージ キューを使用するワークロードに適してストレージ キューにメッセージを投稿します。 同様に、サービス バスのジョブは、service bus キューおよびトピックを使用するワークロードに最適です。

## <a name="the-job-entity-in-detail"></a>「ジョブ」エンティティの詳細
基本的なレベルでは、スケジュールされたジョブはいくつかの部分があります。

* ジョブ タイマーが発生したときに実行するアクション  
* (省略可能)ジョブを実行する時間  
* (省略可能)タイミングと頻度をジョブの繰り返し  
* (省略可能)プライマリ アクションが失敗した場合に実行されるアクション  

内部的には、スケジュールされたジョブには、[次へ] のスケジュールされた実行時間などのシステム指定のデータも含まれます。

次のコードでは、スケジュールされたジョブの包括的な例を示します。 詳細については、後続のセクションで説明します。

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

上記のサンプル スケジュールされたジョブからわかるように、ジョブ定義はいくつかの部分があります。

* 開始時刻 ("startTime")。  
* アクション ("action")、エラーのアクション ("errorAction") が含まれています
* 定期的な ("recurrence")。  
* 状態 (「状態」)  
* 状態 ("")  
* 再試行ポリシー ("retryPolicy")  

詳しくは、これらの各を調べてみましょう。

## <a name="starttime"></a>startTime
"StartTime"は、開始時刻は、でき、呼び出し元で、ネットワーク上でタイム ゾーン オフセットを指定する[ISO 8601 形式](http://en.wikipedia.org/wiki/ISO_8601)です。

## <a name="action-and-erroraction"></a>action と errorAction
"Action"出現するたびに呼び出されるアクションは、サービスの呼び出しの種類について説明します。 アクションは、新機能は、指定されたスケジュールに実行されます。 スケジューラは、HTTP、ストレージ キュー、service bus トピック、およびサービス バス キューの操作をサポートします。

上記の例では、アクションは、HTTP アクションです。 ストレージ キュー アクションの例を次に示します。

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

以下には、サービス バス トピック アクションの例を示します。

  "action": {"type":「servicebustopic でした」、"serviceBusTopicMessage": {"topicPath":"t1"  
      「名前空間」:"mySBNamespace"、"transportType":"netMessaging"//netMessaging または AMQP"authentication"のいずれかを指定できます: {"sasKeyName":"QPolicy"、"type":"sharedAccessKey"}、"message":「によってメッセージ」、"brokeredMessageProperties": {}、"customMessageProperties": {"appname":"FromScheduler"}}、}

以下には、サービス バス キュー アクションの例を示します。

  "action": {"serviceBusQueueMessage": {"queueName":"q1"  
      「名前空間」:"mySBNamespace"、"transportType":"netMessaging"//netMessaging または AMQP"authentication"のいずれかを指定できます: {  
        "sasKeyName":"QPolicy"、"type":"sharedAccessKey"}、"message":「いくつかのメッセージ」  
      "brokeredMessageProperties": {}、"customMessageProperties": {"appname":"FromScheduler"}}、"type":"serviceBusQueue"}

"ErrorAction"は、エラー ハンドラー、プライマリ アクションが失敗したときに呼び出されるアクションです。 この変数は、エラー処理エンドポイントの呼び出しまたはユーザー通知の送信に使用できます。 これは、プライマリが使用できない (たとえばの場合、エンドポイントのサイトで災害が発生) の場合、セカンダリ エンドポイントに到達するために使用できます。 または処理のエンドポイントでエラーを通知するために使用できます。 プライマリ アクションと同じようにエラー アクションに他のアクションに基づいて単純型または複合ロジックことができます。 SAS トークンを作成する方法についてを参照してください[を作成し、Shared Access Signature を使用して](https://msdn.microsoft.com/library/azure/jj721951.aspx)です。

## <a name="recurrence"></a>定期的なアイテム
定期的なアイテムでは、複数の部分があります。

* 頻度: 1 分、時間、日、週、月、年の 1 つ  
* 定期実行に対して指定した頻度で間隔: 間隔  
* 指定されたスケジュール: 分、時間、曜日、月、および、繰り返しの月の日にちを指定します。  
* 回数のカウント:  
* 終了時刻: 指定された終了日時以降後ジョブは実行されません  

ジョブが定期的なは、定期実行の JSON 定義で指定されたオブジェクトがある場合。 Count と endTime の両方を指定すると、最初に発生する完了のルールが受け入れられます。

## <a name="state"></a>state
ジョブの状態が 4 つの値のいずれかの: 有効になっているか、無効に、完了すると、エラーが発生しました。 配置できるまたは PATCH ジョブを有効または無効な状態に更新できるようにします。 ジョブを完了または違反になった場合は、最終的な状態です (ただし、ジョブの削除はできます) を更新できません。 State プロパティの例は次のとおりです。

        "state": "disabled", // enabled, disabled, completed, or faulted
完了したとエラーが発生したジョブは、60 日後に削除されます。

## <a name="status"></a>状態
スケジューラ ジョブが開始した後は、ジョブの現在の状態に関する情報が返されます。 このオブジェクトは、ユーザーが設定できません-システムによって設定されます。 ただし、ジョブの状態を簡単に 1 つ取得できるように、(別のリンクされたリソースではなく)、ジョブ オブジェクトに含まれています。

ジョブの状態が含まれます (存在する場合) は、前回の実行の時間には (の実行中のジョブの場合)、次のスケジュールされた実行の時間と、ジョブの実行回数。

## <a name="retrypolicy"></a>retryPolicy
スケジューラ ジョブが失敗した場合、可能であれば、アクションを再試行するかどうかおよびその方法を決定する再試行ポリシーを指定します。 これによって決定されますが、 **retryType**オブジェクト — に設定されている**none**上記のように、再試行ポリシーがない場合。 設定**固定**再試行ポリシーがある場合。

再試行ポリシーを設定するには、2 つの追加設定を指定することがあります。 再試行間隔 (**retryInterval**) と再試行の回数 (**retryCount**)。

再試行間隔で指定された、 **retryInterval**オブジェクト、再試行の間隔がします。 その既定値は 30 秒、その最小構成可能な値は 15 秒、および、最大値は 18 か月です。 無料ジョブ コレクション内のジョブでは、1 時間の最小の構成可能な値があります。  ISO 8601 形式で定義されます。 再試行回数の値を指定する同様に、 **retryCount**オブジェクトです。 再試行を行う回数を超えることができます。 その既定値は 4、および、最大値は 20\ します。 両方**retryInterval**と**retryCount**は省略可能です。 場合に、既定値が指定が**retryType**に設定されている**固定**値が明示的に指定しないとします。

## <a name="see-also"></a>関連項目
 [スケジューラは何ですか。](scheduler-intro.md)

 [Azure ポータルでスケジューラを使用して作業を開始します。](scheduler-get-started-portal.md)

 [プランと Azure のスケジューラでの課金](scheduler-plans-billing.md)

 [複雑なスケジュールと Azure のスケジューラと高度な定期的なアイテムを作成する方法](scheduler-advanced-complexity.md)

 [Azure スケジューラ REST API リファレンス](https://msdn.microsoft.com/library/mt629143)

 [Azure のスケジューラの PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)

 [Azure のスケジューラの高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Azure スケジューラの制限、既定値、およびエラー コード](scheduler-limits-defaults-errors.md)

 [Azure のスケジューラ送信認証](scheduler-outbound-authentication.md)

