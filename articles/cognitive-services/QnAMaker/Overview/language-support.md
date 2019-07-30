---
title: 言語サポート - QnA Maker
titleSuffix: Azure Cognitive Services
description: ナレッジ ベースの言語は、ソースから質問と回答を自動的に抽出する QnA Maker の機能、およびユーザー クエリに対して QnA Maker が提供する結果の関連性に影響します。 QnA Maker のナレッジ ベースでサポートされるカルチャと自然言語の一覧。 同じナレッジ ベースに複数の言語を混在させないでください。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a543b17633b99bea63d72f46ba856a8b4593d16a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439546"
---
# <a name="language-support-for-qna-maker"></a>QnA Maker の言語サポート

ナレッジ ベースの言語は、[ソース](../Concepts/data-sources-supported.md)から質問と回答を自動的に抽出する QnA Maker の機能、およびユーザー クエリに対して QnA Maker が提供する結果の関連性に影響します。

## <a name="auto-extraction"></a>自動抽出
QnA Maker は任意の言語ページでの質問と回答の抽出をサポートしますが、QnA Maker は質問の識別にキーワードを使うため、抽出の有効性は次の言語の場合に非常に高くなります。

|サポートされている言語| ロケール|
|-----|----|
|英語|en-*|
|フランス語|fr-*|
|イタリア語|it-*|
|ドイツ語|de-*|
|スペイン語|es-*|

## <a name="primary-language-detection"></a>第一言語検出

検出に使用された第一言語は、最初のドキュメントまたは URL が最初のナレッジ ベースに追加されるときに、QnA Maker リソースと、そのリソースで作成されたすべてのナレッジ ベースに対して設定されます。 言語は変更できません。 

複数の言語をサポートする予定があるユーザーは、言語ごとに新しい QnA Maker リソースが必要です。 [言語ベースの QnA Maker ナレッジ ベースを作成](../how-to/language-knowledge-base.md)する方法をご覧ください。  

第一言語を確認する手順は次のとおりです。

1. [Azure Portal](https://portal.azure.com) にサインインします。  
1. QnA Maker リソースの一部として作成された Azure Search リソースを検索して選択します。 Azure Search リソース名は QnA Maker リソースと同じ名前で始まり、種類は**検索サービス**です。 
1. 検索リソースの **[概要]** ページで **[インデックス]** を選択します。 
1. **[testkb]** インデックスを選択します。
1. **[フィールド]** タブを選択します。 
1. **[質問]** フィールドと **[回答]** フィールドの **[アナライザー]** 列を表示します。 


## <a name="query-matching-and-relevance"></a>クエリの一致と関連性
QnA Maker は、Azure Search の[言語アナライザー](https://docs.microsoft.com/rest/api/searchservice/language-support)に依存して結果を提供します。 En-* 言語については、関連性が向上する特別な再ランキング機能を利用できます。

Azure Search の機能はサポートされている言語に従いますが、QnA Maker には Azure Search の結果より上位に追加のランカーがあります。 このランカー モデルでは、en-* においてはいくつかの特別なセマンティックとワード ベースの機能を使用しますが、他の言語ではまだ使用できません。 これらの機能は QnA Maker ランカーの内部動作の一部であるため、使用できるようにはしません。 

QnA Maker は、作成時に[ナレッジ ベースの言語を自動検出](#primary-language-detection)し、それに応じてアナライザーを設定します。 次の言語でナレッジ ベースを作成できます。 

|サポートされている言語|
|-----|
|アラビア語|
|アルメニア語|
ベンガル語|
|バスク語|
|ブルガリア語|
|カタルニア語|
|簡体字中国語|
|繁体字中国語|
|クロアチア語|
|チェコ語|
|デンマーク語|
|オランダ語|
|英語|
|エストニア語|
|フィンランド語|
|フランス語|
|ガリシア語|
|ドイツ語|
|ギリシャ語|
|グジャラート語|
|ヘブライ語|
|ヒンディー語|
|ハンガリー語|
|アイスランド語|
|インドネシア語|
|アイルランド語|
|イタリア語|
|日本語|
|カンナダ語|
|韓国語|
|ラトビア語|
|リトアニア語|
|マラヤーラム語|
|マレー語|
|ノルウェー語|
|ポーランド語|
|ポルトガル語|
|パンジャーブ語|
|ルーマニア語|
|ロシア語|
|セルビア語 (キリル)|
|セルビア語 (ラテン)|
|スロバキア語|
|スロベニア語|
|スペイン語|
|スウェーデン語|
|タミル語|
|テルグ語|
|タイ語|
|トルコ語|
|ウクライナ語|
|ウルドゥー語|
|ベトナム語|
