---
title: クイック スタート:Azure Face .NET SDK を使って画像から顔を検出する
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Azure Face SDK と C# を使用して、画像から顔を検出します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: bf4d11a18932d20e7dcc7580ebe5aa4e060c5a88
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606776"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>クイック スタート:Face .NET SDK を使用して画像から顔を検出する

このクイック スタートでは、Face サービス SDK と C# を使用して、画像から人の顔を検出します。 このクイック スタートで取り上げているコードの実例については、GitHub の [Cognitive Services Vision csharp quickstarts](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) リポジトリにある Face プロジェクトを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="prerequisites"></a>前提条件

- Face API サブスクリプション キー。 無料試用版のサブスクリプション キーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=face-api)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Face API サービスをサブスクライブし、キーを取得します。
- [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション。

## <a name="create-the-visual-studio-project"></a>Visual Studio プロジェクトの作成

1. Visual Studio で、新しい**コンソール アプリ (.NET Framework)** プロジェクトを作成し、**FaceDetection** という名前を付けます。 
1. ソリューションに他のプロジェクトがある場合は、これを単一のスタートアップ プロジェクトとして選択します。
1. 必須の NuGet パッケージを入手します。 ソリューション エクスプローラーで目的のプロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。 **[参照]** タブをクリックし、 **[プレリリースを含める]** を選択した後、次のパッケージを検索してインストールします。
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)
1. プロジェクトのすべての NuGet パッケージの最新バージョンをインストールしたことを確認します。 ソリューション エクスプローラーで目的のプロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。 **[更新]** タブをクリックして、表示されたすべてのパッケージの最新バージョンをインストールします。

## <a name="add-face-detection-code"></a>顔検出コードを追加する

新しいプロジェクトの *Program.cs* ファイルを開きます。 画像を読み込んで顔を検出するために必要なコードをここに追加します。

### <a name="include-namespaces"></a>名前空間を含める

*Program.cs* ファイルの先頭に次の `using` ステートメントを追加します。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>必須フィールドを追加する

次のフィールドを含む **Program** クラスを追加します。 このデータによって、Face サービスへの接続方法と入力データの取得場所が指定されます。 `subscriptionKey` フィールドは、実際のサブスクリプション キーの値で更新する必要があります。また `faceEndpoint` 文字列も、適切なリージョン識別子を含むように、必要に応じて変更してください。 `localImagePath` と `remoteImageUrl` の値についても、実際の画像ファイルを指すパスに設定する必要があります。

`faceAttributes` フィールドは、簡単に言えば、特定の種類の属性を格納する配列です。 検出された顔について、どの情報を取得するかを指定します。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=9-34)]

### <a name="create-and-use-the-face-client"></a>Face クライアントを作成して使用する

次に、次のコードを含む **Program** クラスの **Main** メソッドを追加します。 これは、Face API クライアントを設定するものです。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=36-41)]

また、**Main** メソッドには、リモートとローカルの画像から顔を検出するために、新たに作成した Face クライアントを使用するように、以下のコードも追加します。 実際の検出メソッドは、この後で定義します。 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-50)]

### <a name="detect-faces"></a>顔を検出する

**Program** クラスに次のメソッドを追加します。 Face サービス クライアントを使用してリモートの画像から顔を検出するもので、対象となる画像は URL で参照します。 ここでは、`faceAttributes` フィールドが使用されます&mdash;`faceList` に追加された **DetectedFace** オブジェクトには、指定された属性 (この例では、年齢と性別) があります。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

同様に、**DetectLocalAsync** メソッドを追加します。 Face サービス クライアントを使用してローカルの画像から顔を検出するもので、対象となる画像はファイル パスで参照します。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>顔の属性を取得して表示する

次に、**GetFaceAttributes** メソッドを定義します。 これは、該当する属性情報を含んだ文字列を返します。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

最後に、顔の属性データをコンソール出力に書き込む **DisplayAttributes** メソッドを定義します。 その後、クラスと名前空間を閉じることができます。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-125)]

## <a name="run-the-app"></a>アプリの実行

成功応答には、画像内の個々の顔の性別と年齢が表示されます。 例:

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Face API サービスを使ってローカルとリモートの両方の画像から顔を検出する簡単な .NET コンソール アプリケーションを作成しました。 この後は、一歩進んだチュートリアルに取り組んでみましょう。顔の情報を直感的な方法でユーザーに見せる方法をご覧いただけます。

> [!div class="nextstepaction"]
> [チュートリアル:画像から顔を検出して分析する WPF アプリの作成](../Tutorials/FaceAPIinCSharpTutorial.md)
