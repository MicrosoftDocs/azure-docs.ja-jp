---
title: オンプレミスの攻撃から Microsoft 365 を保護する
description: オンプレミスの攻撃から Microsoft 365 が確実に影響を受けないようにする方法に関するガイダンス。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d548f4d792d8980e2aa5040b09530eaf7868c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609908"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>オンプレミスの攻撃から Microsoft 365 を保護する

多くのお客様は、プライベートな企業ネットワークを Microsoft 365 に接続して、ユーザー、デバイス、アプリケーションがベネフィットを得られるようにしています。 ただし、このようなプライベート ネットワークは、数多くの明確に文書化された方法でセキュリティを侵害される可能性があります。 Microsoft 365 は多くの組織にとってある種の神経系として機能するため、侵害されたオンプレミス インフラストラクチャから保護することが不可欠です。

この記事では、オンプレミスの侵害から Microsoft 365 のクラウド環境を保護するためにシステムを構成する方法について説明します。 主に次の点について説明します。 

- Azure Active Directory (Azure AD) テナントの構成設定。
- Azure AD テナントをオンプレミスのシステムに安全に接続する方法。
- オンプレミスの侵害からクラウド システムを保護する方法でシステムを運用するために必要なトレードオフ。

このガイダンスを実装して、Microsoft 365 のクラウド環境をセキュリティで保護することを強くお勧めします。
> [!NOTE]
> この記事は、もともとブログ記事として公開されたものです。 長期の公開と保守のため、現在の場所に移動されました。
>
> この記事のオフライン バージョンを作成するには、お使いのブラウザーの PDF への出力機能を使用してください。 更新については、ときどきこちらをご確認ください。

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>セキュリティが侵害されたオンプレミス環境からの主要な脅威ベクトル


Microsoft 365 クラウド環境は、広範な監視とセキュリティのインフラストラクチャからベネフィットを受けています。 Microsoft 365 は、機械学習と人の知性を使用して、世界中のトラフィックに注意を向けています。 これにより、攻撃を迅速に検出し、ほぼリアルタイムで再構成できます。 

オンプレミスのインフラストラクチャを Microsoft 365 に接続するハイブリッド デプロイでは、多くの組織で、重要な認証およびディレクトリ オブジェクトの状態管理の決定のため、オンプレミスのコンポーネントに信頼が委任されます。
残念ながら、オンプレミス環境が侵害された場合、これらの信頼関係は、攻撃者が Microsoft 365 環境を侵害する機会になります。

2 つの主要な脅威ベクトルは、*フェデレーションの信頼関係* と *アカウントの同期* です。 どちらのベクトルによっても、攻撃者にクラウドへの管理アクセスが許可されます。

* **フェデレーションの信頼関係** (SAML 認証など) は、オンプレミスの ID インフラストラクチャを介して Microsoft 365 に対する認証を行うために使用されます。 SAML トークン署名証明書が侵害された場合、フェデレーションにより、その証明書を持つすべてのユーザーは、クラウド内の任意のユーザーを偽装できるようになります。 *可能な場合は、Microsoft 365 に対する認証のためのフェデレーションの信頼関係を無効にすることをお勧めします。*

* **アカウントの同期** を使用すると、特権を持つユーザー (資格情報を含む) や、Microsoft 365 での管理者特権を持つグループを変更できます。 直接、または信頼されたロールやグループに含めることにより、*同期されたオブジェクトが Microsoft 365 のユーザーを超える権限を保持しないようにすることをお勧めします*。 これらのオブジェクトに、信頼されたクラウドのロールまたはグループにおいて、確実に直接または入れ子の割り当てがないようにします。

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>オンプレミスのセキュリティ侵害から Microsoft 365 を保護する


前に説明した脅威ベクトルに対処するには、次の図に示す原則に従うことをお勧めします。

![Microsoft 365 の保護に関する参照アーキテクチャ。](media/protect-m365/protect-m365-principles.png)

1. **Microsoft 365 管理者アカウントを完全に分離します。** それらは次のようになっている必要があります。

    * Azure AD で管理されている。

     * 多要素認証を使用して認証される。

     *  Azure AD の条件付きアクセスによってセキュリティ保護されている。

     *  Azure マネージド ワークステーションを使用してのみアクセスされる。

    これらの管理者アカウントは制限付きの使用アカウントです。 *Microsoft 365 では、オンプレミスのアカウントが管理者特権を持つことはできません。* 

    詳細については、[Microsoft 365 の管理者ロールの概要](/microsoft-365/admin/add-users/about-admin-roles)に関するページを参照してください。 また、[Azure AD での Microsoft 365 用のロール](../roles/m365-workload-docs.md)に関するページも参照してください。

1. **Microsoft 365 からデバイスを管理します。** Azure AD Join とクラウドベースのモバイル デバイス管理 (MDM) を使用して、オンプレミスのデバイス管理インフラストラクチャへの依存関係を排除します。 これらの依存関係は、デバイスとセキュリティ制御を侵害するおそれがあります。

1. **オンプレミスのアカウントが、Microsoft 365 に対する昇格された特権を持たないようにしてください。**
    一部のアカウントは、NTLM、LDAP、または Kerberos 認証を必要とするオンプレミス アプリケーションにアクセスします。 これらのアカウントは、組織のオンプレミス ID インフラストラクチャ内に存在する必要があります。 サービス アカウントも含めて、これらのアカウントが特権のあるクラウドのロールまたはグループに含まれていないようにしてください。 また、これらのアカウントへの変更により、クラウド環境の整合性が影響を受けないようにしてください。 特権のあるオンプレミス ソフトウェアが、Microsoft 365 の特権のあるアカウントまたはロールに影響を与えることができないようにする必要があります。

1. **Azure AD クラウド認証** を使用して、オンプレミスの資格情報への依存関係を排除します。 常に、Windows Hello、FIDO、Microsoft Authenticator、Azure AD 多要素認証などの強力な認証を使用します。

## <a name="specific-security-recommendations"></a>特定のセキュリティに関する推奨事項


以下のセクションでは、前述の原則を実装する方法についての具体的なガイダンスを提供します。

### <a name="isolate-privileged-identities"></a>特権 ID を分離する


Azure AD では、管理者などの特権ロールを持つユーザーが、環境の他の部分の構築と管理に対する信頼の根幹になります。 セキュリティ侵害の影響を最小限に抑えるため、次のことを実装します。

* Azure AD と Microsoft 365 の特権ロールには、クラウド専用アカウントを使用します。

* Microsoft 365 と Azure AD を管理するための特権アクセス用に、[特権アクセス デバイス](/security/compass/privileged-access-devices#device-roles-and-profiles)をデプロイします。

*  特権ロールを持つすべての人間のアカウントへの Just-In-Time (JIT) アクセス用に [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) をデプロイします。 ロールのアクティブ化に強力な認証が必要であるようにします。

* [必要なタスクを実行するために必要な最小限の特権](../roles/delegate-by-task.md)を許可する管理者ロールを提供します。

* 同時に委任と複数のロールが含まれる、より充実したロール割り当てエクスペリエンスを有効にするには、Azure AD セキュリティ グループまたは Microsoft 365 グループを使用することを検討します。 これらのグループは、*クラウド グループ* と総称されます。 また、[ロールベースのアクセス制御を有効にします](../roles/groups-assign-role.md)。 [管理単位](../roles/administrative-units.md)を使用して、ロールの範囲を組織の一部に限定することができます。

* [緊急アクセス用アカウント](../roles/security-emergency-access.md)をデプロイします。 資格情報の保存にオンプレミスのパスワード コンテナーを *使わない* ようにしてください。

詳細については、[特権アクセスのセキュリティ保護](/security/compass/overview)に関するページを参照してください。 [Azure AD の管理者向けのセキュリティで保護されたアクセス プラクティス](../roles/security-planning.md)に関するページも参照してください。

### <a name="use-cloud-authentication"></a>クラウド認証を使用する 

資格情報は、主要な攻撃ベクトルです。 資格情報のセキュリティ保護を高めるには、次の方法を実装します。

* [パスワードなしの認証をデプロイします](../authentication/howto-authentication-passwordless-deployment.md)。 パスワードのない資格情報をデプロイすることで、パスワードの使用を可能な限り減らします。 これらの資格情報は、クラウド内でネイティブに管理および検証されます。 以下の認証方法から選択します。

   * [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Microsoft Authenticator アプリ](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 セキュリティ キー](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [多要素認証をデプロイします](../authentication/howto-mfa-getstarted.md)。 [Azure AD 多要素認証を使用して複数の強力な資格情報](../fundamentals/resilience-in-credentials.md)をプロビジョニングします。 このようにすると、クラウド リソースへのアクセスには、操作可能なオンプレミスのパスワードに加えて、Azure AD で管理される資格情報が必要になります。 詳細については、[Azure AD を使用して回復性があるアクセス制御管理戦略を作成する](./resilience-overview.md)方法に関するページを参照してください。

### <a name="limitations-and-tradeoffs"></a>制限事項とトレードオフ

* ハイブリッド アカウントのパスワードの管理には、パスワード保護エージェントやパスワード ライトバック エージェントなどのハイブリッド コンポーネントが必要です。 オンプレミスのインフラストラクチャが侵害された場合、攻撃者はこれらのエージェントが存在するコンピューターを制御できます。 この脆弱性によってクラウド インフラストラクチャが侵害されることはありません。 ただし、クラウド アカウントでは、これらのコンポーネントがオンプレミスの侵害から保護されません。

*  Active Directory から同期されるオンプレミスのアカウントは Azure AD で期限切れにならないようにマークされます。 この設定は通常、オンプレミスの Active Directory パスワード設定によって軽減されます。 ただし、Active Directory のオンプレミスのインスタンスが侵害され、同期が無効になっている場合は、[EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) オプションを設定して、パスワードを強制的に変更する必要があります。

## <a name="provision-user-access-from-the-cloud"></a>クラウドからのユーザー アクセスをプロビジョニングする

*プロビジョニング* とは、アプリケーションまたは ID プロバイダーにユーザー アカウントとグループを作成することです。

![プロビジョニング アーキテクチャの図。](media/protect-m365/protect-m365-provision.png)

次のプロビジョニング方法をお勧めします。

* **クラウド HR アプリから Azure AD にプロビジョニングする**: このプロビジョニングにより、クラウド HR アプリから Azure AD への Joiner-Mover-Leaver サイクルを中断することなく、オンプレミスの侵害を分離することができます。

* **クラウド アプリケーション:** 可能であれば、オンプレミスのプロビジョニング ソリューションではなく、[Azure AD アプリのプロビジョニング](../app-provisioning/user-provisioning.md)をデプロイします。 この方法では、オンプレミスの侵害の悪意のあるハッカー プロファイルの影響を受けないように、サービスとしてのソフトウェア (SaaS) アプリの一部を保護します。 

* **外部 ID**: [Azure AD B2B Collaboration](../external-identities/what-is-b2b.md) を使用します。
    この方法により、パートナー、顧客、サプライヤーとの外部コラボレーションのための、オンプレミス アカウントでの依存関係が減ります。 他の ID プロバイダーとの直接フェデレーションを慎重に評価します。 次の方法で B2B ゲスト アカウントを制限することをお勧めします。

   *  ディレクトリ内の参照グループおよびその他のプロパティへのゲスト アクセスを制限します。 外部コラボレーション設定を使用して、ゲストが所属していないグループを読み取る機能を制限します。 

    *   Azure portal へのアクセスをブロックします。 まれに必要となる例外を作成できます。  すべてのゲストと外部ユーザーを含む条件付きアクセス ポリシーを作成します。 その後、[アクセスをブロックするポリシーを実装します](../../role-based-access-control/conditional-access-azure-management.md)。 

* **切断されたフォレスト:** [Azure AD クラウド プロビジョニング](../cloud-sync/what-is-cloud-sync.md)を使用します。 この方法により、切断されたフォレストに接続できるようになり、オンプレミスの侵害の影響を広げるおそれがあるフォレスト間の接続や信頼関係を確立する必要がなくなります。 
 
### <a name="limitations-and-tradeoffs"></a>制限事項とトレードオフ

ハイブリッド アカウントのプロビジョニングに使用する場合、クラウド HR システムからの Azure AD は、オンプレミスの同期に依存して、Active Directory から Azure AD へのデータ フローを完了します。 同期が中断された場合、新しい従業員レコードを Azure AD で利用できなくなります。

## <a name="use-cloud-groups-for-collaboration-and-access"></a>コラボレーションとアクセスにクラウド グループを使用する

クラウド グループを使用すると、オンプレミス インフラストラクチャからのコラボレーションとアクセスを切り離すことができます。

* **コラボレーション**: 最新のコラボレーションのために Microsoft 365 グループと Microsoft Teams を使用します。 オンプレミスの配布リストの使用を停止し、[Outlook の Microsoft 365 グループに配布リストをアップグレードします](/office365/admin/manage/upgrade-distribution-lists)。

* **アクセス**:Azure AD セキュリティ グループまたは Microsoft 365 グループを使用して、Azure AD 内のアプリケーションへのアクセスを承認します。
* **Office 365 のライセンス**: グループベースのライセンスを使用し、クラウド専用グループを使用して Office 365 にプロビジョニングします。 この方法により、グループ メンバーシップの制御がオンプレミスのインフラストラクチャから切り離されます。

オンプレミスの侵害でメンバーシップが引き継がれるのを避けるため、アクセスに使用されるグループの所有者は、特権 ID と見なされる必要があります。
引き継ぎには、オンプレミスでのグループ メンバーシップの直接操作、または Microsoft 365 での動的グループ メンバーシップに影響する可能性があるオンプレミスの属性の操作が含まれます。

## <a name="manage-devices-from-the-cloud"></a>クラウドからデバイスを管理する


Azure AD の機能を使用して、デバイスを安全に管理します。

-   **Windows 10 ワークステーションを使用する**: MDM ポリシーを使用して、[Azure AD Join を使用したデバイスをデプロイ](../devices/azureadjoin-plan.md)します。 完全に自動化されたプロビジョニング エクスペリエンスを実現するには、[Windows Autopilot](/mem/autopilot/windows-autopilot) を有効にします。

    -   Windows 8.1 以前を実行しているマシンを廃止します。

    -   サーバー OS マシンをワークステーションとしてデプロイしないでください。

    -   すべてのデバイス管理ワークロードの権限のソースとして [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) を使用します。

-   [**特権アクセス デバイスをデプロイする**](/security/compass/privileged-access-devices#device-roles-and-profiles): 特権アクセスを使用して、Microsoft 365 と Azure AD を管理します。

## <a name="workloads-applications-and-resources"></a>ワークロード、アプリケーション、リソース 

-   **オンプレミスのシングル サインオン (SSO) システム** 

    オンプレミスのフェデレーションと Web アクセス管理インフラストラクチャを廃止します。 Azure AD を使用するようにアプリケーションを構成します。  

-   **最新の認証プロトコルをサポートする SaaS および基幹業務 (LOB) アプリケーション** 

    [SSO に Azure AD を使用します](../manage-apps/what-is-single-sign-on.md)。 認証に Azure AD を使用するよう構成されているアプリが多いほど、オンプレミスの侵害のリスクが小さくなります。


* **レガシ アプリケーション** 

   * 先進認証がサポートされていないレガシ アプリケーションに対して、認証、認可、およびリモート アクセスを有効にすることができます。 [Azure AD アプリケーション プロキシ](../manage-apps/application-proxy.md)を使用します。 また、[セキュリティで保護されたハイブリッド アクセス パートナー統合](../manage-apps/secure-hybrid-access.md)を使用して、ネットワークまたはアプリケーションの配信コントローラー ソリューションによって有効にすることもできます。   

   * 先進認証をサポートしている VPN ベンダーを選択します。 その認証を Azure AD と統合します。 オンプレミスの侵害では、Azure AD を使用して VPN を無効にすることにより、アクセスを無効にしたりブロックしたりすることができます。

*  **アプリケーションとワークロード サーバー**

   * サーバーを必要とするアプリケーションまたはリソースを、Azure サービスとしてのインフラストラクチャ (IaaS) に移行できます。 [Azure AD Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) を使用して、Active Directory のオンプレミスのインスタンスでの信頼と依存関係を切り離します。 この分離を実現するには、Azure AD DS に使用される仮想ネットワークが、企業ネットワークに接続されていないことを確認してください。

   * [資格情報の階層化](/security/compass/privileged-access-access-model#ADATM_BM)に関するガイダンスに従ってください。 通常、アプリケーション サーバーは階層 1 の資産と見なされます。

## <a name="conditional-access-policies"></a>条件付きアクセス ポリシー

Azure AD の条件付きアクセスを使用して、信号を解釈し、それを使用して認証の決定を行います。 詳細については、[条件付きアクセスのデプロイ計画](../conditional-access/plan-conditional-access.md)に関するページを参照してください。

* 可能な場合は常に、条件付きアクセスを使用して、[レガシ認証プロトコルをブロック](../conditional-access/howto-conditional-access-policy-block-legacy.md)します。 また、アプリケーション固有の構成を使用して、アプリケーション レベルでレガシ認証プロトコルを無効にします。

   詳細については、[レガシ認証プロトコル](../fundamentals/auth-sync-overview.md)に関するページを参照してください。 または、[Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) と [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant) に固有の詳細を参照してください。

* 推奨される [ID とデバイスのアクセス構成](/microsoft-365/security/office-365-security/identity-access-policies)を実装します。

* 条件付きアクセスが含まれないバージョンの Azure AD を使用している場合は、[Azure AD のセキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md)を使用していることを確認します。

   Azure AD の機能のライセンスに関する詳細については、[Azure AD 価格ガイド](https://azure.microsoft.com/pricing/details/active-directory/)に関するページを参照してください。

## <a name="monitor"></a>モニター 

オンプレミスの侵害から Microsoft 365 を保護するように環境を構成したら、環境を[予防的に監視](../reports-monitoring/overview-monitoring.md)します。
### <a name="scenarios-to-monitor"></a>監視するシナリオ

組織に固有のシナリオに加えて、次の主要なシナリオを監視します。 たとえば、ビジネス クリティカルなアプリケーションとリソースへのアクセスを、予防的に監視する必要があります。

* **Suspicious activity** 

    すべての [Azure AD リスク イベント](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation)で、疑わしいアクティビティを監視します。 [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) は、Azure Security Center とネイティブに統合されています。

    場所ベースのシグナルで多くのノイズが検出されないよう、ネットワークの[ネームド ロケーション](../reports-monitoring/quickstart-configure-named-locations.md)を定義します。 
*  **ユーザー/エンティティ行動分析 (UEBA) のアラート** 

    UEBA を使用して、異常検出に関する分析情報を取得します。
    * Microsoft Cloud App Security (MCAS) により、[クラウドでの UEBA](/cloud-app-security/tutorial-ueba) が提供されます。

    * [Azure Advanced Threat Protection (ATP) からオンプレミスの UEBA を統合する](/defender-for-identity/install-step2)ことができます。 MCAS により、Azure AD Identity Protection からのシグナルが読み取られます。 

* **緊急アクセス アカウントのアクティビティ** 

    [緊急アクセス アカウント](../roles/security-emergency-access.md)を使用するすべてのアクセスを監視します。 調査用のアラートを作成します。 この監視には次のものを含める必要があります。 

   * サインイン。 

   * 資格情報の管理。 

   * グループ メンバーシップのすべての更新。 

   * アプリケーションの割り当て。 
* **特権ロールのアクティビティ**

    [Azure AD Privileged Identity Management (PIM) によって生成されるセキュリティ アラート](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts)を構成して確認します。
    ユーザーが直接割り当てられたときに常にアラートを生成することで、PIM の外部での特権ロールの直接割り当てを監視します。

* **Azure AD のテナント全体の構成**

    テナント全体の構成が変更されたら常に、システムでアラートが生成されるようにする必要があります。 このような変更には以下のものが含まれますが、これらだけではありません。

  *  更新されたカスタム ドメイン。  

  * 許可リストとブロックリストに対する Azure AD B2B の変更。

  * 許可された ID プロバイダー (直接フェデレーションまたはソーシャル サインインによる SAML ID プロバイダー) に対する Azure AD B2B の変更。  

  * 条件付きアクセスまたはリスク ポリシーの変更。 

* **アプリケーション オブジェクトとサービス プリンシパル オブジェクト**
    
   * 条件付きアクセスポリシーが必要になる可能性がある新しいアプリケーションまたはサービス プリンシパル。 

   * サービス プリンシパルに追加された資格情報。
   * アプリケーションの同意アクティビティ。 

* **カスタム ロール**
   * カスタム ロールの定義の更新。 

   * 新規作成されたカスタム ロール。 

### <a name="log-management"></a>ログの管理

一貫したツール セットを容易にするために、ログのストレージと保持の戦略、設計、および実装を定義します。 たとえば、Azure Sentinel、一般的なクエリ、調査やフォレンジックのプレイブックなどのセキュリティ情報イベント管理 (SIEM) システムを検討できます。

* **Azure AD ログ**: 診断、ログの保持、SIEM インジェストなどの設定のベスト プラクティスに常に従って、生成されたログとシグナルを取り込みます。 

    ログ戦略には、次の Azure AD ログを含める必要があります。
   * サインイン アクティビティ 

   * 監査ログ 

   * リスク イベント 

    Azure AD により、サインイン アクティビティ ログと監査ログのための [Azure Monitor の統合](../reports-monitoring/concept-activity-logs-azure-monitor.md)が提供されます。 リスク イベントは、[Microsoft Graph API](/graph/api/resources/identityriskevent) を使用して取り込むことができます。 [Azure Monitor ログに Azure AD ログをストリーム配信する](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)ことができます。

* **ハイブリッド インフラストラクチャの OS セキュリティ ログ**: すべてのハイブリッド ID インフラストラクチャ OS ログは、外部からのアクセスの影響があるため、アーカイブし、階層 0 システムとして注意深く監視する必要があります。 次の要素が含まれます。 

   *  Azure AD Connect という単一のソリューションに代わりました。 [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) をデプロイし、ID の同期を監視する必要があります。

   *  アプリケーション プロキシ エージェント 


   * パスワード ライトバック エージェント 

   * パスワード保護ゲートウェイ マシン  

   * Azure AD 多要素認証 RADIUS 拡張機能を持つネットワーク ポリシーサーバー (NPS) 

## <a name="next-steps"></a>次のステップ
* [Azure AD を使用して ID およびアクセス管理の回復性を強化する](resilience-overview.md)

* [リソースへの外部アクセスをセキュリティで保護する](secure-external-access-resources.md) 
* [すべてのアプリを Azure AD と統合する](five-steps-to-full-application-integration-with-azure-ad.md)