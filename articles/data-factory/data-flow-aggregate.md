---
title: Azure Data Factory Mapping Data Flow の集計変換
description: Azure Data Factory Data Flow の集計変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 7b488b243c0520befb6b5470598f460b5a759fed
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730038"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Azure Data Factory Mapping Data Flow の集計変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

集計変換では、データ ストリームに含まれる列の集計を定義します。 式ビルダーで、さまざまな種類の集計 (SUM、MIN、MAX、COUNT など) を定義し、出力に新しいフィールドを作成して、これらの集計と省略可能な group-by フィールドを含めることができます。

![集計変換のオプション](media/data-flow/agg.png "集計 1")

## <a name="group-by"></a>グループ化
(省略可能) 集計用に Group-by 句を選択し、既存の列の名前か新しい名前を使用します。 [列の追加] を使って group-by 句をさらに追加し、列名の横にあるテキスト ボックス内をクリックして式ビルダーを起動したら、グループ化のために、既存の列、列の組み合わせ、または式のいずれかを選択します。

## <a name="the-aggregate-column-tab"></a>[集計列] タブ 
(必須) [集計列] タブを選択して集計式を作成します。 集計で値を上書きする既存の列を選択するか、集計用の新しい名前で新しいフィールドを作成します。 集計に使用する式は、列名セレクターの横にある右側のボックスに入力します。 このテキスト ボックスをクリックすると、式ビルダーが開きます。

![集計変換のオプション](media/data-flow/agg2.png "アグリゲーター")

## <a name="data-preview-in-expression-builder"></a>式ビルダーでのデータのプレビュー

デバッグ モードでは、式ビルダーで集計関数を使ったデータのプレビューを生成することはできません。 集計変換のデータのプレビューを表示するには、式ビルダーを終了し、データ フロー デザイナーからデータ プロファイルを確認します。
