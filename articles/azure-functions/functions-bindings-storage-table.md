---
title: Azure Functions における Azure Table Storage のバインド
description: Azure Functions で Azure Table のバインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4f2b890dc60cd50b5fcaefabe8d418268b738c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92096727"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Functions における Azure Table Storage のバインド

Azure Functions は[トリガーとバインド](./functions-triggers-bindings.md)を使用して [Azure Storage](../storage/index.yml) と統合されます。 Table Storage との統合により、Table Storage データの読み取りと書き込みを行う関数を作成できるようになります。

| アクション | Type |
|---------|---------|
| 関数でテーブル ストレージ データを読み取る | [入力バインド](./functions-bindings-storage-table-input.md) |
| 関数からテーブル ストレージ データを書き込めるようにする |[出力バインド](./functions-bindings-storage-table-output.md) |

## <a name="packages---functions-2x-and-higher"></a>パッケージ - Functions 2.x 以降

Table ストレージ バインディングは [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet パッケージ、バージョン 3.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Table ストレージ バインディングは [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージ、バージョン 2.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub リポジトリにあります。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>次の手順

- [関数の実行時にテーブル ストレージ データを読み取る](./functions-bindings-storage-table-input.md)
- [関数からテーブル ストレージ データを書き込む](./functions-bindings-storage-table-output.md)
