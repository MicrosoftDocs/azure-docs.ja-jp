---
title: チュートリアル:Azure Purview (プレビュー) で用語集の用語を作成してインポートする
description: このチュートリアルでは、用語集の用語を作成する方法と資産に追加する方法、インポートする方法について説明します。
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 0ea6fcaff1ec699431da8b67adee68735a8611a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696081"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>チュートリアル:Azure Purview (プレビュー) で用語集の用語を作成してインポートする

> [!IMPORTANT]
> Azure Purview は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

用語集は、カタログをメンテナンスしたり整理したりするための重要なツールです。 用語集を作成するには、新しい用語を定義するか用語リストをインポートして、それらの用語を資産に適用します。

このチュートリアルは、"*5 部構成のチュートリアル シリーズのパート 5*" です。Azure Purview を使用して、データ資産全体にわたってデータ ガバナンスを管理する方法の基礎について説明します。 このチュートリアルは、[パート 4「Azure Purview (プレビュー) でリソース セット、詳細、スキーマ、分類を考察する」](tutorial-schemas-and-classifications.md)で完了した作業に基づいて作成されています。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 用語集の用語を作成する。
> * 用語集の用語を資産に追加する。
> * 用語集の用語をインポートする。

## <a name="prerequisites"></a>必須コンポーネント

* 「[チュートリアル: Azure Purview (プレビュー) でリソース セット、詳細、スキーマ、分類を考察する](tutorial-schemas-and-classifications.md)」の作業を済ませてください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-glossary-terms"></a>用語集の用語を作成する

用語集には、ビジネスまたは技術に関する用語を作成できます。 資産に用語を適用することによって注釈を付けることもできます。

以下に示すのは、 **[Glossary term]\(用語集の用語\)** ページで最も一般的ないくつかのフィールドの概要です。

* **状態**: 用語の状態として、 **[Draft]\(下書き\)** 、 **[Approved]\(承認済み\)** 、 **[Expired]\(期限切れ\)** 、または **[Alert]\(アラート\)** を割り当てます。

* **Definition (定義)** : 用語の定義を入力します。

* **Acronym (頭字語)** : 各単語の先頭文字を使用して用語の短縮版を入力します。

* **Synonyms (類義語)** : 定義が同じか似ている別の用語を選択します。

* **Related Terms (関連用語)** : 当該の用語に関連しているものの、定義は異なる他の用語を用語集から選択します。 たとえば、ビジネス用語に関連する技術用語やコード ネーム、特定の用語に関連付けられるべきその他の用語などが挙げられます。

用語集の用語は、次の手順に従って作成します。

1. Azure portal で Azure Purview リソースに移動し、 **[Open Purview Studio]\(Purview Studio を開く\)** を選択します。 Purview Studio のホーム ページに自動的に移動します。

1. 左ペインから **[Glossary]\(用語集\)** を選択し、 **[Glossary terms]\(用語集の用語\)** ページを開きます。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="[Glossary terms]\(用語集の用語\) ページのスクリーンショット。":::

1. **[New term]\(新しい用語\)**  >  **[System default]\(システムの既定値\)**  >  **[Continue]\(続行\)** の順に選択します。

1. **[New term]\(新しい用語\)** ページの **[Overview]\(概要\)** タブで、新しい用語の **[Name]\(名前\)** に「*Financial*」と入力します。

1. **[Definition]\(定義\)** に「*This term represents financial information for Contoso Inc.* 」と入力します。

1. **[Status]\(状態\)** ボックスの一覧で **[Approved]\(承認済み\)** を選択します。

1. 完了したら、 **[作成]** をクリックします。

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="用語集の用語を新規作成する方法を示すスクリーンショット。":::

## <a name="add-glossary-terms-to-an-asset"></a>用語集の用語を資産に追加する

1. [このチュートリアル シリーズのパート 1](tutorial-scan-data.md) で学んだ手順を使用して資産を見つけます (例: **Contoso_CashFlow_{N}.csv**)。

1. 資産の詳細ページで **[Edit]\(編集\)** を選択します。

1. **[Overview]\(概要\)** タブにある **[Glossary terms]\(用語集の用語\)** ボックスの一覧で **Financial** を選択し、 **[Save]\(保存\)** を選択します。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="用語集の用語を資産に追加する方法を示すスクリーンショット。":::

1. **[Overview]\(概要\)** タブの **[Glossary terms]\(用語集の用語\)** セクションに移動して、*Financial* という用語が資産に適用されていることを確認します。

## <a name="import-glossary-terms"></a>用語集の用語をインポートする

用語をインポートしたり、既存の用語を一括で更新したりするには、事前設定されたテンプレートを用語集にアップロードします。

この手順では、.csv ファイルを介して用語集の用語をインポートします。

1. *StarterKitTerms.csv* という名前のファイルの保存場所をメモします。これは、[このチュートリアル シリーズのパート 1](tutorial-scan-data.md) でダウンロードしたスタート キットに含まれています。

   このファイルには、データ資産と関係のある事前設定された用語のリストが含まれています。

1. インポートを開始するために、 **[Glossary]\(用語集\)** を選択し、 **[Import terms]\(用語のインポート\)** を選択します。

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="用語集の用語をインポートする方法を示すスクリーンショット。":::

1. **[Import terms]\(用語のインポート\)** ページで **[System default]\(システムの既定値\)** を選択し、 **[Continue]\(続行\)** を選択します。

1. **[Browse]\(参照\)** を選択して、ダウンロードした *StarterKitTerms.csv* の場所に移動し、 **[Open]\(開く\)** を選択します。

1. **[OK]** を選択して用語のインポートを開始します。

   インポートが完了すると、 **[Glossary terms]\(用語集の用語\)** ページに新しい用語が表示されます。

1. 新しくインポートされた各用語を見て、それらがどのように定義されているかを確認します。

## <a name="create-custom-term-templates"></a>カスタム用語テンプレートを作成する

このセクションでは、カスタム属性を使用してカスタム用語テンプレートを作成する方法と、テンプレートの CSV ファイルを使用してデータをインポートする方法について説明します。

既存のシステム用語テンプレートがいくつか用意されています。これは、 **[Glossary]\(用語集\)**  >  **[Manage term templates]\(用語テンプレートの管理\)**  >  **[System]\(システム\)** を順に選択することで表示できます。

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="システム用語テンプレート。":::

新しいカスタム用語テンプレートを作成するには、次の手順を実行します。

1. 左側のメニューから **[Glossary]\(用語集\)** を選択します。
1. **[Manage term templates]\(用語テンプレートの管理\)**  >  **[Custom]\(カスタム\)**  >  **[New term template]\(新しい用語テンプレート\)** を選択します。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="新しいカスタム用語テンプレートの作成。":::

**[New term template]\(新しい用語テンプレート\)** 画面で、次の手順を実行します。

1. **[Template name]\(テンプレート名\)** に「`Sensitivity level`」と入力します。
1. 必要な説明を入力します (例: `Indicates the level of sensitivity for this data.`)。
1. **[+ New attribute]\(+ 新しい属性\)** を選択して、属性を追加するためのダイアログを開きます。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="[new term template]\(新しい用語テンプレート\) 画面の開始。":::

1. **[New attribute]\(新しい属性\)** 画面に、次のパラメーターを入力します。

   |設定|推奨値|
   |---------|-----------|
   |属性名|is sensitive information|
   |フィールドの種類|dropdown|Single choice (単一選択)|
   |Mark as required (必須としてマーク)|このボックスをオンにします。|
   |+ Add a choice (+ 選択肢の追加)| "Yes" と "No" の 2 つの選択肢を追加します。 "Yes" と "No"。|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="新しい属性を追加する。":::

1. 前の手順を繰り返して、もう 1 つの属性を `can be shared externally` という名前で追加します。 両方の属性を追加したら、最後に **[作成]** を選択します。

## <a name="import-terms-from-a-template"></a>テンプレートから用語をインポートする

次に、先ほど作成した **Sensitivity level** テンプレートを使用して新しい用語をインポートします。 

1. **[Glossary terms]\(用語集の用語\)** 画面から **[Import terms]\(用語のインポート\)** を選択します。

1. **[Import terms]\(用語のインポート\)** 画面から **[Sensitivity level]** を選択します。 **[続行]** をクリックします。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="[Sensitivity level] を選択する。":::

1. **機密レベル** の用語テンプレートには、既定のシステム属性に加え、新しく追加した属性 (`can be shared externally` および `is sensitive information`) が含まれています。 **[Download a sample .CSV]\(サンプル .CSV のダウンロード\)** を選択します。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="サンプル CSV ファイルのダウンロード。":::

1. テンプレート ファイルが自動的にダウンロードされます。カスタムの属性を使用し、新しい用語集の用語を編集してアップロードできます。 ダウンロードした CSV ファイルを開きます。 新しい用語とそれらの適切な値を CSV ファイルに新しい行として追加します。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="CSV ファイルで用語を作成する。":::

   ファイルをアップロードすると、 **[Related Terms]\(関連用語\)** または **[Synonyms]\(類義語\)** の列に入力されている用語のうち、まだ存在しないものが作成されます。 それらは、**システムの既定値** テンプレートを使用して作成されます。

1. ファイルをアップロードするには、 **[Import terms]\(用語のインポート\)** 画面に戻って、 **[Select the complete .CSV file to upload]\(アップロード対象の完成した .CSV ファイルを選択してください\)** ボックスの横にある **[Browse]\(参照\)** を選択します。 表示されたダイアログ ボックスで自分のファイルを選択し、 **[OK]** を選択します。

1. これで、 **[Glossary terms]\(用語集の用語\)** 画面に新しい用語が表示されます。 一覧にある用語の名前をクリックすると、新しい用語の詳細を確認できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
>
> * 用語集の用語を作成する。
> * 用語集の用語を資産に追加する。
> * 用語集の用語をインポートする。

次の記事に進み、さまざまなカタログの分析情報について学習してください。

> [!div class="nextstepaction"]
> [Azure Purview の分析情報についての理解](concept-insights.md)
