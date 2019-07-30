---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: 8110d0a9d574c6691322df2162ca877b031cbc59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442272"
---
バインド拡張機能をインストールする最も簡単な方法は、[拡張機能のバンドル](../articles/azure-functions/functions-bindings-register.md#extension-bundles)を有効にすることです。 バンドルを有効にすると、事前定義された一連の拡張機能パッケージが自動的にインストールされます。

拡張機能のバンドルを有効にするには、*host.json* ファイルを開き、その内容を次のコードに合わせて更新します。

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```