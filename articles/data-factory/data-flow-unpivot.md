---
title: マッピング データ フローのピボット解除変換
description: Azure Data Factory のマッピング データ フローのピボット解除変換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: e7c0a4cd6e44994c4b002fcc2e5fde441cf22283
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541653"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory のピボット解除変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ADF のマッピング データ フローで、正規化されていないデータセットをより正規化されたバージョンに変換するための方法としてピボット解除を使用して、単一のレコード内の複数の列の値を、単一の列内に同じ値を持つ複数のレコードに展開します。

![ピボット解除変換](media/data-flow/unpivot1.png "ピボット解除オプション 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>グループ化解除

![ピボット解除変換](media/data-flow/unpivot5.png "ピボット解除オプション 2")

まず、ピボット集計用にグループ化する列を設定します。 列の一覧の横にある [+] 記号を使用して、グループ化を解除する 1 つまたは複数の列を設定します。

## <a name="unpivot-key"></a>ピボット解除キー

![ピボット解除変換](media/data-flow/unpivot6.png "ピボット解除オプション 3")

ピボット キーは、ADF によって行から列にピボットされる列です。 既定では、データセット内のこのフィールドの一意の値が列にピボットされます。 ただし、必要に応じて、データセットから列の値にピボットする値を入力できます。

## <a name="unpivoted-columns"></a>ピボット解除列

![ピボット解除変換](media/data-flow//unpivot7.png "ピボット解除オプション 4")

最後に、ピボットされた値に対して使用する集計と、変換による新しい出力の投影に列をどのように表示するかを選択します。

(省略可能) 行の値から作成される新しい列の先頭、中間、および末尾に文字列を追加する名前付けパターンを設定できます。

たとえば、"売上" を "地域" によってピボット処理すると、各売上値から新しい列値が算出されるだけです。 次に例を示します。"25"、"50"、"1000" などです。ただし、"売上" というプレフィックス値を設定すれば、値に "売上" が付加されます。

![ピボット解除変換前と変換後の PO、Vendor、および Fruit 列を示す画像。Fruit 列をピボット解除キーとして使用しています。](media/data-flow/unpivot3.png)

[Column Arrangement]\(列の配置\) を [Normal]\(標準\) に設定すると、すべてのピボットされた列がグループ化され、それらの値が集計されます。 [Column Arrangement]\(列の配置\) を [Lateral]\(水平\) に設定すると、列と値が入れ替わります。

![ピボット解除変換](media/data-flow//unpivot7.png "ピボット解除オプション 5")

ピポット解除された最終的なデータの結果セットには、別個の行の値にピボット解除された列の合計が表示されます。

## <a name="next-steps"></a>次のステップ

[ピボット解除変換](data-flow-pivot.md)を使用して、行を列にピボットします。
