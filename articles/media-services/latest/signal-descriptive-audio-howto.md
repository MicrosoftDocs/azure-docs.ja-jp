---
title: Media Services v3 を使用した説明オーディオ トラックの通知
description: Media Services v3 を使用してファイルのアップロード、ビデオのエンコード、説明オーディオ トラックの追加、コンテンツのストリーム配信を行うには、このチュートリアルの手順に従います。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 01854e7636f53ec1faab157b51cc84f3539582b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596724"
---
# <a name="signal-descriptive-audio-tracks"></a>説明オーディオ トラックの通知

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

ナレーション トラックをビデオに追加することで、目の不自由なお客様がナレーションを聞いて動画の内容を追うことができます。 Media Services v3 では、マニフェスト ファイルのオーディオ トラックに注釈を付けることによって、説明オーディオ トラックを通知します。

この記事では、ビデオをエンコードし、説明オーディオを含む音声のみの MP4 ファイル (AAC コーデック) を出力アセットにアップロードし、.ism ファイルを編集して説明オーディオを含める方法について説明します。

## <a name="prerequisites"></a>前提条件

- [Media Services アカウントを作成する](./create-account-howto.md)
- 「[Azure CLI で Azure Media Services API にアクセスする](./access-api-howto.md)」の手順に従い、資格情報を保存します。 API にアクセスするために必要となります。
- 「[ダイナミック パッケージ](dynamic-packaging-overview.md)」を確認してください。
- 「[ビデオのアップロード、エンコード、ストリーミング](stream-files-tutorial-with-api.md)」のチュートリアルを確認してください。

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>入力アセットを作成し、ローカル ファイルをそれにアップロードする 

**CreateInputAsset** 関数は、新しい入力 [アセット](/rest/api/media/assets)を作成し、指定されたローカル ビデオ ファイルをそこにアップロードします。 この **アセット** は、エンコード ジョブへの入力として使われます。 Media Services v3 では、**ジョブ** への入力としては、**アセット** を使うか、または HTTPS URL 経由で Media Services アカウントから使用できるようにされたコンテンツを使うことができます。 

HTTPS URL からのエンコード方法について詳しくは、[こちら](job-input-from-http-how-to.md)の記事をご覧ください。  

Media Services v3 では、Azure Storage API を使ってファイルをアップロードします。 次の .NET スニペットはその方法を示したものです。

後で示す関数は、次の処理を実行します。

* **アセット** を作成する 
* 書き込み可能な [SAS URL](../../storage/common/storage-sas-overview.md) をアセットの[ストレージ内のコンテナー](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)に取得する
* SAS URL を使用してストレージ内のコンテナーにファイルをアップロードする

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

作成した入力アセットの名前を他のメソッドに渡す必要がある場合は、inputAsset.Name のように、`CreateInputAssetAsync` から返された資産オブジェクトの `Name` プロパティを使用してください。 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>エンコード ジョブの結果を格納する出力アセットを作成する

出力[アセット](/rest/api/media/assets)には、対象のエンコード ジョブの結果が格納されます。 次の関数は、出力アセットを作成する方法を示しています。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

作成した出力アセットの名前を他のメソッドに渡す必要がある場合は、outputAsset.Name のように、`CreateIOutputAssetAsync` から返された資産オブジェクトの `Name` プロパティを使用してください。 

この記事の場合は、`outputAsset.Name` 値を `SubmitJobAsync` 関数と `UploadAudioIntoOutputAsset` 関数に渡します。

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>アップロードされたファイルをエンコードする変換とジョブを作成する

Media Services でコンテンツをエンコードまたは処理するときは、レシピとしてエンコード設定をセットアップするのが一般的なパターンです。 その後、**ジョブ** を送信してビデオにレシピを適用します。 新しいビデオごとに新しいジョブを送信することで、ライブラリ内のすべてのビデオにレシピを適用します。 Media Services でのレシピは **変換** と呼ばれます。 詳しくは、「[Transform と Job](./transforms-jobs-concept.md)」をご覧ください。 このチュートリアルで説明するサンプルでは、さまざまな iOS および Android デバイスにストリーム配信するために、ビデオをエンコードするレシピが定義されています。 

次の例では、変換を作成します (存在しない場合)。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

次の関数は、ジョブを送信します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>ジョブが完了するのを待つ

ジョブの完了には時間がかかり、完了したら通知を受け取る必要があります。 ジョブが完了するまで待機する際は、Event Grid を使用することをお勧めします。

ジョブには通常、**Scheduled**、**Queued**、**Processing**、**Finished** (最終状態) という状態があります。 ジョブでエラーが発生すると、**Error** 状態を取得します。 ジョブがキャンセル処理中の場合は **Canceling** を受け取り、完了すると **Canceled** を受け取ります。

詳細については、「[Event Grid イベントの処理](monitoring/reacting-to-media-services-events.md)」を参照してください。

## <a name="upload-the-audio-only-mp4-file"></a>オーディオのみの MP4 ファイルのアップロード

説明オーディオを含む追加のオーディオ専用 MP4 ファイル (AAC コーデック) を出力アセットにアップロードします。  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

`UpoadAudioIntoOutputAsset` 関数の呼び出しの例を次に示します。

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>.ism ファイルを編集する

エンコード ジョブが完了すると、エンコード ジョブで生成されたファイルが出力アセットに含まれます。 

1. Azure portal で、Media Services アカウントに関連付けられているストレージ アカウントに移動します。 
1. 出力アセットの名前が付いたコンテナーを検索します。 
1. コンテナーで .ism ファイルを見つけ、 **[BLOB の編集]** (右側のウィンドウ) をクリックします。 
1. 説明オーディオを含むアップロードされたオーディオのみの MP4 ファイル (AAC コーデック) に関する情報を追加して .ism ファイルを編集し、完了したら **[保存]** を押します。

    説明用オーディオ トラックを通知するには、.ism ファイルに "accessibility" パラメーターと "role" パラメーターを追加する必要があります。 オーディオ トラックをオーディオ説明としてシグナル通知するためには、これらのパラメーターを正しく設定する必要があります。 たとえば、次の例に示すように、特定のオーディオ トラックの .ism ファイルに `<param name="accessibility" value="description" />` と `<param name="role" value="alternate" />` を追加します。
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>ストリーミング ロケーターを取得する

エンコードが完了したら次に、出力アセット内のビデオを、クライアントが再生に使用できるようにします。 これを実現するには 2 つのステップがあります。最初に[ストリーミング ロケーター](/rest/api/media/streaminglocators)を作成し、次にクライアントが使用できるストリーミング URL を作成します。 

**ストリーミング ロケーター** を作成するプロセスは発行と呼ばれます。 既定では、**ストリーミング ロケーター** は API 呼び出しを行うとすぐに有効になり、省略可能な開始時刻と終了時刻を構成しない限り、削除されるまで存続します。 

[StreamingLocator](/rest/api/media/streaminglocators) を作成するときは、使用する **StreamingPolicyName** を指定する必要があります。 この例では、クリアなコンテンツ (暗号化されていないコンテンツ) をストリーム配信するので、定義済みのクリア ストリーミング ポリシー **PredefinedStreamingPolicy.ClearStreamingOnly** が使用されます。

> [!IMPORTANT]
> カスタム [ストリーミング ポリシー](/rest/api/media/streamingpolicies)を使うときは、Media Service アカウントに対してこのようなポリシーの限られたセットを設計し、同じ暗号化オプションとプロトコルが必要なときは常に、お使いの StreamingLocator に対してそのセットを再利用する必要があります。 Media Service アカウントには、ストリーミング ポリシー エントリの数に対するクォータがあります。 ストリーミング ロケーターごとに新しいストリーミング ポリシーを作成しないでください。

次のコードでは、一意の locatorName で関数を呼び出しているものとします。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

このトピックのサンプルではストリーム配信について説明していますが、同じ呼び出しを使って、プログレッシブ ダウンロードによるビデオ配信用のストリーミング ロケーターを作成できます。

### <a name="get-streaming-urls"></a>ストリーミング URL を取得する

[ストリーミング ロケーター](/rest/api/media/streaminglocators)が作成されたので、**GetStreamingURLs** で示されているように、ストリーミング URL を取得できます。 URL を作成するには、[ストリーミング エンドポイント](/rest/api/media/streamingendpoints)のホスト名と **ストリーミング ロケーター** パスを連結する必要があります。 このサンプルでは、*既定の* **ストリーミング エンドポイント** を使っています。 最初に Media Service アカウントを作成したとき、この *既定の* **ストリーミング エンドポイント** は停止状態になっているので、**Start** を呼び出す必要があります。

> [!NOTE]
> このメソッドでは、出力アセットの **ストリーミング ロケーター** を作成するときに使った locatorName が必要です。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Azure Media Player でテストする

ストリーム配信をテストするため、この記事では Azure Media Player を使います。 

> [!NOTE]
> プレーヤーが HTTPS サイトでホストされている場合は、忘れずに URL を "https" に更新してください。

1. Web ブラウザーを開いて、[https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) にアクセスします。
2. **[URL:]** ボックスに、アプリケーションから取得したストリーム配信 URL 値のいずれかを貼り付けます。 
 
     URL は、HLS、Dash、または Smooth 形式で貼り付けることができます。Azure Media Player によって、デバイスでの再生に適切なストリーミング プロトコルに自動的に切り替えられます。
3. **[Update Player]\(プレーヤーの更新\)** をクリックします。

Azure Media Player はテストには使用できますが、運用環境では使わないでください。 

## <a name="next-steps"></a>次のステップ

[ビデオを分析する](analyze-videos-tutorial-with-api.md)
