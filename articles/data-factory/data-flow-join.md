---
title: Azure Data Factory Data Flow の結合変換
description: Azure Data Factory Data Flow の結合変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 18f713198ef9aa45cb72a6718c0f7b086c019258
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540042"
---
# <a name="mapping-data-flow-join-transformation"></a>Mapping Data Flow の結合変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

データ フロー内の 2 つのテーブルのデータを結合するには、結合を使用します。 左側のリレーションシップになる変換をクリックし、ツールボックスから結合変換を追加します。 結合変換の内部で、右側のリレーションシップになる別のデータ ストリームをデータ フローから選択します。

![結合変換](media/data-flow/join.png "結合")

## <a name="join-types"></a>結合の種類

結合変換では、結合の種類を選択する必要があります。

### <a name="inner-join"></a>内部結合

内部結合では、両方のテーブルから列の条件に一致する行だけがパスされます。

### <a name="left-outer"></a>左外部結合

結合条件を満たしていない左側ストリームのすべての行がパスされ、もう一方のテーブルからの出力列と、内部結合によって返されるすべての行が NULL に設定されます。

### <a name="right-outer"></a>右外部結合

結合条件を満たしていない右側ストリームのすべての行がパスされ、もう一方のテーブルに対応する出力列と、内部結合によって返されるすべての行が NULL に設定されます。

### <a name="full-outer"></a>完全外部結合

完全外部結合では、両側からすべての列と行が取り出され、もう一方のテーブルに存在しない列に対しては NULL 値が設定されます。

### <a name="cross-join"></a>クロス結合

式を使用して 2 つのストリームを外積するよう指定します。 これを使用して、カスタム結合条件を作成できます。

## <a name="specify-join-conditions"></a>結合条件を指定する

左結合の条件は、結合の左側に接続されているデータ ストリームからのものです。 右結合の条件は、結合の下部に接続されている 2 つ目のデータ ストリームで、別のストリームへの直接のコネクタまたは別のストリームへの参照のいずれかになります。

少なくとも 1 (1..n) 個の結合条件を入力する必要があります。 これらには、直接参照されるフィールド、ドロップダウン メニューから選択するフィールド、または式を指定できます。

## <a name="join-performance-optimizations"></a>結合のパフォーマンスの最適化

SSIS などのツールのマージ結合とは異なり、ADF Data Flow の結合は強制的なマージ結合操作ではありません。 そのため、最初に結合キーを並べ替える必要がありません。 結合操作は、Spark での最適な結合操作に基づき、Spark でDatabricks を使用して行われます。ブロードキャスト/マップ側の結合:

![結合変換の最適化](media/data-flow/joinoptimize.png "結合の最適化")

データセットが Databricks ワーカー ノードのメモリに収まる場合、結合のパフォーマンスは自動的に最適化されます。 結合操作でデータのパーティション分割を指定して、各ワーカーのメモリにより適したデータセットを作成することもできます。

## <a name="self-join"></a>自己結合

選択変換を使って既存のストリームをエイリアス化することで、ADF Data Flow で自己結合の条件を作成できます。 まず、ストリームから "新しい分岐" を作成します。次に、元のストリーム全体をエイリアス化するために選択を追加します。

![自己結合](media/data-flow/selfjoin.png "自己結合")

上の図では、先頭に選択変換があります。 これは、元のストリームを "OrigSourceBatting" にエイリアス化しています。 下部の強調表示されている結合変換で、この選択エイリアス ストリームを右側の結合として使用して、内部結合の右側と左側の両方で同じキーを参照できるようになっていることがわかります。

## <a name="composite-and-custom-keys"></a>複合キーおよびカスタム キー

カスタム キーや複合キーは、結合変換内でその場で構築できます。 リレーションシップの各行の横にあるプラス記号 (+) を使用して、結合列の行を追加します。 または、その場で結合値を求める式ビルダーで新しいキー値を計算します。

## <a name="next-steps"></a>次の手順

データの結合後は、[新しい列を作成](data-flow-derived-column.md)したり、[ご自分のデータを宛先データ ストアにシンク](data-flow-sink.md)したりできます。
