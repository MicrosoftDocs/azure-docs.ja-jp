---
title: バッチ テストの TSV 形式 - QnA Maker
titleSuffix: Azure Cognitive Services
description: バッチ テストの TSV 形式について
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 0ed948b4bf69fc672a59a7825279a12868d10521
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "87132128"
---
# <a name="batch-testing-tsv-format"></a>バッチ テストの TSV 形式

バッチ テストは、[ソース コード](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)から、または [ダウンロード可能な zip 形式の実行可能ファイル](https://aka.ms/qna_btzip)として入手できます。 バッチ テストを実行するコマンドの形式は次のとおりです。

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|必要な値|
|--|--|
|1|[TSV 入力フィールド](#tsv-input-fields)でフォーマットされた tsv ファイルの名前|
|2|エンドポイントの URI および QnA Maker ポータルの [発行] ページのホスト名を使用します。|
|3|エンドポイントキー。QnA Maker ポータルの [発行] ページにあります。|
|4|結果のバッチ テストによって作成された tsv ファイルの名前。|

次の情報を使用して、バッチ テスト用の TSV 形式を理解し、実装します。 

## <a name="tsv-input-fields"></a>TSV 入力フィールド

|TSV 入力ファイルのフィールド|Notes|
|--|--|
|KBID|[発行] ページの KB ID。|
|Question|ユーザーが入力する質問。|
|メタデータ タグ|省略可能|
|上位のパラメーター|省略可能| 
|期待される回答の ID|省略可能|

![バッチ テスト用の TSV ファイルの入力形式。](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV 出力フィールド 

|TSV 出力ファイル パラメーター|ノート|
|--|--|
|KBID|[発行] ページの KB ID。|
|Question|入力ファイルから入力された質問。|
|回答|ナレッジ ベースからの上位の回答|
|回答 ID|回答 ID|
|Score|回答の予測スコア。 |
|メタデータ タグ|返された回答に関連付けられている|
|期待される回答の ID|省略可能 (期待される回答の ID が指定されている場合のみ)|
|判断ラベル|省略可能。値は correct または incorrect になります (期待される回答が指定されている場合のみ)。|
