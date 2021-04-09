---
title: 仮想ネットワークを管理する - Azure portal - Azure Database for MySQL - フレキシブル サーバー
description: Azure portal を使用した Azure Database for MySQL - フレキシブル サーバーの仮想ネットワークの作成と管理
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 28506ea5afe085e9f52e40495d9931ae0aeddf32
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "90930878"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MySQL - フレキシブル サーバーの仮想ネットワークの作成と管理

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

Azure Database for MySQL フレキシブル サーバーでは、フレキシブル サーバーに接続するために、2 種類の相互に排他的なネットワーク接続方法をサポートしています。 次の 2 つのオプションがあります。

- パブリック アクセス (許可された IP アドレス)
- プライベート アクセス (VNet 統合)

この記事では、Azure portal を使用した **プライベート アクセス (VNet 統合)** での MySQL サーバーの作成について重点的に説明します。 プライベート アクセス (VNet 統合) を使用すると、フレキシブル サーバーを独自の [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) にデプロイできます。 Azure Virtual Network では、非公開で、セキュリティで保護されたネットワーク通信が提供されます。 プライベート アクセスでは、MySQL サーバーへの接続は仮想ネットワークに制限されます。 詳細については、「[プライベート アクセス (VNet 統合)](./concepts-networking.md#private-access-vnet-integration)」を参照してください。

フレキシブル サーバーは、サーバーの作成時に仮想ネットワークとサブネットにデプロイできます。 フレキシブル サーバーをデプロイした後は、そのサーバーを別の仮想ネットワーク、サブネット、または "*パブリック アクセス (許可された IP アドレス)* " に移動することはできません。

## <a name="prerequisites"></a>前提条件
仮想ネットワーク内にフレキシブル サーバーを作成するには、次が必要です。
- [仮想ネットワーク](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > 想ネットワークとサブネットは、フレキシブル サーバーと同じリージョンおよびサブスクリプション内に存在する必要があります。

-  **Microsoft.DBforMySQL/flexibleServers** に [サブネットを委任](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service)すること。 この委任は、Azure Database for MySQL フレキシブル サーバーのみがそのサブネットを使用できることを意味します。 委任されたサブネットに他の Azure リソースの種類を含めることはできません。

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>既に存在する仮想ネットワークに Azure Database for MySQL フレキシブル サーバーを作成する

1. ポータルの左上隅にある **[リソースの作成]** (+) を選択します。
2. **[データベース]**  >  **[Azure Database for MySQL]** の順に選択します。 検索ボックスに「**MySQL**」と入力してサービスを検索することもできます。
3. デプロイ オプションとして **[フレキシブル サーバー]** を選択します。
4. **[基本]** フォームに入力します。
5. **[ネットワーク]** タブに移動して、サーバーへの接続方法を構成します。
6. **[接続方法]** で、 **[プライベート アクセス (VNet 統合)]** を選択します。 **[仮想ネットワーク]** に移動し、上記の前提条件の一環として作成して既に存在する "*仮想ネットワーク*" と "*サブネット*" を選択します。
7. **[確認と作成]** を選択して、フレキシブル サーバーの構成を確認します。
8. **[作成]** を選択して、サーバーをプロビジョニングします。 プロビジョニングには数分かかる場合があります。

>[!Note]
> フレキシブル サーバーを仮想ネットワークとサブネットにデプロイした後は、パブリック アクセス (許可された IP アドレス) に移動することはできません。

## <a name="next-steps"></a>次のステップ
- [Azure CLI を使用して Azure Database for MySQL フレキシブル サーバー仮想ネットワークを作成および管理](./how-to-manage-virtual-network-cli.md)します。
- [Azure Database for MySQL フレキシブル サーバーでのネットワーク](./concepts-networking.md)の詳細を確認します
- [Azure Database for MySQL フレキシブル サーバー仮想ネットワーク](./concepts-networking.md#private-access-vnet-integration)の詳細を理解します。
