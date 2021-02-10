---
title: クイック スタート:.NET 用 QnA Maker クライアント ライブラリ
description: このクイックスタートでは、.NET 用 QnA Maker クライアント ライブラリの使用を開始する方法について説明します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。  QnA Maker を使用すると、FAQ ドキュメント、URL、製品マニュアルなどの半構造化コンテンツからの質問と回答サービスをいっそう強力にできます。
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: e75fdbe49910f9d6fe4fe25a7996fccf415fdf35
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947852"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

.NET 用 QnA Maker クライアント ライブラリは、次の目的で使用することができます。

 * ナレッジベースを作成する
 * ナレッジ ベースの更新
 * ナレッジ ベースの公開
 * 予測ランタイム エンドポイント キーの取得
 * 実行時間の長いタスクの待機
 * ナレッジ ベースのダウンロード
 * ナレッジ ベースから回答を取得する
 * ナレッジベースを削除する

[リファレンス ドキュメント](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/2.0.1) | [C# サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

.NET 用 QnA Maker クライアント ライブラリは、次の目的で使用することができます。

 * ナレッジベースを作成する
 * ナレッジ ベースの更新
 * ナレッジ ベースの公開
 * 実行時間の長いタスクの待機
 * ナレッジ ベースのダウンロード
 * ナレッジ ベースから回答を取得する
 * ナレッジベースを削除する

[リファレンス ドキュメント](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/3.0.0-preview.1) | [C# サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>前提条件

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) または現在のバージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* Azure サブスクリプションを入手したら、Azure portal で [QnA Maker リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)を作成し、オーサリング キーとリソース名を取得します。 デプロイされたら、 **[リソースに移動]** を選択します。
    * アプリケーションを QnA Maker API に接続するには、作成したリソースのキーとリソース名が必要です。 このクイックスタートで後に示すコードに、自分のキーとリソース名を貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) または現在のバージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* Azure サブスクリプションを入手したら、Azure portal で [QnA Maker リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)を作成し、オーサリング キーとエンドポイントを取得します。
    * 注: 必ず **[マネージド]** チェックボックスをオンにしてください。
    * QnA Maker リソースがデプロイされたら、 **[リソースに移動]** を選択します。 アプリケーションを QnA Maker API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

---

## <a name="setting-up"></a>設定

### <a name="visual-studio-ide"></a>Visual Studio IDE

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

Visual Studio を使用して .NET Core アプリケーションを作成します。次に、**ソリューション エクスプローラー** でソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールしてください。 パッケージ マネージャーが開いたら、 **[参照]** を選択して `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker` を検索します。 バージョン `2.0.1` を選択し、 **[インストール]** を選択します。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

Visual Studio を使用して .NET Core アプリケーションを作成します。次に、**ソリューション エクスプローラー** でソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールしてください。 パッケージ マネージャーが開いたら、 **[参照]** を選択し、 **[プレリリースを含める]** をオンにして、`Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker` を検索します。 バージョン `3.0.0-preview.1` を選択し、 **[インストール]** を選択します。

---

### <a name="cli"></a>CLI

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`qna-maker-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (*program.cs*) を使用する単純な "Hello World" C# プロジェクトが作成されます。

```console
dotnet new console -n qna-maker-quickstart
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

```console
dotnet build
```

ビルドの出力に警告やエラーが含まれないようにする必要があります。

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 QnA Maker クライアント ライブラリをインストールします。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.1
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 3.0.0-preview.1
```

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs) にあり、このクイックスタートのコード例が含まれています。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs) にあり、このクイックスタートのコード例が含まれています。

---

### <a name="using-directives"></a>ディレクティブの使用

プロジェクト ディレクトリから *program.cs* ファイルを開いて、次の `using` ディレクティブを追加します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Dependencies)]

---

### <a name="subscription-key-and-resource-endpoints"></a>サブスクリプション キーとリソース エンドポイント

このクイックスタートの共通のタスクを使用するために、次のセクションに示す変数とコードをアプリケーションの `Main` メソッドに追加します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

> [!IMPORTANT]
> Azure portal に移動し、前提条件で作成した QnA Maker リソースのキーとエンドポイントを探します。 それらは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。

- これらの値を格納するために、QNA_MAKER_SUBSCRIPTION_KEY、QNA_MAKER_ENDPOINT、および QNA_MAKER_RUNTIME_ENDPOINT という名前の環境変数を作成します。
- QNA_MAKER_ENDPOINT の値の形式は `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` です。 
- QNA_MAKER_RUNTIME_ENDPOINT の値の形式は `https://YOUR-RESOURCE-NAME.azurewebsites.net` です。
- 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../key-vault/general/overview.md) で安全なキー記憶域を確保できます。

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

> [!IMPORTANT]
> Azure portal に移動し、前提条件で作成した QnA Maker リソースのキーとエンドポイントを探します。 それらは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。

- これらの値を格納するために、QNA_MAKER_SUBSCRIPTION_KEY と QNA_MAKER_ENDPOINT という名前の環境変数を作成します。
- QNA_MAKER_ENDPOINT の値の形式は `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` です。 
- 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../key-vault/general/overview.md) で安全なキー記憶域を確保できます。

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Resourcevariables)]

---

## <a name="object-models"></a>オブジェクト モデル

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[QnA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) では、2 種類のオブジェクト モデルが使用されます。
* **[QnAMakerClient](#qnamakerclient-object-model)** は、ナレッジ ベースを作成、管理、公開、ダウンロードするためのオブジェクトです。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** は、GenerateAnswer API を使用してナレッジ ベースを照会したり、Train API を使用して ([アクティブ ラーニング](../concepts/active-learning-suggestions.md)の一環として) 提案された新しい質問を送信したりするためのオブジェクトです。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[QnA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) では、次のオブジェクト モデルが使用されます。
* **[QnAMakerClient](#qnamakerclient-object-model)** は、ナレッジ ベースの作成、管理、公開、ダウンロード、およびクエリを行うためのオブジェクトです。

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient オブジェクト モデル

作成の QnA Maker クライアントは、自分のキーが含まれている Microsoft.Rest.ServiceClientCredentials を使用して Azure に対する認証を行う [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) オブジェクトです。

クライアントが作成されたら、[ナレッジ ベース](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) プロパティを使用して、ナレッジ ベースを作成、管理、および公開します。

JSON オブジェクトを送信して、ナレッジ ベースを管理します。 即時操作の場合は、通常、状態を示す JSON オブジェクトがメソッドから返されます。 実行時間の長い操作の場合、応答は操作 ID になります。 [client.Operations.GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync) メソッドを操作 ID と共に呼び出して、[要求の状態](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype)を確認します。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient オブジェクト モデル

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

予測の QnA Maker クライアントは、[QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) オブジェクトです。ナレッジベースの発行後に作成クライアントの呼び出し (`client.EndpointKeys.GetKeys`) から返される Microsoft.Rest.ServiceClientCredentials (予測ランタイム キーを含む) を使用して Azure に対する認証を行います。

クエリ ランタイムから回答を取得するには、[GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) メソッドを使用します。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

QnA Maker マネージド リソースでは、**QnAMakerRuntimeClient** オブジェクトを使用する必要はありません。 代わりに、[QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) メソッドを呼び出します。

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

キーを使用してクライアント オブジェクトをインスタンス化し、リソースと共にそれを使用してエンドポイントを構築し、エンドポイントとキーを使用して [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) を作成します。 [ServiceClientCredentials](/dotnet/api/microsoft.rest.serviceclientcredentials) オブジェクトを作成します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

ナレッジ ベースには、次の 3 つのソースの [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) オブジェクトに対する質問と回答のペアが格納されます。

* **本文** の場合は、[QnADTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto) オブジェクトを使用します。
    * メタデータとフォローアップ プロンプトを使用するには、編集コンテキストを使用します (このデータは個々の QnA ペア レベルで追加されるため)。
* **ファイル** の場合は、[FileDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto) オブジェクトを使用します。 FileDTO には、ファイル名と、ファイルに到達するためのパブリック URL が含まれます。
* **URL** の場合は、公開されている URL を表す文字列のリストを使用します。

作成手順には、ナレッジ ベースのプロパティも含まれます。
* `defaultAnswerUsedForExtraction` - 回答が見つからない場合に返されるもの
* `enableHierarchicalExtraction` - 抽出された QnA ペア間のプロンプト関係を自動的に作成する
* `language` - リソースの最初のナレッジ ベースを作成するときに、Azure Search インデックスで使用する言語を設定する

[CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync) メソッドを呼び出した後、返された操作 ID を [MonitorOperation](#get-status-of-an-operation) メソッドに渡して、状態をポーリングします。

次のコードの最後の行では、MonitorOperation の応答からナレッジ ベース ID を返しています。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=CreateKBMethod)]

---

ナレッジ ベースを適切に作成するには、上記のコードで参照されている [`MonitorOperation`](#get-status-of-an-operation) 関数を必ず含めます。

## <a name="update-a-knowledge-base"></a>ナレッジ ベースの更新

ナレッジ ベースを更新するには、ナレッジ ベース ID と、[add](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd)、[update](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate)、および [delete](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) DTO オブジェクトを含む [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) を、[UpdateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync) メソッドに渡します。 更新が成功したかどうかを確認するには、[MonitorOperation](#get-status-of-an-operation) メソッドを使用します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=UpdateKBMethod)]

---

ナレッジ ベースを適切に更新するには、上記のコードで参照されている [`MonitorOperation`](#get-status-of-an-operation) 関数を必ず含めます。

## <a name="download-a-knowledge-base"></a>ナレッジ ベースのダウンロード

データベースを [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto) のリストとしてダウンロードするには、[DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync) メソッドを使用します。 このメソッドの結果はファイルではないため、これは、QnA Maker ポータルの **[設定]** ページからのエクスポートと同等 "_ではありません_"。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>ナレッジ ベースの公開

[PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync) メソッドを使用して、ナレッジ ベースを公開します。 これにより、ナレッジ ベース ID によって参照される、最新の保存済みおよびトレーニング済みのモデルが取得され、エンドポイントで公開されます。 これは、ナレッジ ベースに対してクエリを実行するために必要な手順です。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=PublishKB)]

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

## <a name="get-query-runtime-key"></a>クエリ ランタイム キーの取得

ナレッジ ベースの公開後、ランタイムに対してクエリを実行するには、クエリ ランタイム キーが必要です。 これは、元のクライアント オブジェクトを作成するときに使用したキーとは異なります。

[EndpointKeys](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) メソッドを使用して、[EndpointKeysDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto) クラスを取得します。

そのオブジェクトに返されたいずれかのキー プロパティを使用して、ナレッジ ベースにクエリを実行します。

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey)]

ナレッジ ベースに対してクエリを実行するには、ランタイム キーが必要です。

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>回答を生成するためのランタイムを認証する

ナレッジ ベースにクエリを実行して回答を生成したり、アクティブ ラーニングからトレーニングしたりするための [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) を作成します。

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

QnAMakerRuntimeClient を使用して、次のことを行います。
* ナレッジ ベースから回答を取得する
* [アクティブ ラーニング](../concepts/active-learning-suggestions.md)のために、新しく提案された質問をナレッジ ベースに送信する

## <a name="generate-an-answer-from-the-knowledge-base"></a>ナレッジ ベースから回答を生成する

[RuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync) メソッドを使用して公開済みのナレッジ ベースから回答を生成します。 このメソッドは、ナレッジ ベース ID と [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto) を受け取ります。 さらに、[Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) や [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context) など、QueryDTO のプロパティにアクセスしてチャット ボットで使用することができます。

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

## <a name="generate-an-answer-from-the-knowledge-base"></a>ナレッジ ベースから回答を生成する

[QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) メソッドを使用して公開済みのナレッジ ベースから回答を生成します。 このメソッドは、ナレッジ ベース ID と [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto) を受け取ります。 さらに、[Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top)、[Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context)、[AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest) など、QueryDTO のプロパティにアクセスしてチャット ボットで使用できます。

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer)]

---

これは、ナレッジ ベースに対してクエリを実行する単純な例です。 高度なクエリ シナリオについては、[他のクエリ サンプル](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)をご覧ください。

## <a name="delete-a-knowledge-base"></a>ナレッジ ベースを削除する

[DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync) メソッドをナレッジ ベース ID のパラメーターと共に使用して、ナレッジ ベースを削除します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>操作の状態の取得

create や update などのメソッドの中には、プロセスが終了するのを待つ代わりに、[操作](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation)が返されるのに十分な時間がかかるものがあります。 操作からの[操作 ID](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) を使用して、(再試行ロジックを使用して) ポーリングし、元のメソッドの状態を判別します。

次のコード ブロックの _loop_ および _Task.Delay_ は、再試行ロジックをシミュレートするために使用されています。 これらは自分の再試行ロジックに置き換える必要があります。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=MonitorOperation)]

---

## <a name="run-the-application"></a>アプリケーションの実行

自分のアプリケーション ディレクトリで `dotnet run` コマンドを使用してアプリケーションを実行します。

```dotnetcli
dotnet run
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart) にあります。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart) にあります。

---
