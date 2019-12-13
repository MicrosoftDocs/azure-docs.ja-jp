---
title: マッピング データ フローの存在変換
description: Azure Data Factory マッピング データ フローでの存在変換を使用して、既存の行を確認します
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: efcc45dcf3565b70305323701810c49c4a720394
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930404"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>マッピング データ フローの存在変換

存在変換は、ご利用のデータが別のソースまたはストリームに存在するかどうかを確認する行のフィルター変換です。 出力ストリームには、左側ストリームに存在するすべての行が含まれます。これらの行には、右側ストリームに存在する行も存在しない行もあります。 存在変換は ```SQL WHERE EXISTS``` および ```SQL WHERE NOT EXISTS``` と同様です。

## <a name="configuration"></a>構成

1. **[右側ストリーム]** ドロップダウンで、存在しているかどうかを確認するデータ ストリームを選択します。
1. **[Exist type]\(存在の種類\)** 設定では存在するデータまたは存在しないデータを探すかどうかを指定します。
1. **カスタム式**を使用するかどうかを選択します。
1. 存在条件として比較するキー列を選択します。 既定では、データフローは、1 つの列について各ストリームでの同等性を検索します。 計算値で比較するには、列のドロップダウン上にマウス ポインターを移動し、 **[計算列]** を選択します。

![[存在] 設定](media/data-flow/exists.png "存在 1")

### <a name="multiple-exists-conditions"></a>複数存在条件

各ストリームからの複数の列を比較するには、既存の行の横にある正符号アイコンをクリックして、新しい存在条件を追加します。 それぞれの追加の条件は、"and" ステートメントによって結合されます。 2 つの列を比較することは、次の式と同じです。

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>カスタム式

"and" および "equals to" 以外の演算子を含む自由形式の式を作成するには、 **[カスタム式]** フィールドを選択します。 青いボックスをクリックすると、データ フロー式ビルダーを介してカスタム式を入力できます。

![[存在] のカスタム設定](media/data-flow/exists1.png "存在のカスタム")

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>例

以下の例は、左側ストリーム `NameNorm2` と右側ストリーム `TypeConversions` を取る `checkForChanges` という名前の存在変換です。  存在条件の式は `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` です。この式では、各ストリームで `EMPID` 列と `Region` 列の両方が一致する場合に true が返されます。 存在を確認している間、`negate` は false になります。 最適化タブでブロードキャストを有効にしていないので、`broadcast` の値は `'none'` になります。

Data Factory UX でのこの変換は次の図のようになります。

![存在の例](media/data-flow/exists-script.png "存在の例")

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>次の手順

同様の変換として、[参照](data-flow-lookup.md)と[結合](data-flow-join.md)があります。
