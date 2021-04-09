---
title: Log Analytics ダッシュボードの視覚化のアップグレード
description: 強力な分析情報を提供できるクエリを使用して、Log Analytics ダッシュボードの視覚化をアップグレードする方法について説明します。
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: 276ea1e2f083da7ab4a6ab44e60bc7e0832e8651
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030938"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>Log Analytics ダッシュボードの視覚化のアップグレード

2020 年 2 月に、強化された視覚化テクノロジが導入されました。 クエリ結果を視覚化し、強力な分析情報をすばやく入手できるように、機能が向上および強化されています。 

このアップグレードの詳細については、こちらの [Azure の更新](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/)に関するページを参照してください。 

この新しい視覚化テクノロジは、クエリの結果セットに関するエクスペリエンスを新たに向上させる基盤となります。 

## <a name="dashboards-in-azure"></a>Azure のダッシュボード

Azure のダッシュボードは、Azure 全体の状態を視覚化するための手段です。 Azure の資産状態を一元的に表示し、さまざまなショートカットを使用して一般的なアクションにアクセスできるよう設計されています。 

詳細については、[Azure のダッシュボード](../../azure-portal/azure-portal-dashboards.md)に関するページをご覧ください


## <a name="upgrading-log-analytics-dashboard-parts"></a>Log Analytics ダッシュボードのパーツのアップグレード

新しい視覚化テクノロジによって、以前の実装に関連する一般的な問題のいくつかに対処がなされ、Log Analytics のピン留めされたパーツに次のような新機能が導入されました。 

- **同じ種類を使用可能** - Log Analytics で使用できるすべての視覚化の種類を、ダッシュボードにピン留めされたパーツとして使用できます。

- **外観の一貫性** - ピン留めされたパーツの視覚化の外観が、Log Analytics のものとほぼ同じになっています。 違いが発生するのは、最適化の際にビジュアルのデータ コンテンツにわずかに変更を加える必要があるためです。 これらの違いの詳細については、このドキュメントの「考慮事項」を参照してください。

- **ツールヒントとラベル** - ピン留めされた新しい Log Analytics 視覚化は、ツールヒントをサポートしています。 円グラフとドーナツ グラフでは、ラベルを使用できるようになりました。

- **対話型の凡例** - 視覚化の凡例をクリックすると、Log Analytics と同様に、ピン留めされたビジュアルにディメンションを追加したり削除したりできます。

## <a name="stage-1---opt-in-upgrade-message"></a>ステージ 1 - アップグレードのオプトイン メッセージ

Log Analytics のピン留めされたパーツをアップグレードできる場合、Log Analytics のダッシュボードにピン留めされたパーツに新しい "*オプトイン*" 通知が表示され、ユーザーは視覚化をアップグレードできます。 新しい視覚化を体験する場合は、ダッシュボードで視覚化を選択してアップグレードしてください。

 
![サイドバー](media/dashboard-upgrade/update-message-1.png)
 
![タイルの視覚化を更新する方法を示すスクリーンショット。](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> ダッシュボードを発行すると、アップグレードを元に戻すことはできません。 ただし、再発行せずにダッシュボードから移動すると、変更が破棄されます。  

クリックすると、視覚化が新しいテクノロジに更新されます。 Log Analytics の外観に合わせるために、視覚化に微妙な違いが発生する可能性があります。

視覚化をアップグレードした後は、変更を有効にするためにダッシュボードを再発行する必要があります。

![アップグレードされた視覚化を示すスクリーンショット。](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>ステージ 2 - すべてのダッシュボードの移行

最初のオプトイン期間が過ぎると、Log Analytics チームはシステム内のすべてのダッシュボードをアップグレードします。 チームはすべての Azure ダッシュボードを調整することで、ダッシュボード全体により多くの視覚化を導入して、エクスペリエンスを向上できるようになります。

## <a name="considerations"></a>考慮事項

ダッシュボードにピン留めされた Log Analytics 視覚化には、最適なエクスペリエンスを実現するために設計された固有の動作があります。 ダッシュボードに視覚化をピン留めするときは、次の設計上の考慮事項を確認してください。

### <a name="query-time-scope---30-day-limit"></a>クエリの時間範囲 - 30 日間の制限

ダッシュボードには複数のクエリによる複数の視覚化を含めることができますが、ピン留めされた 1 つのクエリの時間範囲は 30 日に制限されています。 1 つのクエリは、30 日の期間内のみ実行できます。 この制限は、適切なダッシュボード読み込み時間を確保するためのものです。

### <a name="query-data-values---25-values-and-other-grouping"></a>クエリのデータ値 - 25 の値とその他のグループ化

ダッシュボードは視覚的な密度が高く、複雑になることがあります。 ダッシュボードの表示を見やすくするために、表示するデータの種類を 25 に制限することによって、視覚化が最適化されています。 データの種類が 25 を超える場合は、Log Analytics によって最適化されます。 大きい順に 25 種類のデータが個別に表示され、残りの値は "その他" の値としてグループ化されます。 次のグラフは、このようなケースを示しています。  

![25 個の異なるデータ型のダッシュボードを示すスクリーンショット。](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>読み込み時のダッシュボードの更新

ダッシュボードは、読み込み時に更新されます。 ダッシュボードにピン留めされた Log Analytics の視覚化に関連するすべてのクエリが実行され、読み込まれる際にダッシュボードが更新されます。 ダッシュボード ページを開いたままにすると、ダッシュボードのデータは 60 分ごとに更新されます。

## <a name="next-steps"></a>次のステップ

[Log Analytics でダッシュボードを作成して共有する](../visualize/tutorial-logs-dashboards.md)