---
title: Azure Monitor でログ データを分析するビューを作成する | Microsoft Docs
description: Azure Monitor のビュー デザイナーを使用すると、Azure portal に表示されるカスタム ビューを作成し、Log Analytics ワークスペースのデータをさまざまな方法で視覚化することができます。 この記事では、ビュー デザイナーの概要と、カスタム ビューの作成と編集を行うための手順について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 33930823fbeb42011d8e2a368d17c9a21070a243
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035593"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Azure Monitor のビュー デザイナーを使用してカスタム ビューを作成する
Azure Monitor のビュー デザイナーを使用すると、Log Analytics ワークスペースでデータを視覚化するのに役立つさまざまなカスタム ビューを Azure portal で作成できます。 この記事では、ビュー デザイナーの概要と、カスタム ビューの作成と編集を行うための手順について説明します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

ビュー デザイナーの詳細については、次を参照してください。

* [タイルのリファレンス](view-designer-tiles.md):カスタム ビューで使用できる各タイルの設定に関するリファレンス ガイドです。
* [視覚化パーツのリファレンス](view-designer-parts.md):カスタム ビューで使用可能な視覚化パーツの設定のリファレンス ガイドを提供します。


## <a name="concepts"></a>概念
Azure portal の Azure Monitor **[概要]** ページにビューが表示されます。 このページは、**Azure Monitor** メニューの **[Insights]** セクションの **[More]\(詳細\)** をクリックして開きます。 各カスタム ビューのタイルは、同じワークスペースにインストールされた監視ソリューションのタイルと共に、アルファベット順に表示されます。

![[概要] ページ](media/view-designer/overview-page.png)

ビュー デザイナーで作成するビューには、次の表で説明する要素が含まれます。

| パーツ | 説明 |
|:--- |:--- |
| タイル | Azure Monitor **[概要]** ページに表示されます。 各タイルには、それが表しているカスタム ビューの視覚的な概要が表示されます。 各タイルの種類では、レコードのさまざまな視覚化が提供されます。 カスタム ビューを表示するにはタイルを選択します。 |
| カスタム ビュー | タイルを選択すると表示されます。 各ビューには、1 つまたは複数の視覚化パーツが含まれます。 |
| 視覚化パーツ | 1 つまたは複数の[ログ クエリ](../log-query/log-query-overview.md)に基づく Log Analytics ワークスペース内のデータの視覚化を表します。 ほとんどのパーツには、概要レベルの視覚化を提供するヘッダーと、上位の結果を表示する一覧が含まれています。 各パーツの種類では、Log Analytics ワークスペース内のレコードのさまざまな視覚化が提供されます。 パーツの要素を選択すると、ログ クエリが実行されて、詳細なレコードが提供されます。 |

## <a name="required-permissions"></a>必要なアクセス許可
ビューの作成または編集には、少なくとも Log Analytics ワークスペースの[共同作成者レベルのアクセス許可](manage-access.md#manage-access-using-azure-permissions)が必要です。 このアクセス許可がないと、[ビュー デザイナー] オプションがメニューに表示されません。


## <a name="work-with-an-existing-view"></a>既存のビューの使用
ビュー デザイナーで作成されたビューには、次のオプションが表示されます。

![[概要] メニュー](media/view-designer/overview-menu.png)

各オプションについては次の表で説明します。

| オプション | 説明 |
|:--|:--|
| 更新   | 最新データで表示を更新します。 | 
| ログ      | ログ クエリを使用してデータを分析するための [Log Analytics](../log-query/portals.md) が開きます。 |
| Edit       | ビュー デザイナーでビューを開き、内容と構成を編集します。  |
| 複製      | 新しいビューを作成して、ビュー デザイナーで開きます。 新しいビューの名前は、元の名前の末尾に "*コピー*" が追加されたものになります。 |
| 期間 | ビューに含まれるデータの日付と時間範囲のフィルターを設定します。 この日付範囲は、ビューのクエリで設定されたすべての日付範囲の前に適用されます。  |
| +          | ビューに定義されるカスタム フィルターを定義します。 |


## <a name="create-a-new-view"></a>新しいビューを作成する
Log Analytics ワークスペースのメニューで **[ビュー デザイナー]** タイルを選択すると、ビュー デザイナーで新しいビューを作成できます。

![ビュー デザイナー タイル](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>ビュー デザイナーを操作する
新しいビューの作成または既存のビューの編集には、ビュー デザイナーを使います。 

ビュー デザイナーには、3 つのウィンドがあります。 
* **デザイン**:作成または編集しているカスタム ビューが表示されます。 
* **コントロール**:**デザイン** ウィンドウに追加するタイルとパーツが含まれています。 
* **プロパティ**:タイルまたは選択したパーツのプロパティが表示されます。

![ビュー デザイナー](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>ビューのタイルを構成する
カスタム ビューは、タイルを 1 つだけ持つことができます。 現在のタイルを表示したり、別のタイルを選択したりするには、**コントロール** ウィンドウで**タイル**のタブを選択します。 **プロパティ** ウィンドウには、現在のタイルのプロパティが表示されます。 

[タイルのリファレンス](view-designer-tiles.md)に関するページの情報に従ってタイルのプロパティを構成し、 **[適用]** をクリックして変更を保存します。

### <a name="configure-the-visualization-parts"></a>視覚化パーツを構成する
ビューには、任意の数の視覚化パーツを含めることができます。 ビューにパーツを追加するには、 **[ビュー]** タブを選択し、視覚化パーツを選択します。 **プロパティ** ウィンドウに、選択したパーツのプロパティが表示されます。 

[視覚化パーツのリファレンス](view-designer-parts.md)に関するページの情報に従ってビューのプロパティを構成し、 **[適用]** をクリックして変更を保存します。

ビューに含まれる視覚化パーツは 1 行だけです。 既存のパーツを新しい場所にドラッグして、配置を変更できます。

ビューから視覚化パーツを削除するには、パーツの右上にある **[X]** を選択します。


### <a name="menu-options"></a>メニュー オプション
次の表では、編集モードでビューの作業を行うためのオプションを説明します。

![[編集] メニュー](media/view-designer/edit-menu.png)

| オプション | 説明 |
|:--|:--|
| 保存        | 変更を保存し、ビューを閉じます。 |
| Cancel      | 変更を破棄し、ビューを閉じます。 |
| ビューの削除 | ビューを削除します。 |
| エクスポート      | 別のワークスペースにインポートできる [Azure Resource Manager テンプレート](../../azure-resource-manager/resource-group-authoring-templates.md)にビューをエクスポートします。 ファイルの名前はビューの名前であり、拡張子は *omsview* です。 |
| [インポート]      | 別のワークスペースからエクスポートした *omsview* ファイルをインポートします。 これにより、既存のビューの構成が上書きされます。 |
| 複製       | 新しいビューを作成して、ビュー デザイナーで開きます。 新しいビューの名前は、元の名前の末尾に "*コピー*" が追加されたものになります。 |

## <a name="next-steps"></a>次の手順
* [タイル](view-designer-tiles.md)をカスタム ビューに追加します。
* カスタム ビューに[視覚化パーツ](view-designer-parts.md)を追加します。
