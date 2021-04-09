---
title: Azure Monitor ブックの時間パラメーター
description: ユーザーが分析の時間コンテキストを設定できるように時間パラメーターを設定する方法について説明します。 時間パラメーターは、ほぼすべてのレポートで使用されます。
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 9f4f5f82423f0ecb4e810eab2d675e95b8b66763
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717164"
---
# <a name="workbook-time-parameters"></a>ブックの時間パラメーター

時間パラメーターを使用すると、ユーザーは、分析の時間コンテキストを設定できます。これは、ほぼすべてのレポートで使用されます。 これを設定して使用するのは非常に簡単です。作成者は、ユーザー設定の時間範囲のオプションを含め、ドロップダウンに表示する時間範囲を指定することができます。 

## <a name="creating-a-time-parameter"></a>時間パラメーターの作成
1. 編集モードの空白のブックを使用して開始します。
2. ブック内のリンクから _[Add parameters]\(パラメーターの追加\)_ を選択します。
3. 青い _[パラメーターの追加]_ ボタンをクリックします。
4. ポップアップ表示される新しいパラメーター ペインで、次のように入力します。
    1. Parameter name: `TimeRange` (パラメーター名: {2})
    2. [パラメーターの種類]\: [`Time range picker`ドロップ ダウン]
    3. [必須ですか?]\: `checked`オン
    4. [使用可能な時間の範囲]\:[過去 1 時間]、[過去 12 時間]、[過去 24 時間]、[過去 48 時間]、[過去 3 日間]、[過去 7 日間]、[ユーザー設定の時間範囲の選択を許可する]
5. ツール バーの [保存] を選択して、パラメーターを作成します。

    ![時間範囲パラメーターの作成を示す画像](./media/workbooks-time/time-settings.png)

ブックが読み取りモードでどのように表示されるかを次に示します。

![読み取りモードの時間範囲パラメーターを示す画像](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>時間パラメーターの参照
### <a name="via-bindings"></a>バインド経由
1. クエリ コントロールをブックに追加し、Application Insights リソースを選択します。
2. ほとんどのブック コントロールは _[Time Range]\(時間範囲\)_ スコープ ピッカーをサポートしています。 _[Time Range]\(時間範囲\)_ ドロップダウンを開き、下部の時間範囲パラメーター グループで [`{TimeRange}`] を選択します。
3. これにより、時間範囲パラメーターがグラフの時間範囲にバインドされます。 これで、サンプル クエリの時間範囲は [過去 24 時間] になりました。
4. クエリを実行して結果を確認します。

    ![バインドを介して参照される時間範囲パラメーターを示す画像](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>KQL 内
1. クエリ コントロールをブックに追加し、Application Insights リソースを選択します。
2. KQL で、パラメーター `| where timestamp {TimeRange}` を使用して時間スコープ フィルターを入力します。
3. これにより、クエリの評価時間が `| where timestamp > ago(1d)` に拡張されます。これは、パラメーターの時間範囲の値です。
4. クエリを実行して結果を確認します。

    ![KQL で参照される時間範囲を示す画像](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>テキスト内 
1. テキスト コントロールをブックに追加します。
2. マークダウンで、「`The chosen time range is {TimeRange:label}`」と入力します。
3. _[編集完了]_ を選択します
4. テキスト コントロールに、"_The chosen time range is Last 24 hours\(選択された時間範囲は [過去 24 時間] です\)_ " というテキストが表示されます。

## <a name="time-parameter-options"></a>時間パラメーターのオプション
| パラメーター | 説明 | 例 |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | 時間範囲のラベル | 過去 24 時間 |
| `{TimeRange:label}` | 時間範囲のラベル | 過去 24 時間 |
| `{TimeRange:value}` | 時間範囲の値 | > ago(1d) |
| `{TimeRange:query}` | 時間範囲のクエリ | > ago(1d) |
| `{TimeRange:start}` | 時間範囲の開始時刻 | 3/20/2019 4:18 PM |
| `{TimeRange:end}` | 時間範囲の終了時刻 | 3/21/2019 4:18 PM |
| `{TimeRange:grain}` | 時間範囲の粒度 | 30 m |


### <a name="using-parameter-options-in-a-query"></a>クエリでのパラメーター オプションの使用
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>次のステップ

* ブックの豊富な視覚化オプションの学習を[開始](./workbooks-overview.md#visualizations)します。
* ブック リソースへのアクセスを[制御](./workbooks-access-control.md)し、共有します。