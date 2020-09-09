---
title: マッピング データ フローの結合変換
description: Azure Data Factory マッピング データ フローの結合変換を使用して 2 つのデータ ソースのデータを結合する
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: f95f35fe0d17afdeec864674d3360fc3b172cad1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683369"
---
# <a name="join-transformation-in-mapping-data-flow"></a>マッピング データ フローの結合変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

マッピング データ フローで 2 つのソースまたはストリームのデータを結合するには、結合変換を使用します。 出力ストリームには、結合条件に基づいて一致と判定された、両ソースのすべての列が含まれます。 

## <a name="join-types"></a>結合の種類

現在、マッピング データ フローでは、5 種類の結合がサポートされています。

### <a name="inner-join"></a>内部結合

内部結合では、両方のテーブルで値が一致する行のみが出力されます。

### <a name="left-outer"></a>左外部結合

左外部結合では、左側ストリームのすべての行と、右側ストリームで一致するレコードが返されます。 左側ストリームの行に一致するものがない場合、右側ストリームの出力列は NULL に設定されます。 出力は、内部結合で返される行と、左側ストリームの一致のない行になります。

> [!NOTE]
> データ フローによって使用される Spark エンジンは、結合条件でデカルト積が生成されることがあるという理由で、失敗する場合があります。 これが発生する場合は、カスタム クロス結合に切り替えて結合条件を手動で入力することができます。 これによりデータ フローのパフォーマンスが低下する場合があります。これは、実行エンジンが、リレーションシップの両側からすべての行を計算し、行をフィルター処理する必要があるためです。

### <a name="right-outer"></a>右外部結合

右外部結合では、右側ストリームのすべての行と、左側ストリームで一致するレコードが返されます。 右側ストリームの行に一致するものがない場合、左側ストリームの出力列は NULL に設定されます。 出力は、内部結合で返される行と、右側ストリームの一致のない行になります。

### <a name="full-outer"></a>完全外部結合

完全外部結合では、両方の側のすべての列と行が、一致しない列には NULL 値を使用して出力されます。

### <a name="custom-cross-join"></a>カスタム クロス結合

クロス結合では、条件に基づいて 2 つのストリームのクロス積が出力されます。 同等性以外の条件を使用する場合は、クロス結合条件にカスタム式を指定します。 出力ストリームは、結合条件を満たすすべての行になります。

この結合の種類は、非等結合および ```OR``` 条件に使用できます。

完全なデカルト積を明示的に生成する場合は、結合の前に 2 つの独立したストリームの派生列変換を使用して、一致する合成キーを作成します。 たとえば、```SyntheticKey``` と呼ばれる各ストリームで派生列に新しい列を作成し、それを ```1``` と等しい値に設定します。 次に、```a.SyntheticKey == b.SyntheticKey``` をカスタム結合式として使用します。

> [!NOTE]
> カスタム クロス結合で、左右のリレーションシップの各側に少なくとも 1 つの列が含まれていることを確認してください。 各側の列ではなく、静的な値を使用してクロス結合を実行すると、データセット全体が完全にスキャンされるため、データフローのパフォーマンスが低下します。

## <a name="configuration"></a>構成

1. **[右側ストリーム]** ドロップダウンで、結合するデータ ストリームを選択します。
1. 目的の**結合の種類**を選択します
1. 結合条件で照合に使用するキー列を選択します。 既定では、データフローは、1 つの列について各ストリームでの同等性を検索します。 計算値で比較するには、列のドロップダウン上にマウス ポインターを移動し、 **[計算列]** を選択します。

![結合変換](media/data-flow/join.png "Join")

### <a name="non-equi-joins"></a>非等結合

結合条件で等しくない (!=) またはより大きい (>) などの条件演算子を使用するには、2 つの列の間の演算子ドロップダウンを変更します。 非等結合では、 **[最適化]** タブで **[固定]** ブロードキャストを使用して、2 つのストリームのうち少なくとも 1 つをブロードキャストする必要があります。

![非等結合](media/data-flow/non-equi-join.png "非等結合")

## <a name="optimizing-join-performance"></a>結合のパフォーマンスの最適化

SSIS などのツールでのマージ結合とは異なり、結合変換は強制的なマージ結合操作ではありません。 結合キーを並べ替える必要はありません。 結合操作は、Spark の最適な結合操作 (ブロードキャスト結合またはマップ側の結合) に基づいて行われます。

![結合変換の最適化](media/data-flow/joinoptimize.png "結合の最適化")

結合変換、参照変換、および存在変換では、一方または両方のデータ ストリームがワーカー ノードのメモリに収まる場合、**ブロードキャスト**を有効にすることでパフォーマンスを最適化できます。 既定では、ある一方をブロードキャストするかどうかは、Spark エンジンによって自動的に決定されます。 ブロードキャストする側を手動で選択するには **[Fixed]\(固定\)** を選択します。

**[オフ]** オプションを使用してブロードキャストを無効にすることは、結合でタイムアウト エラーが発生する場合を除いて推奨されません。

## <a name="self-join"></a>自己結合

1 つのデータ ストリームを自己結合させるには、選択変換を使用して既存のストリームをエイリアス化します。 変換の横にあるプラス記号アイコンをクリックして **[新しいブランチ]** を選択し、新しいブランチを作成します。 選択変換を追加して、元のストリームをエイリアス化します。 結合変換を追加し、元のストリームを**左側ストリーム**、選択変換を**右側ストリーム**として選択します。

![自己結合](media/data-flow/selfjoin.png "自己結合")

## <a name="testing-join-conditions"></a>結合条件のテスト

デバッグ モードでデータ プレビューを使用して結合変換のテストを行う場合は、小さな既知のデータ セットを使用してください。 大きなデータセットから行をサンプリングすると、テストでどの行とキーが読み取られるのかを予測できなくなります。 結果が確定的なものとならず、結合条件で一致するものが返されなくなる可能性があります。

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>内部結合の例

以下の例は、左側ストリーム `TripData` と右側ストリーム `TripFare` を受け取る `JoinMatchedData` という名前の結合変換です。  結合条件の式は `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` です。この式では、各ストリームの `hack_license` 列、`medallion` 列、`vendor_id` 列、および `pickup_datetime` 列が一致する場合に true が返されます。 `joinType` は `'inner'` です。 左側ストリームでのみブロードキャストを有効にするため、`broadcast` の値を `'left'` に設定しています。

Data Factory UX では、この変換は次の図のようになります。

![結合の例](media/data-flow/join-script1.png "結合の例")

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>カスタム クロス結合の例

以下の例は、左側ストリーム `LeftStream` と右側ストリーム `RightStream` を受け取る `JoiningColumns` という名前の結合変換です。 この変換は 2 つのストリームを取り込んで、列 `leftstreamcolumn` が列 `rightstreamcolumn` よりも大きいすべての行を結合します。 `joinType` は `cross` です。 ブロードキャストが有効になっていません。`broadcast` の値は `'none'` です。

Data Factory UX では、この変換は次の図のようになります。

![結合の例](media/data-flow/join-script2.png "結合の例")

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>次のステップ

データの結合後は、[派生列の作成](data-flow-derived-column.md)や、配布先データ ストアへのデータの[シンク](data-flow-sink.md)を行います。
