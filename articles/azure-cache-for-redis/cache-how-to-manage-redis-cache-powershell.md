---
title: Azure PowerShell を使用した Azure Cache for Redis の管理
description: Azure PowerShell を使用して Azure Cache for Redis の管理タスクを実行する方法について説明します。
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 88e433dbfa87d8ea483789b1fd838c62a6a481c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536761"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Azure PowerShell を使用した Azure Cache for Redis の管理
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このトピックでは、Azure Cache for Redis インスタンスの作成、更新、スケールなどの一般的なタスクを実行する方法、アクセス キーを再生成する方法、キャッシュに関する情報を表示する方法について説明します。 Azure Cache for Redis 用の PowerShell コマンドレットの詳細な一覧については、[Azure Cache for Redis コマンドレット](/powershell/module/az.rediscache)に関するページをご覧ください。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

クラシック デプロイ モデルについて詳しくは、「[Azure Resource Manager とクラシック デプロイ: デプロイ モデルとリソースの状態について](../azure-resource-manager/management/deployment-models.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure PowerShell をインストール済みである場合、Azure PowerShell Version 1.0.0 以降であることが必要です。 インストールした Azure PowerShell のバージョンは、Azure PowerShell コマンド プロンプトで次のコマンドを使用して確認できます。

```azurepowershell
    Get-Module Az | format-table version
```

まず、次のコマンドで Azure にログインする必要があります。

```azurepowershell
    Connect-AzAccount
```

Microsoft Azure のサインイン ダイアログで、Azure アカウントの電子メール アドレスとそのパスワードを指定します。

次に、Azure サブスクリプションが複数ある場合は、使用する Azure サブスクリプションを設定する必要があります。 現在のサブスクリプションを一覧表示するには、次のコマンドを実行します。

```azurepowershell
    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

サブスクリプションを指定するには、次のコマンドを実行します。 次の例では、サブスクリプション名は `ContosoSubscription`です。

```azurepowershell
    Select-AzSubscription -SubscriptionName ContosoSubscription
```

Azure リソース マネージャーで Windows PowerShell を使用するには、以下が必要です。

* Windows PowerShell バージョン 3.0 または 4.0。 Windows PowerShell のバージョンを調べるには、`$PSVersionTable` と入力して、`PSVersion` の値が 3.0 か 4.0 かを確認します。 互換バージョンをインストールするには、「[Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595)」を参照してください。

このチュートリアルに表示されているコマンドレットの詳しいヘルプを確認には、Get-Help コマンドレットを使用します。

```azurepowershell
    Get-Help <cmdlet-name> -Detailed
```

たとえば、 `New-AzRedisCache` コマンドレットについてのヘルプを確認するには、次のように入力します。

```azurepowershell
    Get-Help New-AzRedisCache -Detailed
```

### <a name="how-to-connect-to-other-clouds"></a>他のクラウドに接続する方法
既定では、Azure 環境はグローバル Azure クラウド インスタンスを表す `AzureCloud`です。 別のインスタンスに接続するには、`Connect-AzAccount` コマンドと `-Environment` または -`EnvironmentName` コマンド ライン スイッチを使用し、任意の環境または環境名を指定します。

利用可能な環境の一覧を表示するには、 `Get-AzEnvironment` コマンドレットを実行します。

### <a name="to-connect-to-the-azure-government-cloud"></a>Azure Government Cloud に接続するには
Azure Government Cloud に接続するには、次のいずれかのコマンドを使用します。

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
```

or

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)
```

Azure Government Cloud でキャッシュを作成するには、次のいずれかの場所を使用します。

* USGov バージニア州
* USGov アイオワ州

Azure Government Cloud の詳細については、「[Microsoft Azure Government](https://azure.microsoft.com/features/gov/)」と「[Microsoft Azure Government 開発者向けガイド](../azure-government/documentation-government-developer-guide.md)」をご覧ください。

### <a name="to-connect-to-the-azure-china-cloud"></a>Azure China Cloud に接続するには
Azure China Cloud に接続するには、次のいずれかのコマンドを使用します。

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureChinaCloud
```

or

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)
```

Azure China Cloud でキャッシュを作成するには、次のいずれかの場所を使用します。

* 中国東部
* 中国北部

Azure China Cloud の詳細については、 [中国の 21Vianet が運営している AzureChinaCloud for Azure](https://www.windowsazure.cn/)に関するページをご覧ください。

### <a name="to-connect-to-microsoft-azure-germany"></a>Microsoft Azure Germany に接続するには
Microsoft Azure Germany に接続するには、次のいずれかのコマンドを使用します。

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureGermanCloud
```

or

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)
```

Microsoft Azure Germany でキャッシュを作成するには、次のいずれかの場所を使用します。

* ドイツ中部
* ドイツ北東部

Microsoft Azure Germany の詳細については、「[Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)」を参照してください。

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Azure Cache for Redis 用の PowerShell で使用されるプロパティ
次の表は、Azure PowerShell を使用して Azure Cache for Redis インスタンスを作成し、管理するときに一般的に使用されるパラメーターのプロパティと説明を示しています。

| パラメーター | 説明 | Default |
| --- | --- | --- |
| Name |キャッシュの名前 | |
| Location |キャッシュの場所 | |
| ResourceGroupName |キャッシュを作成するリソース グループの名前 | |
| Size |キャッシュのサイズ。 有効な値: P1、P2、P3、P4、C0、C1、C2、C3、C4、C5、C6、250 MB、1 GB、2.5 GB、6 GB、13 GB、26 GB、53 GB |1GB |
| ShardCount |クラスタリングが有効になっている Premium キャッシュを作成するときに作成するシャードの数。 有効な値: 1、2、3、4、5、6、7、8、9、10 | |
| SKU |キャッシュの SKU を指定します。 有効な値: Basic、Standard、Premium |Standard |
| RedisConfiguration |Redis 構成の設定を指定します。 各設定の詳細については、次の「 [RedisConfiguration プロパティ](#redisconfiguration-properties) 」の表をご覧ください。 | |
| EnableNonSslPort |非 SSL ポートが有効になっているかどうかを示します。 |False |
| MaxMemoryPolicy |このパラメーターは非推奨となりました。代わりに、RedisConfiguration を使用します。 | |
| StaticIP |VNET でキャッシュをホストする場合に、キャッシュのサブネットで一意の IP アドレスを指定します。 指定していない場合、サブネットから自動的にアドレスが 1 つ選択されます。 | |
| Subnet |VNET でキャッシュをホストする場合に、キャッシュをデプロイするサブネットの名前を指定します。 | |
| VirtualNetwork |VNET でキャッシュをホストする場合に、キャッシュをデプロイする VNET のリソース ID を指定します。 | |
| KeyType |アクセス キーを更新するときに再生成するアクセス キーを指定します。 有効な値: Primary、Secondary | |

### <a name="redisconfiguration-properties"></a>RedisConfiguration プロパティ
| プロパティ | 説明 | 価格レベル |
| --- | --- | --- |
| rdb-backup-enabled |[Redis データ永続化](cache-how-to-premium-persistence.md) が有効かどうか。 |Premium のみ |
| rdb-storage-connection-string |[Redis データ永続化](cache-how-to-premium-persistence.md) |Premium のみ |
| rdb-backup-frequency |[Redis データ永続化](cache-how-to-premium-persistence.md) |Premium のみ |
| maxmemory-reserved |キャッシュ以外のプロセスに [予約済みのメモリ](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) を構成する |Standard と Premium |
| maxmemory-policy |キャッシュに [削除ポリシー](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) を構成する |すべての価格レベル |
| notify-keyspace-events |[キースペース通知](cache-configure.md#keyspace-notifications-advanced-settings) |Standard と Premium |
| hash-max-ziplist-entries |小規模の集計データの種類に [メモリ最適化](https://redis.io/topics/memory-optimization) を構成する |Standard と Premium |
| hash-max-ziplist-value |小規模の集計データの種類に [メモリ最適化](https://redis.io/topics/memory-optimization) を構成する |Standard と Premium |
| set-max-intset-entries |小規模の集計データの種類に [メモリ最適化](https://redis.io/topics/memory-optimization) を構成する |Standard と Premium |
| zset-max-ziplist-entries |小規模の集計データの種類に [メモリ最適化](https://redis.io/topics/memory-optimization) を構成する |Standard と Premium |
| zset-max-ziplist-value |小規模の集計データの種類に [メモリ最適化](https://redis.io/topics/memory-optimization) を構成する |Standard と Premium |
| databases |データベースの数を構成する。 このプロパティは、キャッシュの作成時にのみ構成できます。 |Standard と Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Azure Cache for Redis を作成するには
Azure Cache for Redis インスタンスを新規作成するには、[New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) コマンドレットを使用します。

> [!IMPORTANT]
> Azure portal を使用してサブスクリプションに初めて Azure Cache for Redis を作成すると、そのサブスクリプションの `Microsoft.Cache` 名前空間がポータルにより登録されます。 PowerShell を使用してサブスクリプションに最初の Azure Cache for Redis を作成する場合は、先に次のコマンドを使用して名前空間を登録する必要があります。これを実行しないと、`New-AzRedisCache` や `Get-AzRedisCache` などのコマンドレットが失敗します。
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

`New-AzRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

```azurepowershell
    PS C:\> Get-Help New-AzRedisCache -detailed

    NAME
        New-AzRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

既定のパラメーターを使用してキャッシュを作成するには、次のコマンドを実行します。

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"
```

`ResourceGroupName`、`Name`、`Location` は必須のパラメーターですが、他のパラメーターは省略可能で、それぞれに既定値があります。 前述のコマンドを実行すると、Standard SKU の Azure Cache for Redis インスタンスが作成され、指定した名前、場所、リソース グループが設定されます。サイズは 1 GB に設定され、非 SSL ポートは無効になっています。

Premium キャッシュを作成する場合は、P1 (6 GB ～ 60 GB)、P2 (13 GB ～ 130 GB)、P3 (26 GB ～ 260 GB)、P4 (53 GB ～ 530 GB) のいずれかのサイズを指定します。 クラスタリングを有効にする場合は、`ShardCount` パラメーターを使用してシャード数を指定します。 次の例では、3 つのシャードを指定して P1 の Premium キャッシュを作成しています。 P1 Premium キャッシュはサイズが 6 GB です。3 つのシャードを指定したため、合計サイズは 18 GB (3 x 6 GB) です。

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3
```

`RedisConfiguration` の値を指定する場合は、`@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}` のように、キー/値のペアとして値を `{}` で囲みます。 次の例では、`allkeys-random` に設定された maxmemory ポリシーと `KEA` に設定されたキースペース通知を使用して、Standard の 1 GB のキャッシュを作成しています。 詳細については、「[キースペース通知 (詳細設定)](cache-configure.md#keyspace-notifications-advanced-settings)」および「[メモリ ポリシー](cache-configure.md#memory-policies)」をご覧ください。

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}
```

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>キャッシュの作成時にデータベースの設定を構成するには
`databases` の設定は、キャッシュの作成中にのみ構成することができます。 次の例では、[New-AzRedisCache](/powershell/module/az.rediscache/New-azRedisCache) コマンドレットを使用して、48 のデータベースを持つ Premium P3 (26 GB) のキャッシュを作成します。

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}
```

`databases` プロパティの詳細については、「[Default Azure Cache for Redis server configuration](cache-configure.md#default-redis-server-configuration)」 (既定の Azure Cache for Redis サーバー構成) をご覧ください。 [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) コマンドレットを使用してキャッシュを作成する方法の詳細については、上記の「Azure Cache for Redis を作成するには」のセクションをご覧ください。

## <a name="to-update-an-azure-cache-for-redis"></a>Azure Cache for Redis を更新するには
Azure Cache for Redis インスタンスを更新するには、[Set-AzRedisCache](/powershell/module/az.rediscache/Set-azRedisCache) コマンドレットを使用します。

`Set-AzRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

```azurepowershell
    PS C:\> Get-Help Set-AzRedisCache -detailed

    NAME
        Set-AzRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

`Set-AzRedisCache` コマンドレットを使用して、`Size`、`Sku`、`EnableNonSslPort`、`RedisConfiguration` の値などのプロパティを更新できます。 

次のコマンドを実行すると、myCache という名前の Azure Cache for Redis の maxmemory-policy が更新されます。

```azurepowershell
    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}
```

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Azure Cache for Redis をスケーリングするには
`Size`、`Sku`、または `ShardCount` のプロパティが変更されたときに、`Set-AzRedisCache` を使用して、Azure Cache for Redis インスタンスをスケーリングできます。 

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
> 詳細については、「[How to Scale Azure Cache for Redis](cache-how-to-scale.md)」 (Azure Cache for Redis のスケーリング方法) をご覧ください。
> 
> 

次の例は、 `myCache` という名前のキャッシュを 2.5 GB のキャッシュにスケーリングする方法を示しています。 このコマンドは、Basic と Standard の両方のキャッシュで使用できます。

```azurepowershell
    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

このコマンドが発行されると、キャッシュの状態が返されます ( `Get-AzRedisCache`の呼び出しと同様です)。 `ProvisioningState` が `Scaling` である点に注目してください。

```azurepowershell
    PS C:\> Set-AzRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


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
```

スケーリング処理が完了すると、`ProvisioningState` は `Succeeded` に変わります。 Basic から Standard に変更した後にサイズを変更するなど、前の操作に続けてスケーリング操作の実行が必要になる場合は、前の操作が完了するまで待つ必要があります。そうでないと、次のようなエラーが表示されます。

```azurepowershell
    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.
```

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Azure Cache for Redis についての情報を取得するには
[Get-AzRedisCache](/powershell/module/az.rediscache/get-azrediscache) コマンドレットを使用してキャッシュに関する情報を取得できます。

`Get-AzRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCache -detailed

    NAME
        Get-AzRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

現在のサブスクリプションのすべてのキャッシュに関する情報を取得するには、パラメーターを指定せずに `Get-AzRedisCache` を実行します。

```azurepowershell
    Get-AzRedisCache
```

特定のリソース グループのすべてのキャッシュに関する情報を取得するには、`ResourceGroupName` パラメーターを指定して `Get-AzRedisCache` を実行します。

```azurepowershell
    Get-AzRedisCache -ResourceGroupName myGroup
```

特定のキャッシュに関する情報を取得するには、キャッシュの名前を `Name` パラメーターに設定し、そのキャッシュが含まれているリソース グループを `ResourceGroupName` パラメーターに設定して、`Get-AzRedisCache` を実行します。

```azurepowershell
    PS C:\> Get-AzRedisCache -Name myCache -ResourceGroupName myGroup

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
```

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Azure Cache for Redis インスタンスのアクセス キーを取得するには
キャッシュのアクセス キーを取得するには、 [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-azRedisCacheKey) コマンドレットを使用します。

`Get-AzRedisCacheKey`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCacheKey -detailed

    NAME
        Get-AzRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

キャッシュのキーを取得するには、 `Get-AzRedisCacheKey` コマンドレットを呼び出し、キャッシュの名前と、そのキャッシュが含まれているリソース グループの名前を渡します。

```azurepowershell
    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=
```

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Azure Cache for Redis インスタンスのアクセス キーを生成するには
キャッシュのアクセス キーを再生成するには、 [New-AzRedisCacheKey](/powershell/module/az.rediscache/New-azRedisCacheKey) コマンドレットを使用します。

`New-AzRedisCacheKey`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

```azurepowershell
    PS C:\> Get-Help New-AzRedisCacheKey -detailed

    NAME
        New-AzRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

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
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

キャッシュのプライマリ キーまたはセカンダリ キーを再生成するには、`New-AzRedisCacheKey` コマンドレットを呼び出し、名前とリソース グループを渡して、`KeyType` パラメーターに `Primary` または `Secondary` を指定します。 次の例では、キャッシュのセカンダリ アクセス キーが再生成されます。

```azurepowershell
    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=
```

## <a name="to-delete-an-azure-cache-for-redis"></a>Azure Cache for Redis を削除するには
Azure Cache for Redis を削除するには、[Remove-AzRedisCache](/powershell/module/az.rediscache/remove-azrediscache) コマンドレットを使用します。

`Remove-AzRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

```azurepowershell
    PS C:\> Get-Help Remove-AzRedisCache -detailed

    NAME
        Remove-AzRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

次の例では、 `myCache` という名前のキャッシュが削除されます。

```azurepowershell
    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```


## <a name="to-import-an-azure-cache-for-redis"></a>Azure Cache for Redis をインポートするには
Azure Cache for Redis インスタンスにデータをインポートするには、`Import-AzRedisCache` コマンドレットを使用します。

> [!IMPORTANT]
> Import/Export は、[Premium レベル](cache-overview.md#service-tiers)のキャッシュでのみ使用できます。 インポート/エクスポートの詳細については、「[Import and Export data in Azure Cache for Redis](cache-how-to-import-export-data.md)」 (Azure Cache for Redis でデータをインポートおよびエクスポートする) をご覧ください。
> 
> 

`Import-AzRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

```azurepowershell
    PS C:\> Get-Help Import-AzRedisCache -detailed

    NAME
        Import-AzRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


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
            By default Import-AzRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


次のコマンドは、SAS URI で指定された BLOB から Azure Cache for Redis にデータをインポートします。

```azurepowershell
    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force
```

## <a name="to-export-an-azure-cache-for-redis"></a>Azure Cache for Redis をエクスポートするには
Azure Cache for Redis インスタンスからデータをエクスポートするには、`Export-AzRedisCache` コマンドレットを使用します。

> [!IMPORTANT]
> Import/Export は、[Premium レベル](cache-overview.md#service-tiers)のキャッシュでのみ使用できます。 インポート/エクスポートの詳細については、「[Import and Export data in Azure Cache for Redis](cache-how-to-import-export-data.md)」 (Azure Cache for Redis でデータをインポートおよびエクスポートする) をご覧ください。
> 
> 

`Export-AzRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

```azurepowershell
    PS C:\> Get-Help Export-AzRedisCache -detailed

    NAME
        Export-AzRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


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
            By default Export-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


次のコマンドは、Azure Cache for Redis インスタンスから、SAS URI で指定されたコンテナーにデータをエクスポートします。

```azurepowershell
    PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
    -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
    pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"
```

## <a name="to-reboot-an-azure-cache-for-redis"></a>Azure Cache for Redis を再起動するには
Azure Cache for Redis インスタンスを再起動するには、`Reset-AzRedisCache` コマンドレットを使用します。

> [!IMPORTANT]
> 再起動は、[Premium レベル](cache-overview.md#service-tiers)のキャッシュでのみ使用できます。 キャッシュの再起動の詳細については、 [キャッシュ管理 - 再起動](cache-administration.md#reboot)に関するセクションをご覧ください。
> 
> 

`Reset-AzRedisCache`で使用可能なパラメーターとその説明の一覧を表示するには、次のコマンドを実行します。

```azurepowershell
    PS C:\> Get-Help Reset-AzRedisCache -detailed

    NAME
        Reset-AzRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


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
            By default Reset-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


次のコマンドは、指定されたキャッシュの両方のノードを再起動します。

```azurepowershell
    PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
    -Force
```


## <a name="next-steps"></a>次のステップ
Azure での Windows PowerShell の使用の詳細については、次のリソースをご覧ください。

* [MSDN 上の Azure Cache for Redis コマンドレットのドキュメント](/powershell/module/az.rediscache)
* [Azure Resource Manager コマンドレットに関するページ](/powershell/module/): Azure Resource Manager モジュールのコマンドレットを使用する方法について説明します。
* [リソース グループを使用した Azure リソースの管理](../azure-resource-manager/templates/deploy-portal.md): Azure ポータルでリソース グループを作成して管理する方法について説明します。
* [Azure blog (Azure のブログ)](https://azure.microsoft.com/blog/): Azure の新機能について説明します。
* [Windows PowerShell blog (Windows PowerShell ブログ)](https://devblogs.microsoft.com/powershell/): Windows PowerShell の新機能について説明します。
* ["Hey, Scripting Guy!" ブログ](https://devblogs.microsoft.com/scripting/tag/hey-scripting-guy/): 実践で使えるヒントとテクニックを Windows PowerShell コミュニティから得られます。