---
title: 動的ディクショナリ - Translator
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Translator の動的ディクショナリ機能の使用方法について説明します。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: de45867e717f001ab54e16c4b21f04494affd326
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996983"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>動的ディクショナリを使用する方法

単語や語句に適用する翻訳があらかじめわかっている場合は、それを要求内でマークアップとして指定することができます。 動的ディクショナリは、固有名詞や製品名のような複合名詞に対してのみ安全に使用できます。

**構文 :**

<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>

**要件:**

* `From` および `To` 言語には、英語と、それ以外のサポートされている言語を含める必要があります。 
* 自動検出機能を使用する代わりに、API translation 要求に `From` パラメーターを含める必要があります。 

**例: en-de:**

ソース入力: `The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

ターゲット出力: `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

この機能は、HTML を使用する場合と使用しない場合で同じように動作します。

この機能は控えめに使用してください。 翻訳をカスタマイズするより良い方法は、カスタム翻訳ツールを使用することです。 Custom Translator では、コンテキストおよび統計的確率を最大限に活用します。 コンテキスト内の単語または語句を表示するトレーニング データがある場合、またはそれを作成できる場合、はるかに良い結果が得られます。 Custom Translator の詳細については、[https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) で確認できます。