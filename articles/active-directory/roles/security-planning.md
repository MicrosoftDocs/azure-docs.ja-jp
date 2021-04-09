---
title: Azure AD の管理者向けのセキュリティで保護されたアクセス プラクティス | Microsoft Docs
description: 組織の管理アクセスと管理者アカウントがセキュリティで保護されるようにします。 システム設計者および Azure AD、Azure、Microsoft Online Services を構成する IT 担当者向けです。
services: active-directory
keywords: ''
author: rolyon
manager: daveba
ms.author: rolyon
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f2fe29ede2bf0f92049d1ae82bae87326057a63
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594296"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する

ビジネス資産のセキュリティは、IT システムを管理する特権アカウントの整合性に依存します。 サイバー攻撃者は、資格情報の盗難攻撃を使用して、管理者アカウントやその他の特権アクセスをターゲットにし、機密データへのアクセスを試みます。

クラウド サービスの場合、防止と対応は、クラウド サービス プロバイダーと顧客の共同責任です。 エンドポイントとクラウドに対する最新の脅威について詳しくは、[Microsoft セキュリティ インテリジェンス レポート](https://www.microsoft.com/security/operations/security-intelligence-report)のページをご覧ください。 この記事は、現在のプランとここで説明しているガイダンス間のギャップを埋めるためのロードマップの作成に役立ちます。

> [!NOTE]
> Microsoft は、最高レベルの信頼、透過性、標準への準拠、規制コンプライアンスに努めています。 Microsoft のグローバルのインシデント対応チームがクラウド サービスへの攻撃の影響をどのように軽減するか、また Microsoft のビジネス製品とクラウド サービスにセキュリティがどのように組み込まれているかについて詳しくは、「[Microsoft Trust Center - セキュリティ](https://www.microsoft.com/trustcenter/security)」をご覧ください。Microsoft コンプライアンス ターゲットについては、「[Microsoft Trust Center - コンプライアンス](https://www.microsoft.com/trustcenter/compliance)」をご覧ください。

従来、組織のセキュリティは、セキュリティ境界としてネットワークの入口と出口のポイントに重点を置いていました。 しかし、インターネット上の SaaS アプリと個人用デバイスでは、この方法があまり効果的ではありません。 Azure AD では、ネットワーク セキュリティ境界を組織の ID 層の認証に置き換え、管理下にある特権管理者ロールにユーザーを割り当てます。 環境がオンプレミス、クラウド、ハイブリッドのいずれであるかにかかわらず、ユーザーのアクセスを保護する必要があります。

特権アクセスをセキュリティで保護するには、以下に対する変更が必要です。

* プロセス、管理作業、ナレッジ管理
* ホスト防御、アカウントの保護、ID 管理などの技術的なコンポーネント

重要な Microsoft サービスで管理および報告されている方法で、特権アクセスを保護します。 オンプレミスの管理者アカウントがある場合は、「[特権アクセスの保護](/windows-server/identity/securing-privileged-access/securing-privileged-access)」で、Active Directory でのオンプレミスおよびハイブリッドの特権アクセスに関するガイダンスをご覧ください。

> [!NOTE]
> この記事のガイダンスでは、Azure Active Directory Premium プラン P1 と P2 に含まれている Azure Active Directory の主な機能を参照します。 Azure Active Directory Premium P2 は、EMS E5 スイートおよび Microsoft 365 E5 スイートに含まれています。 このガイダンスでは、組織がユーザー用に Azure AD Premium P2 ライセンスを既に購入していることを想定しています。 これらのライセンスがない場合、ガイダンスの一部は組織に適用されないことがあります。 また、この記事全体を通して、全体管理者という用語は "社内管理者" または "テナント管理者" と同義です。

## <a name="develop-a-roadmap"></a>ロードマップの作成

マイクロソフトでは、サイバー攻撃者から特権アクセスを保護するためのロードマップを作成して従うことをお勧めします。 既存の機能と組織内の特定の要件に合わせてロードマップをいつでも調整できます。 ロードマップの各ステージで、敵対者がオンプレミス、クラウド、ハイブリッドの資産の特権アクセスを攻撃するコストと難易度を上げます。 Microsoft では、次の 4 つのロードマップ ステージをお勧めします。 最初に、最も効果的で、最も迅速な実装をスケジュールします。 この記事は、サイバー攻撃のインシデントと応答の実装に関する Microsoft の経験に基づくガイドとすることができます。 このロードマップのタイムラインは、おおよそのものです。

![ロードマップのステージとタイム ライン](./media/security-planning/roadmap-timeline.png)

* ステージ 1 (24-48 時間): すぐに実施することをお勧めする重要な項目

* ステージ 2 (2-4 週間): 最もよく使用される攻撃手法の緩和

* ステージ 3 (1-3 か月): 可視性の構築と管理者アクティビティのフル コントロールの構築

* ステージ 4 (6 か月以降): セキュリティ プラットフォームをさらに強化するための防御の継続的な構築

このロードマップ フレームワークは、既にデプロイしている Microsoft テクノロジの使用を最大化するように設計されています。 既に展開されているか、展開を検討している他のベンダーのセキュリティ ツールを組み込むことも検討してください。

## <a name="stage-1-critical-items-to-do-right-now"></a>ステージ 1:すぐに実施する重要な項目

![ステージ 1 すぐに実施する重要な項目](./media/security-planning/stage-one.png)

ロードマップのステージ 1 では、迅速かつ簡単に実装できる重要なタスクに重点を置きます。 最初の 24 ～ 48 時間以内にこれらの少数の項目をすぐに実施して、セキュリティで保護された特権アクセスの基本的なレベルを確保することをお勧めします。 セキュリティで保護された特権アクセスのロードマップのこのステージには、次のアクションが含まれます。

### <a name="general-preparation"></a>一般的な準備

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management を有効にする

Azure AD 運用環境では、Azure AD Privileged Identity Management (PIM) を有効にすることをお勧めします。 PIM を有効にすると、特権アクセス ロールの変更についての電子メール通知を受け取るようになります。 通知により、高度な特権ロールに他のユーザーが追加された場合に早期警告を受け取れます。

Azure AD Privileged Identity Management は、Azure AD Premium P2 または EMS E5 に含まれています。 オンプレミスおよびクラウドのアプリケーションとリソースへのアクセスを保護するには、[Enterprise Mobility + Security の 90 日間無料試用版](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)にサインアップしてください。 Azure AD Privileged Identity Management と Azure AD Identity Protection では、Azure AD のレポート、監査、アラートを使用してセキュリティ アクティビティが監視されます。

Azure AD Privileged Identity Management を有効にしたら、次のようにします。

1. Azure AD 運用組織の全体管理者であるアカウントを使用して、[Azure portal](https://portal.azure.com/) にサインインします。

2. Privileged Identity Management を使用する Azure AD 組織を選択するには、Azure portal の右上隅でユーザー名を選択します。

3. Azure portal メニューの **[すべてのサービス]** を選択し、**Azure AD Privileged Identity Management** の一覧をフィルター処理します。

4. **[すべてのサービス]** の一覧から Privileged Identity Management を開き、ダッシュ ボードにピン留めします。

組織で PIM を使用する最初のユーザーに **セキュリティ管理者** と **特権ロール管理者** のロールが割り当てられていることを確認します。 ユーザーの Azure AD ディレクトリ ロールの割り当てを管理できるのは特権ロール管理者だけです。 PIM セキュリティ ウィザードの指示に従って初回の検出と割り当てを実行できます。 この時点でさらに変更を加えずに、ウィザードを終了することができます。

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>高度な特権ロールに属するアカウントを識別および分類する

Azure AD Privileged Identity Management を有効にした後、次の Azure AD のロールに属しているユーザーを表示します。

* 全体管理者
* 特権ロール管理者
* Exchange 管理者
* SharePoint 管理者

Azure AD Privileged Identity Management が組織内にない場合は、[PowerShell API](/powershell/module/azuread/get-azureaddirectoryrolemember) を使用できます。 全体管理者は、組織がサブスクライブしているすべてのクラウド サービスで同じアクセス許可を持つため、全体管理者ロールから始めます。 これらのアクセス許可は、Microsoft 365 管理センター、Azure portal、または Microsoft PowerShell の Azure AD モジュールのいずれで割り当てられた場合でも付与されます。

このようなロールの不要になったアカウントがあれば削除します。 次に、管理者ロールに割り当てられている残りのアカウントを分類します。

* 管理ユーザーに割り当てられているが、管理以外の目的 (たとえば、個人用電子メール) にも使用されている
* 管理ユーザーに割り当てられ、管理目的にのみ使用されている
* 複数のユーザー間で共有される
* 非常時の緊急アクセス シナリオ用
* 自動スクリプト用
* 外部ユーザー用

#### <a name="define-at-least-two-emergency-access-accounts"></a>少なくとも 2 つの緊急アクセス用アカウントを定義する

ユーザーが自分のロールから誤ってロックアウトされる可能性があります。 たとえば、フェデレーションされたオンプレミスの ID プロバイダーが利用できない場合、ユーザーはサインインしたり、既存の管理者アカウントをアクティブ化したりすることはできません。 2 つ以上の緊急アクセス アカウントを格納することにより、偶発的にアクセスできなくなった場合に対して備えることができます。

緊急アクセス アカウントは、Azure AD 組織内の特権アクセスを制限するのに役立ちます。 このようなアカウントは高い特権を持っており、特定のユーザーには割り当てられません。 緊急アクセス用アカウントは、通常の管理者アカウントを使うことができない "緊急事態" シナリオに制限されます。 緊急時アカウントの使用を管理し、必要な時のみに限定すようにします。

全体管理者ロールが割り当てられているか、その対象であるアカウントを評価します。 \*.onmicrosoft.com ドメイン ("非常時" の緊急アクセス用) を使用しているクラウド専用アカウントが見当たらない場合は、それらを作成します。 詳しくは、「[Azure AD で緊急アクセス用管理者アカウントを管理する](security-emergency-access.md)」をご覧ください。

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>多要素認証を有効にし、その他のすべての高度な特権を持つシングル ユーザー非フェデレーション管理者アカウントを登録します。

次のような Azure AD 管理者ロールを永続的に割り当てられているすべての個人ユーザーには、サインイン時に Azure AD Multi-Factor Authentication (MFA) を要求します:グローバル管理者、特権ロール管理者、Exchange 管理者、および SharePoint 管理者。 ガイドを使用して[管理者アカウントの Multi-Factor Authentication (MFA)](../authentication/howto-mfa-userstates.md) を有効にし、[https://aka.ms/mfasetup](https://aka.ms/mfasetup) でそれらすべてのユーザーが登録されていることを確認しします。 詳しくは、[Microsoft 365 におけるデータとサービスへのアクセスの保護](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)に関するページの手順 2 と手順 3 をご覧ください。 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>ステージ 2:よく使用される攻撃の緩和

![ステージ 2 よく使用される攻撃の緩和](./media/security-planning/stage-two.png)

ロードマップのステージ 2 は、資格情報の盗用や誤用に最もよく使われる攻撃手法の緩和に重点を置き、約 2 ～ 4 週間で実装することができます。 セキュリティで保護された特権アクセスのロードマップのこのステージには、次のアクションが含まれます。

### <a name="general-preparation"></a>一般的な準備

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>サービス、所有者、管理者のインベントリを実施する

"個人所有機器の持ち込み" と自宅からの作業のポリシーの増加や、ワイヤレス接続の拡大に伴い、ネットワークに接続するユーザーを監視することが非常に重要です。 セキュリティ監査によって、組織がサポートしていない、高いリスクを表すネットワーク上のデバイス、アプリケーション、およびプログラムを明らかにすることができます。 詳しくは、「[Azure セキュリティの管理と監視の概要](../../security/fundamentals/management-monitoring-overview.md)」をご覧ください。 インベントリ プロセスには、次のすべてのタスクを含めてください。

* 管理者ロールを持つユーザーと、それらのユーザーが管理できるサービスを識別します。
* Azure AD PIM を使用して、Azure AD への管理者アクセス権を持つ組織内のユーザーを確認します。
* Azure AD で定義されているロール以外に、Microsoft 365 には、組織内のユーザーに割り当てることができる管理者ロールのセットが用意されています。 各管理者ロールは、共通のビジネス機能にマップされ、[Microsoft 365 管理センター](https://admin.microsoft.com)で特定のタスクを行うためのアクセス許可を組織のユーザーに付与します。 Microsoft 365 管理センターを使用して、Azure AD で管理されていないロール経由を含め、組織内で Microsoft 365 への管理者アクセス権を持つユーザーを確認します。 詳しくは、[Microsoft 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)と [Office 365 のセキュリティ プラクティス](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)に関する記事をご覧ください。
* Azure、Intune、Dynamics 365 など、組織が利用しているサービスでインベントリを実行します。
* 管理目的で使用されているアカウントが次のようになっていることを確認します。

  * 使用可能な電子メール アドレスが設定されている
  * Azure AD Multi-Factor Authentication に登録しているか、オンプレミスの MFA を使用している
* ユーザーに管理アクセス権を使用するビジネス上の正当な理由を尋ねます。
* 管理者アクセス権を必要としない個人ユーザーとサービスの管理者アクセス権を取り消します。

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>職場または学校アカウントに切り替える必要がある管理者ロールの Microsoft アカウントを特定する

最初の全体管理者が Azure AD の使用を開始するときに既存の Microsoft アカウント資格情報を再利用する場合は、Microsoft アカウントを個々のクラウドベースまたは同期済みアカウントに置き換えます。

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>全体管理者アカウントのユーザー アカウントとメール転送を分離する

個人用電子メール アカウントはサイバー攻撃者によって定常的にフィッシングされるリスクがあるため、全体管理者アカウントで個人用電子メール アドレスを使用することは許容されません。 インターネット上のリスクを管理者特権から分離するために、管理者特権を持つユーザーごとに専用のアカウントを作成します。

* 全体管理者タスクを実行するユーザーには、必ず別のアカウントを作成します。
* 全体管理者が誤って電子メールを開いたり、管理者アカウントでプログラムを実行したりしないようにします。
* これらのアカウントが電子メールを作業用のメールボックスに転送することを確認します。
* 全体管理者 (およびその他の特権グループ) アカウントは、オンプレミスの Active Directory に結び付けられていないクラウド専用アカウントである必要があります。

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>管理者アカウントのパスワードが最近変更されたことを確認する

すべてのユーザーが過去 90 日間に少なくとも 1 回管理者アカウントにサインインし、パスワードを変更したことを確認します。 また、共有アカウントのパスワードが最近変更されたことを確認します。

#### <a name="turn-on-password-hash-synchronization"></a>パスワード ハッシュ同期をオンにする

Azure AD Connect では、オンプレミスの Active Directory からクラウドベースの Azure AD 組織に、ユーザーのパスワードのハッシュを同期します。 Active Directory フェデレーション サービス (AD FS) とのフェデレーションを使用する場合、パスワード ハッシュ同期をバックアップとして使用することができます。 このバックアップは、オンプレミスの Active Directory または AD FS サーバーが一時的に使用できなくなった場合に役立ちます。

パスワード ハッシュの同期により、ユーザーは、オンプレミスの Active Directory インスタンスにサインインするときに使うものと同じパスワードを使用してサービスにサインインできます。 パスワード ハッシュの同期を使用すると、改ざんされていることが判明しているパスワードとパスワード ハッシュを比較することで、Identity Protection は侵害された資格情報を検出することができます。 詳細については、「[Azure AD Connect 同期を使用したパスワード ハッシュ同期の実装](../hybrid/how-to-connect-password-hash-synchronization.md)」をご覧ください。

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>特権ロールに属するユーザーおよび露出しているユーザーに多要素認証を要求する

Azure AD では、すべてのユーザーに多要素認証 (MFA) を要求することをお勧めします。 アカウントが侵害された場合に大きな影響を与えるユーザー (財務責任者など) を考慮してください。 MFA により、パスワードの漏洩による攻撃のリスクが軽減されます。

以下を有効にします。

* 組織のすべてのユーザーに対して、[条件付きアクセス ポリシーを使用して MFA](../authentication/howto-mfa-getstarted.md) を有効にします。

Windows Hello for Business を使用する場合は、Windows Hello サインイン エクスペリエンスを使用して MFA 要件を満たすことができます。 詳しくは、「[Windows Hello](/windows/uwp/security/microsoft-passport)」をご覧ください。

#### <a name="configure-identity-protection"></a>Identity Protection を構成する

Azure AD Identity Protection は、アルゴリズムベースの監視およびレポート ツールで、組織の ID に影響する潜在的な脆弱性を検出します。 検出された不審なアクティビティへの自動対応を構成し、それらを解決するのに適切なアクションを実行できます。 詳細については、「[Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)」をご覧ください。

#### <a name="obtain-your-microsoft-365-secure-score-if-using-microsoft-365"></a>Microsoft 365 のセキュリティ スコアを取得する (Microsoft 365 を使用している場合)

セキュリティ スコアは、使用している Microsoft 365 サービスの設定とアクティビティを参照して、Microsoft によって確立されたベースラインと比較します。 セキュリティ プラクティスにどの程度従っているかに基づいてスコアが算出されます。 Microsoft 365 Business Standard または Enterprise サブスクリプションの管理者アクセス許可を持つユーザーは、[https://securescore.office.com](https://securescore.office.com/) でセキュリティ スコアにアクセスできます。

#### <a name="review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365"></a>Microsoft 365 のセキュリティおよびコンプライアンス ガイダンスを確認する (Microsoft 365 を使用している場合)

[セキュリティとコンプライアンスの計画](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57)に関するページでは、Office 365 のお客様が Office 365 を構成し、その他の EMS 機能を有効にするアプローチについて概説しています。 次に、[Microsoft 365 でのデータとサービスへのアクセスの保護](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)に関するページの手順 3 から 6 と [Microsoft 365 でのセキュリティとコンプライアンスの監視](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6)方法に関するガイドを確認します。

#### <a name="configure-microsoft-365-activity-monitoring-if-using-microsoft-365"></a>Microsoft 365 のアクティビティ監視を構成する (Microsoft 365 を使用している場合)

Microsoft 365 を使用しているユーザーについて組織を監視し、管理者アカウントを持っているが、これらのポータルにサインインしないために Microsoft 365 へのアクセスを必要としない可能性があるユーザーを識別します。 詳細については、「[Microsoft 365 管理センターのアクティビティ レポート](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)」を参照してください。

#### <a name="establish-incidentemergency-response-plan-owners"></a>インシデント/緊急時対応計画の所有者を設定する

適切なインシデント対応機能を確立するには、多大な計画とリソースが必要です。 サイバー攻撃を継続的に監視し、インシデント処理の優先順位を設定する必要があります。 インシデント データを収集、分析、およびレポートしてリレーションシップを構築し、他の内部グループおよび計画責任者とのコミュニケーションを確立します。 詳しくは、「[Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717)」をご覧ください。

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>まだ行っていない場合は、オンプレミスの特権管理者アカウントをセキュリティで保護する

Azure Active Directory 組織がオンプレミスの Active Directory と同期されている場合は、[特権アクセスのセキュリティ保護のロードマップ](/windows-server/identity/securing-privileged-access/securing-privileged-access): この段階には次のものが含まれます。

* オンプレミスの管理タスクを実行する必要があるユーザー用に個別の管理者アカウントを作成する
* Active Directory 管理者向けの特権アクセス ワークステーションを配置する
* ワークステーションとサーバーに対して一意のローカル管理者パスワードを作成する

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure へのアクセスを管理する組織における追加の手順

#### <a name="complete-an-inventory-of-subscriptions"></a>サブスクリプションのインベントリを完了する

エンタープライズ ポータルと Azure Portal を使用して、運用アプリケーションをホストする組織内のサブスクリプションを識別します。

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Microsoft アカウントを管理者ロールから削除する

Xbox、Live、Outlook などの他のプログラムの Microsoft アカウントは、組織のサブスクリプションの管理者アカウントとして使用しないでください。 すべての Microsoft アカウントから管理者状態を除去し、Azure AD (たとえば、chris@contoso.com) の職場または学校アカウントで置き換えます。 管理の目的では、他のサービスではなく Azure AD で認証されるアカウントを利用します。

#### <a name="monitor-azure-activity"></a>Azure のアクティビティを監視する

Azure アクティビティ ログは、Azure でのサブスクリプション レベルのイベント履歴を提供します。 このログは、だれがどのリソースをいつ作成、更新、または削除したかについての情報を提供します。 詳しくは、「[Azure サブスクリプションの重要なアクションを監査して通知を受信する](../../azure-monitor/alerts/alerts-activity-log.md)」をご覧ください。

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Azure AD を介して他のクラウド アプリへのアクセスを管理する組織における追加の手順

#### <a name="configure-conditional-access-policies"></a>条件付きアクセス ポリシーを構成する

オンプレミスのアプリケーションとクラウドでホストされるアプリケーションの条件付きアクセス ポリシーを準備します。 ユーザーがワークプレースに参加しているデバイスを持っている場合、詳しくは「[Azure Active Directory デバイス登録を使用したオンプレミスの条件付きアクセスの設定](../../active-directory-b2c/overview.md)」をご覧ください。

## <a name="stage-3-take-control-of-admin-activity"></a>ステージ 3:管理者アクティビティの制御

![ステージ 3: 管理者アクティビティの制御](./media/security-planning/stage-three.png)

ステージ 3 は、ステージ 2 のリスク軽減の上に構築され、約 1 ～ 3 か月以内に実装する必要があります。 セキュリティで保護された特権アクセスのロードマップのこのステージには、次のコンポーネントが含まれます。

### <a name="general-preparation"></a>一般的な準備

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>管理者ロールに属するユーザーのアクセス レビューを実行する

クラウド サービス経由で特権アクセス権を得る企業ユーザーの増加は、管理されないアクセスにつながる可能性があります。 今日のユーザーは、Microsoft 365 の全体管理者や Azure サブスクリプション管理者になったり、VM や SaaS アプリの管理者アクセス権を持ったりすることができます。

組織では、すべての従業員が通常のビジネス トランザクションを特権のないユーザーとして処理し、必要な場合にのみ管理者権限を付与する必要があります。 アクセス レビューを完了して、管理者特権をアクティブ化する資格のあるユーザーを特定し、確認します。

推奨事項は次のとおりです。

1. Azure AD 管理者であるユーザーを確認し、オンデマンドの Just-In-Time 管理者アクセス権とロール ベースのセキュリティ制御を有効にします。
2. 管理特権でアクセスする明確な正当性を持たないユーザーを別のロールに変換します (資格のあるロールがない場合は、ユーザーを削除します)。

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>すべてのユーザーについて、より強力な認証のロールアウトを継続する

高度に露出されたユーザーには、Azure AD MFA や Windows Hello などの最新の強力な認証を使用するように要求します。 高度に露出されたユーザーの例を次に示します。

* 経営幹部レベルの役員
* 高レベルのマネージャー
* IT およびセキュリティの重要な担当者

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Azure AD の管理に専用のワークステーションを使用する

攻撃者は、データの整合性と信頼性を低下させることができるように、特権アカウントをターゲットにしようとすることがあります。 多くの場合、プログラム ロジックを変更するか、または管理者が資格情報を入力するのを盗み取る悪意のあるコードを使用します。 Privileged Access Workstation (PAW) には、機密性の高いタスクに専用のオペレーティング システムが用意されており、インターネット上の攻撃や脅威ベクトルから保護されます。 日常的に使用するワークステーションとデバイスからこのような機密性の高いタスクとアカウントを分離することで、以下に対する保護が強化されます。

* フィッシング攻撃
* アプリケーションとオペレーティング システムの脆弱性
* 偽装攻撃
* キーボード操作のログ記録、Pass-the-Hash、Pass-The-Ticket などの資格情報の盗用攻撃

特権アクセス ワークステーションを配置することで、管理者が強化されていないデスクトップ環境で資格情報を入力するリスクを軽減できます。 詳しくは、[Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) に関するページをご覧ください。

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>インシデントの処理に関する米国国立標準技術研究所の推奨事項を確認する

米国国立標準技術研究所 (NIST) は、特にインシデント関連のデータの分析と各インシデントへの適切な対応の決定について、インシデント処理のガイドラインを提供しています。 詳細については、「[The (NIST) Computer Security Incident Handling Guide (SP 800-61, Revision 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)」((NIST) コンピューター セキュリティ インシデント対応ガイド (SP 800 61、リビジョン 2)) を参照してください。

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>JIT に Privileged Identity Management (PIM) を実装して管理者ロールを追加する

Azure Active Directory の場合は、[Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) 機能を使用します。 特権ロールの期間限定アクティブ化は以下を有効にすることで動作します。

* 特定のタスクを実行する管理特権をアクティブにする
* アクティブ化プロセス中に MFA を適用する
* アラートを使用して帯域外の変更について管理者に通知する
* ユーザーが自分の特権アクセスをあらかじめ構成された時間保持できるようにする
* セキュリティ管理者に次のことを許可する:

  * すべての特権 ID を検出する
  * 監査レポートを表示する
  * 管理特権をアクティブ化する資格があるすべてのユーザーを識別するアクセス レビューを作成する

既に Azure AD Privileged Identity Management を使用している場合は、必要に応じて期限付きの特権の期間 (たとえば、メンテナンス期間) を調整します。

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>パスワードベースのサインイン プロトコルへの露出を確認する (Exchange Online を使用している場合)

資格情報が侵害された場合に、組織にとって致命的になる可能性のあるすべてのユーザーを識別することをお勧めします。 これらのユーザーには、強力な認証要件を設定し、Azure AD 条件付きアクセスを使用して、ユーザー名とパスワードを使用して電子メールにサインインしないようにします。 [条件付きアクセスを使用してレガシ認証](../conditional-access/block-legacy-authentication.md)をブロックすることができます。また、Exchange Online を使用して[基本認証をブロック](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online)することができます。

#### <a name="complete-a-roles-review-assessment-for-microsoft-365-roles-if-using-microsoft-365"></a>Microsoft 365 ロールのロール レビュー アセスメントを実行する (Microsoft 365 を使用している場合)

すべての管理者ユーザーが適切なロールに属しているどうかを評価します (このアセスメントに基づいて削除または再割り当てします)。

#### <a name="review-the-security-incident-management-approach-used-in-microsoft-365-and-compare-with-your-own-organization"></a>Microsoft 365 で使用されているセキュリティ インシデント管理アプローチを確認し、自分の組織と比較する

このレポートは、「[Security Incident Management in Microsoft 365 (Microsoft 365 でのセキュリティ インシデント管理)](https://www.microsoft.com/download/details.aspx?id=54302)」からダウンロードできます。

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>オンプレミスの特権管理者アカウントのセキュリティ保護に進む

Azure Active Directory がオンプレミスの Active Directory に接続されている場合は、[特権アクセスのセキュリティ保護のロードマップ](/windows-server/identity/securing-privileged-access/securing-privileged-access): ステージ 2 のガイダンスに従います。 このステージでは、次のことを行います。

* すべての管理者向けに特権アクセス ワークステーションを配置する
* Require MFA (MFA が必須)
* ドメイン コントローラーのメンテナンスに十分な管理者だけを使用して、ドメインの攻撃対象領域を減らす
* 攻撃検出のための高度な脅威評価を展開する

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure へのアクセスを管理する組織における追加の手順

#### <a name="establish-integrated-monitoring"></a>統合型の監視を確立する

[Azure Security Center](../../security-center/security-center-introduction.md)は以下の機能を提供します。

* Azure サブスクリプション全体のセキュリティ監視とポリシー管理を統合します
* 他の方法では見過ごされてしまう可能性のある脅威を検出します
* 幅広いセキュリティ ソリューションと連携します

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>ホストされる仮想マシン内で、特権アカウントのインベントリを行う

通常は、すべての Azure サブスクリプションまたはリソースへの無制限のアクセス許可をユーザーに付与する必要はありません。 Azure AD 管理者ロールを使用して、ユーザーが自分のジョブを実行するために必要なアクセス許可のみを付与します。 Azure AD 管理者ロールを使用して、ある管理者がサブスクリプションで VM のみを管理できるようにし、他の管理者が同じサブスクリプション内で SQL データベースを管理できるようにすることができます。 詳細については、[Azure ロールベースのアクセス制御](../../active-directory-b2c/overview.md)に関するページを参照してください。

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Azure AD 管理者ロールに対して PIM を実装する

Azure AD 管理者ロールと共に Privileged Identity Management を使用して、Azure リソースへのアクセスを管理、制御、監視します。 PIM を使用した保護では、特権の露出時間を短縮し、レポートとアラートを通じて使用状況の可視性を高めます。 詳細については、「[Azure AD Privileged Identity Management とは](../privileged-identity-management/pim-configure.md)」を参照してください。

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Azure ログ統合を使用して、関連する Azure ログを SIEM システムに送信する

Azure ログ統合を使用すると、未加工のログを、Azure リソースから組織の既存のセキュリティ情報イベント管理 (SIEM) システムに統合できます。 [Azure ログ統合](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)では、Windows イベント ビューアーのログから Windows イベントが収集され、Azure リソースが次から収集されます。

* Azure アクティビティ ログ
* Azure Security Center のアラート
* Azure リソース ログ

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Azure AD を介して他のクラウド アプリへのアクセスを管理する組織における追加の手順

#### <a name="implement-user-provisioning-for-connected-apps"></a>接続されているアプリのユーザー プロビジョニングを実装する

Azure AD を使用すると、Dropbox、Salesforce、ServiceNow などのクラウド アプリでのユーザー ID の作成と管理を自動化できます。 詳しくは、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」をご覧ください。

#### <a name="integrate-information-protection"></a>Information Protection を統合する

Microsoft Cloud App Security を使用すると、ファイルを調査し、Azure Information Protection 分類ラベルに基づいてポリシーを設定して、クラウド データの可視性と制御を向上させることができます。 クラウド内のファイルをスキャンして分類し、Azure Information Protection ラベルを適用します。 詳しくは、「[Azure Information Protection の統合](/cloud-app-security/azip-integration)」をご覧ください。

#### <a name="configure-conditional-access"></a>条件付きアクセスを構成する

グループ、場所、アプリケーションの機密性に基づいて [SaaS アプリ](https://azure.microsoft.com/overview/what-is-saas/)や Azure AD 接続アプリの条件付きアクセスを構成します。 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>接続されているクラウド アプリのアクティビティを監視する

接続されているアプリケーションでもユーザーのアクセスが確実に保護されるように、[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) の利用をお勧めします。 この機能により、クラウド アプリへのエンタープライズ アクセスと管理者アカウントが保護され、以下が可能になります。

* 可視性と制御をクラウド アプリに拡張する
* アクセス、アクティビティ、データ共有のポリシーを作成する
* 危険なアクティビティ、異常な動作、脅威を自動的に識別する
* データの漏えいを防ぐ
* リスクを最小限に抑え、脅威の防止とポリシーの適用を自動化する

Cloud App Security SIEM エージェントは、Cloud App Security を SIEM サーバーと統合して、Microsoft 365 のアラートとアクティビティの一元的な監視を可能にします。 サーバー上で稼働し、Cloud App Security からのアラートとアクティビティをプルして、SIEM サーバーにストリーム送信します。 詳しくは、「[SIEM の統合](/cloud-app-security/siem)」をご覧ください。

## <a name="stage-4-continue-building-defenses"></a>ステージ 4: 防御の構築を継続する

![ステージ 4: アクティブなセキュリティ対策の採用](./media/security-planning/stage-four.png)

ロードマップのステージ 4 は、6 か月以上で実装する必要があります。 ロードマップを完成させて、現在知られている潜在的な攻撃からの特権アクセスの保護を強化します。 将来のセキュリティ上の脅威については、セキュリティを継続的なプロセスとしてとらえ、お使いの環境をターゲットとする敵対者のコストを上げ、成功率を下げるようにすることをお勧めします。

ビジネス資産のセキュリティ保証を確立するには、特権アクセスを保護することが重要です。 ただし、これは継続的なセキュリティ保証を提供する完全なセキュリティ プログラムの一部である必要があります。 このプログラムには、次のような要素が含まれている必要があります。

* ポリシー
* 操作
* 情報セキュリティ
* サーバー
* アプリケーション
* コンピューター
* デバイス
* クラウド ファブリック

特権アクセス アカウントを管理する際に、次の方法をお勧めします。

* 管理者が日常業務を特権のないユーザーとして行っていることを確認します
* 必要な場合にのみ特権アクセスを付与し、その後削除します (Just-In-Time)
* 特権アカウントに関連する監査アクティビティ ログを保持します

完全なセキュリティ ロードマップの構築について詳しくは、「[Microsoft クラウド IT アーキテクチャのリソース](https://almbok.com/office365/microsoft_cloud_it_architecture_resources)」をご覧ください。 ロードマップのいずれかの部分の実装で Microsoft サービスを利用するには、Microsoft の担当者にお問い合わせいただくか、[企業を保護するための重要なサイバー防御の構築](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)に関するページをご覧ください。

セキュリティで保護された特権アクセスのロードマップのこの最終的な継続ステージには、次のコンポーネントが含まれます。

### <a name="general-preparation"></a>一般的な準備

#### <a name="review-admin-roles-in-azure-ad"></a>Azure AD の管理者ロールを確認する

現在の組み込み Azure AD 管理者ロールが最新の状態であるかどうかを判断し、ユーザーが必要なロールにのみ属していることを確認します。 Azure AD では、各種役割ごとに別々の管理者を割り当てることができます。 詳しくは、「[Azure Active Directory での管理者ロールの割り当て](permissions-reference.md)」をご覧ください。

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Azure AD 参加済みデバイスの管理権を持つユーザーを確認する

詳しくは、「[ハイブリッド Azure Active Directory 参加済みデバイスの構成方法](../devices/hybrid-azuread-join-plan.md)」をご覧ください。

#### <a name="review-members-of-built-in-microsoft-365-admin-roles"></a>[Microsoft 365 の組み込み管理者ロール](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)のメンバーを確認する
Microsoft 365 を使用していない場合は、この手順をスキップします。
‎
#### <a name="validate-incident-response-plan"></a>インシデント対応計画を検証する

計画を強化するために、計画が想定どおりに動作していることを定期的に検証することをお勧めします。

* 既存のロードマップを実行して何が欠落していたかを確認する
* 事後の分析に基づいて、既存のプラクティスを改訂するか新しいプラクティスを定義する
* 更新したインシデント対応計画とプラクティスが、組織全体に配布されていることを確認する


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure へのアクセスを管理する組織における追加の手順 

[Azure サブスクリプションの所有権を別のアカウントに譲渡する](../../cost-management-billing/manage/billing-subscription-transfer.md)必要があるかどうかを判断します。
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"非常時": 緊急時の対処方法

![緊急時対処アクセス用のアカウント](./media/security-planning/emergency.jpeg)

1. 主要な管理者とセキュリティ責任者に、インシデントに関する情報を通知します。

2. 攻撃プレイブックをレビューします。

3. "非常時" アカウントのユーザー名とパスワードの組み合わせにアクセスして Azure AD にサインインします。

4. [Azure サポート要求をオープン](../../azure-portal/supportability/how-to-create-azure-support-request.md)して、Microsoft の支援を受けます。

5. [Azure AD サインイン レポート](../reports-monitoring/overview-reports.md)を参照します。 イベントの発生からそのイベントがレポートに含まれるまでに時間がかかる場合があります。

6. ハイブリッド環境で、オンプレミスのインフラストラクチャがフェデレーションされており、AD FS サーバーを利用できない場合、フェデレーション認証からパスワード ハッシュ同期の使用に一時的に切り替えることができます。この切り替えにより、AD FS サーバーが使用可能になるまで、ドメイン フェデレーションは管理された認証に戻ります。

7. 特権アカウントの電子メールを監視します。

8. 潜在的なフォレンジック調査と法的調査のために関連ログのバックアップを保存してください。

Microsoft Office 365 がセキュリティ インシデントを処理する方法について詳しくは、[Microsoft Office 365 でのセキュリティ インシデント管理](https://aka.ms/Office365SIM)に関するドキュメントをご覧ください。

## <a name="faq-answers-for-securing-privileged-access"></a>よくあるご質問: 特権アクセスのセキュリティ保護に関する回答  

**質問:** セキュリティで保護されたアクセス コンポーネントをまだ実装していない場合はどうすればよいですか?

**回答:** 少なくとも 2 つの非常用アカウントを定義し、特権管理者アカウントに MFA を割り当て、ユーザー アカウントを全体管理者アカウントから分離します。

**質問:** セキュリティ侵害の後、最初に対処する必要がある最上位の問題は何ですか?

**回答:** 露出度の高い個人に対して最も強力な認証を要求していることを確認します。

**質問:** 特権管理者が非アクティブ化されている場合はどうしますか?

**回答:** 常に最新の状態に保たれている全体管理者アカウントを作成します。

**Q:** 全体管理者が 1 人しか残っておらず、連絡を取ることができない場合はどうしますか?

**回答:** 非常用アカウントの 1 つを使用して、即時特権アクセス権を取得します。

**質問:** 自分の組織内で管理者を保護するにはどうすればよいですか?

**回答:** 管理者が日常業務を常に標準の "特権のない" ユーザーとして行うようにします。

**質問:** Azure AD 内で管理者アカウントを作成するためのベスト プラクティスは何ですか?

**回答:** 特定の管理タスク用の特権アクセスを予約します。

**質問:** 永続的な管理者アクセス権を縮小するにはどのようなツールが存在しますか?

**回答:** Privileged Identity Management (PIM) と Azure AD 管理者ロールです。

**質問:** Azure AD への管理者アカウントの同期について Microsoft はどのような立場にありますか?

**回答:** 階層 0 の管理者アカウントは、オンプレミスの AD アカウントに対してのみ使用されます。 通常、このようなアカウントは、クラウドの Azure AD と同期されません。 階層 0 の管理者アカウントには、オンプレミスの Active Directory フォレスト、ドメイン、ドメイン コントローラー、および資産を直接的または間接的に管理するアカウント、グループ、その他の資産が含まれます。

**質問:** ポータルで管理者にランダムな管理者アクセス権が割り当てられないようにするにはどうすればよいですか?

**回答:** すべてのユーザーとほとんどの管理者には特権のないアカウントを使用します。 まず、組織のフットプリントを作成して、どの少数の管理者に特権を与えるかを決定します。 そして、新しく作成した管理ユーザーを監視します。

## <a name="next-steps"></a>次のステップ

* [製品のセキュリティに関する Microsoft Trust Center](https://www.microsoft.com/trustcenter/security) - Microsoft クラウド製品およびサービスのセキュリティ機能

* [Microsoft Trust Center - コンプライアンス](https://www.microsoft.com/trustcenter/compliance/complianceofferings) - Microsoft のクラウド サービスのコンプライアンス認証の包括的セット

* [リスク アセスメントの実施方法に関するガイダンス](https://www.microsoft.com/trustcenter/guidance/risk-assessment) - Microsoft クラウド サービスのセキュリティとコンプライアンス要件の管理

### <a name="other-microsoft-online-services"></a>その他の Microsoft オンライン サービス

* [Microsoft Intune のセキュリティ](https://www.microsoft.com/trustcenter/security/intune-security) - Intune は、クラウドからのモバイル デバイス管理、モバイル アプリケーション管理、PC 管理機能を提供します。

* [Microsoft Dynamics 365 のセキュリティ](https://www.microsoft.com/trustcenter/security/dynamics365-security) - Dynamics 365 は、Microsoft クラウドベースのソリューションで、顧客関係管理 (CRM) とエンタープライズ リソース プランニング (ERP) の機能を統合します。
