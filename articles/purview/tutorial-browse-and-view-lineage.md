---
title: チュートリアル:Azure Purview で資産を参照してその系列を確認する
description: このチュートリアルでは、カタログ内の資産を参照して、データ系列を確認する方法について説明します。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 7ffbe2ded44ded4f580655f6ae9e98391490f94a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696095"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>チュートリアル:Azure Purview (プレビュー) で資産を参照してその系列を確認する

> [!IMPORTANT]
> Azure Purview は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このチュートリアルでは、Azure Purview で資産を参照し、その重要な情報 (系列など) を確認する方法について説明します。

このチュートリアルは、"*5 部構成のチュートリアル シリーズのパート 3*" です。Azure Purview を使用して、データ資産全体にわたってデータ ガバナンスを管理する方法の基礎について説明します。 このチュートリアルは、「[パート 2: Azure Purview (プレビュー) ホーム ページを閲覧して資産を検索する](tutorial-asset-search.md)」で完了した作業に基づいて作成されています。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
>
> * カタログ内の資産を参照する。
> * 資産の系列を確認する。

## <a name="prerequisites"></a>必須コンポーネント

* 「[チュートリアル: Azure Purview (プレビュー) ホーム ページを閲覧して資産を検索する](tutorial-asset-search.md)」が完了していること。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="browse-for-assets-in-the-catalog"></a>カタログ内の資産を参照する

前のチュートリアルでは、検索を利用して Azure Purview カタログから資産を検出する方法について説明しました。 カタログのブラウズ環境を使用して、カタログから資産を検出する方法もあります。

このセクションでは、Azure Purview のカタログを参照する方法について説明します。

1. Azure portal で Azure Purview リソースに移動し、 **[Open Purview Studio]\(Purview Studio を開く\)** を選択します。 Purview Studio のホーム ページに自動的に移動します。

1. **[ホーム]** ページで **[Browse assets]\(資産の参照\)** を選択します。

   **[Browse assets]\(資産の参照\)** ページが表示され、カタログにある全種類の資産のサマリーが表示されます。

1. カタログに存在する Azure Data Lake Gen2 タイプの各種資産を詳しく見るために、 **[Azure Data Lake Gen2]** タイルを選択します。

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="資産タイプ別の閲覧ページを示すスクリーンショット。Azure Data Lake Gen2 が選択されています。":::

1. 複数の資産が存在する場合、 **[名前]** 列ヘッダーを選択して、資産をアルファベット順に並べ替えます。 このチュートリアルでは、Azure Data Lake Storage Gen2 資産は 1 つしかありません。

1. 資産の名前を選択します。

1. **[Contoso_GrossProfit_{N}.ssv]** リソース セットを選択します。 この資産がカタログに存在しない場合は、別の資産を選択してください。

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Azure Data Lake Storage Gen2 リソースの一覧":::

## <a name="view-the-lineage-of-assets"></a>資産の系列を確認する

資産の詳細ページで、データのソースの詳細を確認します。

1. **[Contoso_GrossProfit_{N}.ssv]** リソース セットの **[系列]** タブを選択します。

   選択した資産が表示されます。 表示される系列情報から、このリソース セットは、Azure Blob Storage アカウントから Azure Data Lake Storage Gen2 にコピーされたものであることがわかります。

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="資産の系列ビューを示すスクリーンショット。":::

1. このページの BLOB 資産を選択し、 **[Switch to asset]\(資産に切り替え\)** リンクを選択します。

   ウィンドウが Azure Blob リソース セットに切り替わったことを確認してください。元の Azure Data Lake Storage Gen2 のソースとなっていたのは、このリソース セットです。

1. **[概要]** タブを選択して資産を調査し、その詳細を確認します。

Azure Blob と Azure Data Lake Storage Gen2 リソース セット間に Azure Data Factory データ フロー アクティビティを作成してその系列を観察する方法については、[Azure Data Factory データ フロー アクティビティ](../data-factory/concepts-data-flow-overview.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
>
> * カタログ内の資産を参照する。
> * 資産の系列を確認する。

次のチュートリアルに進んで、リソース セット、資産の詳細、スキーマ、分類について学習してください。

> [!div class="nextstepaction"]
> [リソース セット、資産の詳細、スキーマ、分類](tutorial-schemas-and-classifications.md)
