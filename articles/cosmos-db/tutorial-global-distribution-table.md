---
title: Table API による Azure Cosmos DB グローバル分散のチュートリアル
description: Azure Cosmos DB Table API アカウントにおけるグローバル分散の動作とリージョンの優先リストを構成する方法について説明します
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: e6cd574d1041908e91ad5e6629403c0e40d11c03
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870362"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Table API を使用して Azure Cosmos DB グローバル分散を設定する

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * [Table API](table-introduction.md) を使用してグローバル分散を構成する

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Table API を使用して優先リージョンに接続する

[グローバル配布](distribute-data-globally.md)を活用するために、クライアント アプリケーションでは、ドキュメントの操作の実行に使用するリージョンの順序付き優先リストを指定できます。 これは、[TableConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet) プロパティを設定することによって行うことができます。 Azure Cosmos DB Table API SDK は、アカウント構成、現在のリージョンの可用性、および指定されている設定一覧に基づいて、通信に最適なエンドポイントを選定します。

PreferredLocations には、読み取りの優先場所 (マルチホーム) のコンマ区切りリストを指定してください。 各クライアント インスタンスは、低待機時間で読み取りを行えるように、優先順位を付けてこれらのリージョンのサブセットを指定できます。 リージョンは[表示名](https://msdn.microsoft.com/library/azure/gg441293.aspx) (`West US` など) を使用して指定する必要があります。

読み取りはすべて、PreferredLocations リストで使用可能な最初のリージョンに送信されます。 要求が失敗すると、クライアントはリストにある次のリージョンを試します。これが繰り返されます。

SDK は、PreferredLocations で指定されたリージョンからの読み取りを試みます。 このため、3 つのリージョンでデータベース アカウントが利用できるものの、クライアントが PreferredLocations の非書き込みリージョンを 2 つだけ指定している場合などには、書き込みリージョンの外で読み取りが処理されません。これはフェールオーバーの場合にもあてはまります。

SDK は自動的に、すべての書き込みを現在の書き込みリージョンに送信します。

PreferredLocations プロパティが設定されていない場合、すべての要求が現在の書き込みリージョンから処理されます。

このチュートリアルはこれで終わりです。 [Azure Cosmos DB の一貫性レベル](consistency-levels.md)に関する記事を読んで、グローバルにレプリケートされたアカウントの整合性を管理する方法について確認できます。 また、Azure Cosmos DB におけるグローバル データベース レプリケーションの動作の詳細については、[Azure Cosmos DB を使用したデータのグローバル分散](distribute-data-globally.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * Azure Cosmos DB Table API を使用してグローバル分散を構成する

