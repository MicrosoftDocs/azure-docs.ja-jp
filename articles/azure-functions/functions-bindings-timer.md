---
title: "Azure Functions におけるタイマー トリガー | Microsoft Docs"
description: "Azure Functions でタイマー トリガーを使用する方法について説明します。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: chrande; glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2542d8c750fc7e1bcc31a9c0eb1672402facfd58
ms.openlocfilehash: 146884833e968767c14d7e4f924762a592e427e2
ms.lasthandoff: 03/01/2017


---
# <a name="schedule-code-execution-with-azure-functions"></a>Azure Functions を使用したコード実行のスケジュール設定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions でタイマー トリガーを構成およびコーディングする方法について説明します。 Azure Functions には、定義したスケジュールに基づいて関数コードを実行することができるタイマー トリガー バインディングがあります。 

タイマー トリガーでは、複数インスタンスのスケールアウトがサポートされます。 特定のタイマー関数の&1; つのインスタンスが、すべてのインスタンスにわたって実行されます。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a id="trigger"></a>

## <a name="timer-trigger"></a>タイマー トリガー
関数へのタイマー トリガーでは、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
    "schedule": "<CRON expression - see below>",
    "name": "<Name of trigger parameter in function signature>",
    "type": "timerTrigger",
    "direction": "in"
}
```

`schedule` の値は、次の&6; 個のフィールドが含まれる [CRON 式](http://en.wikipedia.org/wiki/Cron#CRON_expression)です。 

    {second} {minute} {hour} {day} {month} {day-of-week}
&nbsp;
>[!NOTE]   
>オンラインで見つかる CRON 式の多くでは、`{second}` フィールドが省略されています。 それらをコピーした場合は、追加の `{second}` フィールドを調整する必要があります。 具体的な例については、以下の「[スケジュールの例](#examples)」を参照してください。

CRON 式で使用する既定のタイム ゾーンは、協定世界時 (UTC) です。 別のタイム ゾーンに基づく CRON 式を使用するには、Function App 用に `WEBSITE_TIME_ZONE` という名前の新しいアプリ設定を作成します。 この値を、[Microsoft のタイム ゾーン インデックス](https://msdn.microsoft.com/library/ms912391.aspx)に関するページに示されている目的のタイム ゾーンの名前に設定します。 

たとえば、"*東部標準時*" は UTC-05:00 です。 タイマー トリガーが毎日東部標準時の 10:00 AM に発生するように設定するには、UTC タイム ゾーンを考慮した次の CRON 式を使用できます。

```json
"schedule": "0 0 15 * * *",
```    

また、Function App の新しいアプリ設定を `WEBSITE_TIME_ZONE` という名前で追加し、その値を "**東部標準時**" に設定することもできます。  その場合、東部標準時の 10:00 AM を表す次の CRON 式を使用できます。 

```json
"schedule": "0 0 10 * * *",
```    


<a name="examples"></a>

## <a name="schedule-examples"></a>スケジュールの例
`schedule` プロパティに使用できる CRON 式の例をいくつか示します。 

5 分に&1; 回トリガーするには、次のように指定します。

```json
"schedule": "0 */5 * * * *"
```

毎正時に&1; 回トリガーするには、次のように指定します。

```json
"schedule": "0 0 * * * *",
```

2 時間に&1; 回トリガーするには、次のように指定します。

```json
"schedule": "0 0 */2 * * *",
```

午前 9 時から午後 5 時まで 1 時間ごとにトリガーするには、次のように指定します。

```json
"schedule": "0 0 9-17 * * *",
```

毎日の午前 9 時 30 分にトリガーするには、次のように指定します。

```json
"schedule": "0 30 9 * * *",
```

平日の午前 9 時 30 分にトリガーするには、次のように指定します。

```json
"schedule": "0 30 9 * * 1-5",
```

<a name="usage"></a>

## <a name="trigger-usage"></a>トリガーの使用方法
タイマー トリガー関数が呼び出されると、[タイマー オブジェクト](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)が関数に渡されます。 次の JSON は、タイマー オブジェクトの&1; つの表現例です。 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

<a name="sample"></a>

## <a name="trigger-sample"></a>トリガー サンプル
function.json の `bindings` 配列に次のタイマー トリガーがあるとします。

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

タイマー オブジェクトを読み取って遅延実行されているかどうかを調べる、言語固有のサンプルを参照してください。

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C でのトリガー サンプル# #
```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>F でのトリガー サンプル# #
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js でのトリガー サンプル
```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="next-steps"></a>次のステップ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


