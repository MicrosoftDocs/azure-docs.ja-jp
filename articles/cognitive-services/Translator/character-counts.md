---
title: 文字数カウント - Translator
titleSuffix: Azure Cognitive Services
description: この記事では、コンテンツの取り込み方法を理解できるように、Azure Cognitive Services の Translator で文字数をカウントする方法について説明します。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7fad90cf47591b5ffab4232c2be3180138738f01
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992834"
---
# <a name="how-the-translator-counts-characters"></a>Translator での文字数のカウント方法

Translator では、入力テキストのすべての Unicode コード ポイントを文字としてカウントします。 テキストの言語への各翻訳は、複数の言語に翻訳する 1 つの API 呼び出し要求されたものであっても、個別の翻訳としてカウントされます。 応答の長さは関係ありません。

カウントされるもの:

* 要求の本文内の Translator に渡されるテキスト
   * Translate、Transliterate、Dictionary Lookup の各メソッドを使用する場合の `Text`
   * Dictionary Examples メソッドを使用する場合の `Text` と `Translation`
* すべてのマークアップ:要求本文のテキスト フィールド内の HTML タグ、XML タグなど。 要求のビルドに使用される JSON 表記 ("Text:" など) はカウントされません。
* 個々の文字
* 句読点
* スペース、タブ、マークアップ、あらゆる種類の空白文字
* Unicode で定義されているすべてのコード ポイント
* 以前に同じテキストを翻訳した場合でも、繰り返しの翻訳

中国語や日本語の漢字などの表意文字に基づくスクリプトでも、Translatorでは Unicode コード ポイントの数 (表意文字につき 1 文字) が引き続きカウントされます。 例外:Unicode サロゲートは 2 文字としてカウントされます。

要求、単語、バイト、または文章の数は、文字数とは関係ありません。

Detect メソッドと BreakSentence メソッドへの呼び出しは、文字消費においてカウントされません。 ただし、Detect メソッドと BreakSentence メソッドへの呼び出しは、カウントされる他の関数の使用に対して妥当な割合となっていることが求められます。 Detect または BreakSentence の呼び出しの数が、他のカウントされるメソッドの 100 倍を超えた場合に、Microsoft は Detect メソッドと BreakSentence メソッドの使用を制限する権利を留保します。

文字数のカウントの詳細については、「[Translator に関する FAQ](https://www.microsoft.com/en-us/translator/faq.aspx)」を参照してください。
