---
title: Azure Monitor ブックのドロップダウン パラメーター
description: ドロップダウン パラメーターを含む作成済みのブックやパラメーター化されたカスタム ブックを使用して、複雑なレポート作成を簡素化します
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d6fb4954ff616bccd9b237aedb3001b0a8d592dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717198"
---
# <a name="workbook-drop-down-parameters"></a>ブックのドロップダウン パラメーター

ドロップダウンを使用すると、ユーザーは既知のセットから 1 つ以上の入力値を収集できます (たとえば、アプリの要求の 1 つを選択します)。 ドロップダウンではユーザー フレンドリな方法でユーザーから任意の入力を収集できます。 ドロップダウンは、対話型のレポートでフィルター処理を有効にする場合に特に便利です。 

ドロップダウンを指定する最も簡単な方法は、パラメーター設定で静的リストを指定することです。 より興味深い方法は、KQL クエリを介してリストを動的に取得することです。 パラメーター設定では、単一選択と複数選択のどちらであるかも指定できます。さらに、複数選択の場合は、結果セットを書式設定する方法 (区切り記号や引用符など) を指定することもできます。

## <a name="creating-a-static-drop-down-parameter"></a>静的ドロップダウン パラメーターの作成

1. 編集モードの空白のブックを使用して開始します。
2. ブック内のリンクから _[Add parameters]\(パラメーターの追加\)_ を選択します。
3. 青い _[パラメーターの追加]_ ボタンをクリックします。
4. ポップアップ表示される新しいパラメーター ペインで、次のように入力します。
    1. Parameter name: `Environment` (パラメーター名: {2})
    2. [パラメーターの種類]\: [`Drop down`ドロップ ダウン]
    3. [必須ですか?]\: `checked`オン
    4. [複数選択を許可する]\: オフ
    5. [データの取得元]\: [クエリ]`JSON`
5. [JSON 入力] テキスト ブロックに、この JSON スニペットを挿入します。
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. 青い [クエリの実行] ボタンを押します。
7. ツール バーの [保存] を選択して、パラメーターを作成します。
8. Environment パラメーターは、3 つの値を含むドロップダウンになります。

    ![静的なドロップダウンの作成を示す画像](./media/workbooks-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>項目のグループを使用した静的ドロップダウンの作成

クエリ結果または JSON に "group" フィールドが含まれている場合は、ドロップダウンに値のグループが表示されます。 上記のサンプルに従ったうえで、代わりに次の JSON を使用します。

```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```

![グループ化されたドロップダウンの例を示す画像](./media/workbooks-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>動的ドロップダウン パラメーターの作成
1. 編集モードの空白のブックを使用して開始します。
2. ブック内のリンクから _[Add parameters]\(パラメーターの追加\)_ を選択します。
3. 青い _[パラメーターの追加]_ ボタンをクリックします。
4. ポップアップ表示される新しいパラメーター ペインで、次のように入力します。
    1. Parameter name: `RequestName` (パラメーター名: {2})
    2. [パラメーターの種類]\: [`Drop down`ドロップ ダウン]
    3. [必須ですか?]\: `checked`オン
    4. [複数選択を許可する]\: オフ
    5. [データの取得元]\: [クエリ]`Query`
5. [JSON 入力] テキスト ブロックに、この JSON スニペットを挿入します。

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. 青い [クエリの実行] ボタンを押します。
2. ツール バーの [保存] を選択して、パラメーターを作成します。
3. RequestName パラメーターは、アプリ内の全要求の名前を含むドロップダウンになります。

    ![動的なドロップダウンの作成を示す画像](./media/workbooks-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>ドロップダウン パラメーターの参照

### <a name="in-kql"></a>KQL 内
1. クエリ コントロールをブックに追加し、Application Insights リソースを選択します。
2. KQL エディターで、このスニペットを入力します

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. これにより、クエリの評価時間が次のように拡張されます。

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. クエリを実行し、結果を確認します。 必要に応じて、それをグラフとして表示します。

    ![KQL で参照されるドロップダウンを示す画像](./media/workbooks-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>パラメーター値、ラベル、選択、グループ
上記の動的ドロップダウン パラメーターで使用されるクエリは、ドロップダウンで忠実に表示される値のリストのみを返します。 しかし、別の表示名、またはこれらのいずれかを選択したい場合はどうしたらよいでしょうか。 ドロップダウン パラメーターでは、値、ラベル、選択、グループの列を介してこれを実現できます。

次のサンプルでは、表示名が絵文字でスタイル設定され、最初のものが選択され、操作名でグループ化されている、Application Insights の依存関係のリストを取得する方法を示しています。

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```

![値、ラベル、選択、グループのオプションが使用されているドロップダウン パラメーターを示す画像](./media/workbooks-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>ドロップダウン パラメーターのオプション
| パラメーター | 説明 | 例 |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | 選択された値 | GET fabrikamaccount |
| `{DependencyName:label}` | 選択されたラベル | 🌐 GET fabrikamaccount |
| `{DependencyName:value}` | 選択された値 | GET fabrikamaccount |

## <a name="multiple-selection"></a>複数選択
これまでの例では、ドロップダウンで 1 つの値のみを選択するようにパラメーターを明示的に設定しました。 ドロップダウン パラメーターでは、複数選択もサポートされています。これは、[複数選択を許可する] チェック ボックスをオンにするだけで有効にできます。 

ユーザーは、[区切り記号] と [次で引用] の設定を使用して、結果セットの形式を指定することもできます。 既定では、次の形式のコレクションとして値が返されます: 'a', 'b', 'c'。 また、選択肢の数を制限するオプションもあります。

パラメーターを参照する KQL は、結果の形式を処理できるように変更する必要があります。 これを有効にする最も一般的な方法は、`in` 演算子を使用することです。

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

複数選択のドロップダウンが動作している例を次に示します。

![複数選択のドロップダウン パラメーターを示す画像](./media/workbooks-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>次のステップ

* ブックの豊富な視覚化オプションの学習を[開始](./workbooks-overview.md#visualizations)します。
* ブック リソースへのアクセスを[制御](./workbooks-access-control.md)し、共有します。