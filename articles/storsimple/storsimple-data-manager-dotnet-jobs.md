---
title: "Microsoft Azure StorSimple データ マネージャー ジョブに .NET SDK を使用する | Microsoft Docs"
description: ".NET SDK を使用して StorSimple データ マネージャー ジョブを起動する方法について説明します (プライベート プレビュー)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 37f795fe59496b0267120537115cf56d44cc5325
ms.openlocfilehash: 60cde851a466a5b4b0752908f11272eedb246b0a
ms.lasthandoff: 12/23/2016

---

# <a name="use-the-net-sdk-to-initiate-data-transformation-private-preview"></a>.Net SDK を使用してデータ変換を開始する (プライベート プレビュー)

## <a name="overview"></a>Overview

この記事では、StorSimple データ マネージャー サービスでデータ変換機能を使用して、StorSimple デバイス データを変換する方法について説明します。 変換されたデータは、クラウド内の他の Azure サービスによって使用されます。 この記事では、データ変換ジョブの開始と、そのジョブの完了を追跡するサンプル .NET コンソール アプリケーションを作成するためのチュートリアルも紹介します。

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件が満たされていることを確認します。
*   Visual Studio 2012、2013、または 2015 がインストールされている。
*   Azure Powershell がインストールされている。 [Azure PowerShell のダウンロード](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
*   データ変換ジョブを初期化するための構成設定 (この設定を取得する手順については、ここで説明します)。
*   リソース グループ内のハイブリッド データ リソースでジョブ定義が正しく構成されている。
*   すべての必須 dll。 この dll は [GitHub リポジトリ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls)からダウンロードします。
*   GitHub リポジトリの `Get-ConfigurationParams.ps1` [スクリプト](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)。

## <a name="step-by-step"></a>詳細な手順:

.NET を使用してデータ変換ジョブを開始する手順を次に示します。

1. 構成パラメーターを取得するには、次の手順を実行します。
    1. `C:\DataTransformation` の GitHub リポジトリ スクリプトから `Get-ConfigurationParams.ps1` をダウンロードします。
    1. GitHub リポジトリから `Get-ConfigurationParams.ps1` スクリプトを実行します。 次のコマンドを入力します。

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        ActiveDirectoryKey と AppName のすべての値を渡すことができます。


2. このスクリプトは次の値を出力します:      - クライアント ID      - テナント ID      - Active Directory キー (上記で入力した値と同じ)      - サブスクリプション ID

3. Visual Studio 2012、2013、または 2015 を使用して、C# .NET コンソール アプリケーションを作成します。

    1. **Visual Studio 2012/2013/2015**を起動します。
    1. **[ファイル]** をクリックし、**[新規作成]** をポイントして、**[プロジェクト]** をクリックします。
    2. **[テンプレート]** を展開し、**[Visual C#]** を選択します。
    3. 右側にあるプロジェクトの種類の一覧から **[コンソール アプリケーション]** を選択します。
    4. **名前**として「**DataTransformationApp**」を入力します。
    5. **場所**として「**C:\DataTransformation**」を入力します。
    6. **[OK]** をクリックしてプロジェクトを作成します。

4.  ここで、[dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) フォルダーに存在するすべての DLL を、作成したプロジェクトに**参照**として追加します。 dll ファイルをダウンロードするには、次の操作を行います。

    1. Visual Studio で、**[表示] > [ソリューション エクスプ ローラー]** の順に移動します。
    1. データ変換アプリ プロジェクトの左側にある矢印をクリックします。 **[参照]** をクリックし、**[参照の追加]** を右クリックします。
    2. パッケージ フォルダーの場所に移動し、すべての DLL を選択して、**[追加]**、**[OK]** の順にクリックします。

5. 次の **using** ステートメントをプロジェクト内のソース ファイル (Program.cs) に追加します。

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```


6. 次のコードでは、データ変換ジョブ インスタンスを初期化します。 これを **Main メソッド**に追加します。 先ほど取得した構成パラメーターの値を置き換えます。 **リソース グループ名**と**ハイブリッド データ リソース名**の値をプラグインします。 **リソース グループ名**は、ジョブ定義が構成されたハイブリッド データ リソースをホストするリソース グループの名前です。

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

8. 初期化したら、次のコードを追加して、ジョブ定義でデータ変換ジョブをトリガーします。 適切な**ジョブ定義名**をプラグインします。

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);

    ```

9. このジョブでは、StorSimple ボリュームのルート ディレクトリの一致するファイルを、指定されたコンテナーにアップロードします。 ファイルがアップロードされると、ジョブ定義と同じ名前の、(コンテナーと同じストレージ アカウントにある) キュー内のメッセージが破棄されます。 このメッセージは、ファイルの追加処理を開始するトリガーとして使用できます。

10. ジョブが開始されたら、次のコードを追加して、ジョブの完了を追跡します。

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


## <a name="next-steps"></a>次のステップ

[StorSimple Data Manger UI を使用してデータを変換します](storsimple-data-manager-ui.md)。

