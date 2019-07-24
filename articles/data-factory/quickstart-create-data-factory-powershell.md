---
title: Azure Data Factory を使用して Blob Storage でデータをコピーする | Microsoft Docs
description: Azure データ ファクトリを作成して、Azure Blob Storage 内のある場所から別の場所にデータをコピーします。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: b675ab9663be674ec2439bfe9139b7c79c144cbd
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453390"
---
# <a name="quickstart-create-an-azure-data-factory-using-powershell"></a>クイック スタート:PowerShell を使用した Azure データ ファクトリの作成

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [現在のバージョン](quickstart-create-data-factory-powershell.md)

このクイックスタートでは、PowerShell を使用して Azure データ ファクトリを作成する方法について説明します。 このデータ ファクトリに作成されたパイプラインは、データを Azure BLOB ストレージ内のあるフォルダーから別のフォルダーに**コピー**します。 Azure Data Factory を使用してデータを**変換**する方法のチュートリアルについては、[Spark を使用したデータ変換のチュートリアル](transform-data-using-spark.md)を参照してください。

> [!NOTE]
> この記事では、Data Factory サービスの概要については詳しく取り上げません。 Azure Data Factory サービスの概要については、「[Azure Data Factory の概要](introduction.md)」をご覧ください。

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-Az-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。

#### <a name="log-in-to-powershell"></a>PowerShell にログインする

1. お使いのマシンで **PowerShell** を起動します。 PowerShell は、このクイックスタートが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

2. 次のコマンドを実行して、Azure Portal へのサインインに使用するのと同じ Azure ユーザー名とパスワードを入力します。

    ```powershell
    Connect-AzAccount
    ```

3. 次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```powershell
    Get-AzSubscription
    ```

4. アカウントに複数のサブスクリプションが関連付けられている場合は、次のコマンドを実行して、使用するサブスクリプションを選択します。 **SubscriptionId** は、実際の Azure サブスクリプションの ID に置き換えてください。

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. 後で PowerShell コマンドで使用できるように、リソース グループ名の変数を定義します。 次のコマンド テキストを PowerShell にコピーし、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前を二重引用符で囲んで指定し、コマンドを実行します。 (例: `"ADFQuickStartRG"`)。

     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$ResourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。

2. Azure リソース グループを作成するには、次のコマンドを実行します。

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$ResourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。

3. データ ファクトリ名の変数を定義します。 

    > [!IMPORTANT]
    >  データ ファクトリ名は、グローバルに一意となるように更新してください。 たとえば、ADFTutorialFactorySP1127 です。

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

4. データ ファクトリを作成するには、$ResGrp 変数の Location および ResourceGroupName プロパティを使用して、次の **Set-AzDataFactoryV2** コマンドレットを実行します。

    ```powershell
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
        -Location $ResGrp.Location -Name $dataFactoryName
    ```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、**共同作成者**ロールまたは**所有者**ロールのメンバーであるか、Azure サブスクリプションの**管理者**である必要があります。

* 現在 Data Factory が利用できる Azure リージョンの一覧については、次のページで目的のリージョンを選択し、**[分析]** を展開して **[Data Factory]** を探してください (「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」)。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

## <a name="create-a-linked-service"></a>リンクされたサービスを作成する

データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このクイックスタートでは、ソース ストアとシンク ストアの両方として使用される Azure Storage のリンクされたサービスを作成します。 リンクされたサービスは、Data Factory サービスが実行時に接続するために使用する接続情報を持っています。

1. 以下の内容を記述した **AzureStorageLinkedService.json** という名前の JSON ファイルを **C:\ADFv2QuickStartPSH** フォルダー内に作成します。まだ存在しない場合は、ADFv2QuickStartPSH フォルダーを作成してください。

    > [!IMPORTANT]
    > &lt;accountName&gt; と &lt;accountKey&gt; を実際の Azure ストレージ アカウントの名前とキーに置き換えてからファイルを保存してください。

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

    メモ帳を使用している場合は、**[名前を付けて保存]** ダイアログ ボックスの **[ファイルの種類]** フィールドで **[すべてのファイル]** を選択します。 そうしないと、ファイルに `.txt` 拡張子が追加されることがあります。 たとえば、「 `AzureStorageLinkedService.json.txt` 」のように入力します。 このファイルをエクスプローラーで作成してからメモ帳で開くと、`.txt` 拡張子は表示されない場合があります。これは、**[登録されている拡張子は表示しない]** オプションが既定で設定されているためです。 `.txt` 拡張子を削除してから次の手順に進んでください。

2. **PowerShell** で **ADFv2QuickStartPSH** フォルダーに切り替えます。

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```

3. **Set-AzDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス**AzureStorageLinkedService** を作成します。

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" `
        -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    出力例を次に示します。

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>データセットを作成する

この手順では、ソースからシンクにコピーするデータを表すデータセットを定義します。 データセットの型は、**AzureBlob** です。 前の手順で作成した **Azure Storage のリンクされたサービス**を参照します。 **folderPath** プロパティを構築するために、パラメーターを受け取ります。 入力データセットでは、パイプライン内のコピー アクティビティが、このパラメーターの値として入力パスを渡します。 同様に、出力データセットでは、コピー アクティビティがこのパラメーターの値として出力パスを渡します。 

1. 以下の内容を記述した **BlobDataset.json** という名前の JSON ファイルを **C:\ADFv2QuickStartPSH** フォルダー内に作成します。

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "@{dataset().path}"
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. データセット **BlobDataset** を作成するには、**Set-AzDataFactoryV2Dataset** コマンドレットを実行します。

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "BlobDataset" `
        -DefinitionFile ".\BlobDataset.json"
    ```

    出力例を次に示します。

    ```console
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>パイプラインを作成する。

このクイックスタートでは、2 つのパラメーター (入力 BLOB パスと出力 BLOB パス) を受け取る 1 つのアクティビティを持つパイプラインを作成します。 これらのパラメーターの値は、パイプラインがトリガー/実行されたときに設定されます。 コピー アクティビティは、入力と出力として、前の手順で作成された同じ BLOB データセットを使用します。 データセットが入力データセットとして使用される場合は、入力パスが指定されます。 また、データセットが出力データセットとして使用される場合は、出力パスが指定されます。

1. 以下の内容を記述した **Adfv2QuickStartPipeline.json** という名前の JSON ファイルを **C:\ADFv2QuickStartPSH** フォルダー内に作成します。

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. パイプライン **Adfv2QuickStartPipeline** を作成するには、**Set-AzDataFactoryV2Pipeline** コマンドレットを実行します。

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -Name "Adfv2QuickStartPipeline" `
        -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

## <a name="create-a-pipeline-run"></a>パイプラインの実行を作成する

このステップでは、パイプライン パラメーターの **inputPath** と **outputPath** に、ソースおよびシンク BLOB パスの実際の値を設定します。 次に、これらの引数を使用して、パイプラインの実行を作成します。

1. 以下の内容を記述した **PipelineParameters.json** という名前の JSON ファイルを **C:\ADFv2QuickStartPSH** フォルダー内に作成します。

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. **Invoke-AzDataFactoryV2Pipeline** コマンドレットを実行し、パイプラインの実行を作成し、パラメーターの値を渡します。 コマンドレットは、将来の監視のために、パイプラインの実行 ID を返します。

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -PipelineName $DFPipeLine.Name `
        -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. 次の PowerShell スクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。 次のスクリプトをコピーして PowerShell ウィンドウに貼り付け、Enter キーを押します。

    ```powershell
    while ($True) {
        $Run = Get-AzDataFactoryV2PipelineRun `
            -ResourceGroupName $ResGrp.ResourceGroupName `
            -DataFactoryName $DataFactory.DataFactoryName `
            -PipelineRunId $RunId

        if ($Run) {
            if ($run.Status -ne 'InProgress') {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output "Pipeline is running...status: InProgress"
        }

        Start-Sleep -Seconds 10
    }
    ```

    パイプラインの実行の出力例を次に示します。

    ```console
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

    次のエラーが表示されることがあります。

    ```console
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```

    エラーが発生した場合は、次の手順のようにします。

    1. AzureStorageLinkedService.json で、Azure Storage アカウントの名前とキーが正しいことを確認します。
    2. 接続文字列の形式が正しいことを確認します。 たとえば、AccountName プロパティと AccountKey プロパティは、セミコロン (`;`) 文字で区切ります。
    3. アカウント名とアカウント キーが山かっこで囲まれていたら、それらを削除します。
    4. 接続文字列の例を次に示します。

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageaccountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```

    5. 「[リンクされたサービスを作成する](#create-a-linked-service)」セクションの手順に従って、リンクされたサービスを再作成します。
    6. 「[パイプラインの実行を作成する](#create-a-pipeline-run)」セクションの手順に従って、パイプラインを再実行します。
    7. 現在の監視コマンドを再実行して、新しいパイプライン実行を監視します。

2. 次のスクリプトを実行し、コピー アクティビティの実行の詳細 (たとえば、読み書きされたデータのサイズ) を取得します。

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. アクティビティの実行の結果である次のサンプル出力のような出力が表示されることを確認します。

    ```console
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedDataIntegrationUnits": 2
    "billedDuration": 14
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="next-steps"></a>次の手順

このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-dot-net.md)を参照してください。
