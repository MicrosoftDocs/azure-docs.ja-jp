---
title: Azure portal を使用した Azure Database for MariaDB サーバーの再起動
description: この記事では、Azure portal を使用して Azure Database for MariaDB サーバーを再起動する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 232037562c4a84ee9217e2e89a0da2ffdc37d560
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621891"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Azure portal を使用した Azure Database for MariaDB サーバーの再起動
このトピックでは、Azure Database for MariaDB サーバーを再起動する方法について説明します。 メンテナンス上の理由でサーバーの再起動が必要な場合があります。これを行うと、サーバーが操作を実行しているときに短時間の停止が発生します。

サービスがビジー状態の場合、サーバーの再起動はブロックされます。 たとえば、仮想コアのスケーリングなどの前に要求した操作がサービスで処理中である場合があります。

再起動を完了するために必要な時間は、MariaDB の回復プロセスに依存しています。 再起動の時間を短縮するために、再起動の実行前にサーバー上で発生しているアクティビティの量を最小限に抑えることをお勧めします。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for MariaDB サーバー](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>サーバーの再起動を実行する

次の手順で MariaDB サーバーを再起動します。

1. Azure portal で Azure Database for MariaDB サーバーを選択します。

2. サーバーの **[概要]** ページのツール バーで、**[再起動]** をクリックします。

   ![Azure Database for MariaDB - [概要] - [再起動] ボタン](./media/howto-restart-server-portal/2-server.png)

3. **[はい]** をクリックして、サーバーを再起動することを確認します。

   ![Azure Database for MariaDB - 再起動の確認](./media/howto-restart-server-portal/3-restart-confirm.png)

4. サーバーの状態が [再起動中] に変わることを確認します。

   ![Azure Database for MariaDB - 再起動の状態](./media/howto-restart-server-portal/4-restarting-status.png)

5. サーバーの再起動が成功したことを確認します。

   ![Azure Database for MariaDB - 再起動の成功](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>次の手順

[クイック スタート:Azure portal を使用した Azure Database for MariaDB サーバーの作成](./quickstart-create-mariadb-server-database-using-azure-portal.md)