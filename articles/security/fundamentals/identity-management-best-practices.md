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
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2019
ms.author: barclayn
ms.openlocfilehash: 093c5878cd2f7df63502a7aff686824af3c88078
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195072"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure の ID 管理とアクセス制御セキュリティのベスト プラクティス
この記事では、Azure の ID 管理とアクセス制御のセキュリティに関するベスト プラクティスについて説明します。 このベスト プラクティスは、[Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

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
* 接続済みテナントを管理する
* シングル サインオンの有効化
* 条件付きアクセスをオンにする
* パスワード管理を有効にする
* ユーザーに多要素認証を適用する
* ロールベースのアクセス制御を使用する
* 特権アカウントの公開時間を短縮する
* リソースが配置される場所を制御する
* ストレージの認証に Azure AD を使用する

## <a name="treat-identity-as-the-primary-security-perimeter"></a>ID を主要なセキュリティ境界として扱う

多くの人々は、ID が主要なセキュリティ境界であると考えています。 これは、従来重視されていたネットワーク セキュリティからの転換です。 ネットワーク境界は浸透を続けており、その境界による防御の効果は [BYOD](https://aka.ms/byodcg) デバイスとクラウド アプリケーションが急増する前ほどは得られません。

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) は ID とアクセスを管理するための Azure ソリューションです。 Azure AD は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 これには、主要なディレクトリ サービス、アプリケーション アクセスの管理、および ID 保護の機能が 1 つのソリューションとして統合されています。

以降のセクションでは、Azure AD を使用した ID とアクセスのセキュリティに対するベスト プラクティスを示します。

## <a name="centralize-identity-management"></a>ID 管理を一元化する

[ハイブリッド ID](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) のシナリオでは、オンプレミスとクラウドのディレクトリを統合することをお勧めします。 この統合により、アカウントの作成場所に関係なく、IT チームが 1 つの場所からアカウントを管理できるようになります。 また、クラウドとオンプレミスの両方のリソースにアクセスするための共通の ID が提供されるので、ユーザーの生産性が向上します。

**ベスト プラクティス**: 単一の Azure AD インスタンスを確立します。 一貫性と単一の権威ソースにより、明確さが増し、ヒューマン エラーや構成の複雑さによるセキュリティ リスクが軽減されます。
**詳細**: 1 つの Azure AD ディレクトリを、企業や組織のアカウントに対する権威ソースとして指定します。

**ベスト プラクティス**: オンプレミスのディレクトリと Azure AD を統合します。  
**詳細**: [Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect) を使用して、オンプレミスのディレクトリとクラウドのディレクトリを同期します。

> [!Note]
> [Azure AD Connect のパフォーマンスに影響を与える要因](../../active-directory/hybrid/plan-connect-performance-factors.md)があります。 パフォーマンスが他より劣るシステムによってセキュリティや生産性が損なわれないように十分な容量が Azure AD Connect にあることを確認します。 大規模な組織や複雑な組織 (10 万を超えるオブジェクトをプロビジョニングする組織) では、[推奨事項](../../active-directory/hybrid/whatis-hybrid-identity.md)に従ってその Azure AD Connect の実装を最適化する必要があります。

**ベスト プラクティス**: 既存の Active Directory インスタンスで高い特権を持つ Azure AD にはアカウントを同期しないようにします。
**詳細**: このようなアカウントをフィルターで除外する既定の [Azure AD Connect の構成](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)を変更しないでください。 この構成により、敵対者がクラウドからオンプレミスの資産にピボットするリスクを軽減します (これは、大きなインシデントになる可能性があります)。

**ベスト プラクティス**: パスワード ハッシュ同期をオンにします。  
**詳細**: パスワード ハッシュ同期は、オンプレミスの Active Directory インスタンスからクラウドベースの Azure AD インスタンスにユーザー パスワード ハッシュを同期するときに使用する機能です。 この同期は、前の攻撃から漏洩した資格情報が再生されるのを保護するのに役立ちます。

Active Directory フェデレーション サービス (AD FS) または他の ID プロバイダーでフェデレーションを使用する場合でも、オンプレミスのサーバーがエラーになるか一時的に利用不可になった場合のバックアップとしてパスワード ハッシュ同期を設定することもできます。 この同期により、ユーザーは、オンプレミスの Active Directory インスタンスにサインインするときに使うものと同じパスワードを使用してサービスにサインインできます。 また、ユーザーが Azure AD に接続されていない他のサービスと同じ電子メール アドレスおよびパスワードを使用している場合に、Identity Protection では、同期されたパスワード ハッシュを侵害が検知されたパスワードと比較して、侵害された資格情報を検出できます。

詳細については、「[Azure AD Connect 同期を使用したパスワード ハッシュ同期の実装](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)」をご覧ください。

**ベスト プラクティス**: 新しいアプリケーションの開発では、Azure AD を認証に使用します。
**詳細**: 適切な機能を使用して認証をサポートします。

  - 従業員に対しては Azure AD
  - ゲスト ユーザーと外部のパートナーに対しては [Azure AD B2B](../../active-directory/b2b/index.yml)
  - 顧客がアプリケーションを使用するときにサインアップ、サインイン、プロファイル管理を行う方法を制御するには [Azure AD B2C](../../active-directory-b2c/index.yml)

オンプレミスの ID とクラウドの ID を統合していない組織では、アカウント管理の際により多くのオーバーヘッドが発生する可能性があります。 このオーバーヘッドによって、ミスやセキュリティ違反の可能性が高まります。

> [!Note]
> 重要なアカウントが存在するディレクトリ、および使用される管理者ワークステーションを新しいクラウド サービスまたは既存のプロセスのどちらで管理するかを、選択する必要があります。 既存の管理および ID プロビジョニング プロセスを使用すると、一部のリスクを軽減できますが、攻撃者がオンプレミスのアカウントを侵害してクラウドにピボットするリスクが発生する可能性もあります。 異なるロールには異なる戦略を使用する場合もあります (たとえば、IT 管理者と部署管理者など)。 2 つのオプションがあります。 1 番目のオプションは、オンプレミスの Active Directory インスタンスと同期されない Azure AD アカウントを作成することです。 管理ワークステーションを Azure AD に参加させると、Microsoft Intune を使用して管理や修正プログラムの適用を行うことができます。 2 番目のオプションは、オンプレミスの Active Directory インスタンスに同期することによって、既存の管理者アカウントを使用することです。 管理とセキュリティに Active Directory ドメインの既存のワークステーションを使用します。

## <a name="manage-connected-tenants"></a>接続済みテナントを管理する
セキュリティ組織では、リスクを評価し、組織のポリシーおよび規制の要件に従っているかどうかを判断するための、可視性が必要です。 セキュリティ組織が、運用環境とネットワークに ([Azure ExpressRoute](../../expressroute/expressroute-introduction.md) または[サイト間 VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) 介して) 接続されているすべてのサブスクリプションに対する可視化を備えていることを、確認する必要があります。 Azure AD の[グローバル管理者/社内管理者](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions)は、自分のアクセス権を[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)ロールに昇格させて、環境に接続されているすべてのサブスクリプションとマネージド グループを見ることができます。

自分および自分のセキュリティ グループが、環境に接続されたすべてのサブスクリプションまたは管理グループを表示できることを確認するには、「[Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](../../role-based-access-control/elevate-access-global-admin.md)」をご覧ください。 リスクの評価が済んだら、この昇格されたアクセス権を削除する必要があります。

## <a name="enable-single-sign-on"></a>シングル サインオンの有効化

モバイルを重視したクラウド中心の世界では、あらゆる場所からデバイス、アプリ、およびサービスへのシングル サインオン (SSO) を可能にして、時間や場所に関係なくユーザーの生産性を向上する必要があります。 管理対象の ID ソリューションが複数あると、IT 部門にとって管理上の問題となるだけでなく、複数のパスワードを覚えておく必要があるユーザーにとっても問題です。

すべてのアプリとリソースで同じ ID ソリューションを使用することにより、SSO が実現されます。 また、ユーザーは、必要なリソースがオンプレミスまたはクラウドのどちらにあっても、同じ資格情報セットを使用してリソースにサインインしてアクセスできます。

**ベスト プラクティス**: SSO を有効にします。  
**詳細**: Azure AD はクラウドに[オンプレミス Active Directory を拡張](/azure/active-directory/connect/active-directory-aadconnect)します。 ユーザーは、主要な職場または学校アカウントを、ドメイン参加済みデバイス、会社のリソース、および作業を完了させるために必要なすべての Web アプリケーションと SaaS アプリケーションに使用することができます。 ユーザーは複数のユーザー名とパスワードのセットを覚える必要がなくなり、組織のグループ メンバーシップや従業員としての地位に基づいて、ユーザーのアプリケーション アクセスが自動的にプロビジョニング (またはプロビジョニング解除) されるようにすることができます。 また、ギャラリー アプリ、または [Azure AD アプリケーション プロキシ](/azure/active-directory/active-directory-application-proxy-get-started)で開発および公開した独自のオンプレミス アプリについてそのアクセスを制御できます。

SSO を使用すると、ユーザーは Azure AD 内の職場または学校アカウントに基づいて [SaaS アプリケーション](/azure/active-directory/active-directory-appssoaccess-whatis)にアクセスできます。 これは、Microsoft SaaS アプリだけでなく、[Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) や [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial) などの他のアプリにも当てはまります。 [SAML ベースの ID](/azure/active-directory/fundamentals-identity) プロバイダーとして Azure AD を使用するように、アプリケーションを構成できます。 セキュリティ コントロールの目的で、Azure AD では、ユーザーに Azure AD を使用するアクセス権が付与されない限り、アプリケーションへのサインインを許可するトークンは発行されません。 ユーザーに対してアクセスを直接許可することも、ユーザーがメンバーであるグループを介して許可することもできます。

SSO を確立するためにユーザーやアプリケーションに共通の ID を作成しない組織では、ユーザーが複数のパスワードを所有するシナリオに陥ります。 このようなシナリオでは、ユーザーがパスワードを再利用したり、脆弱なパスワードを使用したりする可能性が高くなります。

## <a name="turn-on-conditional-access"></a>条件付きアクセスをオンにする

ユーザーはさまざまなデバイスやアプリを使用してどこからでも組織のリソースにアクセスできます。 IT 管理者は、これらのデバイスがセキュリティとコンプライアンスの標準を満たしているかどうかを確認する必要があります。 だれがリソースにアクセスできるかに重点を置くだけでは十分ではなくなっています。

セキュリティと生産性のバランスを取るためには、アクセスの制御に関する決定を行う前に、リソースへのアクセス方法を考慮する必要があります。 Azure AD の条件付きアクセスで、この要件に対処することができます。 条件付きアクセスを使用すると、クラウド アプリへのアクセスに関する条件に基づいて、アクセス制御の決定を自動的に行うことができます。

**ベスト プラクティス**: 会社のリソースへのアクセスを管理および制御します。  
**詳細**: グループ、場所、アプリケーションの機密性に基づいて SaaS アプリや Azure AD 接続アプリの Azure AD [条件付きアクセス](/azure/active-directory/active-directory-conditional-access-azure-portal)を構成します。

**ベスト プラクティス**: レガシ認証プロトコルをブロックします。
**詳細**: 攻撃者は、毎日古いプロトコルの弱点を悪用しています (特にパスワード スプレー攻撃)。 条件付きアクセスを構成して、レガシ プロトコルをブロックします。 ビデオ「[Azure AD: Do’s and Don’ts (べしとべからず)](https://www.youtube.com/watch?v=wGk0J4z90GI)」で詳細をご覧ください。

## <a name="enable-password-management"></a>パスワード管理を有効にする

複数のテナントがある場合、またはユーザーが[自分のパスワードをリセット](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)できるようにする場合は、適切なセキュリティ ポリシーを使用して不適切な使用を防止することが重要です。

**ベスト プラクティス**: ユーザーに対してパスワード リセットのセルフサービス (SSPR) を設定します。  
**詳細**: Azure AD の[パスワード リセットのセルフサービス](/azure/active-directory-b2c/active-directory-b2c-reference-sspr)機能を使用します。

**ベスト プラクティス**: SSPR が実際に使用されているかどうか、またはその使用方法を監視します。  
**詳細**: Azure AD の[パスワード リセット登録アクティビティ レポート](/azure/active-directory/active-directory-passwords-get-insights)を使用して、登録しているユーザーを監視します。 Azure AD で提供されるレポート機能によって、質問に対する答えをあらかじめ用意されたレポートから得ることができます。 適切にライセンスを付与されている場合は、カスタム クエリを作成することもできます。

**ベスト プラクティス**: オンプレミスのインフラストラクチャにクラウドベースのパスワード ポリシーを拡張します。
**詳細**: クラウドベースのパスワード変更と同じチェックを、オンプレミスのパスワード変更に対しても実行することにより、組織内のパスワード ポリシーを強化します。 オンプレミスの Windows Server Active Directory エージェント用に [Azure AD パスワード保護](/azure/active-directory/authentication/concept-password-ban-bad)をインストールして、禁止パスワード リストを既存のインフラストラクチャに拡張します。 オンプレミスのパスワードを変更、設定、またはリセットするユーザーと管理者は、クラウドのみのユーザーと同じパスワード ポリシーに準拠することが必須になります。

## <a name="enforce-multi-factor-verification-for-users"></a>ユーザーに多要素認証を適用する

すべてのユーザーに対して 2 段階認証を要求することをお勧めします。 これには、組織内の管理者およびアカウントが侵害された場合に重大な影響を及ぼす可能性のあるその他のユーザー (財務担当者など) が含まれます。

2 段階認証を要求するオプションは複数あります。 最適なオプションは、目的、実行している Azure AD エディション、およびライセンス プログラムによって異なります。 最適なオプションを判断するには、「[ユーザーに 2 段階認証を要求する方法](/azure/active-directory/authentication/howto-mfa-userstates)」を参照してください。 ライセンスと価格の詳細については、[Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) と [Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) の価格に関するページを参照してください。

2 段階認証を有効にするオプションと利点を次に示します。

**オプション 1**:[ユーザーの状態を変更することで Multi-Factor Authentication を有効にします](../../active-directory/authentication/howto-mfa-userstates.md)。   
**利点**:2 段階認証を要求するための従来の方法です。 これは、[クラウド内の Azure Multi-Factor Authentication と Azure Multi-Factor Authentication Server](/azure/active-directory/authentication/concept-mfa-whichversion) の両方に対応します。 この方法を使用すると、ユーザーはサインインするたびに 2 段階認証を実行するよう求められ、条件付きアクセス ポリシーがオーバーライドされます。

Multi-factor Authentication を有効にする必要がある場合を判断するには、「[所属する組織に適しているのはどちらのバージョンの Azure MFA であるかを確認しましょう](/azure/active-directory/authentication/concept-mfa-whichversion)」をご覧ください。

**オプション 2**:[条件付きアクセス ポリシーを使用して Multi-Factor Authentication を有効にします](/azure/active-directory/authentication/howto-mfa-getstarted)。
**利点**:このオプションでは、[条件付きアクセス](/azure/active-directory/active-directory-conditional-access-azure-portal)を使用して特定の条件下で 2 段階認証を要求できます。 特定の条件としては、異なる場所、信頼されていないデバイス、または危険と見なされるアプリケーションからのユーザーのサインインを指定できます。 2 段階認証を要求する特定の条件を定義すると、要求のメッセージがユーザーに繰り返し表示されないようにすることができます。このようなメッセージは、不快なユーザー エクスペリエンスとなり得ます。

これは、ユーザーの 2 段階認証を有効にするうえで最も柔軟性の高い手段です。 条件付きアクセス ポリシーを有効にする方法は、クラウド内の Azure Multi-Factor Authentication に対してのみ機能します。これは Azure AD の Premium 機能です。 この方法の詳細については、「[クラウドベースの Azure Multi-Factor Authentication をデプロイする](/azure/active-directory/authentication/howto-mfa-getstarted)」を参照してください。

**オプション 3**:[Azure AD Identity Protection](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa) のユーザーおよびサインインのリスクを評価し、条件付きアクセス ポリシーを使用して Multi-Factor Authentication を有効にします。   
**利点**:このオプションの利点は次のとおりです。

- 組織の ID に影響する潜在的な脆弱性を検出します。
- 組織の ID に関連する検出された疑わしいアクションに対する自動応答を構成します。
- 疑わしいインシデントを調査し、適切なアクションを実行して解決します。

この方法では、Azure AD Identity Protection のリスク評価を使用して、すべてのクラウド アプリケーションのユーザーおよびサインインのリスクに基づいて 2 段階認証が要求されるかどうかを判断します。 この方法では、Azure Active Directory P2 ライセンスが必要です。 この方法の詳細については、「[Azure Active Directory Identity Protection](/azure/active-directory/identity-protection/overview)」を参照してください。

> [!Note]
> オプション 1 (ユーザーの状態を変更することで Multi-Factor Authentication を有効にする) は、条件付きアクセス ポリシーをオーバーライドします。 オプション 2 および 3 では条件付きアクセス ポリシーを使用するため、オプション 1 をそれらと共に使用することはできません。

2 段階認証などの新しい ID 保護レイヤーを追加しない組織は、資格情報盗用攻撃を受けやすくなります。 資格情報盗用攻撃はデータの侵害につながる可能性があります。

## <a name="use-role-based-access-control"></a>ロールベースのアクセス制御を使用する
クラウド リソースに対するアクセスの管理は、クラウドを使用しているすべての組織にとって重要なことです。 [ロールベースのアクセス制御 (RBAC)](/azure/role-based-access-control/overview) は、Azure のリソースにアクセスできるユーザー、そのユーザーがそれらのリソースに対して実行できること、そのユーザーがアクセスできる領域を管理するのに役立ちます。

Azure での特定の機能に対して責任を負うグループまたは個人のロールを指定すると、セキュリティ リスクをもたらすヒューマン エラーやオートメーション エラーにつながる可能性のある混乱を避けるのに役立ちます。 データ アクセスにセキュリティ ポリシーを適用する組織では、[必知事項](https://en.wikipedia.org/wiki/Need_to_know)と[最小権限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)のセキュリティ原則に基づいてアクセスを制限することが不可欠です。

セキュリティ チームは、リスクを評価して修復するため、Azure リソースに対する可視性を必要とします。 セキュリティ チームに運用責任がある場合、それらのジョブを実行するために追加のアクセス許可が必要です。

[RBAC](/azure/role-based-access-control/overview) を使用して、特定のスコープ内のユーザー、グループ、アプリケーションにアクセス許可を割り当てることができます。 ロール割り当てのスコープには、サブスクリプション、リソース グループ、または単独のリソースを指定できます。

**ベスト プラクティス**: チーム内で職務を分離し、職務を実行するために必要なアクセスのみをユーザーに許可します。 すべてのユーザーに Azure サブスクリプションまたはリソースで無制限のアクセス許可を付与するのではなく、特定のスコープで特定の操作のみを許可します。
**詳細**: Azure の[組み込み RBAC ロール](/azure/role-based-access-control/built-in-roles)を使用して、ユーザーに権限を割り当てます。

> [!Note]
> 個別のアクセス許可では、不要な複雑さと混乱が発生し、それが積み重なって、何かを壊してしまう心配なしでは修正するのが難しい "レガシ" 構成になります。 リソース固有のアクセス許可は使わないようにします。 代わりに、エンタープライズ全体のアクセス許可には管理グループを使用し、サブスクリプション内のアクセス許可にはリソース グループを使用します。 ユーザー固有のアクセス許可は使わないようにします。 代わりに、Azure AD でグループにアクセスを割り当てます。

**ベスト プラクティス**: セキュリティ チームには Azure のリソースを把握するための Azure の責任のアクセス権を付与し、リスクを評価して修復できるようにします。
**詳細**: セキュリティ チームには、RBAC の[セキュリティ閲覧者](/azure/role-based-access-control/built-in-roles#security-reader)ロールを付与します。 責任の範囲に応じて、ルート管理グループまたはセグメント管理グループを使用できます。

- すべてのエンタープライズ リソースを担当するチームに対しては**ルート管理グループ**
- スコープが限られたチームには**セグメント管理グループ** (一般に、規制または他の組織的な境界のため)

**ベスト プラクティス**: 直接的な運用責任を持つセキュリティ チームには、適切なアクセス許可を付与します。
**詳細**: RBAC の組み込みロールで、適切なロールの割り当てを確認します。 組み込みロールが組織の特定のニーズを満たさない場合は、[Azure リソースに対するカスタム ロール](/azure/role-based-access-control/custom-roles)を作成することができます。 組み込みロールと同様、カスタム ロールは、ユーザー、グループ、サービス プリンシパルに対して、サブスクリプション、リソース グループ、リソースのスコープで割り当てることができます。

**ベスト プラクティス**:Azure Security Center へのアクセス権を、それを必要とするセキュリティ ロールに付与します。 Security Center では、セキュリティ チームはすばやくリスクを特定して修復できます。
**詳細**: これらのニーズを持つセキュリティ チームを RBAC [セキュリティ管理者](/azure/role-based-access-control/built-in-roles#security-admin)に追加し、セキュリティ ポリシーを表示したり、セキュリティ状態を表示したり、セキュリティ ポリシーを編集したり、アラートと推奨事項を表示したり、アラートと推奨事項を無視したりできるようにします。 責任の範囲に応じて、ルート管理グループまたはセグメント管理グループを使用して、これを行うことができます。

RBAC などの機能を使用したデータ アクセス制御を適用しない組織では、ユーザーに必要以上の権限が付与される可能性があります。 これにより、ユーザーがアクセスする必要のない種類のデータ (ビジネスへの影響が高いものなど) にアクセスできるようになり、データのセキュリティ侵害につながる恐れがあります。

## <a name="lower-exposure-of-privileged-accounts"></a>特権アカウントの公開時間を短縮する

特権アクセスのセキュリティ保護は、ビジネス資産を保護するうえで重要な最初のステップです。 セキュリティで保護された情報またはリソースへのアクセス権を持つユーザーの数を最小限に抑えることで、悪意のあるユーザーがこのようなアクセス権を手にしたり、権限のあるユーザーの不注意で機微なリソースのセキュリティが損なわれたりする可能性が抑えられます。

特権アカウントとは、IT システムを管理するアカウントです。 サイバー攻撃では、組織のデータやシステムへのアクセス手段を得るために、このようなアカウントが標的にされます。 特権アクセスを保護するには、悪意のあるユーザーにさらされる危険からアカウントとシステムを分離する必要があります。

サイバー攻撃者から特権アクセスを保護するためのロードマップを作成して従うことをお勧めします。 Azure AD、Microsoft Azure、Office 365、およびその他のクラウド サービスで管理または報告される ID とアクセスを保護するための詳細なロードマップの作成については、「[Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)」を参照してください。

「[Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)」に記載されているベスト プラクティスを以下にまとめています。

**ベスト プラクティス**: 特権アカウントへのアクセスを管理、制御、および監視します。   
**詳細**: [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) を有効にします。 Privileged Identity Management を有効にすると、特権アクセス ロールが変更されたという電子メール通知を受け取ります。 これらの通知は、ディレクトリ内の高度な特権ロールに他のユーザーが追加された場合に早期警告を提供します。

**ベスト プラクティス**: すべての重要な管理者アカウントが、マネージド Azure AD アカウントであるようにします。
**詳細**: 重要な管理者ロールから、すべてのコンシューマー アカウントを削除します (hotmail.com、live.com、outlook.com といった Microsoft アカウントなど)。

**ベスト プラクティス**: 管理者特権を侵害するフィッシングや他の攻撃を回避するため、すべての重要な管理者ロールが管理タスク用のアカウントを持つようにします。
**詳細**: 管理タスクの実行に必要な特権が割り当てられている管理者アカウントを別に作成します。 Microsoft Office 365 のメールや任意の Web 閲覧などの日常の生産性向上ツールに対しては、これらの管理者アカウントを使用できないようにします。

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

グローバル管理者ロールが割り当てられているか、その対象であるアカウントを評価します。 `*.onmicrosoft.com` ドメイン (緊急アクセスが目的) を使用してクラウド専用アカウントが見当たらない場合は、それらを作成します。 詳しくは、「[Azure AD で緊急アクセス用管理者アカウントを管理する](/azure/active-directory/users-groups-roles/directory-emergency-access)」をご覧ください。

**ベスト プラクティス**: 緊急事態が発生した場合の "非常時" プロセスを用意します。
**詳細**: 「[Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)」の手順に従います。

**ベスト プラクティス**: すべての重要な管理者アカウントには、パスワードレス (推奨) または Multi-Factor Authentication を要求します。
**詳細**: [Microsoft Authenticator アプリ](/azure/active-directory/authentication/howto-authentication-phone-sign-in)を使用して、パスワードを使用せずに Azure AD アカウントにサインインします。 [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) のように、Microsoft Authenticator は、キーベースの認証を使用して、デバイスに関連付けられていて生体認証または PIN を使用するユーザー資格情報を有効にします。

次のような Azure AD 管理者ロールを永続的に割り当てられているすべての個人ユーザーには、サインイン時に Azure Multi-factor Authentication を要求します: グローバル管理者、特権ロール管理者、Exchange Online 管理者、SharePoint Online 管理者。 [管理者アカウントの Multi-Factor Authentication](/azure/active-directory/authentication/howto-mfa-userstates) を有効にし、管理者アカウントのユーザーが登録していることを確認します。

**ベスト プラクティス**: 重要な管理者アカウントには、運用タスク (たとえば、閲覧やメール) を使用できない管理ワークステーションを用意します。 これにより、閲覧やメールを使用する攻撃ベクトルから管理者アカウントが保護され、主要なインシデントのリスクが大幅に低下します。
**詳細**: 管理ワークステーションを使用します。 ワークステーションのセキュリティのレベルを選択します。

- 安全性の高い生産性デバイスでは、閲覧や他の生産性タスクに対する高度なセキュリティが提供されます。
- [Privileged Access Workstation (PAW)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) には、機密性の高いタスクに専用のオペレーティング システムが用意されており、インターネット上の攻撃や脅威ベクトルから保護されます。

**ベスト プラクティス**: 従業員が組織を離れるときは、管理者アカウントをプロビジョニング解除します。
**詳細**: 従業員が組織を離れるときに管理者アカウントを無効化または削除するプロセスを設けます。

**ベスト プラクティス**: 最新の攻撃手法を使用して、管理者アカウントを定期的にテストします。
**詳細**: Office 365 攻撃シミュレーターやサードパーティのオファリングを使用して、組織で現実的な攻撃のシナリオを実行します。 これは、実際の攻撃が発生する前に脆弱性のあるユーザーを発見するのに役立ちます。

**ベスト プラクティス**: 最も頻繁に使用される攻撃手法を軽減するための対策を講じます。  
**詳細**: [職場または学校アカウントに切り替える必要がある管理者ロールの Microsoft アカウントを特定する](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[グローバル管理者アカウントのユーザー アカウントとメール転送を分離する](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[管理者アカウントのパスワードが最近変更されたことを確認する](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[パスワード ハッシュ同期をオンにする](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[すべての特権ロールに属するユーザーおよび露出しているユーザーに多要素認証を要求する](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Office 365 セキュリティ スコアを取得する (Office 365 を使用している場合)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Office 365 セキュリティおよびコンプライアンス ガイダンスを確認する (Office 365 を使用している場合)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Office 365 アクティビティの監視を構成する (Office 365 を使用している場合)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[インシデント/緊急時対応計画の所有者を設定する](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[オンプレミスの特権管理者アカウントをセキュリティで保護する](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

特権アクセスをセキュリティで保護しない場合は、高度な特権ロールに属するユーザーが多すぎて攻撃を受けやすくなっている可能性があります。 多くの場合、サイバー攻撃者を含む悪意のあるアクターは、管理者アカウントやその他の特権アクセスの要素をターゲットとして、資格情報盗用を使用して機密データやシステムへのアクセスを取得します。

## <a name="control-locations-where-resources-are-created"></a>リソースが作成される場所を制御する

クラウド事業者がタスクを実行できるようにする一方で、組織のリソースの管理に必要な規則に違反しないようにすることが、非常に重要です。 リソースが作成される場所を制御する必要のある組織では、これらの場所をハード コードする必要があります。

[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) を使用すると、明示的に拒否されるアクションまたはリソースが記述されている定義を含むセキュリティ ポリシーを作成できます。 サブスクリプション、リソース グループ、個別リソースなど、任意の範囲でポリシー定義を割り当てます。

> [!NOTE]
> セキュリティ ポリシーは RBAC とは異なります。 実際には、RBAC を使用して、ユーザーがこれらのリソースを作成することを許可します。
>
>

リソースの作成方法を制御しないと、ユーザーは必要量より多くのリソースを作成することによってサービスを不正使用する可能性が高くなります。 リソースの作成プロセスを強化することは、マルチテナントのシナリオをセキュリティ保護するための重要な手順です。

## <a name="actively-monitor-for-suspicious-activities"></a>疑わしいアクティビティを能動的に監視する

アクティブな ID 監視システムでは、疑わしい動作をすばやく検出して、さらなる調査を行うためのアラートをトリガーします。 組織による ID の監視に役立つ 2 つの Azure AD 機能を次に示します。

**ベスト プラクティス**: 次の動作を識別するための方法を用意します。

- [追跡されない](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources)サインインの試行
- 特定のアカウントに対する[ブルート フォース](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures)攻撃
- 複数の場所からのサインインの試行
- [感染しているデバイスからのサインイン](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices)
- 疑わしい IP アドレス

**詳細**: Azure AD Premium の[異常レポート](/azure/active-directory/active-directory-view-access-usage-reports)を使用します。 IT 管理者がこれらのレポートを毎日または必要に応じて (通常はインシデント対応シナリオ) 実行するためのプロセスと手順を設けます。

**ベスト プラクティス**: リスクを通知し、ビジネス要件に合わせてリスク レベル (高、中、低) を調整できるアクティブな監視システムを用意します。   
**詳細**: [Azure AD Identity Protection](/azure/active-directory/active-directory-identityprotection) を使用します。この製品では、独自のダッシュボードで現在のリスクにフラグを設定し、概要の通知を毎日電子メールで送信します。 指定したリスク レベルに達したときに、検出された問題が自動的に対処されるようにリスク ベースのポリシーを構成することで、組織の ID を保護できます。

ID システムを能動的に監視しないと、ユーザーの資格情報が侵害されるリスクがあります。 侵害された資格情報を用いた疑わしい活動が行われていることを把握しないと、この種の脅威を緩和することはできません。

## <a name="use-azure-ad-for-storage-authentication"></a>ストレージの認証に Azure AD を使用する
[Azure Storage](/azure/storage/common/storage-auth-aad) は、Blob Storage や Queue storage に対する Azure AD での認証と承認をサポートします。 Azure AD 認証では、Azure のロールベースのアクセス制御を使用して、個々の BLOB コンテナーやキューに対する特定のアクセス許可を、ユーザー、グループ、アプリケーションに付与できます。

[ストレージへのアクセスを認証するには Azure AD](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/) を使用することをお勧めします。

## <a name="next-step"></a>次のステップ

Azure を使用してクラウド ソリューションを設計、デプロイ、管理するときに使用するセキュリティのベスト プラクティスの詳細については、「[Azure セキュリティのベスト プラクティスとパターン](best-practices-and-patterns.md)」を参照してください。
