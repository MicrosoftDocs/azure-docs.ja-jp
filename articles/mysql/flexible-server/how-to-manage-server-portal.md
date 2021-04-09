---
title: サーバーの管理 - Azure portal - Azure Database for MySQL フレキシブル サーバー
description: Azure portal から Azure Database for MySQL フレキシブル サーバーを管理する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7a01863b3a0c29e94550be67ca957655cff32660
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90930881"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Azure portal を使用して Azure Database for MySQL - フレキシブル サーバー (プレビュー) を管理する


> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

この記事では、Azure Database for MySQL フレキシブル サーバー (プレビュー) を管理する方法について示します。 管理タスクには、コンピューティングとストレージのスケーリング、管理者パスワードのリセット、サーバーの削除が含まれます。

## <a name="sign-in"></a>サインイン
[Azure portal](https://portal.azure.com) にサインインします。 Azure portal でフレキシブル サーバーのリソースに移動します。

## <a name="scale-compute-and-storage"></a>コンピューティングとストレージのスケーリング

サーバー作成後は、ニーズの変化に応じて、さまざまな[価格レベル](https://azure.microsoft.com/pricing/details/mysql/)の間でスケーリングできます。 また、仮想コアを増減することによって、コンピューティングとメモリのスケールアップまたはダウンもできます。

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

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="フレキシブル サーバーを削除する":::

   > [!NOTE]
   > サーバーを削除すると、元に戻せません。

3. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ
- [サーバーを起動または停止する方法を確認する](how-to-stop-start-server-portal.md)
- [サーバーを復元する方法を確認する](how-to-restore-server-portal.md)
- [接続の問題のトラブルシューティング](how-to-troubleshoot-common-connection-issues.md)

