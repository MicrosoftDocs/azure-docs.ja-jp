---
title: クイック スタート:Node.js 用 REST API シリーズを使用した QnA Maker
titleSuffix: Azure Cognitive Services
description: Node.js 用 QnA Maker REST API シリーズの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。  QnA Maker を使用すると、FAQ ドキュメント、URL、製品マニュアルなどの半構造化コンテンツからの質問と回答サービスをいっそう強力にできます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/13/2019
ms.author: diberry
ms.openlocfilehash: ad7986a0c4b0d59322ccebcaa6b1c70776164c48
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015705"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>クイック スタート:Node.js 用 QnA Maker REST API シリーズ

Node.js 用 QnA Maker REST API シリーズの使用を開始します。 以下の手順に従って、基本タスクのコード例を試してみましょう。  QnA Maker を使用すると、FAQ ドキュメント、URL、製品マニュアルなどの半構造化コンテンツからの質問と回答サービスをいっそう強力にできます。 

Node.js の QnA Maker REST API シリーズを使用して、次の操作を行います。

* ナレッジ ベースの作成
* ナレッジ ベースの置換
* ナレッジ ベースの公開
* ナレッジ ベースを削除する
* ナレッジ ベースのダウンロード
* 操作の状態の取得

[リファレンス ドキュメント](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Node.js サンプル](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [Node.js](https://nodejs.org)。

## <a name="setting-up"></a>設定

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル コンピューター上に QnA Maker のリソースを作成します。 

自分のリソースからキーを取得した後、`QNAMAKER_RESOURCE_KEY` および `QNAMAKER_AUTHORING_ENDPOINT` という名前で、そのリソースの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。 Azure portal 上のリソースの**クイックスタート** ページに表示されているキーとホストの値を使用します。

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

`npm init -y` コマンドを実行して、ノードの `package.json` ファイルを作成します。 

```console
npm init -y
```

`reqeuestretry` および `request` NPM パッケージを追加します。

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>コード例

以下のコード スニペットは、Node.js 用 QnA Maker REST API シリーズを使用して次のことを実行する方法を示します。

* [ナレッジ ベースの作成](#create-a-knowledge-base)
* [ナレッジ ベースの置換](#replace-a-knowledge-base)
* [ナレッジ ベースの公開](#publish-a-knowledge-base)
* [ナレッジ ベースの削除](#delete-a-knowledge-base)
* [ナレッジ ベースのダウンロード](#download-the-knowledge-base)
* [操作の状態の取得](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>依存関係を追加する



`rest-apis.js` という名前のファイルを作成し、HTTP 要求を行うために次の _requires_ ステートメントを追加します。 

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Azure リソース情報の追加

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

ナレッジ ベースには、次の JSON オブジェクトから作成された質問と回答のペアが格納されます。

* **本文**。 
* **ファイル** - アクセス許可を必要としないローカル ファイル。 
* **URL** - 公開されている URL。

[REST API を使用してナレッジ ベースを作成します](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)。 

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>ナレッジ ベースの置換

[REST API を使用してナレッジ ベースを置き換えます](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>ナレッジ ベースの公開

ナレッジ ベースを公開します。 このプロセスによって、HTTP クエリ予測エンドポイントからナレッジ ベースを使用できるようになります。

[REST API を使用してナレッジ ベースを公開します](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)。


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>ナレッジ ベースのダウンロード 

[REST API を使用してナレッジ ベースをダウンロードします](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>ナレッジ ベースを削除する

ナレッジ ベースの使用を終了したら、それを削除します。

[REST API を使用してナレッジ ベースを削除します](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete)。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>操作の状態の取得

作成プロセスなどの長時間実行されるプロセスでは、操作 ID が返されます。操作 ID は、別の REST API 呼び出しで確認する必要があります。 この関数は、作成応答の本文を受け取ります。 重要なキーは `operationState` で、ポーリングを続行する必要があるかどうかを決定します。

[REST API を使用して、ナレッジ ベースの操作を監視します](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)。


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>アプリケーションの実行

自分のアプリケーション ディレクトリで `node rest-apis.js` コマンドを使用して、アプリケーションを実行します。

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
>[チュートリアル:KB を作成して回答する](../tutorials/create-publish-query-in-portal.md)

* [QnA Maker API とは](../Overview/overview.md)
* [ナレッジ ベースを編集する](../how-to/edit-knowledge-base.md)
* [利用状況分析を取得する](../how-to/get-analytics-knowledge-base.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js) にあります。