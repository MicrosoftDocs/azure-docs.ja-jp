---
title: TLS の構成 - Azure portal - Azure Database for MariaDB
description: Azure portal を使用して Azure Database for MariaDB に対する TLS の構成を設定する方法について説明します
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 55a664ceb3524a0c545e037dcba7a2af90034ad4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664768"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Azure portal を使用して Azure Database for MariaDB で TLS の設定を構成する

この記事では、Azure Database for MariaDB サーバーを構成して、通過する接続に対して最小限の TLS バージョンを適用し、構成した最小 TLS バージョンより低い TLS バージョンの接続をすべて拒否することにより、ネットワーク セキュリティを強化する方法について説明します。

データベース サーバーに対する最小 TLS バージョンを設定することにより、Azure Database for MariaDB への接続に TLS のバージョンを強制できます。 たとえば、最小 TLS バージョンを TLS 1.0 に設定すると、サーバーでは TLS 1.0、1.1、および 1.2 以上を使用するクライアントからの接続が許可されます。 または、これを 1.2 に設定すると、TLS 1.2 以上を使用するクライアントからの接続だけを許可し、TLS 1.0 と TLS 1.1 を使用する接続はすべて拒否することを意味します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

* [Azure Database for MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Azure Database for MariaDB の TLS 構成を設定する

MariaDB サーバーの最小 TLS バージョンを設定するには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for MariaDB サーバーを選択します。

1. MariaDB サーバー ページの **[設定]** で、 **[接続のセキュリティ]** をクリックして接続のセキュリティの構成ページを開きます。

1. TLS 1.2 より低い TLS バージョンでの MariaDB サーバーへの接続を拒否するには、 **[TLS の最小バージョン]** で **[1.2]** を選択します。

    ![Azure Database for MariaDB の TLS 構成](./media/howto-tls-configurations/tls-configurations.png)

1. **[保存]** をクリックして変更を保存します。

1. 接続のセキュリティの設定が正常に有効化されたことを確認する通知が表示されます。

    ![Azure Database for MariaDB の TLS 構成の成功](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>次のステップ

[メトリックに関するアラートを作成する方法](howto-alert-metric.md)について学習します