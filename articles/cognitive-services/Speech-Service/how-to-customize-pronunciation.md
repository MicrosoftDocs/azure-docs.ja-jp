---
title: 発音をカスタマイズする - Speech Services
titlesuffix: Azure Cognitive Services
description: Speech Service で発音をカスタマイズする方法について説明します。 カスタムの発音を使用すると、発音形式と単語または用語の表示を定義できます。 製品名や頭字語などのカスタマイズされた用語を処理する場合に便利です。 始めるにあたって必要なのは、発音ファイル (単純な .txt ファイル) のみです。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: f825cf8f381a7a2974b150a74a091412b24b09bc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791185"
---
# <a name="enable-custom-pronunciation"></a>カスタムの発音を有効にする

カスタムの発音を使用すると、発音形式と単語または用語 (頭字語) の表示を定義できます。 製品名や頭字語などのカスタマイズされた用語を処理する場合に便利です。 始めるにあたって必要なのは、発音ファイル (単純な .txt ファイル) のみです。

しくみは次のとおりです。 1 つの .txt ファイルにカスタムの発音エントリを複数入力できます。 構造は次のとおりです。

```
Display form <Tab> Spoken form <Newline>
```

次の表に、いくつかの例を示します。

| 表示形式 | 音声フォーム |
|----------|-------|
| 3CPO | see three pea o |
| L8R | late are |
| CNTK | c n t k|

## <a name="requirements-for-the-spoken-form"></a>音声フォームの要件

音声フォームは小文字にする必要があります (これはインポート時に強制できます)。 データ インポーターでチェックを提供する必要もあります。 音声フォームまたは表示フォームにタブを使用することはできません。 ただし、表示フォームでは、他にも禁止文字が存在する可能性があります (~ や ^ など)。

次のイメージに示すように、各 .txt ファイルには、複数のエントリを含めることができます。

![頭字語の発音の例](media/stt/custom-speech-pronunciation-file.png)

音声フォームは、表示フォームの音声シーケンスです。 文字、単語、または音節で構成されています。 現時点では、音声フォームの編成に役立つその他のガイダンスや一連の標準はありません。

## <a name="supported-pronunciation-characters"></a>サポートされている発音文字

現在、カスタムの発音は、英語 (en-US) とドイツ語 (de-de) でサポートされています。 (カスタムの発音ファイル内の) 用語の音声フォームを表現するために使用できる文字セットを次の表に示します。

| 言語 | 文字 |
|---------- |----------|
| 英語 (en-Us) | a、b、c、d、e、f、g、h、i、j、k、l、m、n、o、p、q、r、s、t、u、v、w、x、y、z |
| ドイツ語 (de-DE) | ä、ö、ü、?、a、b、c、d、e、f、g、h、i、j、k、l、m、n、o、p、q、r、s、t、u、v、w、x、y、z |

> [!NOTE]
> 用語の表示フォーム (発音ファイル内) は、言語適応データセットと同じ方法で記述する必要があります。

## <a name="requirements-for-the-display-form"></a>表示フォームの要件

表示フォームにすることができるのは、既存の単語を組み合わせたカスタムの単語、頭字語、または複合語のみです。

>[!NOTE]
>この機能を使用して一般的な単語を編成し直したり、音声フォームを変更したりすることはお勧めしません。 この機能を使用する前に、一部の独特な単語 (略語、専門用語、外来語など) が正しく転記されているかどうかを確認することをお勧めします。 正しくデコードされている場合は、カスタムの発音ファイルに追加します。 言語モデルでは、常に単語の表示フォームのみを使用する必要があります。

## <a name="requirements-for-the-file-size"></a>ファイル サイズの要件
発音エントリを含む .txt ファイルのサイズは 1 メガバイト (Free レベルのキーでは 1 KB) に制限されています。 通常、このファイルを介して大量のデータをアップロードする必要はありません。 ほとんどの場合、カスタムの発音ファイルは、数キロバイト (KB) のサイズです。 すべてのロケールの .txt ファイルは、UTF-8 BOM 形式でエンコードされている必要があります。 英語のロケールでは、ANSI も使用できます。

## <a name="next-steps"></a>次の手順
* [カスタムの音響モデル](how-to-customize-acoustic-models.md)を作成して認識精度を向上させます。
* [カスタムの言語モデル](how-to-customize-language-model.md)を作成して認識精度を向上させます。
