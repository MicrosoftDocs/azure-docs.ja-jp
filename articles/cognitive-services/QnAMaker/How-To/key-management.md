---
title: リソースとキーの管理 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker サービスは、サブスクリプション キーとエンドポイント キーの 2 種類のキーを扱います。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c1d8104f94d3f215108000b904848f3b968492ee
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341915"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>QnA Maker でキーを管理する方法

QnA Maker サービスは、**サブスクリプション キー**と**エンドポイント キー**の 2 種類のキーを扱います。

![キー管理](../media/qnamaker-how-to-key-management/key-management.png)

1. **サブスクリプション キー**: これらのキーは、[QnA Maker 管理サービス API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) へのアクセスに使用されます。 これらの API を使用して、ナレッジ ベースを編集できます。  

2. **エンドポイント キー**: これらのキーは、ナレッジ ベースのエンドポイントにアクセスして、ユーザーの質問に対する回答を取得する目的で使用されます。 通常、このエンドポイントは、QnA Maker サービスを利用するチャット ボットまたはクライアント アプリ コードで使用します。
 
## <a name="subscription-keys"></a>サブスクリプション キー
サブスクリプション キーは、QnA Maker リソースを作成した Azure portal で表示したりリセットしたりすることができます。 
1. Azure portal の QnA Maker リソースに移動します。

    ![QnA Maker のリソース リスト](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. **[キー]** に移動します。

    ![サブスクリプション キー](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>エンドポイント キー

エンドポイント キーは、[QnA Maker ポータル](https://qnamaker.ai)から管理することができます。

1. [QnA Maker ポータル](https://qnamaker.ai)にログインし、お使いのプロファイルに移動してから、**[サービスの設定]** をクリックします。

    ![エンドポイント キー](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. キーを表示またはリセットします。

    ![エンドポイント キー マネージャー](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >鍵漏えいのおそれがある場合は、キーを更新してください。 それに合わせて、クライアント アプリまたはボットのコードの変更が必要になることがあります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [別の言語によるナレッジ ベースの作成](./language-knowledge-base.md)
