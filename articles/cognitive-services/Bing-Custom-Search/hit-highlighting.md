---
title: 装飾マーカーを使用してテキストを強調表示する - Bing Custom Search
titlesuffix: Azure Cognitive Services
description: 検索応答の文字飾りを有効にする方法を示します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 42b30b14e561fd3851a41701d2ecb8d98d5a02ed
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445559"
---
# <a name="using-decoration-markers-to-highlight-text"></a>装飾マーカーを使用してテキストを強調表示する

Bing は検索語の強調表示をサポートしています。検索語の強調表示により、結果の表示文字列内で検索語 (または Bing が関連すると見なしたその他の語) がマークされます。 たとえば、Web ページの `name`、`displayUrl`、`snippet` フィールドでは検索語をマークするとします。

Bing は、既定で、表示文字列に強調表示のマーカーを含めません。 マーカーを含めるには、要求に `textDecorations` クエリ パラメーターを含め、それを **true** に設定します。 Bing では、検索語のマークに Unicode 文字 E000 と E001 が使用され、検索語の開始と終了がマークされます。 たとえば、検索語が Sailing Dinghy で、いずれかの語がフィールドに存在する場合、次の例に示すように、その語が検索語の強調表示文字で囲まれます。  
  
![検索語の強調表示](./media/bing-hit-highlighting.PNG) 

ユーザー インターフェイスにこの文字列を表示する前に、Unicode 文字を、表示形式に適した文字に置き換えます。 たとえば、テキストを HTML として表示している場合は、E000 を <b\>、E001 を </b\> に置き換えて検索語を強調表示できます。 書式設定を適用しない場合は、文字列からマーカーを削除してください。 

Bing には、マーカーとして Unicode 文字または HTML タグを使用するオプションが用意されています。 使用するマーカーを指定するには、`textFormat` クエリ パラメーターを含めます。 Unicode 文字でコンテンツをマークするには `textFormat` を Raw (既定値) に設定し、HTML タグでコンテンツをマークするには `textFormat` を HTML に設定します。 
  
`textDecorations` が **true** の場合、Bing では、結果の表示文字列に次のマーカーを含めることができます。 HTML に相当するものがない場合、HTML のテーブル セルは空です。

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

次の例で示す `Computation` の結果には、log(2) という検索語の下付き文字マーカーが含まれています。 `expression` フィールドにマーカーが含まれるのは、`textDecoration` が **true** の場合のみです。

![計算のマーカー](./media/bing-markers-computation.PNG) 

要求によって装飾が要求されなかった場合、式は log10(2) になります。 
  
