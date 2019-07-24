---
title: Azure Search 2017-11-11-Preview 向けのプレビュー REST API - Azure Search
description: Azure Search Service REST API バージョン 2017-11-11-Preview には、シノニムや moreLikeThis 検索などの試験的機能が含まれています。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 06/28/2018
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 524c1a6d083db02349c7dae9a0131228613dc170
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997612"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Azure Search Service REST api-version 2017-11-11-Preview
この記事では、まだ一般公開されていない実験的機能を提供する Azure Search Service REST API の `api-version=2017-11-11-Preview` バージョンについて説明します。

> [!NOTE]
> プレビュー機能は、フィードバックの収集を目的とするテストと実験のために提供されており、変更される可能性があります。 運用アプリケーションではプレビュー API を使用しないことを強くお勧めします。


## <a name="new-in-2017-11-11-preview"></a>2017-11-11-Preview の新機能

[**オートコンプリート**](search-autocomplete-tutorial.md)が既存の [Suggestions API](https://docs.microsoft.com/rest/api/searchservice/suggestions) に追加され、検索バーで補完的な先行入力機能を利用できるようになりました。 オートコンプリートは、ユーザーが以降の検索でクエリ文字列として選択できる候補の検索語を返します。 Suggestions は、部分的な入力に応答して実際のドキュメントを返します。検索結果は即時に表示され、検索語の入力が長さと特異性が増すにつれて動的に変化します。

[**コグニティブ検索**](cognitive-search-concept-intro.md)は、Azure Search における新しい強化機能で、テキスト以外のソースや区別されていないテキスト内の潜在的な情報を検索し、それを Azure Search でフルテキスト検索可能なコンテンツに変換します。 プレビューの REST API では、次のリソースが導入または変更されています。 その他のすべての REST API は、一般公開バージョンまたはプレビュー バージョンのどちらを呼び出しても同じです。

+ [スキルセット操作 (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/skillset-operations)

+ [インデクサーの作成 (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [定義済みのスキル](cognitive-search-predefined-skills.md)

その他の REST API は、api-version の設定に関係なく、すべて同じです。 たとえば、`GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` と `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (`Preview` なし) は機能的に同じです。

## <a name="other-preview-features"></a>他のプレビュー機能

以前のプレビューで発表された機能は、パブリック プレビューにもあります。 以前のプレビュー api-version で API を呼び出している場合は、引き続きそのバージョンを使用することも、想定される動作を変更することなく、`2017-11-11-Preview` に切り替えることもできます。

+ `api-version=2015-02-28-Preview` で導入された [Azure BLOB インデックス作成での CSV ファイル](search-howto-index-csv-blobs.md)は、プレビュー機能のままです。 この機能は Azure BLOB インデックス作成の一部であり、パラメーターの設定を通じて呼び出されます。 CSV ファイルの各行は、個別のドキュメントとしてインデックスが作成されます。

+ `api-version=2015-02-28-Preview` で導入された [Azure BLOB インデックス作成での JSON 配列](search-howto-index-json-blobs.md)は、プレビュー機能のままです。 この機能は Azure BLOB インデックス作成の一部であり、パラメーターの設定を通じて呼び出されます。 配列内の各要素は、個別のドキュメントとしてインデックスが作成されます。

+ [moreLikeThis クエリ パラメーター](search-more-like-this.md)は、特定のドキュメントに関連するドキュメントを検索します。 この機能は、以前のプレビューからありました。 


## <a name="how-to-call-a-preview-api"></a>プレビュー API を呼び出す方法

古いプレビューはまだ動作しますが、時間がたてば古くなります。 コードで `api-version=2016-09-01-Preview` または `api-version=2015-02-28-Preview` を呼び出している場合、それらの呼び出しはまだ有効です。 ただし、機能強化の更新が行われるのは、最新のプレビュー バージョンのみです。 

次のサンプル構文は、API のプレビュー バージョンの呼び出しを示しています。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Azure Search サービスは複数のバージョンで使用できます。 詳しくは、[API バージョン](search-api-versions.md)に関するページをご覧ください。
