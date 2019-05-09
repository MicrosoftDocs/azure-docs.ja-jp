---
title: Azure Data Factory の Mapping Data Flow の選択変換
description: Azure Data Factory の Mapping Data Flow の選択変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: bc83b41067d587adce41658a2c4b3d68969750ba
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729340"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure Data Factory の Mapping Data Flow の選択変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

この変換は、列を選択する (行数を減らす) ため、または列とストリームの名前をエイリアス化するために使用します。

選択変換では、ストリーム全体またはそのストリーム内の列をエイリアス化して別の名前 (エイリアス) を割り当てておき、それらの新しい名前をデータ フロー内で参照できるようにします。 この変換は、自己結合シナリオで役に立ちます。 ADF の Data Flow 内で自己結合を実装する方法は、ストリームを指定し、"新しいブランチ" を分岐させ、その直後に "選択" 変換を追加することです。 これで、そのストリームには、元のストリームに結合するために使用できる新しい名前が付けられます (自己結合が作成されます)。

![自己結合](media/data-flow/selfjoin.png "自己結合")

上の図では、選択変換が一番上にあります。 これは、元のストリームを "OrigSourceBatting" にエイリアス化しています。 下部の強調表示されている結合変換で、この選択変換エイリアス ストリームが右側の結合として使用され、内部結合の右側と左側の両方で同じキーを参照できることがわかります。

選択変換は、データ フローから列を選択解除する方法としても使用できます。 たとえば、シンクに 6 つの列が定義されているが、特定の 3 つの列のみを選択して変換した後、シンクにフローする場合は、選択変換を使用してこれら 3 つの列のみを選択できます。

> [!NOTE]
> 特定の列のみを選択するには、[すべて選択] をオフに切り替える必要があります。

オプション

"選択" 変換の既定の設定は、受信するすべての列を処理し、それらの元の名前を保持することです。 選択変換の名前を設定することで、ストリームをエイリアス化できます。

個々の列をエイリアス化する場合は、[すべて選択] をオフにし、下部にある列マッピングを使用します。

![選択変換](media/data-flow/select001.png "選択のエイリアス")
