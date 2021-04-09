---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "78191051"
---
次のコードに合わせて *HttpExample\\\_\_init\_\_.py* を更新します。関数の定義に `msg` パラメーターを、`if name:` ステートメントの下に `msg.set(name)` を追加してください。

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

`msg` パラメーターは、[`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) のインスタンスです。 その `set` メソッドでは、文字列メッセージ (この場合、URL のクエリ文字列として関数に渡された名前) をキューに書き込みます。
