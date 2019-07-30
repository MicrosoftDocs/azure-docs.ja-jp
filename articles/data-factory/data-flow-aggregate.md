---
title: Mapping Data Flow の集計変換 - Azure Data Factory | Microsoft Docs
description: Azure Data Factory で Mapping Data Flow の集計変換を使用して、大規模なデータ集計を行う方法について説明します。
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 21135b26d4bc840b3fcb091e675e5e6bd24d8548
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312107"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Mapping Data Flow の集計変換 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

集計変換では、データ ストリームに含まれる列の集計を定義します。 式ビルダーを使用して、既存の列または計算列によってグループ化できる、SUM、MIN、MAX、COUNT などのさまざまな種類の集計を定義できます。

## <a name="group-by"></a>グループ化
集計で句ごとのグループ化として使用するために、既存の列を選択するか、新しい計算列を作成します。 既存の列を使用するには、ドロップダウン から目的の列を選択します。 新しい計算列を作成するには、句をポイントし、[計算列] をクリックします。 これにより、[Data Flow 式ビルダー](concepts-data-flow-expression-builder.md)が開きます。 計算列を作成したら、[Name as]\(名前\) フィールドに出力列の名前を入力します。 句ごとのグループ化を追加するには、既存の句にカーソルを合わせ、[+] をクリックします。

![集計変換のグループ化設定](media/data-flow/agg.png "集計変換のグループ化設定")

> [!NOTE]
> 集計変換では、句ごとのグループ化は省略可能です。

## <a name="aggregate-column"></a>列を集計する 
[集計] タブを選択して、集計式を作成します。 既存の列を選択して集計で値を上書きするか、新しいフィールドを新しい名前で作成します。 集計式は、列名セレクターの右側のボックスに入力されます。 式を編集するには、テキスト ボックスをクリックして式ビルダーを開きます。 別の集計を追加するには、既存の式の上にカーソルを合わせ、[+] をクリックすると、新しい集計列または[列パターン](concepts-data-flow-column-pattern.md)が作成されます。

![集計変換の集計設定](media/data-flow/agg2.png "集計変換の集計設定")

> [!NOTE]
> 各集計式には、少なくとも 1 つの集計関数が含まれている必要があります。

> [!NOTE]
> デバッグ モードでは、式ビルダーで集計関数を使用したデータのプレビューを生成することはできません。 集計変換のデータのプレビューを表示するには、式ビルダーを終了し、[データ のプレビュー] タブで確認します。

## <a name="next-steps"></a>次の手順

\- [ウィンドウ変換](data-flow-window.md)を使用してウィンドウベースの集計を定義する