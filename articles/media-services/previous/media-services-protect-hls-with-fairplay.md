---
title: Microsoft PlayReady または Apple FairPlay を使用した HLS コンテンツの保護 - Azure | Microsoft Docs
description: このトピックでは、Azure Media Services を使用して HTTP Live Streaming (HLS) コンテンツを Apple FairPlay で動的に暗号化する方法の概要を説明します。 また、Media Services ライセンス配信サービスを使用して FairPlay ライセンスをクライアントに配信する方法も示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 873bc4ab5e435b91ff4400a39c92db0d0bb9baa8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968767"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Apple FairPlay または Microsoft PlayReady による HLS コンテンツの保護

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。   > Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)を参照してください。
>

Azure Media Services では、次の形式を使用して HTTP ライブ ストリーミング (HLS) コンテンツを動的に暗号化することができます。  

* **AES-128 エンベロープ クリア キー**

    **AES-128 CBC** モードを使用してチャンク全体を暗号化します。 ストリームの復号化は iOS および OS X プレーヤーでネイティブにサポートされています。 詳細については、「[AES-128 動的暗号化とキー配信サービスの使用](media-services-protect-with-aes128.md)」を参照してください。
* **Apple FairPlay**

    **AES-128 CBC** モードを使用して個々のビデオやオーディオのサンプルを暗号化します。 **FairPlay ストリーミング** (FPS) はデバイスのオペレーティング システムに統合されており、iOS および Apple TV でネイティブにサポートされます。 OS X 上の Safari では、Encrypted Media Extensions (EME) インターフェイスのサポートを使用することで FPS が可能になります。
* **Microsoft PlayReady**

次の図は、**HLS + FairPlay または PlayReady 動的暗号化**ワークフローを示しています。

![動的暗号化ワークフローの図](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

この記事では、Media Services を使って HLS コンテンツを Apple FairPlay で動的に暗号化する方法を示します。 また、Media Services ライセンス配信サービスを使用して FairPlay ライセンスをクライアントに配信する方法も示します。

> [!NOTE]
> HLS コンテンツを PlayReady で暗号化する必要がある場合は、共通コンテンツ キーを作成し、それを資産に関連付ける必要があります。 また、[PlayReady の動的共通暗号化の使用](media-services-protect-with-playready-widevine.md)に関する記事の説明に従って、コンテンツ キーの承認ポリシーを構成する必要もあります。
>
>

## <a name="requirements-and-considerations"></a>要件と考慮事項

Media Services を使用して FairPlay で暗号化された HLS を配信し、FairPlay ライセンスを配信するには、次のものが必要です。

  * Azure アカウント。 詳細については、[Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)を参照してください。
  * Media Services アカウント。 作成するには、「[Azure Portal を使用した Azure Media Services アカウントの作成](media-services-portal-create-account.md)」を参照してください。
  * [Apple 開発プログラム](https://developer.apple.com/)にサインアップします。
  * コンテンツ所有者による[デプロイ パッケージ](https://developer.apple.com/contact/fps/)の取得が Apple によって求められます。 Media Services でキー セキュリティ モジュール (KSM) が既に実装されていること、および最終的な FPS パッケージを要求していることを明記してください。 最終的な FPS パッケージには、証明書を生成し、アプリケーション シークレット キー (ASK) を取得する指示が含まれています。 ASK を使用して、FairPlay を構成します。
  * Azure Media Services .NET SDK バージョン **3.6.0** 以降。

Media Services キーの配信側で次の設定が必要です。

  * **アプリ証明書 (AC)** : 秘密キーを含む .pfx ファイル。 このファイルを作成し、パスワードを使用して暗号化します。

       キー配信ポリシーを構成する場合、パスワードと .pfx ファイルは Base64 形式にする必要があります。

      次の手順では、FairPlay 用の .pfx 証明書ファイルを生成する方法について説明します。

    1. https://slproweb.com/products/Win32OpenSSL.html から OpenSSL をインストールします。

        Apple によって提供される FairPlay 証明書とその他のファイルが含まれるフォルダーに移動します。
    2. コマンド ラインから次のコマンドを実行します。 このコマンドにより、.cer ファイルが .pem ファイルに変換されます。

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
    3. コマンド ラインから次のコマンドを実行します。 このコマンドにより、.pem ファイルが秘密キーを含む .pfx ファイルに変換されます。 OpenSSL によって .pfx ファイルのパスワードが求められます。

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
  * **アプリ証明書パスワード**: .pfx ファイルを作成するためのパスワード。
  * **アプリ証明書パスワード ID**: 他の Media Services キーをアップロードするのと同様の方法で、パスワードをアップロードする必要があります。 **ContentKeyType.FairPlayPfxPassword** 列挙値を使用して、Media Services ID を取得します。 これは、キー配信ポリシー オプションの内部で使用する必要がある ID です。
  * **iv**: これは 16 バイトのランダム値です。 資産配信ポリシーの iv と一致している必要があります。 iv を生成し、アセット配信ポリシーとキー配信ポリシー オプションの両方に配置します。
  * **ASK**: このキーは、Apple 開発者ポータルを使用して証明書を生成したときに受け取ります。 開発チームごとに一意の ASK が割り当てられます。 ASK のコピーを保存して、安全な場所に保管してください。 後から ASK を FairPlayAsk として Media services に構成する必要があります。
  * **ASK ID**: この ID は、ASK を Media Services にアップロードすると取得されます。 **ContentKeyType.FairPlayASk** 列挙値を使用して ASK をアップロードする必要があります。 これにより Media Services ID が返されます。これは、キー配信ポリシー オプションを設定するときに使用する ID です。

FPS のクライアント側で、次の設定が必要です。

  * **アプリ証明書 (AC)** : オペレーティング システムが一部のペイロードを暗号化する際に使用する公開キーを含む .cer/.der ファイル。 Media Services で把握しておく必要がある理由は、プレーヤーで必要になるからです。 復号化は、キー配信サービスが対応する秘密キーを使用して行います。

FairPlay で暗号化されたストリームを再生するには、まず実際の ASK を取得してから、実際の証明書を生成する必要があります。 そのプロセスにより、3 つすべてのパートが作成されます。

  * .der ファイル
  * .pfx ファイル
  * .pfx のパスワード

次のクライアントは、**AES-128 CBC** 暗号化で HLS をサポートしています。Safari on OS X、Apple TV、iOS。

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>FairPlay の動的暗号化とライセンス配信サービスの構成
以下では、Media Services ライセンス配信サービスと動的暗号化を使用して、FairPlay で資産を保護するための一般的な手順について説明します。

1. 資産を作成し、その資産にファイルをアップロードします。
2. ファイルが含まれる資産をアダプティブ ビットレート MP4 セットにエンコードします。
3. コンテンツ キーを作成し、それをエンコードした資産に関連付けます。  
4. コンテンツ キーの承認ポリシーを構成します。 次を指定します。

   * 配信方法 (ここでは FairPlay)。
   * FairPlay ポリシー オプションの構成 FairPlay を構成する方法の詳細については、以下のサンプルの **ConfigureFairPlayPolicyOptions()** メソッドをご覧ください。

     > [!NOTE]
     > 通常、証明書と ASK は 1 組だけなので、FairPlay ポリシー オプションを構成する必要があるのは 1 回のみです。
     >
     >
   * 制限 (オープンまたはトークン)。
   * キーをクライアントに配信する方法を定義する、キー配信タイプに固有の情報。
5. 資産配信ポリシーを構成します。 配信ポリシーの構成は次のとおりです。

   * 配信プロトコル (HLS)。
   * 動的暗号化のタイプ (共通 CBC 暗号化)。
   * ライセンス取得 URL。

     > [!NOTE]
     > FairPlay と他のデジタル著作権管理 (DRM) システムで暗号化されたストリームを配信する場合は、別々の配信ポリシーを構成する必要があります。
     >
     > * 1 つは、DASH (Dynamic Adaptive Streaming over HTTP) with CENC (共通暗号化) (PlayReady + WideVine) と Smooth with PlayReady を構成するための IAssetDeliveryPolicy
     > * もう 1 つは、HLS 向けに FairPlay を構成するための IAssetDeliveryPolicy
     >
     >
6. ストリーミング URL を取得するために、OnDemand ロケーターを作成します。

## <a name="use-fairplay-key-delivery-by-player-apps"></a>プレーヤー アプリによる FairPlay キー配信の使用
プレーヤー アプリは、iOS SDK を使用して開発できます。 FairPlay コンテンツを再生できるようにするには、ライセンス交換プロトコルを実装する必要があります。 このプロトコルは、Apple によって指定されていません。 キー配信の要求を送信する方法は、各アプリに従います。 Media Services FairPlay キー配信サービスでは、SPC が www-form-url でエンコードされた投稿メッセージとなることが想定されます。これは次のような形式になります。

    spc=<Base64 encoded SPC>

> [!NOTE]
> Azure Media Player は FairPlay の再生をサポートします。 詳しくは、[Azure Media Player のドキュメント](https://amp.azure.net/libs/amp/latest/docs/index.html)をご覧ください。
>
>

## <a name="streaming-urls"></a>ストリーミング URL
資産を複数の DRM を使用して暗号化した場合、ストリーミング URL で暗号化タグを使用する必要があります: (format='m3u8-aapl', encryption='xxx')。

次の考慮事項が適用されます。

* 指定できるのは、ゼロまたは 1 つの暗号化タイプのみです。
* 1 つの暗号化のみが資産に適用された場合は、暗号化タイプを URL で指定する必要はありません。
* 暗号化タイプでは大文字と小文字が区別されます。
* 指定できる暗号化タイプは次のとおりです。  
  * **cenc**: 共通暗号化 (PlayReady または Widevine)
  * **cbcs-aapl**: FairPlay
  * **cbc**: AES エンベロープ暗号化

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio プロジェクトの作成と構成

1. 「[.NET を使用した Media Services 開発](media-services-dotnet-how-to-use.md)」の説明に従って、開発環境をセットアップし、app.config ファイルに接続情報を指定します。 
2. app.config ファイルで定義されている **appSettings** に次の要素を追加します。

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>例

次の例では、Media Services を使用して、FairPlay で暗号化されたコンテンツを配信する機能を示します。 この機能は、Azure Media Services SDK for .NET Version 3.6.0 で導入されました。 

Program.cs ファイルのコードを、このセクションで示されているコードで上書きします。

>[!NOTE]
>さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可などを使う場合は、同じポリシー ID を使う必要があります (たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー))。 詳細については、[こちらの記事](media-services-dotnet-manage-entities.md#limit-access-policies)を参照してください。

必ず変数を更新して、入力ファイルが置かれているフォルダーをポイントするようにしてください。

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
{
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

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

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

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

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


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

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

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
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

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
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

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
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
```

## <a name="additional-notes"></a>その他のメモ

* Widevine は Google Inc. によって提供されるサービスであり、Google Inc. のサービス使用条件とプライバシー ポリシーが適用されます。

## <a name="next-steps-media-services-learning-paths"></a>次のステップ:Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
