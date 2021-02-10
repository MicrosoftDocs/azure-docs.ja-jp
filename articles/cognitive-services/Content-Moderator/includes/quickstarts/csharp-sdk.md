---
title: Content Moderator .NET クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、.NET 用 Azure Content Moderator クライアント ライブラリの使用を開始する方法について説明します。 法的規制に準拠したり、ユーザーに意図されている環境を維持したりするために、コンテンツ フィルタリング ソフトウェアをアプリに組み込みます。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: ed9d7aee5f677a5c278a985c3f8102364e94ab7e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947635"
---
.NET 用 Azure Content Moderator クライアント ライブラリの使用を開始します。 以下の手順に従って、NuGet パッケージをインストールし、基本タスクのコード例を試してみましょう。 

Content Moderator は、不快感を与える可能性がある内容、リスクのある内容、その他望ましくない可能性のある内容を管理できる AI サービスです。 AI を利用したコンテンツ モデレーション サービスを使用して、テキスト、画像、ビデオをスキャンし、コンテンツ フラグを自動的に適用します。 人間のレビュー担当者チーム向けのオンライン モデレーター環境であるレビュー ツールに対して、自分のアプリを統合することができます。 法的規制に準拠したり、ユーザーに意図されている環境を維持したりするために、コンテンツ フィルタリング ソフトウェアをアプリに組み込みます。

.NET 用 Content Moderator クライアント ライブラリは、次の目的で使用できます。

* テキストのモデレート
* 画像のモデレート
* レビューを作成する

[リファレンスのドキュメント](/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [サンプル](../../samples-dotnet.md)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) または現在のバージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Content Moderator リソースを作成"  target="_blank">Content Moderator リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Content Moderator に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio を使用して、新しい .NET Core アプリケーションを作成します。 

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする 

新しいプロジェクトを作成したら、**ソリューション エクスプローラー** でプロジェクト ソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールします。 パッケージ マネージャーが開いたら、 **[参照]** を選択し、 **[プレリリースを含める]** をオンにして、`Microsoft.Azure.CognitiveServices.ContentModerator` を検索します。 バージョン `2.0.0` を選択し、 **[インストール]** を選択します。 

#### <a name="cli"></a>[CLI](#tab/cli)

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

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする 

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Content Moderator クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs) にあり、このクイックスタートのコード例が含まれています。

プロジェクト ディレクトリから、好みのエディターまたは IDE で *Program.cs* ファイルを開きます。 次の `using` ステートメントを追加します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

**Program** クラスで、対象のリソースのキーとエンドポイントの変数を作成します。

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Content Moderator リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services-security.md)に関するページを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


アプリケーションの `main()` メソッドで、このクイックスタートで使用するメソッドの呼び出しを追加します。 これらは後で作成します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>オブジェクト モデル

以下のクラスでは、Content Moderator .NET クライアント ライブラリの主要な機能の一部を処理します。

|名前|説明|
|---|---|
|[ContentModeratorClient](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient)|このクラスは、すべての Content Moderator の機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。|
|[ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation)|このクラスでは、成人向けコンテンツ、個人情報、または人間の顔の画像を分析するための機能が提供されます。|
|[TextModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation)|このクラスでは、言語、冒涜的表現、エラー、個人情報のテキストを分析するための機能が提供されます。|
|[Reviews](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews)|このクラスでは、ジョブ、カスタム ワークフロー、人によるレビューを作成するためのメソッド含む、レビュー API の機能が提供されます。|

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、.NET 用 Content Moderator クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [テキストのモデレート](#moderate-text)
* [画像のモデレート](#moderate-images)
* [レビューを作成する](#create-a-review)

## <a name="authenticate-the-client"></a>クライアントを認証する

新しいメソッドで、実際のエンドポイントとキーを使用してクライアント オブジェクトをインスタンス化します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>テキストのモデレート

次のコードでは、Content Moderator クライアントを使用してテキストの本文が分析され、結果がコンソールに出力されます。 **Program** クラスのルートで、入力ファイルと出力ファイルを定義します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

次に、プロジェクトのルートに *TextFile.txt* ファイルを追加します。 このファイルに独自のテキストを追加するか、次のサンプル テキストを使用します。

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


次に、**Program** クラスのどこかにテキスト モデレーション メソッドを定義します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>画像のモデレート

次のコードでは、Content Moderator クライアントが [ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation) オブジェクトとともに使用され、成人向けコンテンツやきわどい内容のリモート画像が分析されます。

> [!NOTE]
> ローカルの画像コンテンツを分析することもできます。 ローカル画像を操作するメソッドと操作については、[リファレンス ドキュメント](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_)を参照してください。

### <a name="get-sample-images"></a>サンプル イメージの取得

**Program** クラスのルートで、入力ファイルと出力ファイルを定義します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

次に、プロジェクトのルートに入力ファイル *ImageFiles.txt* を作成します。 このファイルでは、画像の URL を追加して&mdash;行ごとに 1 つの URL を分析します。 次のサンプル イメージを使用できます。

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>ヘルパー クラスを定義する

**Program** クラスに次のクラス定義を追加します。 この内部クラスは、画像のモデレーションの結果を処理します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>画像のモデレーション メソッドを定義する

次のメソッドは、テキスト ファイル内の画像 URL を反復処理して **EvaluationData** インスタンスを作成し、成人向けまたはわいせつなコンテンツ、テキスト、および人間の顔の画像を分析します。 次に、最終的な **EvaluationData** インスタンスをリストに追加し、返されたデータの完全な一覧をコンソールに書き込みます。

#### <a name="iterate-through-images"></a>画像を反復処理する

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>コンテンツの分析

Content Moderator 画面に表示される画像の属性の詳細については、[画像のモデレーションの概念](../../image-moderation-api.md)ガイドを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>モデレーション結果をファイルに書き込む

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>レビューを作成する

Content Moderator .NET クライアント ライブラリを使用して、人間のモデレーターがレビューできるように、[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)にコンテンツをフィードすることができます。 レビュー ツールの詳細については、[レビュー ツールの概念的なガイド](../../review-tool-user-guide/human-in-the-loop.md)に関するページを参照してください。

このセクションのメソッドでは、[Reviews](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews) クラスを使用してレビューが作成され、その ID が取得されて、レビュー ツールの Web ポータルから人間による入力を受け取った後、その詳細が確認されます。 これらの情報は、すべて出力テキスト ファイルに記録されます。 

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

次のコードを使用すると、プログラムはユーザーの入力を待機します。 実行時にこの手順に到達すると、[レビュー](https://contentmoderator.cognitive.microsoft.com) ツールにアクセスしてサンプル イメージがアップロードされたことを確認、操作することができます。 レビューを操作する方法の詳細については、[レビューの攻略ガイド](../../review-tool-user-guide/review-moderated-images.md)に関する記事を参照してください。 完了したら、任意のキーを押してプログラムを続行し、レビュー プロセスの結果を取得することができます。

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

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

IDE ウィンドウの上部にある **[デバッグ]** ボタンをクリックして、アプリケーションを実行します。

#### <a name="cli"></a>[CLI](#tab/cli)

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Content Moderator .NET ライブラリを使用して、モデレーション タスクを行う方法について学習しました。 次は、概念的なガイドを読んで、画像や他のメディアのモデレーションに関する詳細について学習します。

> [!div class="nextstepaction"]
> [画像のモデレートの概念](../../image-moderation-api.md)

* [Azure Content Moderator とは](../../overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs) にあります。
