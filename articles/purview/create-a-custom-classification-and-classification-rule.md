---
title: カスタム分類と分類ルールの作成 (プレビュー)
description: Azure Purview で組織に固有のデータ資産のデータの種類を定義する、カスタム分類を作成する方法について説明します。
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/5/2021
ms.openlocfilehash: 2966618619aa40ed60c2f3d0fb2c8e080d34a016
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617048"
---
# <a name="custom-classifications-in-azure-purview"></a>Azure Purview でのカスタム分類

この記事では、組織に固有のデータ資産のデータの種類を定義する、カスタム分類を作成する方法について説明します。 また、データ資産全体で特定のデータを検索できるようにする、カスタム分類ルールの作成についても説明します。

## <a name="default-classifications"></a>既定の分類

Azure Purview Data Catalog には、データ資産に含まれている可能性がある一般的な個人データの種類を表す、既定の分類が多数用意されています。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="分類の選択" border="true":::

既定の分類がニーズに合わない場合は、カスタム分類を作成することもできます。

## <a name="steps-to-create-a-custom-classification"></a>カスタム分類の作成手順

カスタム分類を作成するには、次の手順を実行します。

1. カタログで、左側のメニューから **[管理センター]** を選択します。

2. **[メタデータ管理]** の下にある **[分類]** を選択します。

3. **[+新規]** を選択します

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="新しい分類" border="true":::

**[Add new classification]\(新しい分類の追加\)** ウィンドウが開き、分類の名前と説明を入力できます。 `your company name.classification name` など、名前空間の規則を使用することをお勧めします。

Microsoft のシステム分類は、予約済みの `MICROSOFT.` 名前空間にグループ化されます。 たとえば、**MICROSOFT.GOVERNMENT.US.SOCIAL\_SECURITY\_NUMBER** です。

分類の名前は、文字で始まり、その後に一連の文字、数字、およびピリオド (.) またはアンダースコアが続く必要があります。 スペースは使用できません。 入力すると、UX によって自動的にフレンドリ名が生成されます。 このフレンドリ名は、カタログ内の資産に適用するとユーザーに表示されます。

名前を短くするため、次のロジックに基づいてフレンドリ名が生成されます。

- 名前空間の最後の 2 つのセグメント以外はすべてトリミングされます。

- 大文字と小文字の区別は、各単語の最初の文字が大文字になるように調整されます。 その他の文字はすべて小文字に変換されます。

- すべてのアンダースコア (\_) はスペースで置き換えられます。

たとえば、分類の名前を **CONTOSO.HR.EMPLOYEE\_ID** と指定すると、フレンドり名として **Hr.Employee ID** がシステムに格納されます。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

**[OK]** を選択すると、新しい分類が分類のリストに追加されます。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="カスタム分類" border="true":::

一覧で分類を選択すると、分類の詳細ページが開きます。 ここで、分類に関するすべての詳細を確認できます。

これらの詳細には、存在するインスタンスの数、正式な名前、関連付けられた分類ルール (存在する場合)、所有者名が含まれます。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="分類の選択" border="true":::

## <a name="custom-classification-rules"></a>カスタム分類ルール

カタログ サービスには、特定のデータの種類を自動的に検出するためにスキャナーによって使用される、既定の分類ルールのセットが用意されています。 また、独自のカスタム分類ルールを追加して、データ資産全体で検索する意味がある、他の種類のデータを検出することもできます。 この機能は、データ資産内でデータを検索する際に、非常に役に立ちます。

たとえば、Contoso という名前の会社に、\"Employee\" という単語の後に GUID を付けて EMPLOYEE{GUID} とするように会社全体で標準化された従業員 ID があるとします。 たとえば、従業員 ID の 1 つのインスタンスは、`EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55` のようになります。

Contoso は、カスタム分類ルールを作成することによって、これらの ID のインスタンスを検出するように、スキャン システムを構成できます。 データ パターンに一致する正規表現を指定できます (この場合は `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$`)。 また、通常は名前がわかっている列 (Employee\_ID や EmployeeID など) にデータが格納されている場合は、列パターンの正規表現を追加することで、スキャンの精度を高めることもできます。 正規表現の例として、Employee\_ID\|EmployeeID があります。

スキャン システムはこのルールを使用して、列内の実際のデータと列名を調べ、従業員 ID パターンが見つかったすべてのインスタンスを特定します。

## <a name="steps-to-create-a-custom-classification-rule"></a>カスタム分類ルールの作成手順

カスタム分類ルールを作成するには、次の手順を実行します。

1. 前のセクションの手順に従って、カスタム分類を作成します。 分類ルールの構成にこのカスタム分類を追加して、列内で一致が検出されたときにシステムによって適用されるようにします。

2. **管理センター** のアイコンを選択します。

3. **[Classifications rules]\(分類ルール\)** セクションを選択します。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="分類ルール タイル" border="true":::

4. **[新規]** を選択します。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="新しい分類ルールの追加" border="true":::

5. **[New classification rule]\(新しい分類ルール\)** ダイアログ ボックスが表示されます。 フィールドに入力し、**正規表現ルール** と **辞書ルール** のどちらを作成するかを決定します。

   |フィールド     |説明  |
   |---------|---------|
   |名前   |    必須。 最大で 100 文字入力できます。    |
   |説明      |省略可能。 最大で 256 文字入力できます。    |
   |［更新の分類］    | 必須。 分類の名前をドロップダウン リストから選択して、一致が見つかった場合にスキャナーがそれを適用できるようにします。        |
   |State   |  必須。 オプションが有効または無効になっています。 既定では有効になります。    |

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="新しい分類ルールの作成" border="true":::

### <a name="creating-a-regular-expression-rule"></a>正規表現ルールを作成する

1. 正規表現ルールを作成する場合、次の画面が表示されます。 必要に応じ、ルールに対して **提案された正規表現パターンを生成** するために使用されるファイルをアップロードすることもできます。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="新しい正規表現ルールの作成" border="true":::

1. 提案された正規表現パターンを生成する場合は、ファイルをアップロードした後で、提案されたパターンのいずれかを選択し、 **[Add to Patterns]\(パターンに追加\)** をクリックして、提案されたデータと列のパターンを使用します。 推奨されるパターンを調整することも、ファイルをアップロードせずに独自のパターンを入力することもできます。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="提案された正規表現の生成" border="true":::

   |フィールド     |説明  |
   |---------|---------|
   |データ パターン    |省略可能。 データ フィールドに格納されているデータを表す正規表現です。 制限は非常に大きく設定されています。 前の例では、従業員 ID のデータ パターン テストは `Employee{GUID}` というワードでした。  |
   |列パターン    |省略可能。 一致する列名を表す正規表現です。 制限は非常に大きく設定されています。 |

1. **[Data Pattern]\(データ パターン\)** には、次の 2 つのしきい値を設定できます。

   - **[Distinct match threshold]\(個別の一致のしきい値\)** : スキャナーがデータ パターンを実行する前に、列に含まれている必要がある個別のデータ値の合計数です。 推奨値は 8 です。 この値は、2 から 32 の範囲で手動で調整できます。 システムでは、この値を使用して、スキャナーが正確に分類するのに十分なデータが列に含まれていることを確認する必要があります。 たとえば、すべて値 1 が含まれている複数の行を含む列は分類されません。 1 つの行に値が含まれ、残りの行に null 値が含まれている列の場合も分類されません。 複数のパターンを指定すると、この値がそれぞれに適用されます。

   - **[Minimum match threshold]\(最小の一致のしきい値\)** : この設定を使用して、分類を適用するためにスキャナーによって検出される必要がある列の、個別のデータ値の最小一致率を設定できます。 推奨値は 60% です。 この設定には注意する必要があります。 レベルを 60% 未満に減らすと、誤検知の分類がカタログに導入される可能性があります。 複数のデータ パターンを指定した場合、この設定は無効になり、値は 60% で固定されます。

1. これで、ルールを検証して **作成** できるようになりました。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/verify-rule.png" alt-text="ルールを作成前に検証" border="true":::

### <a name="creating-a-dictionary-rule"></a>辞書ルールを作成する

1. 辞書ルールを作成する場合は、次の画面が表示されます。 作成する分類に使用できるすべての値を含むファイルを 1 つの列にアップロードします。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="辞書ルールの作成" border="true":::

1. 辞書が生成された後、個別の一致と最小一致のしきい値を調整して、ルールを送信することができます。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Purview 辞書ルール - 個別の一致しきい値と最小一致しきい値の調整" border="true":::

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="辞書が生成されたというチェックマークがある、辞書ルールの作成。" border="true":::

## <a name="next-steps"></a>次のステップ

分類ルールが作成されたので、スキャンの実行時にこのルールが使用されるように、スキャン ルール セットに追加できます。 詳細については、「[スキャン ルール セットを作成する](create-a-scan-rule-set.md)」を参照してください。
