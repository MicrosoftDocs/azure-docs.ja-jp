---
title: Azure Search の moreLikeThis (プレビュー) - Azure Search
description: Azure Search REST API で公開されている moreLikeThis (プレビュー) 機能の暫定版ドキュメント。
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d55a6d883e0dcd5ad4b1c1584b76bae06e6c742a
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569044"
---
# <a name="morelikethis-in-azure-search-preview"></a>Azure Search の moreLikeThis (プレビュー)

`moreLikeThis=[key]` は、[Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents) のクエリ パラメーターです。 検索クエリで `moreLikeThis` パラメーターを指定することで、ドキュメント キーに指定されたドキュメントに類似するドキュメントを検索できます。 `moreLikeThis` を指定して検索を要求すると、指定したドキュメントの内容を最も適切に説明する検索用語がドキュメントから抽出され、その用語を使用してクエリが生成されます。 生成されたクエリを使用して、検索要求が作成されます。 既定では、`searchFields` パラメーターを使用してフィールドを制限していない限り、すべての `searchable` フィールドの内容が考慮されます。 `moreLikeThis` パラメーターは、`search=[string]` 検索パラメーターと一緒に使用することはきません。

## <a name="examples"></a>例 

moreLikeThis クエリの例を次に示します。 このクエリは、説明フィールドの内容が `moreLikeThis` パラメーターに指定されたソース ドキュメントのフィールドに最も類似しているドキュメントを検索します。

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```

## <a name="feature-availability"></a>使用可能な機能

moreLikeThis 機能は現在プレビュー段階にあり、preview api-version `2015-02-28-Preview` と `2016-09-01-Preview` でのみサポートされています。 API バージョンは要求で指定するため、同じアプリで一般公開 (GA) API とプレビュー API を組み合わせることができます。 ただし、プレビュー API は SLA の対象ではなく、機能が変更される場合があるため、実稼働アプリケーションでの使用はお勧めしません。
