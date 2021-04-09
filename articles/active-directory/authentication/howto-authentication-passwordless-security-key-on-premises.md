---
title: オンプレミスのリソースへのパスワードなしのセキュリティ キー サインイン - Azure Active Directory
description: Azure Active Directory を使用してオンプレミスのリソースへのパスワードなしのセキュリティ キー サインインを有効にする方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: db1b559bb4f6a1f8866116c287df5b814500210b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101647474"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory"></a>Azure Active Directory を使用してオンプレミスのリソースへのパスワードなしのセキュリティ キー サインインを有効にする 

このドキュメントでは、**Azure AD 参加済み** と **ハイブリッド Azure AD 参加済み** の両方の Windows 10 デバイスがある環境での、オンプレミスのリソースに対するパスワードレス認証の有効化に注目します。 この機能により、Microsoft と互換性のあるセキュリティ キーを使用した、オンプレミスのリソースへのシームレスなシングル サインオン (SSO) が可能になります。

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>FIDO2 キーを使用したオンプレミスのリソースへの SSO

Azure Active Directory (AD) では、1 つまたは複数の Active Directory ドメインに対して Kerberos Ticket Granting Ticket (TGT) を発行できます。 この機能により、ユーザーは FIDO2 セキュリティ キーなどの最新の資格情報で Windows にサインインし、従来の Active Directory ベースのリソースにアクセスできるようになります。 Kerberos サービス チケットと認可は、引き続きオンプレミスの Active Directory ドメイン コントローラーによって制御されます。

Azure AD Kerberos サーバー オブジェクトがオンプレミスの Active Directory に作成され、Azure Active Directory に安全に公開されます。 このオブジェクトは、どの物理サーバーにも関連付けられていません。 これは単に、Active Directory ドメインの Kerberos TGT を生成するために Azure Active Directory で使用できるリソースです。

![Azure AD および AD DS からチケット保証チケット (TGT) を取得する](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. ユーザーは、FIDO2 セキュリティ キーを使用して Windows 10 デバイスにサインインし、Azure AD に対して認証を行います。
1. Azure AD では、ユーザーのオンプレミスの AD ドメインと一致する Kerberos サーバー キーのディレクトリが確認されます。
   1. Azure AD によって、ユーザーのオンプレミスの AD ドメイン用の Kerberos TGT が生成されます。 TGT には、ユーザーの SID のみが含まれます。 TGT には承認データが含まれていません。
1. TGT は Azure AD プライマリ更新トークン (PRT) と共にクライアントに返されます。
1. クライアント マシンは、オンプレミスの AD ドメイン コントローラーに接続し、部分的な TGT と引き換えに完全な形式の TGT を入手します。
1. この時点でクライアント マシンには Azure AD PRT と完全な Active Directory TGT があり、クラウドとオンプレミスの両方のリソースにアクセスできます。

## <a name="requirements"></a>必要条件

組織では、この記事の手順を実行する前に、[Windows 10 デバイスへのパスワードなしのセキュリティ キー サインインを有効にする](howto-authentication-passwordless-security-key.md)に関する記事の手順を完了する必要があります。

また、組織では、以下のソフトウェア要件を満たしている必要があります。

- デバイスでは Windows 10 バージョン 2004 以降を実行している必要があります。
- [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) のバージョンは 1.4.32.0 以降である必要があります。
  - 使用可能な Azure AD ハイブリッド認証オプションの詳細については、「[Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する](../hybrid/choose-ad-authn.md)」および「[Azure AD Connect で使用するインストールの種類の選択](../hybrid/how-to-connect-install-select-installation.md)」を参照します。
- Windows Server ドメイン コントローラーには、次の修正プログラムがインストールされている必要があります。
    - Windows Server 2016 の場合 - https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Windows Server 2019 の場合 - https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>サポートされるシナリオ

次の両方のシナリオで、シングル サインオン (SSO) がサポートされています。

- Microsoft 365 およびその他の SAML 対応アプリケーションなどのクラウド リソース。
- オンプレミスのリソースと、Web サイトに対する Windows 統合認証。 リソースには、IIS 認証を必要とする Web サイトおよび SharePoint サイトや、NTLM 認証を使用するリソースを含めることができます。

### <a name="unsupported-scenarios"></a>サポートされていないシナリオ

以下のシナリオはサポートされていません。

- Windows Server Active Directory Domain Services (AD DS) ドメインに参加済みの (オンプレミス専用デバイスの) デプロイ。
- セキュリティ キーを使用した RDP、VDI、および Citrix のシナリオ。
- セキュリティ キーを使用した S/MIME。
- セキュリティ キーを使用した [別のユーザーとして実行]。
- セキュリティ キーを使用したサーバーへのログイン。

## <a name="create-kerberos-server-object"></a>Kerberos サーバー オブジェクトの作成

管理者は、Azure AD Connect サーバーから PowerShell ツールを使用して、オンプレミスのディレクトリに Azure AD Kerberos サーバー オブジェクトを作成します。 Azure AD ユーザーを含む組織内の各ドメインおよびフォレストで、次の手順を実行してください。

1. 最新のバージョンの Azure AD Connect にアップグレードします。 この手順では、ハイブリッド環境をサポートするために Azure AD Connect が既に構成されていることを前提としています。
1. Azure AD Connect サーバーで、管理者特権の PowerShell プロンプトを開き、`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\` に移動します。
1. 次の PowerShell コマンドを実行して、オンプレミスの Active Directory ドメインと Azure Active Directory テナントの両方に新しい Azure AD Kerberos サーバー オブジェクトを作成します。

> [!NOTE]
> 次の例の `contoso.corp.com` を、オンプレミスの Active Directory ドメイン名に置き換えます。

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Azure AD Kerberos サーバーの表示と確認

新しく作成された Azure AD Kerberos サーバーは、次のコマンドを使用して表示および確認できます。

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

このコマンドで、Azure AD Kerberos サーバーのプロパティが出力されます。 プロパティを確認して、すべてが適切な順序であることを確認できます。

| プロパティ | 説明 |
| --- | --- |
| id | AD DS DC オブジェクトの一意の ID。 この ID は、"スロット" または "ブランチ ID" と呼ばれることもあります。 |
| DomainDnsName | Active Directory ドメインの DNS ドメイン名。 |
| ComputerAccount | Azure AD Kerberos サーバー オブジェクト (DC) のコンピューター アカウント オブジェクト。 |
| UserAccount | Azure AD Kerberos サーバーの TGT 暗号化キーを保持する、無効なユーザー アカウント オブジェクト。 このアカウントの DN は `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` です |
| KeyVersion | Azure AD Kerberos サーバーの TGT 暗号化キーのキー バージョン。 バージョンは、キーの作成時に割り当てられます。 バージョンは、キーがローテーションされるたびに増やされます。 増分はレプリケーション メタデータに基づいており、おそらく 1 より大きい値です。 たとえば、初期の *KeyVersion* が *192272* だったとします。 キーが最初にローテーションされると、バージョンは *212621* に進む可能性があります。 確認すべき重要な点は、オンプレミスのオブジェクトの *KeyVersion* とクラウド オブジェクトの *CloudKeyVersion* が同じであることです。 |
| KeyUpdatedOn | Azure AD Kerberos サーバーの TGT 暗号化キーが更新または作成された日時。 |
| KeyUpdatedFrom | Azure AD Kerberos サーバーの TGT 暗号化キーが最後に更新された DC。 |
| CloudId | Azure AD オブジェクトからの ID。 上記の ID と一致している必要があります。 |
| CloudDomainDnsName | Azure AD オブジェクトの *DomainDnsName*。 上記の *DomainDnsName* と一致している必要があります。 |
| CloudKeyVersion | Azure AD オブジェクトの *KeyVersion*。 上記の *KeyVersion* と一致している必要があります。 |
| CloudKeyUpdatedOn | Azure AD オブジェクトの *KeyUpdatedOn*。 上記の *KeyUpdatedOn* と一致している必要があります。 |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Azure AD Kerberos サーバー キーのローテーション

Azure AD Kerberos サーバー暗号化の krbtgt キーは定期的にローテーションする必要があります。 他のすべての Active Directory ドメイン コントローラーの krbtgt キーのローテーションに使用するのと同じスケジュールに従うことをお勧めします。

> [!WARNING]
> krbtgt キーをローテーションできるツールは他にもありますが、Azure AD Kerberos サーバーの krbtgt キーをローテーションするには、このドキュメントに記載されているツールを使用する必要があります。 これにより、オンプレミスの AD と Azure AD の両方でキーが確実に更新されます。

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Azure AD Kerberos サーバーの削除

シナリオを元に戻し、オンプレミスの Active Directory と Azure Active Directory の両方から Azure AD Kerberos サーバーを削除する場合は、次のコマンドを実行します。

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>複数フォレストと複数ドメインのシナリオ

Azure AD Kerberos サーバー オブジェクトは、Azure AD 内では *KerberosDomain* オブジェクトとして表されます。 オンプレミスの各 Active Directory ドメインは、Azure AD 内では 1 つの *KerberosDomain* オブジェクトとして表されます。

たとえば、`contoso.com` と `fabrikam.com` という 2 つのドメインを含む Active Directory フォレストが組織にあるとします。 Azure AD でのフォレスト全体に対する Kerberos TGT の発行を許可すること選択した場合は、Azure AD 内に 2 つの *KerberosDomain* オブジェクトがあります。 1 つの *KerberosDomain* オブジェクトは `contoso.com` 用で、もう 1 つは `fabrikam.com` 用です。 複数の Active Directory フォレストがある場合は、各フォレスト内のドメインごとに 1 つの *KerberosDomain* オブジェクトがあります。

Azure AD ユーザーを含む組織内の各ドメインおよびフォレストで、「[Kerberos サーバー オブジェクトの作成](#create-kerberos-server-object)」の手順を実行する必要があります。

## <a name="known-behavior"></a>既知の動作

パスワードの有効期限が切れている場合、FIDO でのサインインはブロックされます。 FIDO を使用してログインする前に、ユーザーが自分のパスワードをリセットすることが想定されます。

## <a name="troubleshooting-and-feedback"></a>トラブルシューティングとフィードバック

この機能についてフィードバックを共有したい場合、または問題が発生した場合は、次の手順を使用して Windows フィードバック Hub アプリ経由で共有してください。

1. **フィードバック ハブ** を起動し、サインインしていることを確認します。
1. 次の分類でフィードバックを送信します。
   - カテゴリ:セキュリティとプライバシー
   - サブカテゴリ: FIDO
1. ログをキャプチャするには、 **[Recreate my Problem]\(問題の再現\)** オプションを使用します

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="does-this-work-in-my-on-premises-environment"></a>これはオンプレミス環境で動作しますか。

この機能は、純粋なオンプレミスの Active Directory Domain Services (AD DS) 環境では機能しません。

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>私の組織では、リソースにアクセスするために 2 要素認証を求めています。 この要件をサポートするにはどうすればよいですか。

セキュリティ キーは、さまざまなフォーム ファクターで提供されています。 2 番目の要素として PIN または生体認証を使用してデバイスを有効にする方法については、目的のデバイス製造元にお問い合わせください。

### <a name="can-admins-set-up-security-keys"></a>管理者はセキュリティ キーを設定できますか。

Microsoft はこの機能を一般提供 (GA) するため、この機能に取り組んでいます。

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>準拠しているセキュリティ キーはどこで見つけることができますか。

[FIDO2 セキュリティ キー](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>セキュリティ キーを紛失した場合はどうすればよいですか。

**[セキュリティ情報]** ページに移動してセキュリティ キーを削除すると、Azure portal からキーを削除できます。

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>ハイブリッド Azure AD 参加済みマシンを作成した直後に FIDO を使用できません

ハイブリッド Azure AD 参加済みマシンをクリーン インストールしている場合は、ドメイン参加と再起動プロセスの後、FIDO を使用してサインインする前に、パスワードを使用してサインインし、ポリシーが同期されるまで待つ必要があります。

- 現在の状態を確認するには、コマンド ウィンドウに `dsregcmd /status` と入力し、*AzureAdJoined* と *DomainJoined* の両方に *[YES]* と表示されていることを確認します。
- この遅延は、ドメインに参加しているデバイスに対する既知の制限であり、FIDO に固有ではありません。

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>FIDO を使用してサインインし、資格情報プロンプトが表示された後、自分の NTLM ネットワーク リソースへの SSO を取得できません

時間内に応答してリソース要求を処理できるように、十分な数のドメイン コントローラーに修正プログラムが適用されていることを確認してください。 機能を実行しているドメイン コントローラーが表示できるかどうかを確認するには、`nltest /dsgetdc:contoso /keylist /kdc` の出力を確認します。

## <a name="next-steps"></a>次のステップ

[パスワードなしに関する詳細情報](concept-authentication-passwordless.md)
