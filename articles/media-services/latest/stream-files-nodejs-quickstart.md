---
title: Azure Media Services で動画ファイルをストリーム配信する - Node.js | Microsoft Docs
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
ms.openlocfilehash: fa9fbf3bac55ca0b26c3644b7f6818fa96088612
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639387"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - Node.js

このチュートリアルでは、Azure Media Services を利用すると、さまざまなブラウザーおよびデバイスを対象とした動画のエンコードとストリーム配信の開始がいかに容易であるかを示します。 HTTPS URL、SAS URL、または Azure Blob Storage 内に存在するファイルへのパスを使って入力コンテンツを指定できます。

この記事のサンプルでは、HTTPS URL を使用してアクセスできるようにするコンテンツをエンコードします。 現在、AMS v3では、HTTPS URLを介したチャンク転送のエンコーディングはサポートされていません。

このチュートリアルを最後まで行うと、動画をストリーム配信できるようになります。  

![ビデオを再生する](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- [Node.js](https://nodejs.org/en/download/)
- [Media Services アカウントを作成する](create-account-cli-how-to.md)<br/>Media Services アカウント名、ストレージ名、およびリソース名として使用した値を覚えておいてください。
- 「[Azure CLI で Azure Media Services API にアクセスする](access-api-cli-how-to.md)」の手順に従い、資格情報を保存します。 API にアクセスするために必要となります。

## <a name="download-and-configure-the-sample"></a>サンプルをダウンロードして構成する

次のコマンドを使って、ストリーム配信の Node.js サンプルが含まれる GitHub リポジトリを、お使いのマシンに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

サンプルは、[StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) フォルダーにあります。

ダウンロードしたプロジェクト内の [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) を開きます。 `endpoint config` の値を、[API へのアクセス](access-api-cli-how-to.md)によって取得した資格情報に置き換えます。

サンプルは、次のアクションを実行します。

1. **変換**を作成します (最初に、指定された変換が存在するかどうかを確認します)。 
2. エンコード **ジョブ**の出力として使用される**出力**アセットを作成します。
3. HTTPS の URL に基づいて**ジョブ**の入力を作成します。
4. 前に作成された入力と出力を使用してエンコード **ジョブ**を送信します。
5. ジョブの状態を確認します。
6. **ストリーミング ロケーター**を作成します。
7. ストリーミング URL を作成します。

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

1. エンコードされたファイルが、アプリによってダウンロードされます。 出力ファイルを保存するフォルダーを作成し、[index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) ファイル内の **outputFolder** 変数の値を更新します。
1. **コマンド プロンプト**を開き、サンプルのディレクトリに移動して、以下のコマンドを実行します。

    ```
    npm install 
    node index.js
    ```

実行の完了後、次のような出力が表示されます。

![ラン](./media/stream-files-nodejs-quickstart/run.png)

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

## <a name="see-also"></a>関連項目

[ジョブ エラー コード](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Media Services の概念](concepts-overview.md)
