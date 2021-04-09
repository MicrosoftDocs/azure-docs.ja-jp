---
title: ビジネス用語集の用語テンプレートを管理する方法
description: Azure Purview データ カタログにあるビジネス用語集の用語テンプレートを管理する方法について説明します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96551052"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>ビジネス用語集の用語テンプレートを管理する方法

Azure Purview を使用すると、データを強化するために重要である用語集を作成することができます。 Purview Data Catalog 用語集に追加される新しい各用語は、用語のフィールドを決定する用語テンプレートをベースとします。 この記事では、用語集の用語に関連付けることができる用語テンプレートとカスタム属性を作成する方法について説明します。

## <a name="manage-term-templates-and-custom-attributes"></a>用語テンプレートとカスタム属性を管理する

用語テンプレートを使用して、カスタム属性を作成し、それらをグループ化して、用語の作成時にテンプレートを適用することができます。 用語が作成されると、その用語に関連付けられているテンプレートを変更することはできません。

1. **[用語集の用語]** ページで、 **[Manage term templates]\(用語テンプレートの管理\)** を選択します。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="[用語集の用語] > [Manage term templates]\(用語テンプレートの管理\) ボタンのスクリーンショット。":::

2. ページには、システムとカスタムの属性の両方が表示されます。 **[カスタム]** タブを選択して、用語テンプレートを作成または編集します。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="[用語集の用語] > [Manage term templates]\(用語テンプレートの管理\) ページのスクリーンショット。":::

3. **[+ New term template]\(+ 新しい用語テンプレート\)** を選択して、テンプレートの名前と説明を入力します。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="[用語集の用語] > [Manage term templates]\(用語テンプレートの管理\)> [New term templates]\(新しい用語テンプレート\) のスクリーンショット":::

4. **[+ 新しい属性]** を選択して、用語テンプレートの新しいカスタム属性を作成します。 属性の名前と説明を入力します。 カスタム属性名は用語テンプレート内で一意である必要がありますが、テンプレート全体では同じ名前にすることも再利用することもできます。

   オプションの一覧 ( **[テキスト]** 、 **[単一選択]** 、 **[複数選択]** 、 **[日付]** ) からフィールドの型を選択します。 テキスト型のフィールドの場合、既定値の文字列を指定することもできます。  属性は **必須** としてマークすることもできます。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="[用語集の用語] > [新しい属性] ページのスクリーンショット。":::

5. すべてのカスタム属性を作成したら、 **[プレビュー]** を選択して、カスタム属性の順序を調整します。 カスタム属性を目的の順序にドラッグ アンド ドロップできます。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="[用語集の用語] > [Preview term template]\(用語テンプレートのプレビュー\) のスクリーンショット。":::

6. すべてのカスタム属性を定義したら、 **[作成]** を選択して、カスタム属性が含まれた用語テンプレートを作成します。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="[用語集の用語] > [New term template]\(新しい用語テンプレート\) -[作成] ボタンのスクリーンショット。":::

7. 既存のカスタム属性を期限切れとしてマークするには **[Mark as Expired]\(期限切れとしてとしてマークする\)** をチェックします。 一度期限切れとすると、その属性を再アクティブ化することはできません。 期限切れの属性は、既存の用語に対してグレー表示されます。 今後この用語テンプレートで作成する新しい用語には、期限切れとマークされている属性は表示されなくなります。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="期限切れとしてマークするための [用語集の用語] > [属性の編集] のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

次のチュートリアルを修了してください。[チュートリアル:用語集の用語の作成とインポート](tutorial-import-create-glossary-terms.md)
