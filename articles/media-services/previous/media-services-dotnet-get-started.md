---
title: .NET を使用したオンデマンド コンテンツ配信の概要 | Microsoft Docs
description: このチュートリアルでは、Azure Media Services と .NET を使用したオンデマンド コンテンツ配信アプリケーションの実装手順を紹介します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: f0e7f9fa33af43fb846255815d862d4cd3a07e99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013977"
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>.NET SDK を使用したオンデマンド コンテンツ配信の概要

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

このチュートリアルでは、Azure Media Services (AMS) アプリケーションと Azure Media Services .NET SDK を使用した基本的なビデオ オン デマンド (VoD) コンテンツ配信サービスの実装手順を紹介します。

## <a name="prerequisites"></a>前提条件

チュートリアルを完了するには次のものが必要です。

* Azure アカウント。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。
* Media Services アカウント。 Media Services アカウントを作成するには、[Media Services アカウントを作成する方法](media-services-portal-create-account.md)に関するページを参照してください。
* .NET Framework 4.0 以降。
* 見ることができます。

このチュートリアルに含まれるタスクは次のとおりです。

1. (Azure Portal を使用して) ストリーミング エンドポイントを開始する。
2. Visual Studio プロジェクトの作成と構成
3. Media Services アカウントに接続します。
2. ビデオ ファイルをアップロードする
3. 一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードします。
4. アセットを発行してストリーミング URL とプログレッシブ ダウンロード URL を取得する  
5. コンテンツの再生

## <a name="overview"></a>概要
このチュートリアルでは、Azure Media Services (AMS) SDK for .NET を使用したビデオ オン デマンド (VoD) コンテンツ配信アプリケーションの実装手順について説明します。

チュートリアルでは Media Services の基本的なワークフローや、Media Services 開発に必要となる一般的なプログラミング オブジェクトやタスクについて紹介します。 このチュートリアルを完了すると、サンプル メディア ファイルをアップロード、エンコード、ダウンロードして、ストリーミングやプログレッシブ ダウンロードを実行できます。

### <a name="ams-model"></a>AMS モデル

次の図は、Media Services OData モデルに対する VoD アプリケーションの開発時に最もよく使用されるオブジェクトの一部を示しています。

画像をクリックすると、フル サイズで表示されます。  

[![ビデオ オン デマンド アプリケーションを開発するために Azure Media Services オブジェクト データ モデルで最もよく使用されるオブジェクトの一部を示す図。](./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png)](./media/media-services-dotnet-get-started/media-services-overview-object-model.png#lightbox)

モデル全体は、[こちら](https://media.windows.net/API/$metadata?api-version=2.15)で確認できます。  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Azure Portal を使用したストリーミング エンドポイントの開始

アダプティブ ビットレート ストリーミングでのビデオ配信は、Azure Media Services の代表的な用途の 1 つです。 Media Services にはダイナミック パッケージ機能があり、アダプティブ ビットレート MP4 でエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、Smooth Streaming) でそのまますぐに配信することができます。つまり、事前にパッケージされたこれらのストリーミング形式のバージョンを保存しておく必要がありません。

>[!NOTE]
>AMS アカウントの作成時に、**既定** のストリーミング エンドポイントが自分のアカウントに追加され、**停止** 状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが **実行中** 状態である必要があります。

ストリーミング エンドポイントを開始するには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. [設定] ウィンドウで [ストリーミング エンドポイント] をクリックします。
3. 既定のストリーミング エンドポイントをクリックします。

    [DEFAULT STREAMING ENDPOINT DETAILS (既定のストリーミング エンドポイントの詳細)] ウィンドウが表示されます。

4. [開始] アイコンをクリックします。
5. [保存] をクリックして、変更を保存します。

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio プロジェクトの作成と構成

1. 「[.NET を使用した Media Services 開発](media-services-dotnet-how-to-use.md)」の説明に従って、開発環境をセットアップし、app.config ファイルに接続情報を指定します。 
2. 新しいフォルダーをローカル ドライブの任意の場所に作成し、エンコードしてストリーミングするかプログレッシブ ダウンロードする .mp4 ファイルをコピーします。 この例では、"C:\VideoFiles" というパスを使用しています。

## <a name="connect-to-the-media-services-account"></a>Media Services アカウントへの接続

Media Services を .NET で使用するとき、Media Services に関連したプログラミング タスクの大半、たとえば、各種オブジェクト (アセット、アセットファイル、ジョブ、アクセス ポリシー、ロケーターなど) の作成、更新、アクセス、削除の作業で、 **CloudMediaContext** クラスが必要となります。

既定の Program クラスを次のコードで上書きします。このコードは、App.config ファイルから接続値を読み取り、 **CloudMediaContext** オブジェクトを作成して Media Services に接続する方法を示しています。 詳細については、[Media Services API への接続](media-services-use-aad-auth-to-access-ams-api.md)に関するページを参照してください。

メディア ファイルのある場所に合わせて、ファイル名とパスを更新してください。

**Main** 関数で呼び出すメソッドは、この後、定義していきます。

> [!NOTE]
> この記事の中で定義するすべての関数の定義を追加するまでは、コンパイル エラーが発生します。

```csharp
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
```

## <a name="create-a-new-asset-and-upload-a-video-file"></a>新しいアセットの作成とビデオ ファイルのアップロード

Media Services で、デジタル ファイルをアセットにアップロードし (取り込み) ます。 **Asset** エンティティには、ビデオ、オーディオ、画像、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイル (各ファイルのメタデータを含む) を追加できます。ファイルをアップロードすると、クラウドにコンテンツが安全に保存され、処理したりストリーミングしたりできるようになります。 アセット内のこれらのファイルを **アセットファイル** といいます。

以下に定義した **UploadFile** メソッドは、(.NET SDK Extensions で定義されている) **CreateFromFile** を呼び出します。 **CreateFromFile** によって、指定されたソース ファイルのアップロード先となる新しいアセットが作成されます。

**CreateFromFile** メソッドの **AssetCreationOptions** 引数には、次のいずれかのアセット作成オプションを指定できます。

* **None** : 暗号化は使用されません。 これが既定値です。 このオプションを使用した場合、送信経路上とストレージ内のいずれにおいてもコンテンツが保護されないので注意してください。
  プログレッシブ ダウンロードを使用して MP4 を配信する場合はこのオプションを使用します。
* **StorageEncrypted** – Advanced Encryption Standard (AES)-256 ビット暗号化を使用してクリア コンテンツをローカルに暗号化し、それを Azure Storage にアップロードすると、コンテンツが保存時に暗号化された状態で格納されます。 StorageEncrypted で保護されたアセットは、エンコーディングの前に自動的に暗号化が解除され、暗号化されたファイル システムに配置されます。その後、必要に応じて再度暗号化を適用して、新しい出力アセットとして再びアップロードできます。 StorageEncrypted の主な目的は、高品質の入力メディア ファイルを強力な暗号化によって保護したうえでディスクに保存するというニーズに応えることです。
* **CommonEncryptionProtected** : 既に Common Encryption や PlayReady DRM で暗号化されて保護されているコンテンツ (PlayReady DRM で保護されたスムーズ ストリーミングなど) をアップロードする場合は、このオプションを使用します。
* **EnvelopeEncryptionProtected** : AES で暗号化された HLS をアップロードする場合はこのオプションを使用します。 この場合ファイルは、Transform Manager によってあらかじめエンコードされて暗号化されている必要があります。

**CreateFromFile** メソッドには、ファイルのアップロードの進行状況をレポートするためのコールバックも指定できます。

次の例では、アセットのオプションに **None** を指定しています。

次のメソッドを Program クラスに追加します。

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードする
Media Services に取り込んだアセットには、メディアのエンコード、再パッケージ化、透かしの追加などをクライアントへの配信前に適用できます。 高いパフォーマンスと可用性を確保するために、これらの作業は、複数のバックグラウンド ロール インスタンスに対してスケジューリングされて実行されます。 これらのアクティビティはジョブと呼ばれ、各ジョブは、アセットファイルの実際の作業を実行するアトミック タスクで構成されます。

冒頭で述べたように、Azure Media Services の代表的な用途の 1 つは、クライアントに対するアダプティブ ビットレート ストリーミング配信です。 Media Services は、一連のアダプティブ ビットレート MP4 ファイルを次のいずれかの形式に動的にパッケージ化できます:HTTP ライブ ストリーミング (HLS)、Smooth Streaming、および MPEG DASH。

ダイナミック パッケージを活用するには、mezzanine (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードまたはトランスコードする必要があります。  

以下のコードは、エンコーディング ジョブの送信方法を示したものです。 このジョブには、 **Media Encoder Standard** を使用して mezzanine ファイルを一連のアダプティブ ビットレート MP4 にトランスコードするよう指定するタスクが 1 つ存在します。 このコードは、ジョブを送信してその完了を待機します。

ジョブが完了したら、アセットをストリーミングしたり、コード変換によって作成された MP4 ファイルをプログレッシブにダウンロードしたりできます。

次のメソッドを Program クラスに追加します。

```csharp
    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }
```

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>アセットを発行してストリーミング URL とプログレッシブ ダウンロード URL を取得する

アセットをストリーミングまたはダウンロードするにはまず、ロケーターを作成してアセットを「発行」する必要があります。 アセットに含まれているファイルには、ロケーターを通じてアクセスできます。 Media Services では、2 種類のロケーターがサポートされています。メディアをストリーミングする (MPEG DASH、HLS、Smooth Streaming など) ために使用される OnDemandOrigin ロケーター、およびメディア ファイルをダウンロードするために使用される Access Signature (SAS) ロケーター。

### <a name="some-details-about-url-formats"></a>URL 形式の詳細

ロケーターを作成したら、対象ファイルのストリーミングやダウンロードに使用する URL を作成できます。 このチュートリアルのサンプルでは、適切なブラウザーに貼り付けることができる URL を出力します。 このセクションでは、さまざまな形式の簡単な例を示します。

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>MPEG DASH のストリーミング URL の形式は次のとおりです。

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest **(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>HLS のストリーミング URL の形式は次のとおりです。

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest **(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>スムーズ ストリーミングのストリーミング URL の形式は次のとおりです。

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>ファイルをダウンロードするための SAS URL の形式は次のとおりです。

{BLOB コンテナー名}/{アセット名}/{ファイル名}/{SAS 署名}

Media Services .NET SDK Extensions には、発行済みのアセットの URL を正しい形式で取得できるヘルパー メソッドが用意されています。

次のコードでは、.NET SDK Extensions を使用してロケーターを作成し、ストリーミング URL と プログレッシブ ダウンロード URL を取得しています。 また、ローカル フォルダーにファイルをダウンロードする方法もこのコードで確認できます。

次のメソッドを Program クラスに追加します。

```csharp
    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }
```

## <a name="test-by-playing-your-content"></a>コンテンツを再生することでテストする

前のセクションで定義したプログラムを実行すると、コンソール ウィンドウに次のような URL が表示されます。

アダプティブ ストリーミングの URL:

スムーズ ストリーミング

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest`

HLS

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)`

MPEG DASH

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)`

プログレッシブ ダウンロードの URL (オーディオとビデオ):

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`


ビデオをストリーミングするには、[Azure Media Services プレーヤー](https://aka.ms/azuremediaplayer)の URL テキスト ボックスに URL を貼り付けます。

プログレッシブ ダウンロードをテストするには、ブラウザー (Internet Explorer、Chrome、Safari など) に URL を貼り付けます。

詳細については、次のトピックを参照してください。

- [既存のプレーヤーによるコンテンツの再生](media-services-playback-content-with-existing-players.md)
- [DASH.js を使用した HTML5 アプリケーションへの MPEG-DASH アダプティブ ストリーミング ビデオの埋め込み](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>サンプルのダウンロード
このチュートリアルで作成したコードは、[このサンプル コード](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)に含まれています。

## <a name="next-steps"></a>次の手順

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: https://go.microsoft.com/fwlink/?linkid=255386
[Portal]: https://portal.azure.com/
