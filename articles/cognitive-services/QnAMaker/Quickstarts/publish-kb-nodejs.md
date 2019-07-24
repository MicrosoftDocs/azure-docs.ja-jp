---
title: ナレッジ ベースの公開、REST、Node.js
titleSuffix: QnA Maker - Azure Cognitive Services
description: この Node.js では、ナレッジ ベース (KB) をプログラムから公開する手順を紹介しています。 公開すると、ナレッジ ベースの最新バージョンが、専用の Azure Search インデックスにプッシュされ、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 5538253459643400faf01b5999cfaf8780c05d29
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217956"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-nodejs"></a>クイック スタート: Node.js を使用して QnA Maker のナレッジ ベースを公開する

この REST ベースのクイック スタートでは、ナレッジ ベース (KB) をプログラムから公開する手順を紹介しています。 公開すると、ナレッジ ベースの最新バージョンが、専用の Azure Search インデックスにプッシュされ、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。

このクイック スタートで呼び出す QnA Maker API は次のとおりです。
* [公開](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - この API は、要求の本文に情報を必要としません。

## <a name="prerequisites"></a>前提条件

* [Node.js 6 以降](https://nodejs.org/en/download/)
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーを取得するには、ダッシュボードで **[リソース管理]** の **[キー]** を選択します。 
* URL の kbid クエリ文字列パラメーターに含まれている QnA Maker ナレッジ ベース (KB) ID (下図)。

    ![QnA Maker ナレッジ ベース ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    まだナレッジ ベースがない場合は、このクイック スタート用のサンプルを作成できます。[新しいナレッジ ベースを作成します](create-new-kb-nodejs.md)。


> [!NOTE] 
> 完全なソリューション ファイルは、[**Azure-Samples/cognitive-services-qnamaker-nodejs** GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/publish-knowledge-base-short)から入手できます。

## <a name="create-a-knowledge-base-nodejs-file"></a>ナレッジ ベース Node.js ファイルを作成する

`publish-knowledge-base.js` という名前でファイルを作成します。

## <a name="add-required-dependencies"></a>必要な依存関係を追加する

`publish-knowledge-base.js` の先頭に次の行を追加して、プロジェクトに必要な依存関係を追加します。

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=1-3 "Add the dependencies")]

## <a name="add-required-constants"></a>必要な定数を追加する

上記の必要な依存関係の後に、QnA Maker にアクセスするために必要な定数を追加します。 サンプルの値は実際の値に変更してください。

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=11-14 "Add required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>ナレッジ ベースを公開するための POST 要求を追加する

必要な定数の後に、次のコードを追加します。このコードは、ナレッジ ベースを公開するための HTTPS 要求を QnA Maker API に送信し、その応答を受信します。

[!code-nodejs[Add a POST request to publish knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=16-47 "Add a POST request to publish knowledge base")]

正常に公開されると、この API 呼び出しから状態 204 が返されます。応答の本文にはコンテンツが含まれません。 このコードでは、204 応答に対してコンテンツを追加しています。

それ以外の応答の場合は、その応答がそのまま返されます。

## <a name="run-the-program"></a>プログラムの実行

プログラムをビルドして実行します。 ナレッジ ベースを公開するための要求が QnA Maker API に自動的に送信され、応答がコンソール ウィンドウに出力されます。

ナレッジ ベースが公開された後は、クライアント アプリケーションまたはチャット ボットを使用して、エンドポイントからナレッジ ベースに対してクエリを実行できます。 

```bash
node publish-knowledge-base.js
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>次の手順

ナレッジ ベースが公開された後、[回答を生成するためのエンドポイント URL](../Tutorials/create-publish-answer.md#generating-an-answer) が必要になります。 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
