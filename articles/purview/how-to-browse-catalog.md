---
title: '方法: Data Catalog を参照する'
description: この記事では、資産の種類に基づいて Azure Purview Data Catalog を参照する方法の概要を示します。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: dd2fd4fbc9d8aecd9821d668dd02bd4b68659cf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97695051"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Azure Purview Data Catalog の参照

この記事では、データ ソースの階層型名前空間を使用して Azure Purview Data Catalog でデータを検出する方法について説明します。

## <a name="browse-experience"></a>参照エクスペリエンス

データ コンシューマーは、エクスプローラー ビューを使用して、データ ソースごとに使い慣れた階層型名前空間を使用してデータを検出できます。 データ ソースが登録およびスキャンされると、データ マップによって、データ ソースの構造 (階層型名前空間) に関する情報が抽出されます。 この情報は、データ検出の参照エクスペリエンスを構築するために使用されます。

たとえば、*DateDimension* と言う名前のデータセットは、Azure Data Lake Storage Gen 2 の *[ディメンション]* フォルダーの下で簡単に見つけることができます。 [Browse by Asset Type]\(資産の種類で参照\) エクスペリエンスを使用して ADLS Gen 2 ストレージ アカウントに移動し、[サービス] > [コンテナー] > [フォルダー] を選択して特定の *[ディメンション]* フォルダーに移動し、*DateDimension* テーブルを参照できます。

階層型名前空間を使用したネイティブな参照エクスペリエンスは、対応するデータ ソースごとに提供されます。

## <a name="browse-the-data-catalog-by-asset-type"></a>資産の種類での Data Catalog の参照

1. データ資産を参照するには、ホームページで **[Browse by Asset Type]\(資産の種類で参照\)** をクリックします。

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Purview のホーム ページ" border="true":::

1. **[Browse asset types]\(資産の種類を参照\)** ページでは、データ ソース別にタイルが分類されます。 各データ ソースの資産をさらに調べるには、対応するタイルを選択します。

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="[Browse asset types]\(資産の種類を参照\) ページ" border="true":::

1. 次のページには、選択したデータの種類の最上位レベルの資産が一覧表示されます。 資産の 1 つを選択して、その内容を詳しく調べます。

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="資産の種類に固有の参照ページ。Azure Storage アカウントの例" border="true":::

1. エクスプローラー ビューが開きます。 左側のパネルで資産を選択して、参照を開始します。 ページの右側のパネルには、子資産が一覧表示されます。

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="エクスプローラー ビュー" border="true":::

1. 資産の詳細を表示するには、右端にある名前または省略記号ボタンを選択します。

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="省略記号ボタンを選択して、資産の詳細ページを表示" border="true":::

## <a name="view-related-data-assets"></a>関連するデータ資産の表示

資産の詳細ページが表示されたら、他の関連するデータ資産も表示できます。 たとえば、*DateDimension* の親フォルダーに移動して、 *[ディメンション]* フォルダーを表示したり、コンテナーに移動して特定の階層の資産を表示したりすることができます。

1. 資産の詳細ページで、 **[関連項目]** タブを選択してその他の関連する資産を表示します。

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="[関連項目] タブの起動" border="true":::

1. 現在の資産の完全な階層が左側に表示されます。

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="階層構造" border="true":::

1. 階層内の任意のレベルを選択すると、そのレベルの直下にある資産が一覧表示されます。

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="別の階層を選択" border="true":::

## <a name="next-steps"></a>次のステップ

- [用語集の用語を作成、インポート、エクスポートする方法](how-to-create-import-export-glossary.md)
- [ビジネス用語集の用語テンプレートを管理する方法](how-to-manage-term-templates.md)
