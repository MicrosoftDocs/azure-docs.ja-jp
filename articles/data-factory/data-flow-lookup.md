---
title: Azure Data Factory の Mapping Data Flow の参照変換
description: Azure Data Factory の Mapping Data Flow の参照変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef473ea5f88b9108894787785fe1e9083fab1b0a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788005"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory の Mapping Data Flow の参照変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

参照を使用して、別のソースの参照データをデータ フローに追加します。 参照変換では、参照テーブルをポイントし、キー フィールドが一致する定義済みのソースが必要です。

![参照変換](media/data-flow/lookup1.png "参照")

受信するストリームのフィールドと参照元のフィールドの間で一致させるキー フィールドを選択します。 データ フロー デザイン キャンバスで、参照の右側として使用する新しいソースを先に作成しておく必要があります。

一致が見つかると、参照元から結果の行と列がデータ フローに追加されます。 データ フローの最後でシンクに書き込む、関心のあるフィールドを選択できます。

## <a name="optimizations"></a>最適化

Data Factory では、データ フローがスケールアウト Spark 環境で実行されます。 データセットがワーカー ノードのメモリ領域に収まる場合、参照のパフォーマンスを最適化することができます。

![ブロードキャスト結合](media/data-flow/broadcast.png "ブロードキャスト結合")

### <a name="broadcast-join"></a>ブロードキャスト結合

参照リレーションシップの左側または右側からデータセット全体をメモリにプッシュするよう ADF に要求するには、左側または右側のブロードキャスト結合を選択します。

### <a name="data-partitioning"></a>データのパーティション分割

参照変換の [最適化] タブの [Set Partitioning]\(パーティションの設定\) を選択し、データのパーティション分割を指定することで、ワーカーごとにメモリに収めることができるデータのまとまりを作成することもできます。

## <a name="next-steps"></a>次の手順

[結合](data-flow-join.md)変換と[存在](data-flow-exists.md)変換は、ADF Mapping Data Flow で同様のタスクを実行します。 引き続き、これらの変換について見ていきましょう。
