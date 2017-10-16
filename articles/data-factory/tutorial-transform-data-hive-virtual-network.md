---
title: "Azure Virtual Network で Hive を使用してデータを変換する | Microsoft Docs"
description: "このチュートリアルでは、Azure Data Factory で Hive アクティビティを使用してデータを変換するための詳細な手順を説明します。"
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/10/2017
ms.author: shengc
ms.openlocfilehash: ec472ea9f45a4c8bc18d065001eae381a275d185
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Azure Data Factory で Hive アクティビティを使用して Azure Virtual Network のデータを変換する
Azure Data Factory は、データドリブン型のワークフローをクラウドに作成することでデータの移動と変換を制御し、自動化することができるクラウドベースのデータ統合サービスです。 Azure Data Factory を使えば、データ主導型のワークフロー (パイプライン) を作成し、スケジューリングできます。具体的には、各種データ ストアからデータを取り込む、そのデータを各種コンピューティング サービス (Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning など) で処理/変換する、データ ストア (Azure SQL Data Warehouse など) に出力データを公開して、それを利用するビジネス インテリジェンス (BI) アプリケーションに提供するという一連の処理を行えるワークフローです。 

このチュートリアルでは、Azure PowerShell を使用して Data Factory パイプラインを作成します。このパイプラインで、Azure 仮想ネットワークにある HDInsight クラスター上の Hive アクティビティを使用してデータを変換します。 このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。 
> * セルフホステッド統合ランタイムを作成して設定します
> * リンクされたサービスを作成してデプロイします。
> * Hive アクティビティが含まれたパイプラインを作成してデプロイします。
> * パイプラインの実行を開始します。
> * パイプラインの実行を監視します 
> * 出力を検証します。 


Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件
- **Azure Storage アカウント**。 Hive スクリプトを作成し、Azure ストレージにアップロードします。 Hive スクリプトからの出力は、このストレージ アカウントに格納されます。 このサンプルでは、この Azure ストレージ アカウントがプライマリ ストレージとして HDInsight クラスターによって使用されます。 
- **Azure Virtual Network。** Azure 仮想ネットワークを持っていない場合は、[こちらの手順](../virtual-network/virtual-network-get-started-vnet-subnet.md)に従って作成してください。 このサンプルでは、HDInsight は Azure 仮想ネットワーク内にあります。 Azure Virtual Network の構成例を次に示します。 

    ![Create virtual network](media/tutorial-transform-data-using-hive-in-vnet/create-virtual-network.png)
- **HDInsight クラスター。** HDInsight クラスターを作成し、前の手順で作成した仮想ネットワークに参加させます。手順については、「[Azure Virtual Network を使用した Azure HDInsight の拡張](../hdinsight/hdinsight-extend-hadoop-virtual-network.md)」を参照してください。 仮想ネットワークでの HDInsight の構成例を次に示します。 

    ![仮想ネットワークでの HDInsight](media/tutorial-transform-data-using-hive-in-vnet/hdinsight-in-vnet-configuration.png)
- **Azure PowerShell**。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページに記載されている手順に従います。

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Hive スクリプトを BLOB ストレージ アカウントにアップロードする

1. 次の内容で、**hivescript.hql** という名前の Hive SQL ファイルを作成します。

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Azure BLOB ストレージで、**adftutorial** という名前のコンテナーを作成します (存在しない場合)。
3. `hivescripts` という名前のフォルダーを作成します。
4. `hivescript.hql` ファイルを `hivescripts` サブフォルダーにアップロードします。

 

## <a name="create-a-data-factory"></a>Data Factory を作成する。


1. 変数を 1 つずつ設定します。

    ```powershell
    $subscriptionID = "<subscription ID>" # Your Azure subscription ID
    $resourceGroupName = "ADFTutorialResourceGroup" # Name of the resource group
    $dataFactoryName = "MyDataFactory09142017" # Globally unique name of the data factory
    $pipelineName = "MyHivePipeline" # Name of the pipeline
    $selfHostedIntegrationRuntimeName = "MySelfHostedIR09142017" # make it a unique name. 
    ```
2. **PowerShell**を起動します。 Azure PowerShell は、このクイックスタートが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

    次のコマンドを実行して、Azure Portal へのサインインに使用するユーザー名とパスワードを入力します。
        
    ```powershell
    Login-AzureRmAccount
    ```        
    次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```powershell
    Get-AzureRmSubscription
    ```
    次のコマンドを実行して、使用するサブスクリプションを選択します。 **SubscriptionId** は、実際の Azure サブスクリプションの ID に置き換えてください。

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. サブスクリプションにまだリソース グループ ADFTutorialResourceGroup が存在しない場合は、作成します。 

    ```powershell
    New-AzureRmResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. データ ファクトリを作成します。 

    ```powershell
     $df = Set-AzureRmDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    次のコマンドを実行して、出力を表示します。 

    ```powershell
    $df
    ```

## <a name="create-self-hosted-ir"></a>セルフホステッド IR を作成する
このセクションでは、セルフホステッド統合ランタイムを作成し、HDInsight クラスターがある Azure 仮想ネットワーク内の Azure VM に関連付けます。

1. セルフホステッド統合ランタイムを作成します。 他の統合ランタイムと競合しない一意の名前を使用してください。

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted
   ```
    このコマンドでは、セルフホステッド統合ランタイムの論理登録が作成されます。 
2. PowerShell を使用して、セルフホステッド統合ランタイムを登録するための認証キーを取得します。 セルフホステッド統合ランタイムの登録に使用するいずれかのキーをコピーします。

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName | ConvertTo-Json
   ```

   出力例を次に示します。 

   ```powershell
   {
       "AuthKey1":  "IR@0000000000000000000000000000000000000=",
       "AuthKey2":  "IR@0000000000000000000000000000000000000="
   }
   ```
    **AuthKey1** の値をメモしておきます。引用符は含めません。 
3. Azure VM を作成し、HDInsight クラスターが含まれている仮想ネットワークに参加させます。 詳細については、[仮想マシンの作成方法](../virtual-network/virtual-network-get-started-vnet-subnet.md#create-vms)に関するページを参照してください。 Azure 仮想ネットワークに参加させます。 
4. Azure VM で、[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)をダウンロードします。 前の手順で取得した認証キーを使用して、セルフホステッド統合ランタイムを手動で登録します。 

   ![統合ランタイムの登録](media/tutorial-transform-data-using-hive-in-vnet/register-integration-runtime.png)

   セルフホステッド統合ランタイムが正常に登録されると、次のメッセージが表示されます。![正常に登録](media/tutorial-transform-data-using-hive-in-vnet/registered-successfully.png)

   ノードがクラウド サービスに接続されると、次のページが表示されます。![ノード接続済み](media/tutorial-transform-data-using-hive-in-vnet/node-is-connected.png)

## <a name="author-linked-services"></a>リンクされたサービスを作成する

このセクションでは、次の 2 つのリンクされたサービスを作成してデプロイします。
- Azure ストレージ アカウントをデータ ファクトリにリンクする、Azure Storage のリンクされたサービス。 このストレージは、HDInsight クラスターによって使用されるプライマリ ストレージです。 ここでは、Hive スクリプトとスクリプトの出力を保持するためにも、この Azure ストレージ アカウントを使用します。
- HDInsight のリンクされたサービス。 Azure Data Factory は、Hive スクリプトを実行するために、スクリプトをこの HDInsight クラスターに送信します。

### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス

任意のエディターを使用して JSON ファイルを作成し、Azure Storage のリンクされたサービスから次の JSON 定義をコピーして、ファイルを **MyStorageLinkedService.json** として保存します。

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }  
    }
}
```

**&lt;accountname&gt; と &lt;accountkey&gt;** を Azure ストレージ アカウントの名前とキーに置き換えます。

### <a name="hdinsight-linked-service"></a>HDInsight のリンクされたサービス

任意のエディターを使用して JSON ファイルを作成し、Azure HDInsight のリンクされたサービスから次の JSON 定義をコピーして、ファイルを **MyHDInsightLinkedService.json** として保存します。

```
{
  "name": "MyHDInsightLinkedService",
  "properties": {     
      "type": "HDInsight",
      "typeProperties": {
          "clusterUri": "https://<clustername>.azurehdinsight.net",
          "userName": "<username>",
          "password": {
            "value": "<password>",
            "type": "SecureString"
          },
          "linkedServiceName": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }
  }
}
```

リンクされたサービスの定義で、以下のプロパティの値を更新します。

- **userName**。 クラスターの作成時に指定したクラスター ログイン ユーザーの名前。 
- **password**。 ユーザーのパスワード。
- **clusterUri**。 https://<clustername>.azurehdinsight.net の形式で、HDInsight クラスターの URL を指定します。  この記事では、インターネット経由でクラスターにアクセスできることが前提となっています。 たとえば、`https://clustername.azurehdinsight.net` でクラスターに接続できることが必要です。 このアドレスではパブリック ゲートウェイが使用されていますが、ネットワーク セキュリティ グループ (NSG) またはユーザー定義ルート (UDR) を使用してインターネットからのアクセスが制限されている場合は、このゲートウェイを使用できません。 Data Factory が Azure Virtual Network の HDInsight クラスターにジョブを送信できるようにするには、HDInsight によって使用されるゲートウェイのプライベート IP アドレスに URL を解決できるように、Azure 仮想ネットワークを構成する必要があります。

  1. Azure Portal で、HDInsight がある仮想ネットワークを開きます。 名前が `nic-gateway-0` で始まるネットワーク インターフェイスを開きます。 そのプライベート IP アドレスをメモしておきます。 たとえば、10.6.0.15 です。 
  2. Azure 仮想ネットワークに DNS サーバーがある場合は、HDInsight クラスターの URL `https://<clustername>.azurehdinsight.net` を `10.6.0.15` に解決できるように、DNS レコードを更新します。 これが推奨される方法です。 Azure 仮想ネットワークに DNS サーバーがない場合は、セルフホステッド統合ランタイム ノードとして登録されているすべての VM の hosts ファイル (C:\Windows\System32\drivers\etc) を編集し、次のようなエントリを追加することで、一時的にこれを回避することができます。 
  
        `10.6.0.15 myHDIClusterName.azurehdinsight.net`

JSON ファイルを作成したフォルダーに移動し、次のコマンドを実行して、リンクされたサービスをデプロイします。 


```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"

Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyHDILinkedService" -File "MyHDILinkedService.json"
```

## <a name="author-a-pipeline"></a>パイプラインを作成する
この手順では、Hive アクティビティがある新しいパイプラインを作成します。 このアクティビティでは、Hive スクリプトを実行してサンプル テーブルからデータを返し、定義されたパスに保存します。 任意のエディターで JSON ファイルを作成し、パイプライン定義から次の JSON 定義をコピーして、**MyHiveOnDemandPipeline.json** として保存します。


```json
{
  "name": "MyHivePipeline",
  "properties": {
    "activities": [
      {
        "name": "MyHiveActivity",
        "type": "HDInsightHive",
        "linkedServiceName": {
            "referenceName": "MyHDILinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "scriptPath": "adftutorial\\hivescripts\\hivescript.hql",
          "getDebugInfo": "Failure",
          "defines": {           
            "Output": "wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/"
          },
          "scriptLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}

```

以下の点に注意してください。

- **scriptPath** は、MyStorageLinkedService に使用した Azure ストレージ アカウントの Hive スクリプトへのパスを示します。 パスでは大文字と小文字が区別されます。
- **Output** は、Hive スクリプトで使用される引数です。 `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` の形式で、Azure ストレージ上の既存のフォルダーを指定します。 パスでは大文字と小文字が区別されます。 

JSON ファイルを作成したフォルダーに移動し、次のコマンドを実行して、パイプラインをデプロイします。 


```powershell
Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MyHivePipeline.json"
```

## <a name="start-the-pipeline"></a>パイプラインを開始する 

1. パイプラインの実行を開始します。 将来の監視のために、パイプラインの実行の ID もキャプチャされます。

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
   ```
2. 次のスクリプトを実行し、パイプラインの実行の状態を、完了するまで継続的にチェックします。

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }
    
    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

   サンプル実行の出力結果を次に示します。

    ```json
    Pipeline run status: In Progress
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 000000000-0000-0000-000000000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            :
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    :
    DurationInMs      :
    Status            : InProgress
    Error             :
    
    Pipeline ' MyHivePipeline' run finished. Result:
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 0000000-0000-0000-0000-000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            : {logLocation, clusterInUse, jobId, ExecutionProgress...}
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    : 9/18/2017 6:59:16 AM
    DurationInMs      : 63636
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "logLocation": "wasbs://adfjobs@adfv2samplestor.blob.core.windows.net/HiveQueryJobs/000000000-0000-47c3-9b28-1cdc7f3f2ba2/18_09_2017_06_58_18_023/Status"
    "clusterInUse": "https://adfv2HivePrivate.azurehdinsight.net"
    "jobId": "job_1505387997356_0024"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "MySelfhostedIR"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MyHiveActivity"
    ```
4. `outputfolder` フォルダーで、Hive クエリの結果として新しいファイルが作成されていることを確認します。次のサンプル出力のようになっているはずです。 

   ```
   8 en-US SCH-i500 California
   23 en-US Incredible Pennsylvania
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   246 en-US SCH-i500 District Of Columbia
   246 en-US SCH-i500 District Of Columbia
   ```

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、以下の手順を実行しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。 
> * セルフホステッド統合ランタイムを作成して設定します
> * リンクされたサービスを作成してデプロイします。
> * Hive アクティビティが含まれたパイプラインを作成してデプロイします。
> * パイプラインの実行を開始します。
> * パイプラインの実行を監視します 
> * 出力を検証します。 

次のチュートリアルに進み、Azure 上の Spark クラスターを使ってデータを変換する方法について学習しましょう。

> [!div class="nextstepaction"]
>[Data Factory の制御フローの分岐とチェーン](tutorial-control-flow.md)



