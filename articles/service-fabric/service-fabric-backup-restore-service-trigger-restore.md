---
title: Azure Service Fabric でのバックアップの復元
description: Service Fabric の定期バックアップと復元機能を使用して、アプリケーション データのバックアップからデータを復元できます。
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 8566d82ef0d91caff47ff17a9cb12fcdc8241884
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928030"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Azure Service Fabric でのバックアップの復元

Azure Service Fabric では、Reliable Stateful サービスおよび Reliable Actors は、要求と応答のトランザクションが完了した後、変更可能な信頼できる状態を維持できます。 ステートフル サービスは、災害のために長時間停止したり、情報を失う場合があります。 その場合は、動作し続けられるように、最新の許容可能なバックアップからサービスを復元する必要があります。

たとえば、データをバックアップして次のシナリオから保護するようにサービスを構成できます。

- **ディザスター リカバリーの場合**:Service Fabric クラスター全体の完全な損失。
- **データ損失の場合**:サービス パーティションのレプリカの過半数の完全な損失。
- **データ損失の場合**:サービスの不測の削除または破損。 たとえば、管理者が誤ってサービスを削除した場合です。
- **データ破損の場合**:サービスのバグにより引き起こされるデータの破損。 たとえば、データの破損は、サービス コードのアップグレードにより、Reliable Collection に不正なデータが書き込まれると起こる可能性があります。 このような場合は、コードとデータの両方を、以前の状態に復元する必要があります。

## <a name="prerequisites"></a>前提条件

- 復元をトリガーするには、_Fault Analysis Service (FAS)_ がクラスターで有効になっている必要があります。
- "_バックアップ復元サービス (BRS)_ " がバックアップを作成しました。
- 復元は、パーティションでのみトリガーできます。
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


## <a name="triggered-restore"></a>トリガーされた復元

次のシナリオのいずれかで復元をトリガーできます。

- "_ディザスター リカバリー_" のデータ復元。
- "_データ破損/データ損失_" の場合のデータ復元。

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>ディザスター リカバリーの場合のデータ復元

Service Fabric クラスター全体が失われた場合は、Reliable Stateful サービスと Reliable Actors のパーティションのデータを復元できます。 目的のバックアップは、[バックアップ ストレージ詳細での GetBackupAPI](/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation) を使用するときに一覧から選択できます。 アプリケーション、サービス、またはパーティションのバックアップを列挙できます。

次の例では、失われたクラスターが 「[Reliable Stateful サービスと Reliable Actors の定期バックアップの有効化](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)」で言及されている同じクラスターであるとします。 この場合、`SampleApp` は、バックアップ ポリシーを有効にしてデプロイされ、バックアップは Azure Storage に対して構成されます。

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft.ServiceFabric.Powershell.Http モジュールを使用した PowerShell

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>PowerShell を使用した Rest の呼び出し

REST API を使用して `SampleApp` アプリケーション内のすべてのパーティションに対して作成されたバックアップバックアップの一覧を返す PowerShell スクリプトを実行します。 この API は、使用可能なバックアップを一覧するためにバックアップ ストレージ情報を必要とします。

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

上記を実行したときのサンプル出力:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

復元をトリガーするには、バックアップのいずれかを選択します。 たとえば、ディザスター リカバリー用の最新のバックアップは、次のバックアップになります。

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

復元 API に対して、_BackupId_ と _BackupLocation_ の詳細を提供する必要があります。

[パーティション スキーム](service-fabric-concepts-partitioning.md#get-started-with-partitioning)で詳しく述べているように、代替クラスター内のバックアップ先パーティションを選択する必要もあります。 代替クラスターのバックアップは、元の失われたクラスターの、パーティション スキームで指定されたパーティションに復元されます。

代替クラスターでのパーティション ID が `1c42c47f-439e-4e09-98b9-88b8f60800c6` である場合、"_範囲パーティション分割 (UniformInt64Partition)_ " に対する上位キーと下位キーを比較することによって、元のクラスターのパーティション ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` にマップできます。

"_名前付きパーティション分割_" では、名前の値を比較して、代替クラスターの対象パーティションが識別されます。

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft.ServiceFabric.Powershell.Http モジュールを使用した PowerShell

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>PowerShell を使用した Rest の呼び出し

次の[復元 API](/rest/api/servicefabric/sfclient-api-restorepartition) を使用することにより、バックアップ クラスター パーティションに対して復元を要求します。

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

TrackRestoreProgress を使用して復元の進行状況を追跡できます。

> [!NOTE]
> Powershell を使用してパーティションを復元するときに、backuplocation に '$' が含まれている場合は、'~' を使用してエスケープします。
>

### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer の使用
Service Fabric Explorer から復元をトリガーできます。 Service Fabric Explorer の設定で、詳細設定モードが有効になっていることを確認します。
1. 目的のパーティションを選択し、[アクション] をクリックします。 
2. [Trigger Partition Restore]\(パーティションの復元のトリガー\) を選択し、Azure の情報を入力します。

    ![パーティションの復元のトリガー][2]

    または、FileShare の場合:

    ![パーティションの復元のトリガー (FileShare)][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>"_データ破損_/_データ損失_" の場合のデータ復元

"_データ損失_" または "_データ破損_" の場合、Reliable Stateful サービスと Reliable Actors のパーティション用のバックアップ パーティションは、選択した任意のバックアップに復元できます。

次の例は、「[Reliable Stateful サービスと Reliable Actors の定期バックアップの有効化](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)」の続きです。 この例では、バックアップ ポリシーがパーティションで有効になっており、サービスは Azure Storage に必要な頻度でバックアップを作成しています。

[GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups) の出力からバックアップを選択します。 このシナリオでは、バックアップは以前と同じクラスターから生成されます。

復元をトリガーするには、一覧からバックアップを選択します。 現在の "_データ損失_/_データ破損_" には、次のバックアップを選択します。

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

復元 API には、_BackupId_ と _BackupLocation_ の詳細を提供します。 クラスターでバックアップが有効になっているので、Service Fabric の "_バックアップ復元サービス (BRS)_ " では、関連付けられているバックアップ ポリシーから適切なストレージの場所が識別されます。


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft.ServiceFabric.Powershell.Http モジュールを使用した PowerShell

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>PowerShell を使用した Rest の呼び出し

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

TrackRestoreProgress を使用して復元の進行状況を追跡できます。

> [!NOTE]
> Powershell を使用してパーティションを復元するときに、backuplocation に '$' が含まれている場合は、'~' を使用してエスケープします。
>

## <a name="track-restore-progress"></a>復元の進行状況を追跡する

Reliable Stateful サービスまたは Reliable Actors のパーティションで受け付けられる復元要求は、一度に 1 つだけです。 パーティションは、現在の復元要求が完了した後にのみ別の要求を受け入れます。 パーティションが異なれば、同時に複数の復元要求をトリガーできます。

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft.ServiceFabric.Powershell.Http モジュールを使用した PowerShell

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>PowerShell を使用した Rest の呼び出し

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

復元要求は次の順序で進行します。

1. **受け付け済み**:"_受け付け済み_" の復元状態は、正しい要求パラメーターで要求されたパーティションが開始されたことを示します。
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **進行中**:"_進行中_" の復元状態は、要求で指定されたバックアップでパーティションの復元が行われていることを示します。 パーティションは "_データ損失_" 状態を報告します。
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **成功**、**失敗**、または **タイムアウト**:要求された復元は、次の状態のいずれかで完了する可能性があります。 各状態の重要性と応答の詳細は次のとおりです。
    - **成功**:"_成功_" の復元状態は、回復したパーティション状態を示します。 パーティションは、_RestoredEpoch_ および _RestoredLSN_ の状態を UTC 時刻とともに報告します。

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **失敗**:"_エラー_" の復元状態は、復元要求の失敗を示します。 エラーの原因が報告されます。

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **タイムアウト**:"_タイムアウト_" の復元状態は、要求がタイムアウトしたことを示します。 [RestoreTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) を大きくして新しい復元要求を作成してください。 既定のタイムアウト時間は 10 分です。 再度復元を要求する前に、パーティションがデータ損失状態ではなくなっていることを確認してください。
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>自動復元

Service Fabric クラスター内の Reliable Stateful サービスおよび Reliable Actors パーティションを、"_自動復元_" するように構成できます。 バックアップ ポリシーで `AutoRestore` を _true_ に設定します。 "_自動復元_" を有効にすると、データの損失が報告された場合、最新のパーティション バックアップからデータが自動的に復元されます。 詳細については、次を参照してください。

- [バックアップ ポリシーでの自動復元の有効化](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [RestorePartition API リファレンス](/rest/api/servicefabric/sfclient-api-restorepartition)
- [GetPartitionRestoreProgress API リファレンス](/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>次のステップ
- [定期的なバックアップの構成について](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [バックアップと復元用の REST API リファレンス](/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png
