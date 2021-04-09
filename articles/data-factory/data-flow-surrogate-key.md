---
title: マッピング データ フローの代理キー変換
description: Azure Data Factory のマッピング データ フロー代理キー変換を使用して順次キー値を生成する方法
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
ms.openlocfilehash: d1f8993b1adc297b1bfadba114df76a66e59afa2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93147184"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>マッピング データ フローの代理キー変換 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

データの各行に増分キー値を追加するには、代理キー変換を使用します。 これは、スター スキーマ分析データ モデルでディメンション テーブルを設計する場合に便利です。 スター スキーマでは、ディメンション テーブル内の各メンバーには、ビジネス キー以外の一意のキーが必要です。

## <a name="configuration"></a>構成

![代理キー変換](media/data-flow/surrogate.png "代理キー変換")

**キー列:** 生成された代理キー列の名前。

**開始値:** 生成される最小のキー値。

## <a name="increment-keys-from-existing-sources"></a>既存のソースからの増分キー

ソース内に存在する値からシーケンスを開始するには、キャッシュ シンクを使用してその値を保存し、派生列変換を使用して 2 つの値をまとめて追加することをお勧めします。 キャッシュされた参照を使用して出力を取得し、生成されたキーにそれを追加します。 詳細については、[キャッシュ シンク](data-flow-sink.md#cache-sink)および[キャッシュされた参照](concepts-data-flow-expression-builder.md#cached-lookup)に関するページを参照してください。

![代理キー参照](media/data-flow/cached-lookup-example.png "代理キー参照")

### <a name="increment-from-existing-maximum-value"></a>既存の最大値からの増分

以前の最大によってキー値をシードするには、ソース データがある場所に基づいて 2 つの手法を使用できます。

#### <a name="database-sources"></a>データベース ソース

SQL クエリ オプションを使用して、ソースから MAX () を選択します。 たとえば、`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>` のようにします。

![代理キー クエリ](media/data-flow/surrogate-key-max-database.png "代理キー変換のクエリ")

#### <a name="file-sources"></a>ファイル ソース

前の最大値がファイル内にある場合は、集計変換内で `max()` 関数を使用して、前の最大値を取得します。

![代理キー ファイル](media/data-flow/surrogate-key-max-file.png "代理キー ファイル")

どちらの場合も、キャッシュ シンクに書き込み、値を参照する必要があります。 


## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>例

![代理キー変換](media/data-flow/surrogate.png "代理キー変換")

次のコード スニペットには、上記の代理キー構成に対するデータ フロー スクリプトが含まれています。

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>次のステップ

これらの例では、[結合](data-flow-join.md)と[派生列](data-flow-derived-column.md)の変換を使用します。
