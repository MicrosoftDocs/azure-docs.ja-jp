---
title: Azure クラシック CLI を使用して Azure Cache for Redis を管理する
description: 任意のプラットフォームに Azure クラシック CLI をインストールする方法、Azure クラシック CLI を使って Azure アカウントに接続する方法、クラシック CLI で Azure Cache for Redis を作成および管理する方法。
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7643f882d5ac330046c169e0a3f2fa4920331d4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92537696"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Azure クラシック CLI を使用して Azure Cache for Redis を作成および管理する方法
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure クラシック CLI](cache-manage-cli.md)
>

Azure クラシック CLI は、任意のプラットフォームから Azure インフラストラクチャを管理する優れた方法です。 この記事では、Azure クラシック CLI を使用して Azure Cache for Redis インスタンスを作成および管理する方法について説明します。

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> 最新の Azure CLI サンプル スクリプトについては、[Azure Cache for Redis 向けの Azure CLI のサンプル](cli-samples.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
Azure クラシック CLI を使用して Azure Cache for Redis インスタンスを作成および管理するには、次の手順を行う必要があります。

* Azure アカウントが必要です。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) をほんの少しの時間で作成することができます。
* [Azure クラシック CLI をインストールします](/cli/azure/install-classic-cli)。
* Azure CLI のインストールを個人の Azure アカウントか、職場または学校 Azure アカウントに関連付けることで、`azure login` コマンドを使用してクラシック CLI からログインすることができます。
* 以下のコマンドを実行する前に、`azure config mode arm` コマンドを実行して、クラシック CLI を Resource Manager モードに切り替えます。 詳細については、[Azure クラシック CLI を使用して Azure のリソースとリソース グループを管理する方法](../azure-resource-manager/management/manage-resources-cli.md)に関するページを参照してください。

## <a name="azure-cache-for-redis-properties"></a>Azure Cache for Redis のプロパティ
Azure Cache for Redis インスタンスを作成および更新する場合には、次のプロパティを使用します。

| プロパティ | Switch | 説明 |
| --- | --- | --- |
| name |-n, --name |Azure Cache for Redis の名前です。 |
| resource group |-g, --resource-group |リソース グループの名前です。 |
| location |-l, --location |キャッシュを作成する場所です。 |
| size |-z, --size |Azure Cache for Redis のサイズです。 有効な値: [C0、C1、C2、C3、C4、C5、C6、P1、P2、P3、P4] |
| sku |-x, --sku |Redis SKU です。 値は次のいずれかです: [Basic、Standard、Premium] |
| EnableNonSslPort |-e、--enable-non-ssl-port |Azure Cache for Redis の EnableNonSslPort プロパティです。 キャッシュの非 TLS/SSL ポートを有効にする場合は、このフラグを追加します。 |
| Redis 構成 |-c、--redis-configuration |Redis 構成。 構成のキーと値の JSON 形式の文字列をここに入力します。 形式: "{"":"","":""}" |
| Redis 構成 |-f、--redis-configuration-file |Redis 構成。 構成キーおよび値を含むファイルのパスをここに入力します。 ファイル エントリの形式: {"":"","":""} |
| シャード数 |-r、--shard-count |クラスタリングにより Premium クラスター キャッシュに作成するシャードの数。 |
| Virtual Network |-v、--virtual-network |VNET でキャッシュをホストする場合に、Azure Cache for Redis をデプロイする仮想ネットワークの正確な ARM リソース ID を指定します。 形式の例: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| key type |-t、--key-type |更新するキーの種類です。 有効な値: [Primary、Secondary] |
| StaticIP |-p、--static-ip \<static-ip\> |VNET でキャッシュをホストする場合に、キャッシュのサブネットで一意の IP アドレスを指定します。 指定していない場合、サブネットから自動的にアドレスが 1 つ選択されます。 |
| Subnet |t、--subnet \<subnet\> |VNET でキャッシュをホストする場合に、キャッシュをデプロイするサブネットの名前を指定します。 |
| VirtualNetwork |-v、--virtual-network \<virtual-network\> |VNET でキャッシュをホストする場合に、Azure Cache for Redis をデプロイする仮想ネットワークの正確な ARM リソース ID を指定します。 形式の例: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| サブスクリプション |-s、--subscription |サブスクリプションの識別子です。 |

## <a name="see-all-azure-cache-for-redis-commands"></a>すべての Azure Cache for Redis コマンドを表示する
すべての Azure Cache for Redis コマンドとそのパラメーターを表示するには、`azure rediscache -h` コマンドを使用します。

```azurecli
C:\>azure rediscache -h
help:    Commands to manage your Azure Cache for Redis(s)
help:
help:    Create an Azure Cache for Redis
help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Delete an existing Azure Cache for Redis
help:      rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:      rediscache list [options]
help:
help:    Show properties of an existing Azure Cache for Redis
help:      rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Change settings of an existing Azure Cache for Redis
help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Renew the authentication key for an existing Azure Cache for Redis
help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help  output usage information
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="create-an-azure-cache-for-redis"></a>Azure Cache for Redis を作成する
Azure Cache for Redis を作成するには、次のコマンドを使用します。

```azurecli
    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
```

このコマンドの詳細を確認するには、`azure rediscache create -h` コマンドを実行します。

```azurecli
C:\>azure rediscache create -h
help:    Create an Azure Cache for Redis
help:
help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -l, --location <location>                                Location to create cache.
help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the non-TLS/SSL Port for your cache
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -s, --subscription <id>                                  the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="delete-an-existing-azure-cache-for-redis"></a>既存の Azure Cache for Redis を削除する
Azure Cache for Redis を削除するには、次のコマンドを使用します。

```azurecli
    azure rediscache delete [--name <name> --resource-group <resource-group> ]
```

このコマンドの詳細を確認するには、`azure rediscache delete -h` コマンドを実行します。

```azurecli
C:\>azure rediscache delete -h
help:    Delete an existing Azure Cache for Redis
help:
help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>サブスクリプションまたはリソース グループ内のすべての Azure Cache for Redis を一覧表示する
サブスクリプションまたはリソース グループ内のすべての Azure Cache for Redis を一覧表示するには、次のコマンドを使用します。

```azurecli
    azure rediscache list [options]
```

このコマンドの詳細を確認するには、`azure rediscache list -h` コマンドを実行します。

```azurecli
C:\>azure rediscache list -h
help:    List all Azure Cache for Redis within your Subscription or Resource Group
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
```

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>既存の Azure Cache for Redis のプロパティを表示する
既存の Azure Cache for Redis のプロパティを表示するには、次のコマンドを使用します。

```azurecli
    azure rediscache show [--name <name> --resource-group <resource-group>]
```

このコマンドの詳細を確認するには、`azure rediscache show -h` コマンドを実行します。

```azurecli
C:\>azure rediscache show -h
help:    Show properties of an existing Azure Cache for Redis
help:
help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>既存の Azure Cache for Redis の設定を変更する
既存の Azure Cache for Redis の設定を変更するには、次のコマンドを使用します。

```azurecli
    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
```

このコマンドの詳細を確認するには、`azure rediscache set -h` コマンドを実行します。

```azurecli
C:\>azure rediscache set -h
help:    Change settings of an existing Azure Cache for Redis
help:
help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
help:      -s, --subscription <subscription>                        the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>既存の Azure Cache for Redis の認証キーを更新する
既存の Azure Cache for Redis の認証キーを更新するには、次のコマンドを使用します。

```azurecli
    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]
```

`key-type` に対して、`Primary` または `Secondary` を指定します。

このコマンドの詳細を確認するには、`azure rediscache renew-key -h` コマンドを実行します。

```azurecli
C:\>azure rediscache renew-key -h
help:    Renew the authentication key for an existing Azure Cache for Redis
help:
help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>既存の Azure Cache for Redis のプライマリ キーとセカンダリ キーを一覧表示する
既存の Azure Cache for Redis のプライマリ キーとセカンダリ キーを一覧表示するには、次のコマンドを使用します。

```azurecli
    azure rediscache list-keys [--name <name> --resource-group <resource-group>]
```

このコマンドの詳細を確認するには、`azure rediscache list-keys -h` コマンドを実行します。

```azurecli
C:\>azure rediscache list-keys -h
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:
help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```