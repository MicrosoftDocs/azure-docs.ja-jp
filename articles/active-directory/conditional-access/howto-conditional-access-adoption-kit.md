---
title: 条件付きアクセス導入キット - Azure Active Directory
description: リソースへのアクセスのために Azure AD の条件付きアクセスを導入する
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cc4ff5fb528760be8c910f3da7d5691a6aae0d8
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387069"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Azure AD の条件付きアクセスの導入

モバイルを重視したクラウド中心の世界では、ユーザーはどこからでも、さまざまな種類のデバイスやアプリを使用して組織のリソースにアクセスできます。 その結果、だれがリソースにアクセスできるかに重点を置くだけでは十分ではなくなっています。 だれがアクセスできるかを制御したり、ユーザーがどこにいるか、どのデバイスが使用されているか、などを識別したりできます。

この制御を提供するために、**Azure Active Directory (AD) の条件付きアクセス**では、アプリケーションにアクセスするためユーザーがに満たす必要がある、多要素認証 (MFA) などの条件を指定できます。 条件付きアクセス ポリシーを使用して、承認されたユーザー (クラウド アプリへのアクセスを許可されているユーザー) が特定の条件下でクラウド アプリにアクセスできる方法を制御します。 詳細については、[Azure Active Directory の条件付きアクセス](overview.md#conditional-access-policies)に関するページを参照してください。

## <a name="key-benefits"></a>主な利点

Azure AD の条件付きアクセスを使用する主な利点は次のとおりです。

* **生産性の向上:** 条件付きアクセス (CA) ポリシーにより、ユーザーが MFA の使用を求められている、アクセスをブロックされている、または信頼されたデバイスの使用を要求されているポイントをターゲットにすることができます。 たとえば、企業ネットワークの外にいるときに限って、アプリケーションへの MFA をユーザーに要求するなどのポリシーを設定できます。 MFA 要求を減らすことで、ユーザーは、サインインのたびに MFA を要求される場合よりも生産性を保ちやすくなります。 さらに、Azure AD の条件付きアクセスでは、ユーザーごとにポリシーを指定したり、アプリ固有のポリシーを作成したりできます。
* **リスク管理:** 条件付きアクセスポリシーを有効にすると、クラウド規模の ID 保護、リスクベースのアクセス制御機能、および、多要素認証のネイティブ サポートが提供されます。 条件付きアクセスと ID 保護を組み合わせて、アプリケーションへのアクセスをいつ、ブロックまたはゲート制御するかを定義できます。
* **コンプライアンスとガバナンスへの対応:** Azure AD は、実行されるすべてのアプリケーション アクセス要求に対してのネイティブ監査ログをサポートしますので、Azure AD を使用すれば、アプリケーションに対するアクセス要求と承認の監査、および、アプリケーション全体の使用状況の把握が容易になります。 監査には、要求者の身分証明、要求日、業務上の正当な理由、承認ステータス、および承認者の身分証明が含まれます。 このデータは API から入手することもでき、これによって将来的には、このデータを任意の SIEM (セキュリティ インシデントおよびイベント監視) システムにインポートすることが可能になります。
* **コスト管理:** アクセス ポリシーを Azure AD に移行すると、Active Directory フェデレーション サービス (AD FS) のようなカスタムまたはオンプレミスのソリューションに条件付きアクセスを依存する度合いが下がり、そのインフラストラクチャの稼働コストが削減されます。

## <a name="customer-case-studies"></a>お客様導入事例

ほとんどの組織では、条件に基づいてクラウド アプリにアクセスするために、Azure AD の条件付きアクセスを使用して、自動化されたアクセス制御の決定を定義および実装しますが、ここではその方法を紹介します。 以下の特集記事は、これらの顧客ニーズがどのようにして満たされるかを示しています。

* [**Wipro** は Microsoft のクラウド セキュリティ ツールによってモバイルの生産性を高め、顧客との関係を強化します。](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) 同社では、Azure AD の条件付きアクセス ポリシーによって、独自の企業データに対する統制は維持したまま、(各自の資格情報を使用できる) 信頼された外部エンティティとの間でドキュメント、リソース、およびアプリケーションを共有することが可能になりました。
* [**Accenture** は Microsoft Cloud App Security によってクラウドへの移行を磐石なものにします。](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)Accenture では、Cloud App Security が備える[アプリの条件付きアクセス制御](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)機能を評価しています。この機能では、Azure Active Directory の条件付きアクセスを使用して、特定の条件に基づいてアプリケーション アクセスをゲート制御します。 LePenske 氏は「この機能は、読み取り専用のファイル アクセスを許可しつつダウンロードは禁止するなどの目的に役立ちます」と述べています。
* [グローバル物流輸送企業の **Aramex** delivery limited は、ID とアクセスの管理ソリューションを用いて、クラウドで結び付いたオフィスを構築します。](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en) Aramex の遠隔地の従業員にとって、安全なアクセスを確保することは特に困難でした。 同社では現在、条件付きアクセスを応用して、こうした遠隔地の従業員がネットワークの外から同社の SaaS アプリケーションにアクセスできるようにしています。 条件付きアクセスのルールによって多要素認証の強制の有無を決定し、適切な従業員のみに適切なアクセスが付与されるようにしています。

Azure AD の条件付きアクセスに関する顧客およびパートナーのエクスペリエンスの詳細については、「[Azure によりすばらしい成果を上げているお客様](https://azure.microsoft.com/case-studies/?service=active-directory)」を参照してください。

## <a name="announcements"></a>アナウンス

Azure AD は随時改善されています。 最新情報や最近の動向については、「[Azure Active Directory の新着情報](../fundamentals/whats-new.md)」を参照してください。

Tech Community および Microsoft Identity Division の最新ブログ記事:

* 2018 年 9 月 24 日: [Azure Databricks における Azure Active Directory 条件付きアクセス](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 2018 年 9 月 21 日: [Azure AD の条件付きアクセス カスタム コントロールのパブリック プレビュー開始](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 2018 年 9 月 21 日: [Microsoft Cloud App Security での制限付きアクセスに対する Azure AD の条件付きアクセス サポートの提供開始](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 2018 年 9 月 21 日: [Azure AD の条件付きアクセス: iOS/Android プラットフォームの Managed Browser サポートがプレビュー開始](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 2018 年 9 月 21 日: [国番号に対する Azure AD の条件付きアクセスのパブリック プレビュー開始](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 2018 年 9 月 21 日: [Azure AD Terms of Use の提供開始](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>学習用のリソース

Azure AD の条件付きアクセスが機能するしくみの概要については、以下のリンクを参照してください。

* [Azure Active Directory の条件付きアクセス](overview.md)について学ぶ
* 「[Azure Active Directory 条件付きアクセスの条件の概要](conditions.md)」を知る
* 「[Azure Active Directory 条件付きアクセスの場所の条件の概要](location-condition.md)」を知る
* 「[Azure Active Directory 条件付きアクセスによるアクセス制御の概要](controls.md)」を知る
* 「[Azure Active Directory 条件付きアクセスの what if ツールの概要](what-if-tool.md)」を知る
* [Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)に従う

また、Azure Active Directory と統合されているすべてのサービスへのアクセスを保護するためのガイダンスについては、以下のリンクを参照してください。

* [ベースラインの保護とは (プレビュー)](baseline-protection.md)。 ベースラインの保護により、Azure Active Directory 環境で少なくともベースライン レベルのセキュリティを有効にします。
* [ID とデバイスのアクセスの構成](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)。 Enterprise Mobility + Security 製品を使用して、クラウド サービスへの安全なアクセスを構成する方法について説明します。これは、条件付きアクセス ポリシーと関連機能の所定のセットを含めた、推奨される環境と構成を実装することによって実現されます。
* [Azure Active Directory の条件付きアクセス設定に関するリファレンス](technical-reference.md)。 次のことを学びます:
   * 条件付きアクセスはどのアプリで使用されるか
   * 条件付きアクセスに対応したサービスにはどのようなものがあるか
* [Enable Azure Active Directory Conditional Access for Secure User Access](https://www.youtube.com/watch?v=eLAYBwjCGoA) (セキュリティで保護されたユーザー アクセスのために Azure Active Directory の条件付きアクセスを有効にする)。 このビデオでは、Enterprise Mobility Suite の他のワークロードで条件付きアクセスが果たす役割について解説しています。

### <a name="training-videos"></a>トレーニング ビデオ

**Conditional Access in Enterprise Mobility + Security** (Enterprise Mobility + Security での条件付きアクセス)
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Device-based Conditional Access** (デバイスベースの条件付きアクセス)
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Enable Azure Active Directory for Conditional Access for Secure User Access** (セキュリティで保護されたユーザー アクセスのために Azure Active Directory の条件付きアクセスを有効にする)
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>オンライン コース

[Pluralsight.com](https://www.pluralsight.com/) で、条件付きアクセスに関する以下のコースなどを参照してください。

* Pluralsight.com:[Design Identity Management in Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design) (Microsoft Azure での ID 管理の設計)
   * 「このコースでは、Azure AD を使用して ID 管理ソリューションを設計するために知っておく必要がある重要項目について解説します」。 Azure AD の条件付きアクセスについては、「Using Roles and Access Control with Azure AD」 (Azure AD でロールとアクセスの制御を使用する) モジュールで扱っています。

* Pluralsight.com:[Design Authentication for Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design) (Microsoft Azure の認証の設計)
   * 「このコースでは、Azure AD を使用して、すべてのクラウド認証要件を解決する方法について説明します」。 Azure AD の条件付きアクセスについては、「Authentication Requirements for Different Scenarios」 (さまざまなシナリオでの認証要件) モジュールで扱っています。

* Pluralsight.com:[Design Authorization for Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design) (Microsoft Azure の承認の設計)
   * 「このコースでは、Azure および Azure AD で利用できる認証オプションについて解説します」。 Azure AD の条件付きアクセスについては、「Authorization with Azure Resource Manager and Azure AD」 (Azure Resource Manager と Azure AD を使用した承認) モジュールで扱っています。

### <a name="books"></a>書籍

* O'Reilly- [Implementing Azure Solutions - Second Edition](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * 「Azure のサービスを準備して実行し、組織でそれらを実装する方法を学びましょう」。 Azure AD の条件付きアクセスについては、「[Deploying and Synchronizing Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml)」 (Azure Active Directory のデプロイと同期) の章で扱っています。

* Wiley- [Mastering Microsoft Azure Infrastructure Services](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * 「Microsoft Azure を利用する環境を理解し、評価し、デプロイし、保守するために必要なすべての情報が詰まっています」。

## <a name="white-papers"></a>ホワイト ペーパー

* 2018 年 12 月 18 日公開: [Azure Active Directory で回復性があるアクセス制御管理戦略を作成する](../authentication/concept-resilient-controls.md)
   * このドキュメントでは、予期されていない中断の間のロックアウトのリスクを軽減する回復性を提供するために、組織で採用できる戦略に関するガイダンスを示します。

* 2018 年 9 月 18 日公開: [アプリケーションを Azure Active Directory に移行するためのリソース](../manage-apps/migration-resources.md)
   * このホワイト ペーパーでは、アプリケーションのアクセスと認証を Azure Active Directory (Azure AD) に移行するために役立つリソースの一覧を示しています。

* 2018 年 7 月 12 日公開: [Azure のセキュリティとコンプライアンスのブループリント: UK OFFICIAL ワークロード向け PaaS Web アプリケーション ホスティング](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Azure Blueprint は、認定またはコンプライアンスの要件があるシナリオにソリューションを提供するために、クラウド ベースのアーキテクチャをデプロイするガイダンス ドキュメントと自動化テンプレートで構成されています。

## <a name="guidance-for-it-administrators"></a>IT 管理者向けガイダンス

[Azure portal](https://portal.azure.com/) にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。 「[Azure Active Directory での管理者ロールのアクセス許可](../users-groups-roles/directory-assign-admin-roles.md)」を参照してください。

IT 管理者は [Azure AD の条件付きアクセス](overview.md)を使用して、ユーザーに対し、Azure Multi-Factor Authentication を使用して認証を行うこと、また、信頼されたネットワークまたは信頼されたデバイスからサインインすることを要求します。

作業開始時に役立つリンクを以下に示します。

* [Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)
* [条件付きアクセス ポリシーから除外されているユーザーを管理するための Azure AD アクセス レビューの使用](../governance/conditional-access-exclusion.md)
* [方法:Azure Active Directory の条件付きアクセスの展開を計画する](plan-conditional-access.md)
* [クイック スタート:Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)
* [クイック スタート:クラウド アプリにアクセスする前に利用規約への同意を要求する](require-tou.md)
* [クイック スタート:Azure Active Directory の条件付きアクセスを使用して、セッションのリスクが検出されたときにアクセスをブロックする](app-sign-in-risk.md)
* [Azure Active Directory の条件付きアクセスに関する FAQ](faqs.md)
   * ここに掲載されていない質問がある場合、[MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure)も参照してください。
   * 問題に対する回答が見つからない場合は、Microsoft のサポート チームが随時、問題の解決をお手伝いします。 [[Microsoft サポートに問い合わせる]](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support) をご利用ください。

### <a name="tutorials"></a>チュートリアル

* [**クイック スタート:Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする**](app-based-mfa.md)
   * このクイック スタートでは、環境内で選択したクラウド アプリに対して多要素認証を必要とする Azure AD の条件付きアクセス ポリシーを構成する方法について説明します。

* [**クイック スタート:クラウド アプリにアクセスする前に利用規約への同意を要求する**](require-tou.md)
   * このクイック スタートでは、環境内で選択したクラウド アプリに対して ToU への同意を必要とする Azure AD の条件付きアクセス ポリシーを構成する方法について説明します。

* [**クイック スタート:Azure Active Directory の条件付きアクセスを使用して、セッションのリスクが検出されたときにアクセスをブロックする**](app-sign-in-risk.md)
   * このクイック スタートでは、構成されたサインイン リスク レベルが検出されたときにサインインをブロックする条件付きアクセス ポリシーを構成する方法を説明します。

* [チュートリアル:**Azure Portal での多要素認証が必要なクラシック ポリシーの移行**](policy-migration-mfa.md)
   * このチュートリアルでは、クラウド アプリの多要素認証 (MFA) が必要なクラシック ポリシーを移行する方法を示します。

## <a name="end-user-readiness-and-communication"></a>エンド ユーザーの準備とコミュニケーション

条件付きアクセスは、エンド ユーザーのエクスペリエンスに影響する可能性がある他の Azure AD 機能を使用します。 たとえば、Azure Multi-Factor Authentication を使用して、ユーザーにとって強力な認証を有効にすることができます。 その場合、Azure MFA のエンド ユーザーテンプレートを使用します。

## <a name="next-steps"></a>次の手順

* [条件付きアクセスのデプロイ計画に関するドキュメント](plan-conditional-access.md)を使用してデプロイを開始します。
