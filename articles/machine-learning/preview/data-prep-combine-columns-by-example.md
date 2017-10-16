---
title: "Azure Machine Learning ワークベンチを使用した [Combine Columns by Example] (例による列の結合) 変換"
description: "[Combine Columns by Example] (例による列の結合) 変換の参照ドキュメント"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 3ca1710c969b9bc5a1f56dc53f52c706e1ed07cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="combine-columns-by-example-transformation"></a>[Combine Columns by Example] (例による列の結合) 変換
ユーザーはこの変換を使用して、複数の列から値を結合して新しい列を追加することができます。 ユーザーは、区切り記号を指定したり、結合された値の例を提示したりして、この変換を実行できます。 ユーザーが結合の例を提供すると、変換は、**[Derive Column by Example] (例による列の派生)** 変換で使用されているものと同じ **[By Example] (例による)** エンジンで処理されます。

## <a name="how-to-perform-this-transformation"></a>この変換を実行する方法

この変換を使用するには、次の手順を実行します。
1. 1 つの列に結合する 2 つ以上の列を選択します。 
2. **[Transforms] (変換)** メニューから **[Combine Column by Example (例による列の結合)]** を選択します。 または、選択した列のいずれかのヘッダーを右クリックして、コンテキスト メニューから **[Combine Column by Example (例による列の結合)]** を選択します。 変換エディターが開き、新しい列が、右端の選択した列の横に追加されます。 新しい列には、既定の区切り記号で区切られた、結合された値が含まれます。 選択した列は、列ヘッダーのチェック ボックスで識別できます。 選択された列の追加や削除は、このチェックボックスを使用して実行できます。
3. 新しく作成された列の結合された値を更新できます。 更新された値は、変換を説明する例として使用されます。
4. **[OK]** をクリックして、変換を受け入れます。

### <a name="transform-editor-advanced-mode"></a>変換エディター: 詳細モード

詳細モードでは、列の結合に関して、より詳細な操作を実行できます。 

**[Combine Columns by] (列の結合方法)** の下で **[Separator] (区切り記号)** を選択すると、ユーザーは、**[Separator] (区切り記号)** テキスト ボックスで文字列を指定できます。 **[Separator] (区切り記号)** テキスト ボックスからタブで移動して、データ グリッドで結果をプレビューします。 **[OK]** を押して、変換をコミットします。

**[Combine Columns by] (列の結合方法)** の下で **[Examples] (例)** を選択すると、ユーザーは結合した値の例を提供できます。 例としての行のレベルを上げるには、グリッド内で行をダブルクリックします。 レベルを上げた行に対して、テキスト ボックスに、予想される出力を入力します。 **[Separator] (区切り記号)** テキスト ボックスからタブで移動して、データ グリッドで結果をプレビューします。 **[OK]** を押して、変換をコミットします。 

ユーザーは、変換エディターでリンクをクリックして、**基本モード**と**詳細モード**を切り替えられます。

### <a name="editing-existing-transformation"></a>既存の変換の編集

ユーザーは、変換ステップの **[編集]** オプションを選択することにより、既存の **[Combine Column By Example] (例による列の結合)** 変換を編集することができます。 **[編集]** をクリックすると、変換エディターが**基本モード**で開きます。 ユーザーは、ヘッダー内のリンクをクリックして**詳細モード**に切り替えられます。 そこでは、変換の作成時に提供されたすべての例が表示されます。

## <a name="example-using-separators"></a>区切り記号の使用例

この例では、コンマの後に空白を続けたものが、*Street*、*City*、*State*、および *ZIP* の列を結合する区切り記号として使用されています。

|Street|City|状態|ZIP|分割|
|:----|:----|:----|:----|:----|
|16011 N.E. 36th Way|REDMOND|WA|98052|16011 N.E. 36th Way, REDMOND, WA, 98052|
|16021 N.E. 36th Way|REDMOND|WA|98052|16021 N.E. 36th Way, REDMOND, WA, 98052|
|16031 N.E. 36th Way|REDMOND|WA|98052|16031 N.E. 36th Way, REDMOND, WA, 98052|
|16041 N.E. 36th Way|REDMOND|WA|98052|16041 N.E. 36th Way, REDMOND, WA, 98052|
|16051 N.E. 36th Way|REDMOND|WA|98052|16051 N.E. 36th Way, REDMOND, WA, 98052|
|16061 N.E. 36th Way|REDMOND|WA|98052|16061 N.E. 36th Way, REDMOND, WA, 98052|
|3460 157th Avenue NE|REDMOND|WA|98052|3460 157th Avenue NE, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|3350 157th Ave N.E., REDMOND, WA, 98052|
|3240 157th Avenue N.E.|REDMOND|WA|98052|3240 157th Avenue N.E., REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>[By example] (例による) を使用した例

**太字**の値は例として提供されました。

|日付|月|年|Hour|[分]|秒|結合された列|
|:----|:----|:----|:----|:----|:----|:----|
|13|Oct|2016|15|01|23|**13-Oct-2016 15:01:23 PDT**|
|16|Oct|2016|16|22|33|16-Oct-2016 15:01:33 PDT|
|17|Oct|2016|12|43|12|17-Oct-2016 15:01:12 PDT|
|12|Nov|2016|14|22|44|12-Nov-2016 15:01:44 PDT|
|23|Nov|2016|01|52|45|23-Nov-2016 15:01:45 PDT|
|16|Jan|2017|22|34|56|16-Jan-2016 15:01:56 PDT|
|23|Mar|2017|01|55|25|23-Mar-2016 15:01:25 PDT|
|16|Apr|2017|11|34|36|16-Apr-2016 15:01:36 PDT|

