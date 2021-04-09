---
title: Azure Security Center の資産インベントリ
description: Security Center で監視されているすべてのリソースを完全に可視化する Azure Security Center の資産管理エクスペリエンスについて説明します。
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 02/10/2021
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 873fdba1d24db55b3269cc2c13f0140da4a9b4e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393355"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory"></a>資産インベントリを使用してリソースの調査と管理を行う

Azure Security Center の資産インベントリ ページを使用すると、Security Center に接続したリソースのセキュリティ態勢が 1 つのページに表示されます。 

Security Center では、Azure リソースのセキュリティの状態が定期的に分析されて、潜在的なセキュリティ脆弱性が特定されます。 その後、これらの脆弱性を修正する方法に関する推奨事項が提供されます。

いずれかのリソースに未処理のレコメンデーションがある場合は、インベントリに表示されます。

このビューとそのフィルターを使用して、次のような質問に対処できます。

- Azure Defender が有効なサブスクリプションのうち、未処理の推奨事項があるものはどれか
- 「Production」というタグが付いているマシンのうち、Log Analytics エージェントがないものはどれか
- 特定のタグが付いているマシンのうち、未処理の推奨事項があるマシンは何台か
- 特定のリソース グループ内のリソースのうち、何個のリソースに脆弱性評価サービスによるセキュリティの調査結果があるか

このツールによる資産管理には大きな可能性があり、その範囲は拡大し続けています。 

> [!TIP]
> 資産インベントリ ページのセキュリティに関する推奨事項は、**推奨事項** に関するページのセキュリティに関する推奨事項と同じですが、ここでは影響を受けるリソースに応じて示しています。 推奨事項の解決方法の詳細については、[Azure Security Center でのセキュリティに関する推奨事項の実装](security-center-recommendations.md)に関するページを参照してください。


## <a name="availability"></a>可用性
|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|Free|
|必要なロールとアクセス許可:|すべてのユーザー|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>資産インベントリの主な機能
インベントリ ページには次のツールが含まれています。

:::image type="content" source="media/asset-inventory/highlights-of-inventory.png" alt-text="Azure Security Center の資産インベントリ ページの主な機能" lightbox="media/asset-inventory/highlights-of-inventory.png":::


### <a name="1---summaries"></a>1 - 概要
フィルターを定義する前に、インベントリ ビューの上部に以下の値のストリップが目立つように表示されます。

- **リソースの合計**:Security Center に接続されているリソースの合計数。
- **異常なリソース**:アクティブなセキュリティのレコメンデーションがあるリソース。 セキュリティのレコメンデーションの詳細については、[こちら](security-center-recommendations.md)をご覧ください。
- **監視されていないリソース**:エージェントの監視に関して問題があるリソースです。Log Analytics エージェントがデプロイされていますが、エージェントがデータを送信していないか、正常性に関する他の問題が発生しています。
- **未登録のサブスクリプション**: Azure Security Center にまだ接続されていない選択したスコープ内のサブスクリプション。

### <a name="2---filters"></a>2 - フィルター
ページの上部にある複数のフィルターを使用すると、質問に回答しようとすることによって、リソースの一覧をすばやく絞り込むことができます。 たとえば、" *'Production' というタグが付いているマシンのうち、Log Analytics エージェントがないものはどれですか?* " という質問に回答する場合は、 **[エージェント監視]** フィルターと **[タグ]** フィルターを組み合わせることができます。

フィルターを適用すると、すぐに概要の値がクエリ結果に関連付けて更新されます。 

### <a name="3---export-and-asset-management-tools"></a>3 - エクスポートと資産管理ツール

**エクスポート オプション** - インベントリには、選択したフィルター オプションの結果を CSV ファイルにエクスポートするオプションが含まれます。 クエリ自体を Azure Resource Graph エクスプローラーにエクスポートして、Kusto クエリ言語 (KQL) クエリを絞り込んだり、保存や変更を行ったりすることもできます。

> [!TIP]
> KQL のドキュメントでは、いくつかのサンプル データと単純なクエリを組み合わせたデータベースを使用して、この言語の「感触」をつかむことができます。 詳しくは、[KQL のチュートリアル](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)をご覧ください。

**[資産管理オプション]** - インベントリでは、複雑な検出クエリを実行できます。 クエリに一致するリソースが見つかると、インベントリに次のような操作のショートカットが表示されます。

- フィルター処理されたリソースにタグを割り当てる - タグを付けるリソースの横にあるチェック ボックスをオンにします。
- Security Center に新しいサーバーをオンボードする - **[非 Azure サーバーの追加]** ツールバー ボタンを使用します。
- Azure Logic Apps を使用してワークロードを自動化する - **[ロジック アプリのトリガー]** ボタンを使用して、1 つ以上のリソースでロジック アプリを実行します。 ロジック アプリを事前に準備し、関連するトリガーの種類 (HTTP 要求) を受け入れる必要があります。 [Logic Apps の詳細をご覧ください](../logic-apps/logic-apps-overview.md)。


## <a name="how-does-asset-inventory-work"></a>資産インベントリのしくみ

資産インベントリは、[Azure Resource Graph (ARG)](../governance/resource-graph/index.yml) を利用しています。ARG は、複数のサブスクリプションにまたがって Security Center のセキュリティ態勢データのクエリを実行できる Azure サービスです。

ARG は、大規模なクエリの実行機能によってリソースを効率的に探索できるように設計されています。

資産インベントリで [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/) を使用すると、他のリソース プロパティで ASC データを相互参照して詳細な分析情報をすばやく作成できます。


## <a name="how-to-use-asset-inventory"></a>資産インベントリの使用方法

1. Security Center のサイドバーで、 **[インベントリ]** を選択します。

1. **[名前でフィルター]** ボックスを使用して特定のリソースを表示するか、下記のようにフィルターを使用します。

1. フィルターの関連するオプションを選択して、実行する特定のクエリを作成します。

    既定では、リソースはアクティブなセキュリティのレコメンデーションの数によって並べ替えられます。

    > [!IMPORTANT]
    > 各フィルターのオプションは、現在選択されているサブスクリプション内のリソース **および** 他のフィルターで選択した項目に固有のものです。
    >
    > たとえば、サブスクリプションを 1 つだけ選択していて、そのサブスクリプションに、修復すべき未処理のセキュリティの推奨事項があるリソースがない場合 (異常なリソースが 0 の場合)、 **[推奨事項]** フィルターにオプションは表示されません。 

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="Azure Security Center の資産インベントリのフィルター オプションを使用して、監視されていない運用リソースにリソースをフィルター処理する":::

1. **[セキュリティの調査結果に含まれる内容]** フィルターを使用するには、脆弱性の検出結果の ID、セキュリティ チェック、または CVE 名からフリー テキストを入力して、影響を受けるリソースをフィルター処理します。

    ![[セキュリティの調査結果に含まれる内容] フィルター](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > **[セキュリティの調査結果に含まれる内容]** と **[タグ]** フィルターには、1 つの値のみ指定できます。 複数のフィルターを使用してフィルター処理を行うには **[フィルターの追加]** を使用します。

1. **Azure Defender** フィルターを使用するには、1 つ以上のオプション (オフ、オン、一部) を選択します。

    - **[オフ]** - Azure Defender プランによって保護されていないリソース。 これらのいずれかを右クリックしてアップグレードできます。

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="右クリックでリソースを Azure Defender にアップグレードする" lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - **[オン]** - Azure Defender プランによって保護されているリソース。
    - **[一部]** - これは、Azure Defender プランのすべてではなく一部が無効になっている **サブスクリプション** に適用されます。 たとえば、次のサブスクリプションでは、5 つの Azure Defender プランが無効になっています。 

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="Azure Defender が部分的に有効なサブスクリプション":::

1. クエリの結果をさらに調べるには、該当するリソースを選択します。

1. 現在選択されているフィルター オプションを、Resource Graph エクスプローラーでクエリとして表示するには、 **[クエリを開く]** を選択します。

    ![ARG でのインベントリ クエリ](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. 以前に定義したロジック アプリを実行するには 

1. フィルターを定義してページを開いたままにしておくと、Security Center で結果が自動的に更新されません。 ページを手動で再読み込みするか、 **[更新]** を選択しない限り、リソースを変更しても表示される結果には影響しません。


## <a name="faq---inventory"></a>FAQ - インベントリ

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>一部のサブスクリプション、マシン、ストレージ アカウントなどが表示されないのはなぜですか。

インベントリ ビューには、クラウド セキュリティ態勢管理 (CSPM) の観点から、Security Center に接続されたリソースの一覧が表示されます。 フィルター処理によって返されるのは、環境内のすべてのリソースではなく、未処理の (または「アクティブな」) レコメンデーションを含むリソースだけです。 

たとえば、次のスクリーンショットは、38 個のサブスクリプションにアクセスできるユーザーを示しています。現在、10 個にのみ推奨事項があります。 そのため、**リソースの種類 = サブスクリプション** でフィルター処理すると、アクティブな推奨事項がある 10 個のサブスクリプションだけがインベントリに表示されます。

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="アクティブなレコメンデーションがない場合、一部のサブスクリプションは返されません。":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-azure-defender-or-agent-monitoring-columns"></a>一部のリソースで、Azure Defender 列またはエージェントの監視列に空白の値が表示されるのはなぜですか。

Security Center で監視されているすべてのリソースにエージェントがあるわけではありません。 ディスク、Logic Apps、Data Lake Analytics、イベント ハブなどの Azure Storage アカウントや PaaS リソースがその例です。

価格やエージェントの監視がリソースに関係しない場合、インベントリのこれらの列には何も表示されません。

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="一部のリソースでエージェントの監視列または Azure Defender 列に空白の情報が表示される":::

## <a name="next-steps"></a>次のステップ

この記事では、Azure Security Center の資産インベントリ ページについて説明しました。

関連するツールの詳細については、以下のページをご覧ください。

- [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)
- [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/)