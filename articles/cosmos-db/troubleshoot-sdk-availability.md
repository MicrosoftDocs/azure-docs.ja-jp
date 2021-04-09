---
title: 複数リージョン環境での Azure Cosmos SDK の可用性の診断とトラブルシューティング
description: 複数リージョン環境で操作する場合の Azure Cosmos SDK の可用性の動作について、詳しく説明します。
author: ealsur
ms.service: cosmos-db
ms.date: 02/18/2021
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0720eb01920e39a9bee27e4d00d97acba55b0ad5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661428"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>複数リージョン環境での Azure Cosmos SDK の可用性の診断とトラブルシューティング
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、特定のリージョンに対する接続の問題が発生した場合、またはリージョンのフェールオーバーが発生した場合の最新バージョンの Azure Cosmos SDK の動作について説明します。

すべての Azure Cosmos SDK には、リージョンの優先設定をカスタマイズするオプションがあります。 次のプロパティは、さまざまな SDK で使用されます。

* .NET V2 SDK の [ConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) プロパティ。
* .NET V3 SDK の [CosmosClientOptions.ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) または [CosmosClientOptions.ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) プロパティ。
* Java V4 SDK の [CosmosClientBuilder.preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) メソッド。
* Python SDK の [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) パラメーター。
* JS SDK の [CosmosClientOptions.ConnectionPolicy.preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) パラメーター。

リージョンの優先設定を設定すると、クライアントは、次の表に示すようにリージョンに接続します。

|アカウントの種類 |読み取り |書き込み |
|------------------------|--|--|
| 単一の書き込みリージョン | 優先リージョン | プライマリ リージョン  |
| 複数の書き込みリージョン | 優先リージョン | 優先リージョン  |

**優先リージョンを設定しない** 場合、SDK クライアントは既定でプライマリ リージョンになります。

|アカウントの種類 |読み取り |書き込み |
|------------------------|--|--|
| 単一の書き込みリージョン | プライマリ リージョン | プライマリ リージョン |
| 複数の書き込みリージョン | プライマリ リージョン  | プライマリ リージョン  |

> [!NOTE]
> プライマリ リージョンとは、[Azure Cosmos アカウントのリージョン一覧](distribute-data-globally.md)にある最初のリージョンを指します。
> リージョンの優先設定として指定された値が既存のどの Azure リージョンとも一致しない場合、それらは無視されます。 それらが既存のリージョンと一致していても、アカウントがそれにレプリケートされていない場合、クライアントは、一致する次の優先リージョンまたはプライマリ リージョンに接続します。

> [!WARNING]
> このドキュメントで説明されているフェールオーバーと可用性ロジックは、クライアント構成で無効にできますが、これは、ユーザー アプリケーションで可用性エラー自体が処理される場合を除き、推奨されません。 これは、次のようにして実現できます。
>
> * .NET V2 SDK で [ConnectionPolicy.EnableEndpointRediscovery](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.enableendpointdiscovery) プロパティを false に設定する。
> * .NET V3 SDK で [CosmosClientOptions.LimitToEndpoint](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.limittoendpoint) プロパティを true に設定する。
> * Java V4 SDK で [CosmosClientBuilder.endpointDiscoveryEnabled](/java/api/com.azure.cosmos.cosmosclientbuilder.endpointdiscoveryenabled) メソッドを false に設定する。
> * Python SDK で [CosmosClient.enable_endpoint_discovery](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) パラメーターを false に設定する。
> * JS SDK で [CosmosClientOptions.ConnectionPolicy.enableEndpointDiscovery](/javascript/api/@azure/cosmos/connectionpolicy#enableEndpointDiscovery) パラメーターを false に設定する。

通常の状況下では、SDK クライアントは優先リージョン (リージョンの優先設定が設定されている場合) またはプライマリ リージョン (優先設定が設定されていない場合) に接続され、以下のいずれかのシナリオが発生しない限り、操作はそのリージョンに限定されます。

これらの場合、Azure Cosmos SDK を使用するクライアントではログが公開され、**操作の診断情報** の一部として再試行情報が含まれます。

* .NET V2 SDK の応答の *RequestDiagnosticsString* プロパティ。
* .NET V3 SDK の応答と例外の *Diagnostics* プロパティ。
* Java V4 SDK の応答と例外の *getDiagnostics()* メソッド。

優先順位において次のリージョンを決定する際、SDK クライアントではアカウント リージョンの一覧が使用され、優先リージョン (存在する場合) が優先されます。

これらのイベント中の SLA 保証に関する包括的な詳細については、「[可用性に関する SLA](high-availability.md#slas-for-availability)」を参照してください。

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>アカウントからのリージョンの削除

Azure Cosmos アカウントからリージョンを削除すると、アカウントをアクティブに使用している SDK クライアントでは、バックエンドの応答コードを介してリージョンの削除が検出されます。 次に、クライアントによってリージョンのエンドポイントは使用不可とマークされます。 クライアントによって現在の操作が再試行され、以降のすべての操作は優先順に次のリージョンに永続的にルーティングされます。 優先設定一覧にエントリが 1 つしかなかった (または空だった) が、アカウントに他の使用可能なリージョンがある場合は、アカウント一覧内の次のリージョンにルーティングされます。

## <a name="adding-a-region-to-an-account"></a>アカウントへのリージョンの追加

Azure Cosmos SDK クライアントでは、5 分ごとにアカウント構成が読み取られ、認識しているリージョンが更新されます。

リージョンを削除して後でアカウントに追加し直した場合、SDK 構成のリージョンの優先順位において、追加したリージョンの方が現在接続されているリージョンより高いと、SDK はそのリージョンを永続的に使用するように切り替わります。 追加されたリージョンが検出された後、それ以降のすべての要求はそのリージョンに送信されます。

Azure Cosmos アカウントに割り当てられていないリージョンに接続するようにクライアントを構成した場合、優先リージョンは無視されます。 後でそのリージョンを追加すると、クライアントはそれを検出し、そのリージョンに永続的に切り替わります。

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>単一書き込みリージョン アカウントで書き込みリージョンをフェールオーバーする

現在の書き込みリージョンのフェールオーバーを開始すると、次の書き込み要求は既知のバックエンド応答で失敗します。 この応答が検出されると、クライアントでは、アカウントに対してクエリが実行され、新しい書き込みリージョンが学習され、現在の操作が再試行され、以降のすべての書き込み操作は永続的に新しいリージョンにルーティングされます。

## <a name="regional-outage"></a>リージョンの停止

アカウントが単一書き込みリージョンであり、書き込み操作中にリージョンの停止が発生した場合、動作は[手動フェールオーバー](#manual-failover-single-region)と同様です。 読み取り要求または複数書き込みリージョン アカウントの場合、動作は[リージョンの削除](#remove-region)と同様です。

## <a name="session-consistency-guarantees"></a>セッションの整合性の保証

[セッションの整合性](consistency-levels.md#guarantees-associated-with-consistency-levels)を使用する場合、クライアントでは、自身の書き込みを読み取ることができるように保証する必要があります。 読み取りリージョンの優先設定が書き込みリージョンと異なる単一書き込みリージョン アカウントでは、ユーザーが書き込みを発行し、ローカル リージョンから読み取りが実行されているときに、ローカル リージョンでまだデータのレプリケーションを受け取っていない場合があります (軽い制約の速度)。 このような場合、SDK によって読み取り操作に対する特定の障害が検出され、セッションの整合性を確保するためにプライマリ リージョンに対する読み取りが再試行されます。

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>TCP プロトコルでの一時的な接続の問題

Azure Cosmos SDK クライアントが TCP プロトコルを使用するように構成されているシナリオでは、特定の要求に対して、ネットワークの状態が特定のエンドポイントとの通信に一時的に影響を与える場合があります。 このような一時的なネットワーク状態は、TCP タイムアウトおよびサービス利用不可 (HTTP 503) エラーとして表面化する可能性があります。 クライアントでは、エラーが表面化する前に、同じエンドポイントに対して要求が数秒間ローカルで再試行されます。

ユーザーが複数のリージョンを含む優先リージョン一覧を構成済みで、Azure Cosmos アカウントが複数書き込みリージョンまたは単一書き込みリージョンであり、操作が読み取り要求である場合、クライアントではローカル障害が検出され、優先設定一覧の次のリージョンでその 1 つの操作が再試行されます。

## <a name="next-steps"></a>次の手順

* [可用性の SLA](high-availability.md#slas-for-availability) を確認する。
* 最新の [.NET SDK](sql-api-sdk-dotnet-standard.md) を使用する
* 最新の [Java SDK](sql-api-sdk-java-v4.md) を使用する
* 最新の [Python SDK](sql-api-sdk-python.md) を使用する
* 最新の [Node SDK](sql-api-sdk-node.md) を使用する
