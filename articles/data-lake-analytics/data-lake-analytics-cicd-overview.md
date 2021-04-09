---
title: Azure Data Lake Analytics の CI/CD パイプラインをセットアップする方法
description: Azure Data Lake Analytics の継続的インテグレーションと継続的デプロイをセットアップする方法について説明します。
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 09/14/2018
ms.openlocfilehash: 95b638b85e0746d2995488f2a28a5fb2512b1063
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015266"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics の CI/CD パイプラインをセットアップする方法  

この記事では、U-SQL ジョブと U-SQL データベースの継続的統合およびデプロイ (CI/CD) パイプラインを設定する方法について説明します。  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>U-SQL ジョブに CI/CD を使用する

Azure Data Lake Tools for Visual Studio が提供する U-SQL プロジェクト タイプは、U-SQL スクリプトの整理に役立ちます。 U-SQL プロジェクトを使用して U-SQL コードを管理することで、CI/CD シナリオがさらに簡単になります。

## <a name="build-a-u-sql-project"></a>U-SQL プロジェクトをビルドする

U-SQL プロジェクトは、Microsoft Build Engine (MSBuild) で対応するパラメーターを渡すことによってビルドできます。 この記事の手順に従って、U-SQL プロジェクトのビルド プロセスを設定します。

### <a name="project-migration"></a>プロジェクトの移行

U-SQL プロジェクトのビルド タスクを設定する前に、U-SQL プロジェクトの最新バージョンを使用していることを確認します。 エディターで U-SQL プロジェクト ファイルを開き、次のインポート項目があることを確認します。

```xml
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
```

ない場合、プロジェクトを移行する 2 つのオプションがあります。

- オプション 1: 古いインポート項目を前述の項目に変更します。
- オプション 2:古いプロジェクトを Azure Data Lake Tools for Visual Studio で開きます。 2\.3.3000.0 よりも新しいバージョンを使用します。 古いプロジェクト テンプレートが自動的に最新バージョンにアップグレードされます。 2\.3.3000.0 より新しいバージョンで作成された新しいプロジェクトでは、新しいテンプレートが使用されます。

### <a name="get-nuget"></a>NuGet を入手する

MSBuild には、U-SQL プロジェクトのサポートが組み込まれていません。 このサポートを追加するには、必要な言語サービスを追加する [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet パッケージへの参照をソリューションのために追加する必要があります。

NuGet パッケージ参照を追加するには、Visual Studio のソリューション エクスプローラーでソリューションを右クリックして **[NuGet パッケージの管理]** を選択します。 または、`packages.config` という名前のファイルをソリューション フォルダーに追加し、次の内容をそのファイルに追加します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
```

### <a name="manage-u-sql-database-references"></a>U-SQL データベース参照の管理

U-SQL プロジェクトの U-SQL スクリプトには、U-SQL データベース オブジェクトのクエリ ステートメントが含まれている場合があります。 このような場合は、U-SQL プロジェクトをビルドする前に、オブジェクトの定義を含む、対応する U-SQL データベース プロジェクトを参照する必要があります。 たとえば、U-SQL テーブルに対してクエリを実行する場合や、アセンブリを参照する場合です。

詳細については、[U-SQL データベース プロジェクト](data-lake-analytics-data-lake-tools-develop-usql-database.md)に関するページを参照してください。

>[!NOTE]
> DROP ステートメントは、誤削除の原因となる場合があります。 DROP ステートメントを有効にするには、MSBuild 引数を明示的に指定する必要があります。 **AllowDropStatement** では、アセンブリのドロップやテーブル値関数のドロップなど、非データ関連の DROP 操作を有効にします。 **AllowDataDropStatement** では、テーブルのドロップやスキーマのドロップなど、データ関連の DROP 操作を有効にします。 AllowDataDropStatement を使用する前に、AllowDropStatement を有効にする必要があります。
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>MSBuild コマンド ラインを使用して U-SQL プロジェクトをビルドする

まずプロジェクトを移行し、NuGet パッケージを入手します。 次に、以下の引数を追加して標準の MSBuild コマンド ラインを呼び出し、U-SQL プロジェクトをビルドします。

```console
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
```

引数の定義と値は次のとおりです。

- **USQLSDKPath=\<U-SQL Nuget package>\build\runtime**。 このパラメーターは、U-SQL 言語サービス用の NuGet パッケージのインストール パスを参照します。
- **USQLTargetType=Merge or SyntaxCheck**:

  - **Merge**。 マージ モードでコードビハインド ファイルをコンパイルします。 たとえば、 **.cs**、 **.py**、 **.r** などのファイルです。 結果のユーザー定義コード ライブラリを U-SQL スクリプトにインライン展開します。 たとえば、dll バイナリ、Python、R などのコードです。

  - **SyntaxCheck**。 SyntaxCheck モードでは、まずコードビハインド ファイルが U-SQL スクリプトにマージされます。 次に、U-SQL スクリプトがコンパイルされ、コードが検証されます。

- **DataRoot=\<DataRoot path>** 。 DataRoot は SyntaxCheck モードの場合にのみ必要です。 SyntaxCheck モードでスクリプトをビルドすると、MSBuild によってスクリプト内のデータベース オブジェクトに対する参照がチェックされます。 ビルド前に、U-SQL データベースから参照されるオブジェクトを含め、一致するローカル環境をビルド コンピューターの DataRoot フォルダー上にセットアップしてください。 [U-SQL データベース プロジェクトを参照する](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)ことによって、これらのデータベース依存関係を管理することもできます。 MSBuild ではデータベース オブジェクト参照のみがチェックされ、ファイルはチェックされません。

- **EnableDeployment=true** または **false**。 EnableDeployment は、ビルド処理中に参照されている U-SQL データベースをデプロイできるかどうかを示します。 U-SQL データベース プロジェクトを参照し、U-SQL スクリプトでデータベース オブジェクトを使用する場合は、このパラメーターを **true** に設定します。

### <a name="continuous-integration-through-azure-pipelines"></a>Azure Pipelines を使用した継続的インテグレーション

コマンド ラインに加えて、Visual Studio Build または MSBuild タスクを使用して、Azure Pipelines で U-SQL プロジェクトをビルドすることもできます。 ビルド パイプラインを設定するには、ビルド パイプラインに NuGet 復元タスクと MSBuild タスクという 2 つのタスクを追加します。

![U-SQL プロジェクトの MSBuild タスク](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. `Azure.DataLake.USQL.SDK` を含むソリューションで参照する NuGet パッケージを取得する NuGet 復元タスクを追加して、MSBuild で U-SQL 言語ターゲットを見つけられるようにします。 手順 2 で直接 MSBuild 引数サンプルを使用する場合は、 **[詳細設定]**  >  **[宛先ディレクトリ]** を `$(Build.SourcesDirectory)/packages` に設定します。

   ![U-SQL プロジェクトの NuGet 復元タスク](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. 次の例に示すように、Visual Studio ビルド ツールまたは MSBuild タスクで MSBuild 引数を設定します。 または、Azure Pipelines ビルド パイプラインで、これらの引数の変数を定義することもできます。

   ![U-SQL プロジェクトの CI/CD MSBuild 変数を定義する](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
   ```

### <a name="u-sql-project-build-output"></a>U-SQL プロジェクトのビルド出力

ビルドの実行後、U-SQL プロジェクト内のすべてのスクリプトがビルドされ、`USQLProjectName.usqlpack` という名前の zip ファイルに出力されます。 プロジェクトのフォルダー構造は、zip 形式のビルド出力でも維持されます。

> [!NOTE]
> 各 U-SQL スクリプトの分離コード ファイルは、インライン ステートメントとしてスクリプトのビルド出力にマージされます。

## <a name="test-u-sql-scripts"></a>U-SQL スクリプトのテスト

Azure Data Lake には、U-SQL スクリプトおよび C# UDO/UDAG/UDF 用のテスト プロジェクトが用意されています。

- [U-SQL スクリプトおよび拡張 C# コード用のテスト ケースを追加する方法](data-lake-analytics-cicd-test.md#test-u-sql-scripts)に関するページを参照してください

- [Azure Pipelines でテスト ケースを実行する](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops)方法を確認してください。

## <a name="deploy-a-u-sql-job"></a>U-SQL ジョブをデプロイする

ビルドとテストのプロセスでコードを検証した後、Azure PowerShell タスクで U-SQL ジョブを Azure Pipelines から直接送信することができます。 スクリプトを Azure Data Lake Store または Azure Blob Storage にデプロイし、[スケジュールされたジョブを Azure Data Factory を使用して実行する](../data-factory/transform-data-using-data-lake-analytics.md)こともできます。

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Azure Pipelines を使用して U-SQL ジョブを送信する

U-SQL プロジェクトのビルド出力は **USQLProjectName.usqlpack** という名前の zip ファイルです。 この zip ファイルには、プロジェクト内のすべての U-SQL スクリプトが含まれています。 Pipelines で [Azure PowerShell タスク](/azure/devops/pipelines/tasks/deploy/azure-powershell)を使用し、次のサンプル PowerShell スクリプトで、Azure Pipelines から直接 U-SQL ジョブを送信できます。

```powershell
<#
   This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and submit all scripts one-by-one.

   Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.

   Example :
      USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

   return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
   $usqlFiles = GetUsqlFiles

   Write-Output "$($usqlFiles.Count) jobs to be submitted..."

   # Submit each usql script and wait for completion before moving ahead.
   foreach ($usqlFile in $usqlFiles)
   {
      $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

      Write-Output "Submitting job for '{$usqlFile}'"

      $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism

      LogJobInformation $jobToSubmit

      Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
      $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
      LogJobInformation $jobResult
   }
}

Function LogJobInformation($jobInfo)
{
   Write-Output "************************************************************************"
   Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
   Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
   Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
   Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
   Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
   Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
   Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
   if ($item -ne $null)
   {
      return $item
   }
   return ""
}

Function Main()
{
   Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
   Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
   Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

   Write-Output "Starting USQL script deployment..."

   SubmitAnalyticsJob

   Write-Output "Finished deployment..."
}

Main
```

>[!NOTE]
> `Submit-AzDataLakeAnalyticsJob` および `Wait-AzDataLakeAnalyticsJob` コマンドは、どちらも Azure Resource Manager フレームワークに含まれる Azure Data Lake Analytics の Azure PowerShell コマンドレットです。 Azure PowerShell がインストールされているワークステーションが必要です。 その他のコマンドと例については、[コマンド一覧](/powershell/module/Az.DataLakeAnalytics)を参照してください。
>

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Azure Data Factory を通じた U-SQL ジョブのデプロイ

Azure Pipelines から直接、U-SQL ジョブを送信することができます。 また、ビルドしたスクリプトを Azure Data Lake Store または Azure Blob Storage にアップロードし、[スケジュールされたジョブを Azure Data Factory を使用して実行する](../data-factory/transform-data-using-data-lake-analytics.md)こともできます。

U-SQL スクリプトを Azure Data Lake Store アカウントにアップロードするには、Azure Pipelines 内の [Azure PowerShell タスク](/azure/devops/pipelines/tasks/deploy/azure-powershell)と、次のサンプル PowerShell スクリプトを使用します。

```powershell
<#
   This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.

   Example :
      FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
   Write-Host "************************************************************************"
   Write-Host "Uploading files to $ADLSName"
   Write-Host "***********************************************************************"

   $usqlScripts = GetUsqlFiles

   $files = @(get-childitem $usqlScripts -recurse)
   foreach($file in $files)
   {
      Write-Host "Uploading file: $($file.Name)"
      Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
   }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>U-SQL データベース用の CI/CD

Azure Data Lake Tools for Visual Studio が提供する U-SQL データベース プロジェクト テンプレートは、U-SQL データベースを開発、管理、デプロイするために役立ちます。 詳細については、[U-SQL データベース プロジェクト](data-lake-analytics-data-lake-tools-develop-usql-database.md)に関するページを参照してください。

## <a name="build-u-sql-database-project"></a>U-SQL データベース プロジェクトのビルド

### <a name="get-the-nuget-package"></a>NuGet パッケージを入手する

MSBuild には、U-SQL データベース プロジェクトのサポートが組み込まれていません。 この機能を取得するには、必要な言語サービスを追加する [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet パッケージへの参照をソリューションのために追加する必要があります。

NuGet パッケージ参照を追加するには、Visual Studio のソリューション エクスプローラーでソリューションを右クリックします。 **[NuGet パッケージの管理]** を選択します。 NuGet パッケージを探してインストールします。 また、**packages.config** という名前のファイルをソリューション フォルダーに追加し、次の内容をそのファイルに追加することもできます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>MSBuild コマンド ラインを使用して U-SQL データベース プロジェクトをビルドする

U-SQL データベース プロジェクトをビルドするには、標準の MSBuild コマンド ラインを呼び出して、U-SQL SDK NuGet パッケージ参照を追加引数として渡します。 次の例を参照してください。 

```console
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

引数 `USQLSDKPath=<U-SQL Nuget package>\build\runtime` は、U-SQL 言語サービス用の NuGet パッケージのインストール パスを参照します。

### <a name="continuous-integration-with-azure-pipelines"></a>Azure Pipelines を使用した継続的インテグレーション

コマンド ラインに加えて、Visual Studio Build または MSBuild タスクを使用して、Azure Pipelines で U-SQL データベース プロジェクトをビルドできます。 ビルド タスクを設定するには、ビルド パイプラインに NuGet 復元タスクと MSBuild タスクという 2 つのタスクを追加します。

   ![U-SQL プロジェクトの CI/CD MSBuild タスク](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. `Azure.DataLake.USQL.SDK` を含むソリューションで参照する NuGet パッケージを取得する NuGet 復元タスクを追加して、MSBuild で U-SQL 言語ターゲットを見つけられるようにします。 手順 2 で直接 MSBuild 引数サンプルを使用する場合は、 **[詳細設定]**  >  **[宛先ディレクトリ]** を `$(Build.SourcesDirectory)/packages` に設定します。

   ![U-SQL プロジェクトの CI/CD NuGet タスク](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. 次の例に示すように、Visual Studio ビルド ツールまたは MSBuild タスクで MSBuild 引数を設定します。 または、Azure Pipelines ビルド パイプラインで、これらの引数の変数を定義することもできます。

   ![U-SQL データベース プロジェクトの CI/CD MSBuild 変数を定義する](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```

### <a name="u-sql-database-project-build-output"></a>U-SQL データベース プロジェクトのビルド出力

U-SQL データベース プロジェクトのビルド出力は、名前に `.usqldbpack` というサフィックスのついた U-SQL データベース展開パッケージです。 `.usqldbpack` パッケージは、DDL フォルダー内の単一の U-SQL スクリプトにすべての DDL ステートメントを含む zip ファイルです。 また、一時フォルダー内にあるアセンブリ用のすべての **.dlls** と追加のファイルが含まれています。

## <a name="test-table-valued-functions-and-stored-procedures"></a>テーブル値関数とストアド プロシージャをテストする

テーブル値関数とストアド プロシージャのテスト ケースを直接追加することは現在サポートされていません。 回避策として、それらの関数を呼び出す U-SQL スクリプトを含む U-SQL プロジェクトを作成し、それらに対するテスト ケースを記述することができます。 U-SQL データベース プロジェクトで定義されたテーブル値関数およびストアド プロシージャ用のテスト ケースをセットアップするには、次の手順に従います。

1. テスト目的の U-SQL プロジェクトを作成し、テーブル値関数およびストアド プロシージャを呼び出す U-SQL スクリプトを記述します。

1. この U-SQL プロジェクトにデータベース参照を追加します。 テーブル値関数およびストアド プロシージャの定義を取得するには、DDL ステートメントを含むデータベース プロジェクトを参照する必要があります。 詳細については、[データベースの参照](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)に関するページを参照してください。

1. テーブル値関数およびストアド プロシージャを呼び出す U-SQL スクリプト用のテスト ケースを追加します。 詳細については、[U-SQL スクリプト用のテスト ケースを追加する方法](data-lake-analytics-cicd-test.md#test-u-sql-scripts)に関するページを参照してください。

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Azure Pipelines を使用して U-SQL データベースをデプロイする

`PackageDeploymentTool.exe` には、U-SQL データベース デプロイ パッケージ **.usqldbpack** のデプロイに役立つプログラミングおよびコマンド ライン インターフェイスが用意されています。 SDK は [U-SQL SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)に含まれており、場所は **build/runtime/PackageDeploymentTool.exe** です。 `PackageDeploymentTool.exe` を使用して、U-SQL データベースを Azure Data Lake Analytics とローカル アカウントの両方にデプロイできます。

> [!NOTE]
>
> U-SQL データベース デプロイに対する、PowerShell コマンド ラインのサポートと Azure Pipelines リリース タスクのサポートは、現在保留中です。
>

Azure Pipelines でデータベース デプロイ タスクを設定するには、次の手順を実行します。

1. ビルドまたはリリース パイプラインで PowerShell Script タスクを追加し、次の PowerShell スクリプトを実行します。 このタスクは、`PackageDeploymentTool.exe` と `PackageDeploymentTool.exe` の Azure SDK 依存関係を取得するのに役立ちます。 **-AzureSDK** および **-DBDeploymentTool** パラメーターを設定すると、依存関係とデプロイ ツールを特定のフォルダーに読み込むことができます。 **-AzureSDK** パスを手順 2 の **-AzureSDKPath** パラメーターとして `PackageDeploymentTool.exe` に渡します。

   ```powershell
   <#
      This script is used for getting dependencies and SDKs for U-SQL database deployment.
      PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.

      Example :
          GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
   #>

   param (
      [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
      [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
      [string]$workingfolder = "" # Folder to execute these command lines
   )

   if ([string]::IsNullOrEmpty($workingfolder))
   {
      $scriptpath = $MyInvocation.MyCommand.Path
      $workingfolder = Split-Path $scriptpath
   }
   cd $workingfolder

   echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
   echo "Downloading required packages..."

   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
   iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

   echo "Extracting packages..."

   Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
   Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
   Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
   Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
   Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

   echo "Copy required DLLs to output folder..."

   mkdir $AzureSDK -Force
   mkdir $DBDeploymentTool -Force
   copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
   copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
   copy USQLSDK\build\runtime\*.* $DBDeploymentTool
   ```

2. ビルドまたはリリース パイプラインに **Command-Line タスク** を追加し、`PackageDeploymentTool.exe` を呼び出してスクリプトを入力します。 `PackageDeploymentTool.exe` は、定義された **$DBDeploymentTool** フォルダーにあります。 サンプル スクリプトは次のとおりです。 

   - U-SQL データベースをローカルに展開します。

      ```cmd
      PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
      ```

   - U-SQL データベースを Azure Data Lake Analytics のアカウントにデプロイするには、対話型認証モードを使用します。

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
      ```

   - U-SQL データベースを Azure Data Lake Analytics アカウントに追加するには、**シークレット** 認証を使用します。

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
      ```

   - U-SQL データベースを Azure Data Lake Analytics アカウントに追加するには、**certFile** 認証を使用します。

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
      ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>PackageDeploymentTool.exe のパラメーターの説明:

#### <a name="common-parameters"></a>共通パラメーター

| パラメーター | 説明 | Default value | 必須 |
|---------|-----------|-------------|--------|
|Package|デプロイする U-SQL データベース デプロイ パッケージのパス。|null|true|
|データベース|デプロイまたは作成されるデータベース名。|master|false|
|LogFile|ログ用のファイルのパス。 既定では標準出力 (コンソール) です。|null|false|
|LogLevel|ログ レベル:詳細、標準、警告、エラー。|LogLevel.Normal|false|

#### <a name="parameter-for-local-deployment"></a>ローカル デプロイのパラメーター

|パラメーター|説明|Default value|必須|
|---------|-----------|-------------|--------|
|DataRoot|ローカル データ ルート フォルダーのパス。|null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Azure Data Lake Analytics デプロイのパラメーター

|パラメーター|説明|Default value|必須|
|---------|-----------|-------------|--------|
|Account|アカウント名を使用して、デプロイ先の Azure Data Lake Analytics アカウント指定します。|null|true|
|ResourceGroup|Azure Data Lake Analytics アカウントの Azure リソース グループ名。|null|true|
|SubscriptionId|Azure Data Lake Analytics アカウントの Azure サブスクリプション ID。|null|true|
|Tenant|テナント名は Azure Active Directory (Azure AD) ドメイン名です。 Azure portal のサブスクリプション管理ページでそれを検索します。|null|true|
|AzureSDKPath|Azure SDK 内の依存アセンブリを検索するためのパス。|null|true|
|Interactive|認証に対話型モードを使用するかどうか。|false|false|
|ClientId|非対話型認証に必要な Azure AD アプリケーション ID。|null|非対話型認証に必要です。|
|Secrete|非対話型認証のシークレットまたはパスワード。 信頼できるセキュリティで保護された環境でのみ使用する必要があります。|null|非対話型認証に必須です。または SecreteFile を使用します。|
|SecreteFile|ファイルには、非対話型認証のシークレットまたはパスワードが保存されます。 現在のユーザーのみが読み取ることができるようにします。|null|非対話型認証に必須です。または Secrete を使用します。|
|CertFile|このファイルは、非対話型認証のために X.509 認証を保存します。 既定では、クライアントのシークレットの認証が使用されます。|null|false|
| JobPrefix | U-SQL DDL ジョブのデータベース デプロイ用のプレフィックス。 | Deploy_ + DateTime.Now | false |

## <a name="next-steps"></a>次のステップ

- [Azure Data Lake Analytics コードをテストする方法](data-lake-analytics-cicd-test.md)。
- [ローカル コンピューターで U-SQL スクリプトを実行する](data-lake-analytics-data-lake-tools-local-run.md)。
- [U-SQL データベース プロジェクトを使用して U-SQL データベースを開発する](data-lake-analytics-data-lake-tools-develop-usql-database.md)。