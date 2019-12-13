---
title: ストアド プロシージャ アクティビティを使用した SSIS パッケージの実行 - Azure
description: この記事では、ストアド プロシージャ アクティビティを使用して、SQL Server Integration Services (SSIS) パッケージを Azure Data Factory パイプラインで実行する方法を説明します。
services: data-factory
documentationcenter: ''
author: swinarko
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: sawinark
ms.openlocfilehash: f45c317e64f63fe6192f4e32507876841f4322de
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932110"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Azure Data Factory のストアド プロシージャ アクティビティを使用して SSIS パッケージを実行する
この記事では、ストアド プロシージャ アクティビティを使用して、SSIS パッケージを Azure Data Factory パイプラインで実行する方法を説明します。 

## <a name="prerequisites"></a>前提条件

### <a name="azure-sql-database"></a>Azure SQL Database 
この記事のチュートリアルでは、SSIS カタログをホストする Azure SQL データベースを使用します。 Azure SQL Database Managed Instance を使うこともできます。

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムを作成します
Azure-SSIS 統合ランタイムがない場合は、[SSIS パッケージのデプロイに関するチュートリアル](tutorial-create-azure-ssis-runtime-portal.md)の手順に従って作成します。

## <a name="data-factory-ui-azure-portal"></a>データ ファクトリ UI (Azure Portal)
このセクションでは、データ ファクトリ UI を使用して、SSIS パッケージを呼び出すストアド プロシージャ アクティビティを含む Data Factory パイプラインを作成します。

### <a name="create-a-data-factory"></a>Data Factory の作成
最初の手順として、Azure Portal を使用してデータ ファクトリを作成します。 

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
2. [Azure Portal](https://portal.azure.com) に移動します。 
3. 左側のメニューで **[新規]** をクリックし、 **[データ + 分析]** 、 **[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 名前フィールドで次のエラーが発生した場合は、データ ファクトリの名前を変更してください (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
     ![名前は使用できません - エラー](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
   - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
   - **[新規作成]** を選択し、リソース グループの名前を入力します。   
         
     リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2]** を選択します。
5. データ ファクトリの **場所** を選択します。 Data Factory でサポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他の場所に配置できます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。
8. ダッシュボードに、 **[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

     ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
     ![データ ファクトリのホーム ページ](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Azure Data Factory ユーザー インターフェイス (UI) アプリケーションを起動します。 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>ストアド プロシージャ アクティビティを含むパイプラインを作成する
この手順では、データ ファクトリ UI を使用してパイプラインを作成します。 ストアド プロシージャ アクティビティをパイプラインに追加し、sp_executesql ストアド プロシージャを使用して、SSIS パッケージを実行するように構成します。 

1. 開始ページで **[Create Pipeline]\(パイプラインの作成\)** をクリックします。 

    ![開始ページ](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. **[アクティビティ]** ツールボックスで **[全般]** を展開し、パイプライン デザイナー画面に **[ストアド プロシージャ]** アクティビティをドラッグ アンド ドロップします。 

    ![ストアド プロシージャ アクティビティのドラッグ アンド ドロップ](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. ストアド プロシージャ アクティビティのプロパティ ウィンドウで **[SQL アカウント]** タブに切り替えて、 **[+ 新規]** をクリックします。 SSIS カタログ (SSIDB データベース) をホストする Azure SQL データベースへの接続を作成します。 
   
    ![新しいリンクされたサービスのボタン](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    1. **[種類]** で **[Azure SQL Database]** を選択します。
    2. `SSISDB` データベースをホストしている Azure SQL Database に接続するために、**既定の** Azure Integration Runtime を選択します。
    3. **[サーバー名]** フィールドで、SSISDB データベースをホストしている Azure SQL Database を選択します。
    4. **[データベース名]** で **[SSISDB]** を選択します。
    5. **[ユーザー名]** に、データベースにアクセスするユーザーの名前を入力します。
    6. **[パスワード]** に、ユーザーのパスワードを入力します。 
    7. **[テスト接続]** ボタンをクリックして、データベースへの接続をテストします。
    8. **[保存]** ボタンをクリックして、リンクされたサービスを保存します。 

        ![Azure SQL Database のリンクされたサービス](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. プロパティ ウィンドウで **[SQL アカウント]** タブから **[ストアド プロシージャ]** タブに切り替えて、次の手順を実行します。 

    1. **[編集]** を選択します。 
    2. **[ストアド プロシージャ名]** フィールドに、「`sp_executesql`」と入力します。 
    3. **[ストアド プロシージャ パラメーター]** セクションで **[+ 新規]** をクリックします。 
    4. パラメーターの **[名前]** に、「**stmt**」と入力します。 
    5. パラメーターの **[型]** に、「**String**」と入力します。 
    6. パラメーターの **[値]** に、次の SQL クエリを入力します。

        SQL クエリで、**folder_name**、**project_name**、および **package_name** パラメーターに適切な値を指定します。 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Azure SQL Database のリンクされたサービス](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. パイプラインの構成を検証するために、ツール バーの **[検証]** をクリックします。 **[>>]** をクリックして、 **[Pipeline Validation Report]\(パイプライン検証レポート\)** を閉じます。

    ![パイプラインの検証](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. **[Publish All]\(すべて発行\)** ボタンをクリックして、データ ファクトリにパイプラインを発行します。 

    ![発行](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>パイプラインを実行して監視する
このセクションでは、パイプラインの実行をトリガーして監視します。 

1. パイプラインの実行をトリガーするために、ツール バーの **[トリガー]** をクリックし、 **[Trigger Now]\(今すぐトリガー\)** をクリックします。 

    ![[Trigger Now]\(今すぐトリガー\)](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. **[Pipeline Run]\(パイプラインの実行\)** ウィンドウで **[完了]** を選択します。 
3. 左側で **[監視]** タブに切り替えます。 パイプラインの実行とその状態が、その他の情報 (実行開始時刻など) と共に表示されます。 ビューを更新するには、 **[Refresh]\(最新の情報に更新\)** をクリックします。

    ![パイプライン実行](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 パイプラインに 1 つしかアクティビティ (ストアド プロシージャ アクティビティ) がないので、アクティビティの実行が 1 つだけ表示されます。

    ![アクティビティの実行](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. 次の**クエリ**を Azure SQL サーバーの SSISDB データベースに対して実行すると、パッケージが実行されたことを確認できます。 

    ```sql
    select * from catalog.executions
    ```

    ![パッケージの実行を確認する](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> また、パイプラインがスケジュール上で実行されるように (時間単位、日次など)、お使いのパイプラインのスケジュールされたトリガーを作成します。 例については、「[Create a data factory - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)」 (データ ファクトリの作成 - データ ファクトリ UI ) リンクをご確認ください。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このセクションでは、Azure PowerShell を使用して、SSIS パッケージを呼び出すストアド プロシージャ アクティビティを含む Data Factory パイプラインを作成します。 

[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-az-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。 

### <a name="create-a-data-factory"></a>Data Factory の作成
Azure-SSIS IR と同じデータ ファクトリを使用することも、別のデータ ファクトリを作成することもできます。 次の手順では、データ ファクトリを作成する方法を説明します。 このデータ ファクトリにストアド プロシージャ アクティビティを含むパイプラインを作成します。 ストアド プロシージャ アクティビティが SSISDB データベース内でストアド プロシージャを実行して、SSIS パッケージを実行します。 

1. 後で PowerShell コマンドで使用できるように、リソース グループ名の変数を定義します。 次のコマンド テキストを PowerShell にコピーし、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前を二重引用符で囲んで指定し、コマンドを実行します。 (例: `"adfrg"`)。 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$ResourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。
2. Azure リソース グループを作成するには、次のコマンドを実行します。 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    リソース グループが既に存在する場合、上書きしないようお勧めします。 `$ResourceGroupName` 変数に別の値を割り当てて、コマンドをもう一度実行します。 
3. データ ファクトリ名の変数を定義します。 

    > [!IMPORTANT]
    >  データ ファクトリ名は、グローバルに一意となるように更新してください。 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. データ ファクトリを作成するには、$ResGrp 変数の Location および ResourceGroupName プロパティを使用して、次の **Set-AzDataFactoryV2** コマンドレットを実行します。 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、**共同作成者**ロールまたは**所有者**ロールのメンバーであるか、Azure サブスクリプションの**管理者**である必要があります。
* 現在 Data Factory が利用できる Azure リージョンの一覧については、次のページで目的のリージョンを選択し、 **[分析]** を展開して **[Data Factory]** を探してください (「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」)。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービスを作成する
リンクされたサービスを作成して、SSIS カタログをホストする Azure SQL データベースをデータ ファクトリにリンクします。 このリンクされたサービスの情報をデータ ファクトリが使用して、SSISDB データベースに接続し、ストアド プロシージャを実行して SSIS パッケージを実行します。 

1. 次の内容を記述した **AzureSqlDatabaseLinkedService.json** という名前の JSON ファイルを **C:\ADF\RunSSISPackage** フォルダーに作成します。 

    > [!IMPORTANT]
    > &lt;servername&gt;、&lt;username&gt;、&lt;password&gt; を実際の Azure SQL データベースの値に置き換えてから、ファイルを保存してください。

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. **Azure PowerShell** で **C:\ADF\RunSSISPackage** フォルダーに切り替えます。

3. **Set-AzDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス **AzureSqlDatabaseLinkedService** を作成します。 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>ストアド プロシージャ アクティビティを含むパイプラインを作成する 
この手順では、ストアド プロシージャ アクティビティを含むパイプラインを作成します。 このアクティビティは、SSIS パッケージを実行する sp_executesql ストアド プロシージャを呼び出します。 

1. 次の内容を記述した **RunSSISPackagePipeline.json** という名前の JSON ファイルを **C:\ADF\RunSSISPackage** フォルダーに作成します。

    > [!IMPORTANT]
    > &lt;FOLDER NAME&gt;、&lt;PROJECT NAME&gt;、&lt;PACKAGE NAME&gt; を、SSIS カタログのフォルダー、プロジェクト、パッケージの名前で置き換えてから、ファイルを保存してください。 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. パイプライン **RunSSISPackagePipeline** を作成するには、**Set-AzDataFactoryV2Pipeline** コマンドレットを実行します。

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    出力例を次に示します。

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>パイプラインの実行を作成する
**Invoke-AzDataFactoryV2Pipeline** コマンドレットを使ってパイプラインを実行します。 コマンドレットは、将来の監視のために、パイプラインの実行 ID を返します。

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

次の PowerShell スクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。 次のスクリプトをコピーして PowerShell ウィンドウに貼り付け、Enter キーを押します。 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>トリガーを作成する
前の手順ではオンデマンドでパイプラインを呼び出しました。 スケジュール トリガーを作成して、スケジュール (毎時、毎日など) によってパイプラインをトリガーすることもできます。

1. 以下の内容を記述した **MyTrigger.json** という名前の JSON ファイルを **C:\ADF\RunSSISPackage** フォルダー内に作成します。 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. **Azure PowerShell** で **C:\ADF\RunSSISPackage** フォルダーに切り替えます。
3. **Set-AzDataFactoryV2Trigger** コマンドレットを実行してトリガーを作成します。 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. 既定ではトリガーは停止状態になっています。 **Start-AzDataFactoryV2Trigger** コマンドレットを使用してトリガーを起動します。 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. **Start-AzDataFactoryV2Trigger** コマンドレットを実行して、トリガーが起動されていることを確認します。 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. 時刻の時間部分が次の時間になってから、次のコマンドを実行します。 たとえば、現在時刻が午後 3 時 25 分 (UTC) であれば、午後 4 時 (UTC) にコマンドを実行します。 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    次のクエリを Azure SQL サーバーの SSISDB データベースに対して実行すると、パッケージが実行されたことを確認できます。 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>次の手順
Azure Portal を使用してパイプラインを監視することもできます。 具体的な手順については、「[パイプラインの監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)」を参照してください。
