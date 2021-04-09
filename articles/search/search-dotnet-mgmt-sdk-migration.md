---
title: Azure Search .NET Management SDK へのアップグレード
titleSuffix: Azure Cognitive Search
description: 以前のバージョンから Azure Search .NET Management SDK にアップグレードします。 移行に必要な新しい機能とコード変更について説明します。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8648347eb48081389cf360fa949b31bbd0b8c71e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936709"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Azure Search .NET Management SDK のバージョンのアップグレード

この記事では、検索サービスのプロビジョニングまたはプロビジョニング解除、容量の調整、および API キーの管理に使用される、Azure Search .NET Management SDK の一連のバージョンに移行する方法について説明します。

Management SDK は、Management REST API の特定のバージョンを対象としています。 概念と操作の詳細については、[検索管理 (REST)](/rest/api/searchmanagement/) に関するページを参照してください。

## <a name="versions"></a>バージョン

| SDK バージョン | 対応する REST API バージョン | 機能の追加または動作の変更 |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | api-version=2020-30-20 | エンドポイント セキュリティ (IP ファイアウォール、および [Azure Private Link](../private-link/private-endpoint-overview.md) との統合) を追加します |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | api-version=2019-10-01 | 操作性の向上。 [クエリ キーの一覧](/rest/api/searchmanagement/querykeys/listbysearchservice)の破壊的変更 (GET は廃止されました)。 |
| [1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | api-version=2015-08-19  | 最初のバージョン |

## <a name="how-to-upgrade"></a>アップグレードする方法

1. NuGet パッケージ マネージャー コンソールを使用して、または Visual Studio でプロジェクト参照を右クリックして [NuGet パッケージの管理] を選択することで、`Microsoft.Azure.Management.Search` の NuGet 参照を更新します。

1. NuGet が新しいパッケージとその依存関係をダウンロードした後、プロジェクトをリビルドします。 コードの構成方法によっては、正常にリビルドされる場合があり、作業が終了します。

1. ビルドが失敗した場合、(たとえば単体テストのために) SDK インターフェイスを実装したが、それらが変更されていた可能性があります。 これを解決するには、`BeginCreateOrUpdateWithHttpMessagesAsync` などのより新しいメソッドを実装する必要があります。

1. すべてのビルド エラーを修正した後、新しい機能を利用するようにアプリケーションを変更できます。 

## <a name="upgrade-to-30"></a>3\.0 へのアップグレード

バージョン 3.0 では、IP 範囲へのアクセスを制限することで、また、必要に応じて、パブリック インターネット上には表示されるべきではない検索サービス用の Azure Private Link と統合することで、プライベート エンドポイント保護を追加します。

### <a name="new-apis"></a>新しい API

| API | カテゴリ| 詳細 |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/services/createorupdate#networkruleset) | IP ファイアウォール | サービス エンドポイントへのアクセスを、許可された IP アドレスのリストに制限します。 概念とポータルでの手順については、[IP ファイアウォールの構成](service-configure-firewall.md)に関するページを参照してください。 |
| [共有プライベート リンク リソース](/rest/api/searchmanagement/sharedprivatelinkresources) | Private Link | 検索サービスによって使用される共有プライベート リンク リソースを作成します。  |
| [プライベート エンドポイント接続](/rest/api/searchmanagement/privateendpointconnections) | Private Link | プライベート エンドポイントを介して、検索サービスへの接続を確立して管理します。 概念とポータルでの手順については、[プライベート エンドポイントの作成](service-create-private-endpoint.md)に関するページを参照してください。|
| [プライベート リンク リソース](/rest/api/searchmanagement/privatelinkresources/) | Private Link | プライベート エンドポイント接続を持つ検索サービスの場合は、同じ仮想ネットワークで使用されているすべてのサービスの一覧を取得します。 検索ソリューションで、Azure データ ソース (Azure Storage、Cosmos DB、Azure SQL) からプルするインデクサーが含まれている場合や、Cognitive Services または Key Vault が使用されている場合、これらのすべてのリソースには仮想ネットワーク内のエンドポイントが必要であり、この API は一覧を返す必要があります。 |
| [PublicNetworkAccess](/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| Private Link | これは、サービスの作成または更新要求のプロパティです。 無効にすると、プライベート リンクが唯一のアクセス モダリティになります。 |

### <a name="breaking-changes"></a>重大な変更

[クエリ キーの一覧表示](/rest/api/searchmanagement/querykeys/listbysearchservice)要求に対して GET を使用できなくなりました。 以前のリリースでは、GET または POST を使用できました。このリリース以降のすべてのリリースでは、POST のみがサポートされています。 

## <a name="upgrade-to-20"></a>2\.0 へのアップグレード

Azure Search .NET Management SDK のバージョン 2 はマイナー アップグレードであるため、コードの変更には最小限の労力しか必要としません。SDK への変更は、SDK 自体の使いやすさを向上させるための、厳密にクライアント側の変更です。 これらの変更には、以下が含まれます。

* `Services.CreateOrUpdate` とその非同期バージョンは、`SearchService` のプロビジョニングを自動的にポーリングし、サービスのプロビジョニングが完了するまで戻ることはなくなりました。 これにより、そのようなポーリング コードを自分で記述する必要がなくなります。

* サービスのプロビジョニング を手動でポーリングする場合は、新しい `Services.BeginCreateOrUpdate` メソッドまたはその非同期バージョンのいずれかを使用できます。

* 新しいメソッド `Services.Update` とその非同期バージョンが SDK に追加されています。 これらのメソッドでは、HTTP PATCH を使用して、サービスの増分更新をサポートします。 たとえば、目的の `partitionCount` プロパティと `replicaCount` プロパティのみを含むこれらのメソッドに `SearchService` インスタンスを渡すことで、サービスをスケーリングできるようになりました。 `Services.Get` を呼び出し、返された `SearchService` を変更して `Services.CreateOrUpdate` に渡すという古い方法は、引き続きサポートされますが、必要ではなくなりました。 

## <a name="next-steps"></a>次のステップ

問題が発生した場合の質問の最適な投稿先は、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest) です。 バグを発見した場合は、 [Azure .NET SDK の GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/issues)で問題を報告できます。 問題のタイトルに、必ず "[search]" とラベルを付けてください。