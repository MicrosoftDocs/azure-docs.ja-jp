---
title: Azure Media Services で動画ファイルをストリーム配信する - .NET | Microsoft Docs
description: このチュートリアルの手順に従って、新しい Azure Media Services アカウントを作成し、ファイルをエンコードして、Azure Media Player にストリーム配信します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: Azure Media Services, ストリーム配信
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: 7f997865ba33a51c3e3aa7a4c7e990037be9e534
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637331"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - .NET

このチュートリアルでは、Azure Media Services を利用すると、さまざまなブラウザーおよびデバイスを対象とした動画のエンコードとストリーム配信の開始がいかに容易であるかを示します。 HTTPS URL、SAS URL、または Azure Blob Storage 内に存在するファイルへのパスを使って入力コンテンツを指定できます。
このトピックのサンプルでは、HTTPS URL を使用してアクセスできるようにするコンテンツをエンコードします。 現在、AMS v3では、HTTPS URLを介したチャンク転送のエンコーディングはサポートされていません。

このチュートリアルを最後まで行うと、動画をストリーム配信できるようになります。  

![ビデオを再生する](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- Visual Studio がインストールされていない場合は、[Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) を入手できます。
- [Media Services アカウントを作成する](create-account-cli-how-to.md)<br/>Media Services アカウント名、ストレージ名、およびリソース名として使用した値を覚えておいてください。
- 「[Azure CLI で Azure Media Services API にアクセスする](access-api-cli-how-to.md)」の手順に従い、資格情報を保存します。 API にアクセスするために必要となります。

## <a name="download-and-configure-the-sample"></a>サンプルをダウンロードして構成する

次のコマンドを使って、ストリーム配信の .NET サンプルが含まれる GitHub リポジトリを、お使いのコンピューターに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

サンプルは、[EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles) フォルダーにあります。

ダウンロードしたプロジェクトに含まれる [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) を開きます。 [API へのアクセス](access-api-cli-how-to.md)に関するページで取得した資格情報の値に置き換えます。

サンプルは、次のアクションを実行します。

1. **変換**を作成します (最初に、指定された変換が存在するかどうかを確認します)。 
2. エンコード **ジョブ**の出力として使用される**出力**アセットを作成します。
3. HTTPS の URL に基づいて**ジョブ**の入力を作成します。
4. 前に作成された入力と出力を使用してエンコード **ジョブ**を送信します。
5. ジョブの状態を確認します。
6. **ストリーミング ロケーター**を作成します。
7. ストリーミング URL を作成します。

サンプル内の各関数の機能について詳しくは、[このソース ファイル](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)のコードを調べて、コメントを参照してください。

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

アプリを実行すると、別のプロトコルでビデオを再生するために使用できる URL が表示されます。 

1. Ctrl + F5 キーを押して、*EncodeAndStreamFiles* アプリケーションを実行します。
2. Apple の **HLS** プロトコル (末尾が *manifest(format=m3u8-aapl)* ) を選び、コンソールからストリーム配信 URL をコピーします。

![Output](./media/stream-files-tutorial-with-api/output.png)

サンプルの[ソース コード](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)では、URL の作成方法を確認できます。 URL を作成するには、ストリーミング エンドポイントのホスト名とストリーミング ロケーター パスを連結する必要があります。  

## <a name="test-with-azure-media-player"></a>Azure Media Player でテストする

ストリーム配信をテストするため、この記事では Azure Media Player を使います。 

> [!NOTE]
> プレーヤーが HTTPS サイトでホストされている場合は、忘れずに URL を "https" に更新してください。

1. Web ブラウザーを開いて、[https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) にアクセスします。
2. **[URL:]** ボックスに、アプリケーションを実行したときに取得したストリーム配信 URL 値のいずれかを貼り付けます。 
 
     URL は、HLS、Dash、または Smooth 形式で貼り付けることができます。Azure Media Player によって、デバイスでの再生に適切なストリーミング プロトコルに自動的に切り替えられます。
3. **[Update Player]\(プレーヤーの更新\)** をクリックします。

Azure Media Player はテストには使用できますが、運用環境では使わないでください。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、リソース グループを削除します。

次の CLI コマンドを実行します。

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>コードを確認する

サンプル内の各関数の機能について詳しくは、[このソース ファイル](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)のコードを調べて、コメントを参照してください。

[ファイルのアップロード、エンコード、ストリーム配信](stream-files-tutorial-with-api.md)に関するチュートリアルでは、より高度なストリーム配信の例と詳しい説明が提供されています。 

### <a name="job-error-codes"></a>ジョブ エラー コード

[エラー コード](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)に関するページを参照してください。

## <a name="multithreading"></a>マルチスレッド

Azure Media Services v3 SDK は、スレッドセーフではありません。 マルチスレッド アプリケーションで使うときは、スレッドごとに新しい AzureMediaServicesClient オブジェクトを生成する必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル: ファイルのアップロード、エンコード、ストリーム配信](stream-files-tutorial-with-api.md)
