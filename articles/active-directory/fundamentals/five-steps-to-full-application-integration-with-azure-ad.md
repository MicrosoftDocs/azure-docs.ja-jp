---
title: すべてのアプリを Azure AD と統合するための 5 つの手順
description: このガイドでは、すべてのアプリケーションを Azure AD と統合する方法について説明します。 各手順では、値について説明し、技術的詳細を説明するリソースへのリンクを示しています。
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: c75d04faf9ac59f21aad1dcd88dfe83699a11941
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057046"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>すべてのアプリを Azure AD と統合するための 5 つの手順

Azure Active Directory (Azure AD) は、Microsoft のクラウドベースの ID およびアクセス管理サービスです。 Azure AD には、お客様、パートナー、および従業員が必要とするアプリケーションにアクセスできるように、セキュリティで保護された認証および承認ソリューションが用意されています。 Azure AD では、[条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)、[多要素認証](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)、[シングルサインオン](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)、および [自動ユーザー プロビジョニング](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)によって、ID およびアクセス管理が簡単で安全になります。

会社に Microsoft 365 サブスクリプションがある場合は、Azure AD を[既に使用している](https://docs.microsoft.com/office365/enterprise/about-office-365-identity)可能性があります。 ただし、Azure AD をすべてのアプリケーションで使用でき、[アプリケーションの管理を一元化する](https://docs.microsoft.com/azure/active-directory/manage-apps/common-scenarios)ことで、アプリ ポートフォリオ全体で同じ ID 管理機能、ツール、およびポリシーを使用することができます。 そうすることにより、セキュリティを強化し、コストを削減して、生産性を向上させ、コンプライアンスの確保を可能にする統合ソリューションが得られます。 さらに、オンプレミス アプリにリモート アクセスできるようになります。

このガイドでは、すべてのアプリケーションを Azure AD と統合する方法について説明します。 各手順では、値について説明し、技術的詳細を説明するリソースへのリンクを示しています。 これらの手順は、推奨される順番で提示しています。 ただし、プロセスの任意の部分にジャンプして、各自に最も多くの価値を追加するものから開始してもかまいません。

詳細なビジネス プロセス ホワイトペーパーなど、このトピックの他のリソースについては、「[アプリケーションを Azure Active Directory に移行するためのリソース](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)」ページを参照してください。

## <a name="1-use-azure-ad-for-new-applications"></a>1.新しいアプリケーションに Azure AD を使用する

まず、新しく取得したアプリケーションに焦点を合わせます。 ビジネスで新しいアプリケーションの使用を開始したら、すぐに[それを Azure AD テナントに追加](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)します。 新しいアプリを Azure AD に追加することが組織の標準的な慣習になるように、会社のポリシーを設定します。 これにより、既存のビジネス プロセスに対する中断が最小限になり、現在の環境でビジネスを行う方法を変更することなく、アプリの統合によって得られる価値を調査し、証明することができます。

Azure Active Directory (Azure AD) には、簡単に開始できるようにするための何千もの事前統合されたアプリケーションを含むギャラリーがあります。 [Azure AD 組織にギャラリー アプリを追加する](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)には、次のような人気のあるアプリと統合するためのステップバイステップの[チュートリアル](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)を使用します。

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-tutorial)
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)
- [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-tutorial)
- [AWS](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)
- [Slack](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)

さらに、組織に既に存在するアプリケーションや、Azure AD ギャラリーにまだ属していないベンダーの任意のサードパーティ アプリケーションなど、[ギャラリーにないアプリケーションを統合する](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)こともできます。 また、[ギャラリーに存在していないアプリを追加する](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-app-gallery-listing)こともできます。

最後に、社内で開発したアプリを統合することもできます。 これについては、このガイドの手順 5 で説明します。

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2.既存のアプリケーションの使用状況を特定し、作業の優先順位を設定する

次に、従業員が頻繁に使用しているアプリケーションを検出し、それらを Azure AD に統合するための作業に優先順位を付けます。

まず、Microsoft Cloud App Security の [Cloud Discovery ツール](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)を使用して、ネットワークの&quot;シャドウ&quot; IT (つまり、IT 部門で管理されていないアプリ) を検出して管理できます。 [Microsoft Defender Advanced Threat Protection (ATP) を使用](https://docs.microsoft.com/cloud-app-security/wdatp-integration)して、検出プロセスを簡素化および拡張できます。

さらに、Azure portal の [AD FS アプリケーション アクティビティ レポート](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)を使用して、組織内のすべての AD FS アプリ、それらにサインインした一意のユーザー数、およびそれらを Azure AD と統合するための互換性を検出できます。

既存の状況を検出したら、[プランを作成](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)し、統合する優先順位が最も高いアプリを優先させます。 このプロセスの指針として、次のような質問例が考えられます。

- 最も使用されているアプリはどれですか?
- 最もリスクが高いものはどれですか?
- 今後、使用停止になるため、処理する必要のないアプリはどれですか?
- オンプレミスのまま維持する必要があり、クラウドに移行できないアプリはどれですか?

すべてのアプリが統合され、複数の ID ソリューションに依存することがなくなると、最大の利点とコストの削減が得られます。 ただし、この目標に段階的に近づくにつれて、ID 管理が簡単になり、セキュリティが強化されていきます。 この機会を使って、作業の優先順位を設定し、状況に適したものを判断することが望まれます。

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3.他の ID プロバイダーに依存するアプリを統合する

検出プロセス中に、IT 部門によって追跡されていないアプリケーションが見つかることがありますが、これにより、データとリソースが脆弱になります。 また、Active Directory フェデレーションサービス (AD FS) (ADFS) やその他の ID プロバイダーなど、別の ID ソリューションを使用しているアプリケーションがある場合もあります。 ID とアクセスの管理を統合してコストを削減し、セキュリティを強化する方法を検討してください。 ID ソリューションの数を減らすには、次のようにします。

- オンプレミスのユーザー プロビジョニングと認証の必要性のほか、同じサービスについて他のクラウド ID プロバイダーに支払われるライセンス料金をなくすことで、コストを削減します。
- ID とアクセス管理プロセスの冗長性を減らすことで、管理オーバーヘッドを減らし、セキュリティを強化できます。
- 従業員は、[MyApps ポータル](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)を通じて、必要なすべてのアプリケーションに、セキュリティで保護されたシングルサインオン アクセスができます。
- アプリの使用から得られるデータの量を増やし、その利点を新しく追加されたアプリに拡張することで、条件付きアクセスなどの Azure AD の [ID 保護](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)関連サービスのインテリジェンスを強化します。

アプリを Azure AD と統合するビジネス プロセスの管理に関するガイダンスを公開しています。これには、ビジネスおよびアプリケーションの所有者に気付かせ、関心を持ってもらうために使用できる[ポスター](https://aka.ms/AppOnePager)と[プレゼンテーション](https://aka.ms/AppGuideline) が含まれます。 このプロセスの実行を進めながら、それらのサンプルを独自のブランドで変更し、会社のポータル、ニュースレター、またはその他のメディアを通じて、それらを組織に公開できます。

開始点として、Active Directory フェデレーション サービス (AD FS) の使用を評価することが推奨されます。 多くの組織で、SaaS アプリ、カスタム基幹業務アプリ、Office 365、Azure AD ベースのアプリでの認証に ADFS が使われています。

![図は、オンプレミスのアプリ、基幹業務アプリ、SaaS アプリ、Azure AD 経由の Office 365 がすべて Active Directory と AD FS に点線で接続されていることを示しています。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

ID 管理ソリューションの[中心として ADFS を Azure AD に置き換える](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure)ことで、この構成をアップグレードできます。 これにより、従業員がアクセスする必要があるすべてのアプリのサインオンが可能になり、上記の他の利点に加えて、[MyApps ポータル](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)経由で、従業員が必要とするビジネス アプリケーションを簡単に見つけられるようになります。

![図は、Active Directory と AD FS 経由のオンプレミスのアプリ、基幹業務アプリ、SaaS アプリ、Office 365 がすべて Azure Active Directory に点線で接続されていることを示しています。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Azure AD が中心の ID プロバイダーになると、フェデレーション ソリューションを使用することなく、ADFS から完全に切り替えることができます。 これまで認証に ADFS を使用していたアプリでは、Azure AD だけを使用できるようになります。

![図は、オンプレミス、基幹業務アプリ、SaaS アプリ、Office 365 がすべて Azure Active Directory に点線で接続されていることを示しています。 Active Directory と AD FS は存在しません。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

さらに、別のクラウドベースの ID プロバイダーを使用するアプリを Azure AD に移行することもできます。 組織に、複数の ID アクセス管理 (IAM) ソリューションが設置されている場合があります。 1 つの Azure AD インフラストラクチャに移行することは、IAM ライセンスへの依存関係 (オンプレミスまたはクラウド内) とインフラストラクチャ コストを削減する機会になります。 M365 ライセンス経由で既に Azure AD に支払っている場合、別の IAM ソリューションの追加コストを支払う理由はありません。

## <a name="4-integrate-on-premises-applications"></a>4.オンプレミスのアプリケーションを統合する

従来、アプリケーションは、企業ネットワークに接続している間のみアクセスを許可することによって、セキュリティが維持されてきました。 しかし、接続が拡大している世界では、お客様、パートナー、従業員が世界のどこにいても、アプリにアクセスできるようにする必要があります。 [Azure AD アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) (AppProxy) は、既存のオンプレミス アプリを Azure AD に接続する Azure AD の機能であり、それを実行するために、エッジ サーバーやその他の追加のインフラストラクチャを保守する必要がありません。

![図は、動作中のアプリケーション プロキシ サービスを示しています。 ユーザーが "https://sales.contoso.com" にアクセスすると、その要求が、Azure Active Directory の "https://sales-contoso.msappproxy.net" 経由で、オンプレミスのアドレス "http://sales" にリダイレクトされます。](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

「[チュートリアル:Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)」を使用して、アプリケーション プロキシを有効にし、オンプレミス アプリケーションを Azure AD テナントに追加できます。

さらに、F5 Big-IP APM や Zscaler Private Access などのアプリケーション配信コントローラーを統合することもできます。 これらを Azure AD と統合することにより、パートナー製品のトラフィック管理とセキュリティ機能に加えて、Azure AD の先進の認証および ID 管理を実現できます。 このソリューションは、[安全なハイブリッド アクセス](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)と呼ばれています。 現在、次のサービスのいずれかを使用している場合は、それらを Azure AD と統合する手順を説明したチュートリアルが用意されています。

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)
- [Citrix Application delivery Controller (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial) (旧称 Citrix Netscaler)
- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)
- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)

## <a name="5-integrate-apps-your-developers-build"></a>5.開発者が構築したアプリを統合する

社内で構築されたアプリの場合、開発者は [Microsoft ID プラットフォーム](https://docs.microsoft.com/azure/active-directory/develop/)を使用して、認証と承認を実装できます。 プラットフォームに統合されたアプリケーションは、[Azure AD に登録され](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)、ポートフォリオ内の他のアプリとまったく同じように管理されます。

開発者は、内部使用アプリと顧客向けアプリの両方にプラットフォームを使用できます。またプラットフォームを使用することには他の利点もあります。 プラットフォームの一部である [Microsoft 認証ライブラリ (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) により、開発者は、開発者自身で実装する必要なく、アプリにアクセスするための多要素認証やセキュリティ キーの使用などの最新のエクスペリエンスを可能にすることができます。 さらに、Microsoft ID プラットフォームと統合されたアプリでは、[Microsoft Graph](https://docs.microsoft.com/azure/active-directory/develop/microsoft-graph-intro) にアクセスできます。これは、組織の生産性、ID、およびセキュリティのパターンを記述する Microsoft 365 データを提供する統合 API エンドポイントです。 開発者は、この情報を使用して、ユーザーの生産性を高める機能を実装できます。 たとえば、ユーザーが最近対話したことがある相手を特定し、アプリの UI にそれらを表示します。

プラットフォームの包括的な紹介を提供する[ビデオ シリーズ](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX)のほか、サポートされる言語とプラットフォームでの[多くのコード サンプル](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code)を用意しています。

## <a name="next-steps"></a>次の手順

- [アプリケーションを Azure Active Directory に移行するためのリソース](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)
