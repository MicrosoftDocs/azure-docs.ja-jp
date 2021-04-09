---
title: アプリケーション変更分析の視覚化 - Azure Monitor
description: Azure Monitor でアプリケーション変更分析の視覚化を使用する方法について説明します。
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 8319885de26bf79f5e402c4d06b29e9dd94894de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655853"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>アプリケーション変更分析の視覚化 (プレビュー)

## <a name="standalone-ui"></a>スタンドアロン UI

Azure Monitor では、変更分析のためのスタンドアロン ウィンドウがあり、アプリケーションの依存関係とリソースに関する分析情報の変更を表示できます。

Azure portal の検索バーで変更分析を検索し、エクスペリエンスを起動します。

![Azure portal における変更分析の検索のスクリーンショット](./media/change-analysis/search-change-analysis.png)

選択したサブスクリプションのすべてのリソースの、過去 24 時間の変更内容が表示されます。 すべての変更が古い値および新しい値と共に表示され、分析情報の概要が提供されます。

![Azure portal の変更分析ブレードのスクリーンショット](./media/change-analysis/change-analysis-standalone-blade.png)

変更をクリックすると、Resource Manager の完全なスニペットとその他のプロパティが表示されます。

![変更の詳細のスクリーンショット](./media/change-analysis/change-details.png)

フィードバックについては、フィードバックの送信用のボタンを使用するか、changeanalysisteam@microsoft.com まで電子メールを送信してください。

![[変更分析] タブのフィードバック ボタンのスクリーンショット](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>複数のサブスクリプションのサポート

UI では、リソースの変更を表示する複数のサブスクリプションの選択がサポートされています。 次のようにサブスクリプション フィルターを使用します。

![複数のサブスクリプションの選択をサポートするサブスクリプション フィルターのスクリーンショット](./media/change-analysis/multiple-subscriptions-support.png)


## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>問題の診断と解決ツールのアプリケーション変更分析

アプリケーション変更分析は、Web アプリの問題の診断と解決ツールに含まれているスタンドアロン検出機能です。 また、 **[アプリケーションのクラッシュ]** と **[Web App Down detectors]\(Web アプリのダウン検出\)** にも含まれています。 [問題の診断と解決] ツールを開くと、**Microsoft.ChangeAnalysis** リソース プロバイダーが自動的に登録されます。 次の手順に従って、ゲストでの Web アプリの変更追跡を有効にします。

1. **[Availability and Performance]\(可用性とパフォーマンス\)** を選択します。

    ![[Availability and Performance]\(可用性とパフォーマンス\) のトラブルシューティング オプションのスクリーンショット](./media/change-analysis/availability-and-performance.png)

2. **[Application Changes]\(アプリケーションの変更\)** を選択します。 この機能は、 **[アプリケーションのクラッシュ]** でも使用できます。

   ![[アプリケーションのクラッシュ] ボタンのスクリーンショット](./media/change-analysis/application-changes.png)

3. このリンクから、Web アプリを対象としたアプリケーション変更分析 UI につながります。 Web アプリのゲスト内の変更追跡が有効になっていない場合、バナーに従って、ファイルとアプリの設定の変更を取得します。

   ![[アプリケーションのクラッシュ] オプションのスクリーンショット](./media/change-analysis/enable-changeanalysis.png)

4. **変更分析** を有効にして **[保存]** を選択します。 ツールでは、すべての Web アプリが [App Service プラン] の下に表示されます。 プラン レベルのスイッチを使用して、プランの下にあるすべての Web アプリの変更分析をオンにできます。

    !["変更分析を有効にする" ユーザー インターフェイスのスクリーンショット](./media/change-analysis/change-analysis-on.png)

5. 変更データは、 **[Web アプリのダウン]** および **[アプリケーションのクラッシュ]** 検出機能の選択でも使用でき ます。 変更の種類を時系列でまとめたグラフと、その変更の詳細が表示されます。 既定では、当面の問題を解決できるように、過去 24 時間の変更が表示されます。

     ![変更の差分ビューのスクリーンショット](./media/change-analysis/change-view.png)

## <a name="diagnose-and-solve-problems-tool"></a>問題の診断と解決ツール
問題の診断と解決ツールでは、変更分析を分析情報カードとして利用できます。 リソースに問題が発生し、過去 72 時間以内に検出された変更がある場合、分析情報カードに変更の数が表示されます。 変更の詳細を表示するリンクをクリックすると、変更分析スタンドアロン UI のフィルター処理されたビューが表示されます。

![問題の診断と解決ツールで変更分析情報を表示するスクリーンショット。](./media/change-analysis/change-insight-diagnose-and-solve.png)



## <a name="virtual-machine-diagnose-and-solve-problems"></a>仮想マシンの問題の診断と解決

仮想マシンの問題の診断と解決ツールにアクセスします。  **[トラブルシューティング ツール]** に移動し、ページの下を参照して **[最近の変更の分析]** を選択し、仮想マシンの変更内容を確認します。

![VM の問題の診断と解決のスクリーンショット](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![トラブルシューティング ツールの変更アナライザー](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>アクティビティ ログ変更履歴

アクティビティ ログの[変更履歴の表示](../essentials/activity-log.md#view-change-history)機能では、アプリケーション変更分析サービス バックエンドを呼び出して、操作に関連付けられている変更を取得します。 **変更履歴** では、[Azure Resource Graph](../../governance/resource-graph/overview.md) が直接呼び出されていましたが、バックエンドが交換され、アプリケーション変更分析が呼び出されるようになったため、返される変更に、[Azure リソースグラフ](../../governance/resource-graph/overview.md)からのリソースレベルの変更、 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) からのリソース プロパティ、App Services Web アプリなどの PaaS サービスからのゲスト内の変更が含まれるようになります。 アプリケーション変更分析サービスで、ユーザーのサブスクリプションの変更をスキャンできるようにするには、リソース プロバイダーを登録する必要があります。 **[変更履歴]** タブに初めて入ると、ツールによって **Microsoft.ChangeAnalysis** リソース プロバイダーの登録が自動的に開始されます。 登録後、**Azure Resource Graph** からの変更がすぐに利用できるようになり、過去 14 日間が対象になります。 ほかのソースからの変更は、サブスクリプションのオンボードから 4 時間後に使用できるようになります。

![アクティビティ ログ変更履歴の統合](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>VM Insights の統合

[VM Insights](../vm/vminsights-overview.md) を有効にしているユーザーは、仮想マシンの変更内容を見て、CPU やメモリなどのメトリック グラフでスパイクを発生させた可能性があるものを確認できます。 変更データは、VM Insights のサイド ナビゲーションバーに統合されています。 ユーザーは、VM で変更が発生したかどうかを確認し、 **[変更の調査]** を選択して、アプリケーション変更分析スタンドアロン UI で変更の詳細を表示できます。

[![VM insights の統合](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>次のステップ

- [変更の分析で問題をトラブルシューティングする](change-analysis-troubleshoot.md)方法について学習します
