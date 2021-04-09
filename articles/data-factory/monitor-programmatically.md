---
title: Azure Data Factory をプログラムで監視する
description: さまざまなソフトウェア開発キット (SDK) を使用して、データ ファクトリのパイプラインを監視する方法を説明します。
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/16/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 6c913c7c623c77baea0c575d06d2c44709af43fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740443"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Azure Data Factory をプログラムで監視する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、さまざまなソフトウェア開発キット (SDK) を使用して、データ ファクトリのパイプラインを監視する方法について説明します。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>データの範囲

Data Factory では、パイプラインの実行データを 45 日間だけ格納します。 Data Factory パイプラインの実行に関するデータに対し、プログラムによってクエリを実行する場合 (たとえば、PowerShell コマンド `Get-AzDataFactoryV2PipelineRun` を使用して)、省略可能な `LastUpdatedAfter` パラメーターおよび `LastUpdatedBefore` パラメーターには日付の制限がありません。 ただし、たとえば、過去 1 年間のデータに対してクエリを実行した場合、エラーは発生しませんが、過去 45 日間のパイプライン実行データのみとなります。

過去 45 日より前のパイプライン実行データを保持する場合は、[Azure Monitor](monitor-using-azure-monitor.md) を使用して独自の診断ログを設定する必要があります。

## <a name="pipeline-run-information"></a>パイプライン実行情報

パイプライン実行プロパティについては、[PipelineRun API リファレンス](/rest/api/datafactory/pipelineruns/get#pipelinerun)を参照してください。 パイプライン実行は、そのライフサイクル中にさまざまな状態になります。考えられる実行状態の値を次に示します。

* キューに登録済み
* InProgress
* 成功
* 失敗
* Canceling
* Canceled

## <a name="net"></a>.NET
.NET SDK を使用して、パイプラインを作成し監視する完全なチュートリアルについては、[.NET を使用したデータ ファクトリとパイプラインの作成](quickstart-create-data-factory-dot-net.md)に関する記事をご覧ください。

1. データのコピーが完了するまでパイプラインの実行の状態を継続的にチェックする次のコードを追加します。

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. コピー アクティビティの実行の詳細 (たとえば、読み書きされたデータのサイズ) を取得する次のコードを追加します。

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

.NET SDK の詳細については、[Data Factory .NET SDK リファレンス](/dotnet/api/microsoft.azure.management.datafactory)に関するページをご覧ください。

## <a name="python"></a>Python
Python SDK を使用して、パイプラインを作成し監視する完全なチュートリアルについては、「[Python を使用してデータ ファクトリとパイプラインを作成する](quickstart-create-data-factory-python.md)」をご覧ください。

パイプラインの実行を監視するには、次のコードを追加します。

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Python SDK の詳細については、[データ ファクトリの Python SDK リファレンス](/python/api/overview/azure/datafactory)に関するページをご覧ください。

## <a name="rest-api"></a>REST API
REST API を使用して、パイプラインを作成し監視する完全なチュートリアルについては、[REST API を使用したデータ ファクトリとパイプラインの作成](quickstart-create-data-factory-rest-api.md)に関するページをご覧ください。
 
1. 次のスクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") ) {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. 次のスクリプトを実行し、コピー アクティビティの実行の詳細 (たとえば、読み書きされたデータのサイズ) を取得します。

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

REST API の詳細については、[データ ファクトリの REST API リファレンス](/rest/api/datafactory/)に関するページをご覧ください。

## <a name="powershell"></a>PowerShell
PowerShell を使用して、パイプラインを作成し監視する完全なチュートリアルについては、「[PowerShell を使用してデータ ファクトリとパイプラインを作成する](quickstart-create-data-factory-powershell.md)」をご覧ください。

1. 次のスクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ( ($run.Status -ne "InProgress") -and ($run.Status -ne "Queued") ) {
                Write-Output ("Pipeline run finished. The status is: " +  $run.Status)
                $run
                break
            }
            Write-Output ("Pipeline is running...status: " + $run.Status)
        }

        Start-Sleep -Seconds 30
    }
    ```
2. 次のスクリプトを実行し、コピー アクティビティの実行の詳細 (たとえば、読み書きされたデータのサイズ) を取得します。

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

PowerShell コマンドレットの詳細については、[データ ファクトリの PowerShell コマンドレット リファレンス](/powershell/module/az.datafactory)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
Azure Monitor を使って Data Factory のパイプラインを監視する方法については、[Azure Monitor を使ったパイプラインの監視](monitor-using-azure-monitor.md)に関する記事をご覧ください。