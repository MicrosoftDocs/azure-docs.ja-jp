---
title: クイック スタート:Node.js 用 QnA Maker クライアント ライブラリ
titleSuffix: Azure Cognitive Services
description: Node.js 用 QnA Maker クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。  QnA Maker を使用すると、FAQ ドキュメント、URL、製品マニュアルなどの半構造化コンテンツからの質問と回答サービスをいっそう強力にできます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/07/2019
ms.author: diberry
ms.openlocfilehash: dcbbaa2f4c6ebe709c879909f873b212f238ff2a
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375856"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>クイック スタート:Node.js 用 QnA Maker クライアント ライブラリ

Node.js 用 QnA Maker クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。  QnA Maker を使用すると、FAQ ドキュメント、URL、製品マニュアルなどの半構造化コンテンツからの質問と回答サービスをいっそう強力にできます。 

Node.js 用 QnA Maker クライアント ライブラリは、次の目的で使用することができます。

* ナレッジ ベースの作成 
* ナレッジ ベースの管理
* ナレッジ ベースの公開

[リファレンスのドキュメント](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [パッケージ (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js のサンプル](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [Node.js](https://nodejs.org)。

## <a name="setting-up"></a>設定

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル コンピューター上に QnA Maker のリソースを作成します。 

リソースからキーを取得した後、`QNAMAKER_SUBSCRIPTION_KEY` および `QNAMAKER_HOST` という名前のキーの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。 Azure portal 上のリソースの **[キー]** ページと **[概要]** ページに表示されているキーとホストの値を使用します。

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

`npm init -y` コマンドを実行し、`package.json` ファイルを使用してノード アプリケーションを作成します。 

```console
npm init -y
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

`ms-rest-azure` および `azure-cognitiveservices-qnamaker` NPM パッケージをインストールします。

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

アプリの `package.json` ファイルが依存関係によって更新されます。


## <a name="object-model"></a>オブジェクト モデル

QnA Maker クライアントは、自分のキーが含まれている ServiceClientCredentials を使用して Azure に対する認証を行う [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) オブジェクトです。

クライアントが作成されたら、[ナレッジ ベース](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) プロパティを使用して、ナレッジ ベースを作成、管理、および公開します。 

JSON オブジェクトを送信して、ナレッジ ベースを管理します。 即時操作の場合は、通常、状態を示す JSON オブジェクトがメソッドから返されます。 実行時間の長い操作の場合、応答は操作 ID になります。 [client.Operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) メソッドを操作 ID と共に呼び出して、[要求の状態](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest)を確認します。 

 
## <a name="code-examples"></a>コード例

以下のコード スニペットは、Node.js 用 QnA Maker クライアント ライブラリを使用して次のことを実行する方法を示します。

* [ナレッジ ベースの作成](#create-a-knowledge-base)
* [ナレッジ ベースの更新](#update-a-knowledge-base)
* [ナレッジ ベースの公開](#publish-a-knowledge-base)
* [ナレッジ ベースの削除](#delete-a-knowledge-base)
* [操作の状態の取得](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>依存関係を追加する

`index.js` という名前でファイルを作成します。 ファイルに QnA Maker ライブラリと Azure REST ライブラリを追加します。

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

リソースの Azure エンドポイントおよびキー用の変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。


|環境変数|Node.js 変数|例|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|32 文字の GUID|
|`QNAMAKER_HOST`|`endpoint`|`https://westus.api.cognitive.microsoft.com`|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>クライアントを認証する

次に、自分のキーを使用して ServiceClientCredentials オブジェクトを作成し、それを自分のエンドポイントと共に使用して、[QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) オブジェクトを作成します。 クライアント オブジェクトを使用して[ナレッジ ベース](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) オブジェクトを取得します。


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

ナレッジ ベースには、次の 3 つのソースの [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) オブジェクトに対する質問と回答のペアが格納されます。

* **本文**の場合は、[QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) オブジェクトを使用します。
* **ファイル**の場合は、[FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) オブジェクトを使用します。 
* **URL** の場合は、文字列のリストを使用します。

[create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) メソッドを呼び出した後、返された操作 ID を [Operations.getDetails](#get-status-of-an-operation) メソッドに渡して、状態をポーリングします。 

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]


## <a name="update-a-knowledge-base"></a>ナレッジ ベースの更新

ナレッジ ベースを更新するには、ナレッジ ベース ID と、[add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add)、[update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)、[delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO オブジェクトを含む [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) を、[update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) メソッドに渡します。 更新が成功したかどうかを確認するには、[Operation.getDetail](#get-status-of-an-operation) メソッドを使用します。

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

## <a name="publish-a-knowledge-base"></a>ナレッジ ベースの公開

[publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) メソッドを使用して、ナレッジ ベースを公開します。 これにより、ナレッジ ベース ID によって参照される、最新の保存済みおよびトレーニング済みのモデルが取得され、エンドポイントで公開されます。 

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>ナレッジ ベースを削除する

[delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) メソッドをナレッジ ベース ID のパラメーターと共に使用して、ナレッジ ベースを削除します。 

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>操作の状態の取得

create や update などのメソッドの中には、プロセスが終了するのを待つ代わりに、[操作](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest)が返されるのに十分な時間がかかるものがあります。 操作からの[操作 ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) を使用して、(再試行ロジックを使用して) ポーリングし、元のメソッドの状態を判別します。 

次のコード ブロック内の _setTimeout_ 呼び出しは、非同期コードをシミュレートするために使用されています。 これを再試行ロジックに置き換えます。 

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>アプリケーションの実行

自分のアプリケーション ディレクトリで `node index.js` コマンドを使用してアプリケーションを実行します。

```console
node index.js
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
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) にあります。