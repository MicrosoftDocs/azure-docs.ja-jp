---
title: Azure Purview でデータ ソースを管理する (プレビュー)
description: Azure Purview (プレビュー) で新しいデータ ソースを登録する方法、データ ソースのコレクションを管理する方法、ソースを表示する方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 90a873b7de6ccc1ba21a05bf4c0e288ed668cac0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694463"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>Azure Purview でデータ ソースを管理する (プレビュー)

この記事では、Azure Purview (プレビュー) で新しいデータ ソースを登録する方法、データ ソースのコレクションを管理する方法、ソースを表示する方法について説明します

## <a name="register-a-new-source"></a>新しいソースを登録する

次の手順を使用して新しいソースを登録します。

1. Purview Studio を開き、 **[ソースの登録]** タイルを選択します。

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure Purview Studio":::

1. **[登録]** を選択し、ソースの種類を選択します。 この例では Azure Blob Storage が使用されます。 **[続行]** をクリックします。

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="[ソースの登録] ページでデータ ソースの種類を選択する":::

1. **[ソースの登録]**  ページでフォームに入力します。 ソースの名前を選択し、関連する情報を入力します。 アカウントの選択方法として **[Azure サブスクリプションから]** を選択した場合は、サブスクリプション内のソースがドロップダウン リストに表示されます。 または、ソース情報を手動で入力することもできます。

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="データ ソース情報のフォーム":::

1. **[完了]** を選択します。

## <a name="view-sources"></a>ソースを表示する

Azure Purview Studio の **[ソース]** タブで、登録されているすべてのソースを表示できます。 ビューには、マップ ビューとリスト ビューの 2 種類があります。

### <a name="map-view"></a>マップ ビュー

マップ ビューでは、すべてのソースとコレクションを表示できます。 次の図には、Azure Blob Storage ソースが 1 つあります。 それぞれのソース タイルから、ソースの編集、新しいスキャンの開始、またはソースの詳細の表示を行うことができます。

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Azure Purview データ ソース マップ ビュー":::

### <a name="list-view"></a>リスト ビュー

リスト ビューでは、並べ替え可能なソースの一覧を表示できます。 ソースにポインターを合わせると、編集、新しいスキャンの開始、または削除を行うオプションが表示されます。

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Azure Purview データ ソース リスト ビュー":::

## <a name="manage-collections"></a>コレクションを管理する

データ ソースは、コレクションにグループ化することができます。 新しいコレクションを作成するには、Azure Purview Studio の *[ソース]* ページで **[+ 新しいコレクション]** を選択します。 コレクションに名前を付け、親として *[なし]* を選択します。 新しいコレクションがマップ ビューに表示されます。

ソースをコレクションに追加するには、ソースで **[編集]** 鉛筆を選択し、 **[コレクションの選択]** ドロップダウン メニューからコレクションを選択します。

コレクションの階層を作成するには、上位レベルのコレクションを下位レベルのコレクションに親として割り当てます。 次の図で、*Fabrikam* は *Finance* コレクションの親であり、Azure Blob Storage のデータ ソースが含まれています。 レベル間の矢印に付いた円をクリックすることで、コレクションを折りたたんだり展開したりできます。

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Azure Purview Studio でのコレクションの階層":::

親に対して *[なし]* を選択すると、階層からソースを削除できます。 親を持たないソースは、マップ ビュー内の点線ボックスにグループ化されます。それらを親にリンクする矢印はありません。

## <a name="next-steps"></a>次のステップ

さまざまなデータ　ソースを登録およびスキャンする方法について説明します。

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Power BI のテナント](register-scan-power-bi-tenant.md)
* [Azure SQL Database](register-scan-azure-sql-database.md)
