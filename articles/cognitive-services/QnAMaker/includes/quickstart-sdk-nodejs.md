---
title: クイック スタート:Node.js 用 QnA Maker クライアント ライブラリ
description: このクイックスタートでは、Node.js 用 QnA Maker クライアント ライブラリの使用を開始する方法について説明します。
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: e76acd0f7b776ccad69b0616846988c5ba816a2d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948220"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

Node.js 用 QnA Maker クライアント ライブラリは、次の目的で使用することができます。

* ナレッジ ベースの作成
* ナレッジ ベースの更新
* ナレッジ ベースの公開
* 予測ランタイム エンドポイント キーの取得
* 実行時間の長いタスクの待機
* ナレッジ ベースのダウンロード
* ナレッジ ベースから回答を取得する
* ナレッジ ベースの削除

[リファレンスのドキュメント](/javascript/api/@azure/cognitiveservices-qnamaker/) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js のサンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

Node.js 用 QnA Maker クライアント ライブラリは、次の目的で使用することができます。

* ナレッジ ベースの作成
* ナレッジ ベースの更新
* ナレッジ ベースの公開
* 実行時間の長いタスクの待機
* ナレッジ ベースのダウンロード
* ナレッジ ベースから回答を取得する
* ナレッジ ベースの削除

[リファレンスのドキュメント](/javascript/api/@azure/cognitiveservices-qnamaker/) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js のサンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>前提条件

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [Node.js](https://nodejs.org)。
* Azure サブスクリプションを入手したら、Azure portal で [QnA Maker リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)を作成し、オーサリング キーとリソースを取得します。 デプロイされたら、 **[リソースに移動]** を選択します。
    * アプリケーションを QnA Maker API に接続するには、作成したリソースのキーとリソース名が必要です。 このクイックスタートで後に示すコードに、自分のキーとリソース名を貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [Node.js](https://nodejs.org)。
* Azure サブスクリプションを入手したら、Azure portal で [QnA Maker リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)を作成し、オーサリング キーとエンドポイントを取得します。
    * 注: 必ず **[マネージド]** チェックボックスをオンにしてください。
    * QnA Maker リソースがデプロイされたら、 **[リソースに移動]** を選択します。 アプリケーションを QnA Maker API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

---

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

`npm init -y` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。

```console
npm init -y
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

次の NPM パッケージをインストールします。

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

次の NPM パッケージをインストールします。

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/ms-rest-js
```

---

アプリの `package.json` ファイルが依存関係によって更新されます。

index.js という名前のファイルを作成し、次のライブラリをインポートします。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Dependencies)]

---

リソース名とリソースの Azure キーに対応する変数を作成します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

> [!IMPORTANT]
> Azure portal に移動し、前提条件で作成した QnA Maker リソースのキーとエンドポイントを探します。 それらは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。

- これらの値を格納するために、QNA_MAKER_SUBSCRIPTION_KEY、QNA_MAKER_ENDPOINT、および QNA_MAKER_RUNTIME_ENDPOINT という名前の環境変数を作成します。
- QNA_MAKER_ENDPOINT の値の形式は `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` です。 
- QNA_MAKER_RUNTIME_ENDPOINT の値の形式は `https://YOUR-RESOURCE-NAME.azurewebsites.net` です。
- 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../key-vault/general/overview.md) で安全なキー記憶域を確保できます。

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

> [!IMPORTANT]
> Azure portal に移動し、前提条件で作成した QnA Maker リソースのキーとエンドポイントを探します。 それらは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。

- これらの値を格納するために、QNA_MAKER_SUBSCRIPTION_KEY と QNA_MAKER_ENDPOINT という名前の環境変数を作成します。
- QNA_MAKER_ENDPOINT の値の形式は `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` です。 
- 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../key-vault/general/overview.md) で安全なキー記憶域を確保できます。

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Resourcevariables)]

---

## <a name="object-models"></a>オブジェクト モデル

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[QnA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/) では、2 種類のオブジェクト モデルが使用されます。
* **[QnAMakerClient](#qnamakerclient-object-model)** は、ナレッジ ベースを作成、管理、公開、ダウンロードするためのオブジェクトです。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** は、GenerateAnswer API を使用してナレッジ ベースを照会したり、Train API を使用して ([アクティブ ラーニング](../concepts/active-learning-suggestions.md)の一環として) 提案された新しい質問を送信したりするためのオブジェクトです。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[QnA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/) では、次のオブジェクト モデルが使用されます。
* **[QnAMakerClient](#qnamakerclient-object-model)** は、ナレッジ ベースの作成、管理、公開、ダウンロード、およびクエリを行うためのオブジェクトです。

---

### <a name="qnamakerclient-object-model"></a>QnAMakerClient オブジェクト モデル

作成 QnA Maker クライアントは、キーが含まれている資格情報を使用して Azure に対して認証する [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient) オブジェクトです。

クライアントが作成されたら、[knowledgebase](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient#knowledgebase) を使用して、ナレッジ ベースを作成、管理、公開します。

JSON オブジェクトを送信して、ナレッジ ベースを管理します。 即時操作の場合は、通常、状態を示す JSON オブジェクトがメソッドから返されます。 実行時間の長い操作の場合、応答は操作 ID になります。 [client.operations.getDetails](/javascript/api/@azure/cognitiveservices-qnamaker/operations#getdetails-string--msrest-requestoptionsbase-) メソッドを操作 ID と共に呼び出して、[要求の状態](/javascript/api/@azure/cognitiveservices-qnamaker/operation)を確認します。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient オブジェクト モデル

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

予測の QnA Maker クライアントは、QnAMakerRuntimeClient オブジェクトです。ナレッジ ベースの公開後に作成クライアントの呼び出し ([client.EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys#getkeys-msrest-requestoptionsbase-)) から返される Microsoft.Rest.ServiceClientCredentials (予測ランタイム キーを含む) を使用して Azure に対する認証を行います。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

QnA Maker マネージド リソースでは、QnAMakerRuntimeClient オブジェクトを使用する必要はありません。 代わりに、[QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient) オブジェクトで [generateAnswer](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#generateAnswer_string__QueryDTO__msRest_RequestOptionsBase_) を直接呼び出します。

---

## <a name="code-examples"></a>コード例

以下のコード スニペットは、.NET 用 QnA Maker クライアント ライブラリを使用して次のことを実行する方法を示します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

* [作成クライアントを認証する](#authenticate-the-client-for-authoring-the-knowledge-base)
* [ナレッジ ベースの作成](#create-a-knowledge-base)
* [ナレッジ ベースの更新](#update-a-knowledge-base)
* [ナレッジ ベースのダウンロード](#download-a-knowledge-base)
* [ナレッジ ベースの公開](#publish-a-knowledge-base)
* [ナレッジ ベースの削除](#delete-a-knowledge-base)
* [クエリ ランタイム キーの取得](#get-query-runtime-key)
* [操作の状態の取得](#get-status-of-an-operation)
* [クエリ ランタイム クライアントを認証する](#authenticate-the-runtime-for-generating-an-answer)
* [ナレッジ ベースから回答を生成する](#generate-an-answer-from-the-knowledge-base)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

* [作成クライアントを認証する](#authenticate-the-client-for-authoring-the-knowledge-base)
* [ナレッジ ベースの作成](#create-a-knowledge-base)
* [ナレッジ ベースの更新](#update-a-knowledge-base)
* [ナレッジ ベースのダウンロード](#download-a-knowledge-base)
* [ナレッジ ベースの公開](#publish-a-knowledge-base)
* [ナレッジ ベースの削除](#delete-a-knowledge-base)
* [操作の状態の取得](#get-status-of-an-operation)
* [ナレッジ ベースから回答を生成する](#generate-an-answer-from-the-knowledge-base)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>ナレッジ ベースを作成するためのクライアントを認証する

ご利用のエンドポイントとキーを使用してクライアントをインスタンス化します。 自分のキーを使用して ServiceClientCredentials オブジェクトを作成し、それを自分のエンドポイントと共に使用して、[QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient) オブジェクトを作成します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

ナレッジ ベースには、次の 3 つのソースの [CreateKbDTO](/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto) オブジェクトに対する質問と回答のペアが格納されます。

* **本文** の場合は、[QnADTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadto) オブジェクトを使用します。
    * メタデータとフォローアップ プロンプトを使用するには、編集コンテキストを使用します (このデータは個々の QnA ペア レベルで追加されるため)。
* **ファイル** の場合は、[FileDTO](/javascript/api/@azure/cognitiveservices-qnamaker/filedto) オブジェクトを使用します。 FileDTO には、ファイル名と、ファイルに到達するためのパブリック URL が含まれます。
* **URL** の場合は、公開されている URL を表す文字列のリストを使用します。

作成手順には、ナレッジ ベースのプロパティも含まれます。
* `defaultAnswerUsedForExtraction` - 回答が見つからない場合に返されるもの
* `enableHierarchicalExtraction` - 抽出された QnA ペア間のプロンプト関係を自動的に作成する
* `language` - リソースの最初のナレッジ ベースを作成するときに、Azure Search インデックスで使用する言語を設定する

ナレッジ ベース情報を使用して、[create](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#create-createkbdto--servicecallback-operation--) メソッドを呼び出します。 ナレッジ ベース情報は、基本的に JSON オブジェクトです。

create メソッドから戻ったら、返された操作 ID を [wait_for_operation](#get-status-of-an-operation) メソッドに渡して、状態をポーリングします。 操作が完了すると、wait_for_operation メソッドから戻ってきます。 返された操作の `resourceLocation` ヘッダー値を解析して、新しいナレッジ ベース ID を取得します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=CreateKBMethod)]

---

ナレッジ ベースを適切に作成するには、上記のコードで参照されている [`wait_for_operation`](#get-status-of-an-operation) 関数を必ず含めます。

## <a name="update-a-knowledge-base"></a>ナレッジ ベースの更新

ナレッジ ベースを更新するには、ナレッジ ベース ID と、[add](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#add)、[update](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#update)、[delete](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#deleteproperty) DTO オブジェクトを含む [UpdateKbOperationDTO](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto) を、[update](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#update-string--updatekboperationdto--msrest-requestoptionsbase-) メソッドに渡します。 DTO も、基本的に JSON オブジェクトです。 更新が成功したかどうかを確認するには、[wait_for_operation](#get-status-of-an-operation) メソッドを使用します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=UpdateKBMethod)]

---

ナレッジ ベースを適切に更新するには、上記のコードで参照されている [`wait_for_operation`](#get-status-of-an-operation) 関数を必ず含めます。

## <a name="download-a-knowledge-base"></a>ナレッジ ベースのダウンロード

データベースを [QnADocumentsDTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto) のリストとしてダウンロードするには、[download](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#download-string--models-environmenttype--msrest-requestoptionsbase-) メソッドを使用します。 このメソッドの結果は TSV ファイルではないため、これは、QnA Maker ポータルの **[設定]** ページからのエクスポートと同等 "_ではありません_"。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>ナレッジ ベースの公開

[publish](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#publish-string--msrest-requestoptionsbase-) メソッドを使用して、ナレッジ ベースを公開します。 これにより、ナレッジ ベース ID によって参照される、最新の保存済みおよびトレーニング済みのモデルが取得され、エンドポイントで公開されます。 HTTP 応答コードを調べて、発行が成功したことを検証します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>ナレッジ ベースにクエリを実行する

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

### <a name="get-query-runtime-key"></a>クエリ ランタイム キーの取得

ナレッジ ベースの公開後、ランタイムに対してクエリを実行するには、クエリ ランタイム キーが必要です。 これは、元のクライアント オブジェクトを作成するときに使用したキーとは異なります。

[EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys) メソッドを使用して、[EndpointKeysDTO](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto) クラスを取得します。

そのオブジェクトに返されたいずれかのキー プロパティを使用して、ナレッジ ベースにクエリを実行します。

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>回答を生成するためのランタイムを認証する

ナレッジ ベースにクエリを実行して回答を生成したり、アクティブ ラーニングからトレーニングしたりするための QnAMakerRuntimeClient を作成します。

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

QnAMakerRuntimeClient を使用してナレッジから回答を取得するか、または、[アクティブ ラーニング](../concepts/active-learning-suggestions.md)を目的として提案された新しい質問をナレッジ ベースに送信します。

### <a name="generate-an-answer-from-the-knowledge-base"></a>ナレッジ ベースから回答を生成する

RuntimeClient.runtime.generateAnswer メソッドを使用して公開済みのナレッジ ベースから回答を生成します。 このメソッドは、ナレッジ ベース ID と [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto) を受け取ります。 さらに、Top や Context など、QueryDTO のプロパティにアクセスしてチャット ボットで使用することができます。

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>ナレッジ ベースから回答を生成する

QnAMakerClient.knowledgebase.generateAnswer メソッドを使用して、公開済みのナレッジ ベースから回答を生成します。 このメソッドは、ナレッジ ベース ID と [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto) を受け取ります。 さらに、Top や Context など、QueryDTO のプロパティにアクセスしてチャット ボットで使用することができます。

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=GenerateAnswer)]

---

これは、ナレッジ ベースに対してクエリを実行する単純な例です。 高度なクエリ シナリオについては、[他のクエリ サンプル](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)をご覧ください。

## <a name="delete-a-knowledge-base"></a>ナレッジ ベースを削除する

[delete](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#deletemethod-string--msrest-requestoptionsbase-) メソッドをナレッジ ベース ID のパラメーターと共に使用して、ナレッジ ベースを削除します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>操作の状態の取得

create や update などのメソッドの中には、プロセスが終了するのを待つ代わりに、[操作](/javascript/api/@azure/cognitiveservices-qnamaker/operations)が返されるのに十分な時間がかかるものがあります。 操作からの[操作 ID](/javascript/api/@azure/cognitiveservices-qnamaker/operation#operationid) を使用して、(再試行ロジックを使用して) ポーリングし、元のメソッドの状態を判別します。

次のコード ブロック内の _delayTimer_ 呼び出しは、再試行ロジックをシミュレートするために使用されています。 これを独自の再試行ロジックに置き換えます。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=MonitorOperation)]

---

## <a name="run-the-application"></a>アプリケーションの実行

自分のアプリケーション ディレクトリで `node index.js` コマンドを使用してアプリケーションを実行します。

```console
node index.js
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) にあります。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js) にあります。

---
