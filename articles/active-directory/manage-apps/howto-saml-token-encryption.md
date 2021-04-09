---
title: Azure Active Directory での SAML トークン暗号化
description: Azure Active Directory の SAML トークン暗号化を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32a6034f407bd81e5d2d2ab8227d08a67f034d4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258644"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Azure AD SAML トークン暗号化の構成方法

> [!NOTE]
> トークン暗号化は、Azure Active Directory (Azure AD) のプレミアム機能です。 Azure AD のエディション、機能、および価格について詳しくは、[Azure AD の価格](https://azure.microsoft.com/pricing/details/active-directory/)に関するページをご覧ください。

SAML トークン暗号化を使用すると、それをサポートしているアプリケーションで、暗号化された SAML アサーションを使用できるようになります。 アプリケーションに対してこれが構成されている場合、Azure AD は、Azure AD に格納されている証明書から取得した公開キーを使用して、そのアプリケーションから出力された SAML アサーションを暗号化します。 アプリケーションでは、対応する秘密キーを使用してトークンを復号化する必要があります。これにより、現在サインインしているユーザーの認証の証拠として、そのトークンを使用できるようになります。

Azure AD とアプリケーションの間で SAML アサーションを暗号化すると、トークンの内容がインターセプトされるのをより強力に防護して、個人や会社のデータが侵害されるのを防ぐことができます。

トークン暗号化を使用しなかった場合でも、Azure AD の SAML トークンがネットワーク上でクリア テキストのまま渡されることはありません。 Azure AD では、トークンの要求/応答の交換が、暗号化された HTTPS/TLS チャネル経由で行われるようにする必要があります。これにより、IDP、ブラウザー、およびアプリケーション間の通信が、暗号化されたリンク経由で行われるようになります。 お客様の環境でトークン暗号化を使用するメリットを、追加の証明書の管理で生じるオーバーヘッドと比較して検討してください。   

トークン暗号化を構成するには、公開キーを含んだ X.509 証明書ファイルを、アプリケーションを表す Azure AD アプリケーション オブジェクトにアップロードする必要があります。 X.509 証明書を取得する方法としては、アプリケーション自体からダウンロードする方法と、アプリケーション ベンダーから取得する方法があります (アプリケーション ベンダーから暗号化キーが提供される場合)。アプリケーションでユーザーが秘密キーを指定する必要がある場合は、暗号化ツールと、アプリケーションのキー ストアにアップロードされた秘密キー部分、および Azure AD にアップロードされた対応する公開キー証明書を使用して、秘密キーを作成できます。

Azure AD では、SAML アサーション データの暗号化に AES-256 が使用されます。

## <a name="configure-saml-token-encryption"></a>SAML トークン暗号化の構成

SAML トークン暗号化を構成するには、次の手順に従います。

1. アプリケーションで構成されている秘密キーに一致する公開キー証明書を取得します。

    暗号化に使用する非対称キー ペアを作成します。 なお、暗号化に使用する公開キーがアプリケーションで提供される場合は、アプリケーションの指示に従って X.509 証明書をダウンロードします。

    公開キーは、.cer 形式の X.509 証明書ファイルに格納する必要があります。

    インスタンス用に作成したキーがアプリケーションで使用される場合は、アプリケーションの指示に従って、Azure AD テナントからのトークンの復号化に使用される秘密キーをインストールしてください。

1. Azure AD のアプリケーション構成に証明書を追加します。

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Azure portal でトークン暗号化を構成するには

Azure portal 内で、アプリケーション構成にパブリック証明書を追加できます。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

1. **[Azure Active Directory] > [エンタープライズ アプリケーション]** ブレードに移動し、トークン暗号化を構成するアプリケーションを選択します。

1. アプリケーションのページで、**[トークン暗号化]** を選択します。

    ![Azure portal のトークン暗号化オプション](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > **[トークン暗号化]** オプションは、Azure portal の **[エンタープライズ アプリケーション]** ブレードから (アプリケーション ギャラリーまたはギャラリー以外のアプリから) 設定された SAML アプリケーションに対してのみ使用できます。 その他のアプリケーションについては、このメニュー オプションは無効になります。 Azure portal の **[アプリの登録]** エクスペリエンスを通じて登録されたアプリケーションについては、アプリケーション マニフェストを使用して (Microsoft Graph または PowerShell を通じて)、SAML トークン用の暗号化を構成できます。

1. **[トークン暗号化]** ページで **[証明書のインポート]** を選択して、公開 X.509 証明書を含んだ .cer ファイルをインポートします。

    ![X.509 証明書を含んだ .cer ファイルのインポート](./media/howto-saml-token-encryption/import-certificate-small.png)

1. 証明書がインポートされ、アプリケーション側で使用する秘密キーが構成されたら、サムプリント状態の横にある **[...]** を選択して暗号化をアクティブ化し、ドロップダウン メニューのオプションから **[トークン暗号化証明書のアクティブ化]** を選択します。

1. **[はい]** を選択して、トークン暗号化証明書のアクティブ化を確認します。

1. アプリケーション用に出力された SAML アサーションが暗号化されたことを確認します。

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Azure portal でトークン暗号化を非アクティブ化するには

1. Azure portal で、**[Azure Active Directory] > [エンタープライズ アプリケーション]** に移動し、SAML トークン暗号化が有効化されたアプリケーションを選択します。

1. アプリケーションのページで、**[トークン暗号化]** を選択し、証明書を検索した後、**[...]** オプションを選択してドロップダウン メニューを表示します。

1. **[トークン暗号化証明書の非アクティブ化]** を選択します。

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Graph API、PowerShell、またはアプリ マニフェストを使用して SAML トークン暗号化を構成する

暗号化証明書は、`encrypt` 使用タグを使用して Azure AD 内のアプリケーション オブジェクトに格納されます。 暗号化証明書は複数構成できます。トークンの暗号化用にアクティブ化された証明書は、`tokenEncryptionKeyID` 属性によって識別されます。

Microsoft Graph API または PowerShell を使用してトークン暗号化を構成するには、アプリケーションのオブジェクト ID が必要になります。 この値はプログラムによって検索することもでき、Azure portal でアプリケーションの **[プロパティ]** に移動して、**[オブジェクト ID]** の値を見て確認することもできます。

Graph、PowerShell、またはアプリケーション マニフェストを使用して keyCredential を構成する場合は、keyId に使用する GUID を生成する必要があります。

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Microsoft Graph を使用してトークン暗号化を構成する

1. 暗号化用の X.509 証明書を使用して、アプリケーションの `keyCredentials` を更新します。 次の例は、その方法を示したものです。

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. トークンの暗号化用にアクティブ化された暗号化証明書を特定します。 次の例は、その方法を示したものです。

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>PowerShell を使用してトークン暗号化を構成する

1. 最新の Azure AD PowerShell モジュールを使用して、テナントに接続します。

1. **[Set-AzureApplication](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** コマンドを使用して、トークン暗号化設定を設定します。

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. 次のコマンドを使用して、トークン暗号化設定を読み取ります。

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>アプリケーション マニフェストを使用してトークン暗号化を構成するには

1. Azure portal から、**[Azure Active Directory] > [アプリの登録]** に移動します。

1. ドロップダウン リストから **[すべてのアプリ]** 選択してすべてのアプリを表示し、構成するエンタープライズ アプリケーションを選択します。

1. アプリケーションのページで、**[マニフェスト]** を選択して [アプリケーション マニフェスト](../develop/reference-app-manifest.md)を編集します。

1. `tokenEncryptionKeyId` 属性の値を設定します。

    次の例は、2 つの暗号化証明書を使用して構成されたアプリケーション マニフェストを示したものです。2 つ目の証明書は、tokenEnryptionKeyId を使用してアクティブな証明書として選択されています。

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>次のステップ

* [Azure AD で SAML プロトコルがどのように使用されるか](../develop/active-directory-saml-protocol-reference.md)を学習する
* [Azure AD 内の SAML トークン](../develop/reference-saml-tokens.md)の形式、セキュリティ特性、および内容について学習する