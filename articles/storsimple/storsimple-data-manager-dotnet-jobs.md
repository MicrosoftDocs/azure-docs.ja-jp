---
title: Microsoft Azure StorSimple データ マネージャー ジョブに .NET SDK を使用する
description: StorSimple Data Manager サービス内で .NET SDK を使用し、StorSimple デバイス データを変換する方法について説明します。
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b18627d2806662d6d966af95d51873d5623b2393
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015538"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>.NET SDK を使用してデータ変換を開始する

## <a name="overview"></a>概要

この記事では、StorSimple データ マネージャー サービスでデータ変換機能を使用して、StorSimple デバイス データを変換する方法について説明します。 変換されたデータは、クラウド内の他の Azure サービスによって使用されます。

次の 2 つの方法で、データ変換ジョブを起動することができます。

- .NET SDK を使用する
- Azure Automation Runbook を使用する
 
  この記事では、データ変換ジョブを開始し、そのジョブの完了を追跡するサンプル .NET コンソール アプリケーションを作成する方法を詳しく説明します。 Automation 経由でデータ変換を開始する方法の詳細については、[Azure Automation Runbook を使用したデータ変換ジョブのトリガー](storsimple-data-manager-job-using-automation.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件が満たされていることを確認します。
*   コンピューターで、以下が実行されている。

    - Visual Studio 2012、2013、2015、または 2017。

    - Azure Powershell。 [Azure PowerShell のダウンロード](/powershell/azure/)。
*   リソース グループ内で StorSimple Data Manager に正しく構成されたジョブ定義。
*   すべての必須 dll。 この dll は [GitHub リポジトリ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls)からダウンロードします。
*   GitHub リポジトリの [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)スクリプト。

## <a name="step-by-step-procedure"></a>詳細な手順

.NET を使用してデータ変換ジョブを開始する手順を次に示します。

1. 構成パラメーターを取得するには、次の手順を実行します。
    1. `C:\DataTransformation` の GitHub リポジトリ スクリプトから `Get-ConfigurationParams.ps1` をダウンロードします。
    1. GitHub リポジトリから `Get-ConfigurationParams.ps1` スクリプトを実行します。 次のコマンドを入力します。

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        ActiveDirectoryKey と AppName のすべての値を渡すことができます。

2. このスクリプトは､次の値を出力します｡
    * クライアント ID
    * テナント ID
    * Active Directory キー (上記で入力したものと同じ)
    * サブスクリプション ID

        ![構成パラメーター スクリプトの出力](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Visual Studio 2012、2013、または 2015 を使用して、C# .NET コンソール アプリケーションを作成します。

    1. **Visual Studio 2012/2013/2015** を起動します。
    1. **[ファイル] > [新規] > [プロジェクト]** を選択します。

        ![プロジェクト 1 の作成](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. **[インストール済み] > [テンプレート] > [Visual C#] > [コンソール アプリケーション]** の順にクリックします。
    3. **名前** として「**DataTransformationApp**」を入力します。
    4. **場所** として「**C:\DataTransformation**」を入力します。
    6. **[OK]** をクリックしてプロジェクトを作成します。

        ![プロジェクト 2 の作成](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. ここで、[dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) フォルダーに存在するすべての dll を、作成したプロジェクトに **参照** として追加します。 dll ファイルを追加するには、次の手順を実行します。

   1. Visual Studio で、 **[表示] > [ソリューション エクスプ ローラー]** の順に移動します。
   2. データ変換アプリ プロジェクトの左側にある矢印をクリックします。 **[参照]** をクリックし、 **[参照の追加]** を右クリックします。
    
       ![dlls の追加 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. パッケージ フォルダーの場所に移動し、すべての DLL を選択して、 **[追加]** 、 **[OK]** の順にクリックします。

       ![dlls の追加 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. 次の **using** ステートメントをプロジェクト内のソース ファイル (Program.cs) に追加します。

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. 次のコードでは、データ変換ジョブ インスタンスを初期化します。 これを **Main メソッド** に追加します。 先ほど取得した構成パラメーターの値を置き換えます。 **リソース グループ名** と **ResourceName** の値をプラグインします。 **ResourceGroupName** は、ジョブ定義が構成された StorSimple Data Manager に関連付けられています。 **ResourceName** は、お使いの StorSimple Data Manager サービスの名前です。

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. ジョブ定義を実行するときに使用するパラメーターを指定します

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (または)

    実行時にジョブ定義パラメーターを変更する場合は、次のコードを追加します。

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. 初期化したら、次のコードを追加して、ジョブ定義でデータ変換ジョブをトリガーします。 適切な **ジョブ定義名** をプラグインします。

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    コードを貼り付けたら、ソリューションをビルドします。 データ変換ジョブ インスタンスを初期化するコード スニペットのスクリーンショットを次に示します。

   ![データ変換ジョブを初期化するコード スニペット](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. このジョブは、StorSimple ボリューム内のルート ディレクトリおよびファイル フィルターと一致するデータを変換し、指定された container/file 共有に配置します。 ファイルが変換されると、ジョブ定義と同じ名前のストレージ キュー (container/file 共有と同じストレージ アカウントにある) にメッセージが追加されます。 このメッセージは、ファイルの追加処理を開始するトリガーとして使用できます。

10. ジョブがトリガーされたら、次のコードを使用して、ジョブの完了を追跡します。 このコードの追加は、ジョブの実行のために必須ではありません。

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
    .NET を使用してジョブをトリガーするために使用するコード サンプル全体のスクリーン ショットを次に示します。

    ![.NET ジョブをトリガーするコードのスニペット全体](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>次のステップ

[StorSimple Data Manger UI を使用してデータを変換します](storsimple-data-manager-ui.md)。