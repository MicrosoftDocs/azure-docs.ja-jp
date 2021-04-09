---
title: 概念、用語、およびエンティティ
description: Azure Scheduler の概念、用語、およびエンティティ階層 (ジョブやジョブ コレクションなど) について説明します
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 08/18/2016
ms.openlocfilehash: 899c64e818896cde18e955d6abd82594734c4b57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368164"
---
# <a name="concepts-terminology-and-entities-in-azure-scheduler"></a>Azure Scheduler の概念、用語集、エンティティ

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、[廃止される予定](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)の Azure Scheduler の後継です。 Scheduler で設定したジョブを使用し続けるには、できるだけ早く [Azure Logic Apps に移行](../scheduler/migrate-from-scheduler-to-logic-apps.md)してください。 
>
> Scheduler は Azure portal で利用できなくなりましたが、現時点では [REST API](/rest/api/scheduler) と [Azure Scheduler PowerShell コマンドレット](scheduler-powershell-reference.md)がまだ使用できるので、お客様はジョブとジョブ コレクションを管理することができます。

## <a name="entity-hierarchy"></a>エンティティ階層

Azure Scheduler REST API では、以下の主要なエンティティつまりリソースが公開および使用されます。

| Entity | 説明 |
|--------|-------------|
| **ジョブ** | 単純または複雑な実行方法による単一の反復的な操作を定義します。 操作には、HTTP、ストレージ キュー、Service Bus キュー、Service Bus トピックの要求が含まれます。 | 
| **ジョブ コレクション** | ジョブのグループを含み、コレクション内のジョブで共有される設定、クォータ、調整を保持します。 Azure サブスクリプションの所有者は、ジョブ コレクションを作成し、使用状況やアプリケーションの境界に基づいてジョブをグループ化することができます。 ジョブ コレクションには次の属性があります。 <p>- 1 つのリージョンに制限されます。 <br>- クォータを適用して、コレクション内のすべてのジョブの使用量を制限できます。 <br>- クォータには、MaxJobs と MaxRecurrence が含まれます。 | 
| **ジョブ履歴** | 状態と応答の詳細など、ジョブの実行に関する詳細が記述されています。 |
||| 

## <a name="entity-management"></a>エンティティの管理

大まかには、Scheduler REST API ではエンティティを管理するための操作が公開されています。

### <a name="job-management"></a>ジョブの管理

ジョブを作成および編集するための操作をサポートしています。 すべてのジョブは既存のジョブ コレクションに属する必要があるため、暗黙的な作成はありません。 詳しくは、[ジョブの Scheduler REST API](/rest/api/scheduler/jobs) に関するページをご覧ください。 これらの操作の URI アドレスを次に示します。

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}
```

### <a name="job-collection-management"></a>ジョブ コレクションの管理

ジョブとジョブ コレクションの作成と編集の操作をサポートし、クォータと共有設定にマップしています。 たとえば、クォータではジョブの最大数や最小の繰り返し間隔が指定されています。 詳しくは、[ジョブ コレクションの Scheduler REST API](/rest/api/scheduler/jobcollections) に関するページをご覧ください。 これらの操作の URI アドレスを次に示します。

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}
```

### <a name="job-history-management"></a>ジョブ履歴の管理

ジョブの経過時間やジョブの実行結果など、60 日分のジョブの実行履歴をフェッチするための GET 操作がサポートされています。 状態およびステータスに基づいてフィルター処理を行うためのクエリ文字列パラメーターのサポートを含みます。 詳しくは、[ジョブのジョブ履歴一覧表示の Scheduler REST API](/rest/api/scheduler/jobs/listjobhistory) に関するページをご覧ください。 この操作の URI アドレスを次に示します。

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history
```

## <a name="job-types"></a>ジョブの種類

Azure Scheduler では、複数のジョブの種類がサポートされています。 

* 既存のサービスまたはワークロードに対するエンドポイントがある場合の HTTP ジョブ (TLS をサポートする HTTPS ジョブを含みます)
* Storage キューへのメッセージの投稿など、Storage キューを使用するワークロードに対する Storage キュー ジョブ
* Service Bus キューを使用するワークロードに対する Service Bus キュー ジョブ
* Service Bus トピックを使用するワークロードに対する Service Bus トピック ジョブ

## <a name="job-definition"></a>ジョブ定義

高レベルでは、Scheduler ジョブには次の基本的な部分があります。

* ジョブ タイマーが起動するときに実行するアクション
* 省略可能:ジョブを実行する時間
* 省略可能:ジョブを繰り返し実行するタイミングと頻度
* 省略可能:プライマリ アクションが失敗した場合に実行するエラー アクション

ジョブには、ジョブのスケジュールされた次回の実行時刻など、システム指定のデータも含まれています。 ジョブのコードの定義は、JavaScript Object Notation (JSON) 形式のオブジェクトであり、次の要素が含まれます。

| 要素 | 必須 | 説明 | 
|---------|----------|-------------| 
| [**startTime**](#start-time) | いいえ | [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601)のタイム ゾーン オフセットを含むジョブの開始時刻 | 
| [**action**](#action) | はい | **errorAction** オブジェクトを含む場合がある、プライマリ アクションの詳細 | 
| [**errorAction**](#error-action) | いいえ | プライマリ アクションが失敗した場合に実行するセカンダリ アクションの詳細 |
| [**recurrence**](#recurrence) | いいえ | 定期的なジョブの頻度や間隔などの詳細 | 
| [**retryPolicy**](#retry-policy) | いいえ | アクションを再試行する頻度の詳細 | 
| [**state**](#state) | はい | ジョブの現在の状態の詳細 |
| [**status**](#status) | はい | サービスによって制御される、ジョブの現在のステータスの詳細 |
||||

後のセクションで説明されている要素の完全な詳細を含む HTTP アクションに対する包括的なジョブ定義の例を次に示します。 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z",
   "action": {
      "type": "Http",
      "request": {
         "uri": "http://contoso.com/some-method", 
         "method": "PUT",          
         "body": "Posting from a timer",
         "headers": {
            "Content-Type": "application/json"
         },
         "retryPolicy": { 
             "retryType": "None" 
         },
      },
      "errorAction": {
         "type": "Http",
         "request": {
            "uri": "http://contoso.com/notifyError",
            "method": "POST"
         }
      }
   },
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]
      },
      "count": 10,
      "endTime": "2012-11-04"
   },
   "state": "Disabled",
   "status": {
      "lastExecutionTime": "2007-03-01T13:00:00Z",
      "nextExecutionTime": "2007-03-01T14:00:00Z ",
      "executionCount": 3,
      "failureCount": 0,
      "faultedCount": 0
   }
}
```

<a name="start-time"></a>

## <a name="starttime"></a>startTime

**StartTime** オブジェクトでは、開始時刻とタイム ゾーン オフセットを [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601)で指定できます。

<a name="action"></a>

## <a name="action"></a>action

Scheduler ジョブは、指定されたスケジュールに基づいてプライマリ **アクション** を実行します。 Scheduler は、HTTP、Storage キュー、Service Bus キュー、Service Bus トピックのアクションをサポートしています。 プライマリ **アクション** が失敗した場合、Scheduler はエラーを処理するセカンダリ [**errorAction**](#erroraction) を実行できます。 **アクション** オブジェクトでは次の要素が記述されています。

* アクションのサービスの種類
* アクションの詳細
* 代替 **errorAction**

前の例では、HTTP アクションが記述されています。 Storage キュー アクションの例を次に示します。

```json
"action": {
   "type": "storageQueue",
   "queueMessage": {
      "storageAccount": "myStorageAccount",  
      "queueName": "myqueue",                
      "sasToken": "TOKEN",                   
      "message": "My message body"
    }
}
```

Service Bus キュー アクションの例を次に示します。

```json
"action": {
   "type": "serviceBusQueue",
   "serviceBusQueueMessage": {
      "queueName": "q1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {  
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",  
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Service Bus トピック アクションの例を次に示します。

```json
"action": {
   "type": "serviceBusTopic",
   "serviceBusTopicMessage": {
      "topicPath": "t1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Shared Access Signature (SAS) トークンについて詳しくは、[Shared Access Signature での承認](../storage/common/storage-sas-overview.md)に関するページをご覧ください。

<a name="error-action"></a>

## <a name="erroraction"></a>errorAction

ジョブのプライマリ **アクション** が失敗した場合、Scheduler はエラーを処理する **errorAction** を実行できます。 プライマリ **アクション** では、Scheduler がエラー処理エンドポイントを呼び出したりユーザー通知を送信したりできるように、**errorAction** オブジェクトを指定できます。 

たとえば、プライマリ エンドポイントで障害が発生した場合、**errorAction** を使用して、セカンダリ エンドポイントを呼び出したり、エラー処理エンドポイントに通知したりできます。 

エラー アクションは、プライマリ **アクション** と同じように、単純なロジックにすることも、他のアクションに基づいた複合型のロジックにすることもできます。 

<a name="recurrence"></a>

## <a name="recurrence"></a>recurrence

ジョブの JSON 定義に **recurrence** オブジェクトが含まれている場合、ジョブが繰り返されます。次はその例です。

```json
"recurrence": {
   "frequency": "Week",
   "interval": 1,
   "schedule": {
      "hours": [10, 22],
      "minutes": [0, 30],
      "weekDays": ["Monday", "Wednesday", "Friday"]
   },
   "count": 10,
   "endTime": "2012-11-04"
},
```

| プロパティ | 必須 | 値 | 説明 | 
|----------|----------|-------|-------------| 
| **frequency** | はい。**recurrence** が使用されているとき | "Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" | 発生の間隔の時間単位 | 
| **interval** | いいえ | 1 ～ 1000 | **frequency** に基づいて実行間の時間単位の数を決定する正の整数 | 
| **schedule** | いいえ | 場合により異なる | 複雑で高度なスケジュールの詳細。 **hours**、**minutes**、**weekDays**、**months**、**monthDays** を参照してください | 
| **hours** | いいえ | 1 ～ 24 | ジョブ実行日時に対する時マークを含む配列 | 
| **分** | いいえ | 0 ～ 59 | ジョブ実行日時に対する分マークを含む配列 | 
| **months** | いいえ | 1 ～ 12 | ジョブ実行日時に対する月の配列 | 
| **monthDays** | いいえ | 場合により異なる | ジョブ実行日時に対する日の配列 | 
| **weekDays** | いいえ | "Monday"、"Tuesday"、"Wednesday"、"Thursday"、"Friday"、"Saturday"、"Sunday" | ジョブ実行日時に対する曜日の配列 | 
| **count** | いいえ | <*none*> | 繰り返しの数。 既定では、無限に繰り返します。 **count** と **endTime** の両方を使用することはできません。この場合、最初に完了する規則が適用されます。 | 
| **endTime** | いいえ | <*none*> | 繰り返しを停止する日付と時刻。 既定では、無限に繰り返します。 **count** と **endTime** の両方を使用することはできません。この場合、最初に完了する規則が適用されます。 | 
||||

これらの要素について詳しくは、[複雑なスケジュールと高度な繰り返しの作成](../scheduler/scheduler-advanced-complexity.md)に関するページをご覧ください。

<a name="retry-policy"></a>

## <a name="retrypolicy"></a>retryPolicy

Scheduler ジョブが失敗したときのために、再試行ポリシーを設定して、Scheduler がアクションを再試行するかどうかと方法を決定できます。 既定では、Scheduler により 30 秒間隔でさらに 4 回ジョブの実行が試みられます。 このポリシーの頻度を上げたり下げたりできます。たとえば、次のポリシーは 1 日に 2 回アクションを再試行します。

```json
"retryPolicy": { 
   "retryType": "Fixed",
   "retryInterval": "PT1D",
   "retryCount": 2
},
```

| プロパティ | 必須 | 値 | 説明 | 
|----------|----------|-------|-------------| 
| **retryType** | はい | **Fixed**、**None** | 再試行ポリシーを指定するか (**fixed**) しないか (**none**) を決定します。 | 
| **retryInterval** | いいえ | PT30S | 再試行の間隔と頻度を [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)で指定します。 最小値は 15 秒、最大値は 18 か月です。 | 
| **retryCount** | いいえ | 4 | 再試行の回数を指定します。 最大値は 20 です。 | 
||||

詳細については、[高可用性と信頼性](../scheduler/scheduler-high-availability-reliability.md)に関するページを参照してください。

<a name="status"></a>

## <a name="state"></a>state

ジョブの状態は、**Enabled**、**Disabled**、**Completed**、または **Faulted** です。次はその例です。 

`"state": "Disabled"`

ジョブを **Enabled** または **Disabled** 状態に変更するには、これらのジョブに対して PUT または PATCH 操作を使用できます。
ただし、ジョブが **Completed** または **Faulted** 状態の場合は、状態を更新することはできませんが、ジョブに対して DELETE 操作を実行できます。 Scheduler は、Completed または Faulted のジョブを 60 日後に削除します。 

<a name="status"></a>

## <a name="status"></a>status

ジョブが開始した後、Scheduler はジョブのステータスに関する情報を **status** オブジェクトで返します。これは、Scheduler のみが制御します。 ただし、**status** オブジェクトは **job** オブジェクト内で見つかります。 ジョブのステータスには次の情報が含まれます。

* 前回の実行の日時 (ある場合)
* 進行中のジョブに対してスケジュールされている次回の実行の日時
* ジョブの実行の回数
* 失敗の数 (ある場合)
* エラーの数 (ある場合)

次に例を示します。

```json
"status": {
   "lastExecutionTime": "2007-03-01T13:00:00Z",
   "nextExecutionTime": "2007-03-01T14:00:00Z ",
   "executionCount": 3,
   "failureCount": 0,
   "faultedCount": 0
}
```

## <a name="next-steps"></a>次のステップ

* [複雑なスケジュールと高度な繰り返しを作成する](scheduler-advanced-complexity.md)
* [Azure Scheduler REST API リファレンス](/rest/api/scheduler)
* [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)
* [制限、クォータ、既定値、エラー コード](scheduler-limits-defaults-errors.md)