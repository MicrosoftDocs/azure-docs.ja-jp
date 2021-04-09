---
title: Azure Active Directory アプリケーション プロキシに関してよく寄せられる質問
description: Azure AD アプリケーション プロキシを使用して内部のオンプレミス アプリケーションをリモート ユーザーに公開する方法についてよく寄せられる質問 (FAQ) とその回答を紹介します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 07/23/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: edd2ec633bd78ce1a596782deab57105e9d7f1c3
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487748"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Active Directory (Azure AD) アプリケーション プロキシに関してよく寄せられる質問

このページには、Azure Active Directory (Azure AD) アプリケーション プロキシに関してよく寄せられる質問への回答が記載されています。

## <a name="enabling-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシの有効化

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用するにはどのライセンスが必要ですか。

Azure AD アプリケーション プロキシを使用するには、Azure AD Premium P1 または P2 ライセンスが必要です。 ライセンスの詳細については、[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

### <a name="what-happens-to-azure-ad-application-proxy-in-my-tenant-if-my-license-expires"></a>ライセンスの有効期限が切れた場合、自分のテナントの Azure AD アプリケーション プロキシはどうなりますか。
ライセンスの有効期限が切れると、アプリケーション プロキシは自動的に無効になります。 お使いのアプリケーションの情報は、最大 1 年間保存されます。

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>[アプリケーション プロキシの有効化] ボタンが淡色表示されているのはなぜですか。

少なくとも Azure AD Premium P1 または P2 ライセンスと、Azure AD アプリケーション プロキシ コネクタがインストールされていることを確認します。 最初のコネクタを正常にインストールすると、Azure AD アプリケーション プロキシ サービスが自動的に有効になります。

## <a name="connector-configuration"></a>コネクタの構成

### <a name="why-is-my-connector-still-using-an-older-version-and-not-auto-upgraded-to-latest-version"></a>コネクタでまだ古いバージョンが使用されていて、最新バージョンに自動アップグレードされていないのはなぜですか?

アップデーター サービスが正常に動作していないか、このサービスでインストールできる新しい更新プログラムがないことが原因の可能性があります。

アップデーター サービスが実行されていて、イベント ログにエラーが記録されていない場合は、正常です ([アプリケーションとサービス ログ] -> [Microsoft] -> [AadApplicationProxy] -> [アップデーター] -> [管理者])。 

> [!IMPORTANT]
> 自動アップグレードでは、メジャー バージョンのみがリリースされます。 定期的なスケジュールでコネクタを手動で更新することをお勧めします。 新しいリリース、リリースの種類 (ダウンロード、自動アップグレード)、バグ修正、および新機能の詳細については、「[Azure AD アプリケーション プロキシ: バージョンのリリース履歴](application-proxy-release-version-history.md)」を参照してください。

コネクタを手動でアップグレードするには、次の手順を実行します。

-  最新バージョンのコネクタをダウンロードします。 Azure portal の [アプリケーション プロキシ] の下に表示されます。 「[Azure AD アプリケーション プロキシ: バージョンのリリース履歴](application-proxy-release-version-history.md)」でもリンクを見つけることができます。
-   インストーラーによって、Azure AD アプリケーション プロキシのコネクタ サービスが再起動されます。 場合によっては、インストーラーですべてのファイルを置き換えることができない場合、サーバーの再起動が必要になることがあります。 そのため、アップグレードを開始する前に、すべてのアプリケーション (つまり、イベント ビューアー) を終了することをお勧めします。
-   インストーラーを実行します。 アップグレード プロセスは迅速であり、資格情報を提供する必要がないため、コネクタは再登録されません。

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>アプリケーション プロキシ コネクタ サービスを、既定とは異なるユーザー コンテキストで実行することはできますか。

いいえ、このシナリオはサポートされていません。 既定の設定は次のとおりです。

- Microsoft AAD アプリケーション プロキシ コネクタ - WAPCSvc - ネットワーク サービス
- Microsoft AAD Application Proxy Connector Updater - WAPCUpdaterSvc - NT Authority\System

### <a name="can-a-guest-user-with-the-global-administrator-or-the-application-administrator-role-register-the-connector-for-the-guest-tenant"></a>グローバル管理者またはアプリケーション管理者のロールを持つゲスト ユーザーは、(ゲスト) テナントにコネクタを登録できますか。

いいえ、現時点ではできません。 登録の試行は、常にユーザーのホーム テナントで行われます。

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>バックエンド アプリケーションが複数の Web サーバーでホストされ、ユーザー セッションの永続性 (持続性) が必要です。 セッションの永続化を実現するにはどうすればよいですか。 

推薦事項に関しては、「[アプリケーション プロキシ コネクタとアプリケーションの高可用性と負荷分散](application-proxy-high-availability-load-balancing.md)」を参照してください。

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>コネクタ サーバーから Azure へのトラフィックに対する TLS 終了 (TLS/HTTPS 検査またはアクセラレーション) はサポートされていますか。

アプリケーション プロキシ コネクタは、Azure に対して証明書ベースの認証を実行します。 TLS 終了 (TLS/HTTPS 検査またはアクセラレーション) は、この認証方法を中断するため、サポートされていません。 コネクタから Azure へのトラフィックは、TLS 終了を実行しているデバイスをすべてバイパスする必要があります。  

### <a name="is-tls-12-required-for-all-connections"></a>すべての接続に TLS 1.2 が必要ですか。
はい。 Application Proxy サービスでは、お客様にクラス最高の暗号化を提供するために、アクセスが TLS 1.2 プロトコルのみに制限されています。 これらの変更は段階的にロールアウトされ、2019 年 8 月 31 日以降に有効となりました。 すべてのクライアントとサーバーおよびブラウザーとサーバーの組み合わせが、TLS 1.2 を使用して Application Proxy サービスへの接続を維持するように更新されていることを確認してください。 これらには、Application Proxy を通じて公開されたアプリケーションにアクセスするためにユーザーが使用しているクライアントも含まれます。 便利な参考資料とリソースについては、「[Office 365 での TLS 1.2 に対する準備](/microsoft-365/compliance/prepare-tls-1.2-in-office-365)」を参照してください。

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>コネクタ サーバーとバックエンド アプリケーション サーバーの間に転送プロキシ デバイスを配置できますか。
はい。このシナリオは、コネクタ バージョン 1.5.1526.0 以降でサポートされています。 「[既存のオンプレミス プロキシ サーバーと連携する](application-proxy-configure-connectors-with-proxy-servers.md)」を参照してください。

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>コネクタを Azure AD アプリケーション プロキシに登録するための専用アカウントを作成する必要がありますか。

行う必要はありません。 すべてのグローバル管理者アカウントまたはアプリケーション管理者アカウントが利用できます。 インストール時に入力した資格情報は、登録プロセスの後は使用されません。 代わりに、その後の認証に使用される証明書がコネクタに発行されます。

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Azure AD アプリケーション プロキシ コネクタのパフォーマンスを監視するにはどうすればよいですか。

コネクタと一緒にインストールされるパフォーマンス モニター カウンターが利用できます。 表示するには、次の手順に従います。  

1. **[スタート]** を選択し、「Perfmon」と入力して、ENTER キーを押します。
2. **[パフォーマンス モニター]** を選択し、緑 **+** のアイコンをクリックします。
3. 監視する **Microsoft AAD アプリケーション プロキシ コネクタ** カウンターを追加します。

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Azure AD アプリケーション プロキシ コネクタは、リソースと同じサブネット上にある必要がありますか。

コネクタは、同じサブネット上に存在している必要はありません。 ただし、リソースへの名前解決 (DNS、ホスト ファイル) と必要なネットワーク接続 (リソースへのルーティング、リソースのポート開放など) が必要です。 推薦事項に関しては、「[Azure Active Directory アプリケーション プロキシを使用する場合のネットワーク トポロジに関する注意事項](application-proxy-network-topology.md)」を参照してください。

### <a name="what-versions-of-windows-server-can-i-install-a-connector-on"></a>コネクタをインストールできる Windows Server のバージョンを教えてください。

アプリケーション プロキシには、Windows Server 2012 R2 以降が必要です。 現時点、Windows Server 2019 では HTTP2 に対して制限があります。 Windows Server 2019 でコネクタを正常に使用するには、次のレジストリ キーを追加し、サーバーを再起動する必要があります。

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
```

## <a name="application-configuration"></a>アプリケーションの構成

### <a name="i-am-receiving-an-error-about-an-invalid-certificate-or-possible-wrong-password"></a>証明書が無効か、パスワードが間違っている可能性があるというエラーが返されます

SSL 証明書のアップロード後に、ポータルに「証明書が無効であるか、パスワードが間違っている可能性がある」というメッセージが表示されます。

このエラーの問題解決に使用できるヒントを、次にいくつか示します。
- 証明書に問題がないことを確認します。 それをお使いのローカル コンピューターにインストールします。 問題が発生しなかった場合、その証明書には問題はありません。
- パスワードに特殊文字が含まれないことを確認します。 テストする場合、パスワードには 0 から 9、A から Z、a から z の文字のみが含まれている必要があります。
- 証明書が Microsoft ソフトウェア キー格納プロバイダーで作成されている場合は、RSA アルゴリズムが使用されている必要があります。

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>既定値と「長い」バックエンド タイムアウトの長さはどれくらいですか。 タイムアウトは延長できますか。

既定の長さは 85 秒です。 「長い」設定は 180 秒です。 タイムアウト制限を延長することはできません。

### <a name="can-a-service-principal-manage-application-proxy-using-powershell-or-microsoft-graph-apis"></a>サービス プリンシパルでは、Powershell または Microsoft Graph API を使用して Application Proxy を管理できますか。

いいえ。現在これはサポートされていません。

### <a name="what-happens-if-i-delete-cwap_authsecret-the-client-secret-in-the-app-registration"></a>アプリの登録で CWAP_AuthSecret (クライアント シークレット) を削除するとどうなりますか。

クライアント シークレット (*CWAP_AuthSecret* とも呼ばれます) は、Azure AD アプリケーション プロキシ アプリが作成されるときに、アプリケーション オブジェクトに自動的に追加されます (アプリの登録)。

クライアント シークレットは 1 年間有効です。 現在有効なクライアント シークレットの有効期限が切れる前に、新しい 1 年間有効のクライアント シークレットが自動的に作成されます。 アプリケーション オブジェクトには、常に 3 つの CWAP_AuthSecret クライアント シークレットが保持されます。 

> [!IMPORTANT]
> CWAP_AuthSecret を削除すると Azure AD アプリケーション プロキシの事前認証が中断します。 CWAP_AuthSecret は削除しないでください。

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>アプリケーションで読み込むランディング ページを変更する方法を教えてください。

[アプリケーションの登録] ページで、ホームページの URL を任意のランディング ページの外部 URL に変更できます。 アプリケーションがマイ アプリまたは Office 365 ポータルから起動されると、指定したページが読み込まれます。 構成手順については、「[Azure AD アプリケーション プロキシを使用して、発行されたアプリのカスタム ホーム ページを設定する](./application-proxy-configure-custom-home-page.md)」を参照してください

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>公開できるのは IIS ベースのアプリケーションのみですか。 Windows 以外の Web サーバーで実行されている Web アプリケーションを公開することはできますか。 コネクタを IIS がインストールされているサーバーにインストールする必要はありますか。

いいえ。公開済みのアプリケーションには IIS の必要条件はありません。 Windows Server 以外のサーバーで実行されている Web アプリケーションを公開できます。 ただし、Web サーバーがネゴシエート (Kerberos 認証) をサポートしていない場合、Windows 以外のサーバーで事前認証を使用できない場合があります。 コネクタがインストールされているサーバーでは、IIS は必要ありません。

### <a name="can-i-configure-application-proxy-to-add-the-hsts-header"></a>HSTS ヘッダーを追加するようにアプリケーション プロキシを構成できますか。
アプリケーション プロキシでは、HTTP の Strict-Transport-Security ヘッダーは HTTPS 応答に自動的に追加されませんが、公開されたアプリケーションによって送信された元の応答にそのヘッダーがある場合、それは保持されます。 この機能を有効にするための設定の証明は、ロードマップ上にあります。 これを応答に追加できるようにするプレビューに関心がある場合は、aadapfeedback@microsoft.com に詳細をお問い合わせください。

## <a name="integrated-windows-authentication"></a>統合 Windows 認証

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Kerberos の制約付き委任 (KCD) の設定時に PrincipalsAllowedToDelegateToAccount メソッドを使用する必要があるのはどのような場合ですか。

PrincipalsAllowedToDelegateToAccount メソッドは、コネクタ サーバーが Web アプリケーション サービス アカウントとは異なるドメインにある場合に使用します。 このメソッドでは、リソースベースの制約付き委任を使用する必要があります。
コネクタサーバーと Web アプリケーション サービス アカウントが同じドメインにある場合は、[Active Directory ユーザーとコンピューター] を使用して、各コネクタ コンピューター アカウントで委任設定を構成し、ターゲット SPN に委任することができます。

コネクタ サーバーと Web アプリケーション サービス アカウントが異なるドメインにある場合は、リソース ベースの委任が使用されます。 委任されたアクセス許可は、ターゲット Web サーバーと Web アプリケーション サービス アカウントで構成されます。 この制約付き委任方法は、比較的新しいものです。 この方法は、リソース (Web サービス) 所有者が、委任できるコンピューターとサービス アカウントを制御できるようにすることで、ドメイン間の委任に対応した Windows Server 2012 で導入されました。 この構成に使用できる UI はないため、PowerShell を使用する必要があります。
詳細については、「[アプリケーション プロキシを使った Kerberos の制約付き委任](https://aka.ms/kcdpaper)」に関するホワイトペーパーを参照してください。

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>NTLM 認証は Azure AD アプリケーション プロキシと連携していますか。

NTLM 認証は、事前認証またはシングル サインオンの方法としては使用できません。 NTLM 認証は、クライアントと発行された Web アプリケーションの間で直接ネゴシエートできる場合にのみ使用できます。 NTLM 認証を使用すると、通常、ブラウザーにサインイン プロンプトが表示されます。

### <a name="can-i-use-the-logon-identity-on-premises-user-principal-name-or-on-premises-sam-account-name-in-a-b2b-iwa-single-sign-on-scenario"></a>B2B IWA シングル サインオンのシナリオで、ログオン ID "オンプレミスのユーザー プリンシパル名" または "オンプレミスの SAM アカウント名" を使用できますか。

いいえ。Azure AD のゲスト ユーザーには、前述のログオン ID で必要な属性がないため、これは機能しません。

この場合、"ユーザー プリンシパル名" へのフォールバックが発生します。 B2B シナリオの詳細については、「[Azure AD の B2B ユーザーにオンプレミスのアプリケーションへのアクセスを許可する](../external-identities/hybrid-cloud-to-on-premises.md)」を参照してください。

## <a name="pass-through-authentication"></a>パススルー認証

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>パススルー認証で公開されたアプリケーションに対して条件付きアクセス ポリシーを使用できますか。

条件付きアクセス ポリシーは、Azure AD で事前に認証されたユーザーにのみ適用されます。 パススルー認証では Azure AD 認証がトリガーされないため、条件付きアクセス ポリシーを適用することはできません。 パススルー認証では、可能であればオンプレミス サーバーに MFA ポリシーを実装するか、Azure AD アプリケーション プロキシで事前認証を有効にする必要があります。

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>クライアント証明書の認証要件が設定されている Web アプリケーションを公開できますか。

いいえ。アプリケーション プロキシは TLS トラフィックを終了するため、このシナリオはサポートされていません。  

## <a name="remote-desktop-gateway-publishing"></a>リモート デスクトップ ゲートウェイ 公開

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用してリモート デスクトップ ゲートウェイを公開するにはどうすればよいですか。

「[Azure AD アプリケーション プロキシを使用したリモート デスクトップの公開](application-proxy-integrate-with-remote-desktop-services.md)」を参照してください。

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>リモート デスクトップ ゲートウェイの公開シナリオで Kerberos の制約付き委任 (シングル サインオン - Windows 統合認証) は使用できますか。

いいえ、このシナリオはサポートされていません。  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>ユーザーが Internet Explorer 11 を使用しておらず、事前認証のシナリオを使用できません。 これは想定される動作ですか。

はい、そうです。 事前認証のシナリオでは ActiveX コントロールが必要ですが、サード パーティのブラウザーではサポートされていません。

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>リモート デスクトップ Web クライアント (HTML5) はサポートされていますか。

はい、このシナリオは現在パブリック プレビュー段階です。 「[Azure AD アプリケーション プロキシを使用したリモート デスクトップの公開](application-proxy-integrate-with-remote-desktop-services.md)」を参照してください。

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>事前認証のシナリオの構成後に、ユーザーは 2 回認証する必要がある (1 回目は Azure AD サインイン フォーム、2 回目は RDWeb サインイン フォーム) ことがわかりました。 これは想定される動作ですか。 サインインの回数を 1 回に減らすにはどうすればよいですか。

はい、想定される動作です。 ユーザーが Azure AD 参加済みコンピューターを使用している場合、自動的に Azure AD にサインインします。 ユーザーが資格情報を提供する必要があるのは、RDWeb サインイン フォームのみです。

## <a name="sharepoint-publishing"></a>SharePoint の公開

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用して SharePoint を公開するにはどうすればよいですか。

「[Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化](application-proxy-integrate-with-sharepoint-server.md)」を参照してください。

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>SharePoint モバイル アプリ (iOS/Android) を使用して、公開された SharePoint サーバーにアクセスできますか。

[SharePoint モバイルアプリ](/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises)では、現在、Azure Active Directory 事前認証はサポートされていません。

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Active Directory フェデレーション サービス (AD FS) の公開 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Azure AD アプリケーション プロキシを AD FS プロキシ (Web アプリケーション プロキシなど) として使用できますか。

いいえ。 Azure AD アプリケーション プロキシは Azure AD で動作するように設計されているため、AD FS プロキシとして機能するための要件を満たしていません。

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense-and-remote-desktop-web-client-html5"></a>WebSocket のサポートは、QlikSense やリモート デスクトップ Web クライアント (HTML5) 以外のアプリケーションでも有効ですか。

現在、WebSocket プロトコルのサポートはパブリック プレビュー段階であり、他のアプリケーションでは機能しない可能性があります。 一部のお客様は、他のアプリケーションと WebSocket プロトコルを組み合わせることで成功した事例もあります。 このようなシナリオをテストする場合は、ぜひ結果についてご報告ください。 フィードバックについては、aadapfeedback@microsoft.comまでご連絡ください。

Windows Admin Center (WAC) の機能 (イベント ログ、PowerShell、リモート デスクトップ サービス) は現在、Azure AD アプリケーション プロキシ経由では動作しません。

## <a name="link-translation"></a>リンク変換

### <a name="does-using-link-translation-affect-performance"></a>リンク変換の使用はパフォーマンスに影響しますか。

はい。 リンク変換の使用はパフォーマンスに影響します。 アプリケーション プロキシ サービスは、アプリケーションでハードコードされたリンクをスキャンし、ユーザーに公開する前にそれぞれ公開されている外部 URL で置換します。 

最適なパフォーマンスを得るには、[カスタム ドメイン](./application-proxy-configure-custom-domain.md)を構成して、同じ内部 URL と外部 URL を使用することをお勧めします。 カスタム ドメインを使用できない場合は、マイ アプリによるセキュリティで保護されたサインイン拡張機能またはモバイルの Microsoft Edge ブラウザーを使用することで、リンク変換のパフォーマンスを向上させることができます。 「[Azure AD アプリケーション プロキシで公開されているアプリのハードコードされたリンクをリダイレクトする](application-proxy-configure-hard-coded-link-translation.md)」を参照してください。

## <a name="wildcards"></a>ワイルドカード

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>ワイルドカードを使用して、共通のカスタム ドメイン名と異なるプロトコル (HTTP 用と HTTPS 用それぞれ 1 つ) を持つ 2 つのアプリケーションを公開するにはどうすればよいですか。

このシナリオは直接はサポートされていません。 このシナリオで使用できるオプションは次のとおりです。

1. ワイルドカードを使用して HTTP と HTTPS の両方の URL を個別のアプリケーションとして公開し、それぞれに異なるカスタム ドメインを指定します。 この構成は、外部 URL が異なるため可能です。

2. ワイルドカード アプリケーションを使用して HTTPS URL を公開します。 次のアプリケーション プロキシの PowerShell コマンドレットを使用して、HTTP アプリケーションを個別に公開します。
   - [アプリケーション プロキシ アプリケーションの管理](/powershell/module/azuread/#application_proxy_application_management&preserve-view=true)
   - [アプリケーション プロキシ コネクタの管理](/powershell/module/azuread/#application_proxy_connector_management&preserve-view=true)
