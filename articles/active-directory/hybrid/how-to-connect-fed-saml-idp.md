---
title: Azure AD Connect:シングル サインオンに SAML 2.0 ID プロバイダーを使用する - Azure
description: このドキュメントでは、シングル サインオンに SAML 2.0 準拠の IDP を使用する方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b26c24149d422021dcb86f75c915ade89cbccdec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589877"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>シングル サインオンに SAML 2.0 ID プロバイダー (IdP) を使用する

このドキュメントでは、SAML 2.0 に準拠している SP-Lite プロファイル ベースの ID プロバイダーを、優先セキュリティ トークン サービス (STS)/ID プロバイダーとして使用する方法について説明します。 このシナリオは、SAML 2.0 を使用してアクセスできるユーザー ディレクトリとパスワード ストアがオンプレミスに既にある場合に役立ちます。 この既存のユーザー ディレクトリを使用して、Microsoft 365 やその他の Azure AD で保護されたリソースにサインオンできます。 SAML 2.0 SP-Lite プロファイルは、サインオンおよび属性交換フレームワークを提供するために広く使用されている Security Assertion Markup Language (SAML) フェデレーション ID 標準に基づいています。

>[!NOTE]
>Azure AD との使用テストが行われているサード パーティの IDP の一覧については、「[Azure AD のフェデレーション互換性リスト](how-to-connect-fed-compatibility.md)」を参照してください。

Microsoft では、適切に構成された SAML 2.0 プロファイル ベースの IDP と Microsoft クラウド サービス (Microsoft 365 など) の統合として、このサインオン エクスペリエンスをサポートします。 SAML 2.0 ID プロバイダーはサード パーティ製品であるため、Microsoft では、それらのデプロイ、構成、トラブルシューティングに関するベスト プラクティスをサポートしません。 適切に構成したら、後ほど詳述する Microsoft 接続アナライザー ツールを使用して、SAML 2.0 ID プロバイダーとの統合が適切な構成かどうかをテストできます。 SAML 2.0 SP-Lite プロファイル ベースの ID プロバイダーの詳細については、提供元の組織にお問い合わせください。

> [!IMPORTANT]
> SAML 2.0 ID プロバイダーを使用するこのサインオン シナリオで利用できるクライアントは、以下に限定されています。
> 
> - Outlook Web Access や SharePoint Online などの Web ベースのクライアント
> - 基本認証を使用し、IMAP、POP、Active Sync、MAPI などのサポートされている Exchange アクセス方法をサポートする以下の電子メール リッチ クライアント (拡張クライアント プロトコル エンドポイントのデプロイが必要です)。
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016、Apple iPhone (複数の iOS のバージョン)
>     - 多様な Google Android デバイス
>     - Windows Phone 7、Windows Phone 7.8、および Windows Phone 8.0
>     - Windows 8 メール クライアントと Windows 8.1 メール クライアント
>     - Windows 10 メール クライアント

他のすべてのクライアントは、SAML 2.0 ID プロバイダーを使用するこのサインオン シナリオでは利用できません。 たとえば、Lync 2010 デスクトップ クライアントは、シングル サインオン用に SAML 2.0 ID プロバイダーが構成されているサービスにサインインすることはできません。

## <a name="azure-ad-saml-20-protocol-requirements"></a>Azure AD SAML 2.0 プロトコル要件
このドキュメントには、1 つ以上の Microsoft クラウド サービス (Microsoft 365 など) にサインオンできるように、SAML 2.0 ID プロバイダーが Azure AD とフェデレーションするために実装する必要があるプロトコルとメッセージ形式に関する詳細な要件が含まれています。 このシナリオで使用される Microsoft クラウド サービスの SAML 2.0 証明書利用者 (SP-STS) は Azure AD です。

SAML 2.0 ID プロバイダーの出力メッセージが、用意されているサンプル トレースのメッセージとできるだけ類似していることを確認することをお勧めします。 また、可能であれば、提供されている Azure AD メタデータの特定の属性値を使用してください。 出力メッセージに問題がなければ、後で説明する Microsoft 接続アナライザーでテストできます。

Azure AD メタデータは、この URL [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml) からダウンロードできます。
Microsoft 365 の中国固有のインスタンスを使用している中国国内のお客様は、次のフェデレーション エンドポイントを使用する必要があります: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml)。

## <a name="saml-protocol-requirements"></a>SAML プロトコル要件
このセクションでは、メッセージを正しく書式設定するために、要求と応答のメッセージのペアをまとめる方法を詳しく説明します。

Azure AD は、以下に示すいくつかの要件に従って、SAML 2.0 SP Lite プロファイルを使用する ID プロバイダーと連携するように構成できます。 サンプルの SAML 要求メッセージと応答メッセージを自動テストと手動テストで使用することで、Azure AD との相互運用性を実現することができます。

## <a name="signature-block-requirements"></a>署名ブロック要件
SAML 応答メッセージでは、署名ノードにメッセージ自体のデジタル署名に関する情報が含まれています。 署名ブロックには次の要件があります。

1. アサーション ノード自体を署名する必要があります。
2. DigestMethod として RSA-sha1 アルゴリズムを使用する必要があります。 その他のデジタル署名アルゴリズムは容認されません。
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3. XML ドキュメントにも署名できます。 
4. Transform アルゴリズムは、次の例の値と一致する必要があります。`<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9. SignatureMethod アルゴリズムは、次の例と一致する必要があります。`<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>サポートされるバインディング
バインディングは、トランスポートに関連する必須の通信パラメーターです。 バインディングには次の要件が適用されます。

1. 要求されるトランスポートは HTTPS です。
2. Azure AD は、サインイン中のトークンの送信で HTTP POST を必要とします。
3. Azure AD は、ID プロバイダーへの認証要求で HTTP POST を使用し、ID プロバイダーへのサインアウト メッセージで REDIRECT を必要とします。

## <a name="required-attributes"></a>必須属性
次の表は、SAML 2.0 メッセージの特定の属性の要件を示します。
 
|属性|説明|
| ----- | ----- |
|NameID|このアサーションの値は、Azure AD ユーザーの ImmutableID と同じである必要があります。 最大 64 文字の英数字にすることができます。 HTML で安全に使用できない文字はエンコードする必要があります。たとえば、"+" 文字は ".2B" と表示されます。|
|IDPEmail|ユーザー プリンシパル名 (UPN) は、SAML 応答内で IDPEmail という名前の要素として示されます。これは、Azure AD/Microsoft 365 でのユーザーの UserPrincipalName (UPN) です。 UPN は電子メール アドレス形式です。 Windows Microsoft 365 (Azure Active Directory) での UPN 値です。|
|発行者|ID プロバイダーの URI を指定する必要があります。 サンプル メッセージの発行者を再利用しないでください。 Azure AD テナントに複数のトップ レベル ドメインがある場合、発行者は、ドメインごとに構成される指定の URI 設定と一致する必要があります。|

>[!IMPORTANT]
>現在 Azure AD では、次の SAML 2.0 のNameID フォーマット URI をサポートしています: urn:oasis:names:tc:SAML:2.0:nameid-format:persistent。

## <a name="sample-saml-request-and-response-messages"></a>サンプルの SAML 要求メッセージと応答メッセージ
サインオン メッセージ交換では、要求メッセージと応答メッセージのペアが示されます。
Azure AD からサンプル SAML 2.0 ID プロバイダーに送信されるサンプル要求メッセージを次に示します。 このサンプル SAML 2.0 ID プロバイダーは、SAML-P プロトコルを使用するように構成された Active Directory フェデレーション サービス (AD FS) です。 その他の SAML 2.0 ID プロバイダーとの相互運用性のテストも完了しています。

```xml
  <samlp:AuthnRequest 
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" 
    xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" 
    ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" 
    IssueInstant="2014-01-30T16:18:35Z" 
    Version="2.0" 
    AssertionConsumerServiceIndex="0" >
        <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
        <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
  </samlp:AuthnRequest>
```

サンプル SAML 2.0 準拠 ID プロバイダーから Azure AD/Microsoft 365 に送信されるサンプル応答メッセージを次に示します。

```xml
    <samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>
```

## <a name="configure-your-saml-20-compliant-identity-provider"></a>SAML 2.0 に準拠している ID プロバイダーを構成する
このセクションには、SAML 2.0 プロトコルを使用して、1 つ以上の Microsoft クラウド サービス (Microsoft 365 など) にシングル サインオンでアクセスできるようにするために、Azure AD とフェデレーションするように SAML 2.0 ID プロバイダーを構成する方法に関するガイドラインが含まれています。 このシナリオで使用される Microsoft クラウド サービスの SAML 2.0 証明書利用者は Azure AD です。

## <a name="add-azure-ad-metadata"></a>Azure AD メタデータを追加する
SAML 2.0 ID プロバイダーは、Azure AD 証明書利用者に関する情報を使用する必要があります。 Azure AD は、メタデータを https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml で発行します。

SAML 2.0 ID プロバイダーを構成するときは、常に最新の Azure AD メタデータをインポートすることをお勧めします。

>[!NOTE]
>Azure AD が ID プロバイダーからメタデータを読み取ることはありません。

## <a name="add-azure-ad-as-a-relying-party"></a>Azure AD を証明書利用者として追加する
SAML 2.0 ID プロバイダーと Azure AD 間の通信を有効にする必要があります。 この構成は ID プロバイダーに依存するため、関連するドキュメントを参照する必要があります。 証明書利用者の ID は、通常は、Azure AD メタデータの entityID と同じ値に設定します。

>[!NOTE]
>SAML 2.0 ID プロバイダーのサーバーのクロックが正しいタイム ソースに同期されていることを確認してください。 不正確なクロック時間は、フェデレーション ログインの失敗の原因になる可能性があります。

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>SAML 2.0 ID プロバイダーでサインオンするための Windows PowerShell をインストールする
Azure AD サインオンで使用するように SAML 2.0 ID プロバイダーを構成したら、次の手順は、Azure Active Directory Module for Windows PowerShell をダウンロードしてインストールすることです。 インストールしたら、これらのコマンドレットを使用して、Azure AD ドメインをフェデレーション ドメインとして構成します。

Azure Active Directory Module for Windows PowerShell は、組織のデータを Azure AD で管理するためのダウンロードです。 このモジュールは、Windows PowerShell に一連のコマンドレットをインストールします。これらのコマンドレットを実行して Azure AD へのシングル サインオン アクセスを設定し、サブスクライブしているすべてのクラウド サービスにアクセスします。 コマンドレットをダウンロードしてインストールする方法については、[/previous-versions/azure/jj151815(v=azure.100)](/previous-versions/azure/jj151815(v=azure.100)) をご覧ください

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>SAML ID プロバイダーと Azure AD との間に信頼を確立する
Azure AD ドメインのフェデレーションを構成する前に、カスタム ドメインを構成する必要があります。 Microsoft によって提供されている既定のドメインをフェデレーションすることはできません。 Microsoft の既定のドメインは、"onmicrosoft.com" で終わります。
Windows PowerShell コマンド ライン インターフェイスで一連のコマンドレットを実行して、シングル サインオン用のドメインを追加するか変換します。

SAML 2.0 ID プロバイダーを使用してフェデレーションする各 Azure Active Directory ドメインは、シングル サインオン ドメインとして追加するか、標準ドメインからシングル サインオン ドメインに変換される必要があります。 ドメインを追加または変換すると、SAML 2.0 ID プロバイダーと Azure AD との間に信頼が設定されます。

次の手順は、既存の標準ドメインを SAML 2.0 SP-Lite を使用するフェデレーション ドメインに変換する方法を説明しています。 

>[!NOTE]
>この手順の実行後、ドメインの停止が発生する可能性があります。これは、最大 2 時間、ユーザーに影響を与えます。

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Azure AD ディレクトリのドメインを フェデレーションに構成する


1. 次のテナント管理者として Azure AD ディレクトリに接続します。

  ```powershell
  Connect-MsolService
  ```
  
2. SAML 2.0 とのフェデレーションで使用する目的の Microsoft 365 ドメインを構成します。

  ```powershell
  $dom = "contoso.com" 
  $BrandName - "Sample SAML 2.0 IDP" 
  $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" 
  $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" 
  $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" 
  $MyURI = "urn:uri:MySamlp2IDP" 
  $MySigningCert = "MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" 
  $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" 
  $Protocol = "SAMLP" 
  Set-MsolDomainAuthentication `
    -DomainName $dom `
    -FederationBrandName $BrandName `
    -Authentication Federated `
    -PassiveLogOnUri $LogOnUrl `
    -ActiveLogOnUri $ecpUrl `
    -SigningCertificate $MySigningCert `
    -IssuerUri $MyURI `
    -LogOffUri $LogOffUrl `
    -PreferredAuthenticationProtocol $Protocol
  ``` 

3.  IDP メタデータ ファイルから署名証明書の base64 でエンコードされた文字列を取得できます。 この場所の例は用意されていますが、実装に基づいて若干異なる場合があります。

  ```xml
  <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <KeyDescriptor use="signing">
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
       <X509Data>
         <X509Certificate> MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate>
        </X509Data>
      </KeyInfo>
    </KeyDescriptor>
  </IDPSSODescriptor>
  ``` 

"Set-MsolDomainAuthentication" の詳細については、[/previous-versions/azure/dn194112(v=azure.100)](/previous-versions/azure/dn194112(v=azure.100)) をご覧ください。

>[!NOTE]
>`$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` は、ID プロバイダーに対して ECP 拡張機能を設定する場合にのみ使用する必要があります。 Outlook Web Application (OWA) を除く Exchange Online クライアントは、POST ベースのアクティブなエンドポイントを活用します。 SAML 2.0 STS でアクティブなエンドポイントの Shibboleth の ECP 実装に似たアクティブなエンドポイントを実装すると、これらのリッチ クライアントが Exchange Online サービスと対話することが可能になる場合があります。

フェデレーションが構成された後で、"非フェデレーション" ("マネージ") に切り替えることもできます。ただし、この変更は、完了までに最大 2 時間かかり、クラウドベースのサインイン用の新しいランダム パスワードを各ユーザーに割り当てる必要があります。 "マネージド" への切り替えは、一部のシナリオで設定のエラーをリセットするために必要になる場合があります。 ドメインの変換の詳細については、[/previous-versions/azure/dn194122(v=azure.100)](/previous-versions/azure/dn194122(v=azure.100)) をご覧ください。

## <a name="provision-user-principals-to-azure-ad--microsoft-365"></a>ユーザー プリンシパルを Azure AD/Microsoft 365 にプロビジョニングする
Microsoft 365 に対してユーザーを認証するには、SAML 2.0 要求のアサーションに対応するユーザー プリンシパルを使用して Azure AD をプロビジョニングしておく必要があります。 これらのユーザー プリンシパルが Azure AD で事前に認識されていない場合、フェデレーション サインインで使用することはできません。 ユーザー プリンシパルは、Azure AD Connect または Windows PowerShell を使用してプロビジョニングできます。

Azure AD Connect を使用して、オンプレミスの Active Directory から Azure AD ディレクトリ内のドメインにプリンシパルをプロビジョニングできます。 詳細については、「[オンプレミスのディレクトリと Azure Active Directory の統合](whatis-hybrid-identity.md)」を参照してください。

Windows PowerShell は、新しいユーザーの Azure AD への追加を自動化し、オンプレミスのディレクトリの変更を同期するために使用することもできます。 Windows PowerShell コマンドレットを使用するには、[Azure Active Directory モジュール](/powershell/azure/active-directory/install-adv2)をダウンロードする必要があります。

次の手順は、Azure AD に 1 人のユーザーを追加する方法を示しています。


1. 次のテナント管理者として Azure AD ディレクトリに接続します。Connect-MsolService。
2. 新しいユーザー プリンシパルを作成します。

    ```powershell
    New-MsolUser `
      -UserPrincipalName elwoodf1@contoso.com `
      -ImmutableId ABCDEFG1234567890 `
      -DisplayName "Elwood Folk" `
      -FirstName Elwood `
      -LastName Folk `
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" `
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" `
      -UsageLocation "US" 
    ```

"New-MsolUser" チェックアウトの詳細については、[/previous-versions/azure/dn194096(v=azure.100)](/previous-versions/azure/dn194096(v=azure.100)) をご覧ください

>[!NOTE]
>"UserPrincipalName" 値は SAML 2.0 要求で送信する "IDPEmail" の値と一致し、"ImmutableID" 値は "NameID" アサーションで送信される値と一致する必要があります。

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>SAML 2.0 IDP を使用したシングル サインオンを検証する
管理者としてシングル サインオン (ID フェデレーションとも呼ばれます) を検証して管理する前に、情報をレビューし、次の記事の手順を実行して、SAML 2.0 SP-Lite ベースの ID プロバイダーでのシングル サインオンを設定します。

1. Azure AD SAML 2.0 プロトコル要件をレビューしている
2. SAML 2.0 ID プロバイダーを構成している
3. SAML 2.0 ID プロバイダーでシングル サインオンするための Windows PowerShell をインストールしている
4. SAML 2.0 ID プロバイダーと Azure AD との間に信頼を確立している
5. Windows PowerShell または Azure AD Connect を介して Azure Active Directory (Microsoft 365) に既知のテスト ユーザー プリンシパルをプロビジョニングしている
6. [Azure AD Connect](whatis-hybrid-identity.md) を使用してディレクトリ同期を構成している

SAML 2.0 SP-Lite ベースの ID プロバイダーを使用するシングル サインオンを設定したら、それが正しく動作していることを検証する必要があります。

>[!NOTE]
>ドメインの追加ではなく、ドメインの変換を行った場合は、シングル サインオンが設定されるまで最大 24 時間かかる可能性があります。
シングル サインオンを検証する前に、Active Directory の同期の設定を完了し、ディレクトリを同期し、同期済みユーザーをアクティブ化する必要があります。

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>ツールを使用してシングル サインオンが正しく設定されていること検証する
シングル サインオンが正しく設定されていることを検証するには、次の手順を実行して、会社の資格情報でクラウド サービスにサインインできることを確認します。

Microsoft では、SAML 2.0 ベースの ID プロバイダーをテストするために使用できるツールを提供しています。 テスト ツールを実行する前に、ID プロバイダーとフェデレーションするように Azure AD テナントが構成されている必要があります。

>[!NOTE]
>接続アナライザーを使用するには、Internet Explorer 10 以降が必要です。



1. [接続アナライザー](https://testconnectivity.microsoft.com/?tabid=Client)をダウンロードします。
2. [今すぐインストール] をクリックして、ツールのダウンロードとインストールを開始します。
3. [I can’t set up federation with Office 365, Azure, or other services that use Azure Active Directory]\(Office 365、Azure、または Azure Active Directory を使用するその他のサービスとのフェデレーションを設定できません\) を選択します。
4. ツールがダウンロードされて実行されると、[接続診断] ウィンドウが表示されます。 ツールが示す手順に従って、フェデレーションの接続をテストします。
5. 接続アナライザーによって、サインインする SAML 2.0 IDP が開かれます。テストするユーザー プリンシパルの資格情報を入力します。

    ![SAML 2.0 IDP のサインイン ウィンドウを示すスクリーンショット。](./media/how-to-connect-fed-saml-idp/saml1.png)

6.  フェデレーション テストのサインイン ウィンドウでは、SAML 2.0 ID プロバイダーとフェデレーションするように構成されている Azure AD テナントのアカウント名とパスワードを入力する必要があります。 ツールは、これらの資格情報を使用してサインインを試行し、サインインの試行中に実行したテストの詳細な結果を出力として提示します。

    ![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)

7. このウィンドウには、テストの失敗した結果を示しています。 [詳細結果をレビュー] クリックすると、実行された各テストの結果に関する情報が表示されます。 結果を共有するためにディスクに保存することもできます。
 
> [!NOTE]
> 接続アナライザーは、WS * ベースのプロトコルと ECP/PAOS プロトコルを使用するアクティブ フェデレーションもテストします。 これらを使用していない場合は、ID プロバイダーのアクティブ フェデレーション エンドポイントを使用したアクティブ サインイン フローのテストで発生したエラーは無視することができます。

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>シングル サインオンが正しく設定されていることを手動で検証する

手動による検証では、多くのシナリオで SAML 2.0 ID プロバイダーが正しく動作することを確認するために実行できる追加の手順があります。
シングル サインオンが正しく設定されていることを検証するには、次の手順を完了します。

1. ドメインに参加しているコンピューターで、会社の資格情報で使用するのと同じサインイン名を使用して、クラウド サービスにサインインします。
2. [パスワード] ボックスの内側をクリックします。 シングル サインオンが設定されていれば、パスワード ボックスは網掛けになり、次のメッセージが表示されます。"&lt;お客様の会社&gt;にサインインする必要があります。"
3. &lt;会社名&gt; リンクの [サインイン] をクリックします。 サインインすることができたら、シングル サインオンが設定されています。

## <a name="next-steps"></a>次の手順

- [Azure AD Connect による Active Directory フェデレーション サービスの管理とカスタマイズ](how-to-connect-fed-management.md)
- [Azure AD のフェデレーション互換性リスト](how-to-connect-fed-compatibility.md)
- [Azure AD Connect のカスタム インストール](how-to-connect-install-custom.md)
