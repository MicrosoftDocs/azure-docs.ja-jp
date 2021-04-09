---
title: AzureRM PowerShell スクリプトを使用して StorSimple デバイスを管理する
description: Azure Resource Manager SDK ベースのスクリプトを使用して StorSimple 8000 シリーズ デバイスを管理する方法について説明します。
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: e3339f883d177366a64187e0c090752527802c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968776"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Azure Resource Manager SDK ベースのスクリプトを使用して StorSimple デバイスを管理する

この記事では、Azure Resource Manager SDK ベースのスクリプトを使用して StorSimple 8000 シリーズ デバイスを管理する方法について説明します。 これらのスクリプトを実行するように環境を構成する手順を示すサンプル スクリプトも含まれています。

この記事は、Azure Portal で実行している StorSimple 8000 シリーズ デバイスにのみ適用されます。

## <a name="sample-scripts"></a>サンプルのスクリプト

以下のサンプル スクリプトは、さまざまな StorSimple ジョブを自動化するために使用できます。

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Azure Resource Manager SDK ベースのサンプル スクリプトの表

| Azure Resource Manager スクリプト                    | 説明                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | このスクリプトを使用して、StorSimple デバイスによるサービス データ暗号化キーの変更を承認できます。                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | このスクリプトでは、8010 または 8020 の StorSimple Cloud Appliance を作成します。 クラウド アプライアンスを構成し、StorSimple Data Manager サービスに登録できます。                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | このスクリプトでは、StorSimple ボリュームを作成または変更します。                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | このスクリプトでは、StorSimple デバイス マネージャー サービスに登録されているデバイスのすべてのバックアップを一覧表示します。                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | このスクリプトでは、StorSimple デバイスのすべてのバックアップ ポリシーを取得します。                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | このスクリプトでは、StorSimple デバイス マネージャー サービスで実行されているすべての StorSimple ジョブを取得します。                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | このスクリプトでは、更新サーバーをスキャンし、StorSimple デバイスにインストールできる更新プログラムがあるかどうかを確認します。                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | このスクリプトでは、使用可能な更新プログラムを StorSimple デバイスにインストールします。                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | このスクリプトでは、手動のクラウド スナップショットを開始し、指定されたリテンション期間を過ぎたクラウド スナップショットを削除します。                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | この Azure Automation Runbook PowerShell スクリプトでは、すべてのバックアップ ジョブの状態をレポートします。                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | このスクリプトでは、1 つのバックアップ オブジェクトを削除します。                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | このスクリプトでは、StorSimple デバイスの手動バックアップを開始します。                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | このスクリプトでは、StorSimple デバイス マネージャー サービスに登録されているすべての 8010/8020 StorSimple Cloud Appliance のサービス データ暗号化キーを更新します。                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | このスクリプトでは、バックアップ ポリシーに関連付けられているすべてのスケジュールを分析した後、不足しているバックアップを強調表示します。 使用可能なバックアップの一覧でバックアップ カタログも検証します。             |




## <a name="configure-and-run-a-sample-script"></a>サンプル スクリプトの構成と実行

このセクションでは、スクリプト例を使用して、スクリプトを実行するために必要なさまざまな手順を詳しく説明します。

### <a name="prerequisites"></a>前提条件

開始する前に、次の要件が満たされていることを確認します。

*   Azure PowerShell がインストールされている。 Azure PowerShell モジュールをインストールするには、次の手順を実行します。
    * Windows 環境では、「[Azure PowerShell のインストールおよび構成](/powershell/azure/install-az-ps)」の手順に従います。 StorSimple の Windows Server ホストに Azure PowerShell をインストールできます (Windows Server ホストを使用している場合)。
    * Linux または MacOS 環境では、「[macOS および Linux での Azure PowerShell のインストールおよび構成](/powershell/azure/install-az-ps)」の手順に従います。

Azure PowerShell の使用方法の詳細については、「[Azure PowerShell を使ってみる](/powershell/azure/get-started-azureps)」をご覧ください。

### <a name="run-azure-powershell-script"></a>Azure PowerShell スクリプトの実行

この例で使用するスクリプトは、StorSimple デバイス上のすべてのジョブを一覧表示します。 これには、成功、失敗、または進行中のジョブが含まれます。 スクリプトをダウンロードして実行するには、次の手順を実行します。

1. Azure PowerShell を起動します。 新しいフォルダーを作成し、ディレクトリを新しいフォルダーに変更します。

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. 前の手順で作成したフォルダーに [NuGet CLI をダウンロード](https://www.nuget.org/downloads)します。 _nuget.exe_ にはさまざまなバージョンがあります。 SDK に対応するバージョンを選択します。 各ダウンロード リンクは、 _.exe_ ファイルを直接ポイントします。 ブラウザーから実行するのではなく、ファイルを右クリックしてコンピューターに保存してください。

    次のコマンドを実行して、スクリプトをダウンロードし、前に作成した同じフォルダーに格納することもできます。
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. 依存する SDK をダウンロードします。

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. サンプル GitHub プロジェクトからスクリプトをダウンロードします。

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. スクリプトを実行します。 認証を求められた場合は、Azure 資格情報を入力します。 このスクリプトは、StorSimple デバイス上のすべてのジョブのフィルター処理された一覧を出力します。
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>サンプル出力

サンプル スクリプトを実行すると、次の出力が表示されます。 出力には、2017 年 9 月 25 日に開始し、2017 年 10 月 2 日までに完了した、登録済みデバイスで実行されたすべてのジョブが含まれています。

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>次のステップ

[StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理](storsimple-8000-manager-service-administration.md)。