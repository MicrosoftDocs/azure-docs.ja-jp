---
title: Azure の ID とアクセスのセキュリティ保護に関するベスト プラクティス | Microsoft Docs
description: この記事では、Azure の組み込み機能を利用した ID 管理とアクセス制御に関する一連のベスト プラクティスについて説明します。
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/17/2018
ms.author: barclayn
ms.openlocfilehash: f872c61ad0597d2307cd244668fdfc258f7a45cb
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895688"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure の ID 管理とアクセス制御セキュリティのベスト プラクティス

ID はセキュリティの新しい境界レイヤーであり、従来のネットワーク中心の観点からその役割を引き継ぐものであると一般に考えられています。 セキュリティに関する注目と投資の主軸のこのような変化は、ネットワーク境界の侵入がますます容易になり、[BYOD](https://aka.ms/byodcg) デバイスとクラウド アプリケーションが爆発的に増加する前と比べて境界防御の有効性が低下しているという事実によるものです。

この記事では、Azure の ID 管理とアクセス制御のセキュリティに関するベスト プラクティスについて説明します。 このベスト プラクティスは、[Azure AD](../active-directory/fundamentals/active-directory-whatis.md) に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

それぞれのベスト プラクティスについて、次の点を説明します。

* ベスト プラクティスの内容
* そのベスト プラクティスをお勧めする理由
* そのベスト プラクティスを実践しなかった場合に発生する可能性がある事態
* そのベスト プラクティスに代わる対処法
* そのベスト プラクティスを実践する方法

この Azure の ID 管理とアクセス制御のセキュリティに関するベスト プラクティスの記事は、この記事の執筆時点における共通認識と、Azure プラットフォームの機能および機能セットに基づいています。 共通認識とテクノロジは時間が経つにつれて変化するため、そのような変化に対応するために、この記事は定期的に更新されます。

この記事では、Azure の ID 管理とアクセス制御のセキュリティに関するベスト プラクティスの次のような点について説明します。

* ID を主要なセキュリティ境界として扱う
* ID 管理を一元化する
* シングル サインオンの有効化
* 条件付きアクセスをオンにする
* パスワード管理を有効にする
* ユーザーに多要素認証を適用する
* ロールベースのアクセス制御を使用する
* 特権アカウントの公開時間を短縮する
* リソースが配置される場所を制御する

## <a name="treat-identity-as-the-primary-security-perimeter"></a>ID を主要なセキュリティ境界として扱う

多くの人々は、ID が主要なセキュリティ境界であると考えています。 これは、従来重視されていたネットワーク セキュリティからの転換です。 ネットワーク境界は浸透を続けており、その境界による防御の効果は [BYOD](https://aka.ms/byodcg) デバイスとクラウド アプリケーションが急増する前ほどは得られません。
[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) は ID とアクセスを管理するための Azure ソリューションです。 Azure AD は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 これには、主要なディレクトリ サービス、アプリケーション アクセスの管理、および ID 保護の機能が 1 つのソリューションとして統合されています。

以降のセクションでは、Azure AD を使用した ID とアクセスのセキュリティに対するベスト プラクティスを示します。

## <a name="centralize-identity-management"></a>ID 管理を一元化する

[ハイブリッド ID](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) のシナリオでは、オンプレミスとクラウドのディレクトリを統合することをお勧めします。 この統合により、アカウントの作成場所に関係なく、IT チームが 1 つの場所からアカウントを管理できるようになります。 また、クラウドとオンプレミスの両方のリソースにアクセスするための共通の ID が提供されるので、ユーザーの生産性が向上します。


**ベスト プラクティス**: オンプレミスのディレクトリと Azure AD を統合します。  
**詳細**: [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) を使用して、オンプレミスのディレクトリとクラウドのディレクトリを同期します。

**ベスト プラクティス**: パスワード ハッシュ同期をオンにします。  
**詳細**: パスワード ハッシュ同期は、オンプレミスの Active Directory インスタンスからクラウドベースの Azure Active Directory インスタンスにユーザー パスワード ハッシュのハッシュを同期するときに使用する機能です。

Active Directory フェデレーション サービス (AD FS) または他の ID プロバイダーでフェデレーションを使用する場合でも、オンプレミスのサーバーがエラーになるか一時的に利用不可になった場合のバックアップとしてパスワード ハッシュ同期を設定することもできます。 これにより、ユーザーは、オンプレミスの Active Directory インスタンスにサインインするときに使うものと同じパスワードを使用してサービスにサインインできます。 また、ユーザーが Azure AD に接続されていない他のサービスと同じ電子メール アドレスおよびパスワードを使用している場合に、Identity Protection では、これらのパスワード ハッシュを侵害が検知されたパスワードと比較して、侵害された資格情報を検出できます。

詳細については、「[Azure AD Connect 同期を使用したパスワード ハッシュ同期の実装](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)」をご覧ください。

オンプレミスの ID とクラウドの ID を統合していない組織では、アカウント管理の際により多くのオーバーヘッドが発生する可能性があります。 このオーバーヘッドによって、ミスやセキュリティ違反の可能性が高まります。

## <a name="enable-single-sign-on"></a>シングル サインオンの有効化

モバイルを重視したクラウド中心の世界では、あらゆる場所からデバイス、アプリ、およびサービスへのシングル サインオン (SSO) を可能にして、時間や場所に関係なくユーザーの生産性を向上する必要があります。 管理対象の ID ソリューションが複数あると、IT 部門にとって管理上の問題となるだけでなく、複数のパスワードを覚えておく必要があるユーザーにとっても問題です。

すべてのアプリとリソースで同じ ID ソリューションを使用することにより、SSO が実現されます。 また、ユーザーは、必要なリソースがオンプレミスまたはクラウドのどちらにあっても、同じ資格情報セットを使用してリソースにサインインしてアクセスできます。

**ベスト プラクティス**: SSO を有効にします。  
**詳細**: Azure AD はクラウドに[オンプレミス Active Directory を拡張](../active-directory/connect/active-directory-aadconnect.md)します。 ユーザーは、主要な職場または学校アカウントを、ドメイン参加済みデバイス、会社のリソース、および作業を完了させるために必要なすべての Web アプリケーションと SaaS アプリケーションに使用することができます。 ユーザーは複数のユーザー名とパスワードのセットを覚える必要がなくなり、組織のグループ メンバーシップや従業員としての地位に基づいて、ユーザーのアプリケーション アクセスが自動的にプロビジョニング (またはプロビジョニング解除) されるようにすることができます。 また、ギャラリー アプリ、または [Azure AD アプリケーション プロキシ](../active-directory/active-directory-application-proxy-get-started.md)で開発および公開した独自のオンプレミス アプリについてそのアクセスを制御できます。

SSO を使用すると、ユーザーは Azure AD 内の職場または学校アカウントに基づいて [SaaS アプリケーション](../active-directory/active-directory-appssoaccess-whatis.md)にアクセスできます。 これは、Microsoft SaaS アプリだけでなく、[Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) や [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md) などの他のアプリにも当てはまります。 [SAML ベースの ID](../active-directory/fundamentals-identity.md) プロバイダーとして Azure AD を使用するように、アプリケーションを構成できます。 セキュリティ コントロールの目的で、Azure AD では、ユーザーに Azure AD を使用するアクセス権が付与されない限り、アプリケーションへのサインインを許可するトークンは発行されません。 ユーザーに対してアクセスを直接許可することも、ユーザーがメンバーであるグループを介して許可することもできます。

SSO を確立するためにユーザーやアプリケーションに共通の ID を作成しない組織では、ユーザーが複数のパスワードを所有するシナリオに陥ります。 このようなシナリオでは、ユーザーがパスワードを再利用したり、脆弱なパスワードを使用したりする可能性が高くなります。

## <a name="turn-on-conditional-access"></a>条件付きアクセスをオンにする

ユーザーはさまざまなデバイスやアプリを使用してどこからでも組織のリソースにアクセスできます。 IT 管理者は、これらのデバイスがセキュリティとコンプライアンスの標準を満たしているかどうかを確認する必要があります。 だれがリソースにアクセスできるかに重点を置くだけでは十分ではなくなっています。

セキュリティと生産性のバランスを取るためには、アクセスの制御を決定する前にリソースへのアクセス方法を考慮する必要があります。 Azure AD の条件付きアクセスで、この要件に対処することができます。 条件付きアクセスを使用すると、クラウド アプリへのアクセスを許可するかどうかの判断を、各種の条件を基にアクセスの制御によって自動的に行うことができます。

**ベスト プラクティス**: 会社のリソースへのアクセスを管理および制御します。  
**詳細**: グループ、場所、アプリケーションの機密性に基づいて SaaS アプリや Azure AD 接続アプリの Azure AD [条件付きアクセス](../active-directory/active-directory-conditional-access-azure-portal.md)を構成します。

## <a name="enable-password-management"></a>パスワード管理を有効にする

複数のテナントがある場合、またはユーザーが[自分のパスワードをリセット](../active-directory/active-directory-passwords-update-your-own-password.md)できるようにする場合は、適切なセキュリティ ポリシーを使用して不適切な使用を防止することが重要です。

**ベスト プラクティス**: ユーザーに対してパスワード リセットのセルフサービス (SSPR) を設定します。  
**詳細**: Azure AD の[パスワード リセットのセルフサービス](../active-directory-b2c/active-directory-b2c-reference-sspr.md)機能を使用します。

**ベスト プラクティス**: SSPR が実際に使用されているかどうか、またはその使用方法を監視します。  
**詳細**: Azure AD の[パスワード リセット登録アクティビティ レポート](../active-directory/active-directory-passwords-get-insights.md)を使用して、登録しているユーザーを監視します。 Azure AD で提供されるレポート機能によって、質問に対する答えをあらかじめ用意されたレポートから得ることができます。 適切にライセンスを付与されている場合は、カスタム クエリを作成することもできます。

## <a name="enforce-multi-factor-verification-for-users"></a>ユーザーに多要素認証を適用する

すべてのユーザーに対して 2 段階認証を要求することをお勧めします。 これには、組織内の管理者およびアカウントが侵害された場合に重大な影響を及ぼす可能性のあるその他のユーザー (財務担当者など) が含まれます。

2 段階認証を要求するオプションは複数あります。 最適なオプションは、目的、実行している Azure AD エディション、およびライセンス プログラムによって異なります。 最適なオプションを判断するには、「[ユーザーに 2 段階認証を要求する方法](../active-directory/authentication/howto-mfa-userstates.md)」を参照してください。 ライセンスと価格の詳細については、[Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) と [Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) の価格に関するページを参照してください。

2 段階認証を有効にするオプションと利点を次に示します。

**オプション 1**:[ユーザーの状態を変更することで Multi-Factor Authentication を有効にします](../active-directory/authentication/howto-mfa-userstates.md)。   
**利点**:2 段階認証を要求するための従来の方法です。 これは、[クラウド内の Azure Multi-Factor Authentication と Azure Multi-Factor Authentication Server](../active-directory/authentication/concept-mfa-whichversion.md) の両方に対応します。 この方法を使用すると、ユーザーはサインインする際に毎回 2 段階認証が求められるようになります。また、この方法は条件付きアクセス ポリシーをオーバーライドします。

**オプション 2**:[条件付きアクセス ポリシーを使用して Multi-Factor Authentication を有効にします](../active-directory/authentication/howto-mfa-getstarted.md)。
**利点**:このオプションでは、[条件付きアクセス](../active-directory/active-directory-conditional-access-azure-portal.md)を使用して特定の条件下で 2 段階認証を要求できます。 特定の条件としては、異なる場所、信頼されていないデバイス、または危険と見なされるアプリケーションからのユーザーのサインインを指定できます。 2 段階認証を要求する特定の条件を定義すると、要求のメッセージがユーザーに繰り返し表示されないようにすることができます。このようなメッセージは、不快なユーザー エクスペリエンスとなり得ます。

これは、ユーザーの 2 段階認証を有効にするうえで最も柔軟性の高い手段です。 条件付きアクセス ポリシーを有効にする方法は、クラウド内の Azure Multi-Factor Authentication に対してのみ機能します。これは Azure AD の Premium 機能です。 この方法の詳細については、「[クラウドベースの Azure Multi-Factor Authentication をデプロイする](../active-directory/authentication/howto-mfa-getstarted.md)」を参照してください。

**オプション 3**:[Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md) のユーザーおよびサインインのリスクを評価し、条件付きアクセス ポリシーを使用して Multi-Factor Authentication を有効にします。   
**利点**:このオプションの利点は次のとおりです。

- 組織の ID に影響する潜在的な脆弱性を検出します。
- 組織の ID に関連する検出された疑わしいアクションに対する自動応答を構成します。
- 疑わしいインシデントを調査し、適切なアクションを実行して解決します。

この方法では、Azure AD Identity Protection のリスク評価を使用して、すべてのクラウド アプリケーションのユーザーおよびサインインのリスクに基づいて 2 段階認証が要求されるかどうかを判断します。 この方法では、Azure Active Directory P2 ライセンスが必要です。 この方法の詳細については、「[Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md)」を参照してください。

> [!Note]
> オプション 1 (ユーザーの状態を変更することで Multi-Factor Authentication を有効にする) は、条件付きアクセス ポリシーをオーバーライドします。 オプション 2 および 3 では条件付きアクセス ポリシーを使用するため、オプション 1 をそれらと共に使用することはできません。

2 段階認証などの新しい ID 保護レイヤーを追加しない組織は、資格情報盗用攻撃を受けやすくなります。 資格情報盗用攻撃はデータの侵害につながる可能性があります。

## <a name="use-role-based-access-control-rbac"></a>ロール ベースのアクセス制御 (RBAC) を使用する

データ アクセスにセキュリティ ポリシーを適用する組織では、[必知事項](https://en.wikipedia.org/wiki/Need_to_know)と[最小権限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)のセキュリティ原則に基づいてアクセスを制限することが不可欠です。 [ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) を使用して、特定のスコープ内のユーザー、グループ、アプリケーションにアクセス許可を割り当てることができます。 ロール割り当てのスコープには、サブスクリプション、リソース グループ、または単独のリソースを指定できます。

Azure の[組み込み RBAC](../role-based-access-control/built-in-roles.md) ロールを使用して、ユーザーに権限を割り当てることができます。 RBAC などの機能を使用したデータ アクセス制御を適用しない組織では、ユーザーに必要以上の権限が付与される可能性があります。 これにより、ユーザーがアクセスする必要のない種類のデータ (ビジネスへの影響が高いものなど) にアクセスできるようになり、データのセキュリティ侵害につながる恐れがあります。

## <a name="lower-exposure-of-privileged-accounts"></a>特権アカウントの公開時間を短縮する

特権アクセスのセキュリティ保護は、ビジネス資産を保護するうえで重要な最初のステップです。 セキュリティで保護された情報またはリソースへのアクセス権を持つユーザーの数を最小限に抑えることで、悪意のあるユーザーがこのようなアクセス権を手にしたり、権限のあるユーザーの不注意で機微なリソースのセキュリティが損なわれたりする可能性が抑えられます。

特権アカウントとは、IT システムを管理するアカウントです。 サイバー攻撃では、組織のデータやシステムへのアクセス手段を得るために、このようなアカウントが標的にされます。 特権アクセスを保護するには、悪意のあるユーザーにさらされる危険からアカウントとシステムを分離する必要があります。

サイバー攻撃者から特権アクセスを保護するためのロードマップを作成して従うことをお勧めします。 Azure AD、Microsoft Azure、Office 365、およびその他のクラウド サービスで管理または報告される ID とアクセスを保護するための詳細なロードマップの作成については、「[Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](../active-directory/users-groups-roles/directory-admin-roles-secure.md)」を参照してください。

「[Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](../active-directory/users-groups-roles/directory-admin-roles-secure.md)」に記載されているベスト プラクティスを以下にまとめています。

**ベスト プラクティス**: 特権アカウントへのアクセスを管理、制御、および監視します。   
**詳細**: [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md) を有効にします。 Privileged Identity Management を有効にすると、特権アクセス ロールが変更されたという電子メール通知を受け取ります。 これらの通知は、ディレクトリ内の高度な特権ロールに他のユーザーが追加された場合に早期警告を提供します。

**ベスト プラクティス**: 高度な特権ロールに属するアカウントを識別および分類します。   
**詳細**: Azure AD Privileged Identity Management を有効にした後、グローバル管理者、特権ロール管理者、およびその他の高度な特権ロールに属するユーザーを表示します。 これらのロールで不要になったアカウントを削除し、管理者ロールに割り当てられている残りのアカウントを分類します。

- 管理ユーザーに個別に割り当てられ、管理以外の目的 (たとえば、個人用電子メール) に使用できる
- 管理ユーザーに個別に割り当てられ、管理目的にのみ指定されている
- 複数のユーザー間で共有される
- 非常時のアクセス シナリオ用
- 自動スクリプト用
- 外部ユーザー用

**ベスト プラクティス**: "Just-In-Time" (JIT) アクセスを実装して、権限が公開される時間をさらに短縮し、特権アカウントの使用の可視性を向上します。   
**詳細**: Azure AD Privileged Identity Management では、次のことが可能です。

- ユーザーが自分の権限の JIT のみを利用するよう制限します。
- 権限が自動的に取り消される、短縮された期間のロールを割り当てます。

**ベスト プラクティス**: 少なくとも 2 つの緊急アクセス用アカウントを定義します。   
**詳細**: 緊急アクセス用アカウントは、組織において既存の Azure Active Directory 環境での特権アクセスを制限するのに役立ちます。 このようなアカウントは高い特権を持っており、特定のユーザーには割り当てられません。 緊急アクセス用アカウントは、通常の管理者アカウントを使うことができないシナリオに制限されます。 組織では、緊急用アカウントの使用を必要な時間だけに制限する必要があります。

グローバル管理者ロールが割り当てられているか、その対象であるアカウントを評価します。 `*.onmicrosoft.com` ドメイン (緊急アクセスが目的) を使用してクラウド専用アカウントが見当たらない場合は、それらを作成します。 詳細については、「Azure AD で緊急アクセス用管理者アカウントを管理する」を参照してください。

**ベスト プラクティス**: Multi-Factor Authentication を有効にし、その他のすべての高度な特権を持つシングル ユーザー非フェデレーション管理者アカウントを登録します。  
**詳細**: 1 つまたは複数の Azure AD 管理者ロール (グローバル管理者、特権ロール管理者、Exchange Online 管理者、SharePoint Online 管理者) に永続的に割り当てられているすべての個々のユーザーのサインイン時に Azure Multi-Factor Authentication が必要です。 ガイドを使用して[管理者アカウントの Multi-Factor Authentication](../active-directory/authentication/howto-mfa-userstates.md) を有効にし、それらすべてのユーザーが[登録](https://aka.ms/mfasetup)されていることを確認します。

**ベスト プラクティス**: 最も頻繁に使用される攻撃手法を軽減するための対策を講じます。  
**詳細**: [職場または学校アカウントに切り替える必要がある管理者ロールの Microsoft アカウントを特定する](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[グローバル管理者アカウントのユーザー アカウントとメール転送を分離する](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[管理者アカウントのパスワードが最近変更されたことを確認する](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[パスワード ハッシュ同期をオンにする](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[すべての特権ロールに属するユーザーおよび露出しているユーザーに多要素認証を要求する](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Office 365 セキュリティ スコアを取得する (Office 365 を使用している場合)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Office 365 セキュリティおよびコンプライアンス ガイダンスを確認する (Office 365 を使用している場合)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Office 365 アクティビティの監視を構成する (Office 365 を使用している場合)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[インシデント/緊急時対応計画の所有者を設定する](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[オンプレミスの特権管理者アカウントをセキュリティで保護する](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

特権アクセスをセキュリティで保護しない場合は、高度な特権ロールに属するユーザーが多すぎて攻撃を受けやすくなっている可能性があります。 多くの場合、サイバー攻撃者を含む悪意のあるアクターは、管理者アカウントやその他の特権アクセスの要素をターゲットとして、資格情報盗用を使用して機密データやシステムへのアクセスを取得します。

## <a name="control-locations-where-resources-are-created"></a>リソースが作成される場所を制御する

クラウド事業者がタスクを実行できるようにする一方で、組織のリソースの管理に必要な規則に違反しないようにすることが、非常に重要です。 リソースが作成される場所を制御する必要のある組織では、これらの場所をハード コードする必要があります。

[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) を使用すると、明示的に拒否されるアクションまたはリソースが記述されている定義を含むセキュリティ ポリシーを作成できます。 サブスクリプション、リソース グループ、個別リソースなど、任意の範囲でポリシー定義を割り当てます。

> [!NOTE]
> セキュリティ ポリシーは RBAC とは異なります。 実際には、RBAC を使用して、ユーザーがこれらのリソースを作成することを許可します。
>
>

リソースの作成方法を制御しないと、ユーザーは必要量より多くのリソースを作成することによってサービスを不正使用する可能性が高くなります。 リソースの作成プロセスを強化することは、マルチテナントのシナリオをセキュリティ保護するための重要な手順です。

## <a name="actively-monitor-for-suspicious-activities"></a>疑わしいアクティビティを能動的に監視する

アクティブな ID 監視システムでは、疑わしい動作をすばやく検出して、さらなる調査を行うためのアラートをトリガーします。 組織による ID の監視に役立つ 2 つの Azure AD 機能を次に示します。

**ベスト プラクティス**: 次の動作を識別するための方法を用意します。

- [追跡されない](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md)サインインの試行
- 特定のアカウントに対する[ブルート フォース](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md)攻撃
- 複数の場所からのサインインの試行
- [感染しているデバイスからのサインイン](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md)
- 疑わしい IP アドレス

**詳細**: Azure AD Premium の[異常レポート](../active-directory/active-directory-view-access-usage-reports.md)を使用します。 IT 管理者がこれらのレポートを毎日または必要に応じて (通常はインシデント対応シナリオ) 実行するためのプロセスと手順を設けます。

**ベスト プラクティス**: リスクを通知し、ビジネス要件に合わせてリスク レベル (高、中、低) を調整できるアクティブな監視システムを用意します。   
**詳細**: [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md) を使用します。この製品では、独自のダッシュボードで現在のリスクにフラグを設定し、概要の通知を毎日電子メールで送信します。 指定したリスク レベルに達したときに、検出された問題が自動的に対処されるようにリスク ベースのポリシーを構成することで、組織の ID を保護できます。

ID システムを能動的に監視しないと、ユーザーの資格情報が侵害されるリスクがあります。 侵害された資格情報を用いた疑わしい活動が行われていることを把握しないと、この種の脅威を緩和することはできません。

## <a name="next-step"></a>次のステップ

Azure を使用してクラウド ソリューションを設計、デプロイ、管理するときに使用するセキュリティのベスト プラクティスの詳細については、「[Azure セキュリティのベスト プラクティスとパターン](security-best-practices-and-patterns.md)」を参照してください。
