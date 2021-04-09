---
title: 資産に分類を適用する (プレビュー)
description: このドキュメントでは、資産に分類を適用する方法について説明します。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96550896"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Azure Purview で資産に分類を適用する

この記事では、資産に分類を適用する方法について説明します。

## <a name="introduction"></a>はじめに

分類には、システム型とカスタム型があります。 システム分類は、既定で Purview に存在しています。 カスタム分類は、正規表現パターンに基づいて作成できます。 分類は、自動または手動で資産に適用できます。

このドキュメントでは、データに分類を適用する方法について説明します。

## <a name="prerequisites"></a>前提条件

- 必要に応じてカスタム分類を作成します。
- データ ソースに対するスキャンを設定します。

## <a name="apply-classifications"></a>分類の適応
Azure Purview では、ファイル、テーブル、または列の資産にシステム分類またはカスタム分類を適用できます。 この記事では、資産に分類を手動で適用する手順について説明します。

### <a name="apply-classification-to-a-file-asset"></a>ファイル資産に分類を適用する
Azure Purview では、ドキュメント ファイルをスキャンし、自動的に分類することができます。 たとえば、*multiple.docx* という名前のファイルがあり、そのコンテンツに国民識別番号が含まれている場合、Azure Purview によって、**EU の国民識別番号** という分類がファイル資産の詳細ページに追加されます。

シナリオによっては、ファイル資産に分類を手動で追加したい場合があります。 リソース セットにグループ化された複数のファイルがある場合は、リソース セット レベルで分類を追加します。

カスタム分類またはシステム分類をパーティション リソース セットに追加するには、次の手順に従います。

1. パーティションを検索または参照し、資産の詳細ページに移動します。

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="資産の詳細ページを示すスクリーンショット。":::

1. **[概要]** タブで、 **[分類]** セクションを表示して、既存の分類があるかどうかを確認します。 **[Edit]\(編集\)** を選択します。

1. **[分類]** ドロップ ダウン リストから、目的の分類を選択します。 たとえば、システム分類の **[クレジット カード番号]** や、カスタム分類の **[CustomerAccountID]** です。

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="資産に追加する分類を選択する方法を示すスクリーンショット。":::

1. **[保存]** を選びます。

1. **[概要]** タブで、選択した分類が **[分類]** セクションに表示されていることを確認します。

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="分類が資産に追加されたことを確認する方法を示すスクリーンショット。":::

### <a name="apply-classification-to-a-table-asset"></a>テーブル資産に分類を適用する

Azure Purview では、データ ソースをスキャンするときに、テーブル資産に分類が自動的に割り当てられません。 テーブル資産に分類を設定する場合は、手動で追加する必要があります。

テーブル資産に分類を追加するには、次の手順に従います。

1. 目的のテーブル資産を見つけます。 たとえば、**Customer** テーブルです。

1. テーブルに分類が割り当てられていないことを確認します。 **[編集]** を選択します。

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="テーブル資産の分類を表示および編集する方法を示すスクリーンショット。":::

1. **[分類]** ドロップダウン リストから、1 つまたは複数の分類を選択します。 この例では、 **[CustomerInfo]** という名前のカスタム分類を使用しますが、任意の分類をこの手順で選択できます。

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="テーブル資産に追加する分類を選択する方法を示すスクリーンショット。":::

1. **[保存]** を選択して分類を保存します。

1. **[概要]** ページで、Azure Purview によって新しい分類が追加されたことを確認します。

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="分類がテーブル資産に追加されたことを確認する方法を示すスクリーンショット。":::

### <a name="add-classification-to-a-column-asset"></a>列資産に分類を追加する

Azure Purview では、スキャンが自動的に実行され、すべての列資産に分類が追加されます。 ただし、分類を変更する場合は、列レベルで変更できます。

列に分類を追加するには、次の手順に従います。

1. 列資産を検索して選択し、 **[概要]** タブの **[編集]** を選択します。

1. **[スキーマ]** タブを選択します

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="列のスキーマを編集する方法を示すスクリーンショット。":::

1. 目的の列を特定し、 **[分類の追加]** を選択します。 この例では、 **[共通のパスワード]** 分類を **[PasswordHash]** 列に追加します。

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="分類を列に追加する方法を示すスクリーンショット。":::

1. **[保存]** を選びます。

1. **[スキーマ]** タブを選択し、分類が列に追加されていることを確認します。

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="分類が列スキーマに追加されたことを確認する方法を示すスクリーンショット。":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>既存の分類に対する再スキャンの影響

分類は、データに対するサンプル セットのチェックに基づいて、設定された正規表現のパターンと照合されて、最初に適用されます。 再スキャン時に、新しい分類が適用された場合は、列に分類が追加されます。 列に既存の分類が残っているので、手動で削除する必要があります。

## <a name="next-steps"></a>次のステップ
カスタム分類を作成する方法については、[カスタム分類の作成](create-a-custom-classification-and-classification-rule.md)に関する記事を参照してください。