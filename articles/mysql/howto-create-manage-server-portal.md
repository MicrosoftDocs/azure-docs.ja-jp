---
title: サーバーの管理 - Azure portal - Azure Database for MySQL
description: Azure portal から Azure Database for MySQL サーバーを管理する方法について説明します。
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/26/2021
ms.openlocfilehash: 83876f77e0d7ffc0ae20bc5a545c1f18f53f4a8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897987"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MySQL サーバーの管理

この記事では、Azure Database for MySQL サーバーを管理する方法について示します。 管理タスクには、コンピューティングとストレージのスケーリング、管理者パスワードのリセット、サーバーの詳細の表示が含まれます。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "_スレーブ_" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。
>

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

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Azure Database for MySQL で Basic、General Purpose、Memory Optimized のサービス レベルを選択する際の Azure portal のスクリーンショット":::

   > [!NOTE]
   > レベルを変更すると、サーバーが再起動されます。

3. **[OK]** を選択して変更を保存します。

### <a name="scale-vcores-up-or-down"></a>仮想コアのスケールアップまたはスケールダウン

1. お使いのサーバーを Azure portal で選択します。 **[設定]** セクションにある **[価格レベル]** を選択します。

2. スライダーを目的の値に動かして、 **[vCore]** の設定を変更します。

    :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Azure Database for MySQL で仮想コア オプションを選択する際の Azure portal のスクリーンショット":::

    > [!NOTE]
    > 仮想コアをスケーリングすると、サーバーが再起動されます。

3. **[OK]** を選択して変更を保存します。

### <a name="scale-storage-up"></a>ストレージのスケールアップ

1. お使いのサーバーを Azure portal で選択します。 **[設定]** セクションにある **[価格レベル]** を選択します。

2. スライダーを目的の値まで上に動かして、 **[ストレージ]** の設定を変更します。

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Azure Database for MySQL でストレージ スケールを選択する際の Azure portal のスクリーンショット":::

   > [!NOTE]
   > ストレージはスケールダウンできません。

3. **[OK]** を選択して変更を保存します。

## <a name="update-admin-password"></a>管理パスワードの更新

Azure portal を使用して、管理者ロールのパスワードを変更できます。

1. お使いのサーバーを Azure portal で選択します。 **[概要]** ウィンドウで **[パスワードのリセット]** を選択します。

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Azure Database for MySQL でパスワードをリセットする際の Azure portal のスクリーンショット":::

2. 新しいパスワードを入力し、そのパスワードを確認します。 テキストボックスに、パスワードの複雑さの要件についてのメッセージが表示されます。

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Azure Database for MySQL でパスワードをリセットして保存する際の Azure portal のスクリーンショット":::

3. **[OK]** を選択して新しいパスワードを保存します。
 

> [!IMPORTANT]
> サーバー管理者パスワードをリセットすると、自動的にサーバー管理者の特権が既定値にリセットされます。 1 つ以上のサーバー管理者特権を誤って取り消した場合は、サーバー管理者パスワードをリセットすることを検討してください。
   
> [!NOTE]
> サーバー管理者ユーザーには、既定で次の特権があります。SELECT、INSERT、UPDATE、DELETE、CREATE、DROP、RELOAD、PROCESS、REFERENCES、INDEX、ALTER、SHOW DATABASES、CREATE TEMPORARY TABLES、LOCK TABLES、EXECUTE、REPLICATION SLAVE、REPLICATION CLIENT、CREATE VIEW、SHOW VIEW、CREATE ROUTINE、ALTER ROUTINE、CREATE USER、EVENT、TRIGGER です

## <a name="delete-a-server"></a>サーバーの削除

不要になったサーバーは削除できます。

1. お使いのサーバーを Azure portal で選択します。 **[概要]** ウィンドウで **[削除]** を選択します。

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Azure Database for MySQL でサーバーを削除する際の Azure portal のスクリーンショット":::

2. 入力ボックスにサーバーの名前を入力して、削除するサーバーであることを確認します。

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Azure Database for MySQL でサーバーの削除を確定する際の Azure portal のスクリーンショット":::

   > [!NOTE]
   > サーバーを削除すると、元に戻せません。

3. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- [バックアップとサーバー復元](howto-restore-server-portal.md)について学習する
- [Azure Database for MySQL でのチューニングと監視のオプション](concepts-monitoring.md)について学習する
