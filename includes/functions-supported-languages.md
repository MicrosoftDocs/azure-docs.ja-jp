---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 01e62685fa73a7f547ef5246b28fdfdf659e7afa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623524"
---
|Language                                 |1.x         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|GA (.NET Framework 4.7)|GA (.NET Core 2.2<sup>1</sup>)| GA (.NET Core 3.1)<br/>[プレビュー (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (Node 6)|GA (Node 10、8)| GA (Node 14、12、10) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.7)|GA (.NET Core 2.2<sup>1</sup>)| GA (.NET Core 3.1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|該当なし|GA (Java 8)| GA (Java 11、8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |該当なし|GA (PowerShell Core 6)| GA (PowerShell 7、Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|該当なし|GA (Python 3.7、3.6)| GA (Python 3.8、3.7、3.6) <br/> プレビュー (Python 3.9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |該当なし|GA<sup>2</sup>| GA<sup>2</sup> |


<sup>1</sup> ランタイム バージョン 2.x を対象とする .NET クラス ライブラリのアプリは、.NET Core 3.1 の .NET Core 2.x 互換モードで実行できるようになりました。 詳細については、「[Functions v2.x の考慮事項](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)」を参照してください。  
<sup>2</sup> JavaScript へのトランスパイリングによってサポートされます。

サポートされている言語バージョンの詳細については、言語固有の開発者ガイドに関する記事をご覧ください。   
言語サポートの計画的な変更については、「[Azure ロードマップ](https://azure.microsoft.com/roadmap/?tag=functions)」を参照してください。
