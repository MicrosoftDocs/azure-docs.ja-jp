---
title: Azure Functions の監視を構成する
description: 監視のために関数アプリを Application Insights に接続する方法と、データ収集を構成する方法について説明します。
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5007009d9aabf9a1c1c6e1d5c2f286c0ba25b340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493755"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Azure Functions の監視を構成する方法

Azure Functions が Application Insights に統合されると、関数アプリの監視をより適切に行うことができます。 Azure Monitor の機能である Application Insights は、アプリがログに書き込む情報など、関数アプリによって生成されたデータを収集する拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 Application Insights との統合は、通常、関数アプリが作成されるときに有効になります。 アプリにインストルメンテーション キーが設定されていない場合は、まず [Application Insights との統合を有効にする](#enable-application-insights-integration)必要があります。 

Application Insights はカスタム構成なしで使用できます。 既定の構成ではデータ量が多くなる可能性があります。 Visual Studio Azure サブスクリプションを使っている場合、Application Insights のデータ上限に達する可能性があります。 Application Insights のコストの詳細については、「[Application Insights の使用量とコストを管理する](../azure-monitor/app/pricing.md)」を参照してください。

この記事の後半では、関数から Application Insights に送信するデータを構成し、カスタマイズする方法を説明します。 関数アプリの場合、ログは [host.json] ファイルで構成されます。 

> [!NOTE]
> 特別に構成されたアプリケーション設定を使用すれば、特定の環境の host.json ファイル内の特定の設定を表すことができます。 これにより、プロジェクトに host.json ファイルを再発行しなくても、host.json 設定を効果的に変更できます。 詳細については、「[host.json 値をオーバーライドする](functions-host-json.md#override-hostjson-values)」を参照してください。

## <a name="configure-categories"></a>カテゴリを構成する

Azure Functions のロガーでは、すべてのログに *カテゴリ* があります。 カテゴリは、ランタイム コードや関数コードのどの部分にログが記述されているかを示します。 バージョン 1.x とそれ以降のバージョンとでは、カテゴリが異なります。 以下のチャートは、ランタイムが作成するログの主なカテゴリについて説明します。 

# <a name="v2x"></a>[v2.x+](#tab/v2)

| カテゴリ | テーブル | 説明 |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **dependencies**| 一部のサービスについては、依存関係データが自動的に収集されます。 正常に実行された場合、これらのログは `Information` レベルとなります。 詳細については、「[依存関係](functions-monitoring.md#dependencies)」を参照してください。 例外は `Error` レベルでログされます。 また、キュー メッセージが[有害キュー](functions-bindings-storage-queue-trigger.md#poison-messages)に送信されたといったような場合には、ランタイムによって `Warning` レベルのログも作成されます。 | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | C# および JavaScript SDK を使用すると、カスタム メトリックを収集し、カスタム イベントをログに記録することができます。 詳細については、「[カスタム テレメトリ データ](functions-monitoring.md#custom-telemetry-data)」を参照してください。|
| **`Function.<YOUR_FUNCTION_NAME>`** | **traces**| 特定の関数の実行について、Function started および completed のログが含まれます。 正常に実行された場合、これらのログは `Information` レベルとなります。 例外は `Error` レベルでログされます。 また、キュー メッセージが[有害キュー](functions-bindings-storage-queue-trigger.md#poison-messages)に送信されたといったような場合には、ランタイムによって `Warning` レベルのログも作成されます。 | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **traces**| ユーザーが生成したログ。任意のログ レベルとすることができます。 関数からのログへの書き込みの詳細については、「[ログへの書き込み](functions-monitoring.md#writing-to-logs)」を参照してください。 | 
| **`Host.Aggregator`** | **customMetrics** | これらのランタイム生成ログには、[構成可能な](#configure-the-aggregator)期間の関数呼び出しの回数と平均回数が記録されます。 既定の期間は、30 秒か 1,000 回のどちらか早い方です。 例としては、実行回数、成功率、時間などがあります。 これらすべてのログは `Information` レベルで書き込まれます。 `Warning` 以上でフィルターすると、このデータは表示されなくなります。 |
| **`Host.Results`** | **requests** | これらのランタイム生成ログから、関数の成功または失敗を確認できます。 これらすべてのログは `Information` レベルで書き込まれます。 `Warning` 以上でフィルターすると、このデータは表示されなくなります。 |
| **`Microsoft`** | **traces** | ホストによって起動される .NET ランタイム コンポーネントを反映する完全修飾ログ カテゴリ。  |
| **`Worker`** | **traces** | .NET 以外の言語用の言語ワーカー プロセスによって生成されるログ。 言語ワーカー ログは、`Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher` などの `Microsoft.*` カテゴリでログされる場合もあります。 これらのログは `Information` レベルで書き込まれます。|

> [!NOTE]
> .NET クラス ライブラリ関数の場合、これらのカテゴリでは、`ILogger<T>` ではなく `ILogger` を使用していることが想定されています。 詳細については、[関数 ILogger のドキュメント](functions-dotnet-class-library.md#ilogger)を参照してください。 

# <a name="v1x"></a>[v1.x](#tab/v1)

| カテゴリ | テーブル | 説明 |
| ----- | ----- | ----- |
| **`Function`** | **traces**| ユーザーが生成したログ。任意のログ レベルとすることができます。 関数からのログへの書き込みの詳細については、「[ログへの書き込み](functions-monitoring.md#writing-to-logs)」を参照してください。 | 
| **`Host.Aggregator`** | **customMetrics** | これらのランタイム生成ログには、[構成可能な](#configure-the-aggregator)期間の関数呼び出しの回数と平均回数が記録されます。 既定の期間は、30 秒か 1,000 回のどちらか早い方です。 例としては、実行回数、成功率、時間などがあります。 これらすべてのログは `Information` レベルで書き込まれます。 `Warning` 以上でフィルターすると、このデータは表示されなくなります。 |
| **`Host.Executor`** | **traces** | 特定の関数の実行について、**Function started** および **Function completed** ログを含みます。 正常に実行された場合、これらのログは `Information` レベルとなります。 例外は `Error` レベルで記録されます。 また、キュー メッセージが[有害キュー](functions-bindings-storage-queue-trigger.md#poison-messages)に送信されたといったような場合には、ランタイムによって `Warning` レベルのログも作成されます。  |
| **`Host.Results`** | **requests** | これらのランタイム生成ログから、関数の成功または失敗を確認できます。 これらすべてのログは `Information` レベルで書き込まれます。 `Warning` 以上でフィルターすると、このデータは表示されなくなります。 |

---

**テーブル** 列に、Application Insights のどのテーブルにログが書き込まれるかが示されます。 

## <a name="configure-log-levels"></a>ログ レベルを構成する

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

カテゴリごとに、送信する最小のログ レベルを指定します。 host.json の設定は、[Functions ランタイムのバージョン](functions-versions.md)によって異なります。 

以下の例では、次の規則に基づいてログ記録を定義します。

+ `Host.Results` または `Function` のログについては、`Error` 以上のレベルのイベントのみをログします。 
+ `Host.Aggregator` のログについては、生成されたすべてのメトリック (`Trace`) をログします。
+ その他のすべてのログ (ユーザー ログなど) については、`Information` レベル以上のイベントのみをログします。

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

[host.json] に、同じ文字列で始まる複数のログが含まれている場合は、より明確に定義されたログが最初に照合されます。 ランタイムで、`Host.Aggregator` を除き、`Error` レベルのすべてのものをログする次の例について考えてみます。

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

`None` というログ レベル設定を使用すれば、カテゴリのログが書き込まれないようにすることができます。 

## <a name="configure-the-aggregator"></a>アグリゲーターを構成する

前のセクションで述べたように、ランタイムでは期間内の関数実行についてデータが集計されます。 既定の期間は、30 秒か 1,000 回実行のどちらか早い方です。 [host.json] ファイル内でこの設定を構成できます。  次に例を示します。

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>サンプリングを構成する

Application Insights には、負荷がピークのときに、完了した実行に関してテレメトリ データが生成されすぎないようにする[サンプリング](../azure-monitor/app/sampling.md)機能があります。 Application Insights では、受信実行の割合が指定されたしきい値を超えると、受信した実行の一部がランダムに無視され始めます。 1 秒あたりの最大実行数の既定の設定は 20 (バージョン 1.x では 5) です。 [host.json](./functions-host-json.md#applicationinsights) でサンプリングを構成できます。  次に例を示します。

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


特定の種類のテレメトリをサンプリングから除外することができます。 この例では、`Request` および `Exception` 型のデータがサンプリングから除外されます。 これにより、"*すべての*" 関数実行 (要求) と例外が確実にログされる一方、他の種類のテレメトリはサンプリング対象のままとなります。 

# <a name="v1x"></a>[v1.x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

詳細については、「[Application Insights におけるサンプリング](../azure-monitor/app/sampling.md)」を参照してください。

## <a name="configure-scale-controller-logs"></a>スケール コントローラーのログを構成する

_この機能はプレビュー段階にあります。_ 

[Azure Functions スケール コントローラー](./event-driven-scaling.md#runtime-scaling)から Application Insights または BLOB ストレージにログを出力させることで、関数アプリに対してスケール コントローラーが行っている決定をより詳しく把握することができます。

この機能を有効にするには、`SCALE_CONTROLLER_LOGGING_ENABLED` という名前のアプリケーション設定を、ご利用の関数アプリの設定に追加します。 この設定の値は `<DESTINATION>:<VERBOSITY>` の形式にし、以下に基づいて指定する必要があります。

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

たとえば、次の Azure CLI コマンドを実行すると、スケール コントローラーから Application Insights への詳細ログ記録が有効になります。

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

この例では、`<FUNCTION_APP_NAME>` と `<RESOURCE_GROUP_NAME>` を実際の関数アプリの名前とリソース グループ名でそれぞれ置き換えます。 

次の Azure CLI コマンドでは、詳細度が `None` に設定されているため、ログ記録が無効になります。

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

また、次の Azure CLI コマンドを使用して `SCALE_CONTROLLER_LOGGING_ENABLED` 設定を削除することで、ログ記録を無効にすることもできます。

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

スケール コントローラーのログを有効にすると、[スケール コントローラーのログに対してクエリを実行](analyze-telemetry-data.md#query-scale-controller-logs)できるようになります。 

## <a name="enable-application-insights-integration"></a>Application Insights との統合を有効にする

関数アプリでデータを Application Insights に送信するには、Application Insights リソースのインストルメンテーション キーについて知っておく必要があります。 キーは、**APPINSIGHTS_INSTRUMENTATIONKEY** という名前のアプリ設定に指定されている必要があります。

[Azure portal](./functions-get-started.md) で、または [Azure Functions Core Tools](./create-first-function-cli-csharp.md) を使用してコマンド ラインから、あるいは [Visual Studio Code](./create-first-function-vs-code-csharp.md) を使用して関数アプリを作成すると、Application Insights 統合が既定で有効になります。 Application Insights リソースは関数アプリと同じ名前を持ち、同じリージョンまたは最も近いリージョンのどちらかで作成されます。

### <a name="new-function-app-in-the-portal"></a>ポータルでの新しい関数アプリ

作成されている Application Insights リソースを確認するには、それを選択して **[Application Insights]** ウィンドウを展開します。 **[新しいリソース名]** を変更するか、またはデータを格納する [Azure 地理的環境](https://azure.microsoft.com/global-infrastructure/geographies/)内の別の **[場所]** を選択することができます。

![関数アプリの作成時に Application Insights を有効にする](media/functions-monitoring/enable-ai-new-function-app.png)

**[作成]** を選択すると、関数アプリと共に Application Insights リソースが作成され、アプリケーション設定の `APPINSIGHTS_INSTRUMENTATIONKEY` が設定されます。 これで、すべて準備ができました。

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>既存の関数アプリに追加する 

ご利用の関数アプリで Application Insights リソースが作成されていない場合は、次の手順を使用してリソースを作成します。 その後、関数アプリの[アプリケーション設定](functions-how-to-use-azure-function-app-settings.md#settings)として、そのリソースからインストルメンテーション キーを追加できます。

1. [Azure portal](https://portal.azure.com) で、**関数アプリ** を検索して選択してから、対象の関数アプリを選択します。 

1. ウィンドウの上部にある **[Application Insights が構成されていません]** バナーを選択します。 このバナーが表示されない場合は、アプリで既に Application Insights が有効になっている可能性があります。

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="ポータルから Application Insights を有効にする":::

1. **[リソースの変更]** を展開し、下の表に指定されている設定を使用して、Application Insights リソースを作成します。  

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **新しいリソース名** | 一意のアプリ名 | 関数アプリと同じ名前を使用するのが最も簡単です。この名前は、サブスクリプション内で一意である必要があります。 | 
    | **場所** | 西ヨーロッパ | 可能であれば、お使いの関数アプリと同じ[リージョン](https://azure.microsoft.com/regions/)、または近隣のリージョンを使用してください。 |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Application Insights リソースを作成します":::。

1. **[適用]** を選択します。 

   Application Insights リソースは、関数アプリと同じリソース グループおよびサブスクリプションに作成されます。 リソースが作成されたら、Application Insights ウィンドウを閉じます。

1. 対象の関数アプリで、 **[設定]** の下の **[構成]** を選択し、 **[アプリケーション設定]** を選択します。 `APPINSIGHTS_INSTRUMENTATIONKEY` という設定が表示された場合は、Azure で実行されている関数アプリに対して Application Insights 統合が有効になっています。 何らかの理由でこの設定が存在しない場合は、Application Insights インストルメンテーション キーを使用してそれを値として追加します。

> [!NOTE]
> 初期バージョンの関数では組み込みの監視を使用していましたが、これは推奨されなくなりました。 このような関数アプリで Application Insights 統合を有効にする場合は、[組み込みログも無効にする](#disable-built-in-logging)必要があります。  

## <a name="disable-built-in-logging"></a>組み込みログを無効にする

Application Insights を有効にする場合は、Azure Storage を使用する組み込みログを無効にします。 組み込みログは軽量のワークロードには便利ですが、高負荷の実稼働環境での使用には向きません。 実稼働環境の監視には、Application Insights をお勧めします。 組み込みログを実稼働環境で使用すると、Azure Storage での調整のためにログ レコードが不完全になる場合があります。

組み込みログを無効にするには、`AzureWebJobsDashboard` アプリ設定を削除します。 Azure Portal でアプリ設定を削除する方法については、[関数アプリの管理方法](functions-how-to-use-azure-function-app-settings.md#settings)に関するページで「**アプリケーションの設定**」セクションを参照してください。 アプリ設定を削除する前に、同じ関数アプリの既存の関数によって、Azure Storage のトリガーまたはバインドにその設定が使用されていないことを確認してください。

## <a name="next-steps"></a>次のステップ

監視の詳細については、以下に関するページを参照してください。

+ [Azure Functions を監視する](functions-monitoring.md)
+ [Application Insights で Azure Functions のテレメトリ データを分析する](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.json]: functions-host-json.md
