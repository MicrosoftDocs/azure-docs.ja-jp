---
title: ナレッジ ベース - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker のナレッジ ベースは、一連の質問と回答 (QnA) のペアと、各 QnA ペアに関連付けられている省略可能なメタデータで構成されます。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 02111ac90fe97ddaddbd41ad42410e7e76f1c405
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311106"
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

メタデータ フィールドは、コロンで区切られたキーと値のペアで構成されます **(製品:シュレッダー)**。 キーと値は、どちらもテキストである必要があります。 メタデータのキーにスペースを含めることはできません。 メタデータでは、1 つのキーにつき 1 つの値だけがサポートされています。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースの開発ライフサイクル](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>関連項目

[QnA Maker の概要](../Overview/overview.md)
