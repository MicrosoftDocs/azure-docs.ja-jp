---
title: Azure Data Factory Data Flow のノードの移動
description: Azure Data Factory Data Flow ダイアグラムのノードの移動方法
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d82b32e876144a626333f3df1481c5fce9862067
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732179"
---
# <a name="mapping-data-flow-move-nodes"></a>Mapping Data Flow のノードの移動

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![集計変換のオプション](media/data-flow/agghead.png "アグリゲーターのヘッダー")

Azure Data Factory Data Flow のデザイン サーフェスは、上から下、左から右の方向にデータ フローを構築する "構築" サーフェスです。 各変換にアタッチされたツールボックスとプラス (+) 記号があります。 自由形式の DAG 環境では、エッジを介してノードを接続することではなく、ビジネス ロジックに集中できます。

ドラッグ アンド ドロップのパラダイムがない場合、変換ノードを "移動" する方法として、受信ストリームの変更があります。 代わりに、"受信ストリーム" を変更することで変換を移動します。

Azure Data Factory Data Flow では、ストリームはデータのフローを表します。 変換設定ウィンドウに、[Incoming Stream]\(受信ストリーム\) フィールドが表示されます。 これは、どの受信データ ストリームがその変換をフィードしているかを示します。 グラフ上の変換ノードの物理的な場所を変更するには、受信ストリーム名をクリックし、別のデータ ストリームを選択します。 そのストリーム上の後続のすべての変換と共に現在の変換が新しい場所に移動します。

その後で 1 つ以上の変換を含む変換を移動する場合、新しい分岐を介してデータ フロー内の新しい場所が結合されます。

選択したノードの後に後続の変換がない場合は、その変換のみが新しい場所に移動します。
