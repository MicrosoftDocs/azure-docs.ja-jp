---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aad66a91f7de8380ac7e87f0ce8e35ed43cac4a6
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594532"
---
特定の関数アプリ内のすべての関数のコードは、ホスト構成ファイルと 1 つ以上のサブフォルダーを含むルート プロジェクト フォルダーにあります。 各サブフォルダーには、個別の関数のコードが含まれています。 フォルダー構造は、次の図のように表示されます。

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Functions ランタイムのバージョン 2.x では、関数アプリ内のすべての関数が同じ言語スタックを共有する必要があります。  

[host.json](../articles/azure-functions/functions-host-json.md) ファイルにはランタイム固有の構成が含まれています。このファイルは関数アプリのルート フォルダーにあります。 *bin* フォルダーには、関数アプリに必要なパッケージやその他のライブラリ ファイルが含まれています。 関数アプリ プロジェクトの言語固有の要件を参照してください。

* [C# クラス ライブラリ (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# スクリプト (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# スクリプト](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



