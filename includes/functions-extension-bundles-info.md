---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "78201023"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> 起動中、[Storage バインディング拡張機能](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher)など、Microsoft のバインディング拡張機能がホストによってダウンロードされてインストールされます。 このインストールが実行される理由は、*host.json* ファイルでバインディング拡張機能が次のプロパティによって既定で有効になっているためです。
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> バインディング拡張機能に関連したエラーが発生した場合は、*host.json* に上記のプロパティが存在することを確認してください。
::: zone-end  