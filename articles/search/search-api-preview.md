---
title: Azure Search 2019-05-06-Preview 向けの REST API のプレビュー - Azure Search
description: Azure Search Service REST API バージョン 2019-05-06-Preview には、ナレッジ ストアや顧客管理の暗号化キーなどの試験的機能が含まれています。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 29d079c4e21352ced5fdcde44acaee66b79f6af9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876655"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Azure Search Service REST API バージョン 2019-05-06-Preview
この記事では、まだ一般公開されていない実験的機能を提供する Azure Search Service REST API の `api-version=2019-05-06-Preview` バージョンについて説明します。

> [!NOTE]
> プレビュー機能は、フィードバックの収集を目的とするテストと実験のために提供されており、変更される可能性があります。 運用アプリケーションではプレビュー API を使用しないことを強くお勧めします。


## <a name="new-in-2019-05-06-preview"></a>2019-05-06-Preview の新機能

[**ナレッジ ストア**](knowledge-store-concept-intro.md)は、AI ベースのエンリッチメント パイプラインの新しい宛先です。 Azure ストレージ内で、インデックスだけでなく、インデックス化の実行中に作成された設定済みのデータ構造も永続化できるようになりました。 スキルセット内の要素を通じてデータの物理構造を制御できます (データの形状をどのようにするか、データの保存先をテーブル ストレージと BLOB ストレージのどちらにするか、ビューが複数存在するかどうかなど)。

サービス側の暗号化の保存用の[**顧客が管理する暗号化キー**](search-security-manage-encryption-keys.md)も新しいプレビュー機能です。 Microsoft が管理する組み込みの暗号化の保存だけではなく、自分だけが鍵を所有している暗号化のレイヤーを追加で適用できます。

## <a name="other-preview-features"></a>他のプレビュー機能

以前のプレビューで発表された機能は、パブリック プレビューにもあります。 以前のプレビュー api-version で API を呼び出している場合は、引き続きそのバージョンを使用することも、想定される動作を変更することなく、`2019-05-06-Preview` に切り替えることもできます。

+ [moreLikeThis クエリ パラメーター](search-more-like-this.md)は、特定のドキュメントに関連するドキュメントを検索します。 この機能は、以前のプレビューからありました。 
* [CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)では、テキスト BLOB ごとに 1 つのドキュメントではなく、行ごとに 1 つのドキュメントを作成します。
* [Cosmos DB インデクサーでの MongoDB API のサポート](search-howto-index-cosmosdb.md)は、プレビュー段階です。


## <a name="how-to-call-a-preview-api"></a>プレビュー API を呼び出す方法

古いプレビューはまだ動作しますが、時間がたてば古くなります。 コードで `api-version=2016-09-01-Preview` または `api-version=2017-11-11-Preview` を呼び出している場合、それらの呼び出しはまだ有効です。 ただし、機能強化の更新が行われるのは、最新のプレビュー バージョンのみです。 

次のサンプル構文は、API のプレビュー バージョンの呼び出しを示しています。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Search サービスは複数のバージョンで使用できます。 詳しくは、[API バージョン](search-api-versions.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

Azure Search Service REST API リファレンス ドキュメントをレビューします。 問題が発生した場合は、[StackOverflow](https://stackoverflow.com/) または[サポートへの問い合わせ](https://azure.microsoft.com/support/community/?product=search)でサポートを依頼してください。

> [!div class="nextstepaction"]
> [Search Service REST API リファレンス](https://docs.microsoft.com/rest/api/searchservice/)