---
title: Azure Data Factory Mapping Data Flow の条件分割変換
description: Azure Data Factory Data Flow の条件分割変換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd51cc2d5f95598154b76b5da8e3fc9e4801100d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104947"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>データ フローの条件分割変換のマッピング

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![条件分割ツールボックス](media/data-flow/conditionalsplit2.png "条件分割ツールボックス")

条件分割変換では、データ行をデータの内容に応じた別のストリームにルーティングできます。 条件分割変換の実装は、プログラミング言語の CASE 決定構造と同様です。 この変換では式を評価し、その結果に基づいて、データ行を指定されたストリームに送ります。 この変換には既定の出力も用意されているので、行が式に一致しない場合は既定の出力に送られます。

![条件分割](media/data-flow/conditionalsplit1.png "条件分割オプション")

## <a name="multiple-paths"></a>複数のパス

条件をさらに追加するには、下部の構成ウィンドウで [ストリームの追加] を選択し、式ビルダーのテキスト ボックス内をクリックして式を作成します。

![条件分割マルチ](media/data-flow/conditionalsplit3.png "条件分割マルチ")

## <a name="next-steps"></a>次の手順

条件分割で使用される一般的なデータ フロー変換:[結合変換](data-flow-join.md)、[参照変換](data-flow-lookup.md)、[選択変換](data-flow-select.md)
