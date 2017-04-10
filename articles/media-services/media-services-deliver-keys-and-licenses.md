---
title: "Azure Media Services を使用して DRM ライセンスまたはAES キーを配信する"
description: "この記事では、Azure Media Services (AMS) を使用して PlayReady または Widevine のライセンスと、AES キーを配信するものの、オンプレミスのサーバーで残り (エンコード、暗号化、ストリーミング) を処理する方法について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 8546c2c1-430b-4254-a88d-4436a83f9192
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 02bf743d310519477bb87a2930a2afe687c62c4e


---
# <a name="use-azure-media-services-to-deliver-drm-licenses-or-aes-keys"></a>Azure Media Services を使用して DRM ライセンスまたはAES キーを配信する
Azure Media Services (AMS) を使用すると、コンテンツの取り込みおよびエンコードを行い、コンテンツ保護を追加して、コンテンツのストリーミングを行うことができます (詳細については、 [この](media-services-protect-with-drm.md) 記事をご覧ください)。 ただし、AMS を使用してライセンスまたはキーの配信のみを行い、オンプレミスのサーバーを使用してエンコード、暗号化、およびストリーミングを行うユーザーがいます。 この記事では、AMS を使用して PlayReady または Widevine のライセンスを配信するものの、オンプレミスのサーバーで残りを処理する方法について説明します。 

## <a name="overview"></a>Overview
Media Services は、PlayReady および Widevine DRM のライセンスと AES-128 キーを配信するためのサービスを提供しています。 また、Media Services で提供される API を使用して、DRM 保護されたコンテンツをユーザーが再生するときに DRM ランタイムが適用する権限と制限を構成できます。 ユーザーが保護されたコンテンツを要求すると、プレーヤー アプリケーションが AMS ライセンス サービスにライセンスを要求します。 AMS ライセンス サービスは、(プレーヤーが承認されていれば) ライセンスをプレーヤーに発行します。 PlayReady および Widevine のライセンスには、クライアント プレーヤーがコンテンツの暗号化解除とストリーミングに使用できる暗号化解除キーが含まれています。

Media Services では、ライセンスまたはキーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーを構成することで、ポリシーには、1 つまたは複数の制限 (オープンまたはトークン制限) を指定できます。 トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services では、Simple Web Tokens (SWT) 形式と JSON Web Token (JWT) 形式のトークンがサポートされます。

次の図は、AMS を使用して PlayReady または Widevine のライセンスを配信するものの、オンプレミスのサーバーで残りを処理するために必要な主要手順を示しています。

![PlayReady による保護](./media/media-services-deliver-keys-and-licenses/media-services-diagram1.png)

## <a name="download-sample"></a>サンプルのダウンロード
この記事で説明されているサンプルは、 [こちら](https://github.com/Azure/media-services-dotnet-deliver-drm-licenses)からダウンロードできます。

## <a name="net-code-example"></a>.NET コード例
このトピックのコード例では、共通のコンテンツ キーを作成し、PlayReady または Widevine のライセンス取得 URL を取得する方法を示します。 **コンテンツ キー**、**キー ID**、および**ライセンス取得 URL**の情報を AMS から取得し、オンプレミスのサーバーを構成する必要があります。 オンプレミスのサーバーを構成すると、自分のストリーミング サーバーからストリーミングを行うことができます。 暗号化されたストリームは AMS ライセンス サーバーを参照するため、プレーヤーは AMS にライセンスを要求します。 トークン認証を選択する場合は、HTTPS を介して送信されたトークンを AMS ライセンス サーバーが検証し、(有効な場合は) プレーヤーにライセンスを配信します。 (コード例では、共通のコンテンツ キーを作成し、PlayReady または Widevine のライセンス取得 URL を取得する方法のみを示します。 AES 128 キーを配信する場合は、エンベロープのコンテンツ キーを作成し、キー取得 URL を取得する必要があります。[この](media-services-protect-with-aes128.md)記事ではその方法を示しています)。

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


    namespace DeliverDRMLicenses
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

            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

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
                // It grants the user the ability to playback the content subject to the zero or more restrictions 
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


## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
[PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-drm.md)

[AES-128 動的暗号化とキー配信サービスの使用](media-services-protect-with-aes128.md)

[パートナーを使用して Azure Media Services に Widevine ライセンスを配信する](media-services-licenses-partner-integration.md)




<!--HONumber=Nov16_HO3-->


