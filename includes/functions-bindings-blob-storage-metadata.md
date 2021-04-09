---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: c6b038297945ca900508a822460e1358a2524d23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455918"
---
BLOB トリガーは、いくつかのメタデータ プロパティを提供します。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 これらの値は、[CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob) 型と同じセマンティクスを持ちます。

|プロパティ  |Type  |説明  |
|---------|---------|---------|
|`BlobTrigger`|`string`|トリガーする BLOB のパス。|
|`Uri`|`System.Uri`|プライマリ ロケーションの BLOB URI。|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blob のシステム プロパティ。 |
|`Metadata` |`IDictionary<string,string>`|BLOB のユーザー定義メタデータ。|

たとえば、次の C# スクリプトと JavaScript の例では、トリガーする BLOBへのパスがログに記録されます (コンテナーを含む)。

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```