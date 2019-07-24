---
title: Bing Spell Check API とは
titlesuffix: Azure Cognitive Services
description: Bing Spell Check API について説明します。コンテキストに応じたスペル チェックを行うために、この API では機械学習と統計的機械翻訳が使用されています。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 22f75efb3cb4baa645030e7ad64072674de662ed
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890809"
---
# <a name="what-is-the-bing-spell-check-api"></a>Bing Spell Check API とは

Bing Spell Check API を使用すると、コンテキストに応じた文法とスペルのチェックをテキストに対して実行できます。 ほとんどのスペルチェック機能はディクショナリベースのルール セットに依存しますが、Bing のスペルチェック機能は、機械学習と統計的機械翻訳を活用することでコンテキストに応じた正確な修正を実現しています。 

## <a name="features"></a>機能


|  |  |
|---------|---------|
|複数のスペル チェック モード     | 複数のスペル チェック モードを使用して、文法やスペルに重点を置いた修正を行うことができます。 |
|スラングやくだけた言葉遣いの認識     | テキスト内で使用されている日常的な表現やくだけた用語を認識します。         |
|似ている単語の区別     | 発音は似ているものの意味が異なる単語間 (例: "see" と "sea") で正しい用法を探し出します。        |
|ブランド、タイトル、普及している用法のサポート     | 新しいブランドやタイトルなど、普及しつつある表現を認識します。 |

## <a name="workflow"></a>ワークフロー

Bing Spell Check API は、HTTP 要求を作成して JSON 応答を解析できる任意のプログラミング言語から簡単に呼び出すことができます。 このサービスには、REST API または Bing Spell Check SDK を使用してアクセスできます。 

1. Bing Search API が利用できる [Cognitive Services API アカウント](../cognitive-services-apis-create-account.md)を作成します。 Azure サブスクリプションをお持ちでない場合は、無料のアカウントを作成できます。 
2. 要求を Bing Web Search API に送信します。
3. JSON 応答を解析します

## <a name="next-steps"></a>次の手順

まず、Bing Spell Check Search API の[対話型デモ](https://azure.microsoft.com/services/cognitive-services/spell-check/)で、さまざまなテキストをいかにすばやくチェックできるかを見てみましょう。

API を呼び出す準備ができたら、[Cognitive Services API アカウント](../../cognitive-services/cognitive-services-apis-create-account.md)を作成します。 Azure サブスクリプションをお持ちでない場合は、無料で[アカウントを作成](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)できます。
