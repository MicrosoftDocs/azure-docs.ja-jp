---
title: 仮想ネットワークを管理する - Azure portal - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure portal を使用した Azure Database for PostgreSQL - フレキシブル サーバーの仮想ネットワークの作成と管理
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 746f15d2d712f4b571d3f27e3535c69f5f4f9732
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732770"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Azure portal を使用した Azure Database for PostgreSQL - フレキシブル サーバーの仮想ネットワークの作成と管理

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

Azure Database for PostgreSQL - フレキシブル サーバーでは、フレキシブル サーバーに接続するために、同時に使用できないネットワーク接続方法が 2 種類サポートされています。 次の 2 つのオプションがあります。

* パブリック アクセス (許可された IP アドレス)
* プライベート アクセス (VNet 統合)

この記事では、Azure portal で、**プライベート アクセス (VNet 統合)** を使用して PostgreSQL サーバーを作成する方法について重点的に説明します。 プライベート アクセス (VNet 統合) を使用して、ご利用のフレキシブル サーバーを専用の [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) にデプロイできます。 Azure Virtual Network により、非公開で、セキュリティで保護されたネットワーク通信が提供されます。 プライベート アクセスでは、PostgreSQL サーバーへの接続はご利用の仮想ネットワークに制限されます。 詳細については、「[プライベート アクセス (VNet 統合)](./concepts-networking.md#private-access-vnet-integration)」を参照してください。

フレキシブル サーバーは、サーバーの作成時に仮想ネットワークとサブネットにデプロイできます。 フレキシブル サーバーをデプロイした後は、そのサーバーを別の仮想ネットワーク、サブネット、または "*パブリック アクセス (許可された IP アドレス)* " に移動することはできません。

## <a name="prerequisites"></a>前提条件
仮想ネットワーク内にフレキシブル サーバーを作成するには、次が必要です。
- [仮想ネットワーク](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > 仮想ネットワークとサブネットは、ご利用のフレキシブル サーバーと同じリージョンおよびサブスクリプション内に存在する必要があります。

-  **Microsoft.DBforPostgreSQL/flexibleServers** に [サブネットを委任](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service)すること。 この委任は、Azure Database for PostgreSQL フレキシブル サーバーのみがそのサブネットを使用できることを意味します。 委任されたサブネットに他の Azure リソースの種類を含めることはできません。
-  フレキシブル サーバーに委任されたサブネット用に、`Microsoft.Storage` をサービス エンドポイントに追加します。 これは、次の手順を実行することで行われます。
     1. 仮想ネットワーク ページにアクセスします。
     2. フレキシブル サーバーのデプロイを計画している VNET を選択します。
     3. フレキシブル サーバー用に委任されているサブネットを選択します。
     4. プルアウト画面の **[サービス エンドポイント]** で、ドロップダウンから `Microsoft.storage` を選択します。
     5. 変更を保存します。


## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>Azure Database for PostgreSQL - フレキシブル サーバーを既存の仮想ネットワーク内に作成する

1. ポータルの左上隅にある **[リソースの作成]** (+) を選択します。
2. **[データベース]**  >  **[Azure Database for PostgreSQL]** の順に選択します。 検索ボックスに「**PostgreSQL**」と入力してサービスを検索することもできます。
3. デプロイ オプションとして **[Flexible server]\(フレキシブル サーバー\)** を選択します。
4. **[基本]** フォームに入力します。
5. **[ネットワーク]** タブに移動して、サーバーへの接続方法を構成します。
6. **[接続方法]** で、 **[プライベート アクセス (VNet 統合)]** を選択します。 **[仮想ネットワーク]** に移動し、上記の前提条件の一環として作成して既に存在する "*仮想ネットワーク*" と "*サブネット*" を選択します。
7. **[確認と作成]** を選択して、フレキシブル サーバーの構成を確認します。
8. **[作成]** を選択して、サーバーをプロビジョニングします。 プロビジョニングには数分かかる場合があります。

>[!Note]
> フレキシブル サーバーを仮想ネットワークとサブネットにデプロイした後は、パブリック アクセス (許可された IP アドレス) に移動することはできません。
## <a name="next-steps"></a>次のステップ
- [Azure CLI を使用して Azure Database for PostgreSQL - フレキシブル サーバー仮想ネットワークを作成し、管理する](./how-to-manage-virtual-network-cli.md)。
- [Azure Database for PostgreSQL - フレキシブル サーバーのネットワーク](./concepts-networking.md)の詳細を確認する
- [Azure Database for PostgreSQL - フレキシブル サーバー仮想ネットワーク](./concepts-networking.md#private-access-vnet-integration)の詳細を理解する。