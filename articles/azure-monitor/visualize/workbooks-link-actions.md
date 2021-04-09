---
title: Azure Monitor ブックのリンク アクション
description: Azure Monitor ブックでリンク アクションを使用する方法
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 2d1abe6d77dd4293dc70a07f3b8fd5d9da78d01e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100601155"
---
# <a name="link-actions"></a>リンク アクション

リンク アクションには、ブックのリンク手順、あるいは[グリッド](../visualize/workbooks-grid-visualizations.md)、[タイトル](../visualize/workbooks-tile-visualizations.md)、または[グラフ](../visualize/workbooks-graph-visualizations.md)の列設定を使用してアクセスすることができます。

## <a name="general-link-actions"></a>一般的なリンク アクション

| リンク アクション | クリック時のアクション |
|:------------- |:-------------|
| `Generic Details` | プロパティ グリッドのコンテキスト ビューに行の値を表示します。 |
| `Cell Details` | プロパティ グリッドのコンテキスト ビューにセルの値を表示します。 情報を含む動的な型がセルに含まれている場合に便利です (たとえば、場所、ロール インスタンスなどの要求プロパティを含む json)。 |
| `Url` | セルの値は有効な HTTP URL であることが求められます。セルは、新しいタブでその URL を開くリンクになります。|

## <a name="application-insights"></a>Application Insights

| リンク アクション | クリック時のアクション |
|:------------- |:-------------|
| `Custom Event Details` | セル内のカスタム イベント ID (`itemId`) を使用して、Application Insights 検索の詳細を開きます。 |
| `* Details` | 依存関係、例外、ページ ビュー、要求、トレースを除き、[カスタム イベントの詳細] に似ています。 |
| `Custom Event User Flows` | セル内のカスタム イベント名に基づいてピボットされた Application Insights User Flows エクスペリエンスを開きます。 |
| `* User Flows` | 例外、ページ ビューおよび要求を除き、カスタム イベント User Flows に似ています。 |
| `User Timeline` | セル内のユーザー ID (`user_Id`) を使用して、ユーザー タイムラインを開きます。 |
| `Session Timeline` | セル内の値に対して Application Insights 検索エクスペリエンスを開きます (たとえば、セル内の値が abc であればテキスト "abc" を検索します)。 |

上の表の `*` はワイルド カードを表します

## <a name="azure-resource"></a>Azure リソース

| リンク アクション | クリック時のアクション |
|:------------- |:-------------|
| `ARM Deployment` | Azure Resource Manager テンプレートをデプロイします。  この項目が選択されている場合、追加のフィールドが表示され、作成者は、開く Azure Resource Manager テンプレートやそのテンプレートのパラメーターなどを構成できます。「[Azure Resource Manager デプロイのリンク設定](#azure-resource-manager-deployment-link-settings)」を参照してください。 |
| `Create Alert Rule` | リソースのアラート ルールを作成します。  |
| `Custom View` | カスタム ビューを開きます。 この項目が選択されている場合、追加のフィールドが表示され、作成者はビューの拡張機能、ビュー名、およびビューを開くために使用されるすべてのパラメーターを構成できます。 [カスタム ビュー](#custom-view-link-settings)に関するセクションを参照してください。 |
| `Metrics` | メトリック ビューを開きます。  |
| `Resource overview` | セルのリソース ID 値に基づいて、ポータルでリソースのビューを開きます。 作成者は必要に応じて、リソース ビューで特定のメニュー項目を開く `submenu` 値を設定することもできます。 |
| `Workbook (template)` | ブック テンプレートを開きます。  この項目が選択されている場合、追加のフィールドが表示され、作成者はどのテンプレートを開くかなどを構成できます。  |

## <a name="link-settings"></a>リンク設定

リンク レンダラーを使用する場合は、次の設定が使用可能です。

![リンク設定のスクリーンショット](./media/workbooks-link-actions/link-settings.png)

| 設定 | 説明 |
|:------------- |:-------------|
| `View to open` | 作成者が、上に列挙されているアクションのいずれかを選択できるようにします。 |
| `Menu item` | [リソースの概要] が選択されている場合は、これがリソースの概要で開くメニュー項目となります。 これを使用すると、リソースの "概要" ではなく、アラートまたはアクティビティ ログを開くことができます。 メニュー項目の値は、Azure の `Resourcetype` ごとに異なります。|
| `Link label` | 指定されている場合は、この値がグリッド列に表示されます。 この値が指定されていない場合は、セルの値が表示されます。 ヒートマップやアイコンなどの別の値を表示する場合は、`Link` レンダラーを使用しないでください。代わりに、適切なレンダラーを使用し、[この項目をリンクにする]`Make this item a link` オプションを選択してください。 |
| `Open link in Context Blade` | 指定されている場合、リンクは、完全なビューとして開くのではなく、ウィンドウの右側にポップアップ "コンテキスト" ビューとして開かれます。 |

[この項目をリンクにする]`Make this item a link` オプションを使用する場合は、次の設定が使用可能です。

| 設定 | 説明 |
|:------------- |:-------------|
| `Link value comes from` | リンクを含むレンダラーとしてセルを表示する場合、このフィールドにより、リンクで使用される "リンク" 値の取得元となる場所が指定されます。これにより、作成者はグリッド内の他の列のドロップダウンから選択できるようになります。 たとえば、セルはヒートマップ値である可能性があるが、リンクを使用して行のリソース ID の [リソースの概要] を開く必要があるとします。 その場合は、`Resource Id` フィールドから取得するリンク値を設定します。
| `View to open` | 上記と同じ。 |
| `Menu item` | 上記と同じ。 |
| `Open link in Context Blade` | 上記と同じ。 |

## <a name="azure-resource-manager-deployment-link-settings"></a>Azure Resource Manager デプロイのリンク設定

選択されたリンクの種類が [ARM デプロイ]`ARM Deployment` である場合、作成者は、Azure Resource Manager デプロイを開くために追加の設定を指定する必要があります。 構成には主に 2 つのタブがあります。

### <a name="template-settings"></a>テンプレート設定

このセクションでは、テンプレートの取得元と、Azure Resource Manager デプロイの実行に使用されるパラメーターを定義します。

| source | 説明 |
|:------------- |:-------------|
|`Resource group id comes from` | リソース ID は、デプロイされたリソースを管理するために使用されます。 サブスクリプションは、デプロイされたリソースとコストを管理するために使用されます。 リソース グループは、すべてのリソースを整理および管理するフォルダーのように使用されます。 この値が指定されていない場合、デプロイは失敗します。 「[リンク元](#link-sources)」の `Cell`、`Column`、`Static Value`、または `Parameter` から選択します。|
|`ARM template URI from` | Azure Resource Manager テンプレート自体への URI。 テンプレートをデプロイするユーザーは、テンプレート URI にアクセスできる必要があります。 「[リンク元](#link-sources)」の `Cell`、`Column`、`Parameter`、または `Static Value` から選択します。 まず、[クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)を見てみましょう。|
|`ARM Template Parameters` | このセクションでは、上記に定義されているテンプレート URI に使用するテンプレート パラメーターを定義します。 これらのパラメーターは、テンプレートを実行ページにデプロイするために使用されます。 グリッドには、テンプレート URI に定義されている名前を使用してパラメーターを入力し、それを静的な空の値に設定するのに役立つ展開ツールバー ボタンが含まれています。 このオプションを使用できるのは、グリッドにパラメーターがなく、テンプレート URI が設定されている場合のみです。 下部のセクションは、パラメーターの出力がどのように見えるかをプレビューしたものです。 [更新] を選択して、現在の変更でプレビューを更新します。 通常、パラメーターは値ですが、参照は、ユーザーがアクセスできる keyvault シークレットを指している可能性があります。 <br/><br/> **テンプレート ビューアー ブレードの制限事項** - 参照パラメーターが正しくレンダリングされず、null または値として表示されるため、ユーザーはテンプレート ビューアー タブから参照パラメーターを正しくデプロイできません。|

![Azure Resource Manager テンプレート設定のスクリーンショット](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>UX 設定

このセクションでは、Azure Resource Manager デプロイを実行する前にユーザーに表示される内容を構成します。

| source | 説明 |
|:------------- |:-------------|
|`Title from` | 実行ビューで使用されるタイトル。 「[リンク元](#link-sources)」の `Cell`、`Column`、`Parameter`、または `Static Value` から選択します。|
|`Description from` | これは、テンプレートをデプロイするときにユーザーに役立つ説明を提供するために使用される markdown テキストです。 「[リンク元](#link-sources)」の `Cell`、`Column`、`Parameter`、または `Static Value` から選択します。 <br/><br/> **注:** `Static Value` が選択されている場合は、複数行のテキスト ボックスが表示されます。 このテキスト ボックスでは、`{paramName}` を使用してパラメーターを解決できます。 また、`{columnName_column}` のように列名の後に `_column` を追加することで、列をパラメーターとして扱うこともできます。 次の図の例では、`{VMName_column}` を記述して `VMName` 列を参照できます。 コロンの後の値は[パラメーター フォーマッタ](../visualize/workbooks-parameters.md#parameter-options)です。この場合は `value` です。|
|`Run button text from` | 実行ボタンで Azure Resource Manager テンプレートをデプロイするために使用されるラベル。 これは、Azure Resource Manager テンプレートのデプロイを開始するためにユーザーが選択するものです。|

![Azure Resource Manager UX 設定のスクリーンショット](./media/workbooks-link-actions/ux-settings.png)

これらの構成が設定された後、ユーザーがリンクを選択すると、UX 設定で説明されている UX を使用してビューが開きます。 ユーザーが [次からの実行ボタン テキスト]`Run button text from` を選択すると、「[テンプレート設定](#template-settings)」の値を使用して Azure Resource Manager テンプレートがデプロイされます。 [テンプレートの表示] を選択すると、ユーザーがデプロイ前にテンプレートとパラメーターを確認するためのユーザー用のテンプレート ビューアー タブが開きます。

![Azure Resource Manager の実行ビューのスクリーンショット](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>カスタム ビューのリンク設定

Azure portal でカスタム ビューを開くには、これを使用します。 すべての構成と設定を確認してください。 値が正しくないと、ポータルでエラーが発生するか、ビューを正しく開くことができません。 `Form` または `URL` を使用して設定を構成する 2 つの方法があります。

> [!NOTE]
> メニューを含むビューをコンテキスト タブで開くことはできません。メニューを含むビューがコンテキスト タブで開かれるように構成されている場合は、リンクが選択されたときにコンテキスト タブが表示されません。

### <a name="form"></a>フォーム

| source | 説明 |
|:------------- |:-------------|
|`Extension name` | ビューの名前をホストする拡張機能の名前。|
|`View name` | 開くビューの名前。|

#### <a name="view-inputs"></a>ビューの入力

入力にはグリッドと JSON の 2 種類があります。 シンプルなキーと値のタブ入力には `Grid` を使用し、入れ子になった JSON 入力を指定するには `JSON` を選択します。

- グリッド
    - `Parameter Name`:ビューの入力パラメーターの名前。
    - `Parameter Comes From`:ビュー パラメーターの値の取得元。 「[リンク元](#link-sources)」の `Cell`、`Column`、`Parameter`、または `Static Value` から選択します。
    > [!NOTE]
    > `Static Value` が選択されている場合は、テキスト ボックスで角かっこリンク `{paramName}` を使用してパラメーターを解決できます。 列は、`{columnName_column}` のように列名の後に `_column` を追加することで、パラメーター列として扱うことができます。

    - `Parameter Value`: `Parameter Comes From` に応じて、これは使用可能なパラメーター、列、または静的な値のドロップダウンとなります。

    ![フォームからのカスタム ビュー設定が示されている列設定の編集のスクリーンショット。](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - エディターで JSON 形式のタブ入力を指定します。 `Grid` モードと同様に、パラメーターの場合は `{paramName}`、列の場合は `{columnName_column}` を使用して、パラメーターと列を参照することができます。 [Show JSON Sample]\(JSON サンプルの表示\)`Show JSON Sample` を選択すると、ビュー入力に対して予想されるすべての解決済みのパラメーターと列の出力がユーザーに表示されます。

    ![JSON のビュー入力を使用した [open custom view settings]\(カスタム ビュー設定を開く\) を示すスクリーンショット。](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>[URL]

拡張機能、ビューの名前、およびビューを開くために必要なすべての入力を含むポータル URL を貼り付けます。 [Initialize Settings]\(設定の初期化\)`Initialize Settings` を選択した後、作成者がビュー入力のいずれかを追加、変更、削除するための `Form` が設定されます。

![URL からのカスタム ビュー設定が表示されている列設定を示すスクリーンショット。 ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>ブック (テンプレート) のリンク設定

選択されたリンクの種類が [ブック (テンプレート)]`Workbook (Template)` である場合、作成者は、正しいブック テンプレートを開くために追加の設定を指定する必要があります。 以下の設定には、各設定に適した値をグリッドで見つける方法に関するオプションがあります。

| 設定 | 説明 |
|:------------- |:-------------|
| `Workbook owner Resource Id` | これは、ブックを "所有する" Azure リソースのリソース ID です。 通常は、Application Insights リソース、または Log Analytics ワークスペースです。 Azure Monitor 内では、これはリテラル文字列の `"Azure Monitor"` である場合もあります。 ブックが保存されている場合、これがブックのリンク先となります。 |
| `Workbook resources` | ブックで使用される既定のリソースを指定する Azure リソース ID の配列。 たとえば、開いているテンプレートが仮想マシンのメトリックを示している場合、ここに表示される値は仮想マシンのリソース ID となります。  多くの場合、所有者とリソースは同じ設定値に設定されます。 |
| `Template Id` | 開くテンプレートの ID を指定します。 これがギャラリーのコミュニティ テンプレートである場合 (最も一般的なケース)、そのテンプレートへのパスの前に `Community-` を付けます。`Workbooks/Performance/Apdex` テンプレートの場合は `Community-Workbooks/Performance/Apdex` のようになります。 これが保存されているブックまたはテンプレートへのリンクである場合は、その項目の完全な Azure リソース ID となります。 |
| `Workbook Type` | 開くブック テンプレートの種類を指定します。 最も一般的なケースでは、`Default` または `Workbook` オプションを使って現在のブックの値を使用するようにします。 |
| `Gallery Type` | これにより、開くテンプレートの [ギャラリー] ビューに表示されるギャラリーの種類が指定されます。 最も一般的なケースでは、`Default` または `Workbook` オプションを使って現在のブックの値を使用するようにします。 |
|`Location comes from` | 特定のブック リソースを開く場合は、場所フィールドを指定する必要があります。 場所が指定されていない場合、ブックの内容の検索に時間がかかります。 場所がわかっている場合は、それを指定します。 場所がわからない場合、または特定の場所がないテンプレートを開く場合は、このフィールドを "既定" のままにします。|
|`Pass specific parameters to template` | テンプレートに特定のパラメーターを渡す場合に選択します。 選択されている場合、指定されたパラメーターのみがテンプレートに渡されます。それ以外の場合は、現在のブック内のすべてのパラメーターがテンプレートに渡されます。その場合、このパラメーター値を機能させるために、両方のブックでパラメーターの "*名前*" を同じにする必要があります。|
|`Workbook Template Parameters` | このセクションでは、ターゲット テンプレートに渡されるパラメーターを定義します。 名前は、ターゲット テンプレートのパラメーターの名前と一致している必要があります。 `Cell`、`Column`、`Parameter`、および `Static Value` から値を選択します。 ターゲット テンプレートにそのパラメーターを渡す場合は、名前と値を空にすることはできません。|

作成者は、上記の設定ごとに、リンクされたブックの値の取得元を選択する必要があります。 「[リンク元](#link-sources)」を参照してください

ブックのリンクが開くと、上記の設定で構成されたすべての値が新しいブック ビューに渡されます。

![ブックのリンク設定のスクリーンショット](./media/workbooks-link-actions/workbook-link-settings.png)

![ブック テンプレート パラメーターの設定のスクリーンショット](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>リンク元

| source | 説明 |
|:------------- |:-------------|
| `Cell` | これにより、グリッド内のそのセルの値がリンク値として使用されます |
| `Column` | 選択されている場合、作成者がグリッド内の別の列を選択できるように別のフィールドが表示されます。  その行の列の値がリンク値に使用されます。 これは一般的に、`Template Id` フィールドを `column` に設定し、グリッドの各行で異なるテンプレートを開けるようにするか、あるいは `Workbook resources` フィールドが Azure リソース ID を含む列に設定されている場合は、異なるリソースに対して同じブック テンプレートを開けるようにするために使用されます |
| `Parameter` | 選択されている場合、作成者がパラメーターを選択できるように別のフィールドが表示されます。 リンクがクリックされたときの値には、そのパラメーターの値が使用されます |
| `Static value` | 選択されている場合、リンク先のブックで使用される静的な値を作成者が入力できるように、別のフィールドが表示されます。 これは一般的に、グリッド内のすべての行で同じ値がフィールドに使われる場合に使用されます。 |
| `Step` | ブックの現在の手順で設定されている値を使用します。 これは、現在のブックではなく、"*クエリとメトリックの手順*" で使用されるものに、リンクされたブックのブック リソースを設定するクエリとメトリックの手順で共通です |
| `Workbook` | 現在のブックで設定されている値を使用します。 |
| `Default` | 値が指定されていない場合に使用される既定値を使用します。 これはギャラリーの種類に共通であり、既定のギャラリーは所有者リソースの種類によって設定されます |

## <a name="next-steps"></a>次のステップ

- ブック リソースへのアクセスを[制御](../visualize/workbooks-access-control.md)し、共有します。
- [ブックでグループ](../visualize/workbooks-groups.md)を使用する方法について説明します。