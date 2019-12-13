---
title: クイック スタート:.NET 用 Face クライアント ライブラリ | Microsoft Docs
description: .NET 用 Face クライアント ライブラリを使ってみます。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5602e26e2e1b327d65d411f8e92f264f1bf9fb91
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977864"
---
# <a name="quickstart-face-client-library-for-net"></a>クイック スタート:.NET 用 Face クライアント ライブラリ

.NET 用 Face クライアント ライブラリを使ってみます。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Face API サービスは、画像内の人間の顔を検出および認識するための高度なアルゴリズムへのアクセスを提供します。

.NET 用 Face クライアント ライブラリは、次の目的で使用します。

* [クライアントを認証する](#authenticate-the-client)
* [画像内の顔を検出する](#detect-faces-in-an-image)
* [似た顔を探す](#find-similar-faces)
* [人物グループを作成してトレーニングする](#create-and-train-a-person-group)
* [顔を識別する](#identify-a-face)
* [データ移行のためのスナップショットを作成する](#take-a-snapshot-for-data-migration)

[リファレンスのドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [サンプル](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="setting-up"></a>設定

### <a name="create-a-face-azure-resource"></a>Face Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用してローカル マシン上に Face 用のリソースを作成します。 さらに、以下を実行できます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services/#decision)を無料で入手する。 サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)でこれを入手できます。  
* [Azure portal](https://portal.azure.com/) でご利用のリソースを表示する。

試用版のサブスクリプションまたはリソースからキーを取得した後、キーとエンドポイント URL の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)し、それぞれ `FACE_SUBSCRIPTION_KEY` および `FACE_ENDPOINT` という名前を付けます。

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

好みのエディターまたは IDE で、新しい .NET Core アプリケーションを作成します。 

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`face-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。*Program.cs*。 

```dotnetcli
dotnet new console -n face-quickstart
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

```dotnetcli
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

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

アプリケーションの `Main` メソッドで、対象のリソースの Azure エンドポイントおよびキー用の変数を作成します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Face クライアント ライブラリをインストールします。

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Visual Studio IDE を使用している場合、クライアント ライブラリは、ダウンロード可能な NuGet パッケージとして入手できます。

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、Face .NET SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | このクラスは、Face サービスを使用するための承認を表し、すべての Face 機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。 |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|このクラスは、人間の顔に対して実行できる基本的な検出と認識のタスクを処理します。 |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|このクラスは、画像内の 1 つの顔から検出されたすべてのデータを表します。 これを使用して、顔に関する詳細情報を取得できます。|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|このクラスは、クラウドに格納された **FaceList** コンストラクトを管理します。これには、さまざまな顔のセットが格納されます。 |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| このクラスは、クラウドに格納された **Person** コンストラクトを管理します。これには、1 人の人物に属している顔のセットが格納されます。|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| このクラスは、クラウドに格納された **PersonGroup** コンストラクトを管理します。これには、さまざまな **Person** オブジェクトのセットが格納されます。 |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|このクラスは、スナップショット機能を管理します。 これを使用すると、クラウドベースのすべての Face データを一時的に保存し、そのデータを新しい Azure サブスクリプションに移行することができます。 |

## <a name="code-examples"></a>コード例

以下のコード スニペットでは、.NET 用 Face クライアント ライブラリを使用して次のタスクを実行する方法を示しています。

* [クライアントを認証する](#authenticate-the-client)
* [画像内の顔を検出する](#detect-faces-in-an-image)
* [似た顔を探す](#find-similar-faces)
* [人物グループを作成してトレーニングする](#create-and-train-a-person-group)
* [顔を識別する](#identify-a-face)
* [データ移行のためのスナップショットを作成する](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>クライアントを認証する

> [!NOTE]
> このクイックスタートでは、`FACE_SUBSCRIPTION_KEY` および `FACE_ENDPOINT` という名前の、Face キーとエンドポイントの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)してあることを前提としています。

新しいメソッドで、実際のエンドポイントとキーを使用してクライアントをインスタンス化します。 キーを使用して [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) オブジェクトを作成し、それをエンドポイントと共に使用して、[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) オブジェクトを作成します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

ほとんどの場合、このメソッドは、`Main` メソッドから呼び出します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>画像内の顔を検出する

クラスのルートで、次の URL 文字列を定義します。 この URL は一連のサンプル画像を指します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

検出された顔からどの AI モデルを使用してデータを抽出するかを必要に応じて選択できます。 それらの選択については、[認識モデルの指定](../Face-API-How-to-Topics/specify-recognition-model.md)に関するページを参照してください。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

最後の検出操作では、[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) オブジェクト、画像の URL、認識モデルを受け取ります。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>検出された顔オブジェクトを取得する

次のコード ブロックでは、指定された URL にある画像のうち 3 つから `DetectFaceExtract` メソッドで顔を検出し、[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) オブジェクトのリストをプログラムのメモリに作成します。 抽出する特徴は、[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) 値のリストで指定します。 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>検出された顔データを表示する

`DetectFaceExtract` メソッドの残りの部分では、検出された各顔の属性データが解析されて出力されます。 各属性は、元の顔検出 API 呼び出しで個別に指定する必要があります ([FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) リストで)。 次のコードではすべての属性が処理されますが、通常、使用する必要があるのは 1 個または数個だけです。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>似た顔の検索

以下のコードでは、検出された顔 (ソース) を 1 つ受け取って、他の顔のセット (ターゲット) から一致するものを見つけます。 一致するものが見つかると、一致した顔の ID がコンソールに出力されます。

### <a name="detect-faces-for-comparison"></a>比較の対象となる顔を検出する

まず、2 つ目の顔検出メソッドを定義します。 顔を比較するためにはまず、画像で顔を検出する必要があります。また、この検出メソッドは比較操作向けに最適化されています。 前のセクションほど詳細には顔の属性を抽出せず、また、別の認識モデルを使用しています。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>一致するものを探す

次のメソッドでは、一連のターゲット画像と単一のソース画像から顔を検出します。 その後、それらを比較し、ソース画像と似ているターゲット画像をすべて見つけます。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>一致するものを出力する

次のコードは、一致の詳細をコンソールに出力します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>人物グループを作成してトレーニングする

次のコードでは、6 つの異なる **Person** オブジェクトを含んだ **PersonGroup** を作成します。 各 **Person** を一連のサンプル画像に関連付け、顔の特徴によって各人物を認識するためのトレーニングを行います。 **Person** オブジェクトと **PersonGroup** オブジェクトは、検証、識別、グループ化の各操作で使用されます。

まだ URL 文字列を定義していない場合は、クラスのルートで次の URL 文字列を定義してください。 これは一連のサンプル画像を指します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

顔からデータを抽出するための認識モデルは、このセクションの後出のコードで指定します。また、使用可能なモデルへの参照は、以下のスニペットで作成します。 認識モデルについては、[認識モデルの指定](../Face-API-How-to-Topics/specify-recognition-model.md)に関するページを参照してください。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>PersonGroup を作成する

作成する **PersonGroup** の ID を表す文字列変数をクラスのルートで宣言します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

新しいメソッドに次のコードを追加します。 このコードは、人物の名前をそのサンプル画像に関連付けます。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

次に、Dictionary 内の人物ごとに **Person** オブジェクトを作成して該当する画像から顔データを追加する次のコードを追加します。 各 **Person** オブジェクトは、その一意の ID 文字列によって同じ **PersonGroup** に関連付けられます。 このメソッドに `client`、`url`、`RECOGNITION_MODEL1` の各変数を忘れずに渡してください。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>PersonGroup をトレーニングする

画像から顔データを抽出して個別の **Person** オブジェクトに分類したら、各 **Person** オブジェクトに関連付けられた視覚的特徴を識別できるよう **PersonGroup** をトレーニングする必要があります。 次のコードは、非同期の **train** メソッドを呼び出し、結果をポーリングして、状態をコンソールに出力します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

これで、この **Person** グループとそれに関連付けられた **Person** オブジェクトを検証、識別、グループ化の各操作で使用する準備が整いました。

## <a name="identify-a-face"></a>顔を識別する

識別操作では、人物 (1人または複数人) の画像を受け取り、その画像に含まれるそれぞれの顔の同一性を探します。 検出された顔はそれぞれ、顔の特徴が確認されているさまざまな **Person** オブジェクトのデータベース、つまり **PersonGroup** と比較されます。

> [!IMPORTANT]
> この例を実行するには、まず、「[人物グループを作成してトレーニングする](#create-and-train-a-person-group)」のコードを実行する必要があります。 そのセクションで使用されている変数 (`client`、`url`、`RECOGNITION_MODEL1`) をここでも用意しておく必要があります。

### <a name="get-a-test-image"></a>テスト画像を取得する

「[人物グループを作成してトレーニングする](#create-and-train-a-person-group)」のコードで、`sourceImageFileName` 変数が定義されていることに注目してください。 この変数は、ソース画像 (識別の対象となる人物を含んだ画像) に対応します。

### <a name="identify-faces"></a>顔を識別する

次のコードは、ソース画像を受け取って、その画像で検出されたすべての顔のリストを作成します。 それらの顔が、**PersonGroup** と照らして識別されることになります。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

次のコード スニペットは、識別操作を呼び出してその結果をコンソールに出力します。 ここでは、このサービスによって、ソース画像に含まれるそれぞれの顔が、指定された **PersonGroup** 内の **Person** と照合されます。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>データ移行のためのスナップショットを作成する

スナップショット機能を使用すると、トレーニング済みの **PersonGroup** などの保存した顔データを別の Azure Cognitive Services Face サブスクリプションに移動できます。 この機能を利用するのは、たとえば、無料試用版サブスクリプションを使用して **PersonGroup** オブジェクトを作成済みで、それを有料サブスクリプションに移行することが必要になった場合です。 スナップショット機能の概要については、[顔データの移行](../Face-API-How-to-Topics/how-to-migrate-face-data.md)に関するページを参照してください。

この例では、「[人物グループを作成してトレーニングする](#create-and-train-a-person-group)」で作成した **PersonGroup** を移行します。 先にそのセクションを完了しても、移行する独自の Face データ コンストラクトを作成してもかまいません。

### <a name="set-up-target-subscription"></a>ターゲット サブスクリプションを設定する

まず、Face リソースを含む 2 つ目の Azure サブスクリプションが必要です。そのためには、「[設定](#setting-up)」セクションの手順に従います。 

その後、プログラムの `Main` メソッドに次の変数を定義します。 お使いの Azure アカウントのサブスクリプション ID と新しい (ターゲット) アカウントのキー、エンドポイント、サブスクリプション ID のための、新しい環境変数を作成する必要があります。 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

この例では、ターゲット **PersonGroup** (データのコピー先となる、新しいサブスクリプションに属するオブジェクト) の ID に使用する変数を宣言します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>ターゲット クライアントを認証する

次に、2 つ目の Face サブスクリプションを認証するためのコードを追加します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>スナップショットを使用する

スナップショット操作の残りは、非同期メソッド内で実行する必要があります。 

1. 最初のステップは、スナップショットを**取得する**ことです。これにより、元のサブスクリプションの顔データが一時的なクラウドの場所に保存されます。 このメソッドは、操作の状態を照会するために使用する ID を返します。

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. 次に、操作が完了するまでに ID を照会します。

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. その後、**apply** 操作を使用して、顔データをターゲット サブスクリプションに書き込みます。 このメソッドも、ID 値を返します。

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. 操作が完了するまでにもう一度新しい ID を照会します。

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. 最後に、try/catch ブロックを実行してメソッドを終了します。

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

この時点で、新しい **PersonGroup** オブジェクトには、元のオブジェクトと同じデータが保持されており、新しい (ターゲット) Azure Face サブスクリプションからアクセスすることができます。

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

このクイックスタートで作成してある **PersonGroup** を削除したい場合は、プログラムで次のコードを実行します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

次のコードを使用して削除メソッドを定義します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

さらに、このクイックスタートでスナップショット機能を使用してデータを移行した場合は、ターゲット サブスクリプションに保存されている **PersonGroup** も削除する必要があります。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>次の手順

このクイックスタートでは、.NET 用の Face ライブラリを使用して基本的なタスクを行う方法について学習しました。 次は、リファレンス ドキュメントを参照して、ライブラリの詳細について学習してください。

> [!div class="nextstepaction"]
> [Face API リファレンス (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Face API とは](../overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs) にあります。
