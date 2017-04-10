---
title: "ロジック アプリに Bing Search コネクタを追加する | Microsoft Docs"
description: "Bing Search コネクタと REST API パラメーターの概要"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: a7f530e8-1573-4612-8899-c9c84aa2de6d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: dbb920f212d46365233e83ba1e0a0ae99917e405
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-bing-search-connector"></a>Bing Search コネクタの概要
Bing Search に接続すると、ニュース、動画など検索できます。 Bing Search では、次の操作を実行できます。 

* 検索から取得したデータに基づいてビジネス フローを構築することができます。 
* 画像、ニュースなどを検索するアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、動画を検索し、Twitter を使用して Twitter フィードに動画を投稿することができます。

ロジック アプリに操作を追加する方法については、 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関するページを参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
Bing Search には、次のアクションがあります。 トリガーはありません。 

| トリガー | アクション |
| --- | --- |
| なし |<ul><li>Web を検索する</li><li>動画を検索する</li><li>画像を検索する</li><li>ニュースを検索する</li><li>関連項目を検索する</li><li>候補を検索する</li><li>すべてを検索する</li></ul> |

すべてのコネクタは、JSON および XML 形式のデータに対応します。

## <a name="swagger-rest-api-reference"></a>Swagger REST API リファレンス
適用されるバージョン: 1.0。

### <a name="search-web"></a>Web を検索する
Bing Search から Web サイトを取得します。  
```GET: /Web```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。 有効な値:  <ul><li>オフ</li><li>中</li><li>高</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: en-US) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |
| webFileType |string |× |query |なし |検索を絞り込むファイルの種類 (例: 'DOC') |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="search-videos"></a>動画を検索する
Bing Search から動画を取得します。  
```GET: /Video```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。 有効な値:  <ul><li>オフ</li><li>中</li><li>高</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: en-US) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |
| videoFilters |string |× |query |なし |サイズ、縦横比、イベント ログ、スタイル、向き、またはそれらの組み合わせに基づいて検索をフィルター処理します。  有効な値:  <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>例: 'Duration:Short+Resolution:High' |
| videoSortBy |string |× |query |なし |結果の並べ替え順。 有効な値:  <ul><li>日付</li><li>関連性</li></ul> <p>日付の並べ替え順序は降順です。</p> |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="search-images"></a>画像を検索する
Bing Search から画像を取得します。  
```GET: /Image```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。 有効な値:  <ul><li>オフ</li><li>中</li><li>高</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: en-US) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |
| imageFilters |string |× |query |なし |サイズ、縦横比、イベント ログ、スタイル、向き、またはそれらの組み合わせに基づいて検索をフィルター処理します。 有効な値:  <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>例: 'Size:Small+Aspect:Square' |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="search-news"></a>ニュースを検索する
Bing Search からニュースの検索結果を取得します。  
```GET: /News```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。 有効な値:  <ul><li>オフ</li><li>中</li><li>高</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: en-US) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |
| newsSortBy |string |× |query |なし |結果の並べ替え順。 有効な値:  <ul><li>日付</li><li>関連性</li></ul> <p>日付の並べ替え順序は降順です。</p> |
| newsCategory |string |× |query | |検索を絞り込むニュースのカテゴリ (例: 'rt_Business') |
| newsLocationOverride |string |× |query |なし |Bing の場所検出を上書きします。 このパラメーターは、en-US 市場でのみ使用できます。 入力形式は、US./<state /> (例: 'US.WA') です。 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="search-spellings"></a>候補を検索する
検索候補を取得します。  
```GET: /SpellingSuggestions```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。 有効な値:  <ul><li>オフ</li><li>中</li><li>高</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: en-US) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="search-related"></a>関連項目を検索する
Bing Search から関連する検索結果を取得します。  
```GET: /RelatedSearch```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。 有効な値:  <ul><li>オフ</li><li>中</li><li>高</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: en-US) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="search-all"></a>すべてを検索する
Bing Search から Web サイト、動画、画像など、すべてを取得します。  
```GET: /CompositeSearch```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| query |string |○ |query |なし |検索するテキスト (例: 'xbox') |
| maxResult |integer |× |query |なし |返される結果の最大数 |
| startOffset |integer |× |query |なし |スキップする結果の数 |
| adultContent |string |× |query |なし |成人向けコンテンツ フィルター。 有効な値:  <ul><li>オフ</li><li>中</li><li>高</li></ul> |
| market |string |× |query |なし |検索を絞り込む市場または地域 (例: en-US) |
| longitude |number |× |query |なし |検索を絞り込む経度 (東西の座標、例: 47.603450) |
| latitude |number |× |query |なし |検索を絞り込む緯度 (南北の座標、例: -122.329696) |
| webFileType |string |× |query |なし |検索を絞り込むファイルの種類 (例: 'DOC') |
| videoFilters |string |× |query |なし |サイズ、縦横比、イベント ログ、スタイル、向き、またはそれらの組み合わせに基づいて検索をフィルター処理します。  有効な値:  <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>例: 'Duration:Short+Resolution:High' |
| videoSortBy |string |× |query |なし |結果の並べ替え順。 有効な値:  <ul><li>日付</li><li>関連性</li></ul> <p>日付の並べ替え順序は降順です。</p> |
| imageFilters |string |× |query |なし |サイズ、縦横比、イベント ログ、スタイル、向き、またはそれらの組み合わせに基づいて検索をフィルター処理します。 有効な値:  <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>例: 'Size:Small+Aspect:Square' |
| newsSortBy |string |× |query |なし |結果の並べ替え順。 有効な値:  <ul><li>日付</li><li>関連性</li></ul> <p>日付の並べ替え順序は降順です。</p> |
| newsCategory |string |× |query |なし |検索を絞り込むニュースのカテゴリ (例: 'rt_Business') |
| newsLocationOverride |string |× |query |なし |Bing の場所検出を上書きします。 このパラメーターは、en-US 市場でのみ使用できます。 入力形式は、US./<state /> (例: 'US.WA') です。 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
#### <a name="webresultmodel-bing-web-search-results"></a>WebResultModel: Bing の Web 検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| タイトル |string |× |
| 説明 |string |× |
| DisplayUrl |string |× |
| ID |string |× |
| FullUrl |string |× |

#### <a name="videoresultmodel-bing-video-search-results"></a>VideoResultModel: Bing の動画検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| タイトル |string |× |
| DisplayUrl |string |× |
| ID |string |× |
| MediaUrl |string |× |
| ランタイム |integer |× |
| サムネイル |未定義 |× |

#### <a name="thumbnailmodel-thumbnail-properties-of-the-multimedia-element"></a>ThumbnailModel: マルチメディア要素のサムネイルのプロパティ
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| MediaUrl |string |× |
| ContentType |string |× |
| 幅 |integer |× |
| 高さ |integer |× |
| FileSize |integer |× |

#### <a name="imageresultmodel-bing-image-search-results"></a>ImageResultModel: Bing の画像検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| タイトル |string |× |
| DisplayUrl |string |× |
| ID |string |× |
| MediaUrl |string |× |
| SourceUrl |string |× |
| サムネイル |未定義 |× |

#### <a name="newsresultmodel-bing-news-search-results"></a>NewsResultModel: Bing のニュース検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| タイトル |string |× |
| 説明 |string |× |
| DisplayUrl |string |× |
| ID |string |× |
| から |string |× |
| Date |string |× |

#### <a name="spellresultmodel-bing-spelling-suggestions-results"></a>SpellResultModel: Bing の候補の検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |× |
| 値 |string |× |

#### <a name="relatedsearchresultmodel-bing-related-search-results"></a>RelatedSearchResultModel: Bing の関連項目の検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| タイトル |string |× |
| ID |string |× |
| BingUrl |string |× |

#### <a name="compositesearchresultmodel-bing-composite-search-results"></a>CompositeSearchResultModel: Bing の複合検索結果
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| WebResultsTotal |integer |× |
| ImageResultsTotal |integer |× |
| VideoResultsTotal |integer |× |
| NewsResultsTotal |integer |× |
| SpellSuggestionsTotal |integer |× |
| WebResults |array |× |
| ImageResults |array |× |
| VideoResults |array |× |
| NewsResults |array |× |
| SpellSuggestionResults |array |× |
| RelatedSearchResults |array |× |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。

[API リスト](apis-list.md)に戻ります。


