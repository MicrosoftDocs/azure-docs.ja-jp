---
title: "Azure CLI を使用した Azure Redis Cache の管理 | Microsoft Docs"
description: "任意のプラットフォームに Azure CLI をインストールする方法、Azure CLI を使って Azure アカウントに接続する方法、Azure CLI で Redis Cache を作成および管理する方法。"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: ae8e531c32b49e121b1c43b15071c138aaacf498
ms.lasthandoff: 03/21/2017


---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Azure コマンド ライン インターフェイス (Azure CLI) を使用して Azure Redis Cache を作成および管理する方法
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
>
>

Azure CLI は、任意のプラットフォームから Azure インフラストラクチャを管理する優れた方法です。 この記事では、Azure CLI を使用して Azure Redis Cache インスタンスを作成および管理する方法について説明します。

## <a name="prerequisites"></a>前提条件
Azure CLI を使用して Azure Redis Cache インスタンスを作成および管理するには、次の手順を実行する必要があります。

* Azure アカウントが必要です。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) をほんの少しの時間で作成することができます。
* [Azure CLI のインストール](../cli-install-nodejs.md)を実行します。
* Azure CLI のインストールを個人の Azure アカウント、仕事用 Azure アカウント、学校用 Azure アカウントに関連付けることで、 `azure login` コマンドを使用して Azure CLI からログインすることができます。 相違点を理解して選択するには、「 [Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続](../xplat-cli-connect.md)」を参照してください。
* 以下のコマンドを実行する前に、 `azure config mode arm` コマンドを実行して、Azure CLI をリソース マネージャー モードに切り替えます。 詳細については、「[Azure CLI を使用して Azure のリソースとリソース グループを管理する](../xplat-cli-azure-resource-manager.md)」を参照してください。

## <a name="redis-cache-properties"></a>Redis Cache のプロパティ
Redis Cache インスタンスを作成および更新する場合には、次のプロパティを使用します。

| プロパティ | Switch | Description |
| --- | --- | --- |
| name |-n, --name |Redis Cache の名前です。 |
| resource group |-g, --resource-group |リソース グループの名前です。 |
| location |-l, --location |キャッシュを作成する場所です。 |
| size |-z, --size |Redis Cache のサイズです。 有効な値: [C0、C1、C2、C3、C4、C5、C6、P1、P2、P3、P4] |
| sku |-x, --sku |Redis SKU です。 値は次のいずれかです: [Basic、Standard、Premium] |
| EnableNonSslPort |-e、--enable-non-ssl-port |Redis Cache の EnableNonSslPort プロパティです。 キャッシュの非 SSL ポートを有効にする場合は、このフラグを追加します。 |
| Redis 構成 |-c、--redis-configuration |Redis 構成。 構成のキーと値の JSON 形式の文字列をここに入力します。 形式: "{"":"","":""}" |
| Redis 構成 |-f、--redis-configuration-file |Redis 構成。 構成キーおよび値を含むファイルのパスをここに入力します。 ファイル エントリの形式: {"":"","":""} |
| シャード数 |-r、--shard-count |クラスタリングにより Premium クラスター キャッシュに作成するシャードの数。 |
| 仮想ネットワーク |-v、--virtual-network |VNET でキャッシュをホストする場合に、Redis Cache をデプロイする仮想ネットワークの正確な ARM リソース ID を指定します。 形式の例: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| key type |-t、--key-type |更新するキーの種類です。 有効な値: [Primary、Secondary] |
| StaticIP |-p, --static-ip <static-ip> |VNET でキャッシュをホストする場合に、キャッシュのサブネットで一意の IP アドレスを指定します。 指定していない場合、サブネットから自動的にアドレスが&1; つ選択されます。 |
| サブネット |t、--subnet <subnet> |VNET でキャッシュをホストする場合に、キャッシュをデプロイするサブネットの名前を指定します。 |
| VirtualNetwork |-v, --virtual-network <virtual-network> |VNET でキャッシュをホストする場合に、Redis Cache をデプロイする仮想ネットワークの正確な ARM リソース ID を指定します。 形式の例: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| サブスクリプション |-s、--subscription |サブスクリプションの識別子です。 |

## <a name="see-all-redis-cache-commands"></a>すべての Redis Cache コマンドを参照してください。
すべての Redis Cache コマンドとそのパラメーターを表示するには、 `azure rediscache -h` コマンドを使用します。

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Redis Cache の作成
Redis Cache を作成するには、次のコマンドを使用します。

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

このコマンドの詳細を確認するには、 `azure rediscache create -h` コマンドを実行します。

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>既存の Redis Cache を削除する
Redis Cache を削除するには、次のコマンドを使用します。

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

このコマンドの詳細を確認するには、 `azure rediscache delete -h` コマンドを実行します。

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>サブスクリプションまたはリソース グループ内のすべての Redis Cache を一覧表示する
サブスクリプションまたはリソース グループ内のすべての Redis Cache を一覧表示するには、次のコマンドを使用します。

    azure rediscache list [options]

このコマンドの詳細を確認するには、 `azure rediscache list -h` コマンドを実行します。

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>既存の Redis Cache のプロパティを表示する
既存の Redis Cache のプロパティを表示するには、次のコマンドを使用します。

    azure rediscache show [--name <name> --resource-group <resource-group>]

このコマンドの詳細を確認するには、 `azure rediscache show -h` コマンドを実行します。

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-redis-cache"></a>既存の Redis Cache の設定を変更する
既存の Redis Cache の設定を変更するには、次のコマンドを使用します。

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

このコマンドの詳細を確認するには、 `azure rediscache set -h` コマンドを実行します。

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>既存の Redis Cache の認証キーを更新する
既存の Redis Cache の認証キーを更新するには、次のコマンドを使用します。

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

`key-type` に対して、`Primary` または `Secondary` を指定します。

このコマンドの詳細を確認するには、`azure rediscache renew-key -h` コマンドを実行します。

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>既存の Redis Cache のプライマリ キーとセカンダリ キーを一覧表示する
既存の Redis Cache のプライマリ キーとセカンダリ キーを一覧表示するには、次のコマンドを使用します。

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

このコマンドの詳細を確認するには、 `azure rediscache list-keys -h` コマンドを実行します。

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

