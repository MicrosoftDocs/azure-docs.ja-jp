---
title: パブリック ネットワーク アクセスを拒否する - Azure portal - Azure Database for PostgreSQL - 単一サーバー
description: Azure portal を使用して Azure Database for PostgreSQL 単一サーバーでパブリック ネットワーク アクセスを拒否するように構成する方法について説明します
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 01a256e17b1101782eaee9bebd85f5e7093773d3
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287781"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Azure portal を使用して Azure Database for PostgreSQL 単一サーバーでパブリック ネットワーク アクセスを拒否する

この記事では、Azure Database for PostgreSQL 単一サーバーを、すべてのパブリック構成を拒否し、プライベート エンドポイント経由の接続のみを許可するように構成して、ネットワーク セキュリティを強化する方法について説明します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

* [Azure Database for PostgreSQL 単一サーバー](quickstart-create-server-database-portal.md)

## <a name="set-deny-public-network-access"></a>パブリック ネットワーク アクセスの拒否を設定する

パブリック ネットワーク アクセスを拒否するように PostgreSQL 単一サーバーを設定するには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for PostgreSQL 単一サーバーを選択します。

1. PostgreSQL 単一サーバー ページの **[設定]** で、 **[接続のセキュリティ]** をクリックして接続のセキュリティの構成ページを開きます。

1. **[Deny Public Network Access]\(パブリック ネットワーク アクセスを拒否する\)** で **[はい]** を選択して、PostgreSQL 単一サーバーのパブリック アクセスの拒否を有効にします。

    ![Azure Database for PostgreSQL 単一サーバーのネットワーク アクセスの拒否](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. **[保存]** をクリックして変更を保存します。

1. 接続のセキュリティの設定が正常に有効化されたことを確認する通知が表示されます。

    ![Azure Database for PostgreSQL 単一サーバーのネットワーク アクセスの拒否の成功](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>次のステップ

[メトリックに関するアラートを作成する方法](howto-alert-on-metric.md)について学習します。
