---
title: Azure Active Directory の認証管理の運用リファレンス ガイド
description: この運用リファレンス ガイドでは、認証管理をセキュリティで保護するために実行する必要がある検査とアクションについて説明します
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 85281088692d1c4b0245eb9d069519198f8f315d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919343"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory の認証管理の運用リファレンス ガイド

[Azure AD 運用リファレンス ガイド](active-directory-ops-guide-intro.md)のこのセクションでは、企業のセキュリティ体制に基づいた資格情報のセキュリティ保護と管理、認証エクスペリエンスの定義、割り当ての委任、使用状況の測定、およびアクセス ポリシーの定義に必要な検査と操作について説明します。

> [!NOTE]
> これらの推奨事項は公開日の時点で最新ですが、時間の経過と共に変化する可能性があります。 Microsoft の製品とサービスは時間の経過と共に進化するため、継続的に ID プラクティスを評価する必要があります。

## <a name="key-operational-processes"></a>主要な運用プロセス

### <a name="assign-owners-to-key-tasks"></a>主要タスクに所有者を割り当てる

Azure Active Directory を管理するには、ロールアウト プロジェクトに含まれていない主要な運用タスクとプロセスを継続的に実行する必要があります。 環境を最適化するために、これらのタスクを設定することも重要です。 主なタスクと推奨される所有者は次のとおりです。

| タスク | Owner |
| :- | :- |
| Azure AD のシングル サインオン (SSO) の構成のライフサイクルを管理する | IAM 運用チーム |
| Azure AD アプリケーションの条件付きアクセス ポリシーを策定する | InfoSec アーキテクチャ チーム |
| SIEM システムでサインイン アクティビティをアーカイブする | InfoSec 運用チーム |
| SIEM システムでリスク イベントをアーカイブする | InfoSec 運用チーム |
| セキュリティ レポートについてトリアージおよび調査する | InfoSec 運用チーム |
| リスク イベントについてトリアージおよび調査する | InfoSec 運用チーム |
| Azure AD Identity Protection からのリスクと脆弱性のレポートでフラグが設定されたユーザーをトリアージして調査する | InfoSec 運用チーム |

> [!NOTE]
> Azure AD Identity Protection には、Azure AD Premium P2 ライセンスが必要です。 要件に対する適切なライセンスを確認するには、 [Azure AD Free および Azure AD Premium エディションの一般公開されている機能の比較](https://azure.microsoft.com/pricing/details/active-directory/)に関するページをご覧ください。

リストを確認する際には、所有者が空のタスクに所有者を割り当てるか、上記の推奨事項に一致しない所有者を持つタスクの所有権を調整する必要がある場合があります。

#### <a name="owner-recommended-reading"></a>所有者に関する推奨資料

- [Azure Active Directory での管理者ロールの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure でのガバナンス](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>資格情報の管理

### <a name="password-policies"></a>パスワード ポリシー

パスワードを安全に管理することは、ID およびアクセス管理の最も重要な部分の 1 つであり、多くの場合は攻撃の最大の標的です。 Azure AD は、攻撃が成功するのを防ぐために役立ついくつかの機能をサポートしています。

次の表を使用して、対処が必要な問題を軽減するための推奨される解決策を見つけてください。

| 問題 | 推奨 |
| :- | :- |
| 脆弱なパスワードから保護するためのメカニズムがない | Azure AD の[パスワード リセットのセルフサービス (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) と[パスワード保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)を有効にします |
| 漏洩したパスワードを検出するためのメカニズムがない | [パスワード ハッシュ同期](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) を有効にして分析情報を取得します |
| AD FS を使用し、管理された認証に移行できない | [AD FS エクストラネットのスマート ロックアウト](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)や [Azure AD スマート ](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)を有効にします |
| パスワード ポリシーで、長さ、複数の文字セット、有効期限などの複雑なものに基づくルールを使用している | [Microsoft の推奨プラクティス](https://aka.ms/passwordguidance)を優先して再度検討し、アプローチをパスワード管理に切り替えて、[Azure AD のパスワード保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)をデプロイしてください。 |
| ユーザーが多要素認証 (MFA) を使用するように登録されていない | [すべてのユーザーのセキュリティ情報を登録](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy)して、ユーザーのパスワードとともにユーザーの ID を検証するためのメカニズムとして使用できるようにします |
| パスワードの失効がユーザーのリスクに基づいていない | Azure AD [Identity Protection ユーザー リスク ポリシー](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)をデプロイして、SSPR を使用して漏洩した資格情報のパスワード変更を強制します |
| 識別された IP アドレスから行われる悪意のあるユーザーからの悪意のある認証から保護するための、スマート ロックアウト メカニズムがない | パスワード ハッシュ同期または[パススルー認証](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) のいずれかを含む、クラウドで管理される認証をデプロイします |

#### <a name="password-policies-recommended-reading"></a>パスワード ポリシーに関する推奨資料

- [Azure AD と AD FS のベスト プラクティス: パスワードのスプレー攻撃に対する防御 - Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>パスワード リセットのセルフサービスとパスワード保護を有効にする

パスワードを変更またはリセットする必要があるユーザーは、ヘルプ デスクへの問い合わせの量とコストの最大の発生源の 1 つです。 コストに加え、ユーザーのリスクを軽減するためのツールとしてパスワードを変更することは、組織のセキュリティ体制を改善するための基本的な手順です。

少なくとも、Azure AD の[パスワード リセットのセルフサービス](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) (SSPR) とオンプレミスの[パスワード保護](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) をデプロイして、次のことを行うことをお勧めします。

- ヘルプ デスクへの問い合わせをうまくそらす。
- 一時パスワードの使用を置き替える。
- オンプレミスのソリューションに依存している既存のセルフサービスのパスワード管理ソリューションを置き換える。
- 組織内の[脆弱なパスワードを排除する](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)。

> [!NOTE]
> Azure AD Premium P2 サブスクリプションを使用している組織では、SSPR をデプロイして、[Identity Protection ユーザー リスク ポリシー](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)の一部として使用することをお勧めします。

### <a name="strong-credential-management"></a>強力な資格情報の管理

パスワード自体は、悪意のあるユーザーによる環境へのアクセスを阻止するほど安全ではありません。 少なくとも、特権アカウントを持つすべてのユーザーが、多要素認証 (MFA) を有効にする必要があります。 理想的には、[統合された登録](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)を有効にし、すべてのユーザーに対して、[統合された登録エクスペリエンス](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview)を使用して MFA と SSPR に登録するように要求する必要があります。 最終的には、予期しない状況によるロックアウトのリスクを軽減するために、[回復性を提供する](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls)戦略を採用することをお勧めします。

![統合されたユーザー エクスペリエンスのフロー](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>オンプレミスの停止に対する認証の回復性

Azure AD のパスワード ハッシュ同期 (PHS) と Azure MFA を使用すると、よりシンプルで、漏洩した資格情報の検出が可能になるだけでなく、[NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/) などのサイバー攻撃によってオンプレミスの停止が発生しても、ユーザーが SaaS アプリケーションや Office 365 にアクセスできるようになります。 また、フェデレーションと組み合わせると、PHS を有効にすることもできます。 PHS を有効にすると、フェデレーション サービスが使用できない場合に認証のフォールバックが可能になります。

オンプレミスの組織に障害回復戦略がない場合や、Azure AD と統合されていない場合は、Azure AD PHS をデプロイし、PHS を含むディザスター リカバリー計画を定義する必要があります。 Azure AD PHS を有効にすると、オンプレミスの Active Directory が使用できない場合に、ユーザーが Azure AD に対して認証を行うことができるようになります。

![パスワード ハッシュの同期フロー](./media/active-directory-ops-guide/active-directory-ops-img5.png)

認証オプションに対する理解を深めるには、「[Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)」を参照してください。

### <a name="programmatic-usage-of-credentials"></a>プログラムによる資格情報の使用

PowerShell を使用する Azure AD スクリプトや Graph API を使用するアプリケーションには、セキュリティで保護された認証が必要です。 このようなスクリプトやツールを実行する資格情報の管理が不十分な場合は、資格情報の盗難のリスクが高まります。 ハードコーディングされたパスワードまたはパスワード プロンプトに依存するスクリプトまたはアプリケーションを使用している場合は、まず、構成ファイルまたはソース コード内のパスワードを確認してから、それらの依存関係を置き換えて、可能な限り Azure マネージド ID、統合 Windows 認証、または[証明書](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-access-api-with-certificates)を使用する必要があります。 以前のソリューションに可能性がないアプリケーションの場合は、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) の使用を検討してください。

パスワード資格情報を持つサービス プリンシパルがあることを確認し、それらのパスワード資格情報がスクリプトやアプリケーションによってどのように保護されているかわからない場合は、アプリケーションの所有者に問い合わせて使用パターンの理解を深めてください。

また、パスワード資格情報を持つサービス プリンシパルがある場合は、アプリケーション所有者に連絡して使用パターンを把握することもお勧めします。

## <a name="authentication-experience"></a>認証エクスペリエンス

### <a name="on-premises-authentication"></a>オンプレミスの認証

統合 Windows 認証 (IWA) を使用したフェデレーション認証や、パスワード ハッシュ同期またはパススルー認証を使用したシームレス シングル サインオン (SSO) のマネージド認証は、オンプレミスのドメイン コントローラーへの通信経路のある企業ネットワーク内の場合は最適なユーザー エクスペリエンスです。 これにより、資格情報プロンプトによる疲労が最小限に抑えられ、ユーザーがフィッシング攻撃の犠牲になるリスクが軽減されます。 PHS または PTA によるクラウドで管理された認証を既に使用していても、オンプレミスで認証するときにユーザーがパスワードを入力する必要がある場合は、すぐに[シームレス SSO をデプロイする](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)必要があります。 一方、クラウドで管理された認証に最終的に移行する予定がある場合は、移行プロジェクトの一環としてシームレス SSO を実装する必要があります。

### <a name="device-trust-access-policies"></a>デバイスの信頼のアクセス ポリシー

組織内のユーザーと同様、デバイスは保護対象となる主要なアイデンティティです。 デバイスの ID を使用して、いつでもどこからでもリソースを保護できます。 デバイスを認証し、その信頼の種類を考慮することで、次のようなセキュリティ体制と使用可能性が向上します。

- デバイスが信頼されている場合に、MFA などを使用して障害を回避する
- 信頼されていないデバイスからのアクセスをブロックする
- Windows 10 デバイスの場合は、[オンプレミスのリソースへのシングル サインオンをシームレスに](https://docs.microsoft.com/azure/active-directory/devices/azuread-join-sso)提供します。

この目標は、次のいずれかの方法を使用してデバイス ID を取り込んで Azure AD で管理することで実行できます。

- 組織は、[Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune) を使用してデバイスを管理し、コンプライアンス ポリシーを適用し、デバイスの正常性を証明し、デバイスが準拠しているかどうかに基づいて条件付きアクセス ポリシーを設定することができます。 Microsoft Intune では、iOS デバイス、Mac デスクトップ (JAMF 統合経由)、Windows デスクトップ (Windows 10 のモバイル デバイス管理のネイティブな使用、および Microsoft Endpoint Manager/System Center Configuration Manager との共同管理)、および Android モバイル デバイスを管理できます。
- [Hybrid Azure AD Join](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains) では、Active Directory ドメイン参加済みコンピューター デバイスがある環境で、グループ ポリシー、System Center Configuration Manager、または Microsoft Endpoint Manager を使用した管理を提供します。 組織は、シームレス SSO を使用した PHS または PTA のいずれかを使用して、マネージド環境をデプロイできます。 Azure AD に自分のデバイスを取り込むと、クラウドとオンプレミスのリソースでの SSO を使用したユーザーの生産性を最大化でき、同時に [条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) を使用したクラウドとオンプレミスのリソースへのアクセスをセキュリティで保護することができます。

クラウドに登録されていないドメイン参加済み Windows デバイス、またはクラウドに登録されていても条件付きアクセス ポリシーがないドメイン参加済み Windows デバイスがある場合は、登録されていないデバイスを登録する必要があり、いずれにしても条件付きアクセス ポリシーで[制御として Hybrid Azure AD Join を使用](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)する必要があります。

![ハイブリッド デバイスを必要とする条件付きアクセス ポリシーでの許可のスクリーンショット](./media/active-directory-ops-guide/active-directory-ops-img6.png)

MDM または Microsoft Intune を使用してデバイスを管理していても、条件付きアクセス ポリシーでデバイス制御を使用していない場合は、それらのポリシーで制御として [[デバイスは準拠としてマーク済みである必要がある]](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices#require-device-to-be-marked-as-compliant) を使用することをお勧めします。

![デバイス準拠を必要とする条件付きアクセス ポリシーでの許可のスクリーンショット](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>デバイスの信頼のアクセス ポリシーに関する推奨資料

- [方法:ハイブリッド Azure Active Directory 参加の実装の計画](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
- [ID とデバイスのアクセスの構成](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello for Business

Windows 10 では、[Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) によって PC 上のパスワードが強力な 2 要素認証に置き換えられます。 Windows Hello for Business を使用すると、ユーザーにとってより効率的な MFA エクスペリエンスを実現し、パスワードへの依存を減らすことができます。 Windows 10 デバイスのロールアウトを開始していない場合、または部分的にデプロイしただけの場合は、Windows 10 にアップグレードして、すべてのデバイスで [Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) を有効することをお勧めします。

パスワードレス認証の詳細については、「[Azure Active Directory でのパスワードレスの環境](https://aka.ms/passwordlessdoc)」を参照してください。

## <a name="application-authentication-and-assignment"></a>アプリケーションの認証と割り当て

### <a name="single-sign-on-for-apps"></a>アプリのシングル サインオン

標準化されたシングル サインオンのメカニズムを社内全体に提供することは、最良のユーザー エクスペリエンス、リスクの削減、報告する能力、およびガバナンスにとって不可欠です。 Azure AD で SSO をサポートしていても、現時点ではローカル アカウントを使用するように構成されているアプリケーションを使用している場合は、そのようなアプリケーションを Azure AD で SSO を使用するように再構成する必要があります。 同様に、Azure AD で SSO をサポートしていても、別の ID プロバイダーを使用している場合は、そのようなアプリケーションも Azure AD で SSO を使用するように再構成する必要があります。 フェデレーション プロトコルはサポートしていなくても、フォーム ベースの認証をサポートしているアプリケーションの場合は、Azure AD アプリケーション プロキシを使用して[パスワード保管](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting)を使用するようにアプリケーションを構成することをお勧めします。

![AppProxy パスワードベースのサインオン](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> 組織内の管理されていないアプリケーションを検出するメカニズムがない場合は、[Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security) などのクラウド アクセス セキュリティ ブローカー ソリューション (CASB) を使用して検出プロセスを実装することをお勧めします。

最後に、Azure AD アプリ ギャラリーがあり、Azure AD で SSO をサポートするアプリケーションを使用している場合は、[アプリ ギャラリーでアプリケーションを一覧表示する](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)ことをお勧めします。

#### <a name="single-sign-on-recommended-reading"></a>シングル サインオンに関する推奨資料

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Azure AD への AD FS アプリケーションの移行

[AD FS から Azure AD にアプリを移行](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure)すると、セキュリティの機能の追加、より一貫性のある管理容易性、および共同作業エクスペリエンスの向上が可能になります。 Azure AD で SSO をサポートしている AD FS に構成されているアプリケーションがある場合は、そのようなアプリケーションを Azure AD で SSO を使用するように再構成する必要があります。 Azure AD でサポートされていない一般的でない構成の AD FS で構成されたアプリケーションがある場合は、アプリの所有者に問い合わせて、特別な構成がアプリケーションの絶対的な要件であるかどうかを把握する必要があります。 必要でない場合は、Azure AD で SSO を使用するようにアプリケーションを再構成する必要があります。

![プライマリ ID プロバイダーとしての Azure AD](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health for ADFS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) は、Azure AD に移行できる可能性がある各アプリケーションの構成の詳細を収集するために使用できます。

### <a name="assign-users-to-applications"></a>アプリケーションへのユーザーの割り当て

優れた柔軟性と大規模な管理が可能になるため、[アプリケーションへのユーザーの割り当て](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)は、グループを使用することで最適なマッピングになります。 グループを使用する利点には、[属性ベースの動的グループ メンバーシップ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)と[アプリ所有者への委任](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners)などがあります。 そのため、既にグループを使用して管理している場合は、管理を大規模に向上させるために次の操作を行うことをお勧めします。

- グループ管理とガバナンスをアプリケーション所有者に委任します。
- アプリケーションへのセルフサービス アクセスを許可します。
- ユーザー属性が一貫してアプリケーションへのアクセスを決定できる場合は、動的グループを定義します。
- [Azure AD アクセス レビュー](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)を使用して、アプリケーションへのアクセスに使用されるグループに構成証明を実装します。

一方、個々のユーザーに割り当てられているアプリケーションが見つかった場合は、それらのアプリケーションに[ガバナンス](https://docs.microsoft.com/azure/active-directory/governance/index)を実装してください。

#### <a name="assign-users-to-applications-recommended-reading"></a>アプリケーションへのユーザーの割り当てに関する推奨資料

- [Azure Active Directory でアプリケーションにユーザーとグループを割り当てる](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Azure Active Directory でアプリ登録のアクセス許可を委任する](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Azure Active Directory の動的グループ メンバーシップ ルール](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>アクセス ポリシー

### <a name="named-locations"></a>ネームド ロケーション

Azure AD で[ネームド ロケーション](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)を使うと、組織内の信頼できる IP アドレス範囲にラベルを付けることができます。 Azure AD では、次のためにネームド ロケーションを使用します。

- リスク イベントの誤判定を防ぎます。 信頼できるネットワークの場所からサインインすることで、ユーザーのサインイン リスクが低下します。
- [場所ベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)を構成する。

![ネームド ロケーション](./media/active-directory-ops-guide/active-directory-ops-img10.png)

優先度に基づいた次の表を使用して、組織のニーズに最も合った推奨される解決策を見つけてください。

| **優先順位** | **シナリオ** | **推奨事項** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | PHS または PTA を使用していて、ネームド ロケーションが定義されていない場合 | ネームド ロケーションを定義して、リスク イベントの検出を改善します |
| 2 | フェデレーションされていて、"insideCorporateNetwork" 要求を使用しておらず、ネームド ロケーションが定義されていない場合 | ネームド ロケーションを定義して、リスク イベントの検出を改善します |
| 3 | 条件付きアクセス ポリシーでネームド ロケーションを使用しておらず、条件付きアクセス ポリシーにリスクまたはデバイス制御がない場合 | 条件付きアクセス ポリシーを、ネームド ロケーションを含めるように構成します |
| 4 | フェデレーションされていて、"insideCorporateNetwork" 要求を使用していて、ネームド ロケーションが定義されていない場合 | ネームド ロケーションを定義して、リスク イベントの検出を改善します |
| 5 | ネームド ロケーションではなく MFA で信頼された IP アドレスを使用していて、それらを信頼済みとしてマークする場合 | ネームド ロケーションを定義して、リスク イベントの検出を改善するためにそれらに信頼済みとしてマークします |

### <a name="risk-based-access-policies"></a>リスクベースのアクセス ポリシー

Azure AD は、すべてのサインインとすべてのユーザーのリスクを計算できます。 アクセス ポリシーの条件としてリスクを使用すると、ユーザー エクスペリエンスを向上させることができます。たとえば、認証プロンプトが減ってセキュリティが向上したり、ユーザーに必要なときにのみプロンプトが表示されたり、応答と修復を自動化したりします。

![サインインのリスク ポリシー](./media/active-directory-ops-guide/active-directory-ops-img11.png)

アクセス ポリシーでのリスクの使用をサポートする Azure AD Premium P2 ライセンスを既に所有していても、使用されていない場合は、セキュリティ体制にリスクを追加することを強くお勧めします。

#### <a name="risk-based-access-policies-recommended-reading"></a>リスクベースのアクセス ポリシーの推奨資料

- [方法:サインイン リスク ポリシーを構成する](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)
- [方法:ユーザー リスク ポリシーを構成する](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="client-application-access-policies"></a>クライアント アプリケーションのアクセス ポリシー

Microsoft Intune アプリケーション管理 (MAM) を使用すると、ストレージの暗号化、PIN、リモート ストレージのクリーンアップなどのデータ保護制御を、Outlook Mobile などの互換性のあるクライアント モバイル アプリケーションにプッシュすることができます。 さらに、条件付きアクセス ポリシーを作成して、承認済みまたは互換性のあるアプリから Exchange Online などのクラウド サービスへの[アクセスを制限](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)できます。

従業員が Office モバイル アプリなどの MAM 対応アプリケーションをインストールして、Exchange Online や SharePoint Online などの会社のリソースにアクセスし、BYOD (Bring Your Own Device) もサポートしている場合は、アプリケーションの MAM ポリシーをデプロイして、MDM に登録されていない個人所有のデバイスのアプリケーション構成を管理してから、MAM 対応クライアントからのアクセスのみを許可するように条件付きアクセス ポリシーを更新することをお勧めします。

![条件付きアクセスの付与の制御](./media/active-directory-ops-guide/active-directory-ops-img12.png)

従業員が企業リソースに対して MAM 対応アプリケーションをインストールし、Intune のマネージド デバイスでアクセスを制限する必要がある場合は、個人用デバイスのアプリケーション構成を管理し、MAM 対応クライアントからのアクセスのみを許可するように条件付きアクセス ポリシーを更新するために、アプリケーション MAM ポリシーのデプロイを検討する必要があります。

### <a name="conditional-access-implementation"></a>条件付きアクセスの実装

条件付きアクセスは、組織のセキュリティ体制を向上させるための重要なツールです。 そのため、次のベスト プラクティスに従うことが重要です。

- すべての SaaS アプリケーションに少なくとも 1 つのポリシーが適用されていることを確認します
- ロックアウトのリスクを回避するために、 **[すべてのアプリ]** フィルターを**ブロック**制御と組み合わせないようにします
- **[すべてのユーザー]** をフィルターとして使用せず、誤って **[ゲスト]** を追加しないようにします
- **すべての "レガシ" ポリシーを Azure portal に移行します**
- ユーザー、デバイス、およびアプリケーションのすべての条件をキャッチします
- **ユーザーごとの MFA** を使用するのではなく、条件付きアクセス ポリシーを使用して [MFA を実装](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)します
- 複数のアプリケーションに適用できる重要なポリシーの小さいセットを用意します
- 空の例外グループを定義し、それらをポリシーに追加して例外戦略を設定します
- MFA 制御を使用しない[緊急用](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)アカウントを計画します
- Exchange Online や Sharepoint Online などのサービスに対して同じ一連の制御を実装することで、Office 365 クライアント アプリケーション (たとえば、Teams、OneDrive for Business、Outlook など) で一貫したエクスペリエンスを確保します
- ポリシーへの割り当ては、個人ではなくグループを使用して実装する必要があります
- ポリシーで使用されている例外グループを定期的にレビューして、ユーザーがセキュリティ体制外にある時間を制限します。 Azure AD P2 を所有している場合は、アクセス レビューを使用してプロセスを自動化できます

#### <a name="conditional-access-recommended-reading"></a>条件付きアクセスに関する推奨資料

- [Azure Active Directory の条件付きアクセスのベスト プラクティス](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [ID とデバイスのアクセスの構成](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory の条件付きアクセス設定に関するリファレンス](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [一般的な条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>外部からのアクセス

### <a name="legacy-authentication"></a>レガシ認証

MFA などの強力な資格情報は、レガシ認証プロトコルを使用してアプリを保護することができないため、悪意のあるユーザーによって優先される攻撃ベクトルになります。 アクセスのセキュリティ体制を向上させるには、レガシ認証をロックダウンすることが重要です。

レガシ認証は、アプリで使用される以下のような認証プロトコルを指す用語です。

- 先進認証を使用していない古い Office クライアント (Office 2010 クライアントなど)
- IMAP/SMTP/POP などのメール プロトコルを使用しているクライアント

攻撃者はこれらのプロトコルを使用することを非常に好みます。実際に、ほぼ [100% のパスワードのスプレー攻撃](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)がレガシ認証プロトコルを使用しています。 ハッカーは多要素認証やデバイス認証などの追加のセキュリティの問題に必要な対話型サインインをサポートしていないため、レガシ認証プロトコルを使用します。

環境でレガシ認証が広く使用されている場合は、レガシ クライアントをできるだけ早く[先進認証](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016)をサポートするクライアントに移行するように計画する必要があります。 同じトークンで、一部のユーザーが既に先進認証を使用していても、他のユーザーがまだレガシ認証を使用している場合は、次の手順を実行してレガシ認証クライアントをロックダウンする必要があります。

1. [サインイン アクティビティ レポート](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) を使用して、レガシ認証をまだ使用しているユーザーを特定し、修復を計画します。

   a. 影響を受けるユーザーに対して先進認証対応のクライアントにアップグレードします。
   
   b. 次の手順ごとにロックダウンするように、カットオーバー期間を計画します。
   
   c. レガシ認証にハードの依存関係があるレガシ アプリケーションを特定します。 下記の手順 3 を参照してください。

2. レガシ認証を使用していないユーザーが、より多くの露出を回避するために、ソース (たとえば、Exchange メールボックス) でレガシ プロトコルを無効にします。
3. 残りのアカウント (理想的にはサービス アカウントなどの人間以外の ID) では、認証後に[従来のプロトコルを制限するために条件付きアクセス](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417)を使用します。

#### <a name="legacy-authentication-recommended-reading"></a>レガシ認証に関する推奨資料

- [Exchange Server のメールボックスへの POP3 または IMAP4 アクセスを有効または無効にする](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>同意の付与

不正な同意の付与攻撃では、攻撃者は、連絡先情報、メール、ドキュメントなどのデータへのアクセスを要求する Azure AD 登録済みのアプリケーションを作成します。 ユーザーは、悪意のある Web サイトにアクセスしたときに、フィッシング攻撃を介して悪意のあるアプリケーションに同意を与える可能性があります。

以下は、Microsoft クラウドサービスを精査するアクセス許可を持つアプリのリストです：

- アプリまたは委任された \*.ReadWrite アクセス許可を持つアプリ
- ユーザーの代わりにメールの読み取り、送信、または管理を行うことができる、委任されたアクセス許可を持つアプリ
- 次のアクセス許可の使用を許可されているアプリケーション：

| リソース | アクセス許可 |
| :- | :- |
| Office 365 Exchange Online | EAS.AccessAsUser.All |
| | EWS.AccessAsUser.All |
| | Mail.Read |
| Microsoft Graph | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite |

- アプリには、サインインしたユーザーの、完全なユーザー偽装が許可されています。 例:

|リソース | アクセス許可 |
| :- | :- |
| Azure AD グラフ | Directory.AccessAsUser.All |
| Microsoft Graph | Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

このシナリオを回避するには、[Office 365 での不正な同意の付与の検出と修復](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) に関するページを参照して、不正な許可があるアプリケーションや必要以上に許可がされているアプリケーションを特定して修正する必要があります。 次に、[セルフサービスを完全に削除し](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent)、[ガバナンスプロシージャを確立](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) します。 最後に、アプリのアクセス許可の定期的なレビューをスケジュールし、不要な場合には削除します。

#### <a name="consent-grants-recommended-reading"></a>同意の付与に関する推奨資料

- [Microsoft Graph のアクセス許可](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>ユーザーとグループの設定

次に示すのは、明示的なビジネス ニーズがない場合にロックダウンできるユーザーとグループの設定です。

#### <a name="user-settings"></a>ユーザー設定

- **外部ユーザー** - 外部コラボレーションは、Teams、Power BI、Sharepoint Online、Azure Information Protection などのサービスを使用して、企業内で有機的に発生する場合があります。 ユーザーが開始した外部コラボレーションを制御するための明示的な制約がある場合は、[Azure AD エンタイトルメント管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)やヘルプ デスクの利用などの制御された運用を使用して、外部ユーザーを有効にすることをお勧めします。 サービスに対して有機的な外部コラボレーションを許可しない場合は、[外部ユーザーの招待からメンバーを完全にブロックする](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations)ことができます。 また、外部ユーザーの招待で[特定のドメインを許可またはブロック](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list)することもできます。
- **アプリの登録** - アプリの登録が有効になっている場合、エンド ユーザーはアプリケーション自体をオンボードし、データへのアクセスを許可することができます。 アプリの登録の典型的な例としては、Outlook プラグインを有効にするユーザー、またはメールやカレンダーを読んだりメールを送信したりするための音声アシスタント (Alexa や Siri など) があります。 顧客がアプリの登録を無効にすると決めた場合は、アプリケーションを管理者アカウントに登録する必要があり、多くの場合はプロセスを運用化するプロセスを設計する必要があるため、InfoSec チームと IAM チームは例外 (ビジネス要件に基づいて必要なアプリの登録) の管理に関与する必要があります。
- **管理ポータル** - 組織は Azure portal の Azure AD ブレードをロックダウンして、管理者以外が Azure portal 内の Azure AD 管理にアクセスして混乱を招くことができないようにします。 Azure AD 管理ポータルのユーザー設定にアクセスして、アクセスを制限します。

![管理ポータルの制限されたアクセス](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> 管理者以外は、コマンドラインやその他のプログラム インターフェイスを使用して、引き続き Azure AD 管理インターフェイスにアクセスすることができます。

#### <a name="group-settings"></a>グループ設定

**[セルフサービス グループ管理] / [ユーザーはセキュリティ グループを作成できます] / [O365 グループ]。** クラウドにグループの最新のセルフサービス イニシアチブがない場合は、この機能を使用する準備が整うまで、顧客はこのイニシアチブを無効にすることができます。

#### <a name="groups-recommended-reading"></a>グループに関する推奨資料

- [Azure Active Directory B2B コラボレーションとは](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Azure Active Directory のアプリ、アクセス許可、および同意](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Azure Active Directory でのグループを使用したリソースへのアクセス管理](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [アプリケーション アクセス管理のセルフ サービス化に必要な Azure Active Directory の設定](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>予期しない場所からのトラフィック

攻撃者は世界中のさまざまな場所からやって来ます。 このリスクは、場所が条件である条件付きアクセス ポリシーを使用して管理します。 条件付きアクセス ポリシーの[場所の条件](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)によって、サインインするビジネス上の理由がない場所からのアクセスをブロックすることができます。

![新しいネームド ロケーションを作成する](./media/active-directory-ops-guide/active-directory-ops-img14.png)

使用可能な場合は、セキュリティ情報およびイベント管理 (SIEM) ソリューションを使用して、リージョン間のアクセスのパターンを分析および検索します。 SIEM 製品を使用していない場合、または Azure AD からの認証情報の取り込みではない場合は、[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) を使用してリージョン間のアクセスのパターンを特定することをお勧めします。

## <a name="access-usage"></a>アクセスの使用状況

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>アーカイブされ、インシデント対応計画と統合された Azure AD ログ

Azure AD のサインイン アクティビティ、監査とリスク イベントへのアクセス権を持つことは、トラブルシューティング、使用状況の分析、およびフォレンジクスの調査を行うために不可欠です。 Azure AD は、保有期間が制限されている REST API を介して、これらのソースへのアクセスを提供します。 セキュリティ情報とイベント管理 (SIEM) システム、またはそれと同等のアーカイブ テクノロジは、監査とサポートの長期ストレージの鍵となります。 Azure AD ログの長期ストレージを有効にするには、既存の SIEM ソリューションに追加するか、[Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) を使用する必要があります。 インシデント対応計画および調査の一環として使用できるログをアーカイブしてください。

#### <a name="logs-recommended-reading"></a>ログに関する推奨資料

- [Azure Active Directory 監査 API リファレンス](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Azure Active Directory サインイン アクティビティ レポート API リファレンス](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Azure AD Reporting API と証明書を使ってデータを取得する](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Azure Active Directory Identity Protection 用の Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Office 365 管理アクティビティ API リファレンス](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Azure Active Directory Power BI コンテンツ パックの使用方法](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>まとめ

セキュリティで保護された ID インフラストラクチャには、12 の側面があります。 このリストは、企業のセキュリティ体制に基づいた資格情報のセキュリティ保護と管理、認証エクスペリエンスの定義、割り当ての委任、使用状況の測定、およびアクセス ポリシーの定義に役立ちます。

- 主要タスクに所有者を割り当てます。
- 脆弱なパスワードや漏洩したパスワードを検出し、パスワードの管理と保護を強化し、リソースへのユーザーのアクセスをさらにセキュリティで保護するソリューションを実装します。
- いつでもどこからでもリソースを保護するために、デバイスの ID を管理します。
- パスワードレス認証をデプロイします。
- 組織全体で標準化されたシングル サインオン メカニズムを提供します。
- AD FS から Azure AD にアプリを移行して、セキュリティの強化とより一貫性のある管理を可能にします。
- グループを使用して優れた柔軟性と大規模な管理を可能にすることで、アプリケーションにユーザーを割り当てます。
- リスクベースのアクセス ポリシーを構成します。
- レガシ認証プロトコルをロックダウンします。
- 不正な同意の付与を検出して修復します。
- ユーザーとグループの設定をロックダウンします。
- トラブルシューティング、使用状況分析、フォレンジック調査のために Azure AD ログの長期ストレージを可能にします。

## <a name="next-steps"></a>次の手順

[ID のガバナンスの運用検査とアクション](active-directory-ops-guide-govern.md)を開始します。
