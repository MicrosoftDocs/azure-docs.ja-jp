---
title: SSIS パッケージの実行アクティビティを使用した SSIS パッケージの実行 - Azure | Microsoft Docs
description: この記事では、SSIS パッケージの実行アクティビティを使用して、SQL Server Integration Services (SSIS) パッケージを Azure Data Factory パイプラインで実行する方法を説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/19/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 7287dc2fccf461cf23c45202336e3d92bc5a40aa
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259705"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Azure Data Factory の SSIS パッケージの実行アクティビティを使用して SSIS パッケージを実行する
この記事では、SSIS パッケージの実行アクティビティを使用して、SSIS パッケージを Azure Data Factory (ADF) パイプラインで実行する方法を説明します。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS Integration Runtime (IR) がない場合は、[Azure への SSIS パッケージのデプロイに関するチュートリアル](tutorial-create-azure-ssis-runtime-portal.md)の手順に従って作成します。

## <a name="run-a-package-in-the-azure-portal"></a>Azure Portal でパッケージを実行する
このセクションでは、ADF ユーザー インターフェイス (UI)/アプリを使用して、SSIS パッケージを実行する SSIS パッケージの実行アクティビティを含む ADF パイプラインを作成します。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS パッケージの実行アクティビティでパイプラインを作成する
この手順では、ADF UI/アプリを使用してパイプラインを作成します。 パイプラインに SSIS パッケージの実行アクティビティを追加し、SSIS パッケージを実行するように構成します。 

1. Azure portal の ADF の概要/ホーム ページで、**[Author & Monitor]\(作成と監視\)** タイルをクリックして ADF UI/アプリを別のタブで起動します。 

   ![データ ファクトリのホーム ページ](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   **[Let's get started]\(始めましょう\)** ページで **[Create pipeline]\(パイプラインの作成\)** をクリックします。 

   ![開始ページ](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. **[アクティビティ]** ツールボックスで **[一般]** を展開し、**[SSIS パッケージの実行]** アクティビティをドラッグしてパイプライン デザイナー画面にドロップします。 

   ![SSIS パッケージの実行アクティビティをデザイナー画面にドラッグする](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. SSIS パッケージの実行アクティビティの **[一般]** タブで、アクティビティの名前と説明を入力します。 オプションのタイムアウト値と再試行値を設定します。

   ![[一般] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. SSIS パッケージの実行アクティビティの **[設定]** タブで、パッケージがデプロイされる SSISDB データベースに関連付けられている Azure-SSIS IR を選択します。 パッケージが Windows 認証を使用してデータ ストア (オンプレミスの SQL Server/ファイル共有、Azure Files など) にアクセスする場合は **[Windows 認証]** チェック ボックスをオンにし、パッケージ実行のドメイン/ユーザー名/パスワードを入力します。 パッケージで 32 ビット ランタイムを実行する必要がある場合は、**[32-Bit runtime]\(32 ビット ランタイム\)** チェック ボックスをオンにします。 **[ログ レベル]** で、パッケージ実行用のログの定義済みのスコープを選択します。 カスタマイズしたログ名を代わりに入力する場合は、**[Customized]\(カスタマイズ\)** チェック ボックスをオンにします。 Azure-SSIS IR の実行中に **[Manual entries]\(手動入力\)** チェック ボックスがオフになっている場合は、既存のフォルダー/プロジェクト/パッケージ/環境を SSISDB から参照して選択できます。 **[更新]** ボタンをクリックして、SSISDB から新しく追加したフォルダー/プロジェクト/パッケージ/環境を取り込み、それらを参照して選択できるようにします。 

   ![[設定] タブでプロパティを設定する - 自動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Azure-SSIS IR が実行されていない場合、または **[Manual entries]\(手動入力\)** チェック ボックスがオンになっている場合は、`<folder name>/<project name>/<package name>.dtsx` および `<folder name>/<environment name>` の形式で SSISDB からパッケージと環境のパスを直接入力できます。

   ![[設定] タブでプロパティを設定する - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

5. SSIS パッケージの実行アクティビティの **[SSIS パラメーター]** タブで、Azure-SSIS IR の実行中に **[設定]** タブの **[Manual entries]\(手動入力\)** チェック ボックスがオフになっている場合は、値を割り当てるパラメーターとして、SSISDB から選択したプロジェクト/パッケージ内の既存の SSIS パラメーターが表示されます。 それ以外の場合は、値を割り当てる対象を 1 つずつ手動で入力できます。パッケージ実行を成功させるには、対象が存在し、正しく入力されていることを確認してください。 式、関数、ADF システム変数、および ADF パイプラインのパラメーター/変数を使用して、動的なコンテンツを値に追加できます。 または、Azure Key Vault (AKV) に格納されているシークレットを値として使用できます。 このためには、関連するパラメーターの横にある **[Azure Key Vault]** チェック ボックスをクリックし、AKV にリンクしている既存のサービスを選択/編集するか、新しいサービスを作成してから、パラメーター値としてシークレット名/バージョンを選択します。  AKV にリンクしているサービスを作成/編集するときは、既存の AKV を選択/編集するか新しい AKV を作成できますが、AKV に ADF マネージド ID アクセスをまだ付与していない場合は付与してください。 `<AKV linked service name>/<secret name>/<secret version>` の形式でシークレットを直接入力することもできます。

   ![[SSIS パラメーター] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. SSIS パッケージの実行アクティビティの **[接続マネージャー]** タブで、Azure-SSIS IR の実行中に **[設定]** タブの **[Manual entries]\(手動入力\)** チェック ボックスがオフになっている場合は、プロパティに値を割り当てる接続マネージャーとして、SSISDB から選択したプロジェクト/パッケージ内の既存の接続マネージャーが表示されます。 それ以外の場合は、プロパティに値を割り当てる対象を 1 つずつ手動で入力できます。パッケージ実行を成功させるには、対象が存在し、正しく入力されていることを確認してください。 式、関数、ADF システム変数、および ADF パイプラインのパラメーター/変数を使用して、動的なコンテンツをプロパティ値に追加できます。 または、Azure Key Vault (AKV) に格納されているシークレットをプロパティ値として使用できます。 このためには、関連するプロパティの横にある **[Azure Key Vault]** チェック ボックスをクリックし、AKV にリンクしている既存のサービスを選択/編集するか、新しいサービスを作成してから、プロパティ値としてシークレット名/バージョンを選択します。  AKV にリンクしているサービスを作成/編集するときは、既存の AKV を選択/編集するか新しい AKV を作成できますが、AKV に ADF マネージド ID アクセスをまだ付与していない場合は付与してください。 `<AKV linked service name>/<secret name>/<secret version>` の形式でシークレットを直接入力することもできます。

   ![[接続マネージャー] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. SSIS パッケージの実行アクティビティの **[プロパティのオーバーライド]** タブで、SSISDB から選択したパッケージ内の既存のプロパティのパスを 1 つずつ入力して、それらに手動で値を割り当てることができます。パッケージ実行を成功させるには、パスが存在し、正しく入力されていることを確認してください。たとえば、ユーザー変数の値をオーバーライドするには、そのパスを `\Package.Variables[User::YourVariableName].Value` の形式で入力します。 式、関数、ADF システム変数、および ADF パイプラインのパラメーター/変数を使用して、動的なコンテンツを値に追加することもできます。

   ![[プロパティのオーバーライド] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

8. パイプラインの構成を検証するために、ツール バーの **[検証]** をクリックします。 **[>>]** をクリックして、**[Pipeline Validation Report]\(パイプライン検証レポート\)** を閉じます。

9. **[Publish All]\(すべて発行\)** ボタンをクリックして、ADF にパイプラインを発行します。 

### <a name="run-the-pipeline"></a>パイプラインを実行する
この手順では、パイプラインの実行をトリガーします。 

1. パイプラインの実行をトリガーするために、ツール バーの **[トリガー]** をクリックし、**[Trigger Now]\(今すぐトリガー\)** をクリックします。 

   ![[Trigger Now]\(今すぐトリガー\)](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. **[Pipeline Run]\(パイプラインの実行\)** ウィンドウで **[完了]** を選択します。 

### <a name="monitor-the-pipeline"></a>パイプラインの監視

1. 左側で **[監視]** タブに切り替えます。 パイプラインの実行とその状態が、その他の情報 (実行開始時刻など) と共に表示されます。 ビューを更新するには、**[Refresh]\(最新の情報に更新\)** をクリックします。

   ![パイプライン実行](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 パイプラインに 1 つしかアクティビティ (SSIS パッケージの実行アクティビティ) がないので、アクティビティの実行が 1 つだけ表示されます。

   ![アクティビティの実行](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. 次の**クエリ**を Azure SQL サーバーの SSISDB データベースに対して実行すると、パッケージが実行されたことを確認できます。 

   ```sql
   select * from catalog.executions
   ```

   ![パッケージの実行を確認する](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. パイプラインのアクティビティ実行の出力から SSISDB 実行 ID を取得し、その ID を使用して SSMS 内でより包括的な実行ログとエラー メッセージを確認することもできます。

   ![実行 ID を取得します。](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>トリガーを使用してパイプラインのスケジュールを設定する

また、パイプラインがスケジュール上で実行されるように (時間単位、日次など)、お使いのパイプラインのスケジュールされたトリガーを作成します。 例については、「[Create a data factory - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)」 (データ ファクトリの作成 - データ ファクトリ UI ) リンクをご確認ください。

## <a name="run-a-package-with-powershell"></a>PowerShell を使用してパッケージを実行する
このセクションでは、Azure PowerShell を使用して、SSIS パッケージを実行する SSIS パッケージの実行アクティビティを含む ADF パイプラインを作成します。 

[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-az-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。

### <a name="create-an-adf-with-azure-ssis-ir"></a>Azure-SSIS IR を使用して ADF を作成する
Azure-SSIS IR が既にプロビジョニングされている既存の ADF を使用するか、[PowerShell を使用した Azure への SSIS パッケージのデプロイに関するチュートリアル](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)の手順に従って、Azure-SSIS IR を使用して新しい ADF を作成します。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS パッケージの実行アクティビティでパイプラインを作成する 
この手順では、SSIS パッケージの実行アクティビティでパイプラインを作成します。 このアクティビティは SSIS パッケージを実行します。 

1. 次の例のような内容を記述した **RunSSISPackagePipeline.json** という名前の JSON ファイルを **C:\ADF\RunSSISPackage** フォルダーに作成します。

   > [!IMPORTANT]
   > ファイルを保存する前に、オブジェクトの名前、説明、パス、プロパティ値、パラメーター値、パスワード、およびその他の変数値を置き換えます。 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "mySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "myAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyDomain",
                       "userName": "MyUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "**********"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyPipelineParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MySecret"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "abc"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "userName": {
                               "value": {
                                   "value": "@pipeline().parameters.MyUsername",
                                   "type": "Expression"
                               }
                           },
                           "passWord": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyPassword",
                                   "secretVersion": "3a1b74e361bf4ef4a00e47053b872149"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

2. Azure PowerShell で `C:\ADF\RunSSISPackage` フォルダーに切り替えます。

3. パイプライン **RunSSISPackagePipeline** を作成するには、**Set-AzDataFactoryV2Pipeline** コマンドレットを実行します。

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   出力例を次に示します。

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>パイプラインを実行する
**Invoke-AzDataFactoryV2Pipeline** コマンドレットを使ってパイプラインを実行します。 コマンドレットは、将来の監視のために、パイプラインの実行 ID を返します。

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>パイプラインの監視

次の PowerShell スクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。 次のスクリプトをコピーして PowerShell ウィンドウに貼り付け、Enter キーを押します。 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

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

Azure Portal を使用してパイプラインを監視することもできます。 具体的な手順については、「[パイプラインの監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)」を参照してください。

### <a name="schedule-the-pipeline-with-a-trigger"></a>トリガーを使用してパイプラインのスケジュールを設定する
前の手順ではオンデマンドでパイプラインを実行しました。 スケジュール トリガーを作成して、スケジュール (毎時、毎日など) によってパイプラインをトリガーすることもできます。

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
           }]
       }
   }    
   ```
2. **Azure PowerShell** で **C:\ADF\RunSSISPackage** フォルダーに切り替えます。
3. **Set-AzDataFactoryV2Trigger** コマンドレットを実行してトリガーを作成します。 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. 既定ではトリガーは停止状態になっています。 **Start-AzDataFactoryV2Trigger** コマンドレットを使用してトリガーを起動します。 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. **Start-AzDataFactoryV2Trigger** コマンドレットを実行して、トリガーが起動されていることを確認します。 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. 時刻の時間部分が次の時間になってから、次のコマンドを実行します。 たとえば、現在時刻が午後 3 時 25 分 (UTC) であれば、午後 4 時 (UTC) にコマンドを実行します。 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   次のクエリを Azure SQL サーバーの SSISDB データベースに対して実行すると、パッケージが実行されたことを確認できます。 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>次の手順
次のブログ記事を参照してください。
-   [ADF パイプラインでの SSIS アクティビティを含む ETL/ELT ワークフローの最新化と拡張](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
