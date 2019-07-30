---
title: ナレッジ ベース - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker のナレッジ ベースは、一連の質問と回答 (QnA) のペアと、各 QnA ペアに関連付けられている省略可能なメタデータで構成されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e40af9b2362ee52a1d00f29cdc112d3c2b9a842
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565850"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースとは

QnA Maker のナレッジ ベースは、一連の質問と回答 (QnA) のペアと、各 QnA ペアに関連付けられている省略可能なメタデータで構成されます。

## <a name="key-knowledge-base-concepts"></a>ナレッジ ベースの重要な概念

* **質問** - 質問には、ユーザーのクエリを最も適切に表すテキストが含まれます。 
* **回答** - 回答は、ユーザーのクエリが関連付けられている質問と一致するときに返される応答です。  
* **メタデータ** - メタデータは、QnA ペアに関連付けられ、キーと値のペアとして表されるタグです。 メタデータ タグは、QnA ペアをフィルター処理してクエリとのマッチングを行う対象を制限するために使用されます。

数値の QnA ID で表される 1 つの QnA には、さまざまなバリエーションの質問 (代替の質問) が含まれ、そのすべてが 1 つの回答にマッピングされています。 さらに、各ペアには複数のメタデータ フィールドを関連付けることができます (1 つのキーと 1 つの値)。

![QnA Maker ナレッジ ベース](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>ナレッジ ベースのコンテンツ形式

リッチ コンテンツをナレッジ ベースに取り込むと、QnA Maker はコンテンツを markdown に変換しようとします。 ほとんどのチャット クライアントが解釈できる markdown 形式について理解するには、[こちらの](https://aka.ms/qnamaker-docs-markdown-support)ブログをお読みください。

メタデータ フィールドは、コロンで区切られたキーと値のペアで構成されます **(製品:シュレッダー)** 。 キーと値は、どちらもテキストである必要があります。 メタデータのキーにスペースを含めることはできません。 メタデータでは、1 つのキーにつき 1 つの値だけがサポートされています。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker がユーザー クエリを処理して最適な回答を選択する方法

トレーニングされ、[公開された](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker ナレッジ ベースは、[GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage) で、ボットまたはその他のクライアント アプリケーションからユーザー クエリを受け取ります。 次の図は、ユーザー クエリを受け取ったときのプロセスを示しています。

![ユーザー クエリのランク付けプロセス](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

このプロセスについて次の表で説明します。

|手順|目的|
|--|--|
|1|クライアント アプリケーションがユーザー クエリを [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage) に送信します。|
|2|Qna Maker が、言語検出、スペル チェック プログラム、およびワード ブレーカーを使用してユーザー クエリを前処理します。|
|3|この前処理は、ユーザー クエリを変更して最適な検索結果を得るために行われます。|
|4|この変更されたクエリが Azure Search インデックスに送信され、`top` の数の結果を受け取ります。 正しい回答がこれらの結果にない場合、`top` の値をわずかに増やします。 一般的に `top` の値として 10 を使用すれば、90% のクエリに対応できます。|
|5|QnA Maker は、高度な特徴抽出を適用して、ユーザー クエリに対して取得された Azure Search の結果の正確性を判別します。 |
|6|トレーニング済みのランカー モデルにより、手順 5 からの特徴スコアを使用して、Azure Search の結果にランクが付けられます。|
|7|新しい結果が、ランク順にクライアント アプリケーションに返されます。|
|||

使用される特徴は、単語レベルのセマンティクス、コーパスの用語レベルの重要性、2 つのテキスト文字列間の類似性と関連性を判別するディープ ラーニング済みのセマンティック モデルなどですが、これらに限定されません。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースの開発ライフサイクル](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>関連項目

[QnA Maker の概要](../Overview/overview.md)
