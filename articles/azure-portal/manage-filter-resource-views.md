---
title: Azure リソース情報の表示とフィルター処理
description: 情報をフィルター処理し、さまざまなビューを使用して、Azure リソースをより深く理解します。
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: bb48d0b0a7bf6017fbf407a95c33ef17729e34e3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771636"
---
# <a name="view-and-filter-azure-resource-information"></a>Azure リソース情報の表示とフィルター処理

Azure portal を使用すると、Azure サブスクリプション全体でリソースに関する詳細情報を参照できます。 この記事では、情報をフィルター処理し、さまざまなビューを使用してリソースをより深く理解する方法について説明します。

この記事では、次のスクリーンショットに示されている **[すべてのリソース]** の画面について重点的に説明します。 仮想マシンなどの個々のリソースの種類の画面には、VM の起動や停止などのさまざまなオプションがあります。

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="Azure portal の [すべてのリソース] ビュー":::

## <a name="filter-resources"></a>リソースをフィルター処理する

フィルターを使用してリソースのサブセットに焦点を当てて、 **[すべてのリソース]** の説明を開始します。 次のスクリーンショットは、リソース グループでフィルター処理し、サブスクリプション内の 6 つのリソース グループのうち 2 つを選択した状態を示しています。

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="リソース グループに基づくビューのフィルター処理":::

次のスクリーンショットに示すように、テキスト検索に基づくものを含めて、フィルターを組み合わせることができます。 この場合の結果は、既に選択されている 2 つのリソース グループのいずれかに "SimpleWinVM" が含まれているリソースが対象になります。

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="テキスト入力に基づくビューのフィルター処理":::

ビューに含める列を変更するには、 **[ビューの管理]** を選択し、 **[列の編集]** をクリックします。

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="ビューに表示される列の編集":::

## <a name="save-use-and-delete-views"></a>ビューを保存、使用、削除する

選択したフィルターと列を含むビューを保存できます。 ビューを保存して使用するには、次のようにします。

1. **[ビューの管理]** 、 **[ビューの保存]** の順に選択します。

1. ビューの名前を入力し、 **[OK]** を選択します。 保存されたビューが **[ビューの管理]** メニューに表示されます。

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="保存済みビュー":::

1. ビューを使用するには、 **[既定]** といずれかの独自のビューを切り替えて、表示されるリソースの一覧にどのように影響するかを確認します。

ビューを削除するには、次のようにします。

1. **[ビューの管理]** 、 **[すべてのビューを参照]** の順に選択します。

1. **[保存済みビュー]** ペインで、ビューを選択して **[削除]** アイコン ![ビューの削除アイコン](media/manage-filter-resource-views/icon-delete.png) を選択します。

## <a name="export-information-from-a-view"></a>ビューから情報をエクスポートする

ビューからリソース情報をエクスポートできます。 CSV 形式で情報をエクスポートするには:

1. **[CSV にエクスポート]** を選択します。

    :::image type="content" source="media/manage-filter-resource-views/export-csv.png" alt-text="CSV 形式へのエクスポートのスクリーンショット":::

1. ファイルをローカルに保存してから、Excel または CSV 形式がサポートされている別のアプリケーションで開きます。 

ポータル内を移動すると、個々のリソース グループなど、情報をエクスポートできる領域が他にもあることがわかります。

## <a name="summarize-resources-with-visuals"></a>ビジュアルを使用してリソースを集計する

これまでに見たビューは _リスト ビュー_ になっていますが、ビジュアルを含む _概要ビュー_ もあります。 これらのビューは、リスト ビューと同じように保存して使用できます。 フィルターは 2 種類のビューの間で保持されます。 次に示す **場所** ビューなどの標準的なビューや、Azure Storage の **状態** ビューなど、特定のサービスに関連するビューがあります。

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="マップ ビュー内のリソースの概要":::

概要ビューを保存して使用するには、次のようにします。

1. ビュー メニューで **[概要ビュー]** を選択します。

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="概要ビューのメニュー":::

1. [概要] ビューでは **場所** や **種類** などのさまざまな属性で集計できます。 **[集計の方法]** オプションおよび適切なビジュアルを選択します。 次のスクリーンショットは、 **[Bar chart]\(棒グラフ\)** ビジュアルを使用して **[種類の概要]** を示しています。

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="棒グラフを表示する種類の概要":::

1. **[ビューの管理]** 、 **[保存]** の順に選択して、リスト ビューで行ったようにこのビューを保存します。

1. [概要] ビューの **[種類の概要]** で、グラフ内の棒を 1 つ選択します。 棒を選択すると、1 種類のリソースにフィルター処理された一覧が表示されます。

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="種類によってフィルター処理されたすべてのリソース":::

## <a name="run-queries-in-azure-resource-graph"></a>Azure Resource Graph でクエリを実行する

Azure Resource Graph は、一連のサブスクリプションにわたって大規模にクエリを実行する機能を備えた、効率的でパフォーマンスに優れたリソース探索を実現します。 Azure portal の **[すべてのリソース]** 画面には、現在フィルターされたビューを対象とした Resource Graph クエリを開くためのリンクが含まれています。

Resource Graph クエリを実行するには、次のようにします。

1. **[クエリを開く]** を選択します。

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="Azure Resource Graph クエリを開く":::

1. **Azure Resource Graph エクスプローラー** で、 **[クエリの実行]** を選択して結果を確認します。

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="Azure Resource Graph クエリを実行する":::

    詳細については、「[Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する](../governance/resource-graph/first-query-portal.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Portal の概要](azure-portal-overview.md)

[Azure portal でのダッシュボードの作成と共有](azure-portal-dashboards.md)
