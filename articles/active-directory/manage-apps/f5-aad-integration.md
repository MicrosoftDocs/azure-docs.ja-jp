---
title: F5 を使用した Azure AD の安全なハイブリッド アクセス | Microsoft Docs
description: F5 BIG-IP Access Policy Manager と Azure Active Directory の統合による、安全なハイブリッド アクセス
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38b484bd67f3354132675f343dcc06bd7f9d48a0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499816"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>F5 BIG-IP Access Policy Manager と Azure Active Directory の統合による、安全なハイブリッド アクセス

モビリティが普及し、脅威が進化するにつれ、リソースへのアクセスとガバナンスに対する監視がさらに強化される中、すべての最新化プログラムにおいて、[ゼロ トラスト](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)が何よりも重要な要素になっています。
Microsoft と F5 は、このデジタル変革は、通常、企業が複数年にわたって取り組むものであり、最新化されるまでに、重要なリソースが公開される可能性があると考えています。 F5 BIG-IP と Azure Active Directory Secure Hybrid Access (SHA) は、オンプレミス アプリケーションへのリモート アクセスを向上させるだけでなく、これらの脆弱性を持つサービスの全体的なセキュリティを強化することを目的として開発されました。

調査では、オンプレミス アプリケーションの 60 - 80% が事実上レガシであり、Azure Active Directory (AD) と直接統合できないと推定されています。 同じ調査で、これらのシステムの大部分が、SAP、Oracle、SAGE、および重要なサービスを提供するその他の既知のワークロードのダウンレベル バージョンで実行されていることも示されました。

SHA は、組織が既存の F5 を引き続き使用して、より優れたネットワークとアプリケーションを提供できるようにすることで、この盲点に対処します。 Azure AD と組み合わせて使用すると、異種アプリケーション環境と最新の ID コントロール プレーンとの橋渡しとして機能します。

Azure AD の事前認証によって BIG-IP の公開済みサービスにアクセスすると、次のような利点があります。

- [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview)、[MS Authenticator](../user-help/user-help-auth-app-download-install.md)、[Fast Identity Online (FIDO) キー](../authentication/howto-authentication-passwordless-security-key.md)、および[証明書ベースの認証](../authentication/active-directory-certificate-based-authentication-get-started.md)を使用した、パスワードレス認証

- プリエンプティブな[条件付きアクセス](../conditional-access/overview.md)および[多要素認証 (MFA)](../authentication/concept-mfa-howitworks.md)

- [Identity Protection](../identity-protection/overview-identity-protection.md) -ユーザーとセッションのリスクのプロファイルによる、アダプティブコントロール


- [漏洩した資格情報の検出](../identity-protection/concept-identity-protection-risks.md)

- [セルフサービス パスワード リセット (SSPR)](../authentication/tutorial-enable-sspr.md)

- [パートナー コラボレーション](../governance/entitlement-management-external-users.md) -管理対象ゲスト アクセスのエンタイトルメント管理

- [Cloud App Security (CASB)](/cloud-app-security/what-is-cloud-app-security) - アプリの完全な検出とコントロール

- 脅威の監視 - [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) による高度な脅威分析

- [Azure AD ポータル](https://azure.microsoft.com/features/azure-portal/) - ID とアクセスを管理する単一のコントロール プレーン

## <a name="scenario-description"></a>シナリオの説明

アプリケーション デリバリー コントローラー (ADC) および SSL-VPN として機能する BIG-IP システムは、次に示すような、あらゆる種類のサービスへのローカル アクセスとリモート アクセスを提供します。

- 最新の Web アプリケーションとレガシ Web アプリケーション

- Web ベースではないアプリケーション

- REST および SOAP Web API サービス

その Local Traffic Manager (LTM) は、リバース プロキシ機能を通じて、サービスを安全に公開できるようにします。 同時に、洗練された Access Policy Manager (APM) が豊富な機能で BIG-IP を拡張して、フェデレーションとシングル サインオン (SSO) を有効にします。

統合は、APM と Azure AD の間の標準のフェデレーション信頼に基づいており、[SSL-VPN シナリオ](f5-aad-password-less-vpn.md)を含むほとんどの SHA ユース ケースに共通します。 Security Assertion Markup Language (SAML)、OAuth、Open ID Connect (OIDC) の各リソースも、リモートアクセス用にセキュリティで保護できるため、例外ではありません。 BIG-IP が、SaaS アプリを含むすべてのサービスへのゼロ トラスト アクセスのチョークポイントとなるシナリオも考えられます。

BIG-IP と Azure AD の統合によって、従来のサービスや非 Azure AD 統合サービスを保護するために必要なプロトコル移行を、[パスワードレス認証](https://www.microsoft.com/security/business/identity/passwordless)や[条件付きアクセス](../conditional-access/overview.md)などの最新のコントロールによって実現できます。 このシナリオでは、BIG-IP が引き続きリバース プロキシとしての役割を果たしながら、サービスごとに、事前認証や承認を Azure AD に任せます。

図の手順 1 - 4 は、サービス プロバイダーが開始したフローにおける、ユーザー、BIG-IP、Azure AD の間の、フロントエンドの事前認証のやり取りを示しています。 手順 5 - 6 は、後続の APM セッション エンリッチメントと、個々のバックエンド サービスへの SSO を示しています。

![アーキテクチャ概要図](./media/f5-aad-integration/integration-flow-diagram.png)

| 手順 | 説明 |
|:------|:-----------|
| 1. | ユーザーがポータルでアプリケーション アイコンを選択し、URL が SAML SP (BIG IP) に解決される |
| 2. | BIG-IP が、事前認証のためにユーザーを SAML IDP (Azure AD) にリダイレクトする|
| 3. | Azure AD が、承認のために条件付きアクセス ポリシーと[セッション制御](../conditional-access/concept-conditional-access-session.md)を処理する|
| 4. | ユーザーが、Azure AD によって発行された SAML 要求を提示して、BIG-IP にリダイレクトされる |
| 5. | BIG-IP が、公開済みサービスへの [SSO](../hybrid/how-to-connect-sso.md) および[ロールベースのアクセス制御l (RBAC)](../../role-based-access-control/overview.md) に含める、追加のセッション情報を要求する |
| 6. | BIG-IP が、クライアント要求をバックエンド サービスに転送する

## <a name="user-experience"></a>ユーザー エクスペリエンス

社員、関係者、コンシューマーの区別なく、ほとんどのユーザーは既に Office 365 のログインに精通しているので、SHA 経由での BIG-IP サービスへのアクセスについても、ほとんどの場合は理解しています。

デバイスの種類や場所に関係なく、BIG-IP で公開されたサービスが、セルフサービス機能と共に、[MyApps](../user-help/my-apps-portal-end-user-access.md) または [O365 スタートパッド](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf)で幅広いサービス セットに統合されます。 希望する場合は、BIG-IP 独自の Web トップ ポータルを使用して、公開されたサービスに直接アクセスすることもできます。 ログオフ時には SHA によって BIG-IP と Azure AD の両方でユーザー セッションが確実に終了されるため、サービスが不正アクセスから完全に保護されたままになります。  

このスクリーンショットは、ユーザーが BIG-IP で公開されたサービスを見つけ、アカウントのプロパティを管理するために安全にアクセスできる、Azure AD アプリ ポータルのものです。  

![woodgrove myapps ギャラリーを示すスクリーンショット](media/f5-aad-integration/woodgrove-app-gallery.png)

![woodgrove myaccounts セルフサービス ページを示すスクリーンショット](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>分析情報と分析

BIG-IP はビジネスにとって重要な役割を果たすため、デプロイされた BIG-IP インスタンスを監視して、公開されたサービスが SHA レベルでも運用レベルでも高い可用性を持つことを保証する必要があります。

セキュリティ情報イベント管理 (SIEM) ソリューションを介してローカルまたはリモートでイベントをログに記録するオプションがいくつかあります。これにより、オフボックス ストレージとテレメトリ処理が可能になります。 Azure AD および SHA 固有のアクティビティを監視する効果的な方法は、[Azure Monitor](../../azure-monitor/overview.md) と [Azure Sentinel](../../sentinel/overview.md) を使用することです。これによって次が実現します。

- 組織 (複数のクラウドにまたがる可能性もあり) とオンプレミスの場所の詳細な概要 (BIG-IP インフラストラクチャを含む)

- すべてのシグナルの結合ビューを提供する単一のコントロール プレーン (複雑で多様なツールへの依存を回避)

![監視フローを示す図](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>前提条件

SHA 向けに F5 BIG-IP と Azure AD を統合するには、次の前提条件があります。

- 次のいずれかのプラットフォームで実行されている F5 BIG-IP インスタンス:

  - 物理アプライアンス

  - Microsoft Hyper-V、VMware ESXi、Linux KVM、Citrix Hypervisor などのハイパーバイザー仮想エディション

  - Azure、VMware、KVM、Community Xen、MS Hyper-v、AWS、OpenStack、Google Cloud などのクラウド仮想エディション

    BIG-IP インスタンスの実際の場所は、オンプレミスまたは Azure を含むサポート対象のクラウド プラットフォーム (インターネット接続があり、リソースが発行され、その他の必要なサービス (Active Directory など) がある場合) になります。  

- アクティブな F5 BIG-IP APM ライセンス (次のいずれかのオプションを使用):

   - F5 BIG-IP® Best バンドル (または)

   - F5 BIG-IP Access Policy Manager™ スタンドアロン ライセンス

   - 既存の BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM) に対する F5 BIG-IP Access Policy Manager™ (APM) アドオン ライセンス

   - BIG-IP Access Policy Manager™ (APM) の 90 日間[試用版ライセンス](https://www.f5.com/trial/big-ip-trial.php)

- Azure AD ライセンス (次のいずれかのオプションを使用):

   - Azure AD [無料サブスクリプション](/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20)。パスワードレス認証を使用して SHA を実装するための最小コア要件を提供

   - [Premium サブスクリプション](https://azure.microsoft.com/pricing/details/active-directory/)。[条件付きアクセス](../conditional-access/overview.md)、[MFA](../authentication/concept-mfa-howitworks.md)、[Identity Protection](../identity-protection/overview-identity-protection.md) など、序文に記載されているすべての追加の付加価値機能を提供

SHA を実装するために事前の経験や F5 BIG-IP に関する知識は必要ありませんが、F5 BIG-IP の用語に慣れておくことをお勧めします。 F5 の豊富な[ナレッジ ベース](https://www.f5.com/services/resources/glossary)も、BIG-IP に関する知識を身につけるのに役立ちます。

## <a name="deployment-scenarios"></a>展開シナリオ

次に示すチュートリアルでは、BIG-IP と Azure AD SHA の一般的なパターンのいくつかを実装する方法について詳しく説明しています。

- [F5 BIG-IP を使用した Azure のデプロイに関するチュートリアル](f5-bigip-deployment-guide.md)

- [F5 BIG-IP APM と Azure AD SSO から Kerberos アプリケーション](../saas-apps/kerbf5-tutorial.md#configure-f5-single-sign-on-for-kerberos-application)

- [F5 BIG-IP APM と Azure AD SSO からヘッダーベースのアプリケーション](../saas-apps/headerf5-tutorial.md#configure-f5-single-sign-on-for-header-based-application)

- [Azure AD SHA による F5 BIG-IP SSL-VPN の保護](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>その他のリソース

- [パスワードの終わり、これからはパスワードレス](https://www.microsoft.com/security/business/identity/passwordless)

- [Azure Active Directory のセキュリティで保護されたハイブリッド アクセス](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [リモート作業を可能にする Microsoft ゼロ トラスト フレームワーク](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Azure Sentinel の概要](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>次のステップ

既存の BIG-IP インフラストラクチャを使用するか、試用版のインスタンスをデプロイして、SHA の概念実証 (POC) を実行することを検討してください。 [BIG-IP 仮想エディション (VE) VM を Azure にデプロイ](f5-bigip-deployment-guide.md)するには、約 30 分かかります。その時点で、次のようになります。

- SHA 概念実証をモデル化するための、完全に保護されたプラットフォーム

- 新しい BIG-IP システムの更新プログラムと修正プログラムをテストするために使用する、実稼働前インスタンスの、完全に保護されたプラットフォーム

同時に、BIG-IP を介しての発行の対象となる、SHA で保護するアプリケーションを 1 つまたは 2 つ指定する必要があります。  

実稼働サービスが中断しないように、BIG-IP を介して公開されていないアプリケーションから始めることをお勧めします。 この記事に記載されているガイドラインは、さまざまな BIG-IP 構成オブジェクトを作成し、SHA を設定するための、一般的な手順について理解するのに役立ちます。 完了すると、他の新しいサービスでも同じことができるようになります。さらに、最小限の作業で BIG-IP で公開済みの既存のサービスを SHA に変換するための知識も得られます。

次の対話形式のガイドでは、SHA を実装し、エンドユーザー エクスペリエンスを確認するための、高度な手順について説明します。

[![対話型ガイドの表紙のイメージ](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
