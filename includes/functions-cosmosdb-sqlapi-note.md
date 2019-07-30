---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c68b56463625a09f9c8341881284706ab8250f46
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608269"
---
Azure Cosmos DB のバインドは、SQL API で使用する場合にのみサポートされます。 他のすべての Azure Cosmos DB API については、[Azure Cosmos DB の MongoDB 用 API](../articles/cosmos-db/mongodb-introduction.md)、[Cassandra API](../articles/cosmos-db/cassandra-introduction.md)、[Gremlin API](../articles/cosmos-db/graph-introduction.md)、[Table API](../articles/cosmos-db/table-introduction.md) など、API 用の静的クライアントを使用して関数からデータベースにアクセスする必要があります。
