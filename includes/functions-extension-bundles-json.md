---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689561"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

`extensionBundle` では次のプロパティを使用できます。

| プロパティ | 説明 |
| -------- | ----------- |
| id | Microsoft Azure Functions 拡張機能バンドルの名前空間。 |
| version | インストールするバンドルのバージョン。 Functions ランタイムは常に、バージョン範囲 (間隔) で定義された最大許容バージョンを選択します。 上記のバージョン値には、1.0.0 から 2.0.0 未満までのすべてのバンドル バージョンを指定できます。 詳細については、[バージョン範囲を指定する間隔の表記](/nuget/reference/package-versioning#version-ranges)に関する説明を参照してください。 |