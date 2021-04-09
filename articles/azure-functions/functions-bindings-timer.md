---
title: Azure Functions のタイマー トリガー
description: Azure Functions でタイマー トリガーを使用する方法について説明します。
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 11/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f99f9d240a0a9220d3b7f57cddd0a4f8ba6b6101
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595993"
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Functions のタイマー トリガー

この記事では、Azure Functions でタイマー トリガーを使用する方法について説明します。 タイマー トリガーを使用すると、スケジュールに基づいて関数を実行できます。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

タイマーによってトリガーされる関数を手動で実行する方法については、「[HTTP によってトリガーされない関数を手動で実行する](./functions-manually-run-non-http.md)」を参照してください。

## <a name="packages---functions-2x-and-higher"></a>パッケージ - Functions 2.x 以降

タイマー トリガーは、[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet パッケージ、バージョン 3.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub リポジトリにあります。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

タイマー トリガーは、[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet パッケージ、バージョン 2.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub リポジトリにあります。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例では、分の値が 5 で割り切れる値である時刻ごとに実行される [C# 関数](functions-dotnet-class-library.md)を示します (例: 関数が 18:57:00 に開始された場合、次の実行は 19:00:00 です)。 [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) オブジェクトが関数に渡されます。

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルのタイマー トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数では、この関数呼び出しがスケジュールのミスの発生によるものかどうかを示すログが書き込まれます。 [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) オブジェクトが関数に渡されます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

C# スクリプト コードを次に示します。

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="java"></a>[Java](#tab/java)

次の例の関数は、5 分ごとにトリガーして実行します。 関数の `@TimerTrigger` 注釈では、[CRON 式](https://en.wikipedia.org/wiki/Cron#CRON_expression)と同じ文字列形式を使用してスケジュールが定義されています。

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルのタイマー トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数では、この関数呼び出しがスケジュールのミスの発生によるものかどうかを示すログが書き込まれます。 [タイマー オブジェクト](#usage)が関数に渡されます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

JavaScript コードを次に示します。

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の例は、[PowerShell](./functions-reference-powershell.md) でタイマー トリガーについて *function.json* および *run.ps1* ファイルを構成する方法を示しています。

```json
{
  "bindings": [
    {
      "name": "Timer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */5 * * * *"
    }
  ]
}
```

```powershell
# Input bindings are passed in via param block.
param($Timer)

# Get the current universal time in the default string format.
$currentUTCtime = (Get-Date).ToUniversalTime()

# The 'IsPastDue' property is 'true' when the current function invocation is later than scheduled.
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}

# Write an information log with the current time.
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"
```

[タイマー オブジェクト](#usage)のインスタンスが、関数に対して最初の引数として渡されます。

# <a name="python"></a>[Python](#tab/python)

次の例では、構成が *function.json* ファイルに記述されているタイマー トリガー バインディングを使用します。 このバインディングを使用する実際の [Python 関数](functions-reference-python.md)は、*__init__.py* ファイルに記述されています。 関数に渡されるオブジェクトの型は、[azure.functions.TimerRequest オブジェクト](/python/api/azure-functions/azure.functions.timerrequest)です。 関数ロジックでは、現在の呼び出しがスケジュールのミスの発生によるものかどうかを示すログが書き込まれます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Python コードを次に示します。

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)を使用します。

属性のコンストラクターは、CRON 式または `TimeSpan` を受け取ります。 `TimeSpan` は、関数アプリが App Service プランで実行している場合にのみ使うことができます。 `TimeSpan` は、従量課金プランまたはエラスティック Premium 関数ではサポートされていません。

次の例では CRON 式を示します。

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

関数の `@TimerTrigger` 注釈では、[CRON 式](https://en.wikipedia.org/wiki/Cron#CRON_expression)と同じ文字列形式を使用してスケジュールが定義されています。

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `TimerTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "timerTrigger" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | "in" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のタイマー オブジェクトを表す変数の名前。 | 
|**schedule**|**ScheduleExpression**|[CRON 式](#ncrontab-expressions)または [TimeSpan](#timespan) 値。 `TimeSpan` は、App Service プランで実行している関数アプリに対してのみ使うことができます。 スケジュール式をアプリ設定に含めて、たとえば "%ScheduleAppSetting%" のように、 **%** 記号で囲まれたアプリ設定名にこのプロパティを設定できます。 |
|**runOnStartup**|**runOnStartup**|`true` の場合、関数はランタイムの開始時に呼び出されます。 たとえば、ランタイムが開始するのは、関数アプリが非アクティブになってアイドル状態に移行した後で起動したとき、 関数が変化したために関数アプリが再起動するとき、関数アプリがスケールアウトするときなどです。`true` に設定されている場合でも、特に運用環境では、**runOnStartup** はほとんど呼び出されることはありません。 |
|**useMonitor**|**UseMonitor**|`true` または `false` に設定し、スケジュールを監視する必要があるかどうかを示します。 スケジュールの監視はスケジュールの発生を維持し、関数アプリのインスタンスが再起動するときでもスケジュールが正しく維持されることを保証するのに役立ちます。 このプロパティを明示的に設定しない場合、繰り返し間隔が 1 分以上のスケジュールの既定値は `true` です。 1 分間に 2 回以上トリガーするスケジュールの既定値は `false` です。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> 運用環境では、**runOnStartup** を `true` に設定しないことをお勧めします。 この設定を使用すると、まったく予期できないときにコードが実行されます。 特定の運用環境の設定では、これらの追加の実行によって、従量課金プランでホストされるアプリのコストが大幅に高くなる可能性があります。 たとえば、**runOnStartup** を有効にすると、ご利用の関数アプリがスケーリングされるたびに、トリガーが呼び出されます。 **runOnStartup** を運用環境で有効にする前に、ご利用の関数の運用環境での動作を完全に理解していることを確認してください。

## <a name="usage"></a>使用法

タイマー トリガー関数が呼び出されると、タイマー オブジェクトが関数に渡されます。 次の JSON は、タイマー オブジェクトの 1 つの表現例です。

```json
{
    "schedule":{
    },
    "scheduleStatus": {
        "last":"2016-10-04T10:15:00+00:00",
        "lastUpdated":"2016-10-04T10:16:00+00:00",
        "next":"2016-10-04T10:20:00+00:00"
    },
    "isPastDue":false
}
```

現在の関数の呼び出しがスケジュールより遅い場合、`isPastDue` プロパティは `true` になります。 たとえば、関数アプリが再起動すると、呼び出しが行われない可能性があります。

## <a name="ncrontab-expressions"></a>NCRONTAB 式

Azure Functions では、NCRONTAB 式を解釈するのに [NCronTab](https://github.com/atifaziz/NCrontab) ライブラリが使用されます。 NCRONTAB 式は CRON 式に似ていますが、秒単位の時間精度に使用するために、追加の 6 番目のフィールドが最初に含まれている点が異なります。

`{second} {minute} {hour} {day} {month} {day-of-week}`

各フィールドは、次の種類の値のいずれかを持つことができます。

|Type  |例  |トリガーのタイミング  |
|---------|---------|---------|
|特定の値 |<nobr>`0 5 * * * *`</nobr>| 1 時間ごとに 1 回、1 日の毎時 5 分 |
|すべての値 (`*`)|<nobr>`0 * 5 * * *`</nobr>| 5 時から始まる 1 時間で 1 分ごと |
|範囲 (`-` 演算子)|<nobr>`5-7 * * * * *`</nobr>| 1 分間に 3 回 - 各日、各時間、毎分 5 秒から 7 秒 |
|値のセット (`,` 演算子)|<nobr>`5,8,10 * * * * *`</nobr>| 1 分間に 3 回 - 各日、各時間、毎分 5 秒、8 秒、10 秒 |
|間隔値 (`/` 演算子)|<nobr>`0 */5 * * * *`</nobr>| 1 時間に 12 回 - 各日、毎時 5 分 0 秒 |

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB の例

Azure Functions のタイマー トリガーに使用できる NCRONTAB 式の例をいくつか示します。

| 例            | トリガーのタイミング                     |
|--------------------|------------------------------------|
| `0 */5 * * * *`    | 5 分ごとに 1 回            |
| `0 0 * * * *`      | 毎正時に 1 回      |
| `0 0 */2 * * *`    | 2 時間に 1 回               |
| `0 0 9-17 * * *`   | 午前 9 時から午後 5 時ま、1 時間に 1 回  |
| `0 30 9 * * *`     | 毎日午前 9 時 30 分               |
| `0 30 9 * * 1-5`   | 平日の毎日午前 9 時 30 分           |
| `0 30 9 * Jan Mon` | 1 月の毎週月曜日の午前 9時 30分 |

> [!NOTE]
> NCRONTAB 式には、**6 フィールド** 形式が必要です。 6 番目のフィールド位置は、式の先頭に配置される秒の値です。 5 フィールド CRON 式は Azure ではサポートされていません。

### <a name="ncrontab-time-zones"></a>NCRONTAB タイム ゾーン

CRON 式に出現する値は、時間間隔ではなく時刻と日付を示します。 たとえば、`hour` フィールドの 5 は、5 時間ごとではなく、午前 5 時 00 分を示します。

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="timespan"></a>TimeSpan

 `TimeSpan` は、App Service プランで実行している関数アプリに対してのみ使うことができます。

CRON 式とは異なり、`TimeSpan` の値は各関数呼び出しの間の時間間隔を指定します。 指定された間隔より長く実行した後で関数が完了すると、タイマーすぐに関数を再び呼び出します。

`TimeSpan` は文字列として表され、`hh` が 24 未満のときの形式は `hh:mm:ss` です。 最初の 2 つの数字が 24 以上のときの形式は `dd:hh:mm` です。 次に例をいくつか示します。

| 例      | トリガーのタイミング |
|--------------|----------------|
| "01:00:00"   | 1 時間ごと     |
| "00:01:00"   | 1 分ごと   |
| "25:00:00"   | 25 日ごと  |
| "1.00:00:00" | 毎日      |

## <a name="scale-out"></a>スケールアウト

関数アプリが複数のインスタンスにスケールアウトする場合は、タイマーによってトリガーされる関数の 1 つのインスタンスだけがすべてのインスタンスで実行されます。 未処理の呼び出しがまだ実行中の場合は、再度トリガーされません。

## <a name="function-apps-sharing-storage"></a>ストレージを共有する関数アプリ

App Service にデプロイされていない関数アプリの間でストレージ アカウントを共有している場合は、ホスト ID を各アプリに明示的に割り当てることが必要な場合があります。

| Functions バージョン | 設定                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x (以上)  | `AzureFunctionsWebHost__hostid` 環境変数 |
| 1.x               | *host.json* での `id`                                  |

識別値を省略するか、各関数アプリの識別構成に異なる値を手動で設定することができます。

1 つの関数アプリが複数のインスタンスにスケールアウトされる場合、タイマー インスタンスが 1 しか存在しないようにするために、タイマー トリガーではストレージ ロックが使用されます。 2 つの関数アプリで同じ識別構成が共有されていて、それぞれでタイマー トリガーが使用されている場合は、1 つのタイマーのみが実行されます。

## <a name="retry-behavior"></a>再試行の動作

キュー トリガーとは異なり、タイマー トリガーは関数が失敗した後で再試行しません。 失敗した関数は、次のスケジュール時刻まで再び呼び出されることはありません。

## <a name="manually-invoke-a-timer-trigger"></a>タイマー トリガーを手動で呼び出す

Azure Functions のタイマー トリガーには、手動で関数をトリガーするために呼び出すことができる HTTP Webhook が用意されています。 これは、次のようなシナリオで非常に役立ちます。

* 統合テスト
* スモーク テストまたはウォームアップ アクティビティの一部としてのスロット スワップ
* データベース内のキャッシュまたはルックアップ テーブルに直ちにデータを入力する関数の初期デプロイ

タイマーによってトリガーされる関数を手動で呼び出す方法の詳細については、[HTTP によってトリガーされない関数を手動で実行する](./functions-manually-run-non-http.md)方法に関するページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

タイマー トリガーが期待どおりに機能しない場合の対処方法については、[タイマー トリガーが設定された関数が発生しない問題の調査と報告](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [タイマー トリガーを使用するクイックスタートに進む](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
