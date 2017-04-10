---
title: "Azure PowerShell を使用した Azure Redis Cache の管理 | Microsoft Docs"
description: "Azure PowerShell を使用して Azure Redis Cache の管理タスクを実行する方法について説明します。"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 1136efe5-1e33-4d91-bb49-c8e2a6dca475
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 9d76e82b1658c3ea4dd6631bae232d17f375ab33
ms.openlocfilehash: 61c0fd56aad1cc589138aa02ea43ef315edf9baf


---
# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Azure PowerShell を使用した Azure Redis Cache の管理
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

このトピックでは、Azure Redis Cache インスタンスの作成、更新、スケールなどの一般的なタスクを実行する方法、アクセス キーを再生成する方法、キャッシュに関する情報を表示する方法について説明します。 Azure Redis Cache 用の PowerShell コマンドレットの詳細な一覧については、 [Azure Redis Cache コマンドレット](https://msdn.microsoft.com/library/azure/mt634513.aspx)に関するページをご覧ください。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

クラシック デプロイメント モデルについて詳しくは、「[Azure Resource Manager とクラシック デプロイ: デプロイ モデルとリソースの状態について](../azure-resource-manager/resource-manager-deployment-model.md#classic-deployment-characteristics)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure PowerShell をインストール済みである場合、Azure PowerShell Version 1.0.0 以降であることが必要です。 インストールした Azure PowerShell のバージョンは、Azure PowerShell コマンド プロンプトで次のコマンドを使用して確認できます。

    Get-Module azure | format-table version


まず、次のコマンドで Azure にログインする必要があります。

    Login-AzureRmAccount

Microsoft Azure のサインイン ダイアログで、Azure アカウントの電子メール アドレスとそのパスワードを指定します。

次に、Azure サブスクリプションが複数ある場合は、使用する Azure サブスクリプションを設定する必要があります。 現在のサブスクリプションを一覧表示するには、次のコマンドを実行します。

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

サブスクリプションを指定するには、次のコマンドを実行します。 次の例では、サブスクリプション名は `ContosoSubscription`です。

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Azure リソース マネージャーで Windows PowerShell を使用するには、以下が必要です。

* Windows PowerShell バージョン 3.0 または 4.0。 Windows PowerShell のバージョンを調べるには、`$PSVersionTable` と入力して、`PSVersion` の値が 3.0 か 4.0 かを確認します。 互換バージョンをインストールするには、「[Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595)」または「[Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)」を参照してください。

このチュートリアルに表示されているコマンドレットの詳しいヘルプを確認には、Get-Help コマンドレットを使用します。

    Get-Help <cmdlet-name> -Detailed

たとえば、 `New-AzureRmRedisCache` コマンドレットについてのヘルプを確認するには、次のように入力します。

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-azure-government-cloud-or-azure-china-cloud"></a>Azure Government Cloud または Azure China Cloud に接続する方法
既定では、Azure 環境はグローバル Azure クラウド インスタンスを表す `AzureCloud`です。 別のインスタンスに接続するには、`Add-AzureRmAccount` コマンドと `-Environment` または -`EnvironmentName` コマンド ライン スイッチを使用し、任意の環境または環境名を指定します。

利用可能な環境の一覧を表示するには、 `Get-AzureRmEnvironment` コマンドレットを実行します。

### <a name="to-connect-to-the-azure-government-cloud"></a>Azure Government Cloud に接続するには
Azure Government Cloud に接続するには、次のいずれかのコマンドを使用します。

    Add-AzureRMAccount -EnvironmentName AzureUSGovernment

または

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Azure Government Cloud でキャッシュを作成するには、次のいずれかの場所を使用します。

* 米国政府バージニア州
* 米国政府アイオワ州

Azure Government Cloud の詳細については、「[Microsoft Azure Government](https://azure.microsoft.com/features/gov/)」と「[Microsoft Azure Government 開発者向けガイド](../azure-government-developer-guide.md)」をご覧ください。

### <a name="to-connect-to-the-azure-china-cloud"></a>Azure China Cloud に接続するには
Azure China Cloud に接続するには、次のいずれかのコマンドを使用します。

    Add-AzureRMAccount -EnvironmentName AzureChinaCloud

または

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Azure China Cloud でキャッシュを作成するには、次のいずれかの場所を使用します。

* 中国 (東部)
* 中国 (北部)

Azure China Cloud の詳細については、 [中国の 21Vianet が運営している AzureChinaCloud for Azure](http://www.windowsazure.cn/)に関するページをご覧ください。

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Azure Redis Cache 用の PowerShell で使用されるプロパティ
次の表は、Azure PowerShell を使用して Azure Redis Cache インスタンスを作成し、管理するときに一般的に使用されるパラメーターのプロパティと説明を示しています。

| パラメーター | Description | 既定値 |
| --- | --- | --- |
| Name |キャッシュの名前 | |
| 場所 |キャッシュの場所 | |
| ResourceGroupName |キャッシュを作成するリソース グループの名前 | |
| サイズ |キャッシュのサイズ。 有効な値: P1、P2、P3、P4、C0、C1、C2、C3、C4、C5、C6、250 MB、1 GB、2.5 GB、6 GB、13 GB、26 GB、53 GB |1GB |
| ShardCount |クラスタリングが有効になっている Premium キャッシュを作成するときに作成するシャードの数。 有効な値: 1、2、3、4、5、6、7、8、9、10 | |
| SKU |キャッシュの SKU を指定します。 有効な値: Basic、Standard、Premium |標準 |
| RedisConfiguration |Redis 構成の設定を指定します。 各設定の詳細については、次の「 [RedisConfiguration プロパティ](#redisconfiguration-properties) 」の表をご覧ください。 | |
| EnableNonSslPort |非 SSL ポートが有効になっているかどうかを示します。 |False |
| MaxMemoryPolicy |このパラメーターは廃止されました。代わりに、RedisConfiguration を使用します。 | |
| StaticIP |VNET でキャッシュをホストする場合に、キャッシュのサブネットで一意の IP アドレスを指定します。 指定していない場合、サブネットから自動的にアドレスが 1 つ選択されます。 | |
| サブネット |VNET でキャッシュをホストする場合に、キャッシュをデプロイするサブネットの名前を指定します。 | |
| VirtualNetwork |VNET でキャッシュをホストする場合に、キャッシュをデプロイする VNET のリソース ID を指定します。 | |
| KeyType |アクセス キーを更新するときに再生成するアクセス キーを指定します。 有効な値: Primary、Secondary | |

### <a name="redisconfiguration-properties"></a>RedisConfiguration プロパティ
| プロパティ | Description | 価格レベル |
| --- | --- | --- |
| rdb-backup-enabled |[Redis データ永続化](cache-how-to-premium-persistence.md) が有効かどうか。 |Premium のみ |
| rdb-storage-connection-string | [Redis データ永続化](cache-how-to-premium-persistence.md) |Premium のみ |
| rdb-backup-frequency | [Redis データ永続化](cache-how-to-premium-persistence.md) |Premium のみ |
| maxmemory-reserved |キャッシュ以外のプロセスに [予約済みのメモリ](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) を構成する |Standard と Premium |
| maxmemory-policy |キャッシュに [削除ポリシー](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) を構成する |すべての価格レベル |
| notify-keyspace-events | [キースペース通知](cache-configure.md#keyspace-notifications-advanced-settings) |Standard と Premium |
| hash-max-ziplist-entries |小規模の集計データの種類に [メモリ最適化](http://redis.io/topics/memory-optimization) を構成する |Standard と Premium |
| hash-max-ziplist-value |小規模の集計データの種類に [メモリ最適化](http://redis.io/topics/memory-optimization) を構成する |Standard と Premium |
| set-max-intset-entries |小規模の集計データの種類に [メモリ最適化](http://redis.io/topics/memory-optimization) を構成する |Standard と Premium |
| zset-max-ziplist-entries |小規模の集計データの種類に [メモリ最適化](http://redis.io/topics/memory-optimization) を構成する |Standard と Premium |
| zset-max-ziplist-value |小規模の集計データの種類に [メモリ最適化](http://redis.io/topics/memory-optimization) を構成する |Standard と Premium |
| データベース |データベースの数を構成する。 このプロパティは、キャッシュの作成時にのみ構成できます。 |Standard と Premium |

## <a name="to-create-a-redis-cache"></a>Redis Cache の作成方法
Azure Redis Cache インスタンスを新規作成するには、 [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) コマンドレットを使用します。

> [!IMPORTANT]
> Azure ポータルを使用してサブスクリプションに初めて Redis Cache を作成すると、そのサブスクリプションの `Microsoft.Cache` 名前空間がポータルによって登録されます。 PowerShell を使用してサブスクリプションに最初の Redis Cache を作成する場合は、先に次のコマンドを使用して名前空間を登録する必要があります。これを実行しないと、`New-AzureRmRedisCache` や `Get-AzureRmRedisCache` などのコマンドレットが失敗します。
> 
> `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

`New-AzureRmRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

    PS C:\> Get-Help New-AzureRmRedisCache -detailed

    NAME
        New-AzureRmRedisCache

    SYNOPSIS
        Creates a new redis cache.


    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.


    PARAMETERS
        -Name <String>
            Name of the redis cache to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.

        -Location <String>
            Location in which to create the redis cache.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

既定のパラメーターを使用してキャッシュを作成するには、次のコマンドを実行します。

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`、`Name`、`Location` は必須のパラメーターですが、他のパラメーターは省略可能で、それぞれに既定値があります。 前述のコマンドを実行すると、Standard SKU の Azure Redis Cache インスタンスが作成され、指定した名前、場所、リソース グループが設定されます。サイズは 1 GB に設定され、非 SSL ポートは無効になっています。

Premium キャッシュを作成する場合は、P1 (6 GB ～ 60 GB)、P2 (13 GB ～ 130 GB)、P3 (26 GB ～ 260 GB)、P4 (53 GB ～ 530 GB) のいずれかのサイズを指定します。 クラスタリングを有効にする場合は、`ShardCount` パラメーターを使用してシャード数を指定します。 次の例では、3 つのシャードを指定して P1 の Premium キャッシュを作成しています。 P1 Premium キャッシュはサイズが 6 GB です。3 つのシャードを指定したため、合計サイズは 18 GB (3 x 6 GB) です。

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

`RedisConfiguration` の値を指定する場合は、`@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}` のように、キー/値のペアとして値を `{}` で囲みます。 次の例では、`allkeys-random` に設定された maxmemory ポリシーと `KEA` に設定されたキースペース通知を使用して、Standard の 1 GB のキャッシュを作成しています。 詳細については、「[キースペース通知 (詳細設定)](cache-configure.md#keyspace-notifications-advanced-settings)」および「[maxmemory-policy と maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)」をご覧ください。

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>キャッシュの作成時にデータベースの設定を構成するには
`databases` の設定は、キャッシュの作成中にのみ構成することができます。 次の例では、 [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) コマンドレットを使用して、48 のデータベースを持つ Premium P3 (26 GB) のキャッシュを作成します。

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

`databases` プロパティの詳細については、「 [既定の Azure Redis Cache サーバー構成](cache-configure.md#default-redis-server-configuration)」をご覧ください。 [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) コマンドレットを使用して、キャッシュを作成する方法の詳細については、上記の「[Redis Cache の作成方法](#to-create-a-redis-cache)」セクションをご覧ください。

## <a name="to-update-a-redis-cache"></a>Redis Cache の更新方法
Azure Redis Cache インスタンスを更新するには、 [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) コマンドレットを使用します。

`Set-AzureRmRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed

    NAME
        Set-AzureRmRedisCache

    SYNOPSIS
        Set redis cache updatable parameters.

    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.

    PARAMETERS
        -Name <String>
            Name of the redis cache to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

`Set-AzureRmRedisCache` コマンドレットを使用して、`Size`、`Sku`、`EnableNonSslPort`、`RedisConfiguration` の値などのプロパティを更新できます。 

次のコマンドを実行すると、myCache という名前の Redis Cache の maxmemory-policy が更新されます。

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-a-redis-cache"></a>Redis Cache をスケーリングするには
`Size`、`Sku`、または `ShardCount` のプロパティが変更されたときに、`Set-AzureRmRedisCache` を使用して、Azure Redis Cache インスタンスをスケールできます。 

> [!NOTE]
> PowerShell を使用してキャッシュをスケールする場合、Azure ポータルからキャッシュをスケールする場合と同じ制限とガイドラインが適用されます。 別の価格レベルにスケーリングできますが、次のような制約があります。
> 
> * 上位の価格レベルから下位の価格レベルにスケーリングすることはできません。
> * **Premium** キャッシュから **Standard** または **Basic** キャッシュにスケールすることはできません。
> * **Standard** キャッシュから **Basic** キャッシュにスケールすることはできません。
> * **Basic** キャッシュから **Standard** キャッシュにスケールすることはできますが、同時にサイズを変更することはできません。 サイズを変更する必要がある場合、後続のスケーリング操作でサイズを変更できます。
> * **Basic** キャッシュから直接 **Premium** キャッシュにスケールすることはできません。 1 回のスケーリング操作で **Basic** から **Standard** にスケールし、その後の操作で **Standard** から **Premium** にスケールする必要があります。
> * **C0 (250 MB)** サイズにそれより大きなサイズからスケールダウンすることはできません。
> 
> 詳細については、「 [Azure Redis Cache のスケーリング方法](cache-how-to-scale.md)」をご覧ください。
> 
> 

次の例は、 `myCache` という名前のキャッシュを 2.5 GB のキャッシュにスケーリングする方法を示しています。 このコマンドは、Basic と Standard の両方のキャッシュで使用できます。

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

このコマンドが発行されると、キャッシュの状態が返されます ( `Get-AzureRmRedisCache`の呼び出しと同様です)。 `ProvisioningState` が `Scaling` である点に注目してください。

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

スケーリング処理が完了すると、`ProvisioningState` は `Succeeded` に変わります。 Basic から Standard に変更した後にサイズを変更するなど、前の操作に続けてスケーリング操作の実行が必要になる場合は、前の操作が完了するまで待つ必要があります。そうでないと、次のようなエラーが表示されます。

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Redis Cache に関する情報を取得する方法
[Get AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) コマンドレットを使用してキャッシュに関する情報を取得できます。

`Get-AzureRmRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed

    NAME
        Get-AzureRmRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

現在のサブスクリプションのすべてのキャッシュに関する情報を取得するには、パラメーターを指定せずに `Get-AzureRmRedisCache` を実行します。

    Get-AzureRmRedisCache

特定のリソース グループのすべてのキャッシュに関する情報を取得するには、`ResourceGroupName` パラメーターを指定して `Get-AzureRmRedisCache` を実行します。

    Get-AzureRmRedisCache -ResourceGroupName myGroup

特定のキャッシュに関する情報を取得するには、キャッシュの名前を `Name` パラメーターに設定し、そのキャッシュが含まれているリソース グループを `ResourceGroupName` パラメーターに設定して、`Get-AzureRmRedisCache` を実行します。

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Redis Cache のアクセス キーを取得する方法
キャッシュのアクセス キーを取得するには、 [Get AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) コマンドレットを使用します。

`Get-AzureRmRedisCacheKey`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed

    NAME
        Get-AzureRmRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified redis cache.


    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

キャッシュのキーを取得するには、 `Get-AzureRmRedisCacheKey` コマンドレットを呼び出し、キャッシュの名前と、そのキャッシュが含まれているリソース グループの名前を渡します。

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Redis Cache のアクセス キーを再生成する方法
キャッシュのアクセス キーを再生成するには、 [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx) コマンドレットを使用します。

`New-AzureRmRedisCacheKey`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed

    NAME
        New-AzureRmRedisCacheKey

    SYNOPSIS
        Regenerates the access key of a redis cache.

    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

キャッシュのプライマリ キーまたはセカンダリ キーを再生成するには、`New-AzureRmRedisCacheKey` コマンドレットを呼び出し、名前とリソース グループを渡して、`KeyType` パラメーターに `Primary` または `Secondary` を指定します。 次の例では、キャッシュのセカンダリ アクセス キーが再生成されます。

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Redis Cache を削除する方法
Redis Cache を削除するには、 [Remove-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx) コマンドレットを使用します。

`Remove-AzureRmRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed

    NAME
        Remove-AzureRmRedisCache

    SYNOPSIS
        Remove redis cache if exists.

    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.

    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

次の例では、 `myCache` という名前のキャッシュが削除されます。

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Redis Cache にインポートする方法
Azure Redis Cache インスタンスにデータをインポートするには、 `Import-AzureRmRedisCache` コマンドレットを使用します。

> [!IMPORTANT]
> インポート/エクスポートは、 [Premium レベル](cache-premium-tier-intro.md) のキャッシュでのみ使用できます。 インポート/エクスポートの詳細については、「 [Azure Redis Cache でデータをインポートまたはエクスポートする](cache-how-to-import-export-data.md)」をご覧ください。
> 
> 

`Import-AzureRmRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed

    NAME
        Import-AzureRmRedisCache

    SYNOPSIS
        Import data from blobs to Azure Redis Cache.


    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


次のコマンドは、SAS URI で指定された BLOB から Azure Redis Cache にデータをインポートします。

    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Redis Cache からエクスポートする方法
Azure Redis Cache インスタンスからデータをエクスポートするには、 `Export-AzureRmRedisCache` コマンドレットを使用します。

> [!IMPORTANT]
> インポート/エクスポートは、 [Premium レベル](cache-premium-tier-intro.md) のキャッシュでのみ使用できます。 インポート/エクスポートの詳細については、「 [Azure Redis Cache でデータをインポートまたはエクスポートする](cache-how-to-import-export-data.md)」をご覧ください。
> 
> 

`Export-AzureRmRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed

    NAME
        Export-AzureRmRedisCache

    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.


    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


次のコマンドは、Azure Redis Cache インスタンスから、SAS URI で指定されたコンテナーにデータをエクスポートします。

        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Redis Cache を再起動する方法
Azure Redis Cache インスタンスを再起動するには、 `Reset-AzureRmRedisCache` コマンドレットを使用します。

> [!IMPORTANT]
> 再起動は、 [Premium レベル](cache-premium-tier-intro.md) のキャッシュでのみ使用できます。 キャッシュの再起動の詳細については、 [キャッシュ管理 - 再起動](cache-administration.md#reboot)に関するセクションをご覧ください。
> 
> 

`Reset-AzureRmRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed

    NAME
        Reset-AzureRmRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.


    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


次のコマンドは、指定されたキャッシュの両方のノードを再起動します。

        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>次のステップ
Azure での Windows PowerShell の使用の詳細については、次のリソースをご覧ください。

* [MSDN 上の Azure Redis Cache コマンドレットのドキュメント](https://msdn.microsoft.com/library/azure/mt634513.aspx)
* [Azure リソース マネージャー コマンドレットに関するページ](http://go.microsoft.com/fwlink/?LinkID=394765): AzureResourceManager モジュールのコマンドレットを使用する方法について説明します。
* [リソース グループを使用した Azure リソースの管理](../azure-resource-manager/resource-group-template-deploy-portal.md): Azure ポータルでリソース グループを作成して管理する方法について説明します。
* [Azure blog (Azure のブログ)](http://blogs.msdn.com/windowsazure): Azure の新機能について説明します。
* [Windows PowerShell blog (Windows PowerShell ブログ)](http://blogs.msdn.com/powershell): Windows PowerShell の新機能について説明します。
* ["Hey, Scripting Guy!"ブログ](http://blogs.technet.com/b/heyscriptingguy/): 実践で使えるヒントとテクニックを Windows PowerShell コミュニティから得られます。




<!--HONumber=Nov16_HO5-->


