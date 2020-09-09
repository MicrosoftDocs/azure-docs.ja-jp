---
title: パブリック ネットワーク アクセスを拒否する - Azure portal - Azure Database for MySQL
description: Azure portal を使用して Azure Database for MySQL でパブリック ネットワーク アクセスを拒否するように構成する方法について説明します
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: f8156b01244012d78214f2ba8c49ed76dbceed6d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118785"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Azure portal を使用して Azure Database for MySQL でパブリック ネットワーク アクセスを拒否する

この記事では、Azure Database for MySQL サーバーを、すべてのパブリック構成を拒否し、プライベート エンドポイント経由の接続のみを許可するように構成して、ネットワーク セキュリティを強化する方法について説明します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>パブリック ネットワーク アクセスの拒否を設定する

パブリック ネットワーク アクセスを拒否するように MySQL サーバーを設定するには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for MySQL サーバーを選択します。

1. MySQL サーバー ページの **[設定]** で、 **[接続のセキュリティ]** をクリックして接続のセキュリティの構成ページを開きます。

1. **[Deny Public Network Access]\(パブリック ネットワーク アクセスを拒否する\)** で **[はい]** を選択して、MySQL サーバーのパブリック アクセスの拒否を有効にします。

    ![Azure Database for MySQL のネットワーク アクセスの拒否](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. **[保存]** をクリックして変更を保存します。

1. 接続のセキュリティの設定が正常に有効化されたことを確認する通知が表示されます。

    ![Azure Database for MySQL のネットワーク アクセスの拒否の成功](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>次のステップ

[メトリックに関するアラートを作成する方法](howto-alert-on-metric.md)について学習します。