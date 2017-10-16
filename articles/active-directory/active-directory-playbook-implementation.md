---
title: "Azure Active Directory の PoC 戦略の実装 | Microsoft Docs"
description: "ID とアクセスを管理するシナリオを確認して、迅速に実装します"
services: active-directory
keywords: "Azure Acitve Directory, 戦略, 概念実証, PoC"
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 10877ee33ec04cf0d350417af59d598eab249aa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-implementation"></a>Azure Active Directory の概念実証戦略: 実装

## <a name="foundation---syncing-ad-to-azure-ad"></a>基礎 - AD を Azure AD に同期する 

既にオンプレミスのディレクトリを持つ企業顧客のほとんどにとって、基礎となるのはハイブリッド ID です。 ここでの目標は、意図的にできるだけ時間をかけずに、実際の ID およびアクセス シナリオの価値を示すことにあります。 

| シナリオ | 構成要素| 
| --- | --- |  
| [オンプレミスの ID をクラウドに拡張する](#extending-your-on-premises-identity-to-the-cloud) | [ディレクトリ同期 - パスワード ハッシュ同期](active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation) <br/>**注**: 既に DirSync/ADSync または以前のバージョンの Azure AD Connect がある場合、この手順は省略できます。 このガイドの一部のシナリオでは、新しいバージョンの Azure AD Connect が必要になることがあります。  <br/>[ブランド](active-directory-playbook-building-blocks.md#branding) | 
| [グループを使用して Azure AD ライセンスを割り当てる](#assigning-azure-ad-licenses-using-groups) | [グループ ベースのライセンス](active-directory-playbook-building-blocks.md#group-based-licensing) |


### <a name="extending-your-on-premises-identity-to-the-cloud"></a>オンプレミスの ID をクラウドに拡張する 

1. Bob は Contoso 社の Active Directory 管理者です。 彼は、一連のユーザーを対象として、サービスとしての ID を有効にしようとしています。 ターゲット ユーザーの ID は、Azure AD Connect ウィザードの実行後にクラウドで使用できます。 
2. Bob が、ターゲット ユーザーの 1 人である Susie に、Azure Active Directory アクセス パネルにアクセスし、認証できることを確認するよう依頼します。 Susie にはブランド化されたログイン ページと空のアクセス パネルが表示されます。アクセス パネルはアプリケーション アクセスを有効にするための準備が整っています。

### <a name="assigning-azure-ad-licenses-using-groups"></a>グループを使用して Azure AD ライセンスを割り当てる 

1. Azure AD の全体管理者である Bob が、Azure AD の初期ロールアウトの一環として、Azure AD のライセンスを、特定のユーザーに割り当てたいと考えています。
2. Bob が、パイロット ユーザーのグループを作成します。 
3. Bob が、そのグループにライセンスを割り当てます
4. インフォメーション ワーカーである Susie が、彼女の職務の一部としてセキュリティ グループに追加されます
5. しばらくすると、Susie が Azure AD Premium ライセンスにアクセスできます。 これにより、その他の POC シナリオが後で有効になります。

## <a name="theme---lots-of-apps-one-identity"></a>テーマ - 多くのアプリ、1 つの ID

| シナリオ | 構成要素| 
| --- | --- |  
| [SaaS アプリケーションを統合する - フェデレーション SSO](#integrate-saas-applications---federated-sso) | [SaaS フェデレーション SSO 構成](active-directory-playbook-building-blocks.md#saas-federated-sso-configuration) <br/>[グループ - 委任された所有権](active-directory-playbook-building-blocks.md#groups---delegated-ownership) |
| [SaaS アプリケーションを統合する - パスワード SSO](#integrate-saas-applications---password-sso) | [SaaS パスワード SSO 構成](active-directory-playbook-building-blocks.md#saas-password-sso-configuration) |
| [SSO と ID のライフサイクル イベント](#sso-and-identity-lifecycle-events) | [SaaS と ID のライフサイクル](active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle) |
| [共有アカウントへのアクセスをセキュリティで保護する](#secure-access-to-shared-accounts) | [SaaS 共有アカウント構成](active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration) |
| [オンプレミスのアプリケーションへのリモート アクセスをセキュリティで保護する](#secure-remote-access-to-on-premises-applications) | [アプリのプロキシ構成](active-directory-playbook-building-blocks.md#app-proxy-configuration) |
| [Azure AD に LDAP ID を同期する](#synchronize-ldap-identities-to-azure-ad) |  [Generic LDAP コネクタ構成](active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration) |

### <a name="integrate-saas-applications---federated-sso"></a>SaaS アプリケーションを統合する - フェデレーション SSO 

1. Azure AD の全体管理者である Bob が、マーケティング部門からの依頼により、ServiceNow インスタンスへのアクセスを有効にしようとしています。 Bob が Azure AD ドキュメントに記載されているチュートリアルの詳しい手順に従って、アプリへのユーザーの割り当てを、マーケティング チームのリーダーである Kevin に委任します。 
2. Kevin が、ServiceNow の資格の所有者としてログインし、Susie をアプリに割り当てます。 Susie のプロファイルは ServiceNow で自動的に作成されていることもわかります。
3. Susie はマーケティング部門のインフォメーション ワーカーです。 彼女が Azure AD にログインすると、自身が割り当てられた SaaS アプリケーションがすべて myapps ポータルに表示されています。 ここから、Susie はシームレスに ServiceNow にアクセスできます。
4. マーケティング部門は、誰が ServiceNow にアクセスしたかを監査する必要があります。 Bob がアクティビティ レポートをダウンロードし、そのレポートを電子メールで Kevin と共有します。  

### <a name="sso-and-identity-lifecycle-events"></a>SSO と ID のライフサイクル イベント

1. Susie が休暇をとっており、オンプレミスの AD アカウントは、企業ポリシーに従って一時的に無効になっています。 Susie は現在 Azure AD にログインできないため、ServiceNow にアクセスできません。 
2. Susie がマーケティング部門からセールス部門に異動になりました。 Kevin が、ServiceNow に対する Susie のアクセス権を削除します。 Susie が Azure AD の myapps にログインにしても、ServiceNow タイルは表示されません。 10 分後、Kevin は、Susie のアカウントが ServiceNow 管理コンソールで無効になっていることを確認します。

### <a name="integrate-saas-applications---password-sso"></a>SaaS アプリケーションを統合する - パスワード SSO

1. Bob が、Atlassian HipChat へのアクセスを構成します。 HipChat にはパスワード SSO が統合されており、これにより Susie へのアクセスが許可されます
2. Susie が myapps ポータルにログインすると、Azure AD IE ブラウザー拡張機能をダウンロードするためのリンクが表示されます。これをダウンロードします
3. Susie がそのリンクをクリックすると、HipChat ユーザー名とパスワード資格情報を入力するよう求められます。 これは 1 回限りの操作で、この操作が完了すると、HipChat にアクセスできます
4. 数日後、Susie は myapps ポータルを開き、HipChat をもう一度クリックします。 今回は、シームレスにアクセスできます
5. HipChat アプリケーションの所有者である Kevin が、アプリケーションにアクセスしたユーザーを監査したいと考えています。 Bob が監査レポートをダウンロードし、そのレポートを電子メールで Kevin と共有します。 

### <a name="secure-access-to-shared-accounts"></a>共有アカウントへのアクセスをセキュリティで保護する 

1. Bob の仕事は、セールス チームのメンバーの共有 Twitter ハンドルをセキュリティで保護することです。 Bob は、SSO アプリケーションとして Twitter を追加し、セールス チームのセキュリティ グループに割り当てます。 Bob には共有アカウントに対する資格情報が提供され、その情報をシステムに入力します。 
2. Twitter の資格情報を共有すると、複数の人がその情報にアクセスできるため、信頼性が失われます。 Bob が、Twitter のパスワードの自動ロールオーバーを有効にします。
3. セールス チームのメンバーである Susie が myapps ポータルにログインすると、Azure AD IE ブラウザー拡張機能をダウンロードするためのリンクが表示されます。 Susie はその拡張機能をインストールします。
4. Susie は、クリックすることで Twitter に直接アクセスできます。 Susie はパスワードを知りません。
5. Arnold もセールス チームのメンバーです。 Arnold は、手順 3. ～ 4. の Susie と同じ操作を行います
6. セールス部門は、誰が Twitter にアクセスしたかを監査する必要があります。 Bob がアクティビティ レポートをダウンロードし、そのレポートを電子メールで Kevin と共有します。 

### <a name="secure-remote-access-to-on-premises-applications"></a>オンプレミスのアプリケーションへのリモート アクセスをセキュリティで保護する

1. Azure AD の全体管理者である Bob は、経費アプリケーションなどの便利なオンプレミス リソースのいくつかにリモートで働く従業員がアクセスできるようにしてほしいという依頼を何度も受けています。 Bob は[アプリケーション プロキシのドキュメント](active-directory-application-proxy-enable.md)に従ってコネクタをインストールし、経費アプリケーションをアプリケーション プロキシ アプリケーションとして公開します。 
2. Bob が、経費アプリケーションの外部 URL を、リモート アクセスを必要とする従業員の 1 人である Susie と共有します。 Susie はリンクにアクセスし、AAD に対して認証を行った後、経費アプリにアクセスして、リモートから引き続き生産的に作業を行います。 
3. Bob はその後引き続き同じプロセスで追加のオンプレミス アプリケーションを公開し、必要に応じてユーザーにアクセス権を付与します。 また、機密性の高いアプリケーションを発行する場合は、そのアプリケーションに対して条件付きアクセスと多要素認証を追加して、セキュリティを強化します。

### <a name="synchronize-ldap-identities-to-azure-ad"></a>Azure AD に LDAP ID を同期する

1. Bob の会社の ID インフラストラクチャは複雑です。 ほとんどのユーザーが Windows Server Active Directory Domain Services (ADDS) 内に保持されています。 一部のユーザーは、Active Directory ライトウェイト ディレクトリ サービス (ADLDS) 内の HR システムで管理されています。
2. Bob の仕事は、(ADDS に含まれないユーザーを含め) すべてのユーザーが SaaS アプリにアクセスできるようにすることです。
3. Bob が、Azure AD Connect で ADLDS からデータをプルするように汎用 LDAP コネクタを構成します。
4. Bob が同期規則を作成します。これにより、LDAP ユーザーがメタバースと Azure AD に設定されます
5. LDAP ユーザーである Susie が、同期済み ID を使用して、自身の SaaS アプリにアクセスします



> [!IMPORTANT] 
> これは高度な構成で、FIM/MIM に関する知識を必要とします。 運用環境で使用されている場合、この構成に関するご質問については、[Premier サポート](https://support.microsoft.com/premier)を使ってお問い合わせください。



## <a name="theme---increase-your-security"></a>テーマ - セキュリティを向上させる 

| シナリオ | 構成要素| 
| --- | --- |  
| [管理者アカウントのアクセスをセキュリティで保護する](#secure-administrator-account-access) | [電話を使用した Azure MFA](active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls) |
| [アプリケーション アクセスをセキュリティで保護する](#secure-access-to-applications) | [SaaS アプリケーション向けの条件付きアクセス](active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications) |
| [ジャスト イン タイム管理を有効にする](#enable-just-in-time-jit-administration) | [Privileged Identity Management](active-directory-playbook-building-blocks.md#privileged-identity-management-pim) |
| [リスクに基づいて ID を保護する](#protect-identities-based-on-risk) | [リスク イベントを検出する](active-directory-playbook-building-blocks.md#discovering-risk-events) <br/>[サインイン リスク ポリシーをデプロイする](active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies) |
| [証明書ベースの認証により、パスワードを使用せずに認証する](#authenticate-without-passwords-using-certificate-based-authentication) | [証明書ベースの認証を構成する](active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication)

### <a name="secure-administrator-account-access"></a>管理者アカウントのアクセスをセキュリティで保護する

1. Bob は Azure AD の全体管理者です。 彼は Stuart をサービスの共同管理者にすることにしました。 
2. Bob は、セキュリティ体制を強化するため、常に MFA が求められるように Stuart のアカウントを構成します
3. Stuart が Azure Portal にログインすると、ログインを続行するために自分の電話番号を登録する必要があることがわかります
4. 以降の Stuart からのログインは Multi-factor Authentication で保護され、電話による本人確認が行われます。

### <a name="secure-access-to-applications"></a>アプリケーションへのアクセスをセキュリティで保護する

1. Kevin は ServiceNow のビジネス オーナーです。 会社は今後、企業ネットワーク外からアクセスするユーザーに対し MFA によるログインを要求したいと考えています。
2. Azure AD の全体管理者 Bob が、条件付きアクセス ポリシーを ServiceNow アプリケーションに追加し、外部アクセスに対して MFA を有効にします
3. インフォメーション ワーカー Susie が myapps ポータルにログインし、ServiceNow タイルをクリックします。 MFA を求められるようになりました。

### <a name="enable-just-in-time-jit-administration"></a>ジャスト イン タイム (JIT) 管理を有効にする

1. Bob と Stuart は Azure AD の全体管理者です。 管理ロールへの JIT アクセスを有効にして、さらに、特権ロールの使用状況も記録したいと考えています。
2. Bob が Azure AD テナントで PIM を有効にして、セキュリティ管理者になります。 Bob 自身と Stuart の全体管理者ロールのメンバーシップを、永続的から対象的に変更します。
3. 以降、Bob と Stuart が Azure AD 構成を変更するには、その前に Azure Portal で自身のロールをアクティブ化する必要があります。 

### <a name="protect-identities-based-on-risk"></a>リスクに基づいて ID を保護する 

1. インフォメーション ワーカーである Susie が、Tor ブラウザーからログインしようとします。 
2. Bob が Azure AD Identity Protection ダッシュボードをチェックし、匿名 IP アドレスから Susie がログインしようとしているのを見つけます。 セキュリティ チームは、こうしたユーザー アクセスに対して MFA を要求したいと考えています
3. Bob が Azure AD Identity Protection ポリシーを有効にして、"中" 以上のリスク イベントに対して MFA を要求します
4. しばらくしてから、Susie が Tor ブラウザーから再度ログインします。 今回は MFA が求められます

### <a name="authenticate-without-passwords-using-certificate-based-authentication"></a>証明書ベースの認証により、パスワードを使用せずに認証する

1. 金融機関の全体管理者である Bob が、アプリケーションの認証要素としてのパスワードの使用を禁止します。
2. Bob は ADFS および Azure AD で証明書認証を有効にして、実施します
3. Susie がアプリケーションにアクセスすると、証明書を使用して認証するよう求められます

## <a name="theme---scale-with-self-service"></a>テーマ - セルフサービスでスケーリングする

| シナリオ | 構成要素| 
| --- | --- |  
| [セルフサービスのパスワード リセット](#self-service-password-reset) | [セルフサービスのパスワード リセット](active-directory-playbook-building-blocks.md#self-service-password-reset) |
| [セルフサービスでアプリケーションにアクセスする](#self-service-access-to-applications) | [セルフサービスでアプリケーションにアクセスする](active-directory-playbook-building-blocks.md#self-service-access-to-application-management) |

### <a name="self-service-password-reset"></a>セルフサービスのパスワード リセット 

1. Azure AD の全体管理者である Bob が、Susie など、ユーザーのサブセットに対してセルフサービスのパスワード管理を有効にします。 
2. Susie が myapps ポータルにログインすると、今後のパスワード リセット イベントに対してセキュリティ情報を登録するよう求めるメッセージが表示されます。
3. 数日後、Susie がパスワードを忘れ、Azure AD ポータルからリセットします

### <a name="self-service-access-to-applications"></a>セルフサービスでアプリケーションにアクセスする 

1. Kevin は ServiceNow のビジネス オーナーです。 ユーザーをまとめて追加するのではなく、必要に応じてユーザーに "サインアップ" してほしいと考えています
2. Azure AD の全体管理者である Bob が、ServiceNow アプリケーションを変更して、セルフサービス要求を有効にします
3. インフォメーション ワーカーである Susie が myapps ポータルにログインして、"アプリケーションの追加" ボタンをクリックすると、ServiceNow が推奨アプリケーションとして 表示されます。 その後、myapps ポータルに戻ると、ServiceNow アプリケーションが表示されます。

[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]