---
title: Azure Service Fabric での定期的なバックアップと復元 | Microsoft Docs
description: アプリケーションデータの定期バックアップを可能にする Service Fabric の定期バックアップと復元機能を使用します。
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: 7078a1a5edc310c799690f0f7236dd0947e3290b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059187"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Azure Service Fabric での定期的なバックアップと復元 
> [!div class="op_single_selector"]
> * [Azure 上のクラスター](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [スタンドアロン クラスター](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric は、信頼性に優れた分散型のマイクロサービス ベースのクラウド アプリケーションの開発と管理を簡単にする分散システム プラットフォームです。 ステートレスなマイクロ サービスとステートフルなマイクロ サービスの両方を実行できます。 ステートフル サービスは、要求と応答、または完全なトランザクションを超えて変更可能な信頼できる状態を維持できます。 災害が原因でステートフル サービスの長時間のダウンや情報の損失が発生した場合、再開後にサービスの提供を続行するためには、最新のバックアップの状態を復元する必要があります。

Service Fabric は、複数のノードの状態をレプリケートして、サービスの高可用性を確保します。 クラスター内の 1 つのノードに障害が発生しても、サービスは引き続き利用できます。 ただし、広範な障害が発生した場合でも、サービス データの信頼性を確保しておくことをお勧めします。
 
たとえば、次のような状況を防ぐために、サービスのデータをバックアップすることが望まれます。
- Service Fabric クラスター全体が永久に失われた場合。
- サービス パーティションのレプリカの過半数の完全な損失。
- 状態が誤って削除されたり、破損したりするなどの管理エラー。 例: 十分な権限を持つ管理者が誤ってサービスを削除した。
- データの破損を引き起こすサービスのバグ。 これは、サービス コードのアップグレードにより、Reliable Collection に不正なデータが書き込まれ始めると起こる可能性があります。 このような場合は、コードとデータの両方を、以前の状態に復元する必要があります。
- オフライン データ処理 データを生成するサービスと切り離されて行われるビジネス インテリジェンスのために、データのオフライン処理を用意しておけば便利です。

Service Fabric には、ポイントインタイムの[バックアップと復元](service-fabric-reliable-services-backup-restore.md)を行う API が組み込まれています。 アプリケーション開発者は、これらの API を使用して、サービスの状態を定期的にバックアップできます。 さらに、サービス管理者が特定の時点 (アプリケーションをアップグレードする前など) にサービスの外部からバックアップをトリガーすることを望んでいるのであれば、開発者は、バックアップ (および復元) を API としてサービスから公開する必要があります。 バックアップの管理には、追加コストが発生します。 たとえば、完全バックアップに続けて、30 分ごとに 5 回の増分バックアップを実行したい場合があるとします。 完全バックアップを実行した後、その前に実行された増分バックアップを削除できます。 このアプローチではコードを追加する必要であり、それによってアプリケーションの開発中に追加コストが発生します。

Service Fabric のバックアップと復元サービスを使用すると、ステートフル サービスに格納された情報を簡単かつ自動的にバックアップできます。 定期的にアプリケーション データをバックアップすることは、データが失われたりサービスを利用できなくなったりしないように保護するために不可欠です。 Service Fabric には、オプションで提供されるバックアップと復元サービスがあります。このサービスを使用すると、追加のコードを記述することなく、ステートフルな Reliable Services の定期的なバックアップを構成できます (Actor Services も対象になります)。 これまでに実行したバックアップも簡単に復元できます。 


Service Fabric には、定期的なバックアップと復元機能に関連する次の機能を実現するための一連の API が用意されています。

- ステートフルな Reliable Services と Reliable Actors の定期的なバックアップのスケジュールを設定する。バックアップの (外部) 保存場所へのアップロードがサポートされます。 サポートされる保存場所
    - Azure Storage
    - ファイル共有 (オンプレミス)
- バックアップを列挙する
- パーティションのアドホック バックアップをトリガーする
- 以前のバックアップを使用してパーティションを復元する
- バックアップを一時停止する
- バックアップの保有期間を管理する (予定)

## <a name="prerequisites"></a>前提条件
* Service Fabric のバージョンが 6.4 以降の Fabric クラスター。 Azure リソース テンプレートを使用して Service Fabric クラスターを作成する手順については、こちらの[記事](service-fabric-cluster-creation-via-arm.md)を参照してください。
* バックアップを保存するストレージに接続するために必要なシークレットを暗号化する X.509 証明書。 X.509 証明書を取得または作成する方法については、[こちらの記事](service-fabric-cluster-creation-via-arm.md)を参照してください。
* Service Fabric SDK バージョン 3.0 以降を使用してビルドされた Service Fabric Reliable Stateful アプリケーション。 .NET Core 2.0 がターゲットであるアプリケーションは、Service Fabric SDK バージョン 3.1 以降を使用してビルドする必要があります。
* アプリケーションのバックアップを保存するための Azure ストレージ アカウントを作成します。
* 構成の呼び出しを行うため、Microsoft.ServiceFabric.Powershell.Http モジュール [プレビュー] をインストールします。

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Microsoft.ServiceFabric.Powershell.Http モジュールを使用して、任意の構成要求を行う前に、`Connect-SFCluster` コマンドを使用してクラスターが接続されていることを確認します。

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>バックアップと復元サービスの有効化

### <a name="using-azure-portal"></a>Azure Portal の使用

`Cluster Configuration` タブの `+ Show optional settings` の下で `Include backup restore service` チェック ボックスをオンにします。

![ポータルでバックアップ復元サービスを有効にする][1]


### <a name="using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用
最初に、"_バックアップと復元サービス_" をクラスターで有効にする必要があります。 デプロイするクラスター用テンプレートを用意します。 [サンプル テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)を使用することも、Resource Manager テンプレートを作成することもできます。 次の手順で、"_バックアップと復元サービス_" を有効にします。

1. まず、`Microsoft.ServiceFabric/clusters` リソースの `apiversion` が **`2018-02-01`** に設定されていることを確認し、設定されていない場合は次のスニペットのように更新します。

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 下のスニペットに示すように、次の `addonFeatures` セクションを `properties` セクションの下に追加することで、"_バックアップと復元サービス_" を有効にします。 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. 資格情報を暗号化する X.509 証明書を構成します。 これは、ストレージに接続するために提供された証明書が永続化の前に暗号化されることを保証するために重要です。 下のスニペットに示すように、次の `BackupRestoreService` セクションを `fabricSettings` セクションの下に追加することで、暗号化証明書を有効にします。 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. 上記の変更でクラスター テンプレートを更新したら、その変更を適用して、デプロイ/アップグレードを完了します。 完了すると、"_バックアップと復元サービス_" がクラスターで実行されます。 このサービスの URI は `fabric:/System/BackupRestoreService` です。サービスは、Service Fabric エクスプローラーでシステム サービス セクションの下に置くことができます。 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Reliable Stateful サービスと Reliable Actors の定期バックアップの有効化
Reliable Stateful サービスと Reliable Actors の定期バックアップを有効にする手順について説明します。 これらの手順は、以下を前提としています。
- クラスターが X.509 セキュリティと "_バックアップと復元サービス_" を使用してセットアップされている。
- Reliable Stateful サービスがクラスターにデプロイされている。 このクイックスタート ガイドでは、アプリケーションの URI は `fabric:/SampleApp` であり、このアプリケーションに属する Reliable Stateful サービスの URI は `fabric:/SampleApp/MyStatefulService` です。 このサービスは 1 つのパーティションでデプロイされ、パーティション ID は `974bd92a-b395-4631-8a7f-53bd4ae9cf22` です。
- 管理者ロールのクライアント証明書が、スクリプトが呼び出されるコンピューター上の _CurrentUser_ 証明書ストアの保存場所の _My_ (_Personal_) ストアにインストールされている。 この例では、この証明書の拇印として `1b7ebe2174649c45474a4819dafae956712c31d3` を使用します。 クライアント証明書の詳細については、「[ロールベースのアクセス制御 (Service Fabric クライアント用)](service-fabric-cluster-security-roles.md)」を参照してください。

### <a name="create-backup-policy"></a>バックアップ ポリシーを作成する

最初の手順は、バックアップ スケジュール、バックアップ データのターゲット ストレージ、ポリシー名、および完全バックアップをトリガーする前に許可される増分バックアップの最大数、バックアップ ストレージ用のアイテム保持ポリシーを記述するバックアップ ポリシーの作成です。 

バックアップ ストレージには、先ほど作成した Azure ストレージ アカウントを使用します。 コンテナー `backup-container` は、バックアップを格納するために構成されます。 まだ存在しない場合は、バックアップのアップロード中に、この名前のコンテナーが作成されます。 Azure Storage アカウントの有効な接続文字列を `ConnectionString`に指定して、`account-name` を実際のストレージ アカウント名に、`account-key` を実際のストレージ アカウント キーにそれぞれ置き換えます。

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft.ServiceFabric.Powershell.Http モジュールを使用した PowerShell

新しいバックアップ ポリシーを作成するため、PowerShell コマンドレットを実行します。 `account-name` を実際のストレージ アカウント名に置き換え、`account-key` を実際のストレージ アカウント キーに置き換えます。

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>PowerShell を使用した Rest の呼び出し

必要な REST API を呼び出す次の PowerShell スクリプトを実行して、新しいポリシーを作成します。 `account-name` を実際のストレージ アカウント名に置き換え、`account-key` を実際のストレージ アカウント キーに置き換えます。

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

```

### <a name="enable-periodic-backup"></a>定期バックアップを有効にする
アプリケーションのデータ保護要件を満たすバックアップ ポリシーを定義した後、そのポリシーをアプリケーションに関連付けする必要があります。 バックアップ ポリシーは、要件に応じて、アプリケーション、サービス、またはパーティションに関連付けることができます。

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft.ServiceFabric.Powershell.Http モジュールを使用した PowerShell

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>PowerShell を使用した Rest の呼び出し

必要な REST API を呼び出す次の PowerShell スクリプトを実行して、上の手順で作成した `BackupPolicy1` という名前のバックアップ ポリシーを `SampleApp` という名前のアプリケーションに関連付けます。

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>定期バックアップが動作していることを確認する

アプリケーション レベルでのバックアップを有効にすると、アプリケーションの下で Reliable Stateful サービスと Reliable Actors に属しているすべてのパーティションで、バックアップ ポリシーに従って定期バックアップが開始されます。 

![パーティションがバックアップされる正常性イベント][0]

### <a name="list-backups"></a>バックアップを一覧表示する

_GetBackups_ API を使用して、アプリケーションの Reliable Stateful サービスと Reliable Actors に属するすべてのパーティションに関連付けられているバックアップを列挙できます。 アプリケーション、サービス、またはパーティションのバックアップを列挙できます。

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Microsoft.ServiceFabric.Powershell.Http モジュールを使用した PowerShell

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>PowerShell を使用した Rest の呼び出し

HTTP API を呼び出す次の PowerShell スクリプトを実行して、`SampleApp` アプリケーション内のすべてのパーティションに対して作成されたバックアップを列挙します。

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

## <a name="limitation-caveats"></a>制限事項/注意事項
- Service Fabric PowerShell コマンドレットは、プレビュー モードです。
- Linux 上の Service Fabric クラスターはサポートされません。

## <a name="next-steps"></a>次の手順
- [定期的なバックアップの構成について](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [バックアップと復元用の REST API リファレンス](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png

