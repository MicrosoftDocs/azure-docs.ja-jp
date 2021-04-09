---
title: インクルード ファイル
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: mingshen
ms.date: 05/19/2020
ms.openlocfilehash: 5197b71db4f46d9908d5539c114d636b9474a870
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95758685"
---
説明がより関心を引くものになるように、HTML タグを使用して書式を設定します。 この表に示すように、ほとんどのタグには、開始と終了の両方のコンポーネントが必要です。

|   目的  |  用途  |  例  |  表示結果   |
| --- | --- | --- | --- |
|   太字  |  `<b> & </b>`  |  `<b>`例`</b>`:無料試用版  |  **例**:無料試用版   |
|   斜体  |  `<i> & </i>`  |  `<i>`最初`</i>` の行を選択します。  |  "*最初*" の行を選択します。   |
|   改行  |  `<br>`  |  これは最初の行です。`<br>`これは 2 行目です。  |  これは最初の行です。<br>これは 2 行目です。  |
|  段落  |  `<p> & </p>`  |  `<p>`これは第 1 段落です。`</p>`<br>`<p>`これは第 2 段落です。`</p>`   |   <p>これは第 1 段落です。</p><p>これは第 2 段落です。</p>   |
|   箇条書き  |  `<ul> & </ul>, <li> & </li>`  |  `<ul>`<br>`<li>`箇条書き項目`</li>`<br>`<li>`箇条書き項目`</li>`<br>`<li>`箇条書き項目`</li>`<br>`</ul>`  |  • 箇条書き項目<br>• 箇条書き項目<br>• 箇条書き項目   |
|   番号付け  |  `<ol> & </ol>, <li> & </li>`  |  `<ol>`<br>`<li>`番号付きの項目`</li>`<br>`<li>`番号付きの項目`</li>`<br>`<li>`番号付きの項目`</li>`<br>`</ol>`   |   1.番号付きの項目<br>2.番号付きの項目<br>3.番号付きの項目   |
|   新しい番号 (または箇条書きの黒点) のない改行  |  `<ol> & </ol>, <li> & </li>, <br>`  |  `<ol>`<br>`<li>`ここにテキストを追加`</li>`<br>`<li>`ここにテキストを追加`<br>`ここにテキストを追加`</li>`<br>`</ol>`  |  1. ここにテキストを追加<br>2. ここにテキストを追加<br>&nbsp;&nbsp;&nbsp;&nbsp;ここにテキストを追加   |
|   新しい番号 (または箇条書きの黒点) のない段落  |  `<ol> & </ol>, <li> & </li>, <br>`  |  `<ol>`<br>`<li>`ここにテキストを追加`</li>`<br>`<li>`ここにテキストを追加`<br><br>`ここにテキストを追加`</li>`<br>`</ol>`  |  1. ここにテキストを追加<br>2. ここにテキストを追加<p>&nbsp;&nbsp;&nbsp;&nbsp;ここにテキストを追加   |
|   見出しの使用  |  `<h1> & </h1>, <h2> & </h2>`、`<h6> & </h6>` まで  |  `<h1>`これは見出し 1 です`</h1>`<br>`<h2>`これは見出し 2 です`</h2>`<br>`<h3>`これは見出し 3 です`</h3>`  |  **<font size="+3">これは見出し 1 です</font>**<br>**<font size="+2">これは見出し 2 です</font>**<br>**<font size="+1">これは見出し 3 です</font>**  |
| | | |
