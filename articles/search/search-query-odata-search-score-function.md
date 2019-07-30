---
title: OData の search.score 関数リファレンス - Azure Search
description: Azure Search クエリにおける OData の search.score 関数。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: dc444216c4677b9970b867e92aa5ae259a197220
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081848"
---
# <a name="odata-searchscore-function-in-azure-search"></a>Azure Search における OData の `search.score` 関数

[ **$orderby** パラメーター](search-query-odata-orderby.md) なしで Azure Search にクエリを送信した場合、返される結果は関連性スコア別に降順で並べ替えられます。 **$orderby** を使用する場合でも、既定では、関連性スコアを使用して優先順位が決められます。 ただし、関連性スコアを最初の並べ替え条件として使用し、その他のいくつかの条件を判断基準として使用するのが便利な場合もあります。 `search.score` 関数を使用すれば、それを行うことができます。

## <a name="syntax"></a>構文

**$orderby** の `search.score` の構文は `search.score()` です。 関数 `search.score` には、いかなるパラメーターも取得されません。 これは、 **$orderby** パラメーター内の他の句と同様に、`asc` または `desc` の並べ替え順序指定子と一緒に使用できます。 これは並べ替え条件のリスト内の任意の場所に配置できます。

## <a name="example"></a>例

`search.score` と `rating` に基づいて降順でホテルを並べ替え、その後、所与の座標からの距離別に昇順で並べ替え、2 つのホテルで評価が同じ場合、距離的に近い方が先に表示されるようにします。

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>次の手順  

- [Azure Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
