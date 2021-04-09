---
title: VNet エンドポイントの管理 - Azure CLI - Azure Database for MariaDB
description: この記事では、Azure CLI コマンド ラインを使用して Azure Database for MariaDB VNet サービス エンドポイントおよびルールを作成し、管理する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43d1b7700395bd06960737eae4f318d61aa03717
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665091"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Azure CLI を使用した Azure Database for MariaDB VNet サービス エンドポイントの作成と管理

Virtual Network (VNet) サービス エンドポイントおよびルールは、仮想ネットワークのプライベート アドレス空間を Azure Database for MariaDB サーバーに拡張します。 便利な Azure コマンド ライン インターフェイス (CLI) コマンドを使用して、サーバーを管理するための VNet サービス エンドポイントおよびルールの作成、更新、削除、一覧化、表示を行うことができます。 制限事項を含む、Azure Database for MariaDB VNet サービス エンドポイントの概要については、[Azure Database for MariaDB サーバー VNet サービス エンドポイント](concepts-data-access-security-vnet.md)に関する記事を参照してください。 VNet サービス エンドポイントは、Azure Database for MariaDB でサポートされるすべてのリージョンで利用できます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- [Azure Database for MariaDB サーバーとデータベース](quickstart-create-mariadb-server-database-using-azure-cli.md)が必要です。

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

> [!NOTE]
> VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。

## <a name="configure-vnet-service-endpoints"></a>VNet サービス エンドポイントの構成

Virtual Network を構成するには、[az network vnet](/cli/azure/network/vnet) コマンドを使用します。

複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account#az-account-set) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。 そのサブスクリプションの **az login** 出力の **id** プロパティで、subscription id プレースホルダーを置き換えます。

- アカウントには、仮想ネットワークとサービス エンドポイントを作成するためのアクセス許可が必要です。

サービス エンドポイントは、仮想ネットワークへの書き込みアクセス権を持つユーザーが仮想ネットワーク上で個別に構成できます。

Azure サービス リソースへのアクセスを VNet に限定するには、ユーザーが、追加されるサブネットの "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" へのアクセス許可を持っている必要があります。 このアクセス許可は、既定では組み込みのサービス管理者のロールに含まれ、カスタム ロールを作成することで変更できます。

[組み込みロール](../role-based-access-control/built-in-roles.md)と、特定のアクセス許可を[カスタム ロール](../role-based-access-control/custom-roles.md)に割り当てる方法の詳細をご覧ください。

Vnet と Azure サービス リソースのサブスクリプションは、同じでも異なっていてもかまいません。 VNet と Azure サービス リソースのサブスクリプションが異なる場合、リソースは同じ Active Directory (AD) テナントの下に置かれている必要があります。 両方のサブスクリプションに、**Microsoft.Sql** リソース プロバイダーが登録されていることを確認してください。 詳細については、[resource-manager-registration][resource-manager-portal] に関するページをご覧ください

> [!IMPORTANT]
> サービス エンドポイントを構成する前に、サービス エンドポイントの構成と考慮事項について、この記事を読むことを強くお勧めします。 **仮想ネットワーク サービス エンドポイント:** [仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)は、プロパティ値に 1 つ以上の正式な Azure サービスの種類名が含まれるサブネットです。 VNet サービス エンドポイントでは、SQL Database という名前の Azure サービスを参照する **Microsoft.Sql** というサービス種類名を使用します。 このサービス タグは、Azure SQL Database、Azure Database for MariaDB、PostgreSQL および MySQL サービスにも適用されます。 VNet サービス エンドポイントに **Microsoft.Sql** サービス タグを適用すると、サブネット上の Azure SQL Database、Azure Database for PostgreSQL、Azure Database for MariaDB、Azure Database for MySQL サーバーを含むすべての Azure Database サービスにサービス エンドポイント トラフィックが構成されることに注意することが重要です。

### <a name="sample-script"></a>サンプル スクリプト

このサンプル スクリプトは、Azure Database for MariaDB サーバー、VNet、VNet サービス エンドポイントを作成し、VNet ルールを使用してサブネットのサーバーをセキュリティで保護するために使用されます。 このサンプル スクリプトで、管理者ユーザー名とパスワードを変更します。 `az account set --subscription` コマンドで使用するサブスクリプション ID を、自分のサブスクリプション ID に置き換えてください。

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md