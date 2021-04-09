---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7ef3bd0f401ba54d56ed42df34cd2e761681dbc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904075"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-an-output-binding-definition-to-the-function"></a>出力バインディングの定義を関数に追加する

関数に割り当てることができるトリガーは 1 つだけですが、入力と出力のバインディングは複数割り当てることができます。これらを使用すると、カスタム統合コードを記述しなくても、他の Azure サービスやリソースに接続できます。 
::: zone-end
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
関数フォルダーの *function.json* ファイルでそれらのバインディングを宣言します。 前のクイックスタートの *HttpExample* フォルダーにある *function.json* ファイルでは、`bindings` コレクション内に 2 つのバインディングが含まれています。  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
それぞれのバインディングには、少なくとも型、方向、名前があります。 上の例の 1 つ目のバインディングは、型が `httpTrigger` で、方向が `in` になっています。 `in` 方向の場合、トリガーによって呼び出された関数に送信される入力パラメーターの名前が `name` で指定されます。  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
コレクションの 2 つ目のバインディングの名前は `res` です。 この `http` バインディングは、HTTP 応答の書き込みに使用される出力バインディング (`out`) です。 

この関数から Azure Storage キューに書き込みを行うには、次のコードで示すように、`queue` 型の `out` バインディングを `msg` という名前で追加します。

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
コレクションの 2 つ目のバインディングは、型が `http` で方向が `out` です。この場合、このバインディングは入力パラメーターを渡すものではなく、関数の戻り値を使用するものであることが、`$return` という特殊な `name` からわかります。

この関数から Azure Storage キューに書き込みを行うには、次のコードで示すように、`queue` 型の `out` バインディングを `msg` という名前で追加します。

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
コレクションの 2 つ目のバインディングの名前は `res` です。 この `http` バインディングは、HTTP 応答の書き込みに使用される出力バインディング (`out`) です。 

この関数から Azure Storage キューに書き込みを行うには、次のコードで示すように、`queue` 型の `out` バインディングを `msg` という名前で追加します。

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
このケースでは、`msg` が出力引数として関数に与えられています。 型 `queue` では、`queueName` にキューの名前を指定し、(*local.settings.json* から得られる) Azure Storage 接続の "*名前*" を `connection` に指定する必要もあります。 
::: zone-end  
