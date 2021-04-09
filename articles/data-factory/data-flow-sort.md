---
title: マッピング データ フローでの並べ替え変換
description: Azure Data Factory の Mapping Data の並べ替え変換
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "81606338"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>マッピング データ フローでの並べ替え変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

並べ替え変換を使用すると、現在のデータ ストリームで受信した行を並べ替えることができます。 個々の列を選択し、昇順または降順に並べ替えることができます。

> [!NOTE]
> マッピング データ フローは、複数のノードやパーティションにデータが分散される Spark クラスター上で実行されます。 後続の変換でデータのパーティションを再作成すると、データが再びシャッフルされるため、並べ替えた順序が失われる可能性があります。

## <a name="configuration"></a>構成

![並べ替えの設定](media/data-flow/sort.png "並べ替え")

**大文字と小文字の区別をしない:** 文字列やテキスト フィールドを並べ替えるときに大文字と小文字を区別するかどうか

**Sort Only Within Partitions (パーティション内でのみ並べ替え):** データ フローは spark 上で実行されるため、各データ ストリームはパーティションに分割されます。 この設定では、データ ストリーム全体を並べ替えるのではなく、受信パーティション内でのみデータを並べ替えます。 

**並べ替え条件:** 並べ替えの基準となる列と並べ替えの順序を選択します。 順序によって並べ替えの優先順位が決まります。 データ ストリームの先頭または末尾に null が出現するかどうかを選択します。

### <a name="computed-columns"></a>計算列

並べ替えを適用する前に列の値を変更または抽出するには、列の上にマウス ポインターを移動し、[計算列] を選択します。 これにより、式ビルダーが開き、列の値を使用する代わりに並べ替え操作用の式が作成されます。

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>例

![並べ替えの設定](media/data-flow/sort.png "並べ替え")

次のコード スニペットには、上記の並べ替え構成に対するデータ フロー スクリプトが含まれています。

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>次のステップ

並べ替え後に、[集計変換](data-flow-aggregate.md)を使用できます
