---
title: 装飾マーカーを使ってテキストを強調表示する方法 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Web Search API を使用して、検索結果にテキストの装飾とヒットの強調表示を使用する方法について説明します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: 32c3279cacddf10e77e8d245ba525ab766efd6fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351880"
---
# <a name="using-decoration-markers-to-highlight-text"></a>装飾マーカーを使用してテキストを強調表示する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Bing は検索語の強調表示をサポートしています。検索語の強調表示により、一部の結果の表示文字列内で検索語 (または Bing が関連すると見なしたその他の語) がマークされます。 たとえば、Web ページ結果の `name`、 `displayUrl`、および `snippet` の各フィールドには、マークされた検索語が含まれる場合があります。 

Bing は、既定で、表示文字列に強調表示のマーカーを含めません。 マーカーを有効にするには、要求に `textDecorations` クエリ パラメーターを含め、それを `true` に設定します。

## <a name="hit-highlighting-example"></a>検索結果の強調表示の例

次の例は `Sailing Dinghy` の Web 結果を示しています。 Bing では、Unicode 文字 E000 と E001 を使用して、検索語の開始と終了がマークされます。
  
![検索語の強調表示](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

ユーザー インターフェイスにこの結果を表示する前に、Unicode 文字を、表示形式に適した文字に置き換えます。

## <a name="marker-formatting"></a>マーカーの書式設定

Bing には、マーカーとして Unicode 文字または HTML タグのいずれかを使用するオプションが用意されています。 使用するマーカーを指定するには、[textFormat](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) クエリ パラメーターを含めます。 

| 値             | Marker                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Unicode 文字 (既定) |
| `textFormat=HTML` | HTML 文字              |

## <a name="additional-text-decorations"></a>その他の文字の装飾

Bing はいくつかの異なる文字装飾を返すことができます。 たとえば、`Computation` の結果には、`expression` フィールド内の検索語 `log(2)` の下付き文字マーカーを含めることができます。

![計算のマーカー](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

要求で装飾が指定されていない場合、`expression` フィールドには `log10(2)` が含まれます。 

`textDecorations` が `true` の場合、Bing では、結果の表示文字列に次のマーカーを含めることができます。 同等の HTML タグがない場合、表のセルは空になっています。

|Unicode|HTML|説明
|-|-|-
|U+E000|\<b>|検索語の開始をマークします (検索語の強調表示)
|U+E001|\</b>|検索語の終了をマークします
|U+E002|\<i>|斜体のコンテンツの開始をマークします 
|U+E003|\</i>|斜体のコンテンツの終了をマークします
|U+E004|\<br/>|改行をマークします
|U+E005||電話番号の開始をマークします
|U+E006||電話番号の終了をマークします
|U+E007||住所の開始をマークします
|U+E008||住所の終了をマークします
|U+E009|\&nbsp;|改行なしスペースをマークします
|U+E00C|\<strong>|太字のコンテンツの開始をマークします
|U+E00D|\</strong>|太字のコンテンツの終了をマークします
|U+E00E||背景がその周囲の背景より明るいコンテンツの開始をマークします
|U+E00F||背景がその周囲の背景より明るいコンテンツの終了をマークします
|U+E010||背景がその周囲の背景より暗いコンテンツの開始をマークします
|U+E011||背景がその周囲の背景より暗いコンテンツの終了をマークします
|U+E012|\<del>|取り消し線が引かれているコンテンツの開始をマークします
|U+E013|\</del>|取り消し線が引かれているコンテンツの終了をマークします
|U+E016|\<sub>|下付き文字のコンテンツの開始をマークします
|U+E017|\</sub>|下付き文字のコンテンツの終了をマークします
|U+E018|\<sup>|上付き文字のコンテンツの開始をマークします
|U+E019|\</sup>|上付き文字のコンテンツの終了をマークします

## <a name="next-steps"></a>次のステップ

* [Bing Web Search API とは](overview.md) 
* [サムネイルのサイズ変更とトリミング](resize-and-crop-thumbnails.md)