---
title: 単語アラインメント - Translator
titleSuffix: Azure Cognitive Services
description: アラインメント情報を受信するには、Translate メソッドを使用し、省略可能な includeAlignment パラメーターを含めます。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7288087bfe7d2a7bb03ce8a99831ad8b7f5b549f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995617"
---
# <a name="how-to-receive-word-alignment-information"></a>単語アラインメント情報を受信する方法

## <a name="receiving-word-alignment-information"></a>単語アラインメント情報の受信
アラインメント情報を受信するには、Translate メソッドを使用し、省略可能な includeAlignment パラメーターを含めます。

## <a name="alignment-information-format"></a>アラインメント情報の形式
アライメントは、ソースのすべての単語について、次の形式の文字列値として返されます。 各単語の情報は、中国語のようなスペースで区切られない言語 (書記法) の場合を含め、スペースで区切られます。

[[SourceTextStartIndex]\:[SourceTextEndIndex]–[TgtTextStartIndex]\:[TgtTextEndIndex]] *

アラインメント文字列は、たとえば、"0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21" のようになります。

言い換えると、コロンによって開始および終了インデックスが区切られ、ダッシュによって言語が区切られ、スペースによって単語が区切られます。 1 つの単語が他の言語では 0 個、1 個、または複数個の単語にアライメントされる場合があり、アライメントされた単語が連続していない場合もあります。 アライメント情報を使用できない場合は、Alignment 要素が空になります。 このメソッドは、その場合にエラーを返しません。

## <a name="restrictions"></a>制限
現時点でアライメントが返されるのは、一部の言語ペアに対してのみです。
* 英語から他の任意の言語へ
* 他の任意の言語から英語へ。ただし、繁体字中国語、繁体字中国語、およびラトビア語から英語は除く
* 日本語から韓国語、または韓国語から日本語については、文がお決まりの翻訳である場合、アライメント情報は受け取りません。 お決まりの翻訳の例は、"これはテストです" や "あなたを愛しています" などの、頻繁に使われる文です。

## <a name="example"></a>例

JSON の例

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
