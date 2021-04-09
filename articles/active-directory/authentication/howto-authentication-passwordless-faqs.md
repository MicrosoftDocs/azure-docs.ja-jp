---
title: ハイブリッド FIDO2 セキュリティ キーのデプロイに関する FAQ - Azure Active Directory
description: Azure Active Directory を使用するパスワードレスのハイブリッド FIDO2 セキュリティ キー サインインに関してよく寄せられる質問について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca4943293f9474d4089267d05460d6d8766b79e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646386"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad"></a>Azure AD でのハイブリッド FIDO2 セキュリティ キーのデプロイに関してよく寄せられる質問 (FAQ) 

この記事では、ハイブリッド Azure AD 参加済みデバイスと、オンプレミスのリソースへのパスワードレス サインインに関してよく寄せられるデプロイの質問 (FAQ) について説明します。 このパスワードレスの機能により、FIDO2 セキュリティ キーを使用して、Windows 10 デバイスをハイブリッド Azure AD 参加済みデバイスとして Azure AD 認証することができます。 ユーザーは、FIDO2 キーのような最新の資格情報を使用してデバイス上の Windows にサインインし、オンプレミスのリソースへのシームレスなシングル サインオン (SSO) エクスペリエンスにより、従来の Active Directory Domain Services (AD DS) ベースのリソースにアクセスできます。

ハイブリッド環境のユーザーに対して、次のシナリオがサポートされています。

* FIDO2 セキュリティ キーを使用してハイブリッド Azure AD 参加済みデバイスにサインインし、オンプレミスのリソースへの SSO アクセスを取得します。
* FIDO2 セキュリティ キーを使用して Azure AD 参加済みデバイスにサインインし、オンプレミスのリソースへの SSO アクセスを取得します。

FIDO2 のセキュリティ キーおよびオンプレミスのリソースへのハイブリッド アクセスの概要については、次の記事を参照してください。

* [パスワードレスの FIDO2 セキュリティ キー](howto-authentication-passwordless-security-key.md)
* [パスワードレスの Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [パスワードレスのオンプレミス](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="security-keys"></a>セキュリティ キー

* [私の組織では、リソースにアクセスするために 2 要素認証が必要です。この要件をサポートするにはどうすればよいですか。](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [準拠している FIDO2 セキュリティ キーはどこで確認できますか。](#where-can-i-find-compliant-fido2-security-keys)
* [セキュリティ キーを紛失した場合はどうすればよいですか。](#what-if-i-lose-my-security-key)
* [FIDO2 セキュリティ キーのデータはどのように保護されていますか。](#how-is-the-data-protected-on-the-fido2-security-key)
* [FIDO2 セキュリティ キーの登録はどのように機能しますか。](#how-does-the-registering-of-fido2-security-keys-work)
* [管理者がユーザーのキーを直接プロビジョニングする方法はありますか。](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>私の組織では、リソースにアクセスするために多要素認証が必要です。 この要件をサポートするにはどうすればよいですか。

FIDO2 セキュリティ キーは、さまざまなフォーム ファクターで提供されています。 2 番目の要素として PIN または生体認証を使用してデバイスを有効にする方法については、目的のデバイス製造元にお問い合わせください。 サポートされているプロバイダーの一覧については、[FIDO2 セキュリティ キー プロバイダー](concept-authentication-passwordless.md#fido2-security-key-providers)に関するセクションを参照してください。

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>準拠している FIDO2 セキュリティ キーはどこで確認できますか。

サポートされているプロバイダーの一覧については、[FIDO2 セキュリティ キー プロバイダー](concept-authentication-passwordless.md#fido2-security-key-providers)に関するセクションを参照してください。

### <a name="what-if-i-lose-my-security-key"></a>セキュリティ キーを紛失した場合はどうすればよいですか。

キーを削除するには、Azure portal の **[セキュリティ情報]** ページに移動して、FIDO2 セキュリティ キーを削除できます。

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>FIDO2 セキュリティ キーのデータはどのように保護されていますか。

FIDO2 セキュリティ キーには、格納されている秘密キーを保護するセキュリティで保護されたエンクレーブがあります。 また FIDO2 セキュリティ キーには、Windows Hello と同様にハンマリング対策プロパティも組み込まれており、秘密キーを抽出することはできません。

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>FIDO2 セキュリティ キーの登録はどのように機能しますか。

FIDO2 セキュリティ キーを登録して使用する方法の詳細については、「[パスワードなしのセキュリティ キー サインインを有効にする](howto-authentication-passwordless-security-key.md)」を参照してください。

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>管理者がユーザーのキーを直接プロビジョニングする方法はありますか。

現時点ではできません。

## <a name="prerequisites"></a>前提条件

* [インターネットに接続されていない場合、この機能は動作しますか。](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Azure AD に対して開いておく必要がある具体的なエンド ポイントはどれですか。](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Windows 10 デバイスのドメイン参加の種類 (Azure AD 参加済み、またはハイブリッド Azure AD 参加済み) をどのように特定できますか。](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [パッチを適用すべき DC の数に関する推奨事項はありますか。](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [オンプレミス専用デバイスに FIDO2 資格情報プロバイダーをデプロイできますか。](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [FIDO2 セキュリティ キーのサインインが、ドメイン管理者またはその他の高い権限を持つアカウントに対して機能していません。なぜですか?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>インターネットに接続されていない場合、この機能は動作しますか。

インターネット接続は、この機能を有効にするための前提条件です。 ユーザーが FIDO2 セキュリティ キーを使用して初めてサインインするときは、インターネットに接続している必要があります。 それ以降のサインイン イベントでは、キャッシュされたサインインが機能し、ユーザーがインターネットに接続せずに認証できるようになります。

一貫したエクスペリエンスを実現するには、デバイスがインターネットにアクセスでき、DC と通信できることを確認します。

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Azure AD に対して開いておく必要がある具体的なエンド ポイントはどれですか。

登録と認証には、次のエンドポイントが必要です。

* * *.microsoftonline.com*
* * *.microsoftonline-p.com*
* * *.msauth.net*
* * *.msauthimages.net*
* * *.msecnd.net*
* * *.msftauth.net*
* * *.msftauthimages.net*
* * *.phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

Microsoft のオンライン製品を使用するために必要なエンドポイントの完全な一覧については、「[Office 365 URL および IP アドレス範囲](/microsoft-365/enterprise/urls-and-ip-address-ranges)」を参照してください。

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Windows 10 デバイスのドメイン参加の種類 (Azure AD 参加済み、またはハイブリッド Azure AD 参加済み) をどのように特定できますか。

Windows 10 クライアント デバイスのドメイン参加の種類が正しいかどうかを確認するには、次のコマンドを使用します。

```console
Dsregcmd/status
```

次のサンプル出力では、*AzureADJoined* が *YES* に設定されていることから、デバイスが Azure AD 参加済みであることが示されています。

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

次のサンプル出力では、*DomainedJoined* も *YES* に設定されていることから、デバイスがハイブリッド Azure AD 参加済みであることが示されています。 *DomainName* も表示されます。

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

Windows Server 2016 または 2019 ドメイン コントローラーで、次のパッチが適用されていることを確認します。 必要に応じて、Windows Update を実行してインストールします。

* Windows Server 2016 - [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 - [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

クライアント デバイスから次のコマンドを実行して、パッチがインストールされた適切なドメイン コントローラーへの接続を確認します。

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>パッチを適用すべき DC の数に関する推奨事項はありますか。

Windows Server 2016 または 2019 ドメイン コントローラーの大部分にパッチを適用して、組織の認証要求の負荷を処理できるようにすることをお勧めします。

Windows Server 2016 または 2019 ドメイン コントローラーで、次のパッチが適用されていることを確認します。 必要に応じて、Windows Update を実行してインストールします。

* Windows Server 2016 - [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 - [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>オンプレミス専用デバイスに FIDO2 資格情報プロバイダーをデプロイできますか。

いいえ。この機能はオンプレミス専用デバイスではサポートされていません。 FIDO2 資格情報プロバイダーは表示されません。

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>FIDO2 セキュリティ キーのサインインが、ドメイン管理者またはその他の高い権限を持つアカウントに対して機能していません。 なぜですか?

既定のセキュリティ ポリシーでは、高い権限を持つアカウントでオンプレミス リソースにサインオンするための Azure AD アクセス許可は付与されません。

アカウントのブロックを解除するには、 **[Active Directory ユーザーとコンピューター]** を使用して、*Azure AD Kerberos Computer オブジェクト (CN=AzureADKerberos,OU=Domain Controllers,\<domain-DN>)* の *msDS-NeverRevealGroup* プロパティを変更します。

## <a name="under-the-hood"></a>しくみ

* [オンプレミスの Active Directory Domain Services 環境に、Azure AD Kerberos はどのようにリンクされますか。](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [AD で作成されて Azure AD で公開された、これらの Kerberos サーバー オブジェクトはどこで確認できますか。](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [インターネットへの依存関係がなくなるように、公開キーをオンプレミスの AD DS に登録することができないのはなぜですか。](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [Kerberos サーバー オブジェクトでは、キーはどのようにローテーションされますか。](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [なぜ Azure AD Connect が必要なのですか。これによって、Azure AD から AD DS に情報が書き戻されることはありますか。](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [PRT と部分的な TGT を要求した場合、HTTP 要求/応答はどのようになりますか。](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>オンプレミスの Active Directory Domain Services 環境に、Azure AD Kerberos はどのようにリンクされますか。

これには、オンプレミスの AD DS 環境と、Azure AD テナントの 2 つの部分があります。

**Active Directory Domain Services (AD DS)**

Azure AD Kerberos サーバーは、オンプレミスの AD DS 環境ではドメイン コントローラー (DC) オブジェクトとして表されます。 この DC オブジェクトは、次の複数のオブジェクトで構成されています。

* *CN=AzureADKerberos,OU=Domain Controllers,\<domain-DN>*
    
    AD DS で読み取り専用ドメイン コントローラー (RODC) を表す *コンピューター* オブジェクト。 このオブジェクトに関連付けられているコンピューターはありません。 むしろ、これは DC の論理的な表現です。

* *CN=krbtgt_AzureAD,CN=Users,\<domain-DN>*

    RODC Kerberos チケット保証チケット (TGT) 暗号化キーを表す *ユーザー* オブジェクト。

* *CN=900274c4-b7d2-43c8-90ee-00a9f650e335,CN=AzureAD,CN=System,\<domain-DN>*

    Azure AD Kerberos サーバー オブジェクトに関するメタデータを格納する *ServiceConnectionPoint* オブジェクト。 管理ツールではこのオブジェクトを使用して、Azure AD Kerberos サーバー オブジェクトを特定し、探し出します。

**Azure Active Directory**

Azure AD Kerberos サーバーは、Azure AD 内では *KerberosDomain* オブジェクトとして表されます。 オンプレミスの各 AD DS 環境は、Azure AD テナント内では 1 つの *KerberosDomain* オブジェクトとして表されます。

たとえば、*contoso.com* および *fabrikam.com* という 2 つのドメインを持つ AD DS フォレストがあるとします。 Azure AD を使用してフォレスト全体に対して Kerberos チケット保証チケット (TGT) を発行することを許可した場合、Azure AD には 2 つの *KerberosDomain* オブジェクトが存在します。1 つは *contoso.com* 用で、もう 1 つは *fabrikam.com* 用です。

複数の AD DS フォレストがある場合は、各フォレスト内のドメインごとに 1 つの *KerberosDomain* オブジェクトがあります。

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>AD DS で作成されて Azure AD で公開された、これらの Kerberos サーバー オブジェクトはどこで確認できますか。

すべてのオブジェクトを表示するには、Azure AD Connect の最新バージョンに含まれている Azure AD Kerberos サーバー PowerShell コマンドレットを使用します。

オブジェクトの表示方法を含む詳細については、「[Kerberos サーバー オブジェクトの作成](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object)」を参照してください。

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>インターネットへの依存関係がなくなるように、公開キーをオンプレミスの AD DS に登録することができないのはなぜですか。

Microsoft では Windows Hello for Business のデプロイ モデルの複雑さに関するフィードバックを受け取っていたため、証明書と PKI を使用する必要をなくすために、デプロイ モデルを簡略化する必要がありました (FIDO2 は証明書を使用しません)。

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>Kerberos サーバー オブジェクトでは、キーはどのようにローテーションされますか。

他の DC と同様に、Azure AD Kerberos サーバー暗号化の *krbtgt* キーは定期的にローテーションする必要があります。 他のすべての AD DS *krbtgt* キーのローテーションのスケジュールと同じスケジュールにすることをお勧めします。

> [!NOTE]
> *krbtgt* キーをローテーションするツールは他にもありますが、Azure AD Kerberos サーバーの [*krbtgt* キーをローテーションするには、PowerShell コマンドレットを使用する](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key)必要があります。 この方法を使用すると、オンプレミスの AD DS 環境と Azure AD の両方で、キーが確実に更新されます。

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>なぜ Azure AD Connect が必要なのですか。 これによって、Azure AD から AD DS に情報が書き戻されることはありますか。

Azure AD Connect によって、Azure AD から AD DS に情報が書き戻されることはありません。 このユーティリティには、AD DS で Kerberos サーバー オブジェクトを作成して Azure AD で公開する、PowerShell モジュールが含まれています。

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>PRT と部分的な TGT を要求した場合、HTTP 要求/応答はどのようになりますか。

HTTP 要求は、標準のプライマリ更新トークン (PRT) 要求です。 この PRT 要求には、Kerberos チケット保証チケット (TGT) が必要であることを示すクレームが含まれています。

| 要求 | 値 | 説明                             |
|-------|-------|-----------------------------------------|
| tgt   | true  | クレームは、クライアントに TGT が必要であることを示します。 |

Azure AD は、暗号化されたクライアント キーとメッセージ バッファーを、追加のプロパティとして PRT 応答に結合します。 ペイロードは、Azure AD デバイス セッション キーを使用して暗号化されます。

| フィールド              | Type   | 説明  |
|--------------------|--------|--------------|
| tgt_client_key     | string | Base64 でエンコードされたクライアント キー (シークレット)。 このキーは、TGT を保護するために使用されるクライアント シークレットです。 このパスワードレスのシナリオでは、クライアント シークレットはサーバーによって各 TGT 要求の一部として生成され、応答でクライアントに返されます。 |
| tgt_key_type       | INT    | クライアント キーと KERB_MESSAGE_BUFFER に含まれる Kerberos セッション キーの両方に使用される、オンプレミスの AD DS キーの種類。 |
| tgt_message_buffer | string | Base64 でエンコードされた KERB_MESSAGE_BUFFER。 |

## <a name="next-steps"></a>次のステップ

FIDO2 のセキュリティ キーおよびオンプレミスのリソースへのハイブリッド アクセスの概要については、次の記事を参照してください。

* [パスワードレスの FIDO2 セキュリティ キー](howto-authentication-passwordless-security-key.md)
* [パスワードレスの Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [パスワードレスのオンプレミス](howto-authentication-passwordless-security-key-on-premises.md)
