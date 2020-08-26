---
title: クイック スタート:.NET 用 Form Recognizer クライアント ライブラリ
description: このクイックスタートでは、.NET 用の Form Recognizer クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: 480c1e991225f707b6497849b6e8d8b5c4124f21
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505405"
---
[リファレンスのドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)。
* トレーニング データのセットを含む Azure Storage Blob。 トレーニング データ セットをまとめるためのヒントとオプションについては、「[カスタム モデルのトレーニング データ セットを作成する](../../build-training-data-set.md)」を参照してください。 このクイックスタートでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)の **Train** フォルダーにあるファイルを使用できます。
* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="setting-up"></a>設定

### <a name="create-a-form-recognizer-azure-resource"></a>Form Recognizer Azure リソースを作成する

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>環境変数を作成する

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`formrecognizer-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。_Program.cs_。 

```console
dotnet new console -n formrecognizer-quickstart
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドします。

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

プロジェクト ディレクトリから、好みのエディターまたは IDE で *Program.cs* ファイルを開きます。 次の `using` ディレクティブを追加します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

次に、アプリケーションの **Main** メソッドに次のコードを追加します。 この非同期タスクは後で定義します。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Form Recognizer クライアント ライブラリをインストールします。

```console
dotnet add package Azure.AI.FormRecognizer --version 1.0.0-preview.3
```

Visual Studio IDE を使用している場合、クライアント ライブラリは、ダウンロード可能な NuGet パッケージとして入手できます。


<!-- Objet model TBD -->



## <a name="code-examples"></a>コード例

これらのコード スニペットでは、.NET 用 Form Recognizer クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [フォーム コンテンツを認識する](#recognize-form-content)
* [領収書を認識する](#recognize-receipts)
* [カスタム モデルをトレーニングする](#train-a-custom-model)
* [カスタム モデルを使用してフォームを分析する](#analyze-forms-with-a-custom-model)
* [カスタム モデルを管理する](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>クライアントを認証する

`Main` メソッドの下に、`Main` で参照されているタスクを定義します。 ここでは、上で定義したサブスクリプション変数を使用して 2 つのクライアント オブジェクトを認証します。 必要に応じて、新しいクライアント オブジェクトを作成せずに API キーを更新できるように、**AzureKeyCredential** オブジェクトを使用します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]


### <a name="call-client-specific-methods"></a>クライアント固有のメソッドを呼び出す

次のコード ブロックは、クライアント オブジェクトを使用して、Form Recognizer SDK の主要タスクごとにメソッドを呼び出します。 これらのメソッドは後で定義します。

また、トレーニングとテスト データの URL への参照を追加する必要もあります。 
* カスタム モデルのトレーニング データの SAS URL を取得するには、Microsoft Azure Storage Explorer を開き、ご利用のコンテナーを右クリックし、 **[Shared Access Signature の取得]** を選択します。 アクセス許可の **[読み取り]** と **[表示]** がオンになっていることを確認し、 **[作成]** をクリックします。 次に、その値を **URL** セクションにコピーします。 それは次の書式になります`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
* テストするフォームの URL を取得するには、上記の手順を使用して、BLOB ストレージ内の個々のドキュメントの SAS URL を取得できます。 または、別の場所にあるドキュメントの URL を取得します。
* 上記のメソッドを使用して、領収書の画像の URL を取得するか、指定されたサンプル画像の URL を使用します。

> [!NOTE]
> このガイドのコード スニペットでは、URL でアクセスされるリモート フォームが使用されます。 ローカル フォーム ドキュメントを代わりに処理する場合は、[リファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre)の関連するメソッドを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_calls)]

## <a name="recognize-form-content"></a>フォーム コンテンツを認識する

Form Recognizer を使用すると、ドキュメント内の表、行、および単語を認識できます。モデルをトレーニングする必要はありません。

指定された URI にあるファイルの内容を認識するには、**StartRecognizeContentFromUri** メソッドを使用します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]


返される値は **FormPage** オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つあります。 次のコードでは、これらのオブジェクトを反復処理し、抽出されたキー/値のペアとテーブル データを出力します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]


## <a name="recognize-receipts"></a>領収書を認識する

このセクションでは、事前トレーニング済みの領収書モデルを使用して、米国の領収書から共通フィールドを認識して抽出する方法を示します。

URI からの領収書を認識するには、**StartRecognizeReceiptsFromUri** メソッドを使用します。 返される値は **RecognizedReceipt** オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つあります。 次のコードは、指定された URI で領収書を処理し、主要なフィールドと値をコンソールに出力します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]


次のコード ブロックは、領収書で検出された個々の項目を反復処理し、その詳細をコンソールに出力します。
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_item_print)]

最終的に、最後のコード ブロックが、領収書の合計値を出力します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_total_print)]

## <a name="train-a-custom-model"></a>カスタム モデルをトレーニングする

このセクションでは、独自のデータを使用してモデルをトレーニングする方法を示します。 トレーニング済みのモデルは、元のフォーム ドキュメント内のキー/値の関係を含む構造化データを出力できます。 モデルをトレーニングした後、モデルをテストおよび再トレーニングでき、最終的にはモデルを使用して、ニーズに従ってより多くのフォームから正確にデータを抽出できます。

> [!NOTE]
> また、[Form Recognizer のサンプル ラベル付けツール](../../quickstarts/label-tool.md)などのグラフィカル ユーザー インターフェイスを使用してモデルをトレーニングすることもできます。

### <a name="train-a-model-without-labels"></a>ラベルなしでモデルをトレーニングする

カスタム モデルをトレーニングして、トレーニング ドキュメントに手動でラベルを付けることなく、カスタム フォームにあるすべてのフィールドと値を認識できるようにします。

次のメソッドは、指定された一連のドキュメントでモデルをトレーニングし、モデルの状態をコンソールに出力します。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]

返される **CustomFormModel** オブジェクトには、モデルが認識できるフォームの種類と、それぞれのフォームの種類から抽出できるフィールドに関する情報が含まれています。 次のコード ブロックは、この情報をコンソールに出力します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

最後に、このメソッドはモデルの一意の ID を返します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="train-a-model-with-labels"></a>ラベルを使用してモデルをトレーニングする

トレーニング ドキュメントに手動でラベルを付けて、カスタム モデルをトレーニングすることもできます。 ラベルを使用してトレーニングを行うと、一部のシナリオでパフォーマンスの向上につながります。 ラベルを使用してトレーニングするには、トレーニング ドキュメントと共に、BLOB ストレージ コンテナーに特別なラベル情報ファイル ( *\<filename\>.pdf.labels.json*) を用意する必要があります。 [Form Recognizer のサンプル ラベル付けツール](../../quickstarts/label-tool.md) には、これらのラベル ファイルの作成を助ける UI が提供されています。 それらの用意ができたら、*uselabels* パラメーターを `true` に設定して **StartTrainingAsync** メソッドを呼び出すことができます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

返される **CustomFormModel** は、モデルが抽出できるフィールドを、各フィールドの予測精度と共に示します。 次のコード ブロックは、この情報をコンソールに出力します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]

## <a name="analyze-forms-with-a-custom-model"></a>カスタム モデルを使用してフォームを分析する

このセクションでは、独自のフォームでトレーニングしたモデルを使用して、カスタムのフォームの種類からキー/値の情報やその他のコンテンツを抽出する方法について説明します。

> [!IMPORTANT]
> このシナリオを実装するには、モデルのトレーニングが完了している必要があります。それにより、次のメソッドにその ID を渡すことができます。 「[モデルをトレーニングする](#train-a-model-without-labels)」セクションを参照してください。

**StartRecognizeCustomFormsFromUri** メソッドを使用します。 返される値は **RecognizedForm** オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つあります。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

次のコードは、分析結果をコンソールに出力します。 認識された各フィールドと対応する値が、信頼度スコアと共に出力されます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]

## <a name="manage-your-custom-models"></a>カスタム モデルを管理する

このセクションでは、アカウントに格納されているカスタム モデルを管理する方法について説明します。 次のコードは、例として、1 つのメソッドですべてのモデル管理タスクを実行します。 まず、次のメソッド シグネチャをコピーします。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>FormRecognizer リソース アカウント内のモデルの数を確認する

次のコード ブロックは、Form Recognizer アカウントに保存したモデルの数を確認し、アカウントの制限と比較します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>リソース アカウントに現在格納されているモデルを一覧表示する

次のコード ブロックは、アカウント内の現在のモデルを一覧表示し、その詳細をコンソールに出力します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]

### <a name="get-a-specific-model-using-the-models-id"></a>モデルの ID を使用して特定のモデルを取得する

次のコード ブロックは、新しいモデルをトレーニングし (「[モデルをトレーニングする](#train-a-model-without-labels)」セクションと同様)、その後、その ID を使用して、それへの 2 番目の参照を取得します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="delete-a-model-from-the-resource-account"></a>リソース アカウントからモデルを削除する

ID を参照することで、アカウントからモデルを削除することもできます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>トラブルシューティング

.NET SDK を使用して Cognitive Services の Form Recognizer クライアント ライブラリを操作すると、サービスから返されたエラーによって `RequestFailedException` が発生します。 これには、REST API 要求によって返されるのと同じ HTTP 状態コードが含まれます。

たとえば、無効な URI を含む領収書の画像を送信すると、"Bad Request" (無効な要求) を示す `400` エラーが返されます。

```csharp
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

操作のクライアント要求 ID などの追加情報がログに記録されます。

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Form Recognizer .NET クライアント ライブラリを使用してモデルをトレーニングし、さまざまな方法でフォームを分析しました。 次に、より適切なトレーニング データ セットを作成し、より正確なモデルを生成するためのヒントについて学習します。

> [!div class="nextstepaction"]
> [トレーニング データ セットの作成](../../build-training-data-set.md)

* [Form Recognizer とは](../../overview.md)
* このガイドのサンプル コード (およびその他の情報) については、[GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) を参照してください。
