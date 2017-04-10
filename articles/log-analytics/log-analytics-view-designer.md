---
title: "OMS Log Analytics のビューを作成してデータを分析する | Microsoft Docs"
description: "Log Analytics のビュー デザイナーを使用すると、OMS と Azure Portal に表示されるカスタム ビューを作成し、OMS リポジトリのデータをさまざまな方法で視覚化することができます。 この記事では、ビュー デザイナーの概要と、カスタム ビューの作成と編集を行うための手順について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: d65f730a0f84b89d0228a22b42d0421ad4f2678f
ms.lasthandoff: 03/09/2017


---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Log Analytics のビュー デザイナーを使用してカスタム ビューを作成する
[Log Analytics](log-analytics-overview.md) のビュー デザイナーを使用すると、OMS コンソールで、OMS リポジトリのデータのさまざまな視覚化を含むカスタム ビューを作成できます。 この記事では、ビュー デザイナーの概要と、カスタム ビューの作成と編集を行うための手順について説明します。

ビュー デザイナーについてはこの他に次の記事があります。

* [タイル リファレンス](log-analytics-view-designer-tiles.md) - カスタム ビューで使用できるタイルごとの設定のリファレンス。 
* [視覚化パーツ リファレンス](log-analytics-view-designer-parts.md) - カスタム ビューで使用できるタイルごとの設定のリファレンス。 

## <a name="concepts"></a>概念
ビュー デザイナーで作成されるビューには、次の表に示す要素が含まれます。

| パーツ | 説明 |
|:--- |:--- |
| タイル |Log Analytics のメインの概要ダッシュボードに表示されます。  カスタム ビューに含まれる情報を視覚的にまとめたものを表示します。  タイルの種類に応じて、OMS リポジトリ内のレコードが視覚化されて表示されます。  タイルをクリックすると、カスタム ビューが開きます。 |
| カスタム ビュー |タイルをクリックすると表示されます。  1 つまたは複数の視覚化パーツが含まれます。 |
| 視覚化パーツ |1 つまたは複数の[ログ検索](log-analytics-log-searches.md)に基づく OMS リポジトリ内のデータの視覚化。  ほとんどのパーツには、高レベルの視覚化を提供するヘッダーと上位の結果の一覧が含まれています。  パーツの種類に応じて、OMS リポジトリ内のレコードが視覚化されて表示されます。  詳細レコードを提供するログ検索を実行するには、パーツの要素をクリックします。 |

![ビュー デザイナーの概要](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>ビュー デザイナーをワークスペースに追加する
ビュー デザイナーがプレビュー段階にある間は、OMS ポータルの **[設定]** セクションで **[プレビュー機能]** を選択することでワークスペースに追加する必要があります。

![プレビューの有効化](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>ビューの作成と編集
### <a name="create-a-new-view"></a>新しいビューを作成する
OMS のメイン ダッシュボードでビュー デザイナー タイルをクリックして、**ビュー デザイナー**で新しいビューを開きます。

![ビュー デザイナー タイル](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>既存のビューを編集する
ビュー デザイナーで既存のビューを編集するには、OMS のメイン ダッシュボードで該当するタイルをクリックしてビューを開きます。  次に、**[編集]** ボタンをクリックして、ビューをデザイナー ビューで開きます。

![ビューの編集](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>既存のビューを複製する
ビューを複製すると、新しいビューが作成され、ビュー デザイナーに表示されます。  新しいビューの名前は、元の名前の末尾に "コピー" が追加されたものになります。  ビューを複製するには、OMS のメイン ダッシュボードで該当するタイルをクリックしてビューを開きます。  次に、**[複製]** ボタンをクリックして、ビューをデザイナー ビューで開きます。

![クローンの複製](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>既存のビューを削除する
既存のビューを削除するには、OMS のメイン ダッシュボードで該当するタイルをクリックしてビューを開きます。  次に、**[編集]** ボタンをクリックしてビューをデザイナー ビューで開き、**[ビューの削除]** をクリックします。

![ビューの削除](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>既存のビューをエクスポートする
ビューを JSON ファイルにエクスポートして、別のワークスペースにインポートしたり、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md) で使用したりできます。  既存のビューをエクスポートするには、OMS のメイン ダッシュボードで該当するタイルをクリックしてビューを開きます。  次に、**[エクスポート]** ボタンをクリックして、ブラウザーのダウンロード フォルダーの中にファイルを作成します。  ファイルの名前は、ビューの名前に拡張子 *omsview* が付加されたものになります。

![ビューのエクスポート](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>既存のビューをインポートする
別の管理グループからエクスポートした *omsview* ファイルをインポートできます。  既存のビューをインポートする場合は、その前に新しいビューを作成しておきます。  次に、**[インポート]** ボタンをクリックし、*omsview* ファイルを選択します。  ファイルの構成が既存のビューにコピーされます。

![ビューのエクスポート](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>ビュー デザイナーの操作
ビュー デザイナーには、3 つのウィンドがあります。  **デザイン** ウィンドウは、カスタム ビューを表します。  タイルとパーツを**コントロール** ウィンドウから**デザイン** ウィンドウに追加すると、それらがビューに追加されます。  **プロパティ** ウィンドウには、タイルまたは選択したパーツのプロパティが表示されます。

![ビュー デザイナー](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>ビューのタイルを構成する
カスタム ビューは、タイルを&1; つだけ持つことができます。  **タイル** ウィンドウの **タイル** タブを選択して現在のタイルを表示するか、別のタイルを選択します。  **プロパティ** ウィンドウには、現在のタイルのプロパティが表示されます。  「[タイル リファレンス](log-analytics-view-designer-tiles.md)」の詳細情報に従ってタイルのプロパティを構成し、**[適用]** をクリックして変更を保存します。

### <a name="configure-visualization-parts"></a>視覚化パーツを構成する
ビューには、任意の数の視覚化パーツを含めることができます。  **[ビュー]** タブを選択し、ビューに追加する視覚化パーツを選択します。  **プロパティ** ウィンドウに、選択したパーツのプロパティが表示されます。  「[視覚化パーツ リファレンス](log-analytics-view-designer-parts.md)」の詳細情報に従ってビューのプロパティを構成し、**[適用]** をクリックして変更を保存します。

### <a name="delete-a-visualization-part"></a>視覚化パーツを削除する
ビューから視覚化パーツを削除するには、パーツの右上隅にある **[X]** ボタンをクリックします。

### <a name="rearrange-visualization-parts"></a>視覚化パーツの順序を変更する
ビューには、1 列の視覚化パーツのみが含まれています。  パーツをクリックし、新しい場所にドラッグすることで、既存のパーツの順序を変更します。

## <a name="next-steps"></a>次のステップ
* [タイル](log-analytics-view-designer-tiles.md)をカスタム ビューに追加します。
* [視覚化パーツ](log-analytics-view-designer-parts.md)をカスタム ビューに追加します。


