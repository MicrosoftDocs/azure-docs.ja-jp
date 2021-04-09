---
title: Azure Cosmos DB SQL SDK の接続モード
description: Azure Cosmos DB SQL SDK で使用可能なさまざまな接続モードについて説明します。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: maquaran
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: cf985999bac0cf45eec5d8f0f5f9e921b6f4591c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934953"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Azure Cosmos DB SQL SDK の接続モード
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

クライアントが Azure Cosmos DB に接続する方法は、特に監視対象となるクライアント側の待機時間について、パフォーマンスに重要な影響を及ぼします。 Azure Cosmos DB では、ゲートウェイ モードと呼ばれる HTTPS を介したシンプルなオープン RESTful プログラミング モデルが提供されます。 さらに、直接モードと呼ばれる効率的な TCP プロトコルも提供されます。これは、通信モデルでは RESTful でもあり、初期認証とトラフィックの暗号化に TLS が使用されます。

## <a name="available-connectivity-modes"></a>使用可能な接続モード

使用可能な 2 つの接続モードは、次のとおりです。

  * ゲートウェイ モード
      
    ゲートウェイ モードは、すべての SDK プラットフォームでサポートされています。 ゲートウェイ モードでは標準の HTTPS ポートと単一の DNS エンドポイントが使用されるため、ファイアウォールの厳しい制限がある企業ネットワーク内でアプリケーションを実行する場合は、ゲートウェイ モードが最適な選択肢となります。 ただし、パフォーマンスのトレードオフとして、ゲートウェイ モードでは、Azure Cosmos DB に対してデータの読み取りまたは書き込みを行うたびに、追加のネットワーク ホップが必要になります。 ソケット接続の数に制限がある環境でアプリケーションを実行する場合、ゲートウェイ接続モードも推奨されます。

    Azure Functions (特に[従量課金プラン](../azure-functions/consumption-plan.md)) で SDK を使用する場合は、現在の[接続数の制限](../azure-functions/manage-connections.md)に注意してください。

  * 直接モード

    直接モードでは、TCP プロトコルを介した接続がサポートされ、ネットワーク ホップ数が減るためパフォーマンスが向上します。 アプリケーションからバックエンド レプリカに直接接続されます。 現在、直接モードは .NET と Java SDK プラットフォームでのみサポートされています。
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Azure Cosmos DB の接続モード" border="false":::

基本的に、これらの接続モードでは、データ プレーン要求 (ドキュメントの読み取りと書き込み) がクライアント マシンから Azure Cosmos DB バックエンドのパーティションに到達するまでのルートを調整します。 直接モードは、最高のパフォーマンスを得るための推奨オプションです。クライアントは、Azure Cosmos DB バックエンドのパーティションへの TCP 接続を直接開き、中継なしで要求を "*直接*" 送信できます。 これに対して、ゲートウェイ モードでは、クライアントからの要求は Azure Cosmos DB フロントエンドのいわゆる "ゲートウェイ" サーバーにルーティングされ、そこから Azure Cosmos DB バックエンドの適切なパーティションに送信されます。

## <a name="service-port-ranges"></a>ソース ポート範囲

直接モードを使用する場合は、Azure Cosmos DB で動的 TCP ポートが使用されるため、ゲートウェイ ポートに加えてポート範囲 10000 から 20000 を開いておく必要があります。 [プライベート エンドポイント](./how-to-configure-private-endpoints.md)で直接モードを使用する場合は、TCP ポートの全範囲である 0 から 65535 を開いておく必要があります。 これらのポートが開いていない場合に TCP プロトコルを使用しようとすると、[503 サービスを利用できません] エラーが受信される可能性があります。

次の表は、さまざまな API で使用できる接続モードと、各 API で使用されるサービス ポートの概要を示しています。

|接続モード  |サポートされるプロトコル  |サポートされる SDK  |API/サービス ポート  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  すべての SDK    |   SQL (443)、MongoDB (10250、10255、10256)、Table (443)、Cassandra (10350)、Graph (443) <br> ポート 10250 は、Geo レプリケーションを使用することなく、MongoDB インスタンスの既定の Azure Cosmos DB API にマップされます。 一方で、ポート 10255 と 10256 は、Geo レプリケーションを使用して、このインスタンスにマップされます。   |
|直接    |     TCP    |  .NET SDK Java SDK    | パブリック/サービス エンドポイントを使用する場合: 10000 から 20000 の範囲のポート<br>プライベート エンドポイントを使用する場合: 0 から 65535 の範囲のポート |

## <a name="next-steps"></a>次の手順

特定の SDK プラットフォーム パフォーマンスを最適化する場合:

* [.NET V2 SDK パフォーマンスのヒント](performance-tips.md)

* [.NET V3 SDK パフォーマンスのヒント](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Java V4 SDK パフォーマンスのヒント](performance-tips-java-sdk-v4-sql.md)