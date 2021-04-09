---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "78190919"
---
C# クラス ライブラリ プロジェクトでは、バインドは関数メソッドのバインド属性として定義されます。 その後、Functions に必要な *function.json* ファイルが、これらの属性に基づいて自動的に生成されます。

*HttpExample.cs* プロジェクト ファイルを開いて、`Run` メソッドの定義に次のパラメーターを追加します。

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg` パラメーターは `ICollector<T>` 型です。これは、関数の完了時に出力バインドに書き込まれるメッセージのコレクションを表します。 この場合、出力は `outqueue` という名前のストレージ キューです。 このストレージ アカウントの接続文字列は、`StorageAccountAttribute` で設定されます。 この属性は、ストレージ アカウントの接続文字列を含む設定を示し、クラス、メソッド、パラメーター レベルで適用できます。 この例では、既定のストレージ アカウントを既に使用しているため、`StorageAccountAttribute` を省略できます。

Run メソッドの定義は次のようになります。  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
