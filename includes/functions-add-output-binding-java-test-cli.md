---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673276"
---
## <a name="update-the-tests"></a>テストを更新する

アーキタイプはテストのセットも作成するため、`run` メソッド シグネチャ内の新しい `msg` パラメーターを処理するためにこれらのテストを更新する必要があります。  

_src/main/java_ の下の対象のテスト コードの場所を参照し、*Function.java* プロジェクト ファイルを開きます。`//Invoke` の下のコード行を次のコードに置き換えます。

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::