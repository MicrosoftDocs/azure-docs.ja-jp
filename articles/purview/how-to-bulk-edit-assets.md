---
title: 選択された資産の一覧に複数の用語集の用語をタグ付けする方法
description: Azure Purview で資産を一括編集する方法について説明します。
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 493d3ceb596bb29da0e60ab08518375412417a0e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049486"
---
# <a name="how-to-bulk-edit-assets-to-tag-glossary-terms"></a>資産を一括編集して用語集の用語にタグを付ける方法

この記事では、1 回の操作で、選択された資産の一覧に複数の用語集の用語をタグ付けする方法について説明します。

### <a name="add-assets-to-view-selected-list-using-search"></a>検索を使用して資産を [選択対象の表示] リストに追加する

1. 一括編集用の一覧に追加するデータ資産を検索します。

2. 検索結果ページで、一括編集の **[選択対象の表示]** 一覧に追加する資産の上にマウス ポインターを移動すると、チェックボックスが表示されます。

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="チェックボックスのスクリーンショット。":::

3. チェックボックスをオンにして、一括編集の **[選択対象の表示]** 一覧にそれを追加します。 追加されると、ページの下部に選択された項目アイコンが表示されます。

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="一覧のスクリーンショット。":::

4. 上記の手順を繰り返して、すべてのデータ資産を一覧に追加します。

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>資産の詳細ページから [選択対象の表示] リストに資産を追加する

1. 資産の詳細ページで、右上隅にあるチェックボックスをオンにして、一括編集の **[選択対象の表示]** 一覧に資産を追加します。

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="資産のスクリーンショット。":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>[選択対象の表示] 一覧で資産を一括編集して、用語集の用語を追加、置換、または削除します。

1. 一括編集が必要なすべてのデータ資産の特定が完了したら、検索結果ページまたは資産の詳細ページから **[選択対象の表示]** 一覧を選択します。

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="表示のスクリーンショット。":::

2. 一覧を確認し、用語を削除する場合は **[削除]** を選択します。

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="削除のスクリーンショット。":::

3. 選択したすべての資産で用語集の用語を追加、削除、または置換するには、一括編集を選択します。

4. 用語集の用語を追加するには、操作として **[追加]** を選択します。 [新しい値] で、追加するすべての用語集の用語を選択します。 完了したら、[適用] を選択します。
    - [追加] 操作により、既にデータ資産にタグ付けされている用語集の用語の一覧に新しい値が追加されます。  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="追加のスクリーンショット。":::

5. 用語集の用語を置き換えるには、操作として **[Replace with]\(書き換え\)** を選択します。 [新しい値] で、置き換えるすべての用語集の用語を選択します。 完了したら、[適用] を選択します。
    - 置き換え操作によって、選択したデータ資産で、すべての用語集の用語が、[新しい値] で選択された用語に置き換えられます。
   
6. 用語集の用語を削除するには、操作として **[削除]** を選択します。 完了したら、[適用] を選択します。
    - 削除操作によって、選択したデータ資産で、すべての用語集の用語が削除されます。
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="用語の削除のスクリーンショット。":::

7. classifications、owners、experts に対して上の手順を繰り返します。

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="分類と連絡先のスクリーンショット。":::

8. 完了したら、 **[閉じる]** または **[Remove all and close]\(すべて削除して閉じる\)** を選択して一括編集ブレードを閉じます。 [閉じる] では、選択された資産は削除されません。一方、[Remove all and close]\(すべて削除して閉じる\) では、すべての選択された資産が削除されます。
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="閉じるのスクリーンショット。":::

   > [!Important]
   > 一括編集で推奨される資産の数は 25 です。 25 を超える数を選択すると、パフォーマンスの問題が発生する可能性があります。
   > **[選択対象の表示]** ボックスは、少なくとも 1 つの資産が選択されている場合にのみ表示されます。


次のチュートリアルを修了してください。[チュートリアル:用語集の用語の作成とインポート](how-to-create-import-export-glossary.md)
