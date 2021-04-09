---
title: Azure Cosmos DB:SQL Async Java API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Async Java SDK の各バージョン間の変更など、SQL Async Java API と SDK に関するあらゆる詳細を提供します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: eecca39b3d7eabadcd03ab27babab05a39de12ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577243"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API 用 Azure Cosmos DB Async Java SDK:リリース ノートとリソース
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark コネクタ](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [REST リソース プロバイダー](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [Bulk Executor - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

SQL API Async Java SDK は、[Netty ライブラリ](https://netty.io/)をサポートした非同期操作が可能であるという点で、SQL API Java SDK とは異なります。 既存の [SQL API Java SDK](sql-api-sdk-java.md) では、非同期操作はサポートされません。 

> [!IMPORTANT]  
> これは Azure Cosmos DB 用の最新の Java SDK では "*ありません*"。 プロジェクトには [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) を使用することを検討してください。 アップグレードするには、[Azure Cosmos DB Java SDK v4 への移行](migrate-java-v4-sdk.md)に関するガイド、および[ Reactor と RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) に関するガイドの手順に従ってください。 
>

| | リンク |
|---|---|
| **SDK のダウンロード** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API ドキュメント** |[Java API リファレンス ドキュメント](/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) | 
|**SDK への協力** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**開始するには** | [Async Java SDK の開始](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Code sample** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **パフォーマンスに関するヒント**| [GitHub の Readme](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **サポートされている最小ランタイム**|[JDK 8](/java/azure/jdk/) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>よく寄せられる質問
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページを参照してください。