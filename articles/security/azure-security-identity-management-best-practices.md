---
title: "Azure の ID とアクセスのセキュリティ保護に関するベスト プラクティス | Microsoft Docs"
description: "この記事では、Azure の組み込み機能を利用した ID 管理とアクセス制御に関する一連のベスト プラクティスについて説明します。"
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 3cba38d95535ff5ed3cd62aac5c0aa04a310f48c
ms.openlocfilehash: 50f9073d3c35bd9dcfd826ff44e767fb69558757


---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure の ID 管理とアクセス制御セキュリティのベスト プラクティス
ID はセキュリティの新しい境界レイヤーであり、従来のネットワーク中心の観点からその役割を引き継ぐものであると一般に考えられています。 セキュリティに関する注目と投資の主軸のこのような変化は、ネットワーク境界の侵入がますます容易になり、[BYOD](http://aka.ms/byodcg) デバイスとクラウド アプリケーションが爆発的に増加する前と比べて境界防御の有効性が低下しているという事実によるものです。

この記事では、Azure の ID 管理とアクセス制御のセキュリティに関するベスト プラクティスについて説明します。 このベスト プラクティスは、[Azure AD](../active-directory/active-directory-whatis.md) に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

それぞれのベスト プラクティスについて、次の点を説明します。

* ベスト プラクティスの内容
* そのベスト プラクティスをお勧めする理由
* そのベスト プラクティスを実践しなかった場合に発生する可能性がある事態
* そのベスト プラクティスに代わる対処法
* そのベスト プラクティスを実践する方法

この Azure の ID 管理とアクセス制御のセキュリティに関するベスト プラクティスの記事は、この記事の執筆時点における共通認識と、Azure プラットフォームの機能および機能セットに基づいています。 共通認識とテクノロジは時間が経つにつれて変化するため、そのような変化に対応するために、この記事は定期的に更新されます。

この記事では、Azure の ID 管理とアクセス制御のセキュリティに関するベスト プラクティスの次のような点について説明します。

* ID 管理を一元化する
* シングル サインオン (SSO) を有効にする
* パスワード管理をデプロイする
* 多要素認証 (MFA) をユーザーに適用する
* ロールベースのアクセス制御 (RBAC) を使用する
* リソース マネージャーを使用してリソースが作成される場所を制御する
* SaaS アプリの ID 機能を利用するよう開発者に指示する
* 疑わしいアクティビティを能動的に監視する

## <a name="centralize-your-identity-management"></a>ID 管理を一元化する
ID のセキュリティ保護に向けた重要なステップの&1; つは、アカウントが作成された場所に関係なく、1 つの場所からアカウントを管理できるようにすることです。 大半の企業の IT 組織では主要なアカウント ディレクトリがオンプレミスにありますが、ハイブリッド クラウドのデプロイが増えており、オンプレミスのディレクトリとクラウドのディレクトリを統合して、シームレスなエクスペリエンスをエンド ユーザーに提供する方法を理解しておくことが重要です。

この[ハイブリッド ID](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md) シナリオを実現するには、2 つのオプションをお勧めします。

* Azure AD Connect を使用して、オンプレミスのディレクトリとクラウドのディレクトリを同期する
* [Active Directory フェデレーション サービス](https://msdn.microsoft.com/library/bb897402.aspx) (AD FS) を使用して、オンプレミスの ID とクラウドのディレクトリを統合する

オンプレミスの ID とクラウドの ID を統合しないと、アカウント管理のオーバーヘッドが増加し、間違いやセキュリティ違反が発生する可能性が高くなります。

Azure AD 同期の詳細については、「[オンプレミス ID と Azure Active Directory の統合](../active-directory/active-directory-aadconnect.md)」をご覧ください。

## <a name="enable-single-sign-on-sso"></a>シングル サインオン (SSO) を有効にする
管理対象のディレクトリが複数あると、IT 部門にとって管理上の問題となるだけでなく、複数のパスワードを覚えておく必要があるエンド ユーザーにとっても問題です。 [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) を使用することにより、ユーザーは、必要なリソースがオンプレミスまたはクラウドのどちらにあっても、同じ資格情報セットを使用してリソースにサインインしてアクセスできます。

SSO を使用すると、ユーザーは Azure AD 内の組織アカウントに基づいて[SaaS アプリケーション](../active-directory/active-directory-appssoaccess-whatis.md)にアクセスできます。 これは、Microsoft SaaS アプリだけでなく、[Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) や [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md) などの他のアプリにも当てはまります。 [SAML ベースの ID](../active-directory/fundamentals-identity.md) プロバイダーとして Azure AD を使用するように、アプリケーションを構成できます。 セキュリティ コントロールの目的で、Azure AD では、ユーザーに Azure AD を使用するアクセス権が付与されない限り、アプリケーションへのサインインを許可するトークンは発行されません。 ユーザーに対してアクセスを直接許可することも、ユーザーがメンバーであるグループを介して許可することもできます。

> [!NOTE]
> SSO を使用するかどうかにより、オンプレミスのディレクトリとクラウド ディレクトリの統合方法に影響があります。 SSO が必要な場合、ディレクトリ同期は[同じサインオン エクスペリエンス](../active-directory/active-directory-aadconnect.md)だけを提供するので、フェデレーションを使用する必要があります。
>
>

ユーザーとアプリケーションに SSO を適用しないと、ユーザーは複数のパスワードを使用するようになり、パスワードを再利用したり、脆弱なパスワードを使用したりする可能性が高くなります。

Azure AD の SSO の詳細については、「[Azure AD Connect を使用した AD FS の管理とカスタマイズ](../active-directory/active-directory-aadconnect-federation-management.md)」をご覧ください。

## <a name="deploy-password-management"></a>パスワード管理をデプロイする
複数のテナントがある場合、またはユーザーが[自分のパスワードをリセット](../active-directory/active-directory-passwords-update-your-own-password.md)できるようにする場合は、適切なセキュリティ ポリシーを使用して不適切な使用を防止することが重要です。 Azure では、セルフ サービスのパスワード リセット機能を利用し、ビジネス ニーズに合わせてセキュリティ オプションをカスタマイズすることができます。

ユーザーからフィードバックを収集し、これらの手順を実行したときの経験から学ぶことが特に重要です。 ユーザーの経験を基にして、大きなグループへのデプロイで発生する可能性がある問題を軽減する詳細な計画を作成します。 また、[パスワード リセット登録アクティビティ レポート](../active-directory/active-directory-passwords-get-insights.md)を使用して登録しているユーザーを監視することもお勧めします。

サポートへのパスワード変更依頼をなくそうとしても、ユーザーが自分でパスワードをリセットできるようにすると、パスワードに関する問題のためのサービス デスクへの問い合わせ件数が増加する可能性があります。 複数のテナントがある組織では、この種の機能を実装して、セキュリティ ポリシーで確立されたセキュリティ境界内でユーザーがパスワードをリセットできるようにする必要があります。

パスワード リセットの詳細については、「[Password Management のデプロイとユーザー トレーニング](../active-directory/active-directory-passwords-best-practices.md)」をご覧ください。

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>多要素認証 (MFA) をユーザーに適用する
[PCI DSS バージョン 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32) などの業界標準に準拠する必要がある組織では、多要素認証がユーザー認証のために必須の機能です。 MFA を使用してユーザーを認証すると、業界標準に準拠するだけでなく、[Pass-the-Hash (PtH)](http://aka.ms/PtHPaper) などの資格情報盗難型の攻撃の緩和にも役立ちます。

ユーザーに対する Azure MFA を有効にすると、ユーザーのサインインとトランザクションに新しいセキュリティ層が追加されます。 この場合、ファイル サーバーまたは SharePoint Online 上にあるドキュメントにアクセスするトランザクションが発生する可能性があります。 また、Azure MFA は不正に取得された資格情報によって組織データにアクセスされる危険を減らすためにも役立ちます。

たとえば、ユーザーに Azure MFA を適用し、検証手段として電話またはテキスト メッセージを使用するように MFA を構成します。 ユーザーの資格情報が侵害された場合でも、攻撃者はユーザーの電話にアクセスできないので、リソースにアクセスすることはできません。 新しい ID 保護層を追加しない場合、資格情報盗用攻撃を受けやすくなり、データの侵害につながる可能性があります。

認証全体をオンプレミスで管理する必要がある場合は、代わりに [Azure Multi-Factor Authentication Server](../multi-factor-authentication/multi-factor-authentication-get-started-server.md) (MFA オンプレミスとも呼ばれます) を利用できます。 この手法を利用すると、オンプレミスに MFA サーバーを置いていても、多要素認証を適用することができます。

Azure MFA の詳細については、「[クラウドでの Azure Multi-Factor Authentication Server の概要](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)」をご覧ください。

## <a name="use-role-based-access-control-rbac"></a>ロールベースのアクセス制御 (RBAC) を使用する
データ アクセスにセキュリティ ポリシーを適用する組織では、[必知事項](https://en.wikipedia.org/wiki/Need_to_know)と[最小権限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)のセキュリティ原則に基づいてアクセスを制限することが不可欠です。 Azure のロールベースのアクセス制御 (RBAC) を使用して、特定のスコープ内のユーザー、グループ、アプリケーションにアクセス許可を割り当てることができます。 ロール割り当てのスコープには、サブスクリプション、リソース グループ、または単独のリソースを指定できます。

Azure の[組み込み RBAC ロール](../active-directory/role-based-access-built-in-roles.md)を利用して、ユーザーに権限を割り当てることができます。 クラウド事業者は、ストレージ アカウントを管理する必要がある場合は*ストレージ アカウント作成協力者*ロール、従来のストレージ アカウントを管理する場合は*従来のストレージ アカウント作成協力者*ロールの使用を検討してください。 VM とストレージ アカウントを管理する必要があるクラウド事業者は、それを*仮想マシン作成協力者*ロールに追加することを検討してください。

RBAC などの機能を利用したデータ アクセス制御を適用しない場合、ユーザーに必要以上の権限が付与される可能性があります。 これにより、ユーザーがアクセスする必要のない種類のデータ (ビジネスへの影響が高いものなど) にアクセスできるようになり、データのセキュリティ侵害につながる恐れがあります。

Azure RBAC の詳細については、「[Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)」をご覧ください。

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>リソース マネージャーを使用してリソースが作成される場所を制御する
クラウド事業者がタスクを実行できるようにする一方で、組織のリソースの管理に必要な規則に違反しないようにすることが、非常に重要です。 リソースが作成される場所を制御する必要のある組織では、これらの場所をハード コードする必要があります。

そのためには、明示的に拒否されるアクションまたはリソースが記述されている定義を含むセキュリティ ポリシーを作成できます。 サブスクリプション、リソース グループ、個別リソースなど、任意の範囲でポリシー定義を割り当てます。

> [!NOTE]
> これは RBAC と同じことではなく、実際には RBAC を利用して、リソースを作成する権限を持つユーザーを認証します。
>
>

適切なコスト センターが関連付けられている場合にだけ操作を許可し、それ以外の要求は拒否する場合も、[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) を利用してカスタム ポリシーを作成します。

リソースの作成方法を制御しないと、ユーザーは必要量より多くのリソースを作成することによってサービスを不正使用する可能性が高くなります。 リソースの作成プロセスを強化することは、マルチ テナントのシナリオをセキュリティ保護するための重要な手順です。

Azure Resource Manager でのポリシー作成の詳細については、「[ポリシーを使用したリソース管理とアクセス制御](../azure-resource-manager/resource-manager-policy.md)」をご覧ください。

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>SaaS アプリの ID 機能を利用するよう開発者に指示する
ユーザー ID は、オンプレミスのディレクトリまたはクラウド ディレクトリと統合できる [SaaS アプリ](https://azure.microsoft.com/marketplace/active-directory/all/)にユーザーがアクセスする多くのシナリオで利用されます。 何よりもまず、開発者には、[Microsoft セキュリティ開発ライフサイクル (SDL)](https://www.microsoft.com/sdl/default.aspx) などの安全な方法を使用してアプリを開発することをお勧めします。 Azure AD は、[OAuth 2.0](http://oauth.net/2/) や [OpenID Connect](http://openid.net/connect/) などの業界標準プロトコルをサポートする Identity as a Service と、さまざまなプラットフォーム向けのオープン ソース ライブラリを提供することで、開発者のために認証を簡素化します。

Azure AD に認証を委託するすべてのアプリケーションを登録してください。これは必須の手順です。 なぜなら、サインオン (SSO) を処理するとき、またはトークンを交換するときに、Azure AD はアプリケーションとの通信を調整する必要があるためです。 Azure AD によって発行されたトークンの有効期間が終了すると、ユーザーのセッションの有効期限が切れます。 アプリケーションでこの時間を使用する必要があるか、またはこの時間を短縮できるかを、常に評価する必要があります。 有効期間の短縮は、非アクティブな時間に基づいてユーザーを強制的にサインアウトさせるセキュリティ手段として利用できます。

アプリへのアクセスに対して ID 管理を適用せず、アプリと ID 管理システムを安全に統合する方法を開発者に示さないと、[Open Web Application Security Project (OWASP) の上位 10 項目で説明されている弱い認証とセッション管理](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet)のような資格情報盗難型の攻撃を受けやすくなる可能性があります。

SaaS アプリの認証シナリオの詳細については、「[Azure AD の認証シナリオ](../active-directory/active-directory-authentication-scenarios.md)」をご覧ください。

## <a name="actively-monitor-for-suspicious-activities"></a>疑わしいアクティビティを能動的に監視する
[Verizon の 2016 データ侵害レポート](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/)によると、資格情報の侵害はまだ増加しており、サイバー犯罪者が最も利益を得やすいビジネスの 1 つになっています。 そのため、疑わしい活動をすばやく検出して詳しい調査のためのアラートをトリガーできる能動的な ID 監視システムを設けることが重要です。 Azure AD には ID の監視に役立つ&2; つの主要な機能があります。Azure AD Premium の[異常レポート](../active-directory/active-directory-view-access-usage-reports.md)と、Azure AD [Identity Protection](../active-directory/active-directory-identityprotection.md) 機能です。

異常レポートを使用して、[追跡されない](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md)サインインの試行、特定のアカウントに対する[ブルート フォース](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md)攻撃、複数の場所からのサインインの試行、[感染したデバイス](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md)からのサインイン、疑わしい IP アドレスを検出する必要があります。 これらはレポートであることに注意してください。 つまり、IT 管理者がこれらのレポートを毎日または必要に応じて (通常はインシデント対応シナリオ) 実行するためのプロセスと手順を設ける必要があります。

一方、Azure AD Identity Protection は能動的な監視システムであり、独自のダッシュボードに現在のリスクが表示されます。 さらに、毎日電子メールで概要の通知も送られます。 ビジネス ニーズに合わせて、リスク レベルを調整することをお勧めします。 リスク イベントのリスク レベルは、リスク イベントの重大度 (高、中、低) を示します。 リスク レベルは、Identity Protection のユーザーが組織に対するリスクの軽減に必要なアクションの優先順位を決定するときの参考になります。

ID システムを能動的に監視しないと、ユーザーの資格情報が侵害されるリスクがあります。 侵害された資格情報を用いた疑わしい活動が行われていることを把握しないと、この種の脅威を緩和することはできません。
Azure Identity Protection の詳細については、「[Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)」をご覧ください。



<!--HONumber=Jan17_HO5-->


