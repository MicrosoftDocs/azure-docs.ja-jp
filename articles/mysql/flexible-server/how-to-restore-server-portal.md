---
title: 復元する - Azure portal - Azure Database for MySQL フレキシブル サーバー
description: この記事では、Azure portal を使用して Azure Database for SQL で復元操作を実行する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 062d53fcb122ebacd004d7dca5e11f5a883354cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241958"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL - フレキシブル サーバー (プレビュー) のポイントインタイム リストア


> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

この記事では、バックアップを使用して、フレキシブル サーバーでポイントインタイム リストアを実行する手順について説明します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

-   Azure Database for MySQL フレキシブル サーバーを所有している必要があります。

## <a name="restore-to-the-latest-restore-point"></a>最新の復元ポイントに復元する

以下の手順に従って、既存の最も古いバックアップを使用して、フレキシブル サーバーを復元します。

1.  [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2.  左側のパネルで **[概要]** をクリックします。

3.  [概要] ページで、 **[復元]** をクリックします。

    [プレースホルダー]

4.  [復元] ページには、**最新の復元ポイント** とカスタムの復元ポイントのどちらかを選択するオプションが表示されます。

5.  **[最新の復元ポイント]** を選択します。


6.  **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="最初の復元時刻":::

8.  **[OK]** をクリックします。

9.  復元操作が開始されたことを示す通知が表示されます。

## <a name="restoring-to-a-custom-restore-point"></a>カスタム復元ポイントへの復元

以下の手順に従って、既存の最も古いバックアップを使用して、フレキシブル サーバーを復元します。

1.  [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2.  [概要] ページで、 **[復元]** をクリックします。

    [プレースホルダー]

3.  [復元] ページに、最も古い復元ポイントとカスタム復元ポイントのどちらかを選択するオプションが表示されます。

4.  **[カスタム復元ポイント]** を選択します。

5.  日付と時刻を選択します。

6.  **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。

6.  **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。 
   
    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="概要の表示":::
 
7.  **[OK]** をクリックします。

8.  復元操作が開始されたことを示す通知が表示されます。

## <a name="next-steps"></a>次のステップ

プレースホルダー
