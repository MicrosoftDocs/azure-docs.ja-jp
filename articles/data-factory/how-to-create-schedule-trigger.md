---
title: Azure Data Factory でのスケジュール トリガーの作成
description: スケジュールでパイプラインを実行するトリガーを Azure Data Factory で作成する方法について説明します。
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 4bf5e9e1e890b2f91377075c4c8b7c8ff6c50fa0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779853"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>スケジュールどおりにパイプラインを実行するトリガーの作成

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、スケジュール トリガーの概要と、スケジュール トリガーを作成、起動、監視する手順について説明します。 他の種類のトリガーについては、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。

スケジュール トリガーを作成するときは、トリガーのスケジュール (開始日、繰り返し、終了日など) を指定し、パイプラインと関連付けます。 パイプラインとトリガーには多対多の関係があります。 複数のトリガーが 1 つのパイプラインを開始することができます。 1 つのトリガーが複数のパイプラインを開始することもできます。

以下のセクションでは、さまざまな方法でスケジュール トリガーを作成する手順を説明します。 

## <a name="data-factory-ui"></a>Data Factory UI

**スケジュール トリガー** を作成して、パイプラインを定期的 (毎時、毎日など) に実行するようにスケジュールできます。 

> [!NOTE]
> パイプラインとスケジュール トリガーの作成に関する完全なチュートリアル (トリガーをパイプラインに関連付けて、パイプラインを実行および監視する) については、[Data Factory UI を使用したデータ ファクトリの作成に関するクイック スタート](quickstart-create-data-factory-portal.md)をご覧ください。

1. 鉛筆のシンボルを使用して表示された **[編集]** タブに切り替えます。 

    ![[編集] タブに切り替える](./media/how-to-create-schedule-trigger/switch-edit-tab.png)

1. メニューの **[トリガー]** を選択して、 **[New/Edit]\(新規作成/編集\)** を選択します。 

    ![新しいトリガーのメニュー](./media/how-to-create-schedule-trigger/new-trigger-menu.png)

1. **[Add Triggers]\(トリガーの追加\)** ページで、 **[Choose trigger]\(トリガーの選択\)** を選択してから、 **[+新規]** を選択します。 

    ![トリガーの追加 - 新しいトリガー](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)

1. **[新しいトリガー]** ページで、次の手順を行います。 

    1. **[種類]** で **[スケジュール]** が選択されていることを確認します。
    1. **[開始日]** にトリガーの開始日時を指定します。 既定では、現在の日時が協定世界時 (UTC) で設定されています。
    1. トリガーが作成されるタイム ゾーンを指定します。 タイム ゾーンの設定は、高度な繰り返しオプションの **[開始日]** 、 **[終了日]** 、 **[スケジュール実行回数]** に適用されます。 タイム ゾーンの設定を変更することで、開始日が自動的に変更されることはありません。 指定したタイム ゾーンにおいて、開始日が正しいことを確認してください。 トリガーのスケジュール化された実行時間は、開始日よりも後と見なされることに注意してください (開始日は、実行時間よりも少なくとも 1 分短くなるようにしてください。そうでないと、次回の繰り返しでパイプラインがトリガーされます)。 

        > [!NOTE]
        > 夏時間を観測するタイム ゾーンの場合、年 2 回の変更に対して、トリガー時間が自動的に調整されます。 夏時間の変更を無効にするには、UTC など、夏時間を観測しないタイムゾーンを選択してください

    1. トリガーの **繰り返し** を指定します。 ドロップダウン リストから値のいずれか (分単位、時間単位、日単位、週単位、月単位) を選択します。 テキスト ボックスに乗数を入力します。 たとえば、15 分に 1 回トリガーを実行する場合は、 **[1 分ごと]** を選択し、テキスト ボックスに「**15**」と入力します。 
    1. 終了日時を指定するには、 **[Specify an End Date]\(終了日の指定\)** を選択し、 _[終了日]_ を指定して、 **[OK]** を選択します。 各パイプライン実行に関連したコストがかかります。 テストする場合は、パイプラインが数回だけトリガーされるように設定してください。 ただし、発行時から終了時刻までにパイプラインを実行できる十分な時間があるようにします。 トリガーは、UI でトリガーを保存したときではなく、Data Factory にソリューションを発行した後で有効になります。

        ![トリガー設定](./media/how-to-create-schedule-trigger/trigger-settings-01.png)

        ![終了日のトリガー設定](./media/how-to-create-schedule-trigger/trigger-settings-02.png)

1. **[新しいトリガー]** ウィンドウで、 **[アクティブ化]** オプションの **[はい]** を選択し、 **[OK]** を選択します。 このチェック ボックスを使用して、トリガーを後で非アクティブ化できます。 

    ![トリガーの設定 - [次へ] ボタン](./media/how-to-create-schedule-trigger/trigger-settings-next.png)

1. **[新しいトリガー]** ウィンドウで警告メッセージを確認し、 **[OK]** を選択します。

    ![トリガーの設定 - [完了] ボタン](./media/how-to-create-schedule-trigger/new-trigger-finish.png)

1. **[すべて発行]** を選択して、変更を Data Factory に発行します。 変更を Data Factory に発行するまで、そのトリガーによって、パイプライン実行のトリガーは開始されません。 

    ![[発行] ボタン](./media/how-to-create-schedule-trigger/publish-2.png)

1. 左側の **[パイプラインの実行]** タブに切り替えてから、 **[更新]** を選択して一覧を更新します。 スケジュールされたトリガーによってトリガーされたパイプライン実行が表示されます。 **[トリガー元]** 列の値に注意してください。 **[Trigger Now]\(今すぐトリガー\)** を使用すると、一覧に手動のトリガー実行が表示されます。 

    ![トリガーされた実行を監視する](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)

1. **[Trigger Runs]\(トリガーの実行\)**  \  **[スケジュール]** ビューに切り替えます。 

    ![トリガーの実行を監視する](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このセクションでは、Azure PowerShell を使用してスケジュール トリガーを作成、起動、監視する方法について説明します。 このサンプルの動作を確認するには、まず[Azure PowerShell を使用してデータ ファクトリを作成する方法のクイック スタート](quickstart-create-data-factory-powershell.md)に関する記事を参照してください。 続いて、メインのメソッドに以下のコードを追加します。このコードは、15 分ごとに実行するスケジュールのトリガーを作成して起動します。 このトリガーは、クイック スタートの一環として作成する **Adfv2QuickStartPipeline** という名前のパイプラインに関連付けられます。

1. 次の内容が含まれた **MyTrigger.json** という名前の JSON ファイルを C:\ADFv2QuickStartPSH\ フォルダーに作成します。

    > [!IMPORTANT]
    > JSON ファイルを保存する前に、**startTime** 要素の値を現在の UTC 時間に設定します。 **endTime** 要素の値を現在の UTC 時間の 1 時間後に設定します。

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00Z",
                    "endTime": "2017-12-08T01:00:00Z",
                    "timeZone": "UTC"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    JSON スニペットにおける設定は以下のとおりです。
    - トリガーの **type** 要素が "ScheduleTrigger" に設定されています。
    - **frequency** 要素が "Minute" に設定され、**interval** 要素が 15 に設定されています。 そのため、トリガーは開始時刻と終了時刻の間で 15 分ごとにパイプラインを実行します。
    - **timeZone** 要素では、トリガーが作成されるタイム ゾーンを指定します。 この設定は、**startTime** と **endTime** の両方に影響します。
    - **endTime** 要素は、**startTime** 要素の値の 1 時間後になっています。 そのため、トリガーは開始時刻の 15 分後、30 分後、45 分後にパイプラインを実行します。 必ず、開始時刻を現在の UTC 時間に更新し、終了時刻を開始時刻の 1 時間後に更新してください。 

        > [!IMPORTANT]
        > UTC タイム ゾーンでは、startTime と endTime は "yyyy-MM-ddTHH:mm:ss **Z**" の形式に従う必要がありますが、他のタイム ゾーンでは、startTime と endTime は "yyyy-MM-ddTHH:mm:ss" に従います。 
        > 
        > ISO 8601 標準により、タイムスタンプへの _Z_ サフィックスは、日時を UTC タイム ゾーンとしてマークし、timeZone フィールドを使用不可にします。 UTC タイム ゾーンの _Z_ サフィックスが欠落していると、トリガーの "_アクティブ化_" でエラーが発生します。

    - トリガーは、**Adfv2QuickStartPipeline** パイプラインに関連付けられます。 複数のパイプラインをトリガーに関連付けるには、**pipelineReference** セクションを追加します。
    - クイック スタートのパイプラインは、**inputPath** と **outputPath** の 2 つの **パラメーター** の値を受け取ります。 そのため、これらのパラメーターの値をトリガーから渡します。

1. **Set-AzDataFactoryV2Trigger** コマンドレットを使用してトリガーを作成します。

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

1. **Get-AzDataFactoryV2Trigger** コマンドレットを使用して、トリガーの状態が **Stopped** であることを確認します。

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. **Start-AzDataFactoryV2Trigger** コマンドレットを使用してトリガーを起動します。

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. **Get-AzDataFactoryV2Trigger** コマンドレットを使用して、トリガーの状態が **Started** であることを確認します。

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1.  **Get-AzDataFactoryV2TriggerRun** コマンドレットを使用して、Azure PowerShell でトリガー実行を取得します。 トリガー実行に関する情報を取得するには、次のコマンドを定期的に実行します。 トリガー定義の値に合わせて、**TriggerRunStartedAfter** と **TriggerRunStartedBefore** の値を更新します。

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    > [!NOTE]
    > スケジュール トリガーのトリガー時間は UTC タイムスタンプで指定します。 _TriggerRunStartedAfter_ と _TriggerRunStartedBefore_ でも UTC タイムスタンプが想定されます

    Azure Portal でトリガー実行とパイプライン実行を監視するには、[パイプライン実行の監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)に関するセクションをご覧ください。

## <a name="net-sdk"></a>.NET SDK

このセクションでは、.NET SDK を使用してトリガーを作成、起動、監視する方法について説明します。 このサンプルの動作を確認するには、まず[.NET SDK を使用してデータ ファクトリを作成する方法のクイック スタート](quickstart-create-data-factory-dot-net.md)に関する記事を参照してください。 続いて、メインのメソッドに以下のコードを追加します。このコードは、15 分ごとに実行するスケジュールのトリガーを作成して起動します。 このトリガーは、クイック スタートの一環として作成する **Adfv2QuickStartPipeline** という名前のパイプラインに関連付けられます。

15 分ごとに実行されるスケジュール トリガーを作成して起動するには、メイン メソッドに次のコードを追加します。

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

UTC 以外の別のタイムゾーンでトリガーを作成するには、次の設定が必要です。
```csharp
<<ClientInstance>>.SerializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.SerializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
<<ClientInstance>>.SerializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.DeserializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
```

トリガー実行を監視するには、サンプルの最後の `Console.WriteLine` ステートメントの前に次のコードを追加します。

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Azure Portal でトリガー実行とパイプライン実行を監視するには、[パイプライン実行の監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)に関するセクションをご覧ください。

## <a name="python-sdk"></a>Python SDK

このセクションでは、Python SDK を使用してトリガーを作成、起動、監視する方法について説明します。 このサンプルの動作を確認するには、まず[Python SDK を使用したデータ ファクトリの作成に関するクイック スタート](quickstart-create-data-factory-python.md)に目を通します。 次に、Python スクリプトの "パイプライン実行を監視する" コード ブロックの後に次のコード ブロックを追加します。 このコードは、指定された開始時刻と終了時刻の間で 15 分ごとに実行するスケジュールのトリガーを作成します。 **start_time** 変数を現在の UTC 時間に更新し、**end_time** 変数を現在の UTC 時間の 1 時間後に更新します。

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00Z', end_time='2017-12-12T05:00:00Z', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Azure Portal でトリガー実行とパイプライン実行を監視するには、[パイプライン実行の監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)に関するセクションをご覧ください。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

Azure Resource Manager テンプレートを使用してトリガーを作成できます。 詳しい手順については、[Resource Manager テンプレートを使用した Azure データ ファクトリの作成](quickstart-create-data-factory-resource-manager-template.md)に関する記事をご覧ください。  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>トリガーの開始時刻をパイプラインに渡す

Azure Data Factory バージョン 1 では、次の各システム変数を使用することによって、パーティション分割されたデータの読み取りと書き込みをサポートします:**SliceStart**、**SliceEnd**、**WindowStart**、**WindowEnd**。 現在のバージョンの Azure Data Factory では、パイプライン パラメーターを使用してこの動作を実現できます。 トリガーの開始時刻とスケジュールされた時刻を、パイプライン パラメーターの値として設定します。 次の例では、トリガーのスケジュールされた時刻が、**scheduledRunTime** パイプライン パラメーターに値として渡されます。

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>JSON スキーマ

次の JSON 定義は、スケジュールと繰り返しを指定してスケジュール トリガーを作成する方法を示しています。

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  **parameters** プロパティは、**pipelines** 要素の必須プロパティです。 パイプラインにパラメーターがない場合は、**parameters** プロパティ用に空の JSON 定義を含める必要があります。


### <a name="schema-overview"></a>スキーマの概要

次の表に、トリガーの繰り返しとスケジュール設定に関連する主要なスキーマ要素の概要を示します。

| JSON プロパティ | 説明 |
|:--- |:--- |
| **startTime** | 日付/時刻の値。 単純なスケジュールの場合、**startTime** プロパティの値が最初の発生日時に適用されます。 複雑なスケジュールの場合、指定した **startTime** 値になるとすぐにトリガーが起動します。 <br> UTC タイム ゾーンの場合の形式は `'yyyy-MM-ddTHH:mm:ssZ'` で、その他のタイムゾーンの場合の形式は `'yyyy-MM-ddTHH:mm:ss'` です。 |
| **endTime** | トリガーの終了日時。 指定した終了日時を過ぎると、トリガーは実行されません。 このプロパティの値に過去の日時を指定することはできません。 このプロパティは省略可能です。  <br> UTC タイム ゾーンの場合の形式は `'yyyy-MM-ddTHH:mm:ssZ'` で、その他のタイムゾーンの場合の形式は `'yyyy-MM-ddTHH:mm:ss'` です。 |
| **timeZone** | トリガーが作成されるタイム ゾーン。 この設定は **startTime**、**endTime**、**schedule** に影響します。 [サポートされているタイム ゾーンの一覧](#time-zone-option)を参照してください |
| **recurrence** | トリガーの繰り返し規則を指定する recurrence オブジェクト。 この recurrence オブジェクトは、**frequency**、**interval** **endTime** **count**、**schedule** の各要素をサポートします。 recurrence オブジェクトを定義する場合、**frequency** 要素は必須です。 recurrence オブジェクトの他の要素は省略可能です。 |
| **frequency** | トリガーが繰り返される頻度の単位。 サポートされる値には、"minute"、"hour"、"day"、"week"、"month" があります。 |
| **interval** | トリガーの実行頻度を決定する、**frequency** 値の間隔を示す正の整数。 たとえば、**interval** が 3 で **frequency** が "week" の場合、トリガーは 3 週間ごとに繰り返されます。 |
| **schedule** | トリガーの繰り返しのスケジュール。 **frequency** 値が指定されたトリガーは、繰り返しのスケジュールに基づいて繰り返しが変更されます。 **schedule** プロパティには、分、時間、曜日、日にち、週番号に基づいた繰り返しの変更を指定します。

> [!IMPORTANT]
> UTC タイム ゾーンでは、startTime と endTime は "yyyy-MM-ddTHH:mm:ss **Z**" の形式に従う必要がありますが、他のタイム ゾーンでは、startTime と endTime は "yyyy-MM-ddTHH:mm:ss" に従います。 
> 
> ISO 8601 標準により、タイムスタンプへの _Z_ サフィックスは、日時を UTC タイム ゾーンとしてマークし、timeZone フィールドを使用不可にします。 UTC タイム ゾーンの _Z_ サフィックスが欠落していると、トリガーの "_アクティブ化_" でエラーが発生します。

### <a name="schema-defaults-limits-and-examples"></a>スキーマの既定値、制限、例

| JSON プロパティ | Type | 必須 | 既定値 | 有効な値 | 例 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | String | はい | なし | ISO-8601 の日付/時刻 | UTC タイムゾーンの場合は `"startTime" : "2013-01-09T09:30:00-08:00Z"`、 <br> その他のタイム ゾーンの場合は `"2013-01-09T09:30:00-08:00"` |
| **timeZone** | String | はい | なし | [タイム ゾーン値](#time-zone-option)  | `"UTC"` |
| **recurrence** | Object | はい | なし | recurrence オブジェクト | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Number | いいえ | 1 | 1 ～ 1,000 | `"interval":10` |
| **endTime** | String | はい | なし | 将来の時刻を表す日付/時刻の値 | UTC タイムゾーンの場合は `"endTime" : "2013-02-09T09:30:00-08:00Z"`、 <br> その他のタイム ゾーンの場合は `"endTime" : "2013-02-09T09:30:00-08:00"`|
| **schedule** | Object | いいえ | なし | schedule オブジェクト | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="time-zone-option"></a>タイム ゾーン オプション

スケジュール トリガーでサポートされているタイム ゾーンの一部を次に示します。

| タイム ゾーン | UTC オフセット (夏時間なし) | timeZone 値 | 夏時間を観測する | タイムスタンプ形式 |
| :--- | :--- | :--- | :--- | :--- |
| 協定世界時 | 0 | `UTC` | いいえ | `'yyyy-MM-ddTHH:mm:ssZ'`|
| 太平洋標準時 (PT) | -8 | `Pacific Standard Time` | はい | `'yyyy-MM-ddTHH:mm:ss'` |
| 中部標準時 (CT) | -6 | `Central Standard Time` | はい | `'yyyy-MM-ddTHH:mm:ss'` |
| 東部標準時 (ET) | -5 | `Eastern Standard Time` | はい | `'yyyy-MM-ddTHH:mm:ss'` |
| グリニッジ標準時 (GMT) | 0 | `GMT Standard Time` | はい | `'yyyy-MM-ddTHH:mm:ss'` |
| 中央ヨーロッパ標準時 | +1 | `W. Europe Standard Time` | はい | `'yyyy-MM-ddTHH:mm:ss'` |
| インド標準時 (IST) | +5:30 | `India Standard Time` | いいえ | `'yyyy-MM-ddTHH:mm:ss'` |
| 中国標準時 | +8 | `China Standard Time` | いいえ | `'yyyy-MM-ddTHH:mm:ss'` |

この一覧は完全なものではありません。 タイム ゾーン オプションの完全な一覧については、Data Factory ポータルの[トリガー作成ページ](#data-factory-ui)を閲覧してください

### <a name="starttime-property"></a>startTime プロパティ
次の表に、**startTime** プロパティでトリガー実行を制御する方法を示します。

| startTime の値 | スケジュールなしの繰り返し | スケジュールありの繰り返し |
|:--- |:--- |:--- |
| 開始時刻が過去に設定されている | 開始時刻より後の将来の最初の実行時刻を計算し、その時刻に実行されます。<br/><br/>以降は、前回の実行時刻からの計算に基づいて実行されます。<br/><br/>この表の後の例を参照してください。 | トリガーは、指定した開始時刻になると "_すぐに_" 起動します。 最初の発生は、開始時刻から計算されたスケジュールに基づきます。<br/><br/>以降は、繰り返しのスケジュールに基づいて実行されます。 |
| 開始時刻が将来または現在に設定されている | 指定した開始時刻に 1 回実行されます。<br/><br/>以降は、前回の実行時刻からの計算に基づいて実行されます。 | トリガーは、指定した開始時刻になると "_すぐに_" 起動します。 最初の発生は、開始時刻から計算されたスケジュールに基づきます。<br/><br/>以降は、繰り返しのスケジュールに基づいて実行されます。 |

ここでは、開始時刻が過去であり、スケジュールなしの繰り返しが設定されている場合の動作の例を説明します。 現在の時刻が `2017-04-08 13:00`、開始時刻が `2017-04-07 14:00`、繰り返しが 2 日ごとであると仮定します  (**recurrence** 値を定義するには、**frequency** プロパティを "day"、**interval** プロパティを 2 に設定します)。**startTime** 値が過去であり、現在の時刻よりも前であることに注意してください。

この条件では、最初の実行は `2017-04-09` の `14:00` になります。 Scheduler エンジンは、開始時刻から実行を計算します。 過去のインスタンスはすべて破棄されます。 エンジンは、将来発生する次回のインスタンスを使用します。 このシナリオでは、開始時刻が `2017-04-07` の `2:00pm` であるため、次回のインスタンスはその 2 日後、つまり `2017-04-09` の `2:00pm` になります。

**startTime** 値が `2017-04-05 14:00` または `2017-04-01 14:00` であっても、最初の実行時刻は同じです。 最初の実行後、以降の実行はスケジュールを使用して計算されます。 したがって、以降の実行は、`2017-04-11` の `2:00pm`、`2017-04-13` の `2:00pm`、`2017-04-15` の `2:00pm` の順に続きます。

最後に、トリガーのスケジュールに時間または分が設定されていない場合は、最初の実行の時間または分が既定値として使用されます。

### <a name="schedule-property"></a>schedule プロパティ

一方で、schedule を使用することで、トリガーの実行回数を制限できます。 たとえば、月単位の頻度を指定したトリガーを 31 日にのみ実行するようにスケジュールすると、トリガーは 31 日がある月にのみ実行されます。

他方、schedule によりトリガーの実行回数を増やすこともできます。 たとえば、月単位の頻度を指定したトリガーを月の 1 日と 2 日に実行するようにスケジュールすると、トリガーは月に 1 回ではなく、月の 1 日と 2 日に実行されます。

**schedule** の複数の要素を指定した場合、最も大きいスケジュール設定から最も小さいスケジュール設定の順序で評価されます。 まず週番号が評価され、日にち、曜日、時間、分の順に評価されます。

次の表に、**schedule** の要素の詳細を示します。

| JSON 要素 | 説明 | 有効な値 |
|:--- |:--- |:--- |
| **分** | トリガーを実行する時刻 (分)。 | <ul><li>Integer</li><li>整数の配列</li></ul>
| **hours** | トリガーを実行する時刻 (時)。 | <ul><li>Integer</li><li>整数の配列</li></ul> |
| **weekDays** | トリガーが実行される曜日。 この値を指定できるのは、頻度が週単位の場合のみです。 | <ul><li>Monday、Tuesday、Wednesday、Thursday、Friday、Saturday、Sunday</li><li>曜日の値の配列 (配列の最大サイズは 7)</li><li>曜日の値の大文字小文字は区別されません</li></ul> |
| **monthlyOccurrences** | トリガーが実行される月の特定曜日。 この値を指定できるのは、頻度が月単位の場合のみです。 | <ul><li>**monthlyOccurrence** オブジェクトの配列: `{ "day": day,  "occurrence": occurrence }`。</li><li>**day** 属性は、トリガーが実行される曜日を表します。 たとえば、**monthlyOccurrences** プロパティの **day** 値が `{Sunday}` の場合は、月の毎週日曜日を意味します。 **day** 属性は必須です。</li><li>**occurrence** 属性は、月の指定した **day** の出現を表します。 たとえば、**monthlyOccurrences** プロパティの **day** 値と **occurrence** 値が `{Sunday, -1}` の場合、月の最後の日曜日を意味します。 **occurrence** 属性は省略可能です。</li></ul> |
| **monthDays** | トリガーが実行される日にち。 この値を指定できるのは、頻度が月単位の場合のみです。 | <ul><li>-1 以下かつ -31 以上の任意の値</li><li>1 以上かつ 31 以下の任意の値</li><li>値の配列</li></ul> |

## <a name="examples-of-trigger-recurrence-schedules"></a>トリガーの繰り返しのスケジュールの例

このセクションでは、**schedule** オブジェクトとその要素に注目して、繰り返しのスケジュールの例を示します。

各例は、**interval** 値が 1 であり、schedule の定義に従って **frequency** に適切な値が指定されていることを前提としています。 たとえば、**frequency** 値に "day" を指定し、**schedule** オブジェクトで "monthDays" の変更を指定することはできません。 このような制限事項は、前のセクションの表に記載されています。

| 例 | 説明 |
|:--- |:--- |
| `{"hours":[5]}` | 毎日午前 5 時に実行されます。 |
| `{"minutes":[15], "hours":[5]}` | 毎日午前 5 時 15 分に実行されます。 |
| `{"minutes":[15], "hours":[5,17]}` | 毎日午前 5 時 15 分と午後 5 時 15 分に実行されます。 |
| `{"minutes":[15,45], "hours":[5,17]}` | 毎日午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行されます。 |
| `{"minutes":[0,15,30,45]}` | 15 分ごとに実行されます。 |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 1 時間ごとに実行されます。 このトリガーは 1 時間おきに実行されます。 **startTime** 値が指定されている場合、分はこの値によって制御されます。 値が指定されていない場合は、作成時刻によって制御されます。 たとえば、開始時刻または作成時刻が (どちらが適用される場合でも) 午後 12 時 25 分の場合、トリガーは 0 時 25 分、1 時 25 分、2 時 25 分...23 時 25 分に実行されます。<br/><br/>このスケジュールは、**frequency** 値に "hour"、**interval** 値に 1 を指定し、**schedule** を指定しないトリガーに相当します。  **frequency** と **interval** に別の値を指定してこのスケジュールを使用すると、他のトリガーを作成できます。 たとえば、**frequency** 値が "month" の場合は、月に 1 回だけ実行され、**frequency** 値が "day" の場合は、毎日実行されます。 |
| `{"minutes":[0]}` | 毎正時に実行されます。 このトリガーは、毎正時 (午前 0 時、午前 1 時、午前 2 時 (以降も同様)) に実行されます。<br/><br/>このスケジュールは、**frequency** 値に "hour"、**startTime** 値に 0 分を指定したトリガー、または **schedule** を設定せずに **frequency** 値に "day" を指定したトリガーに相当します。 **frequency** 値が "week" または "month" の場合、スケジュールはそれぞれ週に 1 日または月に 1 日だけ実行されます。 |
| `{"minutes":[15]}` | 毎正時から 15 分後に実行されます。 このトリガーは、毎正時から 15 分後 (午前 0 時 15 分、午前 1 時 15 分、午前 2 時 15 分 (以降も同様)、最後が午後 11 時 15 分) に実行されます。 |
| `{"hours":[17], "weekDays":["saturday"]}` | 毎週土曜日の午後 5 時に実行されます。 |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 毎週月曜日、水曜日、金曜日の午後 5 時に実行されます。 |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 毎週月曜日、水曜日、金曜日の午後 5 時 15 分と午後 5 時 45 分に実行されます。 |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 平日に 15 分ごとに実行されます。 |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 平日の午前 9 時から午後 4 時 45 分まで 15 分ごとに実行されます。 |
| `{"weekDays":["tuesday", "thursday"]}` | 火曜日と木曜日の指定された開始時刻に実行されます。 |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | 毎月 28 日の午前 6 時に実行されます (**frequency** 値が "month" であることを想定)。 |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 月の最終日の午前 6 時に実行されます。 月の最終日にトリガーを実行するには、28、29、30、31 という日にちではなく -1 を使用します。 |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 毎月最初と最後の日の午前 6 時に実行されます。 |
| `{monthDays":[1,14]}` | 毎月 1 日と 14 日の指定された開始時刻に実行されます。 |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 毎月最初の金曜日の午前 5 時に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 毎月最初の金曜日の指定された開始時刻に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 毎月、月の最後から 3 番目の金曜日の指定された開始時刻に実行されます。 |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 毎月最初と最後の金曜日の午前 5 時 15 分に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 毎月最初と最後の金曜日の指定された開始時刻に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 毎月第 5 金曜日の指定された開始時刻に実行されます。 第 5 金曜日にのみ実行するようにスケジュールされているため、月に第 5 金曜日がない場合、パイプラインは実行されません。 月の最後の金曜日にトリガーを実行する場合は、**occurrence** 値に 5 ではなく -1 を使用することを検討してください。 |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 月の最後の金曜日に 15 分ごとに実行されます。 |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 毎月第 3 水曜日の午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行されます。 |

## <a name="next-steps"></a>次のステップ

- トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#trigger-execution)」をご覧ください。
- パイプラインでトリガー メタデータを参照する方法については、[パイプライン実行でのトリガー メタデータの参照](how-to-use-trigger-parameterization.md)に関する記事を参照してください
