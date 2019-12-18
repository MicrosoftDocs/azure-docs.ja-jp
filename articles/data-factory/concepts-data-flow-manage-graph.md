---
title: データ フローグラフ
description: Data Factory のデータ フロー グラフの操作方法
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: da180bfb1aec29fa15b070fd73ba84d708ada927
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928304"
---
# <a name="mapping-data-flow-graphs"></a>データ フロー グラフのマッピング

マッピング データ フローのデザイン サーフェスは、上から下、左から右の方向にデータ フローを構築する "構築" サーフェスです。 各変換にアタッチされたツールボックスとプラス (+) 記号があります。 自由形式の DAG 環境では、エッジを介してノードを接続することではなく、ビジネス ロジックに集中できます。

データ フロー グラフを管理するための組み込みのメカニズムを以下に示します。

## <a name="move-nodes"></a>ノードの移動

![集計変換のオプション](media/data-flow/agghead.png "アグリゲーター ヘッダー")

ドラッグ アンド ドロップのパラダイムがない場合、変換ノードを "移動" する方法は、受信ストリームを変更することです。 代わりに、"受信ストリーム" を変更することで変換を移動します。

## <a name="streams-of-data-inside-of-data-flow"></a>データ フロー内のデータのストリーム

Azure Data Factory Data Flow では、ストリームはデータのフローを表します。 変換設定ウィンドウに、[Incoming Stream]\(受信ストリーム\) フィールドが表示されます。 これは、どの受信データ ストリームがその変換をフィードしているかを示します。 グラフ上の変換ノードの物理的な場所を変更するには、受信ストリーム名をクリックし、別のデータ ストリームを選択します。 そのストリーム上の後続のすべての変換と共に現在の変換が新しい場所に移動します。

その後で 1 つ以上の変換を含む変換を移動する場合、新しい分岐を介してデータ フロー内の新しい場所が結合されます。

選択したノードの後に後続の変換がない場合は、その変換のみが新しい場所に移動します。

## <a name="hide-graph-and-show-graph"></a>グラフの非表示とグラフの表示

下部の [構成] ペインの右端にボタンがあります。ここでは、変換の構成を操作するときに下のペインを全画面表示に展開できます。 これにより、[前へ] ボタンと [次へ] ボタンを使用して、グラフの構成を移動できます。 グラフ ビューに戻るには、下向きボタンをクリックすると、分割画面に戻ります。

## <a name="search-graph"></a>グラフの検索

グラフは、デザイン サーフェスの [検索] ボタンを使って検索できます。

![Search](media/data-flow/search001.png "グラフの検索")

## <a name="next-steps"></a>次の手順

Data Flow のデザインが完了したら、デバッグ ボタンをオンにして、デバッグ モードでテストします。この作業は、[データ フロー デザイナー](concepts-data-flow-debug-mode.md)または[パイプライン デバッグ](control-flow-execute-data-flow-activity.md)のいずれかで直接行います。
