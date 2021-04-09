---
title: Azure Monitor リソース グループの分析情報 | Microsoft Docs
description: Azure Monitor によるリソース グループ レベルで分散アプリケーションとサービスの正常性とパフォーマンスの概要
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ab2c56b5e5cfc3dcb7411db27e7faabb2589be6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704329"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Azure Monitor でリソース グループを監視する (プレビュー)

多くの場合、最新のアプリケーションは複雑で高度に分散しており、多くの個別のパーツが連携してサービスを提供しています。 このような複雑さを認識して、Azure Monitor はリソース グループの分析情報の監視機能を提供しています。 そのため、リソース グループ&mdash;とアプリケーション&mdash;全体の正常性とパフォーマンスに関するコンテキストを表示しながら、個々のリソースで問題が発生した場合に問題の分類と診断を簡単に実行できます。

## <a name="access-insights-for-resource-groups"></a>リソース グループの分析情報にアクセスする

1. 左側のナビゲーションバーから **[リソース グループ]** を選択します。
2. 確認するリソース グループの 1 つを選択します (多数のリソース グループがある場合は、サブスクリプションによるフィルター処理が役に立つ場合があります)。
3. リソース グループの分析情報にアクセスするには、任意のリソース グループの左側のメニューで **[分析情報]** をクリックします。

![リソース グループの分析情報の概要ページのスクリーンショット](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>アクティブなアラートと正常性の問題があるリソース

概要ページには、トリガーされてまだアクティブなアラートの数と、各リソースの現在の Azure Resource Health が表示されます。 このような情報を組み合わせることで、問題が発生しているリソースをすばやく見つけることができます。 アラートは、コードやインフラストラクチャの構成方法の問題を検出するために役立ちます。 Azure Resource Health によって、個々のアプリケーションに固有ではない Azure プラットフォーム自体に関する問題が明らかになります。

![[Azure Resource Health] ウィンドウのスクリーンショット](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Azure Resource Health を表示するには、表の上の **[Azure Resource Health を表示します]** チェックボックスをオンにします。 この列は、ページの読み込みを速くするために既定では非表示です。

![リソースの正常性グラフが追加されたスクリーンショット](./media/resource-group-insights/0003-overview.png)

既定では、リソースはアプリ層とリソースの種類によってグループ化されています。 **アプリ層** は、リソースの種類の単純な分類です。リソース グループ分析情報の概要ページのコンテキスト内にのみ存在します。 アプリケーション コード、コンピューティング インフラストラクチャ、ネットワーク、ストレージ + データベースに関連するリソースの種類があります。 管理ツールには独自のアプリ層があり、他のすべてのリソースは **[その他]** のアプリ層に属するものとして分類されます。 このグループ化機能によって、アプリケーションのどのサブシステムが正常か正常ではないかを一目で把握することができます。

## <a name="diagnose-issues-in-your-resource-group"></a>リソース グループ内の問題を診断する

リソース グループの分析情報ページには、問題の診断に役立つその他のツールがいくつか用意されています

   |         |          |
   | ---------------- |:-----|
   | [**アラート**](../alerts/alerts-overview.md)      |  アラートを表示、作成、管理します。 |
   | [**メトリック**](../data-platform.md) | メトリック ベースのデータを視覚化し、探索します。    |
   | [**アクティビティ ログ**](../essentials/platform-logs-overview.md) | Azure で発生したサブスクリプション レベルのイベント。  |
   | [**アプリケーション マップ**](../app/app-map.md) | 分散アプリケーションのトポロジを確認して、パフォーマンスのボトルネックや障害のホットスポットを特定します。 |

## <a name="failures-and-performance"></a>エラーとパフォーマンス

アプリケーションの動作が遅い場合や、ユーザーからエラーが報告された場合はどうすればよいでしょうか。 問題を分離するためにすべてのリソースを検索するのは時間がかかります。

**[パフォーマンス]** タブと **[エラー]** タブでは、多数の一般的なリソースの種類についてパフォーマンスおよびエラーの診断ビューをまとめることで、このプロセスを簡略化しています。

ほとんどのリソースの種類で、Azure Monitor Workbook テンプレートのギャラリーが開きます。 作成された各ブックは、カスタマイズして保存し、チームと共有し、今後同様の問題を診断するために再利用できます。

### <a name="investigate-failures"></a>エラーを調査する

[エラー] タブをテストするには、左側のメニューの **[調査]** にある **[エラー]** を選択します。

選択すると、左側のメニュー バーが変更され、新しいオプションが表示されます。

![エラーの概要ウィンドウのスクリーンショット](./media/resource-group-insights/00004-failures.png)

App Service を選択すると、Azure Monitor Workbook テンプレートのギャラリーが表示されます。

![アプリケーション ブック ギャラリーのスクリーンショット](./media/resource-group-insights/0005-failure-insights-workbook.png)

エラーの分析情報のテンプレートを選択すると、ブックが開きます。

![エラー レポートのスクリーンショット](./media/resource-group-insights/0006-failure-visual.png)

任意の行を選択できます。 選択内容はグラフィカルな詳細ビューに表示されます。

![エラーの詳細のスクリーンショット](./media/resource-group-insights/0007-failure-details.png)

ブックによって、簡単に使用できる形式でカスタム レポートや視覚化を作成するという困難な作業がなくなります。 ユーザーによっては、事前に構築済みのパラメーターを調整したいだけかもしれませんが、ブックは完全にカスタマイズ可能です。

このブックが内部的にどのように機能するかを把握するには、上部のバーにある **[編集]** を選択します。

![その他の編集オプションのスクリーンショット](./media/resource-group-insights/0008-failure-edit.png)

ブックのさまざまな要素の近くに複数の **[編集]** ボックスが表示されます。 操作の一覧の下にある **[編集]** ボックスを選択します。

![[編集] ボックスのスクリーンショット](./media/resource-group-insights/0009-failure-edit-graph.png)

これにより、テーブルの視覚化を制御している基になるログ クエリが表示されます。

 ![ログ クエリ ウィンドウのスクリーンショット](./media/resource-group-insights/0010-failure-edit-query.png)

クエリは直接変更することができます。 また、参照として使用し、独自のカスタム パラメーター ブックを設計するときに利用することもできます。

### <a name="investigate-performance"></a>パフォーマンスを調査する

パフォーマンスには独自のブック ギャラリーがあります。 App Service の場合、事前に構築済みのアプリケーション パフォーマンス ブックには、次のビューがあります。

 ![パフォーマンス ビューのスクリーンショット](./media/resource-group-insights/0011-performance.png)

この場合、編集を選択すると、Azure Monitor メトリックによってこの一連の視覚化が行われていることがわかります。

 ![Azure メトリックが表示されたパフォーマンス ビューのスクリーンショット](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="enabling-access-to-alerts"></a>アラートへのアクセスを有効にする

Azure Monitor でリソース グループのアラートを確認するには、このサブスクリプションの所有者または共同作成者ロールを持つユーザーが、サブスクリプション内の任意のリソース グループに対して、Azure Monitor for Resource Groups を開く必要があります。 これにより、読み取りアクセス権を持つユーザーが、サブスクリプション内のすべてのリソース グループのアラートを Azure Monitor for Resource Groups で確認できるようになります。 所有者または共同作成者ロールをお持ちの場合、数分単位でこのページを更新してください。

Azure Monitor for Resource Groups は、Azure Monitor Alerts Management システムに依存して、アラート ステータスを取得します。 Alerts Management は、既定でどのリソース グループおよびサブスクリプションにも構成されているわけではなく、所有者または共同作成者ロールを持つユーザーしか有効化できません。 次のいずれかの方法で、有効化できます。
* サブスクリプション内の任意のリソース グループに対して Azure Monitor for Resource Groups を開く。
* サブスクリプションに移動して、 **[リソース プロバイダー]** をクリックしてから、 **[Register for Alerts.Management]\(Alerts.Management への登録\)** をクリックする。

## <a name="next-steps"></a>次のステップ

- [Azure Monitor ブック](../visualize/workbooks-overview.md)
- [Azure Resource Health](../../service-health/resource-health-overview.md)
- [Azure Monitor アラート](../alerts/alerts-overview.md)
