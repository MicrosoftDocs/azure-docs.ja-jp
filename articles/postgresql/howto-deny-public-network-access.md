---
title: パブリック ネットワーク アクセスを拒否する - Azure portal - Azure Database for PostgreSQL - 単一サーバー
description: Azure portal を使用して Azure Database for PostgreSQL 単一サーバーでパブリック ネットワーク アクセスを拒否するように構成する方法について説明します
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: e195c005676df27385e5e00736b04bdb689fafc5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727109"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Azure portal を使用して Azure Database for PostgreSQL 単一サーバーでパブリック ネットワーク アクセスを拒否する

この記事では、Azure Database for PostgreSQL 単一サーバーを、すべてのパブリック構成を拒否し、プライベート エンドポイント経由の接続のみを許可するように構成して、ネットワーク セキュリティを強化する方法について説明します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

* 価格レベルが General Purpose または Memory Optimized の [Azure Database for PostgreSQL 単一サーバー](quickstart-create-server-database-portal.md)。

## <a name="set-deny-public-network-access"></a>パブリック ネットワーク アクセスの拒否を設定する

パブリック ネットワーク アクセスを拒否するように PostgreSQL 単一サーバーを設定するには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for PostgreSQL 単一サーバーを選択します。

1. PostgreSQL 単一サーバー ページの **[設定]** で、 **[接続のセキュリティ]** をクリックして接続のセキュリティの構成ページを開きます。

1. **[Deny Public Network Access]\(パブリック ネットワーク アクセスを拒否する\)** で **[はい]** を選択して、PostgreSQL 単一サーバーのパブリック アクセスの拒否を有効にします。

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="Azure Database for PostgreSQL 単一サーバーのネットワーク アクセスの拒否":::

1. **[保存]** をクリックして変更を保存します。

1. 接続のセキュリティの設定が正常に有効化されたことを確認する通知が表示されます。

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="Azure Database for PostgreSQL 単一サーバーのネットワーク アクセスの拒否の成功":::

## <a name="next-steps"></a>次のステップ

[メトリックに関するアラートを作成する方法](howto-alert-on-metric.md)について学習します。
