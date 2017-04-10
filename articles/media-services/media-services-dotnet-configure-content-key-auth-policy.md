---
title: "Media Services .NET SDK を使用したコンテンツ キー承認ポリシーの構成 | Microsoft Docs"
description: "Media Services .NET SDK を利用し、コンテンツ キー承認ポリシーを構成する方法について説明します。"
services: media-services
documentationcenter: 
author: Mingfeiy
manager: erikre
editor: 
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;mingfeiy
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: 3dcd45307716b7343fbac00e083e8f26c9eb967f
ms.lasthandoff: 01/11/2017


---
# <a name="dynamic-encryption-configure-content-key-authorization-policy"></a>動的暗号化: コンテンツ キー承認ポリシーを構成する
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>概要
Microsoft Azure Media Services を使用すると、Advanced Encryption Standard (AES) (128 ビット暗号化キーを使用) または [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/)で保護された MPEG DASH、Smooth Streaming、HTTP ライブ ストリーミング (HLS) のストリームを配信できます。 AMS では、Widevine DRM で暗号化された DASH ストリームを配信することもできます。 PlayReady と Widevine はいずれも共通暗号化 (ISO/IEC 23001-7 CENC) 仕様に従って暗号化されます。

Media Services では、クライアントが暗号化されたコンテンツを再生するために AES キーまたは PlayReady/Widevine ライセンスを取得できる **キー/ライセンス配信サービス** も提供します。

Media Services で資産を暗号化する場合は、[こちら](media-services-dotnet-create-contentkey.md)の説明に従って暗号化キー (**CommonEncryption** か **EnvelopeEncryption**) を資産に関連付ける必要があります。また、このトピックでの説明に従って、キーの承認ポリシーを構成する必要があります。

プレーヤーがストリームを要求すると、Media Services は指定されたキーを使用して、AES または DRM 暗号化によってコンテンツを動的に暗号化します。 ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。 ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定した承認ポリシーを評価します。

Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (**オープン**または**トークン**制限) を指定できます。 トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services では、**Simple Web Tokens** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 形式と **JSON Web Token** ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) 形式のトークンがサポートされます。

Media Services では、Secure Token Services は提供されません。 トークンを発行するには、カスタム STS を作成するか、Microsoft Azure ACS を活用できます。 STS は、指定されたキーで署名されたトークンを作成し、トークン制限構成で指定した要求を発行するよう構成する必要があります (この記事の説明を参照)。 Media Services のキー配信サービスは、トークンが有効で、トークン内の要求がコンテンツ キー向けに構成された要求と一致する場合、暗号化キーをクライアントに返します。

詳細については、次をご覧ください。

[JWT トークンの承認](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Azure Media Services OWIN MVC ベースのアプリを Azure Active Directory と統合し、JWT 要求に基づいてコンテンツ キーの配信を制限する](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[Azure ACS を使用してトークンを発行する](http://mingfeiy.com/acs-with-key-services)。

### <a name="some-considerations-apply"></a>いくつかの考慮事項が適用されます。
* AMS アカウントの作成時に、**既定**のストリーミング エンドポイントが自分のアカウントに追加され、**停止**状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、ストリーミング エンドポイントが**実行中**状態である必要があります。 
* 資産には、一連のアダプティブ ビットレート MP4 または アダプティブ ビットレート Smooth Streaming ファイルが含まれている必要があります。 詳細については、「 [資産をエンコードする](media-services-encode-asset.md)」をご覧ください。
* **AssetCreationOptions.StorageEncrypted** オプションを使用して、資産をアップロードしてエンコードします。
* 複数のコンテンツ キーで同じポリシー構成を必要とする場合は、1 つの承認ポリシーを作成して、複数のコンテンツ キーに利用することを強くお勧めします。
* キー配信サービスでは、ContentKeyAuthorizationPolicy とそれに関連するオブジェクト (ポリシーのオプションと制限) を 15 分間キャッシュします。  ContentKeyAuthorizationPolicy を作成して、"Token" 制限を使用するように指定した場合に、"Token" 制限をテストしてから、ポリシーを "Open" 制限に更新すると、ポリシーが "Open" バージョンのポリシーに切り替わるまで、約 15 分かかります。
* 資産の配信ポリシーを追加または更新する場合は、既存のロケーターを削除し (存在する場合)、新しいロケーターを作成する必要があります。
* 現時点では、プログレッシブ ダウンロードは暗号化できません。

## <a name="aes-128-dynamic-encryption"></a>AES-128 動的暗号化
### <a name="open-restriction"></a>オープン制限
オープン制限とは、キーを要求するすべてのユーザーに、システムがキーを提供することを意味します。 この制限は、テストに便利です。

次の例では、オープン承認ポリシーを作成し、それをコンテンツ キーに追加します。

    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }


### <a name="token-restriction"></a>トークン制限
このセクションでは、コンテンツ キー承認ポリシーを作成し、それをコンテンツ キーに関連付ける方法について説明します。 承認ポリシーには、ユーザーがキーを受け取ることを承認されているかどうかを判断するために、承認要求が満たす必要がある内容について記載されています (トークンの署名に使用されたキーが「検証キー」リストに含まれているなど)。

トークン制限オプションを構成するには、XML を使用してトークンの承認要件を記述する必要があります。 トークン制限の構成 XML は、次の XML スキーマに準拠する必要があります。

#### <a name="a-idschemaatoken-restriction-schema"></a><a id="schema"></a>トークン制限スキーマ
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>

**トークン**制限ポリシーを構成する際は、プライマリ**検証キー**、**発行者**、**対象ユーザー**の各パラメーターを指定する必要があります。 **プライマリ検証キー**には、トークンの署名に使用されたキーが含まれ、**発行者**は、トークンを発行するセキュリティ トークン サービスです。 **対象ユーザー** (**スコープ**とも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。 Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。 

**Media Services SDK for .NET** を使用する場合、**TokenRestrictionTemplate** クラスを使用して制限トークンを生成できます。
次の例では、トークン制限を含む承認ポリシーを作成します。 この例では、クライアントが署名キー (VerificationKey)、トークン発行者、必要な要求を含むトークンを提示する必要があります。

    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

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

#### <a name="a-idtestatest-token"></a><a id="test"></a>テスト トークン
キー承認ポリシーに使用されたトークン制限に基づいてテスト トークンを取得するには、次の処理を行います。

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();


## <a name="playready-dynamic-encryption"></a>PlayReady 動的暗号化
Media Services により、ユーザーが保護されたコンテンツを再生する際に、PlayReady DRM ランタイムに適用される権限と制限を構成できます。 

PlayReady を使用してコンテンツを保護する場合、承認ポリシーの指定の&1; つとして、 [PlayReady ライセンス テンプレート](media-services-playready-license-template-overview.md)を定義する XML 文字列を指定する必要があります。 Media Services SDK for .NET では、**PlayReadyLicenseResponseTemplate** クラスと **PlayReadyLicenseTemplate** クラスを利用して、PlayReady ライセンス テンプレートを定義できます。

[このトピック](media-services-protect-with-drm.md)では、**PlayReady** と **Widevine** を使用してコンテンツを暗号化する方法を説明しています。

### <a name="open-restriction"></a>オープン制限
オープン制限とは、キーを要求するすべてのユーザーに、システムがキーを提供することを意味します。 この制限は、テストに便利です。

次の例では、オープン承認ポリシーを作成し、それをコンテンツ キーに追加します。

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

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }

### <a name="token-restriction"></a>トークン制限
トークン制限オプションを構成するには、XML を使用してトークンの承認要件を記述する必要があります。 トークン制限の構成 XML は、 [この](#schema) セクションで表示される XML スキーマに一致する必要があります。

    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

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


キー承認ポリシーに使用されたトークン制限に基づいてテスト トークンを取得するには、 [こちら](#test) のセクションをご覧ください。 

## <a name="a-idtypesatypes-used-when-defining-contentkeyauthorizationpolicy"></a><a id="types"></a>ContentKeyAuthorizationPolicy を定義するときに使用される種類
### <a name="a-idcontentkeyrestrictiontypeacontentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

### <a name="a-idcontentkeydeliverytypeacontentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

### <a name="a-idtokentypeatokentype"></a><a id="TokenType"></a>TokenType
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>次のステップ
これで、コンテンツ キーの承認ポリシーの構成が完了しました。次は、[資産の配信ポリシーの構成方法](media-services-dotnet-configure-asset-delivery-policy.md)に関するページをご覧ください。


