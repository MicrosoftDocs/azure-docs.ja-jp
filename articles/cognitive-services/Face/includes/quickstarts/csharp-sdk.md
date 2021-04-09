---
title: Face .NET クライアント ライブラリのクイックスタート
description: .NET 用 Face クライアント ライブラリを使用して、顔の検出、類似検索 (画像による顔検索)、顔の識別 (顔認識検索)、顔データの移行を行います。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: b06a35274082e09ec0973c5123b39935a93e354d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444952"
---
.NET 用 Face クライアント ライブラリを使用して顔認識を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Face サービスは、画像内の人間の顔を検出および認識するための高度なアルゴリズムへのアクセスを提供します。

.NET 用 Face クライアント ライブラリは、次の目的で使用します。

* [画像内の顔を検出する](#detect-faces-in-an-image)
* [似た顔を探す](#find-similar-faces)
* [人物グループを作成する](#create-a-person-group)
* [顔を識別する](#identify-a-face)

[リファレンスのドキュメント](/dotnet/api/overview/azure/cognitiveservices/client/faceapi) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1) | [サンプル](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>前提条件


* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) または現在のバージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face リソースを作成"  target="_blank">Face リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Face API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio を使用して、新しい .NET Core アプリケーションを作成します。 

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする 

新しいプロジェクトを作成したら、**ソリューション エクスプローラー** でプロジェクト ソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールします。 パッケージ マネージャーが開いたら、 **[参照]** を選択し、 **[プレリリースを含める]** をオンにして、`Microsoft.Azure.CognitiveServices.Vision.Face` を検索します。 バージョン `2.6.0-preview.1` を選択し、 **[インストール]** を選択します。 

#### <a name="cli"></a>[CLI](#tab/cli)

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`face-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (*program.cs*) を使用する単純な "Hello World" C# プロジェクトが作成されます。 

```console
dotnet new console -n face-quickstart
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

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Face クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

---

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs) にあり、このクイックスタートのコード例が含まれています。


プロジェクト ディレクトリから *program.cs* ファイルを開いて、次の `using` ディレクティブを追加します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

アプリケーションの **Program** クラスで、対象のリソースのキーとエンドポイントの変数を作成します。


> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Face リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services-security.md)に関するページを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

アプリケーションの **Main** メソッドで、このクイックスタートで使用するメソッドの呼び出しを追加します。 これらは後で実装します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、Face .NET クライアント ライブラリの主要な機能の一部が処理されます。

|名前|説明|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) | このクラスは、Face サービスを使用するための承認を表し、すべての Face 機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。 |
|[FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations)|このクラスは、人間の顔に対して実行できる基本的な検出と認識のタスクを処理します。 |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)|このクラスは、画像内の 1 つの顔から検出されたすべてのデータを表します。 これを使用して、顔に関する詳細情報を取得できます。|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations)|このクラスは、クラウドに格納された **FaceList** コンストラクトを管理します。これには、さまざまな顔のセットが格納されます。 |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions)| このクラスは、クラウドに格納された **Person** コンストラクトを管理します。これには、1 人の人物に属している顔のセットが格納されます。|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations)| このクラスは、クラウドに格納された **PersonGroup** コンストラクトを管理します。これには、さまざまな **Person** オブジェクトのセットが格納されます。 |

## <a name="code-examples"></a>コード例

以下のコード スニペットでは、.NET 用 Face クライアント ライブラリを使用して次のタスクを実行する方法を示しています。

* [クライアントを認証する](#authenticate-the-client)
* [画像内の顔を検出する](#detect-faces-in-an-image)
* [似た顔を探す](#find-similar-faces)
* [人物グループを作成する](#create-a-person-group)
* [顔を識別する](#identify-a-face)

## <a name="authenticate-the-client"></a>クライアントを認証する

新しいメソッドで、実際のエンドポイントとキーを使用してクライアントをインスタンス化します。 自分のキーを指定して **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials)** オブジェクトを作成し、それを自分のエンドポイントと共に使用して **[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient)** オブジェクトを作成します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>ヘルパー フィールドを宣言する

次のフィールドは、後で追加するいくつかの Face 操作に必要です。 **Program** クラスのルートで、次の URL 文字列を定義します。 この URL は、サンプル画像のフォルダーを指しています。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

**Main** メソッドで、さまざまな認識モデルの種類を指すように文字列を定義します。 後で、顔検出に使用する認識モデルを指定できます。 それらの選択については、[認識モデルの指定](../../Face-API-How-to-Topics/specify-recognition-model.md)に関するページを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>画像内の顔を検出する

### <a name="get-detected-face-objects"></a>検出された顔オブジェクトを取得する

顔を検出するための新しいメソッドを作成します。 `DetectFaceExtract` メソッドでは、指定された URL にある画像のうち 3 つを処理し、 **[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)** オブジェクトのリストをプログラムのメモリに作成します。 抽出する特徴は、 **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** 値のリストで指定します。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> また、ローカルの画像から顔を検出することもできます。 [IFaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations) のメソッドを参照してください (**DetectWithStreamAsync** など)。

### <a name="display-detected-face-data"></a>検出された顔データを表示する

`DetectFaceExtract` メソッドの残りの部分では、検出された各顔の属性データが解析されて出力されます。 各属性は、元の顔検出 API 呼び出しで個別に指定する必要があります ( **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** リストで)。 次のコードではすべての属性が処理されますが、通常、使用する必要があるのは 1 個または数個だけです。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>似た顔の検索

以下のコードでは、検出された顔 (ソース) を 1 つ受け取って、他の顔のセット (ターゲット) から一致するものを見つけます (画像による顔検索)。 一致するものが見つかると、一致した顔の ID がコンソールに出力されます。

### <a name="detect-faces-for-comparison"></a>比較の対象となる顔を検出する

まず、2 つ目の顔検出メソッドを定義します。 顔を比較するためにはまず、画像で顔を検出する必要があります。また、この検出メソッドは比較操作向けに最適化されています。 前のセクションほど詳細には顔の属性を抽出せず、また、別の認識モデルを使用しています。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>一致するものを探す

次のメソッドでは、一連のターゲット画像と単一のソース画像から顔を検出します。 その後、それらを比較し、ソース画像と似ているターゲット画像をすべて見つけます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>一致するものを出力する

次のコードは、一致の詳細をコンソールに出力します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>顔を識別する

識別操作では、人物 (1人または複数人) の画像を受け取り、その画像に含まれるそれぞれの顔の同一性を見つけます (顔認識検索)。 検出された顔はそれぞれ、顔の特徴が確認されているさまざまな **Person** オブジェクトのデータベース、つまり **PersonGroup** と比較されます。 識別操作を行うには、最初に **PersonGroup** を作成してトレーニングする必要があります

### <a name="create-a-person-group"></a>人物グループを作成する

次のコードでは、6 つの異なる **Person** オブジェクトを含んだ **PersonGroup** を作成します。 各 **Person** を一連のサンプル画像に関連付け、顔の特徴によって各人物を認識するためのトレーニングを行います。 **Person** オブジェクトと **PersonGroup** オブジェクトは、検証、識別、グループ化の各操作で使用されます。

作成する **PersonGroup** の ID を表す文字列変数をクラスのルートで宣言します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

新しいメソッドに次のコードを追加します。 このメソッドは、識別操作を実行します。 コードの最初のブロックでは、人物の名前をそのサンプル画像に関連付けます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

このコードでは変数 `sourceImageFileName` が定義されていることに注意してください。 この変数は、ソース画像 (識別の対象となる人物を含んだ画像) に対応します。

次に、Dictionary 内の人物ごとに **Person** オブジェクトを作成して該当する画像から顔データを追加する次のコードを追加します。 各 **Person** オブジェクトは、その一意の ID 文字列によって同じ **PersonGroup** に関連付けられます。 このメソッドに `client`、`url`、`RECOGNITION_MODEL1` の各変数を忘れずに渡してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> ローカル画像から **PersonGroup** を作成することもできます。 [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson) のメソッドを参照してください (**AddFaceFromStreamAsync** など)。

### <a name="train-the-persongroup"></a>PersonGroup をトレーニングする

画像から顔データを抽出して個別の **Person** オブジェクトに分類したら、各 **Person** オブジェクトに関連付けられた視覚的特徴を識別できるよう **PersonGroup** をトレーニングする必要があります。 次のコードは、非同期の **train** メソッドを呼び出し、結果をポーリングして、状態をコンソールに出力します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> Face API は、本質的に静的な一連の事前構築済みモデルで実行されます (サービスの実行中にモデルのパフォーマンスが低下したり改善されたりすることはありません)。 Microsoft により、まったく新しいモデル バージョンに移行することなくモデルのバックエンドが更新されると、モデルによって生成される結果が変わる可能性があります。 より新しいバージョンのモデルを利用するには、同じ登録画像でより新しいモデルをパラメーターとして指定し、**PersonGroup** を再トレーニングすることができます。

これで、この **Person** グループとそれに関連付けられた **Person** オブジェクトを検証、識別、グループ化の各操作で使用する準備が整いました。

### <a name="identify-faces"></a>顔を識別する

次のコードは、ソース画像を受け取って、その画像で検出されたすべての顔のリストを作成します。 それらの顔が、**PersonGroup** と照らして識別されることになります。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

次のコード スニペットは、**IdentifyAsync** 操作を呼び出してその結果をコンソールに出力します。 ここでは、このサービスによって、ソース画像に含まれるそれぞれの顔が、指定された **PersonGroup** 内の **Person** と照合されます。 これにより、識別メソッドが終了します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

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

このクイックスタートで作成してある **PersonGroup** を削除したい場合は、プログラムで次のコードを実行します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

次のコードを使用して削除メソッドを定義します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、.NET 用の Face クライアント ライブラリを使用して基本的な顔認識タスクを行う方法について学習しました。 次は、リファレンス ドキュメントを参照して、ライブラリの詳細について学習してください。

> [!div class="nextstepaction"]
> [Face API リファレンス (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)

* [Face サービスとは](../../overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs) にあります。
