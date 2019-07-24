---
title: ナレッジ ベースの公開、REST、Go
titleSuffix: QnA Maker - Azure Cognitive Services
description: この Go REST ベースのクイック スタートでは、ナレッジ ベースを公開する手順を紹介しています。公開時に、テスト済みのナレッジ ベースの最新バージョンが、公開済みのナレッジ ベースを表す専用の Azure Search インデックスにプッシュされます。 また、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 058365f4dcc6e4be7c14c162afb1c0d4a9f3d9a6
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213502"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>クイック スタート: Go を使用して QnA Maker のナレッジ ベースを公開する

この REST ベースのクイック スタートでは、ナレッジ ベース (KB) をプログラムから公開する手順を紹介しています。 公開すると、ナレッジ ベースの最新バージョンが、専用の Azure Search インデックスにプッシュされ、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。

このクイック スタートで呼び出す QnA Maker API は次のとおりです。
* [公開](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - この API は、要求の本文に情報を必要としません。

## <a name="prerequisites"></a>前提条件

* [Go 1.10.1](https://golang.org/dl/)
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーを取得するには、ダッシュボードで **[リソース管理]** の **[キー]** を選択します。 

* URL の kbid クエリ文字列パラメーターに含まれている QnA Maker ナレッジ ベース (KB) ID (下図)。

    ![QnA Maker ナレッジ ベース ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    まだナレッジ ベースがない場合は、このクイック スタート用のサンプルを作成できます。[新しいナレッジ ベースの作成](create-new-kb-csharp.md)に関するページを参照してください。

> [!NOTE] 
> 完全なソリューション ファイルは、[**Azure-Samples/cognitive-services-qnamaker-go** GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base)から入手できます。

## <a name="create-a-go-file"></a>Go ファイルを作成する

VSCode を開き、`publish-kb.go` という名前の新しいファイルを作成します。

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

`publish-kb.go` の先頭に次の行を追加して、プロジェクトに必要な依存関係を追加します。

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>main 関数を作成する

必要な依存関係の後に、次のクラスを追加します。

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>必要な定数を追加する

**main** 


 関数内に、QnA Maker にアクセスするために必要な定数を追加します。 サンプルの値は実際の値に変更してください。

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>KB を公開するための POST 要求を追加する

必要な定数の後に、次のコードを追加します。このコードは、ナレッジ ベースを公開するための HTTPS 要求を QnA Maker API に送信し、その応答を受信します。

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

正常に公開されると、この API 呼び出しから状態 204 が返されます。応答の本文にはコンテンツが含まれません。 このコードでは、204 応答に対してコンテンツを追加しています。

それ以外の応答の場合は、その応答がそのまま返されます。

## <a name="build-and-run-the-program"></a>プログラムをビルドして実行する

以下のコマンドを入力してファイルをコンパイルします。 このコマンド プロンプトから、ビルドの成功に関する情報は一切返されません。

```bash
go build publish-kb.go
```

コマンド ラインで次のコマンドを入力して、プログラムを実行します。 これによって、KB を公開するための要求が QnA Maker API に送信され、成功または失敗のための 204 が出力されます。

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>次の手順

ナレッジ ベースが公開された後、[回答を生成するためのエンドポイント URL](../Tutorials/create-publish-answer.md#generating-an-answer) が必要になります。 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
