---
title: API のバージョン
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の REST API のバージョン ポリシーと .NET SDK のクライアント ライブラリ。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: d2308d065755df834a224ef3b1df202d7b7dc5ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100518948"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure Cognitive Search の API のバージョン

Azure Cognitive Search では、定期的に機能更新がロールアウトされています。 このような更新が発生すると、下位互換性を維持するために、API の新しいバージョンが必要になる場合があります (毎回とは限りません)。 新しいバージョンが発行されると、お客様は検索サービスの更新内容をコードに統合するタイミングと方法を管理することができます。

Azure Cognitive Search のチームでは、原則として必要な場合にのみ新しい API バージョンを公開しています。新しい API バージョンの使用には、お客様側でコードをアップグレードする作業が必要になる可能性があるためです。 下位互換性が保たれなくなる形で API の一部の要素が変更された場合に限り、新しいバージョンが必要です。 このような変更は、既存の機能に修正を加えたことや、新機能において既存の API のセキュリティ構成を変更したことが原因で発生することがあります。

SDK 更新プログラムにも同じルールが適用されます。 Azure Cognitive Search SDK は[セマンティック バージョニング](https://semver.org/) ルールに従っています。つまり、バージョンにはメジャー、マイナー、およびビルド番号という 3 つの部分があります (たとえば、1.1.0)。 SDK の新しいメジャー バージョンは、下位互換性が保たれなくなる変更が生じた場合にのみリリースされます。 下位互換性が保たれる機能更新の場合はマイナー バージョンがインクリメントされ、バグ修正の場合はビルド バージョンのみがインクリメントされます。

> [!Important]
> .NET、Java、Python、および JavaScript 用の Azure SDK によって、Azure Cognitive Search 用の新しいクライアント ライブラリがロールアウトされます。 現在、最新の Search REST API (2020-06-30) や Management REST API (2020-03-13) が完全にサポートされる Azure SDK ライブラリはありませんが、これは時間の経過と共に変化します。 このページや[新機能](whats-new.md)に関するページで機能拡張のお知らせを定期的に確認することができます。

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>サポートされていないバージョン

2020 年 10 月 15 日までに、既存の検索ソリューションを、最新バージョンの REST API にアップグレードしてください。 その時点で、次に示すバージョンの Azure Cognitive Search REST API は廃止されてサポートされなくなります。

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **2014-07-31-Preview**
+ **2014-10-20-Preview**

さらに、[**3.0.0-rc**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) より前のバージョンの Azure Cognitive Search .NET SDK も、これらの REST API バージョンの 1 つを対象としているため、廃止される予定です。 

この日以降、非推奨の REST API または SDK のバージョンを使用するアプリケーションは動作しなくなり、アップグレードする必要があります。 この種類の変更と同様に、調整に十分な時間をとるため、12 か月前通知を行っています。

引き続き Azure Cognitive Search を使用するには、2020 年 10 月 15 日までに、[REST API](search-api-migration.md) を対象とする既存のコードを、[REST API バージョン 2020-06-30](/rest/api/searchservice/) またはそれより新しい SDK に移行してください。  最新バージョンへの更新についてご不明な点がある場合は、コードを更新するのに十分な時間があることを確認するため、2020 年 5 月 15 日までに azuresearch_contact@microsoft.com にメールでお問い合わせください。

## <a name="rest-apis"></a>REST API

Azure Cognitive Search サービスのインスタンスでは、最新のバージョンを含む、REST API の複数のバージョンをサポートしています。 バージョンが最新ではなくなった場合でも、そのバージョンを引き続き使用できますが、最新バージョンを使用するように[コードを移行する](search-api-migration.md)ことをお勧めします。 REST API を使用している場合は、api-version パラメーターを使用して、すべての要求に API バージョンを指定する必要があります。 .NET SDK を使用している場合は、使用している SDK のバージョンによって REST API の対応するバージョンが決まります。 サービスが新しいバージョンの API をサポートするようにアップグレードされた場合でも、使用中の古い SDK のコードを変更なしで引き続き実行できます。

以下の表には、現在および以前にリリースされた Search Service REST API のバージョンのバージョン履歴を示します。 最新の安定バージョンとプレビュー バージョンのみのドキュメントが公開されています。

### <a name="search-service-apis"></a>Search Service API

検索サービスでコンテンツを作成して管理します。

| バージョン&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | 説明 |
|-------------------------|--------|------------------------------|
| [Search 2020-06-30](/rest/api/searchservice/index)| Stable | 関連性のスコアリングが強化され、ナレッジ ストアが一般公開されている、Search REST API の最新の安定リリースです。|
| [Search 2020-06-30-Preview](/rest/api/searchservice/index-preview)| プレビュー | 安定バージョンに関連付けられているプレビュー バージョン。 複数の[プレビュー機能](search-api-preview.md)が含まれています。 |
| 検索 2019-05-06 | Stable  | [複合型](search-howto-complex-data-types.md)が追加されています。 |
| 検索 2019-05-06-Preview | プレビュー | 安定バージョンに関連付けられているプレビュー バージョン。 |
| Search 2017-11-11 | Stable | スキルセットと [AI エンリッチメント](cognitive-search-concept-intro.md)が追加されています。 |
| Search 2017-11-11-Preview | プレビュー | 安定バージョンに関連付けられているプレビュー バージョン。 |
| Search 2016-09-01 |Stable | [インデクサー](search-indexer-overview.md)が追加されています。 |
| Search 2016-09-01-Preview | プレビュー | 安定バージョンに関連付けられているプレビュー バージョン。|
| Search 2015-02-28 | 2020 年 10 月 10 日以降はサポートされません   | 最初に一般公開されたリリースです。  |
| Search 2015-02-28-Preview | 2020 年 10 月 10 日以降はサポートされません  | 安定バージョンに関連付けられているプレビュー バージョン。 |
| Search 2014-10-20-Preview | 2020 年 10 月 10 日以降はサポートされません | 2 番目のパブリック プレビューです。 |
| Search 2014-07-31-Preview | 2020 年 10 月 10 日以降はサポートされません  | 1 番目のパブリック プレビューです。 |

### <a name="management-rest-apis"></a>管理 REST API

検索サービスを作成および構成し、API キーを管理します。

| バージョン&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | 説明 |
|-------------------------|--------|------------------------------|
| [管理 2020-08-01](/rest/api/searchmanagement/) | Stable | 管理 REST API の最新の安定バージョン リリースです。 プレビュー バージョンに記載されているものを除くすべての送信アクセス リソースに、一般公開されている共有プライベート リンク リソースのサポートが追加されています |
| [管理 2020-08-01-Preview](/rest/api/searchmanagement/index-preview) | プレビュー  | 現在プレビューの段階: Azure Functions と Azure Database for MySQL 用の共有プライベート リンク リソースのサポート。 |
| 管理 2020-03-13  | Stable | プライベート リンクを使用する[プライベート エンドポイント](service-create-private-endpoint.md)、および[ネットワーク IP 規則](service-configure-firewall.md)が新しいサービスに追加されています。 詳細については、この [Swagger 仕様](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2020-08-01)を参照してください。 |
| 管理 2019-10-01-Preview | [プレビュー]  | この一覧には、導入されたプレビュー機能はありませんでした。 このプレビューは機能的には 2020-03-13 と同等です。 詳細については、この [Swagger 仕様](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/preview/2019-10-01-preview)を参照してください。 |
| Management 2015-08-19  | Stable | Management REST API の最初の一般公開バージョン。 サービスのプロビジョニング、スケールアップ、および API キーの管理が可能です。 詳細については、この [Swagger 仕様](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19)を参照してください。 |
| Management 2015-08-19-Preview  | プレビュー | Management REST API の最初のプレビュー バージョン。 詳細については、この [Swagger 仕様](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19)を参照してください。 |

## <a name="azure-sdk-for-net"></a>Azure SDK for .NET

次の表では、新しいバージョンの SDK へのリンクを示します。 

| SDK バージョン | Status | 説明 |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Stable | 2020 年 7 月にリリースされた、Azure .NET SDK の新しいクライアント ライブラリです。 Search REST api-version=2020-06-30 REST API を対象としていますが、geo フィルターのネイティブ サポートはまだ提供されていません。 地理的な操作には、[Microsoft.Spatial](https://www.nuget.org/packages/Microsoft.Spatial/) パッケージをお勧めします。 [System.Text.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) および [Newtonsoft.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md) についての例があります。 |
| [Microsoft.Azure.Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | リリース日: 2019 年 5 月。 Search REST api-version=2019-05-06 を対象としています。|
| [Microsoft.Azure.Management.Search 4.0.0](/dotnet/api/overview/azure/search/management) | Stable | Management REST api-version=2020-08-01 を対象としています。  |
| Microsoft.Azure.Management.Search 3.0.0 | Stable | Management REST api-version=2015-08-19 を対象としています。  |

## <a name="azure-sdk-for-java"></a>Azure SDK for Java

| SDK バージョン | Status | 説明  |
|-------------|--------|------------------------------|
| [Java azure-search-documents 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stable | 2020 年 7 月にリリースされた、Azure Java SDK の新しいクライアント ライブラリです。 Search REST api-version=2019-05-06 を対象としています。 |
| [Java Management Client 1.35.0](/java/api/overview/azure/search/management) | Stable | Management REST api-version=2015-08-19 を対象としています。 |

## <a name="azure-sdk-for-javascript"></a>Azure SDK for JavaScript

| SDK バージョン | Status | 説明  |
|-------------|--------|------------------------------|
| [JavaScript @azure/search-documents 11.0](https://www.npmjs.com/package/@azure/search-documents) | Stable | 2020 年 7 月にリリースされた、Azure JavaScript と TypesScript SDK の新しいクライアント ライブラリです。 Search REST api-version=2016-09-01 を対象としています。 |
| [JavaScript @azure/arm-search](https://www.npmjs.com/package/@azure/arm-search) | Stable | Management REST api-version=2015-08-19 を対象としています。 |

## <a name="azure-sdk-for-python"></a>Azure SDK for Python

| SDK バージョン | Status | 説明  |
|-------------|--------|------------------------------|
| [Python azure-search-documents 11.0](https://pypi.org/project/azure-search-documents/) | Stable | 2020 年 7 月にリリースされた、Azure Python SDK の新しいクライアント ライブラリです。 Search REST api-version=2019-05-06 を対象としています。 |
| [Python azure-mgmt-search 8.0](https://pypi.org/project/azure-mgmt-search/) | Stable | Management REST api-version=2015-08-19 を対象としています。 |
