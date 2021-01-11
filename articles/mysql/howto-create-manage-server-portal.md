---
title: サーバーの管理 - Azure portal - Azure Database for MySQL
description: Azure portal から Azure Database for MySQL サーバーを管理する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: dfca1eb2eebe4418880be9da67e250bbc6e380a9
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852178"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MySQL サーバーの管理

この記事では、Azure Database for MySQL サーバーを管理する方法について示します。 管理タスクには、コンピューティングとストレージのスケーリング、管理者パスワードのリセット、サーバーの詳細の表示が含まれます。

## <a name="sign-in"></a>サインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-server"></a>サーバーの作成

Azure Database for MySQL サーバーを作成して使用を開始する方法については、[クイックスタート](quickstart-create-mysql-server-database-using-azure-portal.md)を参照してください。

## <a name="scale-compute-and-storage"></a>コンピューティングとストレージのスケーリング

サーバー作成後は、ニーズの変化に応じて、汎用レベルとメモリ最適化レベルの間でスケーリングできます。 また、仮想コアを増減することによって、コンピューティングとメモリのスケーリングもできます。 ストレージをスケールアップすることはできますが、ストレージをスケールダウンすることはできません。

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>汎用レベルとメモリ最適化レベルの間のスケーリング

汎用からメモリ最適化 (またはその逆) にスケーリングできます。 サーバー作成後の Basic レベルへの変更や Basic レベルからの変更はサポートされていません。

1. お使いのサーバーを Azure portal で選択します。 **[設定]** セクションにある **[価格レベル]** を選択します。

2. スケーリングの対象に応じて、 **[汎用]** または **[メモリ最適化]** を選択します。

   ![Azure Database for MySQL で Basic、General Purpose、Memory Optimized のサービス レベルを選択する際の Azure portal のスクリーンショット](./media/howto-create-manage-server-portal/change-pricing-tier.png)

   > [!NOTE]
   > レベルを変更すると、サーバーが再起動されます。

3. **[OK]** を選択して変更を保存します。

### <a name="scale-vcores-up-or-down"></a>仮想コアのスケールアップまたはスケールダウン

1. お使いのサーバーを Azure portal で選択します。 **[設定]** セクションにある **[価格レベル]** を選択します。

2. スライダーを目的の値に動かして、 **[vCore]** の設定を変更します。

    ![Azure Database for MySQL で仮想コア オプションを選択する際の Azure portal のスクリーンショット](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > 仮想コアをスケーリングすると、サーバーが再起動されます。

3. **[OK]** を選択して変更を保存します。

### <a name="scale-storage-up"></a>ストレージのスケールアップ

1. お使いのサーバーを Azure portal で選択します。 **[設定]** セクションにある **[価格レベル]** を選択します。

2. スライダーを目的の値まで上に動かして、 **[ストレージ]** の設定を変更します。

   ![Azure Database for MySQL でストレージ スケールを選択する際の Azure portal のスクリーンショット](./media/howto-create-manage-server-portal/scaling-storage.png)

   > [!NOTE]
   > ストレージはスケールダウンできません。

3. **[OK]** を選択して変更を保存します。

## <a name="update-admin-password"></a>管理パスワードの更新

Azure portal を使用して、管理者ロールのパスワードを変更できます。

1. お使いのサーバーを Azure portal で選択します。 **[概要]** ウィンドウで **[パスワードのリセット]** を選択します。

   ![Azure Database for MySQL でパスワードをリセットする際の Azure portal のスクリーンショット](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. 新しいパスワードを入力し、そのパスワードを確認します。 テキストボックスに、パスワードの複雑さの要件についてのメッセージが表示されます。

   ![Azure Database for MySQL でパスワードをリセットして保存する際の Azure portal のスクリーンショット](./media/howto-create-manage-server-portal/reset-password.png)

3. **[OK]** を選択して新しいパスワードを保存します。

## <a name="delete-a-server"></a>サーバーの削除

不要になったサーバーは削除できます。

1. お使いのサーバーを Azure portal で選択します。 **[概要]** ウィンドウで **[削除]** を選択します。

   ![Azure Database for MySQL でサーバーを削除する際の Azure portal のスクリーンショット](./media/howto-create-manage-server-portal/overview-delete.png)

2. 入力ボックスにサーバーの名前を入力して、削除するサーバーであることを確認します。

   ![Azure Database for MySQL でサーバーの削除を確定する際の Azure portal のスクリーンショット](./media/howto-create-manage-server-portal/confirm-delete.png)

   > [!NOTE]
   > サーバーを削除すると、元に戻せません。

3. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- [バックアップとサーバー復元](howto-restore-server-portal.md)について学習する
- [Azure Database for MySQL でのチューニングと監視のオプション](concepts-monitoring.md)について学習する
