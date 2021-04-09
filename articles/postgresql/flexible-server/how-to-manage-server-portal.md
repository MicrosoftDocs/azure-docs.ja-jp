---
title: サーバーを管理する - Azure portal - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure portal から Azure Database for PostgreSQL - フレキシブル サーバーを管理する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc
ms.openlocfilehash: 1ac418d855696138341115412dc7e2601d4cf3a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91961410"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Azure portal を使用して Azure Database for PostgreSQL - フレキシブル サーバーを管理する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

この記事では、Azure Database for PostgreSQL - フレキシブル サーバーを管理する方法を示します。 管理タスクには、コンピューティングとストレージのスケーリング、管理者パスワードのリセット、サーバーの詳細の表示が含まれます。

## <a name="sign-in"></a>サインイン

[Azure portal](https://portal.azure.com) にサインインします。 Azure portal でフレキシブル サーバーのリソースに移動します。

## <a name="scale-compute-and-storage"></a>コンピューティングとストレージのスケーリング

サーバー作成後は、ニーズの変化に応じて、さまざまな[価格レベル](https://azure.microsoft.com/pricing/details/postgresql/)の間でスケーリングできます。 また、仮想コアを増減することによって、コンピューティングとメモリのスケールアップまたはダウンもできます。

> [!NOTE]
> ストレージを低い値にスケールダウンすることはできません。

1. お使いのサーバーを Azure portal で選択します。 **[設定]** セクションにある **[コンピューティングとストレージ]** を選択します。
2. **[コンピューティング レベル]** 、 **[仮想コア]** 、 **[ストレージ]** を変更して、より高いコンピューティング レベルを使用してサーバーをスケールアップしたり、ストレージまたは仮想コアを必要な値に増やすことで同じレベル内でスケールアップしたりできます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="ストレージのフレキシブル サーバーのスケーリング":::

> [!Important]
> - ストレージはスケールダウンできません。
> - 仮想コアをスケーリングすると、サーバーが再起動されます。

3. **[OK]** を選択して変更を保存します。

## <a name="reset-admin-password"></a>管理者パスワードをリセットする

Azure portal を使用して、管理者ロールのパスワードを変更できます。

1. お使いのサーバーを Azure portal で選択します。 **[概要]** ウィンドウで **[パスワードのリセット]** を選択します。
2. 新しいパスワードを入力し、そのパスワードを確認します。 テキストボックスに、パスワードの複雑さの要件についてのメッセージが表示されます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="フレキシブル サーバーのパスワードをリセットする":::

3. **[保存]** を選択して新しいパスワードを保存します。

## <a name="delete-a-server"></a>サーバーの削除

不要になったサーバーは削除できます。

1. お使いのサーバーを Azure portal で選択します。 **[概要]** ウィンドウで **[削除]** を選択します。
2. 入力ボックスにサーバーの名前を入力して、サーバーを削除することを確認します。

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="フレキシブル サーバーを削除する":::

   > [!IMPORTANT]
   > サーバーを削除すると、元に戻せません。

  > [!div class="mx-imgBorder"]
  > ![フレキシブル サーバーを削除する](./media/howto-manage-server-portal/delete-server.png)  

3. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- [バックアップと復元の概念を確認する](concepts-backup-restore.md)
- [サーバーのチューニングと監視を行う](concepts-monitoring.md)
