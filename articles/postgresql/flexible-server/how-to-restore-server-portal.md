---
title: 復元 - Azure portal - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure portal を使用して Azure Database for PostgreSQL で復元操作を実行する方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90931198"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>フレキシブル サーバーのポイントインタイム リストア

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

この記事では、バックアップを使用して、フレキシブル サーバーでポイントインタイム リストアを実行する手順について説明します。 保持期間内に、最初の復元ポイントまたはカスタム復元ポイントへの復元を実行できます。

## <a name="pre-requisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

-   Azure Database for PostgreSQL - フレキシブル サーバーを所有している必要があります。 同じ手順は、ゾーン冗長性が構成されたフレキシブル サーバーにも適用できます。

## <a name="restoring-to-the-earliest-restore-point"></a>最初の復元ポイントへの復元

以下の手順に従って、既存の最も古いバックアップを使用して、フレキシブル サーバーを復元します。

1.  [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2.  左側のパネルから **[概要]** をクリックし、 **[復元]** をクリックします。
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="復元の概要":::

3.  [復元] ページに、最も古い復元ポイントとカスタム復元ポイントのどちらかを選択するオプションが表示されます。

4.  **[最初の復元ポイント]** を選択し、 **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。 復元できる最も古いタイムスタンプが表示されます。 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="最初の復元時刻":::

5.  **[OK]** をクリックします。

6.  復元操作が開始されたことを示す通知が表示されます。

## <a name="restoring-to-a-custom-restore-point"></a>カスタム復元ポイントへの復元

以下の手順に従って、既存の最も古いバックアップを使用して、フレキシブル サーバーを復元します。

1.  [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2.  [概要] ページで、 **[復元]** をクリックします。
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="復元の概要":::
    
3.  [復元] ページに、最も古い復元ポイントとカスタム復元ポイントのどちらかを選択するオプションが表示されます。

4.  **[カスタム復元ポイント]** を選択します。

5.  日時を選択し、 **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="カスタム復元時刻":::
 
6.  **[OK]** をクリックします。

7.  復元操作が開始されたことを示す通知が表示されます。

## <a name="next-steps"></a>次のステップ

-   [ビジネス継続性](./concepts-business-continuity.md)について確認する
-   [ゾーン冗長による高可用性](./concepts-high-availability.md)について確認する
-   [バックアップと復旧](./concepts-backup-restore.md)について確認する
