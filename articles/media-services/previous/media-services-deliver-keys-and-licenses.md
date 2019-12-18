---
title: Azure Media Services を使用して DRM ライセンスまたはAES キーを配信する | Microsoft Docs
description: この記事では、Azure Media Services を使用して PlayReady または Widevine のライセンスと AES キーを配信するが、残りの処理 (エンコード、暗号化、ストリーミング) はオンプレミスのサーバーで行う方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 8546c2c1-430b-4254-a88d-4436a83f9192
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 61a5213ea1b801b3ceeb3d9a698a20d479509811
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974531"
---
# <a name="use-media-services-to-deliver-drm-licenses-or-aes-keys"></a>Media Services を使用して DRM ライセンスまたは AES キーを配信する 

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。   > Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)を参照してください。
>

Azure Media Services を使用すると、コンテンツの取り込み、エンコード、保護の追加、およびストリーミングを行うことができます。 詳細については、「[PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-playready-widevine.md)」をご覧ください。 お客様によっては、ライセンスまたはキーの配信のみに Media Services を使用し、エンコード、暗号化、ストリーミングにはオンプレミスのサーバーを使用する場合があります。 この記事では、Media Services を使用して PlayReady または Widevine のライセンスを配信するが、残りの処理はオンプレミスのサーバーで行う方法について説明します。 

## <a name="overview"></a>概要
Media Services は、PlayReady および Widevine の DRM (デジタル著作権管理) ライセンスと AES-128 キーを配信するためのサービスを提供しています。 また、Media Services で提供される API を使用して、DRM で保護されたコンテンツをユーザーが再生するときに DRM ランタイムが適用する権限と制限を構成できます。 ユーザーが保護されたコンテンツを要求すると、プレーヤー アプリケーションが Media Services ライセンス サービスにライセンスを要求します。 ライセンスが承認されると、Media Services ライセンス サービスはプレーヤーにライセンスを発行します。 PlayReady および Widevine のライセンスには、クライアント プレーヤーがコンテンツの暗号化解除とストリーミングに使用できる暗号化解除キーが含まれています。

Media Services では、ライセンスまたはキーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キーの承認ポリシーは自分で構成します。 ポリシーには、1 つまたは複数の制限 (オープンまたはトークン制限) を指定できます。 トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services では、単純 Web トークン (SWT) 形式と JSON Web トークン (JWT) 形式のトークンがサポートされます。

次の図は、AMS を使用して PlayReady または Widevine のライセンスを配信するが、残りの処理はオンプレミスのサーバーで行うための主な手順を示しています。

![PlayReady による保護](./media/media-services-deliver-keys-and-licenses/media-services-diagram1.png)

## <a name="download-sample"></a>サンプルのダウンロード
この記事で説明するサンプルをダウンロードするには、[.NET で Azure Media Services を使用して PlayReady または Widevine のライセンスを配信する方法](https://github.com/Azure/media-services-dotnet-deliver-drm-licenses)に関する記事をご覧ください。

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio プロジェクトの作成と構成

1. 「[.NET を使用した Media Services 開発](media-services-dotnet-how-to-use.md)」の説明に従って、開発環境をセットアップし、app.config ファイルに接続情報を指定します。

2. app.config ファイルで定義されている **appSettings** に次の要素を追加します。

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```
 
## <a name="net-code-example"></a>.NET コード例
次のコード例では、共通のコンテンツ キーを作成し、PlayReady または Widevine のライセンス取得 URL を取得する方法を示します。 オンプレミスのサーバーを構成するには、コンテンツ キー、キー ID、およびライセンス取得 URL が必要です。 オンプレミスのサーバーを構成したら、自分のストリーミング サーバーからストリーミングを実行できます。 暗号化されたストリームは Media Services ライセンス サーバーを参照するため、プレーヤーは Media Services にライセンスを要求します。 トークン認証を選択する場合は、HTTPS を介して送信されたトークンを Media Services ライセンス サーバーが検証します。 トークンが有効な場合は、ライセンス サーバーからプレーヤーにライセンスが配信されます。 次のコード例では、共通のコンテンツ キーを作成し、PlayReady または Widevine のライセンス取得 URL を取得する方法のみを示します。 AES 128 キーを配信する場合は、エンベロープのコンテンツ キーを作成し、キー取得 URL を取得する必要があります。 詳細については、「[AES-128 動的暗号化とキー配信サービスの使用](media-services-protect-with-aes128.md)」をご覧ください。

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
using Newtonsoft.Json;

namespace DeliverDRMLicenses
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

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = true;
            string tokenTemplateString = null;


            IContentKey key = CreateCommonTypeContentKey();

            // Print out the key ID and Key in base64 string format
            Console.WriteLine("Created key {0} with key value {1} ",
                key.Id, System.Convert.ToBase64String(key.GetClearKeyValue()));

            Console.WriteLine("PlayReady License Key delivery URL: {0}",
                key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));

            Console.WriteLine("Widevine License Key delivery URL: {0}",
                key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine));

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}",
                key.AuthorizationPolicyId);
            Console.WriteLine();
            Console.ReadLine();
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {

            // Create ContentKeyAuthorizationPolicy with Open restrictions 
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>
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

            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>
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

            //The PlayReadyLicenseResponseTemplate class represents the template 
            //for the response sent back to the end user. 
            //It contains a field for a custom data string between the license server 
            //and the application (may be useful for custom app logic) 
            //as well as a list of one or more license templates.

            PlayReadyLicenseResponseTemplate responseTemplate =
                new PlayReadyLicenseResponseTemplate();

            // The PlayReadyLicenseTemplate class represents a license template 
            // for creating PlayReady licenses
            // to be returned to the end users. 
            // It contains the data on the content key in the license 
            // and any rights or restrictions to be 
            // enforced by the PlayReady DRM runtime when using the content key.
            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();

            // Configure whether the license is persistent 
            // (saved in persistent storage on the client) 
            // or non-persistent (only held in memory while the player is using the license).  
            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

            // AllowTestDevices controls whether test devices can use the license or not.  
            // If true, the MinimumSecurityLevel property of the license
            // is set to 150.  If false (the default), 
            // the MinimumSecurityLevel property of the license is set to 2000.
            licenseTemplate.AllowTestDevices = true;

            // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
            // It grants the user the ability to play back the content subject to the zero or more restrictions 
            // configured in the license and on the PlayRight itself (for playback specific policy). 
            // Much of the policy on the PlayRight has to do with output restrictions 
            // which control the types of outputs that the content can be played over and 
            // any restrictions that must be put in place when using a given output.
            // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
            //then the DRM runtime will only allow the video to be displayed over digital outputs 
            //(analog video outputs won’t be allowed to pass the content).

            // IMPORTANT: These types of restrictions can be very powerful 
            // but can also affect the consumer experience. 
            // If the output protections are configured too restrictive, 
            // the content might be unplayable on some clients. 
            // For more information, see the PlayReady Compliance Rules document.

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
                                required_output_protection =
                                    new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
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


        static public IContentKey CreateCommonTypeContentKey()
        {
            // Create envelope encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                                    keyId,
                                    contentKey,
                                    "ContentKey",
                                    ContentKeyType.CommonEncryption);

            return key;
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

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
* [PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-playready-widevine.md)
* [AES-128 動的暗号化とキー配信サービスの使用](media-services-protect-with-aes128.md)
