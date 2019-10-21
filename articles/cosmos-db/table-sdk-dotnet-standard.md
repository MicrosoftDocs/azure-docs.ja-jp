---
title: Azure Cosmos DB Table API .NET Standard SDK およびリソース
description: リリース日、提供終了日、各バージョン間の変更点など、Azure Cosmos DB Table API および .NET Standard SDK に関するあらゆる詳細を提供します。
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: c1422b6411fc60383cffa6c3082108bb940c4343
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996211"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET Standard API: ダウンロードおよびリリース ノート
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK のダウンロード**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**サンプル**|[Cosmos DB Table API .NET のサンプル](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**クイックスタート**|[クイックスタート](create-table-dotnet.md)|
|**チュートリアル**|[チュートリアル](tutorial-develop-table-dotnet.md)|
|**現在サポートされているフレームワーク**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**問題の報告**|[問題を報告する](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>2\.0.0 シリーズのリリース ノート
2.0.0 シリーズは [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) に依存します。パフォーマンスが向上し、Cosmos DB エンドポイントに名前空間が統合されています。

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview
* 2\.0.0 Table SDK の初期プレビューは [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) に依存します。パフォーマンスが向上し、Cosmos DB エンドポイントに名前空間が統合されています。 パブリック API は変わりません。

## <a name="release-notes-for-100-series"></a>1\.0.0 シリーズのリリース ノート
1.0.0 シリーズは [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) に依存します。

### <a name="a-name105105"></a><a name="1.0.5"/>1.0.5
* Rest Executor を使用して Cosmos DB Table API と通信するための新しい構成が TableClientConfiguration に導入されました

### <a name="a-name105-preview105-preview"></a><a name="1.0.5-preview"/>1.0.5-preview
* バグの修正

### <a name="a-name104104"></a><a name="1.0.4"/>1.0.4
* バグの修正
* RestExecutorConfiguration に対して HttpClientTimeout オプションが提供されます。

### <a name="a-name104-preview104-preview"></a><a name="1.0.4-preview"/>1.0.4-preview
* バグの修正
* RestExecutorConfiguration に対して HttpClientTimeout オプションが提供されます。

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* バグの修正

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 一般公開リリース

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-preview
* CloudTableClient を構成する方法が変更されました。 構築時に TableClientConfiguration オブジェクトを受け取るようになりました。 TableClientConfiguration では、対象のエンドポイントが Cosmos DB Table API であるかまたは Azure Storage Table API であるかによって、クライアントの動作を構成するさまざまなプロパティが提供されます。
* カスタム列に並べ替えられた順序で結果を返すために TableQuery にサポートが追加されました。 この機能は、Cosmos DB テーブル エンドポイントでのみサポートされています。
* さまざまな結果型で RequestCharges を公開するためのサポートが追加されました。 この機能は、Cosmos DB テーブル エンドポイントでのみサポートされています。

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Azure Storage Table エンドポイントに対する SAS トークン、TablePermissions、ServiceProperties、および ServiceStats の操作が追加されています。 
   > [!NOTE]
   > 以前の Azure Storage Table SDK の一部の機能はまだサポートされていません (クライアント側の暗号化など)。

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Azure Storage Table エンドポイントに対するコア CRUD、バッチ、およびクエリ操作のサポートが追加されています。 
   > [!NOTE]
   > 以前の Azure Storage Table SDK の一部の機能はまだサポートされていません (クライアント側の暗号化など)。

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* Azure Cosmos DB Table .NET Standard SDK は、Cosmos DB 上の Table データ モデルに効率的にアクセスするためのクロスプラットフォーム対応 .NET ライブラリです。 この最初のリリースでは、テーブルおよびエンティティの CRUD とクエリの機能全体、および [Cosmos DB Table SDK For .NET Framework](table-sdk-dotnet.md) に類似する API がサポートされています。 
   > [!NOTE]
   >  Azure Storage Table のエンドポイントは、0.9.1-preview バージョンではまだサポートされていません。

## <a name="release-and-retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月**前に通知します。

このクロスプラットフォーム対応の .NET Standard ライブラリ [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) は、.NET Framework ライブラリ [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) を置き換えます。

### <a name="200-series"></a>2.0.0 シリーズ
| Version | リリース日 | 提供終了日 |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |2019 年 8 月 22 日 |--- |

### <a name="100-series"></a>1.0.0 シリーズ
| Version | リリース日 | 提供終了日 |
| --- | --- | --- |
| [1.0.5](#1.0.5) |2019 年 9 月 13 日 |--- |
| [1.0.5-preview](#1.0.5-preview) |2019 年 8 月 20 日 |--- |
| [1.0.4](#1.0.4) |2019 年 8 月 12 日 |--- |
| [1.0.4-preview](#1.0.4-preview) |2019 年 7 月 26 日 |--- |
| 1.0.2-preview |2019 年 5 月 2 日 |--- |
| [1.0.1](#1.0.1) |2019 年 4 月 19 日 |--- |
| [1.0.0](#1.0.0) |2019 年 3 月 13 日 |--- |
| [0.11.0-preview](#0.11.0-preview) |2019 年 3 月 5 日 |--- |
| [0.10.1-preview](#0.10.1-preview) |2019 年 1 月 22 日 |--- |
| [0.10.0-preview](#0.10.0-preview) |2018 年 12 月 18 日 |--- |
| [0.9.1-preview](#0.9.1-preview) |2018 年 10 月 18 日 |--- |


## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Azure Cosmos DB Table API の詳細については、「[Introduction to Azure Cosmos DB Table API (Azure Cosmos DB Table API の概要)](table-introduction.md)」を参照してください。
