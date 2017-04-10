---
title: "PlayReady または Widevine の動的共通暗号化を使用する | Microsoft Docs"
description: "Microsoft Azure Media Services を使用すると、MPEG DASH、Smooth Streaming、および HTTP ライブ ストリーミング (HLS) のストリームを Microsoft PlayReady DRM で保護して配信できます。 Widevine DRM で暗号化された DASH を配信することもできます。 このトピックでは、PlayReady と Widevine DRM を使用して動的に暗号化する方法について説明します。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 548d1a12-e2cb-45fe-9307-4ec0320567a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 8f11b9a6606e30e323295d4144497fae90040d2a
ms.lasthandoff: 03/14/2017


---
# <a name="using-playready-andor-widevine-dynamic-common-encryption"></a>PlayReady または Widevine の動的共通暗号化を使用する

> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-drm.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>
>

Microsoft Azure Media Services を使用すると、MPEG DASH、Smooth Streaming、および HTTP ライブ ストリーミング (HLS) のストリームを [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/)で保護して配信できます。 Widevine DRM ライセンスで暗号化された DASH ストリームを配信することもできます。 PlayReady と Widevine はいずれも共通暗号化 (ISO/IEC 23001-7 CENC) 仕様に従って暗号化されます。 [AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (バージョン 3.5.1 以降) または REST API を使用して、Widevine を使用するように AssetDeliveryConfiguration を構成できます。

Media Services は、PlayReady および Widevine DRM のライセンスを配信するためのサービスを提供しています。 また、Media Services で提供される API を使用して、保護されたコンテンツをユーザーが再生するときに PlayReady または Widevine DRM ランタイムが適用する権限と制限を構成することもできます。 ユーザーが DRM で保護されたコンテンツを要求すると、プレーヤー アプリケーションが AMS ライセンス サービスにライセンスを要求します。 AMS ライセンス サービスは、プレーヤーが承認されていれば、ライセンスをプレーヤーに発行します。 PlayReady または Widevine のライセンスには、クライアント プレーヤーがコンテンツの暗号化解除とストリーミングに使用できる暗号化解除キーが含まれています。

[Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)、[EZDRM](http://ezdrm.com/)、[castLabs](http://castlabs.com/company/partners/azure/) の各 AMS パートナーを使用して、Widevine ライセンスを提供することもできます。 詳細については、[Axinom](media-services-axinom-integration.md) および [castLabs](media-services-castlabs-integration.md) との統合を参照してください。

Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープンまたはトークン制限) を指定できます。 トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services では、[Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) 形式と [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) 形式のトークンがサポートされます。 詳細については、「コンテンツ キー承認ポリシーを構成する」を参照してください。

動的暗号化を使用するには、一連のマルチビットレート MP4 ファイルまたはマルチビットレート Smooth Streaming ソース ファイルを含む資産が必要です。 また、資産の配信ポリシーを構成する必要があります (このトピック内で後述します)。 次に、ストリーミング URL で指定された形式に基づいて、オンデマンド ストリーミング サーバーは、選択されたプロトコルでストリームを配信できるようにします。 その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの各要求に応じて、適切な HTTP 応答を構築して返します。

このトピックには、PlayReady や Widevine など、複数の DRM で保護されたメディアを配信するアプリケーションの開発に取り組む開発者にとって有用な情報が含まれています。 このトピックでは、認証ポリシーを使用する PlayReady ライセンス配信サービスの構成方法を説明します。これにより、許可されたクライアントのみが PlayReady または Widevine のライセンスを受け取ることができるようになります。 また、DASH を介した PlayReady または Widevine DRM による動的な暗号化を使用する方法も説明します。

>[!NOTE]
>AMS アカウントの作成時に、**既定**のストリーミング エンドポイントが自分のアカウントに追加され、**停止**状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 

## <a name="download-sample"></a>サンプルのダウンロード
この記事で説明されているサンプルは、 [こちら](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)からダウンロードできます。

## <a name="configuring-dynamic-common-encryption-and-drm-license-delivery-services"></a>動的共通暗号化と DRM ライセンス配信サービスの構成

以下では、Media Services ライセンス配信サービスと動的暗号化を使用して、PlayReady で資産を保護するときに実行する必要のある一般的な手順について説明します。

1. 資産を作成し、その資産にファイルをアップロードします。
2. ファイルが含まれる資産をアダプティブ ビットレート MP4 セットにエンコードします。
3. コンテンツ キーを作成し、それをエンコードした資産に関連付けます。 Media Services では、コンテンツ キーに資産の暗号化キーが含まれています。
4. コンテンツ キーの承認ポリシーを構成します。 コンテンツ キーがクライアントに配信されるには、クライアントがこのコンテンツ キー承認ポリシーを満たしている必要があります。

    コンテンツ キー承認ポリシーを作成するときは、配信方法 (PlayReady または Widevine)、制限 (オープンまたはトークン)、クライアントへのキー配信方法を定義するキー配信種類に固有の情報 ([PlayReady](media-services-playready-license-template-overview.md) または [Widevine](media-services-widevine-license-template-overview.md) ライセンス テンプレート) を指定する必要があります。

5. 資産の配信ポリシーを構成します。 配信ポリシーの構成には、配信プロトコル (たとえば、MPEG DASH、HLS、Smooth Streaming、またはそのすべて)、動的暗号化の種類 (たとえば、共通暗号化)、PlayReady または Widevine のライセンス取得 URL が含まれます。

    同じ資産の各プロトコルに異なるポリシーを適用できます。 たとえば、PlayReady 暗号化を Smooth/DASH に適用し、AES Envelope を HLS に適用できます。 配信ポリシーで定義されていないプロトコル (たとえば、プロトコルとして HLS のみを指定する&1; つのポリシーを追加した場合) は、ストリーミングからブロックされます。 ただし、資産配信ポリシーをまったく定義していない場合は例外となります。 この場合、すべてのプロトコルが平文で許可されます。

6. ストリーミング URL を取得するために OnDemand ロケーターを作成します。

このトピックの最後に、詳細な .NET の例があります。

前述したワークフローを図にすると、次のようになります。 ここでは、認証のためにトークンが使用されています。

![PlayReady による保護](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

このトピックの残りの部分では、詳細な説明、コード例、および前述したタスクの実行方法を説明するトピックへのリンクを紹介します。

## <a name="current-limitations"></a>現時点での制限事項
資産の配信ポリシーを追加または更新する場合は、関連するロケーターを削除し (存在する場合)、新しいロケーターを作成する必要があります。

Azure Media Services で Widevine を使用して暗号化するときの制限事項として、現在は複数のコンテンツ キーはサポートされていません。

## <a name="create-an-asset-and-upload-files-into-the-asset"></a>資産を作成し、その資産にファイルをアップロードする
ビデオの管理、エンコード、およびストリーミングを行うには、最初にコンテンツを Microsoft Azure Media Services にアップロードする必要があります。 コンテンツをアップロードすると、クラウドにコンテンツが安全に保存され、処理したりストリーミングしたりできるようになります。

詳細については、 [Media Services アカウントへのファイルのアップロード](media-services-dotnet-upload-files.md)に関するページを参照してください。

## <a name="encode-the-asset-containing-the-file-to-the-adaptive-bitrate-mp4-set"></a>ファイルが含まれる資産をアダプティブ ビットレート MP4 セットにエンコードする
動的暗号化を使用する場合に必要となるのは、一連のマルチビットレート MP4 ファイルまたはマルチビットレート Smooth Streaming ソース ファイルを含む資産の作成のみです。 そうすれば、マニフェストおよびフラグメント要求で指定された形式に基づき、オンデマンド ストリーミング サーバーによって、ユーザーが選択したプロトコルでストリームを受信するようになります。 その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて、適切な応答を構築して返します。 詳しくは、[ダイナミック パッケージの概要](media-services-dynamic-packaging-overview.md)に関する記事をご覧ください。

エンコード手順については、「 [Media Encoder Standard を使用して資産をエンコードする方法](media-services-dotnet-encode-with-media-encoder-standard.md)」を参照してください。

## <a id="create_contentkey"></a>コンテンツ キーを作成し、それをエンコードした資産に関連付ける
Media Services では、コンテンツ キーに、資産を暗号化するときに使用するキーが含まれています。

詳細については、 [コンテンツ キーの作成](media-services-dotnet-create-contentkey.md)に関するページを参照してください。

## <a id="configure_key_auth_policy"></a>コンテンツ キー承認ポリシーの構成
Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーを構成する必要があります。キーがクライアント (プレーヤー) に配信されるには、クライアントがこのコンテンツ キー承認ポリシーを満たしている必要があります。 コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープンまたはトークン制限) を指定できます。

詳細については、「 [コンテンツ キー承認ポリシーを構成する](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption)」を参照してください。

## <a id="configure_asset_delivery_policy"></a>資産の配信ポリシーを構成する
資産の配信ポリシーを構成します。 資産の配信ポリシーの構成には、次の内容が含まれます。

* DRM ライセンス取得 URL
* 資産配信プロトコル (たとえば、MPEG DASH、HLS、Smooth Streaming、またはすべて)。
* 動的暗号化のタイプ (この場合は、共通暗号化)

詳細については、 [資産の配信ポリシーの構成 ](media-services-rest-configure-asset-delivery-policy.md)に関するページを参照してください。

## <a id="create_locator"></a>ストリーミング URL を取得するために OnDemand ロケーターを作成する
Smooth、DASH、HLS のストリーミング URL をユーザーに提供する必要があります。

> [!NOTE]
> 資産の配信ポリシーを追加または更新する場合は、既存のロケーターを削除し (存在する場合)、新しいロケーターを作成する必要があります。
>
>

資産を発行し、ストリーミング URL を構築する手順については、「 [Build a streaming URL (ストリーミング URL の構築)](media-services-deliver-streaming-content.md)」をご覧ください。

## <a name="get-a-test-token"></a>テスト トークンを取得する
キー認証ポリシーに使用されたトークンによる制限に基づいて、テスト トークンを取得します。

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string.
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);


[AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) を使用して、ストリームをテストできます。

## <a id="example"></a>例
次の例では、Azure Media Services SDK for .Net バージョン 3.5.2 で導入された機能を示します (具体的には、Widevine ライセンス テンプレートを定義し、Azure Media Services に Widevine ライセンスを要求する機能)。 パッケージのインストールには、次の Nuget パッケージ コマンドを使用しました。

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. 新しいコンソール プロジェクトを作成します。
2. NuGet を使用して、Azure Media Services .NET SDK をインストールして追加します。
3. その他の参照 System.Configuration を追加します。
4. アカウント名とキー情報が含まれた構成ファイルを追加します。

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup>
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>

                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>

                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>
7. Program.cs ファイルのコードを、このセクションで示されているコードで上書きします。

    >[!NOTE]
    >さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可などを使う場合は、同じポリシー ID を使う必要があります (たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー))。 詳細については、 [こちらの](media-services-dotnet-manage-entities.md#limit-access-policies) トピックを参照してください。

    必ず変数を更新して、入力ファイルが置かれているフォルダーをポイントするようにしてください。

        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
        using Newtonsoft.Json;

        namespace DynamicEncryptionWithDRM
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];

                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);

                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;

                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");

                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);

                    bool tokenRestriction = false;
                    string tokenTemplateString = null;

                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);

                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
                    Console.WriteLine();

                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);

                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();

                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();

                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }

                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).

                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);

                    Console.ReadLine();
                }

                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }

                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

                    var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

                    Console.WriteLine("Created assetFile {0}", assetFile.Name);

                    Console.WriteLine("Upload {0}", assetFile.Name);

                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);

                    return inputAsset;
                }

                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "Adaptive Streaming";

                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));

                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),     AssetCreationOptions.StorageEncrypted);

                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();

                    return job.OutputMediaAssets[0];
                }


                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {

                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);

                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);

                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);

                    return key;
                }

                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {

                    // Create ContentKeyAuthorizationPolicy with Open restrictions
                    // and create authorization policy          

                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Open",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null
                        }
                    };

                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);

                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.Widevine,
                            restrictions, WidevineLicenseTemplate);

                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with no restrictions").
                                Result;


                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }

                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();

                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Token Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                            Requirements = tokenTemplateString,
                        }
                    };

                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);

                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.Widevine,
                                restrictions, WidevineLicenseTemplate);

                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with token restrictions").
                                Result;

                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;

                    return tokenTemplateString;
                }

                static private string GenerateTokenRequirements()
                {
                    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

                    template.PrimaryVerificationKey = new SymmetricVerificationKey();
                    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
                    template.Audience = _sampleAudience.ToString();
                    template.Issuer = _sampleIssuer.ToString();
                    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

                    return TokenRestrictionTemplateSerializer.Serialize(template);
                }

                static private string ConfigurePlayReadyLicenseTemplate()
                {
                    // The following code configures PlayReady License Template using .NET classes
                    // and returns the XML string.

                    //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user.
                    //It contains a field for a custom data string between the license server and the application
                    //(may be useful for custom app logic) as well as a list of one or more license templates.
                    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

                    // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
                    // to be returned to the end users.
                    //It contains the data on the content key in the license and any rights or restrictions to be
                    //enforced by the PlayReady DRM runtime when using the content key.
                    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
                    //Configure whether the license is persistent (saved in persistent storage on the client)
                    //or non-persistent (only held in memory while the player is using the license).  
                    licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

                    // AllowTestDevices controls whether test devices can use the license or not.  
                    // If true, the MinimumSecurityLevel property of the license
                    // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
                    licenseTemplate.AllowTestDevices = true;

                    // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class.
                    // It grants the user the ability to playback the content subject to the zero or more restrictions
                    // configured in the license and on the PlayRight itself (for playback specific policy).
                    // Much of the policy on the PlayRight has to do with output restrictions
                    // which control the types of outputs that the content can be played over and
                    // any restrictions that must be put in place when using a given output.
                    // For example, if the DigitalVideoOnlyContentRestriction is enabled,
                    //then the DRM runtime will only allow the video to be displayed over digital outputs
                    //(analog video outputs won’t be allowed to pass the content).

                    //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience.
                    // If the output protections are configured too restrictive,
                    // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

                    // For example:
                    //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

                    responseTemplate.LicenseTemplates.Add(licenseTemplate);

                    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
                }

                private static string ConfigureWidevineLicenseTemplate()
                {
                    var template = new WidevineMessage
                    {
                        allowed_track_types = AllowedTrackTypes.SD_HD,
                        content_key_specs = new[]
                        {
                            new ContentKeySpecs
                            {
                                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                        policy_overrides = new
                        {
                            can_play = true,
                            can_persist = true,
                            can_renew = false
                        }
                    };

                    string configuration = JsonConvert.SerializeObject(template);
                    return configuration;
                }

                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    // Get the PlayReady license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

                    // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
                    // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
                    // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption
                    // to append /? KID =< keyId > to the end of the url when creating the manifest.
                    // As a result Widevine license acquisition URL will have KID appended twice,
                    // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

                    Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
                    UriBuilder uriBuilder = new UriBuilder(widevineUrl);
                    uriBuilder.Query = String.Empty;
                    widevineUrl = uriBuilder.Uri;

                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                            {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}

                        };

                    // In this case we only specify Dash streaming protocol in the delivery policy,
                    // All other protocols will be blocked from streaming.
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);


                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);

                }

                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {

                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset.

                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();

                    // Create a 30-day readonly access policy.
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);

                    // Create a locator to the streaming content on an origin.
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));

                    // Create a URL to the manifest file.
                    return originLocator.Path + assetFile.Name;
                }

                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }

                static private byte[] GetRandomBuffer(int length)
                {
                    var returnValue = new byte[length];

                    using (var rng =
                        new System.Security.Cryptography.RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(returnValue);
                    }

                    return returnValue;
                }
            }
        }


## <a name="next-step"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
[CENC とマルチ DRM およびアクセス制御](media-services-cenc-with-multidrm-access-control.md)

[Configure Widevine packaging with AMS (AMS を使用して Widevine パッケージを構成する)](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Azure Media Services での Google Widevine ライセンス配信サービスのお知らせ](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)

