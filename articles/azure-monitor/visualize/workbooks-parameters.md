---
title: Azure Monitor ブックの作成パラメーター
description: ブックの作成者がパラメーターを使用してコンシューマーからの入力を収集し、それをブックの他の部分で参照できるようにする方法について説明します。
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 95e810373ac5a591c530004e8d1175c3300656f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700560"
---
# <a name="workbook-parameters"></a>ブックのパラメーター

ブックの作成者は、パラメーターを使用してコンシューマーからの入力を収集し、それをブックの他の部分で参照できるようにします。通常は、結果セットの範囲を指定したり、適切なビジュアルを設定したりするために使用します。 これは、作成者が対話型のレポートやエクスペリエンスをビルドできるようにする、重要な機能です。 

ブックを使用すると、パラメーターのコントロールがコンシューマーにどのように表示されるか (テキスト ボックスかドロップ ダウンか、単一選択か複数選択か、テキスト、JSON、KQL、Azure Resource Graph からの値など) を制御できます。  

サポートされているパラメーターの種類には、次のものが含まれます。
* [時間](workbooks-time.md) - ユーザーが事前設定された時間の範囲から選択またはカスタム範囲を選択することを許可する
* [ドロップ ダウン](workbooks-dropdowns.md) - ユーザーが値または値のセットから選択することを許可する
* [テキスト](workbooks-text.md) - ユーザーが任意のテキストを入力することを許可する
* [リソース](workbooks-resources.md) - ユーザーが 1 つ以上の Azure リソースを選択することを許可する
* [サブスクリプション](workbooks-resources.md) - ユーザーが 1 つ以上の Azure サブスクリプション リソースを選択することを許可する
* リソースの種類 - ユーザーが 1 つ以上の Azure リソースの種類の値を選択することを許可する
* 場所 - ユーザーが 1 つ以上の Azure の場所の値を選択することを許可する

これらのパラメーター値は、バインドまたは値の展開を介して、ブックの他の部分で参照できます。

## <a name="creating-a-parameter"></a>パラメーターの作成
1. 編集モードの空白のブックを使用して開始します。
2. ブック内のリンクから _[Add parameters]\(パラメーターの追加\)_ を選択します。
3. 青い _[パラメーターの追加]_ ボタンをクリックします。
4. ポップアップ表示される新しいパラメーター ペインで、次のように入力します。
    1. [パラメーター名]\: `TimeRange` " *(パラメーター __名__ にスペースや特殊文字を含めることは **できません**)* "
    2. [表示名]\: `Time Range` " *(ただし、__表示名__ にはスペース、特殊文字、絵文字などを含めることができます)* "
    2. [パラメーターの種類]\: [`Time range picker`ドロップ ダウン]
    3. [必須ですか?]\: `checked`オン
    4. [使用可能な時間の範囲]\:[過去 1 時間]、[過去 12 時間]、[過去 24 時間]、[過去 48 時間]、[過去 3 日間]、[過去 7 日間]、[ユーザー設定の時間範囲の選択を許可する]
5. ツール バーの [保存] を選択して、パラメーターを作成します。

   ![時間範囲パラメーターの作成を示す画像](./media/workbooks-parameters/time-settings.png)

[ピル] スタイルで、ブックが読み取りモードでどのように表示されるかを次に示します。

   ![読み取りモードの時間範囲パラメーターを示す画像](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>パラメーターの参照
### <a name="via-bindings"></a>バインド経由
1. クエリ コントロールをブックに追加し、Application Insights リソースを選択します。
2. _[時間範囲]_ ドロップ ダウンを開き、下部の [パラメーター] セクションから [`Time Range`] を選択します。
3. これにより、時間範囲パラメーターがグラフの時間範囲にバインドされます。 これで、サンプル クエリの時間範囲は [過去 24 時間] になりました。
4. クエリを実行して結果を確認します。

    ![バインドを介して参照される時間範囲パラメーターを示す画像](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>KQL 内
1. クエリ コントロールをブックに追加し、Application Insights リソースを選択します。
2. KQL で、パラメーター `| where timestamp {TimeRange}` を使用して時間スコープ フィルターを入力します。
3. これにより、クエリの評価時間が `| where timestamp > ago(1d)` に拡張されます。これは、パラメーターの時間範囲の値です。
4. クエリを実行して結果を確認します。

    ![KQL で参照される時間範囲を示す画像](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>テキスト内 
1. テキスト コントロールをブックに追加します。
2. マークダウンで、「`The chosen time range is {TimeRange:label}`」と入力します。
3. _[編集完了]_ を選択します
4. テキスト コントロールに、"_The chosen time range is Last 24 hours\(選択された時間範囲は [過去 24 時間] です\)_ " というテキストが表示されます。

## <a name="parameter-options"></a>パラメーターのオプション
「_テキスト内_」セクションでは、パラメーター値の代わりにそのパラメーターの `label` を使用しました。 パラメーターでは、その種類に応じて、さまざまなオプションが表示されます。たとえば、時間範囲ピッカーでは値、ラベル、クエリ、開始時間、終了時間、グレインなどのオプションがあります。

対象のパラメーターの拡張オプションを確認するには、次の _[パラメーターの編集]_ ペインの [`Previews`] セクションを使用します。

![時間範囲パラメーターのオプションを示す画像](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>次のステップ

* ブックの豊富な視覚化オプションの学習を[開始](./workbooks-overview.md#visualizations)します。
* ブック リソースへのアクセスを[制御](./workbooks-access-control.md)し、共有します。