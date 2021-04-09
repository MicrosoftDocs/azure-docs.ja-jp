---
title: VNet エンドポイントの管理 - Azure portal - Azure Database for MariaDB
description: Azure portal を使用して Azure Database for MariaDB VNet のサービス エンドポイントとルールを作成および管理します
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5eaa7821c61010b322d8f9032c439df28c297f3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665023"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Azure portal を使用して Azure Database for MariaDB VNet のサービス エンドポイントと VNet ルールを作成および管理する

Virtual Network (VNet) サービス エンドポイントおよびルールは、仮想ネットワークのプライベート アドレス空間を Azure Database for MariaDB サーバーに拡張します。 制限事項を含む、Azure Database for MariaDB VNet サービス エンドポイントの概要については、[Azure Database for MariaDB サーバー VNet サービス エンドポイント](concepts-data-access-security-vnet.md)に関する記事を参照してください。 VNet サービス エンドポイントは、Azure Database for MariaDB でサポートされるすべてのリージョンで利用できます。

> [!NOTE]
> VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>VNet ルールを作成してサービス エンドポイントを有効にする

1. MariaDB サーバー ページの [設定] で、**[接続のセキュリティ]** をクリックして Azure Database for MariaDB の [接続のセキュリティ] ページを開きます。

2. [Azure サービスへのアクセスを許可] が **[オフ]** に設定されていることを確認します。

> [!Important]
> 制御を [オン] に設定すると、Azure MariaDB サーバーはすべてのサブネットからの通信を受け入れます。 制御を [オン] に設定したままにすると、セキュリティの観点からアクセス過多になる可能性があります。 Microsoft Azure Virtual Network サービス エンドポイント機能は、Azure Database for MariaDB の仮想ネットワーク規則機能と共に使用することで、セキュリティ脅威にさらされる領域を減少させることができます。

3. 次に、 **[+ 既存の仮想ネットワークを追加]** をクリックします。 既存の VNet がない場合は、 **[+ 新しい仮想ネットワークの作成]** をクリックして作成できます。 「[クイック スタート:Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)」を参照してください

   ![Azure Portal - [接続のセキュリティ] のクリック](./media/howto-manage-vnet-portal/1-connection-security.png)

4. VNet ルール名を入力し、サブスクリプション、仮想ネットワーク、サブネット名を選択して、 **[有効にする]** をクリックします。 これにより、**Microsoft.SQL** サービス タグを使用してサブネット上で VNet サービス エンドポイントが自動的に有効になります。

   ![Azure portal - VNet の構成](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   アカウントには、仮想ネットワークとサービス エンドポイントを作成するためのアクセス許可が必要です。

   サービス エンドポイントは、仮想ネットワークへの書き込みアクセス権を持つユーザーが仮想ネットワーク上で個別に構成できます。
    
   Azure サービス リソースへのアクセスを VNet に限定するには、ユーザーが、追加されるサブネットの "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" へのアクセス許可を持っている必要があります。 このアクセス許可は、既定では組み込みのサービス管理者のロールに含まれ、カスタム ロールを作成することで変更できます。
    
   [組み込みロール](../role-based-access-control/built-in-roles.md)と、特定のアクセス許可を[カスタム ロール](../role-based-access-control/custom-roles.md)に割り当てる方法の詳細をご覧ください。
    
   Vnet と Azure サービス リソースのサブスクリプションは、同じでも異なっていてもかまいません。 VNet と Azure サービス リソースのサブスクリプションが異なる場合、リソースは同じ Active Directory (AD) テナントの下に置かれている必要があります。 両方のサブスクリプションに、**Microsoft.Sql** リソース プロバイダーが登録されていることを確認してください。 詳細については、[resource-manager-registration][resource-manager-portal] に関するページをご覧ください

   > [!IMPORTANT]
   > サービス エンドポイントを構成する前に、サービス エンドポイントの構成と考慮事項について、この記事を読むことを強くお勧めします。 **仮想ネットワーク サービス エンドポイント:** [仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)は、プロパティ値に 1 つ以上の正式な Azure サービスの種類名が含まれるサブネットです。 VNet サービス エンドポイントでは、SQL Database という名前の Azure サービスを参照する **Microsoft.Sql** というサービス種類名を使用します。 このサービス タグは、Azure SQL Database、Azure Database for MariaDB、PostgreSQL および MySQL サービスにも適用されます。 VNet サービス エンドポイントに **Microsoft.Sql** サービス タグを適用すると、サブネット上の Azure SQL Database、Azure Database for PostgreSQL、Azure Database for MariaDB、Azure Database for MySQL サーバーを含むすべての Azure Database サービスにサービス エンドポイント トラフィックが構成されることに注意することが重要です。
   > 

5. 有効になったら、 **[OK]** をクリックすると、VNet ルールと共に VNet サービス エンドポイントが有効になっていることが表示されます。

   ![VNet サービス エンドポイントが有効で、VNet ルールが作成されている](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>次のステップ
- [Azure Database for MariaDB に SSL を構成](howto-configure-ssl.md)する方法の詳細
- 同様に、[Azure CLI を使用して、VNet サービス エンドポイントを有効にし、Azure Database for MariaDB の VNET ルールを作成](howto-manage-vnet-cli.md)するスクリプトを作成できます。

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md