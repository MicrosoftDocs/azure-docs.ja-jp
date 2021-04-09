---
title: チュートリアル:Azure Purview ホーム ページを閲覧して資産を検索する
description: このチュートリアルでは、Azure Purview ホーム ページの機能を使用し、カタログ内で検索を行う方法について説明します。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 02/22/2020
ms.openlocfilehash: 5285d5b2c7cbe6845fb6043fa1096f94254602e7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677843"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>チュートリアル:Azure Purview (プレビュー) ホーム ページを閲覧して資産を検索する

> [!IMPORTANT]
> Azure Purview は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このチュートリアルでは、Azure Purview について理解するために、そのホーム ページにある機能をひととおり見ながら、カタログ内のアセットを検索します。

この記事は、"*5 部構成のチュートリアル シリーズのパート 2*" です。Azure Purview を使用して、データ資産全体にわたってデータ ガバナンスを管理する方法の基礎について説明します。 このチュートリアルは次のパートで完了した作業を基にしています。このシリーズの[第 1 部: Azure Purview でデータをスキャンする](tutorial-scan-data.md)方法に関するページを先に済ませておいてください。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
>
> * Azure Purview ホーム ページを閲覧する。
> * 資産を検索する。
> * 資産の所有者を追加する。

## <a name="prerequisites"></a>必須コンポーネント

* 「[チュートリアル: Azure Purview でデータをスキャンする](tutorial-scan-data.md)」が完了していること。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="navigate-the-azure-purview-home-page"></a>Azure Purview ホーム ページの閲覧

以降の手順に従って、Azure Purview ホーム ページをひととおり見てみましょう。

1. Azure portal で Azure Purview リソースに移動し、 **[Open Purview Studio]\(Purview Studio を開く\)** を選択します。 Purview Studio のホーム ページに自動的に移動します。

   ホーム ページの上部には、カタログの名前とカタログの分析結果一式が表示されます。 ソース数、データ資産の数、用語集の用語の数が表示されます。 サマリーを見ると、合計 200 を超える資産と 113 件の用語集の用語が存在していることがわかります。 この数は、組織がスキャンを実行し、Azure Purview に用語を追加するにつれて更新されます。

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="Azure Purview ホーム ページのスクリーンショット。":::

1. **[Browse assets]\(資産の参照\)** を選択すると、すべての資産が表示されます。

## <a name="search-for-an-asset"></a>資産の検索

最初に、いくつかの基本的な用語について簡単に説明します。

* **資産**: カタログにおいて、データ資産内のエンティティの定義を含んだ単一の簡潔なオブジェクト。 エンティティの例としては、SQL テーブル、Power BI レポート、データ ファクトリのアクティビティなどがあります。
  
* **[スキーマ]** : 列や属性ともいいます。スキーマは資産やリソース セットの構造を表します。

* **リソース セット**: カタログにおいて、ストレージ内の多数の物理オブジェクトを表す単一のオブジェクト。 これらのオブジェクトは通常、共通のスキーマを共有します。また、ほとんどの場合、名前付け規則やフォルダー構造も共有します。 たとえば、日付形式は *yyyy/mm/dd* です。 詳細については、[リソース セットについて](concept-resource-sets.md)のページを参照してください。

* **資産の種類**: 論理名の下にまとめられた資産とリソース セットのグループ。通常、データ プラットフォームの名前に対応します。

次の手順に従って資産を検索し、自分自身を所有者に指定してみましょう。

1. ホーム ページの **[Search catalog]\(カタログの検索\)** ボックスに、データ資産を含んだリソース グループ (前のチュートリアルで作成したリソース グループ) の名前を入力します。 候補が一覧表示されます。

1. **[View search results]\(検索結果の表示\)** を選択します。 どの資産もリソース グループの名前で始まるため、検索結果にはすべての資産が表示されます。 このチュートリアルでなければ、リソース グループではなく、特定の資産の名前で検索することになるでしょう。

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="カタログの検索候補の一覧を示すスクリーンショット。":::

1. 左側のナビゲーションにあるフィルターを使用すると、資産の種類、分類、連絡先、ラベル、用語集の用語に基づいてフィルタリングできます。

1. リソース セットを検索するには、セットの名前を先頭から入力していきます。 キーワードと一致する検索候補の一覧が表示されます。 引用符で囲むことにより絶対名を検索することもできます。

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Azure Purview キーワード資産検索":::

## <a name="edit-an-asset"></a>資産の編集

1. 検索結果からいずれかの資産を選択します。 次に、 **[Edit]\(編集\)** を選択します。

1. **[Overview]\(概要\)** タブで説明を追加できます。

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="[Overview]\(概要\) タブの [Description]\(説明\) フィールドを示すスクリーンショット。":::

1. **[連絡先]** タブを選択します。 **[Owners]\(所有者\)** の横にある **[Select user or group]\(ユーザーまたはグループの選択\)** で、会社のメール アドレスを先頭から入力していきます。

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="入力されたフィールドのスクリーンショット。":::

    名前の候補が自動的に表示されます。

1. **[Experts]\(エキスパート\)** の横の **[Select user or group]\(ユーザーまたはグループの選択\)** に名前 (直属のマネージャーの名前など) を入力し、 **[Save]\(保存\)** を選択します。

    説明、所有者名、エキスパート名の各フィールドの入力はこれで完了です。

## <a name="next-steps"></a>次のステップ

このシリーズの次のチュートリアルに進む前に、Azure Purview ホーム ページをご自身でもじっくり見てみてください。 このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
>
> * Azure Purview ホーム ページを閲覧する。
> * 資産を検索する。
> * 資産の所有者を追加する。

次のチュートリアルに進み、Azure Purview で資産を参照したり資産の系列を検出したりする方法を学習してください。

> [!div class="nextstepaction"]
> [資産を参照してその系列を確認する](tutorial-browse-and-view-lineage.md)
