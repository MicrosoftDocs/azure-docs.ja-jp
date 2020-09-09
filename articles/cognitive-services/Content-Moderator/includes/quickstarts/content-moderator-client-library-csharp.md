---
title: Content Moderator .NET クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、.NET 用 Content Moderator クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-dotnet
ms.openlocfilehash: 65465c97f4457756fb376219caeb9cb066a2ec77
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929120"
---
.NET 用 Content Moderator クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Content Moderator は、テキスト、画像、ビデオのコンテンツに不快感を与える可能性がある内容、リスクのある内容、その他望ましくない可能性のある内容が含まれているかどうかを確認する Cognitive Services です。 このサービスでは、そのような内容が検出されると、それに応じたラベル (フラグ) がコンテンツに適用されます。 その後は、法的規制に準拠したり、ユーザーに意図されている環境を維持したりするために、フラグの設定されたコンテンツをアプリで処理することができます。

.NET 用 Content Moderator クライアント ライブラリは、次の目的で使用できます。

* [テキストのモデレート](#moderate-text)
* [画像のモデレート](#moderate-images)
* [レビューを作成する](#create-a-review)

[リファレンスのドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [サンプル](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="setting-up"></a>設定

### <a name="create-a-content-moderator-azure-resource"></a>Content Moderator の Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル コンピューター上に Content Moderator 用のリソースを作成します。 次のこともできます。

* お使いのリソースを [Azure portal](https://portal.azure.com/) で表示する

リソースからキーを取得した後、キーとエンドポイント URL の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)し、それぞれに `CONTENT_MODERATOR_SUBSCRIPTION_KEY` と `CONTENT_MODERATOR_ENDPOINT` という名前を付けます。

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

好みのテキスト エディターまたは IDE で、新しい .NET Core アプリケーションを作成します。 

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`content-moderator-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。*Program.cs*。

```console
dotnet new console -n content-moderator-quickstart
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

プロジェクト ディレクトリから、好みのエディターまたは IDE で *Program.cs* ファイルを開きます。 次の `using` ステートメントを追加します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

**Program** クラスで、自分のリソースのエンドポイントの場所用の変数と、環境変数としてのキーを作成します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Content Moderator クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Visual Studio IDE を使用している場合、クライアント ライブラリは、ダウンロード可能な NuGet パッケージとして入手できます。

## <a name="object-model"></a>オブジェクト モデル

以下のクラスでは、Content Moderator .NET クライアント ライブラリの主要な機能の一部を処理します。

|名前|説明|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|このクラスは、すべての Content Moderator の機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|このクラスでは、成人向けコンテンツ、個人情報、または人間の顔の画像を分析するための機能が提供されます。|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|このクラスでは、言語、冒涜的表現、エラー、個人情報のテキストを分析するための機能が提供されます。|
|[Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|このクラスでは、ジョブ、カスタム ワークフロー、人によるレビューを作成するためのメソッド含む、レビュー API の機能が提供されます。|

## <a name="code-examples"></a>コード例


これらのコード スニペットでは、.NET 用 Content Moderator クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [テキストのモデレート](#moderate-text)
* [画像のモデレート](#moderate-images)
* [レビューを作成する](#create-a-review)

## <a name="authenticate-the-client"></a>クライアントを認証する

新しいメソッドで、実際のエンドポイントとキーを使用してクライアント オブジェクトをインスタンス化します。 シナリオごとに異なるクライアントを作成する必要ありませんが、コードの整理には役立ちます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>テキストのモデレート

次のコードでは、Content Moderator クライアントを使用してテキストの本文が分析され、結果がコンソールに出力されます。 **Program** クラスのルートで、入力ファイルと出力ファイルを定義します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

次に、プロジェクトのルートに *TextFile.txt* ファイルを追加します。 このファイルに独自のテキストを追加するか、次のサンプル テキストを使用します。

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

`Main` メソッドに次のメソッド呼び出しを追加します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

次に、**Program** クラスのどこかにテキスト モデレーション メソッドを定義します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>画像のモデレート

次のコードでは、Content Moderator クライアントが [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) オブジェクトとともに使用され、成人向けコンテンツやきわどい内容のリモート画像が分析されます。

> [!NOTE]
> ローカルの画像コンテンツを分析することもできます。 ローカル画像を操作するメソッドと操作については、[リファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_)を参照してください。

### <a name="get-sample-images"></a>サンプル イメージの取得

入力ファイルと出力ファイルの定義:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

次に、プロジェクトのルートに入力ファイル *ImageFiles.txt* を作成します。 このファイルでは、画像の URL を追加して&mdash;行ごとに 1 つの URL を分析します。 次のサンプル イメージを使用できます。

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

入力ファイルと出力ファイルを `Main` メソッドの次のメソッド呼び出しに渡します。 このメソッドは、後の手順で定義します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

### <a name="define-helper-class"></a>ヘルパー クラスを定義する

**Program** クラスに次のクラス定義を追加します。 この内部クラスは、画像のモデレーションの結果を処理します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>画像のモデレーション メソッドを定義する

次のメソッドは、テキスト ファイル内の画像 URL を反復処理して **EvaluationData** インスタンスを作成し、成人向けまたはわいせつなコンテンツ、テキスト、および人間の顔の画像を分析します。 次に、最終的な **EvaluationData** インスタンスをリストに追加し、返されたデータの完全な一覧をコンソールに書き込みます。

#### <a name="iterate-through-image-urls"></a>画像 URL を反復処理する

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>コンテンツの分析

Content Moderator 画面に表示される画像の属性の詳細については、[画像のモデレーションの概念](../../image-moderation-api.md)ガイドを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>モデレーション結果をファイルに書き込む

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>レビューを作成する

Content Moderator .NET クライアント ライブラリを使用して、人間のモデレーターがレビューできるように、[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)にコンテンツをフィードすることができます。 レビュー ツールの詳細については、[レビュー ツールの概念的なガイド](../../review-tool-user-guide/human-in-the-loop.md)に関するページを参照してください。

このセクションのメソッドでは、[Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) クラスを使用してレビューが作成され、その ID が取得されて、レビュー ツールの Web ポータルから人間による入力を受け取った後、その詳細が確認されます。 これらの情報は、すべて出力テキスト ファイルに記録されます。 このメソッドを `Main` メソッドから呼び出します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>サンプル イメージの取得

**Program** クラスのルートで、次の配列を宣言します。 この変数は、レビューの作成に使用するサンプル イメージを参照します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>レビューの資格情報を取得する

まず、[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)にサインインし、チーム名を取得します。 次に、それを **Program** クラス内の適切な変数に割り当てます。 必要に応じて、コールバック エンドポイントを設定して、レビューのアクティビティの更新を受け取ることができます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>ヘルパー クラスを定義する

**Program** クラスに次のクラス定義を追加します。 このクラスは、レビュー ツールに送信される単一のレビュー インスタンスを表すために使用されます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>ヘルパー メソッドを定義する

**Program** クラスに次のメソッドを追加します。 このメソッドは、レビュー クエリの結果を出力テキスト ファイルに書き込みます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>レビュー作成メソッドを定義する

これで、レビューの作成とクエリを処理するメソッドを定義する準備ができました。 新しいメソッド **CreateReviews** を追加し、次のローカル変数を定義します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>レビューをレビュー ツールに投稿する

続いて、指定されたサンプル イメージを反復処理してメタデータを追加し、一括でレビュー ツールに送信するために、次のコードを追加します。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

API 呼び出しから返されたオブジェクトには、アップロードされた各イメージの一意な ID 値が含まれます。 次のコードでは、ID を解析し、それを使用してバッチ内の各イメージの状態を Content Moderator に照会します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>レビューの詳細を取得する

次のコードを使用すると、プログラムはユーザーの入力を待機します。 実行時にこの手順に到達すると、[レビュー](https://contentmoderator.cognitive.microsoft.com) ツールにアクセスしてサンプル イメージがアップロードされたことを確認、操作することができます。 レビューを操作する方法の詳細については、[レビューの攻略ガイド](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images)に関する記事を参照してください。 完了したら、任意のキーを押してプログラムを続行し、レビュー プロセスの結果を取得することができます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

このシナリオでコールバック エンドポイントを使用した場合、次の形式でイベントを受信する必要があります。

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Content Moderator .NET ライブラリを使用して、モデレーション タスクを行う方法について学習しました。 次は、概念的なガイドを読んで、画像や他のメディアのモデレーションに関する詳細について学習します。

> [!div class="nextstepaction"]
> [画像のモデレートの概念](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Azure Content Moderator とは](../../overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs) にあります。
