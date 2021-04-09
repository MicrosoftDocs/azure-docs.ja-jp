---
title: Azure Service Fabric でのオンデマンド バックアップ
description: Service Fabric でバックアップと復元機能を使用し、必要に応じてアプリケーションのデータをバックアップします。
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d7986c8cd8d0714215c7b4dc57170be346e627ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928047"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Azure Service Fabric でのオンデマンド バックアップ

障害やデータ損失のシナリオに対応するため、Reliable Stateful サービスと Reliable Actors のデータをバックアップできます。

Azure Service Fabric には、[データの定期的なバックアップ](service-fabric-backuprestoreservice-quickstart-azurecluster.md)機能と、必要に応じたデータのバックアップ機能があります。 オンデマンド バックアップは、基になるサービスまたはその環境での計画的な変更による "_データ損失_/_データ破損_" から保護できるため、便利です。

オンデマンド バックアップ機能は、サービスまたはサービス環境の操作が手動でトリガーされる前に、サービスの状態をキャプチャする場合に役立ちます。 たとえば、サービスをアップグレードまたはダウングレードするときにサービス バイナリに変更を加える場合があります。 このような場合、オンデマンド バックアップは、アプリケーション コードのバグによる破損からデータを保護するために役立ちます。
## <a name="prerequisites"></a>前提条件

- 構成の呼び出しを行うため、Microsoft.ServiceFabric.Powershell.Http モジュール (プレビュー) をインストールします。

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> PowerShellGet のバージョンが 1.6.0 未満の場合、更新して *-AllowPrerelease* フラグのサポートを追加する必要があります。
>
> `Install-Module -Name PowerShellGet -Force`

- Microsoft.ServiceFabric.Powershell.Http モジュールを使用して、任意の構成要求を行う前に、`Connect-SFCluster` コマンドを使用してクラスターが接続されていることを確認します。

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>オンデマンド バックアップをトリガーする

オンデマンド バックアップには、バックアップ ファイルをアップロードするためのストレージの詳細が必要です。 オンデマンド バックアップの場所は、定期的なバックアップ ポリシーまたはオンデマンド バックアップ要求のいずれの場合でも指定します。

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>定期的バックアップ ポリシーで指定されているストレージへのオンデマンド バックアップ

ストレージへの追加のオンデマンド バックアップに Reliable Stateful サービスまたは Reliable Actor のパーティションを使用するように定期的バックアップ ポリシーを構成することができます。

次のケースは、「[Reliable Stateful サービスと Reliable Actors の定期バックアップの有効化](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)」のシナリオの続きです。 このケースでは、バックアップ ポリシーでパーティションの使用を有効にして、Azure Storage で設定した頻度でバックアップを実行します。

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft.ServiceFabric.Powershell.Http モジュールを使用した PowerShell

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>PowerShell を使用した Rest の呼び出し

[BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) API を使用して、パーティション ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` のオンデマンド バックアップのトリガーを設定します。

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API を使用して、[オンデマンド バックアップの進行状況](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)の追跡を有効にします。

### <a name="on-demand-backup-to-specified-storage"></a>指定したストレージへのオンデマンド バックアップ

Reliable Stateful サービスまたは Reliable Actor のパーティションのオンデマンド バックアップを要求することができます。 オンデマンド バックアップ要求の一部として、ストレージ情報を指定します。


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft.ServiceFabric.Powershell.Http モジュールを使用した PowerShell

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>PowerShell を使用した Rest の呼び出し

[BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) API を使用して、パーティション ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` のオンデマンド バックアップのトリガーを設定します。 次の Azure Storage 情報を含めます。

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API を使用して、[オンデマンド バックアップの進行状況](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)の追跡を設定することができます。

### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer の使用
Service Fabric Explorer の設定で、詳細設定モードが有効になっていることを確認します。
1. 目的のパーティションを選択し、[アクション] をクリックします。 
2. [Trigger Partition Backup]\(パーティションのバックアップのトリガー\) を選択し、Azure の情報を入力します。

    ![パーティションのバックアップのトリガー][0]

    または、FileShare の場合:

    ![パーティションのバックアップのトリガー (FileShare)][1]

## <a name="tracking-on-demand-backup-progress"></a>オンデマンド バックアップの進行状況の追跡

Reliable Stateful サービスまたは Reliable Actors のパーティションで受け付けられるオンデマンド バックアップ要求は、一度に 1 つだけです。 別の要求は、現在のオンデマンド バックアップ要求が完了した後にのみ受け付けられます。

異なる複数のパーティションから同時にオンデマンド バックアップ要求をトリガーすることができます。


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft.ServiceFabric.Powershell.Http モジュールを使用した PowerShell

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>PowerShell を使用した Rest の呼び出し

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

オンデマンド バックアップ要求は、次のような状態になる可能性があります。

- **受け付け済み**:バックアップはパーティションで開始され、進行中です。
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **成功**、**失敗**、または **タイムアウト**:要求されたオンデマンド バックアップは、次の状態のいずれかで完了する可能性があります。
  - **成功**:"_成功_" バックアップ状態は、パーティションの状態が正常にバックアップされたことを示します。 応答では、パーティションの _BackupEpoch_ および _BackupLSN_ と UTC 時刻が提供されます。
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **失敗**:"_失敗_" バックアップ状態は、パーティションの状態のバックアップ中にエラーが発生したことを示します。 エラーの原因は応答で示されます。
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **タイムアウト**:"_タイムアウト_" バックアップ状態は、パーティション状態のバックアップを指定された期間で作成できなかったことを示します。 既定のタイムアウト値は 10 分です。 このシナリオでは、さらに大きい [BackupTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) を指定して新しいオンデマンド バックアップ要求を開始します。
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>次のステップ

- [Azure Service Fabric の定期バックアップ構成を理解する](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Compute REST API リファレンス](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png
