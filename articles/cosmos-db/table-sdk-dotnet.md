---
title: Azure Cosmos DB Table API .NET SDK およびリソース
description: リリース日、提供終了日、各バージョン間で行われた変更など、Azure Cosmos DB Table API for .NET に関するすべてについて説明します。
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.custom: devx-track-dotnet
ms.openlocfilehash: 4bea9f202d40090217219752a13ffde64b466140
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597589"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET API: ダウンロードおよびリリース ノート
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | リンク  |
|---|---|
|**SDK のダウンロード**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)|
|**クイックスタート**|[Azure Cosmos DB: .NET と Table API を使用したアプリのビルド](create-table-dotnet.md)|
|**チュートリアル**|[Azure Cosmos DB: .NET での Table API を使用した開発](tutorial-develop-table-dotnet.md)|
|**現在サポートされているフレームワーク**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) はメンテナンス モードにあり、間もなく非推奨となる予定です。 Table API によってサポートされる最新の機能を引き続き入手するには、新しい .NET Standard ライブラリ [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) にアップグレードしてください。

> プレビュー中にテーブル API アカウントを作成した場合、一般公開されたテーブル API SDK を使うには[新しいテーブル API アカウント](create-table-dotnet.md#create-a-database-account)を作成してください。
>

## <a name="release-notes"></a>リリース ノート

### <a name="212"></a><a name="2.1.2"></a>2.1.2

* バグの修正

### <a name="210"></a><a name="2.1.0"></a>2.1.0

* バグの修正

### <a name="200"></a><a name="2.0.0"></a>2.0.0

* マルチリージョンの書き込みがサポートされるようになりました。
* Microsoft.Azure.DocumentDB、Microsoft.OData.Core、Microsoft.OData.Edm、Microsoft.Spatial に関する NuGet パッケージの依存関係が修正されました。

### <a name="113"></a><a name="1.1.3"></a>1.1.3

* Microsoft.Azure.Storage.Common と Microsoft.Azure.DocumentDB の NuGet パッケージの依存関係が修正されました。
* JsonConvert.DefaultSettings が構成されたときに、テーブルのシリアル化のバグ修正があります。

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* 直接モードで無効な ETAG の検証が追加されました。
* ゲートウェイ モードで LINQ クエリ バグが修正されました。
* 同期 API が SynchronizationContext によってスレッド プールで実行されるようになりました。

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* TableQueryMaxItemCount、TableQueryEnableScan、TableQueryMaxDegreeOfParallelism、および TableQueryContinuationTokenLimitInKb を TableRequestOptions に追加
* バグの修正

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* 一般公開リリース

### <a name="090-preview"></a><a name="0.1.0-preview"></a>0.9.0 - プレビュー

* 初期プレビュー リリース

## <a name="release-and-retirement-dates"></a>リリース日と提供終了日

Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月** 前に通知します。

`Microsoft.Azure.CosmosDB.Table` ライブラリは現在 .NET Framework でのみ利用可能で、メンテナンス モードにあり、間もなく非推奨となる予定です。 新機能および最適化は、.NET Standard ライブラリの [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) にのみ追加されます。そのため、[Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) にアップグレードすることをお勧めします。

[WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) プレビュー パッケージは非推奨となりました。 WindowsAzure.Storage PremiumTable SDK は、2018 年 11 月 15 日に提供終了されます。その時点で提供終了された SDK への要求は許可されなくなります。

| Version | リリース日 | 提供終了日 |
| --- | --- | --- |
| [2.1.2](#2.1.2) |2019 年 9 月 16 日| |
| [2.1.0](#2.1.0) |2019 年 1 月 22 日|2020 年 4 月 1 日 |
| [2.0.0](#2.0.0) |2018 年 9 月 26 日|2020 年 3 月 1 日 |
| [1.1.3](#1.1.3) |2018 年 7 月 17 日|2019 年 12 月 1 日 |
| [1.1.1](#1.1.1) |2018 年 3 月 26 日|2019 年 12 月 1 日 |
| [1.1.0](#1.1.0) |2018 年 2 月 21 日|2019 年 12 月 1 日 |
| [1.0.0](#1.0.0) |2017 年 11 月 15 日|2019 年 11 月 15 日 |
| 0.9.0 - プレビュー |2017 年 11 月 11 日 |2019 年 11 月 11 日 |

## <a name="troubleshooting"></a>トラブルシューティング

エラーが発生した場合 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

Microsoft.Azure.CosmosDB.Table NuGet パッケージの使用を試している場合は、問題を解決する 2 つのオプションがあります。

* Package Manage Console を使用して Microsoft.Azure.CosmosDB.Table パッケージとその依存関係をインストールします。 これを行うには、ソリューションの Package Manager Console に以下を入力します。 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* NuGet パッケージ管理ツールを使用して、Microsoft.Azure.CosmosDB.Table をインストールする前に、Microsoft.Azure.Storage.Common NuGet パッケージをインストールします。

## <a name="faq"></a>よく寄せられる質問

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目

Azure Cosmos DB Table API の詳細については、「[Introduction to Azure Cosmos DB Table API (Azure Cosmos DB Table API の概要)](table-introduction.md)」を参照してください。 
