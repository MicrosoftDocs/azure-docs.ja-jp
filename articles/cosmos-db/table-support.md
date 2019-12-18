---
title: Azure Cosmos DB における Azure Table Storage サポート
description: 同じテーブル データ モデルと操作を共有して Azure Cosmos DB Table API と Azure Storage Tables を連携させる方法について説明します
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 12/02/2019
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 4f742ce34ec429f0fc822562c1ad4ad07f3e7965
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870430"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Table API と Azure Table Storage を使用した開発

Azure Cosmos DB Table API と Azure Table Storage は同じテーブル データ モデルを共有しており、SDK を介して同じ create、delete、update、およびクエリ操作が公開されています。

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API を使用した開発

現時点では、[Azure Cosmos DB Table API](table-introduction.md) には開発に使用できる 4 つの SDK があります。 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK。 .NET Standard を対象とするこのライブラリには、公開されている [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage) と同じクラスおよびメソッド シグネチャがありますが、Table API を使用して Azure Cosmos DB アカウントに接続する機能もあります。 .NET Framework ライブラリ [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) のユーザーには、それがメンテナンス モードにあり、間もなく非推奨となる予定であるため、[Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) にアップグレードすることをお勧めします。

* [Python SDK](table-sdk-python.md):新しい Azure Cosmos DB Python SDK は、Python で Azure Table Storage をサポートする唯一の SDK です。 この SDK は、Azure Table Storage と Azure Cosmos DB Table API の両方に接続します。

* [Java SDK](table-sdk-java.md): この Azure Storage SDK には、Table API を使用して Azure Cosmos DB アカウントに接続する機能があります。

* [Node.js SDK](table-sdk-nodejs.md): この Azure Storage SDK には、Table API を使用して Azure Cosmos DB アカウントに接続する機能があります。


Table API の使用に関するその他の情報は、[Table API を使用した開発に関する FAQ](faq.md#table) の記事で入手できます。

## <a name="developing-with-azure-table-storage"></a>Azure Table Storage を使用した開発

Azure Table Storage では以下の SDK を開発に使用できます。

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/)。 このライブラリでは、Storage Table service を使うことができます。
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python)。 Azure Cosmos DB Table SDK for Python では、Table Storage サービスがサポートされています (Azure Table Storage と Cosmos DB の Table API は同じ機能を共有しているため、また、SDK の開発作業を調整するために、この SDK を使用することをお勧めします)。
- [Azure Storage SDK for Java](https://github.com/azure/azure-storage-java)。 この Azure Storage SDK は、Azure Table Storage を使用するためのクライアント ライブラリを Java で提供します。
- [Node.js SDK](https://github.com/Azure/azure-storage-node)。 この SDK は、Storage Table service を使用するための Node.js パッケージとブラウザー互換の JavaScript クライアント ライブラリを提供します。
- [AzureRmStorageTable PowerShell モジュール](https://www.powershellgallery.com/packages/AzureRmStorageTable)。 この PowerShell モジュールには、ストレージ テーブルを使用するためのコマンドレットがあります。
- [C++ 用 Azure Storage クライアント ライブラリリ](https://github.com/Azure/azure-storage-cpp/)。 このライブラリでは、Azure Storage 用のアプリケーションを作成できます。
- [Ruby 用 Azure Storage Table クライアント ライブラリ](https://github.com/azure/azure-storage-ruby/tree/master/table)。 このプロジェクトは、Azure Table Storage service に間単にアクセスできる Ruby パッケージを提供します。
- [Azure Storage Table PHP クライアント ライブラリ](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table)。 このプロジェクトは、Azure Table Storage service に間単にアクセスできる PHP クライアント ライブラリを提供します。


   





