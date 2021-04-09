---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673457"
---
Java プロジェクトでは、バインドは関数メソッドのバインド注釈として定義されます。 その後、これらの注釈に基づいて *function.json* ファイルが自動的に生成されます。

_src/main/java_ の下の対象の関数コードの場所を参照し、*Function.java* プロジェクト ファイルを開きます。`run` メソッド定義に、次のパラメーターを追加します。

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

`msg` パラメーターは [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 型です。これは、関数の完了時に出力バインドにメッセージとして書き込まれる文字列のコレクションを表します。 この場合、出力は `outqueue` という名前のストレージ キューです。 このストレージ アカウントの接続文字列は、`connection` メソッドによって設定されます。 接続文字列自体ではなく、ストレージ アカウントの接続文字列を含むアプリケーション設定を渡します。

`run` メソッドの定義は次の例のようになります。  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::