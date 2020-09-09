---
title: クイック スタート:ナレッジ ベースを公開する、REST、Python - QnA Maker
description: この Python REST ベースのクイックスタートでは、自分のナレッジ ベースを公開し、アプリケーションまたはチャット ボット内で呼び出すことができるエンドポイントを作成します。
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: 6ba263633c6ee638ea742c47e36be4d184cdea75
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266444"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>クイック スタート:Python を使用して QnA Maker のナレッジ ベースを公開する

この REST ベースのクイック スタートでは、ナレッジ ベース (KB) をプログラムから公開する手順を紹介しています。 公開すると、ナレッジ ベースの最新バージョンが、専用の Azure Cognitive Search インデックスにプッシュされ、アプリケーションやチャット ボット内で呼び出すことができるエンドポイントが作成されます。

このクイックスタートで呼び出す QnA Maker REST API は次のとおりです。
* [公開](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - この API は、要求の本文に情報を必要としません。

## <a name="prerequisites"></a>前提条件

* [Python 3.7](https://www.python.org/downloads/)
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーと (リソース名を含む) エンドポイントを取得するには、Azure portal で対象のリソースの **[クイックスタート]** を選択します。
* URL の `kbid` クエリ文字列パラメーターに含まれている QnA Maker ナレッジ ベース (KB) ID (下図)。

    ![QnA Maker ナレッジ ベース ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    まだナレッジ ベースがない場合は、このクイック スタート用のサンプルを作成できます。[新しいナレッジ ベースを作成します](../how-to/create-knowledge-base.md)。

> [!NOTE]
> 完全なソリューション ファイルは、[**Azure-Samples/cognitive-services-qnamaker-python** GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base)から入手できます。

## <a name="create-a-knowledge-base-python-file"></a>ナレッジ ベースの Python ファイルを作成する

`publish-kb-3x.py` という名前でファイルを作成します。

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

`publish-kb-3x.py` の先頭に次の行を追加して、プロジェクトに必要な依存関係を追加します。

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/publish-kb.py" id="dependencies":::

## <a name="add-required-constants"></a>必要な定数を追加する

上記の必要な依存関係の後に、QnA Maker にアクセスするために必要な定数を追加します。 サンプルの値は実際の値に変更してください。

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/publish-kb.py" id="constants":::

## <a name="add-post-request-to-publish-knowledge-base"></a>ナレッジ ベースを公開するための POST 要求を追加する

必要な定数の後に、次のコードを追加します。このコードは、ナレッジ ベースを公開するための HTTPS 要求を QnA Maker API に送信し、その応答を受信します。

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/publish-kb.py" id="main":::

正常に公開されると、この API 呼び出しから状態 204 が返されます。応答の本文にはコンテンツが含まれません。 このコードでは、204 応答に対してコンテンツを追加しています。

それ以外の応答の場合は、その応答がそのまま返されます。

## <a name="build-and-run-the-program"></a>プログラムをビルドして実行する

コマンド ラインで次のコマンドを入力して、プログラムを実行します。 これによって、ナレッジ ベースを公開するための要求が QnA Maker API に送信され、成功または失敗のための 204 が出力されます。

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>次のステップ

ナレッジ ベースが公開された後、[回答を生成するためのエンドポイント URL](./get-answer-from-knowledge-base-python.md) が必要になります。

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2092179)

[QnA Maker の概要](../Overview/overview.md)
