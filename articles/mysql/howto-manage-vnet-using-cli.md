---
title: Azure CLI を使用した Azure Database for MySQL VNet サービス エンドポイントおよびルールの作成と管理 | Microsoft Docs
description: この記事では、Azure CLI コマンド ラインを使用して Azure Database for MySQL VNet サービス エンドポイントおよびルールを作成し、管理する方法について説明します。
author: bolzmj
ms.author: mbolz
manager: jhubbard
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 199a5ead9856c5f9cba404ce89611efe2d78de8d
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004382"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Azure CLI を使用した Azure Database for MySQL VNet サービス エンドポイントの作成と管理
Virtual Network (VNet) サービス エンドポイントおよびルールは、仮想ネットワークのプライベート アドレス空間を Azure Database for MySQL サーバーに拡張します。 便利な Azure コマンド ライン インターフェイス (CLI) コマンドを使用して、サーバーを管理するための VNet サービス エンドポイントおよびルールの作成、更新、削除、一覧化、表示を行うことができます。 制限事項も含め、Azure Database for MySQL VNet サービス エンドポイントの概要については、[Azure Database for MySQL サーバー VNet サービス エンドポイント ](concepts-data-access-and-security-vnet.md) に関する記事を参照してください。 VNet サービス エンドポイントは、Azure Database for MySQL でサポートされるすべてのリージョンで利用できます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure CLI](/cli/azure/install-azure-cli) をインストールするか、ブラウザーで Azure Cloud Shell を使用します。
- [Azure Database for MySQL サーバーとデータベース](quickstart-create-mysql-server-database-using-azure-cli.md)。

> [!NOTE]
> VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。
> VNet ピアリングでは、トラフィックがサービス エンドポイントを含む共通 VNet Gateway を通過して、ピアにフローするようになっている場合は、ACL/VNet ルールを作成して、Gateway VNet 内の Azure Virtual Machines が Azure Database for MySQL サーバーにアクセスできるようにしてください。

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Azure Database for MySQL の VNet サービス エンドポイントを構成する
Virtual Network を構成するには、[az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) コマンドを使用します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

CLI をローカルで実行している場合は、[az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) コマンドを使用してアカウントにログインする必要があります。 対応するサブスクリプション名のコマンド出力で **id** プロパティを確認します。
```azurecli-interactive
az login
```

複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。 そのサブスクリプションの **az login** 出力の **id** プロパティで、subscription id プレースホルダーを置き換えます。

- アカウントには、仮想ネットワークとサービス エンドポイントを作成するためのアクセス許可が必要です。

サービス エンドポイントは、仮想ネットワークへの書き込みアクセス権を持つユーザーが仮想ネットワーク上で個別に構成できます。

Azure サービス リソースへのアクセスを VNet に限定するには、ユーザーが、追加されるサブネットの "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" へのアクセス許可を持っている必要があります。 このアクセス許可は、既定では組み込みのサービス管理者のロールに含まれ、カスタム ロールを作成することで変更できます。

[組み込みロール](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)と、特定のアクセス許可を[カスタム ロール](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)に割り当てる方法の詳細をご覧ください。

Vnet と Azure サービス リソースのサブスクリプションは、同じでも異なっていてもかまいません。 VNet と Azure サービス リソースのサブスクリプションが異なる場合、リソースは同じ Active Directory (AD) テナントの下に置かれている必要があります。

> [!IMPORTANT]
> 下記のサンプル スクリプトを実行したり、サービス エンドポイントを構成したりする前に、サービス エンドポイントの構成と考慮事項について、この記事を読むことを強くお勧めします。 **Virtual Network サービス エンドポイント:**[Virtual Network サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)は、プロパティ値に 1 つ以上の正式な Azure サービスの種類名が含まれるサブネットです。 VNet サービス エンドポイントでは、SQL Database という名前の Azure サービスを参照する **Microsoft.Sql** というサービス種類名を使用します。 このサービス タグは、Azure SQL Database、Azure Database for PostgreSQL および MySQL サービスにも適用されます。 VNet サービス エンドポイントに **Microsoft.Sql** サービス タグを適用すると、サブネット上の Azure SQL Database、Azure Database for PostgreSQL、Azure Database for MySQL サーバーを含むすべての Azure Database サービスに対してサービス エンドポイント トラフィックが構成されることに注意することが重要です。 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Azure Database for MySQL データベース、VNet、VNet サービス エンドポイントを作成し、VNet ルールを使用してサブネットのサーバーをセキュリティで保護するサンプル スクリプト
このサンプル スクリプトでは、強調表示された行を変更して、管理者のユーザー名とパスワードをカスタマイズします。 `az account set --subscription` コマンドで使用するサブスクリプション ID を、自分のサブスクリプション ID に置き換えてください。
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
