---
title: Azure Data Factory Mapping Data Flow の存在変換
description: Azure Data Factory Mapping Data Flow の存在変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6ce27ba699ae766ed4d2428f67d91379464bb9f1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783464"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Azure Data Factory Mapping Data Flow の存在変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

存在変換は、データ内の行のフローを停止または許可する行のフィルター変換です。 存在変換は ```SQL WHERE EXISTS``` および ```SQL WHERE NOT EXISTS``` と同様です。 フィルター変換の後、データ ストリームからの結果の行には、ソース 1 の列の値がソース 2 に存在するすべての行か、またはソース 1 の列の値がソース 2 に存在しないすべての行が含まれます。

![存在の設定](media/data-flow/exsits.png "存在 1")

Data Flow がストリーム 1 の値をストリーム 2 と比較できるように、存在の 2 番目のソースを選択します。

ソース 1 とソース 2 から、存在の有無を調べる値を持つ列を選択します。

## <a name="multiple-exists-conditions"></a>複数存在条件

存在の列条件の各行の横に、各行の上にカーソルと置くと + 記号が表示されます。 これによって、存在条件に対して複数の行を追加できます。

## <a name="next-steps"></a>次の手順

