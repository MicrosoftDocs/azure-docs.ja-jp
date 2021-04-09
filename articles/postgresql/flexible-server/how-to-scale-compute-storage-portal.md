---
title: スケール操作 - Azure portal - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure Portal を使用して Azure Database for PostgreSQL でスケール操作を実行する方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90931350"
---
# <a name="scale-operations-in-flexible-server"></a>フレキシブル サーバーでのスケール操作

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

この記事では、コンピューティングとストレージのスケーリング操作を実行する手順について説明します。 バースト可能 SKU、汎用 SKU、およびメモリ最適化された SKU の間で、アプリケーションの実行に適した仮想コアの数の選択など、コンピューティング レベルを変更できます。 ストレージをスケールアップすることもできます。 予想される IOPS は、コンピューティング レベル、仮想コア、およびストレージ容量に基づいて表示されます。 また、選択した内容に基づいてコスト見積もりも表示されます。

> [!IMPORTANT]
> ストレージをスケールダウンすることはできません。

## <a name="pre-requisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

-   Azure Database for PostgreSQL - フレキシブル サーバーを所有している必要があります。 同じ手順は、ゾーン冗長性が構成されたフレキシブル サーバーにも適用できます。
> [!IMPORTANT]
> 高可用性が構成されている場合、バースト可能 SKU を選択することはできません。 スケーリング操作中、スタンバイはまず目的のサイズにスケーリングされ、プライマリ サーバーはフェールオーバーされ、プライマリはスケーリングされます。 

## <a name="scaling-the-compute-tier-and-size"></a>コンピューティング レベルとサイズのスケーリング

コンピューティング レベルを選択するには、次の手順に従います。
 
1.  [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2.  **[コンピューティングとストレージ]** をクリックします。

3.  現在の設定を含むページが表示されます。
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="コンピューティングとストレージ ビュー":::

4.  バースト可能レベル、汎用レベル、およびメモリ最適化されたレベルの間で、コンピューティング クラスを選択できます。
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="コンピューティング レベルの一覧表示":::


5.  既定の仮想コアとメモリ サイズに問題がない場合は、次の手順を省略できます。

6.  仮想コアの数を変更する場合は、 **[コンピューティング サイズ]** のドロップダウンをクリックし、一覧から仮想コア/メモリの必要な数をクリックします。
    
    - バースト可能なコンピューティング レベル: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="バースト可能なコンピューティング":::

    - 汎用コンピューティング レベル: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="汎用コンピューティング":::

    - メモリ最適化されたコンピューティング レベル: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="メモリ最適化されたコンピューティング":::

7.  **[保存]** をクリックします。 
8.  確認メッセージが表示されます。 続行する場合は、 **[OK]** をクリックします。 
9.  進行中のスケーリング操作に関する通知。


## <a name="scaling-storage-size"></a>ストレージ サイズのスケーリング

次の手順に従って、ストレージ サイズを増やします。

1.  [Azure portal](https://portal.azure.com/) で、ストレージ サイズを増やすフレキシブル サーバーを選択します。
2.  **[コンピューティングとストレージ]** をクリックします。

3.  現在の設定を含むページが表示されます。
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="[コンピューティングとストレージ]をクリックします":::
4.  スライド バーのあるフィールド **[ストレージサイズ (GiB)]** が現在のサイズで表示されます。

5.  バーを目的のサイズまでスライドさせます。 対応する IOPS の数値が表示されます。 IOPS は、コンピューティング レベルとサイズに依存します。 コスト情報も表示されます。 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="ストレージのスケールアップ":::

6.  ストレージのサイズに問題がなければ、 **[保存]** をクリックします。 
7.  確認メッセージが表示されます。 続行する場合は、 **[OK]** をクリックします。 
8.  進行中のスケーリング操作に関する通知。

## <a name="next-steps"></a>次のステップ

-   [ビジネス継続性](./concepts-business-continuity.md)について確認します
-   [高可用性](./concepts-high-availability.md)について確認します
-   [バックアップと回復](./concepts-backup-restore.md)について確認します
