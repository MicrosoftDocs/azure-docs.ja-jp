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
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641875"
---
BLOB トリガーは、いくつかのメタデータ プロパティを提供します。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 これらの値は、[CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) 型と同じセマンティクスを持ちます。

|プロパティ  |Type  |説明  |
|---------|---------|---------|
|`BlobTrigger`|`string`|トリガーする BLOB のパス。|
|`Uri`|`System.Uri`|プライマリ ロケーションの BLOB URI。|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blob のシステム プロパティ。 |
|`Metadata` |`IDictionary<string,string>`|BLOB のユーザー定義メタデータ。|

たとえば、次の C# スクリプトと JavaScript の例では、トリガーする BLOBへのパスがログに記録されます (コンテナーを含む)。

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
