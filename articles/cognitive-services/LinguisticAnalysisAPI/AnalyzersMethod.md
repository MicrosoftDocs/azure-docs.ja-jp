---
title: Analyzers メソッド - Lingistic Analysis API
titlesuffix: Azure Cognitive Services
description: アナライザの REST API は、Linguistic Analysis API が現在サポートしているアナライザーの一覧を表示します。
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 9338e87644554ac8b3121c5341cea6f2bc512a97
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878189"
---
# <a name="analyzers-method"></a>Analyzers メソッド

> [!IMPORTANT]
> Linguistic Analysis プレビューは、2018 年 8 月 9 日に使用停止になりました。 テキスト処理と分析には、[Azure Machine Learning テキスト解析モジュール](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)の使用をお勧めします。

**analyzers** REST API は、サービスによって現在サポートされているアナライザーの一覧を提供します。
応答には、それらの[名前](Analyzer-Names.md)とそれぞれでサポートされる言語 (英語の場合は "en" など) が含まれます。

## <a name="request-parameters"></a>要求パラメーター
なし

<br>

## <a name="response-parameters"></a>応答パラメーター
Name | type | 説明
-----|------|--------------
languages | string のリスト | このアナライザーを使用できる 2 文字の ISO 言語コードの一覧。
id   | 文字列 | このアナライザーの一意の ID
kind | 文字列 | アナライザーの大まかな種類
specification | 文字列 | このアナライザーに使用される仕様の名前
implementation | 文字列 | このアナライザーの背後にあるモデルやアルゴリズムの説明

<br>
## <a name="example"></a>例
GET /analyzers

応答:JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3",
        "implementation": "SplitMerge"
    },
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags",
        "specification": "PennTreebank3",
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens",
        "specification":"PennTreebank3",
        "implementation": "regexes"
    }
]
```
