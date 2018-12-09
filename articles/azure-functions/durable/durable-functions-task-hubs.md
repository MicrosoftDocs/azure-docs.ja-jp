---
title: Durable Functions におけるタスク ハブ - Azure
description: Azure Functions の Durable Functions 拡張機能におけるタスク ハブについて説明します。 タスク ハブを構成する方法について説明します。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 7a6346e594c5a7cc4cf02f3ea658aac4977e641a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637457"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions におけるタスク ハブ (Azure Functions)

[Durable Functions](durable-functions-overview.md) の*タスク ハブ*は、オーケストレーションに使用される Azure Storage リソースの論理コンテナーです。 オーケストレーター関数とアクティビティ関数は、同じタスク ハブに属しているときに限り、情報をやり取りすることができます。

各関数アプリには、個別のタスク ハブがあります。 複数の関数アプリでストレージ アカウントを共有している場合、ストレージ アカウントには複数のタスク ハブが含まれます。 次の図は、共有ストレージ アカウントと専用ストレージ アカウントの各関数アプリにタスク ハブが 1 つあることを示しています。

![共有ストレージ アカウントと専用ストレージ アカウントの図](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage のリソース

タスク ハブは次のストレージ リソースから構成されます。 

* 1 つまたは複数のコントロールキュー。
* 1 つの作業項目キュー。
* 1 つの履歴テーブル。
* 1 つのインスタンス テーブル。
* Lease Blob を少なくとも 1 つ含んだ 1 つのストレージ コンテナー。

これらすべてのリソースは、オーケストレーター関数またはアクティビティ関数の実行時 (またはスケジュール時) に、既定の Azure ストレージ アカウントに自動的に作成されます。 これらのリソースがどのように使用されるかについては、[パフォーマンスとスケーリング](durable-functions-perf-and-scale.md)に関する記事で説明しています。

## <a name="task-hub-names"></a>タスク ハブ名

次の例に示すように、タスク ハブは *host.json* ファイルに宣言されている名前で識別されます。

### <a name="hostjson-functions-v1"></a>host.json (Functions v1)
```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```
### <a name="hostjson-functions-v2"></a>host.json (Functions v2)
```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "HubName": "MyTaskHub"
    }
  }
}
```
次の *host.json* ファイルの例に示すように、タスク ハブはアプリの設定を使用して構成することもできます。

### <a name="hostjson-functions-v1"></a>host.json (Functions v1)
```json
{
  "durableTask": {
    "HubName": "%MyTaskHub%"
  }
}
```
### <a name="hostjson-functions-v2"></a>host.json (Functions v2)
```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "HubName": "%MyTaskHub%"
    }
  }
}
```
タスク ハブ名は、`MyTaskHub` アプリ設定の値に設定されます。 次の `local.settings.json` は、`MyTaskHub` 設定を `samplehubname` として定義する方法を示しています。

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" :  "samplehubname" 
  }
}
```

タスク ハブの名前は、先頭文字をアルファベットとする必要があります。また、使用できるのはアルファベットと数値だけです。 指定されていない場合、既定の名前は **DurableFunctionsHub** です。

> [!NOTE]
> この名前は共有ストレージ アカウント内に複数のタスク ハブがある場合に、それぞれのタスク ハブを区別するものです。 共有ストレージ アカウントを共有する関数アプリが複数ある場合、*host.json* ファイルでタスク ハブごとに異なる名前を構成する必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [バージョン管理の方法](durable-functions-versioning.md)
