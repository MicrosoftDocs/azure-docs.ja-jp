---
title: Azure Security Center の資産インベントリ
description: Security Center で監視されているすべてのリソースを完全に可視化する Azure Security Center の資産管理エクスペリエンスについて説明します。
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 08/11/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 7fab15ae4e9e58af58a4490beb2d512379976d1c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263834"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>資産インベントリ ツールと資産管理ツールを使用してリソースの調査と管理を行う

Azure Security Center の資産インベントリ ページを使用すると、Security Center に接続したリソースのセキュリティ態勢が 1 つのページに表示されます。 

Security Center では、Azure リソースのセキュリティの状態が定期的に分析されて、潜在的なセキュリティ脆弱性が特定されます。 その後、これらの脆弱性を修正する方法に関するレコメンデーションが提供されます。

いずれかのリソースに未処理のレコメンデーションがある場合は、インベントリに表示されます。

このビューとそのフィルターを使用して、次のような質問に対処できます。

- Standard レベルのサブスクリプションのうち、未処理のレコメンデーションがあるものはどれか
- 「Production」というタグが付いているマシンのうち、Log Analytics エージェントがないものはどれか
- 特定のタグが付いているマシンのうち、何台のマシンに未処理のレコメンデーションがあるか
- 特定のリソース グループ内のリソースのうち、何個のリソースに脆弱性評価サービスによるセキュリティの調査結果があるか

このツールによる資産管理には大きな可能性があり、その範囲は拡大し続けています。 


## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|プレビュー|
|価格:|Free|
|必要なロールとアクセス許可:|すべてのユーザー|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![No](./media/icons/no-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>資産インベントリの主な機能

インベントリ ページには次のツールが含まれています。

- **概要** - フィルターを定義する前に、インベントリ ビューの上部に以下の値が横並びで目立つように表示されます。

    - **リソースの合計**:Security Center に接続されているリソースの合計数。
    - **異常なリソース**:アクティブなセキュリティのレコメンデーションがあるリソース。 セキュリティのレコメンデーションの詳細については、[こちら](https://docs.microsoft.com/azure/security-center/security-center-recommendations)をご覧ください。
    - **監視されていないリソース**:エージェントの監視に関して問題があるリソースです。Log Analytics エージェントがデプロイされていますが、エージェントがデータを送信していないか、正常性に関する他の問題が発生しています。

- **フィルター** - ページの上部にある複数のフィルターを使用すると、回答しようとしている質問に従ってリソースの一覧をすばやく絞り込むことができます。 たとえば、「 *「Production」というタグが付いているマシンのうち、Log Analytics エージェントがないものはどれか*」という質問に回答する場合は、次のクリップに示すように、 **[エージェント監視]** フィルターと **[タグ]** フィルターを組み合わせることができます。

    ![監視されていない運用リソースへのフィルター処理](./media/asset-inventory/filtering-to-prod-unmonitored.gif)

    フィルターを適用すると、すぐに概要の値がクエリ結果に関連付けて更新されます。 

- **エクスポート オプション** - インベントリには、選択したフィルター オプションの結果を CSV ファイルにエクスポートするオプションが用意されています。 さらに、クエリ自体を Azure Resource Graph エクスプローラーにエクスポートして、KQL クエリの絞り込み、保存、変更を行うこともできます。

    ![インベントリのエクスポート オプション](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > KQL のドキュメントでは、いくつかのサンプル データと単純なクエリを組み合わせたデータベースを使用して、この言語の「感触」をつかむことができます。 詳しくは、[KQL のチュートリアル](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)をご覧ください。

- **[資産管理オプション]** - インベントリでは、複雑な検出クエリを実行できます。 クエリに一致するリソースが見つかると、インベントリに次のような操作のショートカットが表示されます。

    - フィルター処理されたリソースにタグを割り当てる - タグを付けるリソースの横にあるチェック ボックスをオンにします
    - Security Center に新しいサーバーをオンボードする - **[非 Azure サーバーの追加]** ツールバー ボタンを使用します


## <a name="how-does-asset-inventory-work"></a>資産インベントリのしくみ

資産インベントリは、[Azure Resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/) を利用しています。ARG は、複数のサブスクリプションにまたがって Security Center のセキュリティ態勢データのクエリを実行できる Azure サービスです。

ARG は、大規模なクエリの実行機能によってリソースを効率的に探索できるように設計されています。

資産インベントリで [Kusto クエリ言語 (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) を使用すると、他のリソース プロパティで ASC データを相互参照して詳細な分析情報をすばやく作成できます。


## <a name="how-to-use-asset-inventory"></a>資産インベントリの使用方法

1. Security Center のサイドバーで、 **[インベントリ]** を選択します。

1. 必要に応じて、特定のリソースを表示するには、 **[名前でフィルター]** ボックスに名前を入力します。

1. フィルターの関連するオプションを選択して、実行する特定のクエリを作成します。

    ![インベントリのフィルター](./media/asset-inventory/inventory-filters.png)

    既定では、リソースはアクティブなセキュリティのレコメンデーションの数によって並べ替えられます。

    > [!IMPORTANT]
    > 各フィルターのオプションは、現在選択されているサブスクリプション内のリソース**および**他のフィルターで選択した項目に固有のものです。
    >
    > たとえば、サブスクリプションを 1 つだけ選択していて、そのサブスクリプションに未処理のセキュリティのレコメンデーションを含むリソースがない (異常なリソースが 0 である) 場合、 **[レコメンデーション]** フィルターにはオプションが表示されません。 

1. **[セキュリティの調査結果に含まれる内容]** フィルターを使用するには、脆弱性の検出結果の ID、セキュリティ チェック、または CVE 名からフリー テキストを入力して、影響を受けるリソースをフィルター処理します。

    ![[セキュリティの調査結果に含まれる内容] フィルター](./media/asset-inventory/security-findings-contain-elements.png)

1. **[価格レベル]** フィルターを使用するには、1 つまたは複数のオプション (Free、Partial、または Standard) を選択します。

    - **Free** - Free 価格レベルで提供されるリソース
    - **Standard** - Standard 価格レベルで提供されるリソース
    - **Partial** - これは、Standard 価格レベルで提供されるが、一部のオプションのセキュリティ プランが無効になっているサブスクリプションに適用されます。 たとえば、次のサブスクリプションは Standard レベルで提供されますが、Standard レベルの 5 つの要素が無効になっています。 

        ![Standard (部分的) 価格レベルで提供されるサブスクリプション](./media/asset-inventory/pricing-tier-partial.png)

1. クエリの結果をさらに調べるには、該当するリソースを選択します。

1. 必要に応じて、 **[Resource Graph エクスプローラーで表示]** を選択し、Resource Graph エクスプローラーでクエリを開きます。

    ![ARG でのインベントリ クエリ](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. フィルターを定義してページを開いたままにしておくと、Security Center で結果が自動的に更新されません。 ページを手動で再読み込みするか、 **[更新]** を選択しない限り、リソースを変更しても表示される結果には影響しません。


## <a name="faq---inventory"></a>FAQ - インベントリ

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>一部のサブスクリプション、マシン、ストレージ アカウントなどが表示されないのはなぜですか。

インベントリ ビューには、クラウド セキュリティ態勢管理 (CSPM) の観点でリソースの一覧が表示されます。 フィルター処理によって返されるのは、環境内のすべてのリソースではなく、未処理の (または「アクティブな」) レコメンデーションを含むリソースだけです。 

たとえば、9 個のサブスクリプションがあり、現時点では 8 個のサブスクリプションにレコメンデーションがある場合、 **[リソースの種類] = [サブスクリプション]** でフィルター処理すると、アクティブなレコメンデーションを含む 8 個のサブスクリプションのみが表示されます。

![アクティブなレコメンデーションがない場合、一部のサブスクリプションは返されません。](./media/asset-inventory/filtered-subscriptions-some.png)


### <a name="why-do-some-of-my-resources-show-blank-values-in-the-pricing-or-agent-monitoring-columns"></a>一部のリソースの価格列やエージェントの監視列に空白の値が表示されるのはなぜですか。

Security Center で監視されているすべてのリソースにエージェントがあるわけではありません。 ディスク、Logic Apps、Data Lake Analytics、イベント ハブなどの Azure Storage アカウントや PaaS リソースがその例です。

価格やエージェントの監視がリソースに関係しない場合、インベントリのこれらの列には何も表示されません。

![一部のリソースでは、エージェントの監視列や価格列に空白の情報が表示されます](./media/asset-inventory/agent-pricing-blanks.png)



## <a name="next-steps"></a>次のステップ

この記事では、Azure Security Center の資産インベントリ ページについて説明しました。

関連するツールの詳細については、以下のページをご覧ください。

- [Azure Resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)

- [Kusto クエリ言語 (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)