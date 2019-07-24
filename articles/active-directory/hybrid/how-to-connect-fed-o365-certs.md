---
title: Office 365 と Azure AD のユーザー向け証明書の更新 | Microsoft Docs
description: この記事では、証明書の更新を通知する電子メールによって生じる問題を解決する方法を Office 365 のユーザー向けに説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d98a1aabef2de505e66b2127226b9e89cd791e20
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883447"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Office 365 および Azure Active Directory 用のフェデレーション証明書の更新
## <a name="overview"></a>概要
Azure Active Directory (Azure AD) と Active Directory Federation Services (AD FS) とのフェデレーションが正常に機能するためには、AD FS が Azure AD に提示するセキュリティ トークンに署名するときに使う証明書が、Azure AD 側の構成内容と一致している必要があります。 完全に一致していないと、信頼関係が失われる可能性があります。 証明書の情報は、AD FS と (エクストラネット アクセスに使用される) Web アプリケーション プロキシをデプロイするときに Azure AD によって同期されます。

この記事では、トークン署名証明書を管理し、Azure AD との同期状態を維持する方法について詳しく説明します。以下のケースを想定しています。

* Web アプリケーション プロキシをデプロイしていないため、エクストラネットではフェデレーション メタデータを利用できない。
* トークン署名証明書に AD FS の既定の構成を使用していない。
* サード パーティの ID プロバイダーを使用している。

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>トークン署名証明書に使用する AD FS の既定の構成
通常、トークン署名証明書とトークン暗号化解除証明書は自己署名証明書であり、有効期間は 1 年です。 AD FS には **AutoCertificateRollover**と呼ばれる自動更新プロセスが既定で含まれています。 AD FS 2.0 以降を使用している場合、Office 365 と Azure AD では、証明書は期限切れになる前に自動的に更新されます。

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Microsoft 365 管理センターからの更新の通知またはメール
> [!NOTE]
> Office の証明書を更新するよう求める電子メールまたはポータル通知が届いた場合は、 [トークン署名証明書に対する変更の管理](#managecerts) についての記載を参照して、何らかの対処が必要かどうかを確認してください。 実際には対処が不要であるにもかかわらず証明書の更新を求める通知が送信される問題が確認されています。
>
>

Azure AD は、フェデレーション メタデータを監視し、その結果に応じてトークン署名証明書の更新を試みます。 トークン署名証明書の有効期限が切れる 30 日前に、Azure AD がフェデレーション メタデータをポーリングして新しい証明書が利用可能かどうかをチェックします。

* フェデレーション メタデータをポーリングして新しい証明書を取得できた場合は、Microsoft 365 管理センターでのメール通知も警告もユーザーには送信されません。
* フェデレーション メタデータにアクセスできないか、証明書の自動ロールオーバーが有効になっていないことが原因で、新しいトークン署名証明書を取得できない場合は、Azure AD によって、Microsoft 365 管理センターでメール通知と警告が発行されます。

![Office 365 portal notification](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> AD FS を使用している場合、ビジネス継続性を確保するために、既知の問題による認証エラーを防止する更新プログラムがサーバーに適用されていることを確認してください。該当する更新プログラムは次のとおりです。 以後、更新期間における AD FS プロキシ サーバーの既知の問題が軽減されます。
>
> Server 2012 R2 - [Windows Server 2014 年 5 月の更新プログラム ロールアップ](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 および 2012 - [Windows Server 2012 または Windows 2008 R2 SP1 で、プロキシ経由の認証に失敗する](https://support.microsoft.com/kb/3094446)
>
>

## 証明書の更新が必要かどうかを確認する <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>手順 1:AutoCertificateRollover の状態を確認する
AD FS サーバーで PowerShell を開きます。 AutoCertificateRollover の値が True に設定されていることを確認します。

    Get-Adfsproperties

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>AD FS 2.0 を使用している場合は、最初に Add-Pssnapin Microsoft.Adfs.Powershell を実行してください。

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>手順 2:AD FS と Azure AD が同期されていることを確認する
AD FS Server で MSOnline PowerShell プロンプトを開き、Azure AD に接続します。

> [!NOTE]
> MSOL-Cmdlets は、MSOnline PowerShell モジュールの一部です。
> PowerShell ギャラリーから MSOnline PowerShell モジュールを直接ダウンロードできます。
> 
>

    Install-Module MSOnline

MSOnline PowerShell-Module を使用して Azure AD に接続します。

    Import-Module MSOnline
    Connect-MsolService

AD FS と Azure AD との間の信頼関係のプロパティで証明書が構成されていることを特定のドメインを指定して確認します。

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

両方の出力結果において拇印が一致した場合、Azure AD 側と証明書が同期されています。

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>手順 3:証明書の有効期限が迫っているかどうかを確認する
Get-MsolFederationProperty または Get-AdfsCertificate の出力結果で、"有効期間の終了時刻" の日付を確認します。 今日の日付から 30 日未満である場合、期限切れに対処する必要があります。

| AutoCertificateRollover | Azure AD 側と証明書が同期されている | フェデレーション メタデータにパブリックにアクセス可能 | 有効期限までの日数 | Action |
|:---:|:---:|:---:|:---:|:---:|
| はい |はい |はい |- |対処は必要ありません。 「 [トークン署名証明書を自動的に更新する](#autorenew)」を参照してください。 |
| はい |いいえ  |- |15 日未満 |すぐに更新してください。 「 [トークン署名証明書を手動で更新する](#manualrenew)」を参照してください。 |
| いいえ  |- |- |30 日未満 |すぐに更新してください。 「 [トークン署名証明書を手動で更新する](#manualrenew)」を参照してください。 |

\[-] 該当せず

## トークン署名証明書を自動的に更新する (推奨) <a name="autorenew"></a>
次の 2 点両方に該当する場合は、手動の手順を実行する必要はありません。

* Web アプリケーション プロキシをデプロイ済みで、エクストラネットからフェデレーション メタデータへのアクセスを有効にできる。
* AD FS の既定の構成を使用している (AutoCertificateRollover が有効である)。

証明書を自動的に更新できるかどうかを確認するには、次の点をチェックしてください。

**1.AD FS の AutoCertificateRollover プロパティが True に設定されている。** これは、有効期限が切れる前に、AD FS が新しいトークン署名証明書とトークン暗号化解除証明書を自動的に生成することを示します。

**2.AD FS のフェデレーション メタデータへのパブリック アクセスが確保されている。**  (社内ネットワークの外部の) パブリック インターネット上のコンピューターで次の URL に移動して、フェデレーション メタデータにパブリックにアクセスできることを確認します。

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

この `(your_FS_name)` は、fs.contoso.com など、組織で使用しているフェデレーション サービスのホスト名に置き換えます。  どちらの設定も適切であることを確認できた場合、他の作業は不要です。  

例: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## トークン署名証明書を手動で更新する <a name="manualrenew"></a>
トークン署名証明書を手動で更新することもできます。 たとえば、次のシナリオは手動更新の方が適している場合があります。

* トークン署名証明書が自己署名証明書ではない。 よくある理由に、企業が、組織的な証明機関によって登録された AD FS 証明書を管理していることが挙げられます。
* フェデレーション メタデータのパブリック アクセスがネットワーク セキュリティで禁止されている。

このようなシナリオでは、トークン署名証明書を更新するたびに、PowerShell コマンド Update-MsolFederatedDomain を使って Office 365 ドメインも更新する必要があります。

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>手順 1:AD FS のトークン署名証明書が新しいことを確認する
**既定の構成が変更されている場合**

AD FS の既定の構成が変更されている (**AutoCertificateRollover** が **False** に設定されている) 場合、(自己署名ではない) カスタムの証明書が使用されていると考えられます。 AD FS トークン署名証明書の更新方法の詳細については、「 [AD FS 自己署名証明書を使用しないお客様へのガイダンス](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert)」を参照してください。

**フェデレーション メタデータへのパブリック アクセスができない場合**

**AutoCertificateRollover** が **True** に設定されているにもかかわらず、フェデレーション メタデータに対してパブリックにアクセスできない場合は、まず新しいトークン署名証明書が AD FS によって生成されていることを確認します。 以下の手順に従って、新しいトークン署名証明書があることを確認してください。

1. プライマリ AD FS サーバーにログオンしていることを確認します。
2. PowerShell コマンド ウィンドウを開き、次のコマンドを実行して、AD FS の現在の署名証明書を確認します。

    PS C:\>Get-ADFSCertificate –CertificateType token-signing

   > [!NOTE]
   > AD FS 2.0 を使用している場合は、Add-Pssnapin Microsoft.Adfs.Powershell を最初に実行する必要があります。
   >
   >
3. コマンドの出力に表示されたすべての証明書を確認します。 新しい証明書が AD FS によって生成されている場合は、出力に 2 つの証明書が表示されます。1 つは **IsPrimary** 値が **True** で **NotAfter** の日付が 5 日以内、もう 1 つは **IsPrimary** 値が **False** で **NotAfter** の日付が約 1 年後です。
4. 証明書が 1 つしか表示されず、その **NotAfter** の日付が 5 日以内の場合は、新しい証明書を生成する必要があります。
5. 新しい証明書を生成するには、PowerShell コマンド プロンプトで次のコマンドを実行します: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`。
6. 次のコマンドを再実行して更新を確認します。PS C:\>Get-ADFSCertificate –CertificateType token-signing

これで、2 つの証明書が表示されます。1 つは **NotAfter** の日付が約 1 年後で、**IsPrimary** の値が **False** です。

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>手順 2:Office 365 の信頼のために新しいトークン署名証明書を更新する
次のように、信頼に使用できるように、新しいトークン署名証明書で Office 365 を更新します。

1. Windows PowerShell 用 Microsoft Azure Active Directory モジュールを開きます。
2. $cred=Get-Credential を実行します。 このコマンドレットで資格情報の入力を求められたら、クラウド サービス管理者アカウントの資格情報を入力します。
3. Connect-MsolService –Credential $cred を実行します。このコマンドレットでクラウド サービスに接続します。 クラウド サービスに接続している状況を作った後で、ツールによってインストールされた追加のコマンドレットを実行する必要があります。
4. AD FS のプライマリ フェデレーション サーバー以外のコンピューターでこれらのコマンドを実行している場合は、Set-MSOLAdfscontext -Computer &lt;AD FS プライマリ サーバー&gt;を実行します。この &lt;AD FS プライマリ サーバー&gt;は、プライマリ AD FS サーバーの内部 FQDN 名です。 このコマンドレットで AD FS に接続している状況を作ります。
5. Update-MSOLFederatedDomain –DomainName &lt;ドメイン&gt; を実行します。 このコマンドレットは、AD FS の設定でクラウド サービスを更新し、両者の信頼関係を構成します。

> [!NOTE]
> contoso.com や fabrikam.com などの複数のトップ レベル ドメインをサポートする必要がある場合は、すべてのコマンドレットで **SupportMultipleDomain** スイッチを使用する必要があります。 詳細については、 [複数のトップ レベル ドメインのサポート](how-to-connect-install-multiple-domains.md)に関するページを参照してください。
>


## Azure AD Connect を使用して Azure AD 信頼を修復する <a name="connectrenew"></a>
Azure AD Connect を使用して AD FS ファームと Azure AD 信頼を構成した場合は、トークン署名証明書に関して何らかの対処が必要かどうかを Azure AD Connect を使用して検出できます。 証明書を更新する必要がある場合は、Azure AD Connect を使用して更新できます。

詳細については、「 [信頼の修復](how-to-connect-fed-management.md)」を参照してください。

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>AD FS と Azure AD の証明書の更新手順
トークン署名証明書は標準の X509 証明書であり、フェデレーション サーバーが発行するすべてのトークンに安全に署名するために使用されます。 トークン暗号化解除証明書は、標準の X509 証明書であり、受信トークンの暗号化解除に使用されます。 

既定では、AD FS は、初期構成時にも、証明書の有効期限が近づいたときにも、トークン署名証明書とトークン暗号化解除証明書を自動的に生成するように構成されます。

Azure AD は、現在の証明書の有効期限が切れる 30 日前に、フェデレーション サービスのメタデータから新しい証明書を取得しようとします。 その時点で新しい証明書が利用できない場合、Azure AD は 1 日間隔でメタデータの監視を続けます。 新しい証明書がメタデータで利用可能になるとすぐに、ドメインのフェデレーション設定が新しい証明書情報で更新されます。 `Get-MsolDomainFederationSettings` を使用すると、NextSigningCertificate / SigningCertificate に新しい証明書があるかどうかを確認できます。

AD FS のトークン署名証明書の詳細については「[Obtain and Configure Token Signing and Token Decryption Certificates for AD FS (AD FS でのトークン署名証明書およびトークン暗号化解除証明書の取得と構成)](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)」 をご覧ください。
