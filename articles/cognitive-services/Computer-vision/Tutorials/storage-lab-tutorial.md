---
title: チュートリアル:Azure の画像のメタデータを生成する
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、Azure Computer Vision サービスを Web アプリに統合して、画像のメタデータを生成する方法について学習します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 7c83350dbecaf20e9b35f159b2c01824777bc665
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973715"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>チュートリアル:Computer Vision を使用して Azure Storage に画像メタデータを生成する

このチュートリアルでは、Azure Computer Vision サービスを Web アプリに統合して、アップロードされた画像のメタデータを生成する方法について学習します。 これは[デジタル資産管理 (DAM)](../Home.md#computer-vision-for-digital-asset-management) のシナリオに役立ちます。たとえば、企業がすべての画像についてわかりやすいキャプションや検索可能なキーワードをすばやく生成する場合などです。

完全なアプリ ガイドは、GitHub 上の [Azure Storage と Cognitive Services のラボ](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md)に関するページにあります。このチュートリアルでは、基本的にラボの演習 5 を取り扱います。 すべての手順に従って完全なアプリケーションを作成してみてください。ただし、Computer Vision を既存の Web アプリに統合する方法についてだけ知りたい場合は、こちらをお読みください。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * Azure で Computer Vision リソースを作成する
> * Azure Storage の画像に対して画像分析を実行する
> * Azure Storage の画像にメタデータをアタッチする
> * Azure Storage Explorer を使用して画像のメタデータを確認する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 

## <a name="prerequisites"></a>前提条件

- "ASP.NET と Web 開発" および "Azure の開発" ワークロードがインストールされている [Visual Studio 2017 Community エディション](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)以上。
- 画像ストレージ用に BLOB コンテナーが設定された Azure ストレージ アカウント (この手順に関してヘルプが必要な場合は [Azure Storage ラボの演習 1](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) に従ってください)。
- Azure Storage Explorer ツール (この手順に関してヘルプが必要な場合は [Azure Storage ラボの演習 2](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) に従ってください)。
- Azure Storage にアクセスできる ASP.NET Web アプリケーション (このようなアプリをすばやく作成するには、[Azure Storage ラボの演習 3](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) に従ってください)。

## <a name="create-a-computer-vision-resource"></a>Computer Vision リソースを作成する

お客様の Azure アカウント用に Computer Vision リソースを作成する必要があります。このリソースによって、Azure の Computer Vision サービスへのアクセスが管理されます。 

1. [Azure Cognitive Services リソースを作成する](../../cognitive-services-apis-create-account.md)の指示に従って、Computer Vision リソースを作成します。

1. 次に、お客様のリソース グループのメニューに移動し、お客様が先ほど作成した Computer Vision API サブスクリプションをクリックします。 **[エンドポイント]** の下の URL を、一瞬で簡単に取得できる場所にコピーします。 次に、 **[アクセス キーを表示]** をクリックします。

    ![エンドポイントの URL とアクセス キーのリンクを強調した Azure portal ページ](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. 次のウィンドウで、 **[キー 1]** の値をクリップボードにコピーします。

    ![コピー ボタンが強調表示された [キーの管理] ダイアログ](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Computer Vision の資格情報を追加する

次に、お客様のアプリが Computer Vision リソースにアクセスできるように、必要な資格情報を追加します

Visual Studio でお客様の ASP.NET Web アプリケーションを開き、プロジェクトのルートにある **Web.config** ファイルに移動します。 次のステートメントをファイルの `<appSettings>` セクションに追加します。このとき、`VISION_KEY` を前の手順でコピーしたキーに置き換え、`VISION_ENDPOINT` をその前の手順で保存した URL に置き換えます。

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

次に、ソリューション エクスプローラーで、プロジェクトを右クリックし、 **[NuGet パッケージの管理]** コマンドを使用してパッケージ **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** をインストールします。 このパッケージには、Computer Vision API を呼び出すために必要な型が含まれています。

## <a name="add-metadata-generation-code"></a>メタデータ生成コードを追加する

次に、Computer Vision サービスを実際に利用して画像のメタデータを作成するコードを追加します。 これらの手順はラボの ASP.NET アプリを対象にしていますが、お客様独自のアプリにも利用できます。 重要なのは、この時点で、Azure Storage コンテナーに画像をアップロードし、そこから画像を読み取ってビューに表示できる ASP.NET Web アプリケーションがあるということです。 この手順についてよくわからない場合は、[Azure Storage ラボの演習 3](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) に従うことをお勧めします。 

1. プロジェクトの **Controllers** フォルダーにある *HomeController.cs* ファイルを開き、ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. 次に、**Upload** メソッドに進みます。このメソッドでは、画像を BLOB ストレージに変換してアップロードします。 次のコードを、`// Generate a thumbnail` で始まるブロックの直後 (または image-blob-creation プロセスの最後) に追加します。 このコードでは、画像 (`photo`) を含む BLOB を受け取り、Computer Vision を使用してその画像の説明を生成します。 Computer Vision API によって、画像に適用されるキーワードの一覧も生成されます。 生成された説明とキーワードは、後で取得できるように BLOB のメタデータに格納されます。

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. 次に、同じファイルの **Index** メソッドに進みます。 このメソッドでは、対象となる BLOB コンテナーに格納されている画像 BLOB を (**IListBlobItem** インスタンスとして) 列挙し、それらをアプリケーション ビューに渡します。 このメソッドの `foreach` ブロックを次のコードに置き換えます。 このコードでは、**CloudBlockBlob.FetchAttributes** を呼び出して、各 BLOB のアタッチされたメタデータを取得します。 コンピューターによって生成された説明 (`caption`) がメタデータから抽出され、それが **BlobInfo** オブジェクトに追加されます。これがビューに渡されます。
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>アプリケーションをテストする

変更内容を Visual Studio に保存し、**Ctrl + F5** キーを押してブラウザーでアプリケーションを起動します。 ラボのリソースの "photos" フォルダーから、またはご自分のフォルダーから、アプリを使用して画像をいくつかアップロードします。 ビュー内のいずれかの画像の上にカーソルを置くと、ツールチップ ウィンドウが表示され、画像に対してコンピューターによって生成されたキャプションが表示されます。

![コンピューターによって生成されたキャプション](../Images/thumbnail-with-tooltip.png)

アタッチされているメタデータをすべて表示するには、Azure Storage Explorer を使用して、お客様が画像に使用しているストレージ コンテナーを表示します。 コンテナー内の任意の BLOB を右クリックして、 **[プロパティ]** を選択します。 ダイアログに、キーと値のペアの一覧が表示されます。 コンピューターによって生成された画像の説明は "Caption" という項目に格納され、検索キーワードは "Tag0"、”Tag1" などに格納されます。 完了したら、 **[キャンセル]** をクリックしてダイアログを閉じます。

![メタデータ タグが一覧表示された画像のプロパティ ダイアログ ウィンドウ](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

お客様の Web アプリの作業を続行する場合は、「[次のステップ](#next-steps)」セクションを参照してください。 このアプリケーションを使い続ける予定がない場合は、アプリに固有のリソースをすべて削除する必要があります。 リソースの削除は、お客様の Azure Storage サブスクリプションと Computer Vision リソースが含まれているリソース グループを削除することで実行できます。 これにより、ストレージ アカウント、そこにアップロードされた BLOB、および ASP.NET Web アプリに接続するために必要な App Service リソースが削除されます。 

リソース グループを削除するには、ポータルで **[リソース グループ]** タブを開き、お客様がこのプロジェクトに使用したリソース グループに移動して、ビューの上部にある **[リソース グループの削除]** をクリックします。 リソース グループはいったん削除すると復旧できないため、リソース グループの名前を入力して削除の意思を確認するように求められます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure の Computer Vision サービスを既存の Web アプリに設定して、BLOB 画像をアップロードしたときにキャプションとキーワードが自動的に生成されるようにしました。 次は、Azure Storage ラボの演習 6 を参照して、お客様の Web アプリに検索機能を追加する方法について学習してください。 そこでは、Computer Vision サービスによって生成される検索キーワードを利用します。

> [!div class="nextstepaction"]
> [アプリに検索を追加する](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
