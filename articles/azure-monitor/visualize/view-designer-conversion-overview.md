---
title: Azure Monitor ビュー デザイナーからブックへの移行ガイド
description: Azure Monitor のビューからブックへの移行。
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 19de2a3d3f5a998c09db7c4df689b0378e2597fd
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043373"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure Monitor ビュー デザイナーからブックへの移行ガイド
[ビュー デザイナー](view-designer.md)は Azure Monitor の機能で、Log Analytics ワークスペース内のデータを、グラフ、リスト、タイムラインを使用して視覚化するのに役立つカスタム ビューを作成できます。 これらはブックに移行されており、Azure portal 内でデータを分析し、高度な視覚的レポートを作成するための柔軟なキャンバスを提供します。 この記事は、ビュー デザイナーからブックへの移行を行う際に役立ちます。 


## <a name="workbooks-overview"></a>ブックの概要
[ブック](../vm/vminsights-workbooks.md)では、テキスト、 [ログ クエリ](/azure/data-explorer/kusto/query/)、メトリック、パラメーターが、内容豊富な対話型レポートに組み合わされます。 Azure リソースに対して同じアクセス権を持つチーム メンバーがブックを編集することもできます。

Workbooks は次のようなシナリオで便利です。

-   目的のメトリックが事前にわかっていないときに、仮想マシンの使用状況を調べます。CPU 使用率、ディスク領域、メモリ、ネットワークの依存関係など。他の使用状況分析ツールとは異なり、ブックを使用すると複数の種類の視覚化ツールや分析ツールを結合できるため、この種のフリーフォーム探索に適しています。
-   主要なカウンターと他のログ イベントに関するメトリックを示すことによって、最近プロビジョニングされた VM のパフォーマンスをチームに説明します。
-   VM のサイズ変更実験の結果を、チームの他のメンバーと共有します。 実験の目標をテキストで説明した後に、その実験の評価に使用される使用状況メトリックと分析クエリ、および各メトリックが目標を上回ったかまたは下回ったかを明確に提示できます。
-   停止が VM の使用に及ぼす影響を報告し、データ、テキスト説明、および次の手順についての話し合いを組み合わせて将来の停止を防止します。


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>ビュー デザイナーのダッシュボードをブックに変換する理由

ビュー デザイナーには、さまざまなクエリベースのビューと視覚化を生成する機能が用意されています。 ただし、多くの高度なカスタマイズは、グリッドやタイル レイアウトの書式設定や、データを表す代替グラフィックスの選択など、制限されたままです。 ビュー デザイナーは、データを表すために、合計で 9 つの個別のタイルに制限されています。

Workbooks は、データの持つ可能性を最大限に引き出すためのプラットフォームです。 ブックにはすべての機能が保持されるだけでなく、テキスト、メトリック、パラメーターなどを通じて追加の機能もサポートされます。 たとえば、ブックを使用すると、ユーザーは高密度のグリッドを統合し、検索バーを追加してデータを簡単にフィルター処理して分析することができます。 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>ブックを使用することの、ビュー デザイナーに勝る利点

* ログとメトリックの両方がサポートされます。
* 個別のアクセス制御ビューと共有ブック ビューの両方の個人用ビューを使用できます。
* タブ、サイズ変更、およびスケール コントロールを持つカスタム レイアウト オプション。
* 複数の Log Analytics ワークスペース、Application Insights アプリケーション、サブスクリプションに対する横断的なクエリ実行のサポート。
* 関連付けられたグラフや視覚化を動的に更新するカスタム パラメーターが有効になります。
* パブリック GitHub のテンプレート ギャラリーのサポート。

このガイドでは、一般的に使用されるいくつかのビュー デザイナー ビューを直接再作成するための簡単な手順を説明していますが、ブックを使用すると、ユーザーは独自のカスタムの視覚化とメトリックを自由に作成してデザインできます。 次のスクリーンショットは、[ワークスペースの使用状況テンプレート](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook)のものであり、ブックで何を作成できるかを表す例を示しています。


![ブック アプリケーションの例](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>ブックの使用を開始する方法
[ブック] から開いたブックは、Log Analytics ワークスペースで、ビュー デザイナーの場所のすぐ下にあるサイド ナビゲーション バーの項目として有効になります。

![ブックのナビゲーション](media/view-designer-conversion-overview/workbooks-nav.png)

選択すると、ワークスペース用に保存されているすべてのブックとテンプレートの一覧がギャラリーに表示されます。

![ブック ギャラリー](media/view-designer-conversion-overview/workbooks-gallery.png)

新しいブックを開始するには、 **[クイック スタート]** の下にある **空** のテンプレートを選択するか、上部のナビゲーション バーの **[新規]** アイコンを選択します。 テンプレートを表示するか、保存済みブックに戻るには、ギャラリーからその項目を選択するか、検索バーで名前を検索します。

ブックを保存するには、特定のタイトル、サブスクリプション、リソース グループ、場所を指定してレポートを保存する必要があります。
ブックは、LA ワークスペースと同じサブスクリプションおよびリソース グループを使用して、同じ設定にオートフィルされます。ただし、これらのレポート設定はユーザーが変更できます。 ブックは、既定では *[個人用レポート]* に保存され、個々のユーザーのみがアクセスできます。 また、共有レポートに直接保存することや、後で共有することもできます。

![ブックの保存](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>次のステップ

- [変換オプション](view-designer-conversion-options.md)