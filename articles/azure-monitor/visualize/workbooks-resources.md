---
title: Azure Monitor ブックのリソース パラメーター
description: リソース パラメーターを使用し、ブック内でリソースを選択できるようにする方法について説明します。 リソース パラメーターを使用し、データの取得元にするスコープを設定します。
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d4a6d04f88544b3a4f9f9c0c0b290d17f5ef148d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725579"
---
# <a name="workbook-resource-parameters"></a>ブックのリソース パラメーター

リソース パラメーターを使用すると、ブック内のリソースを選択できます。 これは、データの取得元の範囲を設定するのに便利です。 例として、ユーザーに VM のセットを選択することを許可することが挙げられます。このセットは、後にデータを表示するためにグラフによって使用されます。

リソース ピッカーの値は、ブックのコンテキスト、静的リスト、または Azure Resource Graph のクエリから取得できます。

## <a name="creating-a-resource-parameter-workbook-resources"></a>リソース パラメーターの作成 (ブック リソース)
1. 編集モードの空白のブックを使用して開始します。
2. ブック内のリンクから _[Add parameters]\(パラメーターの追加\)_ を選択します。
3. 青い _[パラメーターの追加]_ ボタンをクリックします。
4. ポップアップ表示される新しいパラメーター ペインで、次のように入力します。
    1. Parameter name: `Applications` (パラメーター名: {2})
    2. [パラメーターの種類]\: [`Resource picker`ドロップ ダウン]
    3. [必須ですか?]\: `checked`オン
    4. [Allow multiple selections]\(複数選択を許可する\): `checked`
5. [データの取得元]\: [クエリ]`Workbook Resources`
6. [リソースの種類のみを含める]\: `Application Insights`
7. ツール バーの [保存] を選択して、パラメーターを作成します。

![ブック リソースを使用したリソース パラメーターの作成を示す画像](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>リソース パラメーターの作成 (Azure Resource Graph)
1. 編集モードの空白のブックを使用して開始します。
2. ブック内のリンクから _[Add parameters]\(パラメーターの追加\)_ を選択します。
3. 青い _[パラメーターの追加]_ ボタンをクリックします。
4. ポップアップ表示される新しいパラメーター ペインで、次のように入力します。
    1. Parameter name: `Applications` (パラメーター名: {2})
    2. [パラメーターの種類]\: [`Resource picker`ドロップ ダウン]
    3. [必須ですか?]\: `checked`オン
    4. [Allow multiple selections]\(複数選択を許可する\): `checked`
5. [データの取得元]\: [クエリ]`Query`
    1. [クエリの種類]\: [`Azure Resource Graph`]
    2. [サブスクリプション]\: [`Use default subscriptions`]
    3. クエリ コントロールで、次のスニペットを追加します。
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. ツール バーの [保存] を選択して、パラメーターを作成します。

![Azure Resource Graph を使用したリソース パラメーターの作成を示す画像](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure Resource Graph は、まだすべてのクラウドで使用できるわけではありません。 この方法を選択する場合は、ターゲット クラウドでサポートされていることを確認してください。

[Azure Resource Graph のドキュメント](../../governance/resource-graph/overview.md)

## <a name="creating-a-resource-parameter--json-list"></a>リソース パラメーターの作成 (JSON リスト)
1. 編集モードの空白のブックを使用して開始します。
2. ブック内のリンクから _[Add parameters]\(パラメーターの追加\)_ を選択します。
3. 青い _[パラメーターの追加]_ ボタンをクリックします。
4. ポップアップ表示される新しいパラメーター ペインで、次のように入力します。
    1. Parameter name: `Applications` (パラメーター名: {2})
    2. [パラメーターの種類]\: [`Resource picker`ドロップ ダウン]
    3. [必須ですか?]\: `checked`オン
    4. [Allow multiple selections]\(複数選択を許可する\): `checked`
5. [データの取得元]\: [クエリ]`JSON`
    1. コンテンツ コントロールで、次の JSON スニペットを追加します。
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. 青い _[更新]_ ボタンをクリックします。
6. オプションで、[`Include only resource types`] を _[Application Insights]_ に設定します。
7. ツール バーの [保存] を選択して、パラメーターを作成します。

## <a name="referencing-a-resource-parameter"></a>リソース パラメーターの参照
1. クエリ コントロールをブックに追加し、Application Insights リソースを選択します。
2. _[Application Insights]_ ドロップ ダウンを使用して、パラメーターをコントロールにバインドします。 これにより、クエリの範囲が、実行時にパラメーターによって返されるリソースに設定されます。
4. KQL コントロールで、次のスニペットを追加します。
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. クエリを実行し、結果を確認します。 

![クエリ コントロールで参照されているリソース パラメーターを示す画像](./media/workbooks-resources/resource-reference.png)

> この方法を使用すると、リソースをメトリックなどの他のコントロールにバインドできます。

## <a name="resource-parameter-options"></a>リソース パラメーターのオプション
| パラメーター | 説明 | 例 |
| ------------- |:-------------|:-------------|
| `{Applications}` | 選択されたリソース ID | _/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication_ |
| `{Applications:label}` | 選択されたリソースのラベル | `acmefrontend` |
| `{Applications:value}` | 選択されたリソースの値 | _'/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication'_ |
| `{Applications:name}` | 選択されたリソースの名前 | `acmefrontend` |
| `{Applications:resourceGroup}` | 選択されたリソースのリソース グループ | `acmegroup` |
| `{Applications:resourceType}` | 選択されたリソースの種類 | _microsoft.insights/components_ |
| `{Applications:subscription}` | 選択されたリソースのサブスクリプション |  |
| `{Applications:grid}` | リソースのプロパティを示すグリッド。 デバッグ中にテキスト ブロックでレンダリングする場合に便利です。  |  |

## <a name="next-steps"></a>次のステップ

* ブックの豊富な視覚化オプションの学習を[開始](./workbooks-overview.md#visualizations)します。
* ブック リソースへのアクセスを[制御](./workbooks-access-control.md)し、共有します。