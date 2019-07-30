---
title: 管理アクセスをセキュリティで保護するためのベスト プラクティス - Azure Active Directory | Microsoft Docs
description: 組織の管理アクセスと管理者アカウントがセキュリティで保護されるようにします。 システム設計者および Azure AD、Azure、Microsoft Online Services を構成する IT 担当者向けです。
services: active-directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.date: 03/18/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e61207ea23b9b9560668b58b33e3f32f5f70ab2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083936"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する

現代の組織内のほとんどまたはすべてのビジネス資産のセキュリティは、IT システムを管理する特権アカウントの整合性に依存します。 多くの場合、サイバー攻撃者を含む悪意のあるアクターは、管理者アカウントやその他の特権アクセスの要素をターゲットとして、資格情報盗用攻撃を使用して機密データやシステムにすばやくアクセスしようとします。 クラウド サービスの場合、防止と対応は、クラウド サービス プロバイダーと顧客の共同責任です。 エンドポイントとクラウドに対する最新の脅威について詳しくは、[Microsoft セキュリティ インテリジェンス レポート](https://www.microsoft.com/security/operations/security-intelligence-report)のページをご覧ください。 この記事は、現在のプランとここで説明しているガイダンス間のギャップを埋めるためのロードマップの作成に役立ちます。

> [!NOTE]
> Microsoft は、最高レベルの信頼、透過性、標準への準拠、規制コンプライアンスに努めています。 Microsoft のグローバルのインシデント対応チームがクラウド サービスへの攻撃の影響をどのように軽減するか、また Microsoft のビジネス製品とクラウド サービスにセキュリティがどのように組み込まれているかについて詳しくは、「[Microsoft Trust Center - セキュリティ](https://www.microsoft.com/trustcenter/security)」をご覧ください。Microsoft コンプライアンス ターゲットについては、「[Microsoft Trust Center - コンプライアンス](https://www.microsoft.com/trustcenter/compliance)」をご覧ください。

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
ほとんどの組織では、ビジネス資産のセキュリティは、IT システムを管理する特権アカウントの整合性に依存します。 サイバー攻撃者は、組織の機密データにアクセスするために、インフラストラクチャ システム (Active Directory や Azure Active Directory) への特権アクセスに注目します。 

インターネット上での SaaS アプリや個人用デバイスの使用が増加しているため、プライマリ セキュリティの境界としてネットワークの入口と出口の保護に焦点を当てた従来のアプローチは効果が低くなっています。 複雑な現代の企業内のネットワーク セキュリティ境界を自然に置き換えるものは、組織の ID 層での認証と認可のコントロールです。

特権管理者アカウントは、この新しい "セキュリティ境界" のコントロールに効果的です。 環境がオンプレミス、クラウド、またはオンプレミスとクラウド ホステッド サービスのハイブリッドのいずれであるかに関係なく、特権アクセスを保護することが重要です。 断固たる敵対者から管理アクセスを保護するには、組織のシステムをリスクから分離する完全かつ熟慮されたアプローチをとる必要があります。 

特権アクセスをセキュリティで保護するには、以下に対する変更が必要です

* プロセス、管理作業、ナレッジ管理
* ホスト防御、アカウントの保護、ID 管理などの技術的なコンポーネント

このドキュメントでは主に、Azure AD、Microsoft Azure、Office 365、その他のクラウド サービスで管理またはレポートされる ID およびアクセスをセキュリティで保護するためのロードマップの作成に焦点を当てています。 オンプレミスの管理者アカウントがある組織の場合は、「[Securing Privileged Access (特権アクセスのセキュリティ保護)](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)」で、Active Directory から管理されるオンプレミスおよびハイブリッドの特権アクセスに関するガイダンスをご覧ください。 

> [!NOTE] 
> この記事のガイダンスでは、Azure Active Directory Premium プラン P1 と P2 に含まれている Azure Active Directory の主な機能を参照します。 Azure Active Directory Premium P2 は、EMS E5 スイートおよび Microsoft 365 E5 スイートに含まれています。 このガイダンスでは、組織がユーザー用に Azure AD Premium P2 ライセンスを既に購入していることを想定しています。 これらのライセンスがない場合、ガイダンスの一部は組織に適用されないことがあります。 また、この記事全体で、グローバル管理者 (または全体管理者) という用語は "会社の管理者" または "テナント管理者" と同義です。

## <a name="develop-a-roadmap"></a>ロードマップの作成 

マイクロソフトでは、サイバー攻撃者から特権アクセスを保護するためのロードマップを作成して従うことをお勧めします。 既存の機能と組織内の特定の要件に合わせてロードマップをいつでも調整できます。 ロードマップの各ステージで、敵対者がオンプレミス、クラウド、ハイブリッドの資産の特権アクセスを攻撃するコストと難易度を上げます。 次の 4 つのロードマップ ステージをお勧めします。この推奨ロードマップは、サイバー攻撃のインシデントと対応の実装に関する Microsoft の経験に基づいて、最も効果的で迅速な実装をスケジュールします。 このロードマップのタイムラインは、おおよそのものです。

![ロードマップのステージとタイム ライン](./media/directory-admin-roles-secure/roadmap-timeline.png)

* ステージ 1 (24-48 時間): すぐに実施することをお勧めする重要な項目

* ステージ 2 (2-4 週間): 最もよく使用される攻撃手法の緩和

* ステージ 3 (1-3 か月): 可視性の構築と管理者アクティビティのフル コントロールの構築

* ステージ 4 (6 か月以降): セキュリティ プラットフォームをさらに強化するための防御の継続的な構築

このロードマップ フレームワークは、既にデプロイしている Microsoft テクノロジの使用を最大化するように設計されています。 現在および今後の主要なセキュリティ テクノロジを活用したり、既に展開しているか、展開を検討している他のベンダーのセキュリティ ツールを統合したりすることもできます。 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>ステージ 1: すぐに実施することをお勧めする重要な項目

![ステージ 1 すぐに実施する重要な項目](./media/directory-admin-roles-secure/stage-one.png)

ロードマップのステージ 1 では、迅速かつ簡単に実装できる重要なタスクに重点を置きます。 最初の 24 ～ 48 時間以内にこれらの少数の項目をすぐに実施して、セキュリティで保護された特権アクセスの基本的なレベルを確保することをお勧めします。 セキュリティで保護された特権アクセスのロードマップのこのステージには、次のアクションが含まれます。

### <a name="general-preparation"></a>一般的な準備

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management を有効にする

Azure AD Privileged Identity Management (PIM) をまだ有効にしていない場合は、運用環境のテナントで有効にします。 Privileged Identity Management を有効にすると、特権アクセス ロールが変更されたという電子メール通知を受け取ります。 これらの通知は、ディレクトリ内の高度な特権ロールに他のユーザーが追加された場合に早期警告を提供します。

Azure AD Privileged Identity Management は、Azure AD Premium P2 または EMS E5 に含まれています。 これらのソリューションでは、オンプレミスの環境のアプリケーションとリソースやクラウドへのアクセスを保護できます。 Azure AD Premium P2 または EMS E5 がまだなく、このロードマップで参照されている機能をさらに評価したい場合は、[Enterprise Mobility + Security の 90 日間無料試用版](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)にサインアップしてください。 これらのライセンスの試用版を使用して、Azure AD Privileged Identity Management と Azure AD Identity Protection を試し、Azure AD の高度なセキュリティ レポート、監査、アラートを使用してアクティビティを監視してください。

Azure AD Privileged Identity Management を有効にした後で:

1. 運用環境のテナントのグローバル管理者であるアカウントで [Azure Portal](https://portal.azure.com/) にサインインします。

2. Privileged Identity Management を使用するテナントを選択するには、Azure Portal の右上隅でユーザー名を選択します。

3. **[すべてのサービス]** を選択し、**Azure AD Privileged Identity Management** の一覧をフィルターします。

4. **[すべてのサービス]** の一覧から Privileged Identity Management を開き、ダッシュ ボードにピン留めします。

テナント内で Azure AD Privileged Identity Management を使用する最初のユーザーには、そのテナントの **[セキュリティ管理者]** ロールと **[特権ロール管理者]** ロールが自動的に割り当てられます。 ユーザーの Azure AD ディレクトリ ロールの割り当てを管理できるのは特権ロール管理者だけです。 また、Azure AD Privileged Identity Management を追加すると、初期の検出と割り当てを体験できるセキュリティ ウィザードが表示されます。 この時点でさらに変更を加えずに、ウィザードを終了することができます。 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>高度な特権ロールに属するアカウントを識別および分類する 

Azure AD Privileged Identity Management を有効にした後、グローバル管理者、特権ロール管理者、Exchange Online 管理者、SharePoint Online 管理者のディレクトリ ロールに属するユーザーを表示します。 テナントに Azure AD PIM がない場合は、[PowerShell API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0) を使用できます。 グローバル管理者ロールは汎用的であるため、このロールから開始します。Microsoft 365 管理センターと Azure portal のどちらで割り当てられたか、あるいは Microsoft PowerShell 用 Azure AD モジュールを使用して割り当てられたかに関係なく、この管理者ロールが割り当てられたユーザーは、組織がサブスクライブしているすべてのクラウド サービスで同じ権限を持つことになります。 

このようなロールの不要になったアカウントがあれば削除します。 次に、管理者ロールに割り当てられている残りのアカウントを分類します。

* 管理ユーザーに個別に割り当てられ、管理以外の目的 (たとえば、個人用電子メール) にも使用できる
* 管理ユーザーに個別に割り当てられ、管理目的にのみ指定されている
* 複数のユーザー間で共有される
* 非常時の緊急アクセス シナリオ用
* 自動スクリプト用
* 外部ユーザー用

#### <a name="define-at-least-two-emergency-access-accounts"></a>少なくとも 2 つの緊急アクセス用アカウントを定義する 

管理者として既存の個別ユーザーのアカウントへのサインインやアクティブ化ができないために Azure AD テナントの管理から誤ってロックアウトされる可能性がある状況になっていないことを確認します。 たとえば、組織がオンプレミスの ID プロバイダーにフェデレーションされており、その ID プロバイダーが利用不可であるためユーザーがオンプレミスにサインインできない場合などです。 複数の緊急アクセス用アカウントをテナントに保持すると、誤って管理アクセスが不可能になることによる影響を軽減できます。

緊急アクセス用アカウントは、組織において既存の Azure Active Directory 環境内での特権アクセスを制限するのに役立ちます。 このようなアカウントは高い特権を持っており、特定のユーザーには割り当てられません。 緊急アクセス用アカウントは、通常の管理者アカウントを使うことができない '緊急事態' に制限されます。 組織は、緊急用アカウントの使用をコントロールおよび低減し、必要なときにだけ使うという目的を守る必要があります。

グローバル管理者ロールが割り当てられているか、その対象であるアカウントを評価します。 *.onmicrosoft.com ドメイン ("非常時" の緊急アクセスが目的) を使用しているクラウド専用アカウントが見当たらない場合は、それらを作成します。 詳しくは、「[Azure AD で緊急アクセス用管理者アカウントを管理する](directory-emergency-access.md)」をご覧ください。

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>多要素認証を有効にし、その他のすべての高度な特権を持つシングル ユーザー非フェデレーション管理者アカウントを登録する

1 つまたは複数の Azure AD 管理者ロール (グローバル管理者、特権ロール管理者、Exchange Online 管理者、SharePoint Online 管理者) に永続的に割り当てられているすべての個々のユーザーのサインイン時に Azure Multi-factor Authentication (MFA) が必要です。 ガイドを使用して[管理者アカウントの Multi-Factor Authentication (MFA)](../authentication/howto-mfa-userstates.md) を有効にし、[https://aka.ms/mfasetup](https://aka.ms/mfasetup) でそれらすべてのユーザーが登録されていることを確認しします。 詳しくは、「[Office 365 でデータやサービスへのアクセスを保護する](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)」の手順 2. と手順 3. をご覧ください。 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>ステージ 2: 最もよく使用される攻撃手法の緩和

![ステージ 2 よく使用される攻撃の緩和](./media/directory-admin-roles-secure/stage-two.png)

ロードマップのステージ 2 は、資格情報の盗用や誤用に最もよく使われる攻撃手法の緩和に重点を置き、約 2 ～ 4 週間で実装することができます。 セキュリティで保護された特権アクセスのロードマップのこのステージには、次のアクションが含まれます。

### <a name="general-preparation"></a>一般的な準備

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>サービス、所有者、管理者のインベントリを実施する

個人所有機器の持ち込み (BYOD) と自宅からの作業のポリシーの増加や、ビジネスにおけるワイヤレス接続の拡大に伴い、ネットワークに接続するユーザーを監視することが重要です。 多くの場合、効果的なセキュリティ監査は、IT でサポートされていないため安全ではない可能性のあるネットワーク上で実行されているデバイス、アプリケーション、プログラムを明らかにします。 詳しくは、「[Azure セキュリティの管理と監視の概要](../../security/security-management-and-monitoring-overview.md)」をご覧ください。 インベントリ プロセスには、次のすべてのタスクを含めてください。 

* 管理者ロールを持つユーザーと、それらのユーザーが管理できるサービスを識別します。
* Azure AD PIM を使用して、ステージ 1 に示されているロール以外の追加ロールなど、Azure AD への管理者アクセス権を持つ組織内のユーザーを確認します。
* Azure AD で定義されているロール以外に、Office 365 には、組織内のユーザーに割り当てることができる管理者ロールのセットが付属します。 各管理者ロールは、共通のビジネス機能にマップされ、[Microsoft 365 管理センター](https://admin.microsoft.com)で特定のタスクを行うためのアクセス許可を組織のユーザーに付与します。 Microsoft 365 管理センターを使用して、Azure AD で管理されていないロール経由を含め、Office 365 への管理者アクセス権を持つ組織内のユーザーを確認します。 詳しくは、「[Office 365 の管理者ロールについて」](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」と「[Office 365 のセキュリティのベスト プラクティス](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3)」をご覧ください。
* Azure、Intune、Dynamics 365 など、組織が利用している他のサービスでインベントリを実行します。
* 管理者アカウント (ユーザーの日常的なアカウントだけでなく管理目的でも使用されるアカウント) に、仕事用メール アドレスが割り当てられ、Azure MFA に登録されているか MFA をオンプレミスで使用していることを確認します。
* ユーザーに管理アクセス権を使用するビジネス上の正当な理由を尋ねます。
* 管理者アクセス権を必要としない個人ユーザーとサービスの管理者アクセス権を取り消します。

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>職場または学校アカウントに切り替える必要がある管理者ロールの Microsoft アカウントを特定する

組織の最初のグローバル管理者は、Azure AD の使用を開始するときに既存の Microsoft アカウント資格情報を再利用することがあります。 これらの Microsoft アカウントは、個々のクラウドベースのアカウントまたは同期アカウントで置き換える必要があります。 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>グローバル管理者アカウントのユーザー アカウントとメール転送を分離する 

個人用電子メール アカウントはサイバー攻撃者によって定期的にフィッシングされるため、グローバル管理者アカウントでは個人用電子メール アドレスを使用しないでください。 インターネット上のリスク (フィッシング攻撃、意図しない Web サイトの参照) を管理者特権から分離するために、管理者特権を持つユーザーごとに専用のアカウントを作成します。 

まだ行っていない場合は、ユーザーがグローバル管理者のタスクを実行するための別のアカウントを作成して、誤って電子メールを開いたり、管理者アカウントに関連付けられているプログラムを実行したりしないようにします。 これらのアカウントが電子メールを作業用のメールボックスに転送することを確認します。  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>管理者アカウントのパスワードが最近変更されたことを確認する

すべてのユーザーが過去 90 日間に少なくとも 1 回管理者アカウントにサインインし、パスワードを変更したことを確認します。 また、複数のユーザーがパスワードを知っている共有アカウントのパスワードが最近変更されたことを確認します。

#### <a name="turn-on-password-hash-synchronization"></a>パスワード ハッシュ同期をオンにする

パスワード ハッシュ同期は、オンプレミスの Active Directory インスタンスからクラウドベースの Azure Active Directory インスタンスにユーザー パスワード ハッシュのハッシュを同期するときに使用する機能です。 Active Directory フェデレーション サービス (AD FS) または他の ID プロバイダーでフェデレーションを使用する場合でも、AD サーバーや ADFS サーバーなどのオンプレミスのインフラストラクチャがエラーになるか一時的に利用不可になった場合のバックアップとしてパスワード ハッシュ同期を設定することもできます。 これにより、ユーザーは、オンプレミスの Active Directory インスタンスにサインインするときに使うものと同じパスワードを使用してサービスにサインインできます。 また、ユーザーが Azure AD に接続されていない他のサービスと同じ電子メール アドレスおよびパスワードを利用している場合に、Identity Protection では、これらのパスワード ハッシュを侵害が検知されたパスワードと比較して、侵害された資格情報を検出できます。  詳細については、「[Azure AD Connect 同期を使用したパスワード ハッシュ同期の実装](../hybrid/how-to-connect-password-hash-synchronization.md)」をご覧ください。

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>すべての特権ロールに属するユーザーおよび露出しているユーザーに多要素認証 (MFA) を要求する

Azure AD では、管理者や、アカウントが侵害された場合に大きな影響を及ぼす他のすべてユーザー (たとえば、財務責任者) を含め、すべてのユーザーに多要素認証 (MFA) を要求することをお勧めします。 これにより、パスワードの漏洩による攻撃のリスクが軽減されます。

以下を有効にします。

* 組織のすべてのユーザーに対して、[条件付きアクセス ポリシーを使用して MFA](../authentication/howto-mfa-getstarted.md) を有効にします。

Windows Hello for Business を使用する場合は、Windows Hello サインイン エクスペリエンスを使用して MFA 要件を満たすことができます。 詳しくは、「[Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport)」をご覧ください。 

#### <a name="configure-identity-protection"></a>Identity Protection を構成する 

Azure AD Identity Protection は、アルゴリズムベースの監視およびレポート ツールで、組織の ID に影響する潜在的な脆弱性を検出するために使用できます。 検出された不審なアクティビティへの自動対応を構成し、それらを解決するのに適切なアクションを実行できます。 詳細については、「[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)」をご覧ください。

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Office 365 セキュリティ スコアを取得する (Office 365 を使用している場合)

セキュリティ スコアは、使用している Office 365 サービス (OneDrive、SharePoint、Exchange など) を検出し、設定とアクティビティを参照して、Microsoft によって確立されたベースラインと比較します。 セキュリティに関するベスト プラクティスにどの程度従っているかに基づいてスコアが算出されます。 Office 365 Business Premium または Enterprise サブスクリプションの管理者アクセス許可を持つユーザー (グローバル管理者またはカスタムの管理者ロール) は、[https://securescore.office.com](https://securescore.office.com/) でセキュリティ スコアにアクセスできます。

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Office 365 セキュリティおよびコンプライアンス ガイダンスを確認する (Office 365 を使用している場合)

[セキュリティとコンプライアンスの計画](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57)に関するページでは、Office 365 のお客様が Office 365 を構成する方法とその他の EMS 機能を活用する方法のアプローチについて概説しています。 次に、「[Office 365 でデータやサービスへのアクセスを保護する](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)」の手順 3. ～ 6. と「[Office 365 でセキュリティとコンプライアンスを監視する](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6)」のガイドを確認します。

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Office 365 アクティビティの監視を構成する (Office 365 を使用している場合)

組織内のユーザーが Office 365 サービスをどのように使用しているかを監視できるため、管理者アカウントを持つユーザーや、ポータルにサインインしていないため Office 365 へのアクセスが必要のないユーザーを識別できます。 詳細については、「[Microsoft 365 管理センターのアクティビティ レポート](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)」を参照してください。

#### <a name="establish-incidentemergency-response-plan-owners"></a>インシデント/緊急時対応計画の所有者を設定する

インシデント対応の効果的な実行は複雑な作業です。 そのため、成功するインシデント対応機能を確立するには、多大な計画とリソースが必要です。 サイバー攻撃を継続的に監視し、インシデントの処理の優先順位付けの手順を確立することが重要です。 データを効果的に収集、分析、レポートする方法は、他の内部グループや計画所有者とのリレーションシップを構築し、コミュニケーションを確立するために不可欠です。 詳しくは、「[Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717)」をご覧ください。 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>まだ行っていない場合は、オンプレミスの特権管理者アカウントをセキュリティで保護する

Azure Active Directory テナントがオンプレミスの Active Directory と同期されている場合は、[特権アクセスのセキュリティ保護のロードマップ](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): ステージ 1 のガイダンスに従います。 これには、オンプレミスの管理タスクを実行する必要があるユーザー用の別の管理者アカウントの作成、Active Directory 管理者の Privileged Access Workstations のデプロイ、ワークステーションとサーバーの一意のローカル管理者パスワードの作成が含まれます。

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure へのアクセスを管理する組織における追加の手順

#### <a name="complete-an-inventory-of-subscriptions"></a>サブスクリプションのインベントリを完了する

エンタープライズ ポータルと Azure Portal を使用して、運用アプリケーションをホストする組織内のサブスクリプションを識別します。

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Microsoft アカウントを管理者ロールから削除する

Xbox、Live、Outlook などの他のプログラムの Microsoft アカウントは、組織のサブスクリプションの管理者アカウントとして使用しないでください。 すべての Microsoft アカウントから管理状態を除去し、Azure Active Directory (たとえば、chris@contoso.com) の職場または学校アカウントで置き換えます。

#### <a name="monitor-azure-activity"></a>Azure のアクティビティを監視する

Azure アクティビティ ログは、Azure でのサブスクリプション レベルのイベント履歴を提供します。 このログは、だれがどのリソースをいつ作成、更新、または削除したかについての情報を提供します。 詳しくは、「[Azure サブスクリプションの重要なアクションを監査して通知を受信する](../../azure-monitor/platform/quick-audit-notify-action-subscription.md)」をご覧ください。

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Azure AD を介して他のクラウド アプリへのアクセスを管理する組織における追加の手順

#### <a name="configure-conditional-access-policies"></a>条件付きアクセス ポリシーを構成する

オンプレミスのアプリケーションとクラウドでホストされるアプリケーションの条件付きアクセス ポリシーを準備します。 ユーザーがワークプレースに参加しているデバイスを持っている場合、詳しくは「[Azure Active Directory デバイス登録を使用したオンプレミスの条件付きアクセスの設定](../active-directory-device-registration-on-premises-setup.md)」をご覧ください。


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>ステージ 3:可視性の構築と管理者アクティビティのフル コントロールの実施

![ステージ 3 管理者アクティビティの制御](./media/directory-admin-roles-secure/stage-three.png)

ステージ 3 は、ステージ 2 のリスク軽減の上に構築され、約 1 ～ 3 か月以内に実装するように設計されています。 セキュリティで保護された特権アクセスのロードマップのこのステージには、次のコンポーネントが含まれます。

### <a name="general-preparation"></a>一般的な準備

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>管理者ロールに属するユーザーのアクセス レビューを実行する

クラウド サービス経由で特権アクセス権を得る企業ユーザーの増加は、管理されないプラットフォームの増加につながります。 Office 365 のグローバル管理者になるユーザー、Azure サブスクリプション管理者、VM や SaaS アプリの管理者アクセス権を持つユーザーなどがこれにあたります。 代わりに、組織のすべての従業員、特に管理者は、日常的なビジネス トランザクションを特権のないユーザーとして処理し、必要な場合にのみ管理者権限で実行する必要があります。 初期導入後に管理者ロールに属するユーザー数が拡大する可能性があるため、アクセス レビューを実行し、管理者特権をアクティブにする資格を持つすべてのユーザーを確認してください。 

以下の手順を実行します。

* Azure AD 管理者であるユーザーを確認し、オンデマンドの Just-In-Time 管理者アクセス権とロール ベースのセキュリティ制御を有効にします。
* 管理特権でアクセスする明確な正当性を持たないユーザーを別のロールに変換します (資格のあるロールがない場合は、ユーザーを削除します)。

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>すべてのユーザーについて、より強力な認証のロールアウトを継続する 

経営幹部、上位管理者、クリティカルな IT およびセキュリティ担当者、露出度の高いその他のユーザーには、Azure MFA や Windows Hello などの最新の強力な認証を要求します。 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Azure AD の管理に専用のワークステーションを使用する

攻撃者は、特権アカウントをターゲットとして組織のデータやシステムにアクセスして、プログラム ロジックを変更したり、資格情報を入力する管理者の情報を探ってデータの整合性と信頼性を破壊しようとする場合があります。 Privileged Access Workstation (PAW) には、機密性の高いタスクに専用のオペレーティング システムが用意されており、インターネット上の攻撃や脅威ベクトルから保護されます。 このような機密性の高いタスクとアカウントを日常的に使用するワークステーションやデバイスから切り離すことで、フィッシング攻撃、アプリケーションと OS の脆弱性、さまざまな偽装攻撃、資格情報の盗難 (キーロガー、pass-the-hash、pass-the-ticket など) から強力に保護されます。 Privileged Access Workstations をデプロイすることで、管理者が強化されたデスクトップ環境以外で管理者の資格情報を入力するリスクを軽減できます。 詳しくは、[Privileged Access Workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) に関するページをご覧ください。

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>インシデントの処理に関する米国国立標準技術研究所の推奨事項を確認する 

米国国立標準技術研究所 (NIST) は、特にインシデント関連のデータの分析と各インシデントへの適切な対応の決定について、インシデント処理のガイドラインを提供しています。 詳細については、「[The (NIST) Computer Security Incident Handling Guide (SP 800-61, Revision 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)」((NIST) コンピューター セキュリティ インシデント対応ガイド (SP 800 61、リビジョン 2)) を参照してください。

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>JIT に Privileged Identity Management (PIM) を実装して管理者ロールを追加する

Azure Active Directory の場合は、[Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) 機能を使用します。 特権ロールの期間限定アクティブ化は以下を有効にすることで動作します。

* 特定のタスクを実行するために管理特権をアクティブにする
* アクティブ化プロセス中に MFA を適用する
* アラートを使用して帯域外の変更について管理者に通知する
* ユーザーが特定の特権をあらかじめ構成された時間保持できるようにする
* セキュリティ管理者に、すべての特権 ID の検出、監査レポートの表示、管理者特権をアクティブにする資格を持つすべてのユーザーを識別するためのアクセス レビューの作成を許可する

既に Azure AD Privileged Identity Management を使用している場合は、必要に応じて期限付きの特権の期間 (たとえば、メンテナンス期間) を調整します。

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>パスワードベースのサインイン プロトコルへの露出を確認する (Exchange Online を使用している場合)

以前は、プロトコルでは、ユーザー名/パスワードの組み合わせはデバイス、電子メール アカウント、携帯電話などに埋め込まれていると見なされていました。 しかし現在は、クラウドにサイバー攻撃のリスクがあるため、資格情報が侵害された場合に組織に致命的な影響を与える可能性のあるユーザーをすべて特定し、強力な認証要件と条件付きアクセスを実装することで、それらのユーザーがユーザー名/パスワードを介して自分の電子メールにサインインできないようにすることをお勧めします。 [条件付きアクセス ポリシーを使用してレガシ認証をブロック](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication)できます。 Exchnage Online での[基本認証の無効化](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online)の詳細をご確認ください。 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Office 365 ロールのロール レビュー アセスメントを実行する (Office 365 を使用している場合)

すべての管理者ユーザーが適切なロールに属しているどうかを評価します (このアセスメントに基づいて削除または再割り当てします)。

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Office 365 で使用されているセキュリティ インシデント管理アプローチを確認し、自分の組織と比較する

このレポートは、「[Security Incident Management in Microsoft Office 365 (Microsoft Office 365 でのセキュリティ インシデント管理)](https://www.microsoft.com/download/details.aspx?id=54302)」からダウンロードできます。

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>オンプレミスの特権管理者アカウントのセキュリティ保護に進む

Azure Active Directory がオンプレミスの Active Directory に接続されている場合は、[特権アクセスのセキュリティ保護のロードマップ](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): ステージ 2 のガイダンスに従います。 これには、すべての管理者に対する Privileged Access Workstations のデプロイ、MFA の要求、DC メンテナンスでの Just Enough Admin の使用、ドメインの攻撃対象領域の縮小、攻撃検出用の ATA のデプロイが含まれます。

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure へのアクセスを管理する組織における追加の手順

#### <a name="establish-integrated-monitoring"></a>統合型の監視を確立する

[Azure Security Center](../../security-center/security-center-intro.md) は、Azure サブスクリプション全体に統合されたセキュリティの監視とポリシーの管理を提供し、気づかれなかった可能性がある脅威を検出できるよう支援し、セキュリティ ソリューションの広範なエコシステムと連動します。

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>ホストされる仮想マシン内で、特権アカウントのインベントリを行う

ほとんどの場合、すべての Azure サブスクリプションまたはリソースへの無制限のアクセス許可をユーザーに付与する必要はありません。 Azure AD 管理者ロールを使用して、組織内の職務を分離し、特定のジョブを実行する必要のあるユーザーに必要なアクセス権のみを付与します。 たとえば、Azure AD 管理者ロールを使用して、ある管理者がサブスクリプションで VM のみを管理できるようにし、他の管理者が同じサブスクリプション内で SQL データベースを管理できるようにします。 詳細については、「[Azure Portal でのロールベースの Access Control の基礎を確認する](../../role-based-access-control/overview.md)」を参照してください。

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Azure AD 管理者ロールに対して PIM を実装する

Azure AD 管理者ロールと共に Privileged Identity Management を使用して、Azure リソースへのアクセスを管理、制御、監視します。 PIM を使用して、特権の露出時間を短縮し、レポートとアラートを通じて使用状況の可視性を高めることで、特権アカウントをサイバー攻撃から保護します。 詳しくは、[Privileged Identity Management での Azure リソースへの RBAC アクセスの管理](../../role-based-access-control/pim-azure-resource.md)に関する記事をご覧ください。

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Azure ログ統合を使用して、関連する Azure ログを SIEM システムに送信する 

Azure ログ統合を使用すると、未加工のログを、Azure リソースから組織の既存のセキュリティ情報イベント管理 (SIEM) システムに統合できます。 [Azure ログ統合](../../security/security-azure-log-integration-overview.md)では、Windows イベント ビューアー ログの Windows イベントと、Azure アクティビティ ログの Azure リソース、Azure Security Center アラート、Azure 診断ログを収集します。 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Azure AD を介して他のクラウド アプリへのアクセスを管理する組織における追加の手順

#### <a name="implement-user-provisioning-for-connected-apps"></a>接続されているアプリのユーザー プロビジョニングを実装する

Azure AD を使用すると、Dropbox、Salesforce、ServiceNow などのクラウド (SaaS) アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。 詳しくは、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」をご覧ください。

#### <a name="integrate-information-protection"></a>Information Protection を統合する

MCAS を使用すると、ファイルを調査し、Azure Information Protection 分類ラベルに基づいてポリシーを設定して、クラウド内のデータの可視性と制御を向上させることができます。 クラウド内のファイルをスキャンして分類し、Azure Information Protection ラベルを適用します。 詳しくは、「[Azure Information Protection の統合](https://docs.microsoft.com/cloud-app-security/azip-integration)」をご覧ください。

#### <a name="configure-conditional-access"></a>条件付きアクセスを構成する

グループ、場所、アプリケーションの機密性に基づいて [SaaS アプリ](https://azure.microsoft.com/overview/what-is-saas/)や Azure AD 接続アプリの条件付きアクセスを構成します。 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>接続されているクラウド アプリのアクティビティを監視する

接続されているアプリケーションでもユーザーのアクセスが保護されていることを確認するには、[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) の利用をお勧めします。 これは、以下を有効にすることで、管理者アカウントを保護するだけでなく、クラウド アプリへのエンタープライズ アクセスも保護します。

* 可視性と制御をクラウド アプリに拡張する
* アクセス、アクティビティ、データ共有のポリシーを作成する
* 危険なアクティビティ、異常な動作、脅威を自動的に識別する
* データの漏えいを防ぐ
* リスクを最小限に抑え、脅威の防止とポリシーの適用を自動化する

Cloud App Security SIEM エージェントは、Cloud App Security を SIEM サーバーと統合して、Office 365 のアラートとアクティビティの一元的な監視を可能にします。 サーバー上で稼働し、Cloud App Security からのアラートとアクティビティをプルして、SIEM サーバーにストリーム送信します。 詳しくは、「[SIEM の統合](https://docs.microsoft.com/cloud-app-security/siem)」をご覧ください。

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>ステージ 4: 防御の構築を継続して予防的なセキュリティ対策を強化する

![ステージ 4 予防的なセキュリティ対策の採用](./media/directory-admin-roles-secure/stage-four.png)

ロードマップのステージ 4 はステージ 3 の可視性の上に構築され、6 か月以降に実装するよう設計されています。 ロードマップが完成すると、現在知られていて利用できる潜在的な攻撃からの強力な特権アクセス保護を作成できます。 残念ながら、セキュリティの脅威は常に進化および変化しているため、セキュリティは、環境をターゲットとする敵対者のコストを上げ、成功率を減らすことに重点を置いた継続的なプロセスと考えることをお勧めします。

特権アクセスの保護は、現代の組織のビジネス資産のセキュリティ保証を確立するための重要な最初の手順ですが、ポリシー、操作、情報セキュリティ、サーバー、アプリケーション、PC、デバイス、クラウド ファブリック、その他の継続的なセキュリティ保証を提供するコンポーネントなどの要素が含まれる完全なセキュリティ プログラムを構成するのはそれだけではありません。 

特権アクセス アカウントの管理に加えて、以下のことを継続的に確認することをお勧めします。

* 管理者が日常業務を特権のないユーザーとして行っていることを確認します。
* 必要な場合にのみ特権アクセスを付与し、その後削除します (Just-In-Time)。
* 特権アカウントに関連する監査アクティビティを保持および確認します。

完全なセキュリティ ロードマップの構築について詳しくは、「[Microsoft クラウド IT アーキテクチャのリソース](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources)」をご覧ください。 Microsoft サービスと連携して、これらのトピックについて支援を受ける方法について詳しくは、Microsoft の担当者にお問い合わせいただくか、「[Build critical cyber defenses to protect your enterprise (企業を保護するための重要なサイバー防御を構築する)](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)」をご覧ください。

セキュリティで保護された特権アクセスのロードマップのこの最終的な継続ステージには、次のコンポーネントが含まれます。

### <a name="general-preparation"></a>一般的な準備

#### <a name="review-admin-roles-in-azure-active-directory"></a>Azure Active Directory の管理者ロールを確認する 

現在の組み込み Azure AD 管理者ロールがまだ最新の状態であるかどうかを判断し、ユーザーが対応するアクセス許可に必要なロールと委任にのみ属していることを確認します。 Azure AD では、各種役割ごとに別々の管理者を指定することができます。 詳しくは、「[Azure Active Directory での管理者ロールの割り当て](directory-assign-admin-roles.md)」をご覧ください。

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Azure AD 参加済みデバイスの管理権を持つユーザーを確認する

詳しくは、「[ハイブリッド Azure Active Directory 参加済みデバイスの構成方法](../device-management-hybrid-azuread-joined-devices-setup.md)」をご覧ください。

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>[Office 365 の組み込み管理者ロール](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)のメンバーを確認する
Office 365 を使用している場合。
‎
#### <a name="validate-incident-response-plan"></a>インシデント対応計画を検証する

計画を強化するために、計画が想定どおりに動作していることを定期的に検証することをお勧めします。

* 既存のロードマップを実行して何が欠落していたかを確認する
* 事後の分析に基づいて、既存のベスト プラクティスを改訂または新しいベスト プラクティスを定義する
* 更新したインシデント対応計画とベスト プラクティスが、組織全体に配布されていることを確認する


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure へのアクセスを管理する組織における追加の手順 

[Azure サブスクリプションの所有権を別のアカウントに譲渡する](../../billing/billing-subscription-transfer.md)必要があるかどうかを判断します。
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"非常時": 緊急時の対処方法

![緊急時対処アクセス用のアカウント](./media/directory-admin-roles-secure/emergency.jpeg)

1. 主要な管理者とセキュリティ責任者に、インシデントに関する適切な情報を通知します。

2. 攻撃プレイブックをレビューします。 

3. "非常時" アカウントのユーザー名/パスワードの組み合わせにアクセスして Azure AD にサインインします。 

4. [Azure サポート要求をオープン](../../azure-supportability/how-to-create-azure-support-request.md)して、Microsoft の支援を受けます。

5. [Azure AD サインイン レポート](../reports-monitoring/overview-reports.md)を参照します。 イベントの発生からそのイベントがレポートに含まれるまでの時間差がある可能性があります。

6. ハイブリッド環境でフェデレーション サーバーと AD FS サーバーを利用できない場合、フェデレーション認証からパスワード ハッシュ同期の使用に一時的に切り替えることが必要な場合があります。これにより、AD FS サーバーが使用可能になるまで、ドメイン フェデレーションは管理された認証に戻ります。

7. 特権アカウントの電子メールを監視します。

8. 潜在的なフォレンジック調査と法的調査のために関連ログのバックアップを保存してください。

Microsoft Office 365 がセキュリティ インシデントを処理する方法について詳しくは、[Microsoft Office 365 でのセキュリティ インシデント管理](https://aka.ms/Office365SIM)に関するドキュメントをご覧ください。

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>よくあるご質問: 特権アクセスの保護についてよく寄せられる質問  

**質問:** セキュリティで保護されたアクセス コンポーネントをまだ実装していない場合はどうすればよいですか?

**回答:** 少なくとも 2 つの非常用アカウントを定義し、特権管理者アカウントに MFA を割り当て、ユーザー アカウントをグローバル管理者アカウントから分離します。

**質問:** セキュリティ侵害の後、最初に対処する必要がある最上位の問題は何ですか?

**回答:** 露出度の高い個人に対して最も強力な認証を要求していることを確認します。

**質問:** 特権管理者が非アクティブ化されている場合はどうしますか?

**回答:** 常に最新の状態に保たれているグローバル管理者アカウントを作成します。

**質問:** 唯一のグローバル管理者が退職し、アクセスできない場合はどうしますか? 

**回答:** 非常用アカウントの 1 つを使用して、即時特権アクセス権を取得します。

**質問:** 自分の組織内で管理者を保護するにはどうすればよいですか?

**回答:** 管理者が日常業務を常に標準の "特権のない" ユーザーとして行うようにします。

**質問:** Azure AD 内で管理者アカウントを作成するためのベスト プラクティスは何ですか?

**回答:** 特定の管理タスク用の特権アクセスを予約します。

**質問:** 永続的な管理者アクセス権を縮小するにはどのようなツールが存在しますか?

**回答:** Privileged Identity Management (PIM) と Azure AD 管理者ロールです。

**質問:** Azure AD への管理者アカウントの同期について Microsoft はどのような立場にありますか?

**回答:** 0 層の管理者アカウント (AD フォレスト、ドメイン、またはドメイン コントローラー、およびすべての資産を直接または間接的に管理制御できるアカウント、グループ、その他の資産を含む) は、オンプレミスの AD アカウントに対してのみ利用でき、通常はクラウドの Azure AD と同期されません。

**質問:** ポータルで管理者にランダムな管理者アクセス権が割り当てられないようにするにはどうすればよいですか?

**回答:** すべてのユーザーとほとんどの管理者には特権のないアカウントを使用します。 まず、組織のフットプリントを作成して、どの少数の管理者に特権を与えるかを決定します。 そして、新しく作成した管理ユーザーを監視します。

## <a name="next-steps"></a>次の手順

* [製品のセキュリティに関する Microsoft Trust Center](https://www.microsoft.com/trustcenter/security) - Microsoft クラウド製品およびサービスのセキュリティ機能

* [Microsoft Trust Center - コンプライアンス](https://www.microsoft.com/trustcenter/compliance/complianceofferings) - Microsoft のクラウド サービスのコンプライアンス認証の包括的セット

* [リスク アセスメントの実施方法に関するガイダンス](https://www.microsoft.com/trustcenter/guidance/risk-assessment) - Microsoft クラウド サービスのセキュリティとコンプライアンス要件の管理

### <a name="other-microsoft-online-services"></a>その他の Microsoft オンライン サービス

* [Microsoft Intune のセキュリティ](https://www.microsoft.com/trustcenter/security/intune-security) - Intune は、クラウドからのモバイル デバイス管理、モバイル アプリケーション管理、PC 管理機能を提供します。

* [Microsoft Dynamics 365 のセキュリティ](https://www.microsoft.com/trustcenter/security/dynamics365-security) - Dynamics 365 は、Microsoft クラウドベースのソリューションで、顧客関係管理 (CRM) とエンタープライズ リソース プランニング (ERP) の機能を統合します。
