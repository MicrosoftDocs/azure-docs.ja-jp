---
title: チュートリアル:Azure Purview (プレビュー) でリソース セット、詳細、スキーマ、分類を考察する
description: このチュートリアルでは、リソース セット、資産の詳細、スキーマ、分類を使用する方法について説明します。
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c74324ebeeefeed361c0557c45a280a411effa22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693328"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>チュートリアル:Azure Purview (プレビュー) でリソース セット、詳細、スキーマ、分類を考察する

> [!IMPORTANT]
> Azure Purview は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このチュートリアルでは、カタログの主なコンポーネントであるリソース セット、資産の詳細、スキーマ、分類について詳しく見ていきます。

このチュートリアルは、"*5 部構成のチュートリアル シリーズのパート 4*" です。Azure Purview を使用して、データ資産全体にわたってデータ ガバナンスを管理する方法の基礎について説明します。 このチュートリアルは、「[パート 3: Azure Purview (プレビュー) で資産を参照してその系列を確認する](tutorial-browse-and-view-lineage.md)」で完了した作業に基づいて作成されています。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * リソース セットを表示する。
> * 資産の詳細を表示する。
> * スキーマを編集して分類を追加する。

## <a name="prerequisites"></a>必須コンポーネント

* 「[チュートリアル: Azure Purview (プレビュー) で資産を参照してその系列を確認する](tutorial-browse-and-view-lineage.md)」が完了していること。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="view-resource-sets"></a>リソース セットを表示する

リソース セットは、カタログにおいて、ストレージ内の多数の物理オブジェクトを表す単一のオブジェクトです。 通常これらのオブジェクトは、共通のスキーマを共有します。また、ほとんどの場合、名前付け規則やフォルダー構造も共有します。 たとえば、日付形式は *yyyy/mm/dd* です。 リソース セットの詳細については、[リソース セットについて](concept-resource-sets.md)のページを参照してください。

1. Azure portal で Azure Purview リソースに移動し、 **[Open Purview Studio]\(Purview Studio を開く\)** を選択します。 Purview Studio のホーム ページに自動的に移動します。

2. **[Search assets]\(資産の検索\)** ボックスに「**contoso_staging_positivecashflow_ n**」と入力し、検索結果から **[Contoso_Staging_PositiveCashFlow_{N}.csv]** を選択します。

## <a name="view-asset-details"></a>資産の詳細を表示する

1. **[概要]** タブに、**説明**、**用語集の用語**、**プロパティ** (**qualifiedName** など) が表示されます。

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="リソース セットの資産ページの [概要] タブを示すスクリーンショット。":::

1. **[プロパティ]** に、次の 2 つのフィールドがあることに注目してください。

   * **partitionCount**: このリソース セットに関連付けられている物理ファイルの数を示します。
   * **schemaCount**: このリソース セット内に見つかったスキーマのバリエーションの数を示します。

   これらのプロパティは、[このチュートリアル シリーズのパート 1](tutorial-scan-data.md) で実行したスキャンが完了してから 24 時間以内に、Azure Purview によって設定されます。

1. **[連絡先]** タブを選択して、 **[エキスパート]** と **[所有者]** の値を確認します。

## <a name="edit-the-schema-and-add-classifications"></a>スキーマを編集して分類を追加する

1. **[スキーマ]** タブを選択します。列名とそれらに関連付けられている分類を観察します。 プロパティは、スキャンによって自動的に設定されることに注意してください。

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="[スキーマ] タブを示すスクリーンショット。":::

1. 資産を編集するには、左上の **[編集]** ボタンを選択します。 次に、 **[スキーマ]** タブを選択します。

1. 列の **説明** を追加するか、または列名をもっとわかりやすい名前に変更します。

1. 資産レベルで分類を追加します。分類が未設定の列名について、 **[Column level classification]\(列レベルの分類\)** ボックスの一覧を選択してください。

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="スキーマの編集ページ":::

1. 変更が完了したら、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
>
> * リソース セットを表示する。
> * 資産の詳細を表示する。
> * スキーマを編集して分類を追加する。

次のチュートリアルに進んで、用語集の概要や、資産の新しい用語を定義してインポートする方法を確認してください。

> [!div class="nextstepaction"]
> [用語集の用語を作成してインポートする](tutorial-import-create-glossary-terms.md)