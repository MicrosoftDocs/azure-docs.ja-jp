---
title: Windows Virtual Desktop 用の Azure セキュリティ ベースライン
description: Windows Virtual Desktop セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 393495dabb77a5d177c97d37313433bb00ce5a36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726752"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Windows Virtual Desktop 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 2.0](../security/benchmarks/overview.md) のガイダンスが Windows Virtual Desktop に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Windows Virtual Desktop に適用できる関連ガイダンスによって定義されています。 Windows Virtual Desktop に適用できない **制御** は、除外されています。

Windows Virtual Desktop サービスには、このサービス自体、マルチセッション仮想 SKU 用の Windows 10 Enterprise、FSLogix が含まれています。 FSLogix のセキュリティに関する推奨事項については、[ストレージ用のセキュリティ ベースライン](../storage/common/security-baseline.md)に関するページをご覧ください。 このサービスでは仮想マシン上でエージェントが実行されますが、お客様は仮想マシンを完全に制御できるため、[コンピューティングのセキュリティに関する推奨事項](../virtual-machines/windows/security-baseline.md)に従ってください

Windows Virtual Desktop を Azure セキュリティ ベンチマークに完全にマップする方法については、[完全な Windows Virtual Desktop セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)をご覧ください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-controls-v2-network-security.md)」を参照してください。*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 内部トラフィック用のセキュリティを実装する

**ガイダンス**: Windows Virtual Desktop に登録する仮想マシンをデプロイする場合は、仮想ネットワークを作成するか、既存のものを使用する必要があります。 すべての Azure 仮想ネットワークが、ビジネス リスクに合わせたエンタープライズ セグメント化の原則に従っていることを確認します。 組織のリスクが高くなる可能性があるシステムは、独自の仮想ネットワーク内に隔離し、ネットワーク セキュリティ グループまたは Azure Firewall で十分に保護する必要があります。

Azure Security Center のアダプティブ ネットワークのセキュリティ強化機能を使用して、外部ネットワーク トラフィック ルールへの参照によってポートとソース IP を制限するネットワーク セキュリティ グループの構成を推奨します。

アプリケーションとエンタープライズのセグメント化戦略に基づき、ネットワーク セキュリティ グループの規則に基づいて、内部リソース間のトラフィックを制限または許可します。 明確に定義された特定のアプリケーション (3 層アプリなど) については、これは高度にセキュリティで保護された "既定で拒否、例外による許可" の方法になります。 相互に対話する多くのアプリケーションとエンドポイントがある場合、これが適切にスケーリングされない可能性があります。 また、多数のエンタープライズ セグメントまたはスポーク (ハブ/スポーク トポロジにおいて) で中央管理が必要な環境では、Azure Firewall を使用することもできます

(Windows Virtual Desktop の一部である) 仮想マシンに関連付けられているネットワーク セキュリティ グループについては、特定のエンドポイントへの送信トラフィックを許可する必要があります。 

- [Windows Virtual Desktop でアクセスを許可する必要がある URL を確認する](safe-url-list.md)

- [Azure Security Center でのアダプティブ ネットワークのセキュリティ強化機能](../security-center/security-center-adaptive-network-hardening.md) 

- [Windows Virtual Desktop 用の Azure Firewall](../firewall/protect-windows-virtual-desktop.md)

- [セキュリティ規則を使用してネットワーク セキュリティ グループを作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Azure Firewall をデプロイして構成する方法](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: プライベート ネットワークをまとめて接続する

**ガイダンス**: Azure ExpressRoute または Azure 仮想プライベート ネットワークを使用して、コロケーション環境内で Azure のデータセンターとオンプレミスのインフラストラクチャ間のプライベート接続を作成できます。 ExpressRoute 接続はパブリック インターネットを経由しないので、一般的なインターネット接続と比べて信頼性が高く、高速で、待ち時間も短くなります。 

ポイント対サイトおよびサイト間仮想プライベート ネットワークでは、仮想プライベート ネットワークのオプションと Azure ExpressRoute の任意の組み合わせを使用して、オンプレミスのデバイスやネットワークを仮想ネットワークに接続できます。

Azure で 2 つ以上の仮想ネットワークを接続するには、Virtual Network ピアリングを使用します。 ピアリングされた仮想ネットワーク間のネットワーク トラフィックはプライベートであり、Azure のバックボーン ネットワーク上に留まります。

- [ExpressRoute 接続モデルとは](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN の概要](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 外部ネットワーク攻撃からアプリケーションやサービスを保護する

**ガイダンス**: Azure Firewall を使用して、インターネットや他の外部の場所からの、悪意がある可能性のあるトラフィックから、アプリケーションやサービスを保護します。 分散型サービス拒否攻撃、アプリ固有の攻撃、未承諾で悪意の可能性があるインターネット トラフィックなど、外部ネットワークからの攻撃に対して Windows Virtual Desktop リソースを保護します。 Azure 仮想ネットワークで DDoS 標準保護を有効にすることで、分散型サービス拒否攻撃から資産を保護します。 Azure Security Center を使用して、ネットワーク関連のリスクに関する構成ミスのリスクを検出します。

Windows Virtual Desktop は Web アプリを実行するためのものではなく、Web アプリを対象とする外部ネットワーク攻撃からこれを保護するために追加で設定を構成したり、追加のネットワーク サービスを配置したりする必要はありません。

- [Azure Firewall のドキュメント](../firewall/index.yml)

- [Azure portal を使用した Azure DDoS Protection Standard の管理](../ddos-protection/manage-ddos-protection.md) 

- [Azure Security Center の推奨事項](../security-center/recommendations-reference.md#networking-recommendations)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: 侵入検出および侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: Azure Firewall で脅威インテリジェンスベースのフィルター処理を使用して、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックに対してアラートを送信し、必要に応じてトラフィックをブロックします。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。 ペイロード検査が必要な場合は、サードパーティの侵入検知または防止ソリューションを Azure Marketplace からデプロイできます。 

侵入検知または防止ソリューションの使用に関して規制やその他の要件がある場合は、そのソリューションがセキュリティ情報イベント管理 (SIEM) ソリューションに高品質のアラートを提供するように常に調整されていることを確認します。

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace にはサードパーティの IDS 機能が含まれる](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender の ATP EDR 機能](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: ネットワーク セキュリティ規則を簡略化する

**ガイダンス**: Windows Virtual Desktop リソース用に構成されたネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、Azure 仮想ネットワーク サービス タグを使用します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 サービス タグ名の指定 (例:WindowsVirtualDesktop) を規則の適切なソースまたは宛先フィールドで行うと、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

- [サービス タグとその使用方法の概要](../virtual-network/service-tags-overview.md)

- [Windows Virtual Desktop サービス タグの対象について詳しくは、こちらをご覧ください](safe-url-list.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="identity-management"></a>ID 管理

*詳細については、[Azure セキュリティ ベンチマークの「ID 管理](../security/benchmarks/security-controls-v2-identity-management.md).* 」を参照してください。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1:Azure Active Directory を中央 ID および認証システムとして標準化する

**ガイダンス**: Windows Virtual Desktop では、Azure Active Directory (Azure AD) を既定の ID およびアクセス管理サービスとして使用します。 Azure AD を標準化して、次のリソースでの組織の ID とアクセス管理を統制する必要があります。

- Azure portal、Azure Storage、Azure 仮想マシン (Linux と Windows)、Azure Key Vault、PaaS、SaaS アプリケーションなどの Microsoft Cloud リソース。

- Azure 上のアプリケーションや企業ネットワーク リソースなどの組織のリソース。

Azure AD を保護することは、組織のクラウド セキュリティ プラクティスの中で高い優先順位を持つ必要があります。 Azure AD では、Microsoft のベスト プラクティスの推奨事項を基準にした、お客様の ID セキュリティ体制を評価するために役立つ ID セキュリティ スコアが提供されます。 スコアを使用して、構成がベスト プラクティスの推奨事項とどの程度一致しているかを測定し、セキュリティ体制を強化します。

Azure AD では、外部 ID もサポートされるため、Microsoft アカウントを持たないユーザーが外部 ID を使用してアプリやリソースにサインインできます。

- [Azure AD のテナント](../active-directory/develop/single-and-multi-tenant-apps.md)

- [アプリケーションに外部 ID プロバイダーを使用する](../active-directory/external-identities/identity-providers.md)

- [Azure AD の ID セキュリティ スコアとは](../active-directory/fundamentals/identity-secure-score.md)

- [Windows Virtual Desktop を操作するために必要な特定のロール](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2:アプリケーション ID を安全かつ自動的に管理する

**ガイダンス**: Windows Virtual Desktop では、サービスや自動化などの人間以外のアカウントのために Azure マネージド ID がサポートされています。 リソースにアクセスしたり実行したりするためのより強力な人間のアカウントを作成するのではなく、Azure マネージド ID 機能を使用することをお勧めします。 

Windows Virtual Desktop では、証明書の資格情報を使用してサービス プリンシパルを構成し、クライアント シークレットにフォールバックするため、Azure Active Directory (Azure AD) を使用してリソース レベルのアクセス許可が制限されたサービス プリンシパルを作成することをお勧めします。 どちらの場合も、Azure Key Vault を Azure マネージド ID と組み合わせて使用し、ランタイム環境 (Azure 関数など) でキー コンテナーから資格情報を取得できるようにすることができます。

- [Azure リソースのマネージド ID をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure サービス プリンシパル](/powershell/azure/create-azure-service-principal-azureps) 

- [証明書を使用してサービス プリンシパルを作成する](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Azure Key Vault を使用してセキュリティ プリンシパルを登録する](../key-vault/general/authentication.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3:アプリケーションのアクセスに Azure AD シングル サインオン (SSO) を使用する

**ガイダンス**: Windows Virtual Desktop では、Azure Active Directory を使用して、Azure リソース、クラウド アプリ、オンプレミスのアプリに ID およびアクセス管理を提供します。 これには、従業員などの企業 ID だけでなく、パートナー、ベンダー、サプライヤーなどの外部 ID も含まれます。 これにより、オンプレミスおよびクラウド内の組織のデータとリソースへのアクセスを管理し、セキュリティで保護するためのシングルサインオン (SSO) が可能になります。 シームレスで安全なアクセスを実現し、可視性と制御性を高めるため、すべてのユーザー、アプリ、デバイスを Azure AD に接続します。

- [Azure AD を使用したアプリケーションの SSO について理解する](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4:すべての Azure Active Directory ベースのアクセスに強力な認証制御を使用する 

**ガイダンス**: Windows Virtual Desktop では、多要素認証による強力な認証制御と、強力なパスワードレス手法をサポートしている Azure Active Directory (Azure AD) を使用します。

- 多要素認証 - Azure AD Multi-Factor Authentication を有効にして、多要素認証の設定におけるいくつかのベスト プラクティスについて、Azure Security Center の ID およびアクセス管理の推奨事項に従います。 多要素認証は、選択されたすべてのユーザーに対して、またはサインインの条件とリスク要因に基づいてユーザーごとのレベルで適用できます。

- パスワードレス認証 - 3 つのパスワードレス認証オプションを使用できます。Windows Hello for Business、Microsoft Authenticator アプリ、スマート カードなどのオンプレミスの認証方法。

Windows Virtual Desktop では、ベースライン パスワード ポリシーに従うクラウド専用アカウント (Azure で直接作成されたユーザー アカウント) やハイブリッド アカウント (オンプレミスのパスワード ポリシーに従うオンプレミスの Azure AD のユーザー アカウント) など、従来のパスワードベースの認証がサポートされています。 パスワード ベースの認証を使用すると、Azure AD により、ユーザーが推測されやすいパスワードを設定できないようにするパスワード保護機能が提供されます。 Microsoft では、テレメトリに基づいて更新される禁止されたパスワードのグローバル リストが提供されており、お客様はニーズ (ブランドや文化の参照など) に基づいてリストを補強することができます。 このパスワード保護は、クラウド専用アカウントとハイブリッド アカウントに使用できます。

パスワード資格情報だけに基づく認証は、一般的な攻撃方法の影響を受けやすいものです。 セキュリティを強化するには、多要素認証や強力なパスワード ポリシーなどの強力な認証を使用します。 サードパーティ製のアプリや Marketplace サービスで既定のパスワードが使用されている可能性がある場合は、サービスの初期セットアップ時にそれらを変更する必要があります。

管理者と特権ユーザーについて、高いレベルの強力な認証方法が使用されていることを確認し、その後、適切で強力な認証ポリシーを他のユーザーにロールアウトします。

- [Azure Active Directory のパスワードレス認証オプションの概要](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Azure AD の規定のパスワード ポリシー](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Azure Active Directory パスワード保護を使用して不適切なパスワードを排除する](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5:アカウントの異常を監視してアラートを出す

**ガイダンス**: Windows Virtual Desktop は、以下のデータ ソースを提供する Azure Active Directory (Azure AD) と統合されています。

- サインイン - サインイン レポートでは、マネージド アプリの使用状況とユーザー サインイン アクティビティに関する情報が提供されます。

- 監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。

- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によってサインインが試行された可能性があることを示す指標です。

- リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

これらのデータ ソースは、Azure Monitor、Azure Sentinel、またはサードパーティのセキュリティ情報イベント管理 (SIEM) システムと統合できます。 Azure Security Center で、認証試行の失敗回数が多すぎるなど、サブスクリプションにおける特定の不審なアクティビティや、非推奨のアカウントに対してアラートを生成することもできます。 セキュリティ ソリューションである Azure Advanced Threat Protection (ATP) では、Active Directory シグナルを使用することで、高度な脅威、セキュリティ侵害を受けた ID、および悪意のある内部関係者のアクションを特定、検出、および調査できます。

- [Azure AD でアクティビティ レポートを監査する](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center の脅威インテリジェンス保護モジュールでのアラート](../security-center/alerts-reference.md)

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6:条件に基づいて Azure リソースへのアクセスを制限する

**ガイダンス**: Windows Virtual Desktop では、ユーザー定義の条件に基づいてよりきめ細かなアクセス制御を行うために、Azure Active Directory (Azure AD) を使用した条件付きアクセスがサポートされています。 たとえば、特定の IP 範囲からのユーザー ログインでは、アクセスのために多要素認証を使用する必要があります。 

さらに、さまざまなユース ケースに対してきめ細かな認証セッション管理ポリシーを使用することもできます。

- [Azure での条件付きアクセスの概要](../active-directory/conditional-access/overview.md) 

- [一般的な条件付きアクセス ポリシー](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [条件付きアクセスを使用して認証セッション管理を構成する](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [Windows Virtual Desktop に固有の条件付きアクセスの設定情報については、こちらをご覧ください](set-up-mfa.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="privileged-access"></a>特権アクセス

*詳細については、[Azure セキュリティ ベンチマークの「特権アクセス](../security/benchmarks/security-controls-v2-privileged-access.md)」を参照してください。*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2:ビジネス クリティカルなシステムへの管理アクセスを制限する

**ガイダンス**: Windows Virtual Desktop では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、ビジネス クリティカルなシステムへのアクセスを分離します。 また、ビジネス クリティカルなシステムにエージェントがインストールされている Active Directory ドメイン コントローラー、セキュリティ ツール、システム管理ツールなど、ビジネス クリティカルなアクセスへの管理アクセス権を持つ管理、ID、およびセキュリティ システムへのアクセスもご自身で必ず制限するようにしてください。 これらの管理およびセキュリティ システムを侵害した攻撃者は、それらをすぐに悪用してビジネス クリティカルな資産を侵害する可能性があります。

一貫したアクセス制御を確保するため、自分の企業のセグメント化戦略にすべての種類のアクセス制御を合わせる必要があります。

- [Azure のコンポーネントと参照モデル](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [管理グループ アクセス](../governance/management-groups/overview.md#management-group-access) 

- [Azure サブスクリプション管理者](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Windows Virtual Desktop を管理するために必要な最少管理者権限](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3:ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Windows Virtual Desktop では、Azure Active Directory (Azure AD) アカウントを使用してそのリソースを管理し、ユーザー アカウントとアクセス割り当てを定期的にレビューして、アカウントとそのアクセスが有効であることを確認します。

Azure AD アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリへのアクセス、およびロールの割り当てをレビューします。 Azure AD レポートによって、古いアカウントの検出に役立つログが提供されます。

さらに、過剰な数の管理者アカウントが作成された場合にアラートを発行したり、古い管理者アカウントや不適切に構成されている管理者アカウントを特定するように Azure Privileged Identity Management を構成することもできます。

一部の Azure サービスでは、Azure AD で管理されていないローカル ユーザーとロールがサポートされています。 これらのユーザーは個別に管理する必要があります。

- [Windows Virtual Desktop の組み込みロール](rbac.md)

- [Privileged Identity Management (PIM) で Azure リソース ロールのアクセス レビューを作成する](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4:Azure AD で緊急アクセスを設定する

**ガイダンス**: Windows Virtual Desktop では、Azure Active Directory (Azure AD) を使用して、そのリソースを管理します。 Azure AD 組織から誤ってロックアウトされるのを防ぐために、通常の管理者アカウントを使用できない場合にアクセスするための緊急アクセス用アカウントを設定します。 緊急アクセス用アカウントは高い特権を持っており、特定の個人に割り当てることはできません。 緊急アクセス用アカウントは、通常の管理者アカウントを使うことができない "緊急事態" に制限されます。

緊急アクセス用アカウントの資格情報 (パスワード、証明書、スマート カードなど) は安全に保管し、緊急時にのみそれらを使うことを許可された個人のみに知らせる必要があります。

- [Azure AD で緊急アクセス用アカウントを管理する](../active-directory/roles/security-emergency-access.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5:エンタイトルメント管理を自動化する 

**ガイダンス**: Windows Virtual Desktop では、Azure Active Directory (Azure AD) との統合により、そのリソースを管理します。 Azure AD のエンタイトルメント管理機能を使用して、アクセスの割り当て、レビュー、有効期限など、アクセス要求のワークフローを自動化します。 さらに、2 段階または複数段階の承認もサポートされています。

- [Azure AD アクセス レビューとは](../active-directory/governance/access-reviews-overview.md) 

- [Azure AD エンタイトルメント管理とは](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6:特権アクセス ワークステーションを使用する

**ガイダンス**: セキュリティで保護されて分離されたワークステーションは、管理者、開発者、重要なサービス オペレーターなどの機密性の高い役割のセキュリティにとって非常に重要です。 管理タスクに高度にセキュリティ保護されたユーザー ワークステーションや Azure Bastion を使用します。 

Azure Active Directory (Azure AD)、Microsoft Defender Advanced Threat Protection (ATP)、または Microsoft Intune を使用して、管理タスクのためにセキュリティで保護されたマネージド ユーザー ワークステーションを展開します。 セキュリティで保護されたワークステーションを一元管理して、強力な認証、ソフトウェアとハードウェアのベースライン、制限された論理アクセスとネットワーク アクセスなどのセキュリティで保護された構成を実施できます。

- [特権アクセス ワークステーションを理解する](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [特権アクセス ワークステーションを展開する](/security/compass/privileged-access-deployment)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7:Just Enough Administration (最小限の特権の原則) に従う 

**ガイダンス**: Windows Virtual Desktop では、Azure ロールベースのアクセス制御 (RBAC) との統合により、そのリソースを管理します。 Azure RBAC を使用すると、ロールの割り当てによって Azure リソースへのアクセスを管理できます。 これらのロールを、ユーザー、グループ サービス プリンシパル、およびマネージド ID に割り当てることができます。 特定のリソースに対して定義済みの組み込みロールがあります。これらのロールは、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリまたは照会できます。 

Azure RBAC を使用してリソースに割り当てる特権は、常に、ロールで必要なものに制限する必要があります。 これは、Azure Active Directory (Azure AD) による Privileged Identity Management (PIM) の Just-in-Time (JIT) アプローチを補完するものであり、定期的に見直す必要があります。

さらに、組み込みのロールを使用してアクセス許可を割り当て、カスタム ロールは必要な場合にのみ作成します。

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md) 

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md) 

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

- [Windows Virtual Desktop の組み込みロール](rbac.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Microsoft サポートの承認プロセスを選択する  

**ガイダンス**: Microsoft が顧客データにアクセスする必要があるサポート シナリオでは、顧客データへのアクセス要求を確認し、承認または拒否するためのインターフェイスを提供するために、Windows Virtual Desktop によってカスタマー ロックボックスがサポートされています。

- [カスタマー ロックボックスについて](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-controls-v2-data-protection.md)」を参照してください。*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1:機密データを検出、分類、ラベル付けする

**ガイダンス**: 機密データを検出、分類、ラベル付けして、適切な制御を設計できるようにします。 これにより、組織の技術システムで機密情報が安全に保存、処理、転送されるようになります。

Azure、オンプレミス、Office 365 などの場所にある Office ドキュメントの機密情報には、Azure Information Protection (およびこれに付随するスキャン ツール) を使用します。

Azure SQL Database に格納されている情報の分類とラベル付けには、Azure SQL Information Protection を使用します。

- [Azure Information Protection を使用して機密情報をタグ付けする](/azure/information-protection/what-is-information-protection) 

- [Azure SQL Data Discovery を実装する方法](../azure-sql/database/data-discovery-and-classification-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="dp-2-protect-sensitive-data"></a>DP-2:機密データを保護する

**ガイダンス**:機密データを保護するには、Azure のロール ベースのアクセス制御 (Azure RBAC)、ネットワーク ベースのアクセス制御、(SQL などのデータベースでの暗号化など) Azure サービスの特定の制御を使用してアクセスを制限します。

一貫したアクセス制御を確保するには、自分の企業のセグメント化戦略にすべての種類のアクセス制御を合わせる必要があります。 企業のセグメント化戦略では、機密またはビジネスに重要なデータやシステムの場所からも通知される必要があります。

Microsoft では、顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Microsoft では、Azure 内の顧客データが確実にセキュリティで保護されるように、データ保護コントロールおよび機能をいくつか実装しています。

- [Azure のロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) 

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3:機密データの不正転送を監視する

**ガイダンス**:企業が可視および制御できる範囲外の場所にデータが不正に転送されるのを監視することができます。 これには通常、不正なデータ流出を示す可能性のある異常な活動 (大規模または異常な転送) の監視が含まれます。

Azure Storage と Azure SQL の両方の Advanced Threat Protection (ATP) 機能を使用すると、機密情報の不正転送を示唆する異常な情報転送のアラートを送信できます。

Azure Information Protection (AIP) には、分類およびラベル付けされた情報を監視する機能があります。

データ損失防止ソリューション (ホスト ベースのものなど) を使用すると、検出および予防コントロールを適用してデータ流出を回避できます。

- [Azure SQL ATP を有効にする](../azure-sql/database/threat-detection-overview.md) 

- [Azure Storage ATP を有効にする](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="asset-management"></a>アセット管理

*詳細については、[Azure セキュリティ ベンチマークの「アセット管理](../security/benchmarks/security-controls-v2-asset-management.md)」を参照してください。*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1:セキュリティ チームが資産のリスクを確実に可視化できるようにする

**ガイダンス**:セキュリティ チームに Azure テナントとサブスクリプションのセキュリティ閲覧者アクセス許可を付与して、セキュリティ チームが Azure Security Center を使用してセキュリティ上のリスクを監視できるようにします。 

セキュリティ リスクの監視は、セキュリティ チームの責任の構造に応じて、中央のセキュリティ チームまたはローカル チームの責任になります。 ただし、セキュリティ分析情報とリスクは、常に組織内で一元的に集計する必要があります。 

セキュリティ閲覧者のアクセス許可は、テナント全体 (ルート管理グループ) に幅広く適用することも、管理グループまたは特定のサブスクリプションにスコープ指定することもできます。 

ワークロードとサービスを可視化するには、追加のアクセス許可が必要になることがあります。 

- [セキュリティ閲覧者ロールの概要](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理グループの概要](../governance/management-groups/overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2:セキュリティ チームが資産インベントリとメタデータに確実にアクセスできるようにする

**ガイダンス**:Azure リソース、リソース グループ、サブスクリプションにタグを適用して、それらを論理的に分類できます。 各タグは、名前と値のペアで構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。

Azure 仮想マシンのインベントリを使用して、Virtual Machines 上のソフトウェアに関する情報の収集を自動化します。 ソフトウェアの名前、バージョン、発行元、および更新時刻は、Azure portal から入手できます。 インストール日やその他の情報にアクセスするには、ゲストレベルの診断を有効にし、Windows イベント ログを Log Analytics ワークスペースに取り込みます。

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center の資産インベントリ管理](../security-center/asset-inventory.md) 

- [リソースの名前付けとタグ付けの意思決定ガイド](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Azure 仮想マシンのインベントリを有効にする方法](../automation/automation-tutorial-installed-software.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3:承認された Azure サービスのみを使用する

**ガイダンス**:Azure Policy を使用して、環境内でユーザーがプロビジョニングできるサービスを監査および制限します。 Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 また、Azure Monitor を使用して、承認されていないサービスが検出されたときにアラートをトリガーするルールを作成することもできます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/built-in-policies.md#general) 

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4:アセット ライフサイクル管理のセキュリティを確保する

**ガイダンス**: 適用不可。 Windows Virtual Desktop を使用してライフサイクル管理プロセスの資産のセキュリティを確保することはできません。 影響が大きいと見なされる資産の属性とネットワーク構成を維持することは、お客様の責任です。 

お客様にて、属性とネットワーク構成の変更をキャプチャするプロセスを作成し、変更の影響を測定し、必要に応じて修復タスクを作成することをお勧めします。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6:コンピューティング リソースで承認済みのアプリケーションのみを使用する

**ガイダンス**: Azure 仮想マシンのインベントリを使用して、仮想マシン上のすべてのソフトウェアに関する情報の収集を自動化します。 ソフトウェアの名前、バージョン、発行元、および更新時刻は、Azure portal から入手できます。 インストール日やその他の情報にアクセスするには、ゲストレベルの診断を有効にし、Windows イベント ログを Log Analytics ワークスペースに取り込みます。

- [Azure 仮想マシンのインベントリを有効にする方法](../automation/automation-tutorial-installed-software.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-threat-detection"></a>ログと脅威検出

*詳細については、[Azure セキュリティ ベンチマークの「ログと脅威検出](../security/benchmarks/security-controls-v2-logging-threat-detection.md)」を参照してください。*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1:Azure リソースの脅威検出を有効にする

**ガイダンス**: Azure Security Center の組み込みの脅威検出機能を使用して、Windows Virtual Desktop リソースに対して Azure Defender (正式名: Azure Advanced Threat Protection) を有効にします。 Azure Defender for Windows Virtual Desktop では、Windows Virtual Desktop リソースに対して通常と異なる潜在的に有害なアクセスまたは悪用が試行されたことを検出するセキュリティ インテリジェンスの追加レイヤーを提供します。

Windows Virtual Desktop のすべてのログを、カスタム脅威検出を設定するために使用できるセキュリティ情報イベント管理 (SIEM) ソリューションに転送します。 潜在的な脅威や異常を検出するために、さまざまな種類の Azure 資産を監視していることを確認してください。 アナリストが選別しやすいように、質の高いアラートを取得して誤検知を減らすことに専念します。 アラートは、ログ データ、エージェント、その他のデータを元に生成できます。

- [Azure Security Center での脅威の防止](../security-center/azure-defender.md) 

- [Azure Security Center セキュリティ アラート リファレンス ガイド](../security-center/alerts-reference.md)

- [脅威を検出するためのカスタム分析規則を作成する](../sentinel/tutorial-detect-threats-custom.md) 

- [Azure Sentinel を使用したサイバー脅威インテリジェンス](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2:Azure ID とアクセスの管理のために脅威検出を有効にする

**ガイダンス**: Azure Active Directory (Azure AD) では、次のユーザー ログが記録されます。これらは、Azure AD レポートで確認できるほか、より高度な監視と分析のユース ケースの場合は、Azure Monitor、Azure Sentinel、その他のセキュリティ情報イベント管理 (SIEM) または監視ツールと統合できます。

- サインイン - サインイン レポートでは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報が提供されます。

- 監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。

- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によってサインインが試行された可能性があることを示す指標です。

- リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

Azure Security Center で、認証試行の失敗回数が多すぎるなど、サブスクリプションにおける特定の不審なアクティビティや、使用すべきでないアカウントに対してアラートを生成することもできます。 Azure Security Center の脅威保護モジュールでは、基本的なセキュリティ検疫監視に加えて、個々の Azure コンピューティング リソース (仮想マシン、コンテナー、アプリ サービス)、データ リソース (SQL DB、ストレージ)、Azure サービス レイヤーから、さらに詳しいセキュリティ アラートを収集することもできます。 この機能を使用することにより、個々のリソース内でアカウントの異常を確認できます。

- [Azure Active Directory の監査アクティビティ レポート](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Identity Protection を有効にする](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Security Center での脅威の防止](../security-center/azure-defender.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3:Azure ネットワーク アクティビティのログ記録を有効にする

**ガイダンス**: Windows Virtual Desktop では、ドメインネーム サービス (DNS) クエリ ログの生成または処理を行いません。 しかし、サービスに登録されているリソースによってフロー ログが生成されることがあります。

セキュリティ分析で、インシデント調査、脅威ハンティング、セキュリティ アラートの生成をサポートするために、ネットワーク セキュリティ グループのリソースおよびフロー ログ、Azure Firewall ログ、Web アプリ ファイアウォール (WAF) ログを有効にして収集します。 Azure Monitor Log Analytics ワークスペースにフロー ログを送信し、Traffic Analytics を使用して分析情報を提供できます。

- [ネットワーク セキュリティ グループのフローのログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Azure Firewall のログとメトリック](../firewall/logs-and-metrics.md) 

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md) 

- [Azure Monitor の Azure ネットワーク監視ソリューション](../azure-monitor/insights/azure-networking-analytics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4:Azure リソースのログ記録を有効にする

**ガイダンス**: 自動的に有効になるアクティビティ ログには、読み取り操作 (GET) を除く、Windows Virtual Desktop リソースに対するすべての書き込み操作 (PUT、POST、DELETE) が含まれています。 アクティビティ ログを使用すると、トラブルシューティング時にエラーを見つけたり、組織内のユーザーがリソースをどのように変更したかを監視したりできます。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure でのログ記録とログのさまざまな種類について](../azure-monitor/essentials/platform-logs-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5:セキュリティ ログの管理と分析を一元化する

**ガイダンス**:ログ記録のストレージと分析を一元化して、相関関係を有効にします。 ログ ソースごとに、データ所有者、アクセス ガイダンス、ストレージの場所、データの処理とアクセスに使用するツール、データ保持の要件を割り当てたことを確認します。

Azure アクティビティ ログを一元的なログ記録に統合していることを確認します。 Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。

さらに、Azure Sentinel またはサード パーティのセキュリティ情報イベント管理 (SIEM) に対してデータを有効にしてオンボードします。 多くの組織では、頻繁に使用される "ホット" データに対しては Azure Sentinel を、使用頻度の低い "コールド" データに対しては Azure Storage を使用することを選択しています。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-controls-v2-incident-response.md)」を参照してください。*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 準備 – インシデント対応プロセスを Azure 用に更新する

**ガイダンス**:組織において、セキュリティ インシデントに対応するためのプロセスが用意されていること、Azure のこれらのプロセスが更新されていること、それらのプロセスを定期的に使用して準備されていることを確認します。

- [企業環境全体にセキュリティを実装する](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [インシデント対応のリファレンス ガイド](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 準備 – インシデント通知をセットアップする

**ガイダンス**:Azure Security Center でセキュリティ インシデントの連絡先情報を設定します。 この連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 また、インシデント対応のニーズに応じて、異なる Azure サービスでインシデント アラートと通知をカスタマイズするオプションもあります。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 検出と分析 – 高品質のアラートに基づいてインシデントを作成する

**ガイダンス**:高品質のアラートを作成し、アラートの品質を測定するプロセスがあることを確認します。 これにより、過去のインシデントから教訓を学び、アナリストに対するアラートに優先順位を付けることができるので、擬陽性に時間を無駄にすることがありません。 

高品質のアラートは、過去のインシデントからの経験、検証されたコミュニティ ソース、およびさまざまなシグナル ソースの融合と関連付けによってアラートを生成してクリーンアップするように設計されたツールに基づいて構築できます。 

Azure Security Center では、多数の Azure 資産について高品質のアラートが提供されます。 ASC データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 Azure Sentinel を使用すると、高度なアラート ルールを作成し、調査のためにインシデントを自動的に生成できます。 

エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートし、Azure リソースへのリスクを特定します。 アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートします。

- [エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 検出と分析 – インシデントを調査する

**ガイダンス**:アナリストが潜在的なインシデントを調査するときに、さまざまなデータ ソースを照会して使用し、発生した内容の完全なビューを構築できることを確認します。 キル チェーン全体で潜在的な攻撃者のアクティビティを追跡して死角を回避するには、さまざまなログを収集する必要があります。  また、他のアナリストのため、および将来において履歴が参照される場合のために、確実に分析情報と学習がキャプチャされているようにします。  

調査のためのデータ ソースには、スコープ内のサービスおよび実行中のシステムから既に収集されている一元化されたログ ソースが含まれますが、次のものも含まれます。

- ネットワーク データ - ネットワーク セキュリティ グループのフロー ログ、Azure Network Watcher、Azure Monitor を使用して、ネットワーク フロー ログやその他の分析情報をキャプチャします。 

- 実行中のシステムのスナップショット: 

    - Azure 仮想マシンのスナップショット機能を使用して、実行中のシステムのディスクのスナップショットを作成します。 

    - オペレーティング システムのネイティブ メモリ ダンプ機能を使用して、実行中のシステムのメモリのスナップショットを作成します。

    - Azure サービスのスナップショット機能またはソフトウェア独自の機能を使用して、実行中のシステムのスナップショットを作成します。

Azure Sentinel により、事実上すべてのログソースに対して広範な Data Analytics と、インシデントのライフサイクル全体を管理するためのケース管理ポータルが提供されます。 調査中のインテリジェンス情報を、追跡とレポートのためにインシデントに関連付けることができます。 

- [Windows マシンのディスクのスナップショットを作成する](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux マシンのディスクのスナップショットを作成する](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure サポートの診断情報とメモリ ダンプ コレクション](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel でインシデントを調査します](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 検出と分析 – インシデントの優先順位を付ける

**ガイダンス**:アラートの重要度と資産の機密性に基づいて、最初に注目するインシデントについてのコンテキストをアナリストに提供します。 

Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してリソースをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。 インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 包含、根絶、復旧 – インシデントの処理を自動化する

**ガイダンス**:手動による反復タスクを自動化して、応答時間を短縮し、アナリストの負担を軽減します。 手動タスクの実行には時間がかかり、各インシデントの速度が低下し、アナリストが処理できるインシデントの数が減少します。 手動タスクではアナリストの疲労も増加します。これにより、遅延が発生する人的エラーのリスクが増加し、複雑なタスクに効果的に焦点を当てるアナリストの能力が低下します。 Azure Security Center と Azure Sentinel のワークフロー自動化機能を使用して、自動的にアクションをトリガーしたり、プレイブックを実行して受信したセキュリティ アラートに応答したりします。 プレイブックにより、通知の送信、アカウントの無効化、問題のあるネットワークの特定などのアクションが実行されます。 

- [Security Center でワークフロー自動化を構成する](../security-center/workflow-automation.md)

- [Azure Security Center で脅威への自動対応を設定する](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel で脅威への自動対応を設定します](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="posture-and-vulnerability-management"></a>体制と脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「体制と脆弱性の管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)」を参照してください。*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: コンピューティング リソースにセキュリティで保護された構成を確立する

**ガイダンス**:Azure Security Center と Azure Policy を使用して、VM、コンテナーなど、すべてのコンピューティング リソースにセキュリティで保護された構成を確立します。

カスタム オペレーティング システム イメージまたは Azure Automation State Configuration を使用して、組織に必要なオペレーティング システムのセキュリティ構成を確立できます。

- [Azure Security Center の推奨事項を監視する方法](../security-center/security-center-recommendations.md) 

- [Azure Automation State Configuration の概要](../automation/automation-dsc-overview.md) 

- [Windows Virtual Desktop の環境](environment-setup.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: コンピューティング リソースに対するセキュリティで保護された構成を維持する

**ガイダンス**: Azure Security Center と Azure Policy を使用して、仮想マシン、コンテナーなど、Azure コンピューティング リソースの構成上のリスクを定期的に評価し、修復します。 さらに、Azure Resource Manager テンプレート、カスタム オペレーティング システム イメージ、または Azure Automation State Configuration を使用して、組織に必要なオペレーティング システムのセキュリティ構成を維持できます。 Microsoft 仮想マシン テンプレートと Azure Automation State Configuration を組み合わせると、セキュリティ要件を満たし、それを維持するために役立ちます。

Microsoft によって公開された Azure Marketplace の仮想マシン イメージは、Microsoft によって管理および維持されます。

また、Azure Security Center を使用して、コンテナー イメージの脆弱性をスキャンし、Center for Internet Security の Docker ベンチマークに照らしてコンテナー内の Docker 構成の継続的な監視を行うこともできます。 Azure Security Center の推奨事項ページを使用して、推奨事項を表示したり、問題を修復したりできます。

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../security-center/deploy-vulnerability-assessment-vm.md) 

- [ARM テンプレートから Azure 仮想マシンを作成する方法](../virtual-machines/windows/ps-template.md) 

- [Azure Automation State Configuration の概要](../automation/automation-dsc-overview.md) 

- [Security Center のコンテナーのセキュリティ](../security-center/container-security.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: カスタム オペレーティング システムとコンテナーのイメージを安全に格納する

**ガイダンス**: Windows Virtual Desktop では、お客様がオペレーティング システム イメージを管理できます。 Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、確実に承認されたユーザーのみがカスタム イメージにアクセスできます。 Azure Shared Image Gallery を使用すると、組織内のさまざまなユーザー、サービス プリンシパル、Active Directory グループに対してイメージを共有できます。 コンテナー イメージを Azure Container Registry に保存し、RBAC を使用して、承認されたユーザーのみがアクセスできるようにします。

- [Azure RBAC について](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Azure RBAC を構成する方法](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [共有イメージ ギャラリーの概要](../virtual-machines/shared-image-galleries.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6: ソフトウェアの脆弱性評価を実行する

**ガイダンス**: Windows Virtual Desktop を使用すると、独自の仮想マシンをデプロイしてサービスに登録できるだけでなく、環境内で SQL データベースを実行することもできます。

Windows Virtual Desktop では、ネットワーク デバイスと Web アプリで脆弱性評価を実行するためのサードパーティ ソリューションを使用できます。 リモート スキャンを実施する場合は、1 つの永続的な管理者アカウントを使用しないでください。 スキャン アカウントには、JIT プロビジョニングの方法論を実装することを検討してください。 スキャン アカウントの資格情報は保護と監視の対象とし、脆弱性のスキャンのためにのみ使用する必要があります。

必要に応じて、スキャン結果を一定の間隔でエクスポートし、前回のスキャンと結果を比較して、脆弱性が修復されていることを確認します。

Azure 仮想マシン (および SQL サーバー) に対する脆弱性評価の実行に関する Azure Security Center の推奨事項に従います。 Azure Security Center には、仮想マシン、コンテナー イメージ、SQL データベース用の組み込みの脆弱性スキャナーがあります。

必要に応じて、スキャン結果を一定の間隔でエクスポートし、前回のスキャンと結果を比較して、脆弱性が修復されていることを確認します。 Azure Security Center によって提案された脆弱性管理の推奨事項を使用する場合は、選択したソリューションのポータルに切り替えてスキャン データの履歴を表示できます。

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../security-center/deploy-vulnerability-assessment-vm.md) 

- [仮想マシン向けの統合された脆弱性スキャナー](../security-center/deploy-vulnerability-assessment-vm.md) 
- [SQL 脆弱性評価](../azure-sql/database/sql-vulnerability-assessment.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: ソフトウェアの脆弱性を迅速かつ自動的に修復する

**ガイダンス**: Windows Virtual Desktop では、サードパーティ製ソフトウェアを使用せず、要求もしません。 しかし、Windows Virtual Desktop を使用すれば、独自の仮想マシンをデプロイしてサービスに登録できます。

Azure Automation Update Management またはサードパーティのソリューションを使用して、最新のセキュリティ更新プログラムが Windows Server 仮想マシンにインストールされていることを確認します。 Windows 仮想マシンについては、Windows Update が有効になっていて、自動的に更新するよう設定されていることを確認します。

サードパーティ製ソフトウェアにはサードパーティの修正プログラム管理ソリューションを使用し、Configuration Manager には System Center Updates Publisher を使用します。

- [Azure で仮想マシンに対して Update Management を構成する方法](../automation/update-management/overview.md) 

- [Azure VM の更新プログラムとパッチの管理](../automation/update-management/manage-updates-for-vm.md)

- [Microsoft Endpoint Configuration Manager を Windows Virtual Desktop 向けに構成する](configure-automatic-updates.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 定期的に攻撃シミュレーションを実施する

**ガイダンス**: Windows Virtual Desktop では、お客様が Windows Virtual Desktop リソースに対して独自の侵入テストを実行することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="endpoint-security"></a>エンドポイント セキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「エンドポイントのセキュリティ](../security/benchmarks/security-controls-v2-endpoint-security.md)」を参照してください。*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1:エンドポイントでの検出と対応 (EDR) を使用する

**ガイダンス**: Windows Virtual Desktop には、エンドポイントの検出と応答 (EDR) プロセスに対する特定の機能がありません。 しかし、サービスに登録されたリソースは、エンドポイントの検出と応答機能を活用できます。 

サーバーとクライアントに対してエンドポイントの検出と応答機能を有効にし、セキュリティ情報イベント管理 (SIEM) ソリューションおよびセキュリティ運用プロセスと統合します。

Microsoft Defender の Advanced Threat Protection では、高度な脅威を防御、検出、調査し、それに対応できるように、エンタープライズ エンドポイント セキュリティ プラットフォームの一部としてエンドポイントの検出と応答 (EDR) 機能を提供します。

- [Microsoft Defender Advanced Threat Protection の概要](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Windows サーバー向けの Microsoft Defender ATP サービス](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Windows 以外のサーバー向けの Microsoft Defender ATP サービス](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [非永続的な仮想デスクトップ インフラストラクチャ向けの Microsoft Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2:一元管理された最新のマルウェア対策ソフトウェアを使用する

**ガイダンス**: リアルタイムおよび定期的なスキャンを実行できる一元管理された最新のエンドポイント マルウェア対策ソリューションを使用して、Windows Virtual Desktop を保護します。

Azure Security Center では、お使いの仮想マシンにいくつもの一般的なマルウェア対策ソリューションが使用されていることを自動的に特定し、エンドポイント保護の実行状態を報告したり、推奨事項を提供したりできます。

Azure Cloud Services 向けの Microsoft Antimalware は、Windows 仮想マシン (VM) の既定のマルウェア対策です。 さらに、データ サービス向けの Azure Security Center の脅威検出を使用して、Azure Storage アカウントにアップロードされたマルウェアを検出することもできます。

- [Cloud Services と Virtual Machines に対して Microsoft Antimalware を構成する方法](../security/fundamentals/antimalware.md) 

- [サポートされているエンドポイント保護ソリューション](../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: マルウェア対策の署名が迅速かつ一貫して更新されるようにします。

Azure Security Center の"計算とアプリ" の推奨事項に従って、すべての仮想マシンおよびコンテナーが最新の署名を備え、最新の状態であることを確認します。

Microsoft Antimalware では、既定で、最新の署名とエンジンの更新プログラムが自動的にインストールされます。

- [Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware をデプロイする方法](../security/fundamentals/antimalware.md) 

- [Azure Security Center での Endpoint Protection の評価と推奨事項](../security-center/security-center-endpoint-protection.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="backup-and-recovery"></a>バックアップと回復

*詳細については、[Azure セキュリティ ベンチマークの「バックアップと回復」](../security/benchmarks/security-controls-v2-backup-recovery.md)を参照してください。*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1:定期的な自動バックアップを保証する

**ガイダンス**: 予期しないイベントが発生した後もビジネスが継続性されるよう、システムとデータが確実にバックアップします。 これは、目標復旧時点 (RPO) と目標復旧時間 (RTO) のすべての目標から導き出されたものである必要があります。

Azure Backup を有効にし、(Azure VM、SQL Server、HANA データベースまたはファイル共有などの) バックアップ ソース、希望の頻度および保持期間を構成します。

geo 冗長ストレージ オプションを有効にして、セカンダリ リージョンにバックアップ データをレプリケートし、リージョン間での復元を使用して復旧されるように、冗長性のレベルを高くすることもできます。

- [エンタープライズ規模の事業継続とディザスター リカバリー](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Azure Backup を有効にする方法](../backup/index.yml) 

- [リージョンをまたがる復元を有効にする方法](../backup/backup-azure-arm-restore-vms.md#cross-region-restore) 

- [Windows Virtual Desktop で事業継続とディザスター リカバリー プランを設定する方法](disaster-recovery.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="br-2-encrypt-backup-data"></a>BR-2:バックアップ データを暗号化する

**ガイダンス**: 攻撃に対し、自分のバックアップが確実に保護されるようにする必要があります。 これには、機密性が失われるのを防ぐためにバックアップを暗号化することも含まれます。

通常の Azure サービスのバックアップでは、バックアップ データは Azure プラットフォーム マネージド キーを使用して自動的に暗号化されます。 バックアップに、カスタマー マネージド キーを選択することもできます。 この場合は、キー コンテナー内のこのカスタマー マネージド キーも確実にバックアップ対象にします。

バックアップおよびカスタマー マネージド キーを保護するには、Azure Backup、Azure Key Vault、またはその他のリソースでロールベースのアクセス制御を使用します。 さらに、バックアップが変更または削除される前に多要素認証を求める、高度なセキュリティ機能を有効にすることもできます。

Azure Backup のセキュリティ機能の概要 (/azure/backup/security-overview) 

- [カスタマー マネージド キーを使用したバックアップ データの暗号化](../backup/encryption-at-rest-with-cmk.md) 

- [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?preserve-view=true&view=azurermps-6.13.0)

- [ハイブリッド バックアップを攻撃から保護するためのセキュリティ機能](../backup/backup-azure-security-feature.md#prevent-attacks)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: バックアップ メディアのデータ整合性を定期的に検証するため、データ復元プロセスを実行して、バックアップが適切に機能しているかどうか確認することをお勧めします。

- [Azure 仮想マシンのバックアップからファイルを復旧する方法](../backup/backup-azure-restore-files-from-vm.md)

- [セキュリティの実装](../backup/backup-azure-restore-files-from-vm.md#security-implementations)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="governance-and-strategy"></a>ガバナンスと戦略

*詳細については、[Azure セキュリティ ベンチマークの「ガバナンスと戦略](../security/benchmarks/security-controls-v2-governance-strategy.md)」を参照してください。*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 資産の管理とデータ保護の戦略を定義する 

**ガイダンス**:システムとデータを継続的に監視および保護するための明確な戦略が文書化および伝達されるようにします。 ビジネスに不可欠なデータとシステムの検出、評価、保護、監視の優先順位を決定します。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   ビジネス リスクに応じたデータ分類標準

-   リスクと資産のインベントリに対するセキュリティ組織の可視性 

-   Azure サービスを使用するためのセキュリティ組織の承認 

-   ライフサイクルを通じた資産のセキュリティ

-   組織のデータ分類に従った必要なアクセス制御戦略

-   Azure ネイティブおよびサードパーティのデータ保護機能の使用

-   転送中および保存中のユース ケースのデータ暗号化要件

-   適切な暗号化標準

詳細については、次のリファレンスを参照してください。
- [Azure セキュリティ アーキテクチャに関する推奨事項 - ストレージ、データ、暗号化](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure のセキュリティの基礎 - Azure のデータ セキュリティ、暗号化、ストレージ](../security/fundamentals/encryption-overview.md)

- [クラウド導入フレームワーク - Azure のデータ セキュリティと暗号化のベスト プラクティス](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure セキュリティ ベンチマーク - アセット管理](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure セキュリティ ベンチマーク - データ保護](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 企業のセグメント化戦略を定義する 

**ガイダンス**:ID、ネットワーク、アプリケーション、サブスクリプション、管理グループ、その他のコントロールを利用し、アセットへのアクセスをセグメント化する企業規模の戦略を確立します。

セキュリティ分離の必要性と、互いと通信し、データにアクセスする必要があるシステムの日常的操作を有効にするという必要性のバランスを慎重に取ります。

セグメント化戦略は、ネットワーク セキュリティ、ID とアクセス モデル、アプリケーション アクセス許可とアクセス モデル、人的プロセスの制御など、あらゆるコントロールの種類を対象として確実に一貫性をもって実装します。

- [Azure のセグメント化戦略に関するガイド (動画)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure のセグメント化戦略に関するガイド (ドキュメント)](/security/compass/governance#enterprise-segmentation-strategy)

- [ネットワークのセグメント化を企業のセグメント化戦略に合わせる](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: セキュリティ態勢管理の戦略を定義する

**ガイダンス**:個々の資産とそれらがホストされている環境に対するリスクを継続的に測定し、軽減します。 高い価値を持つ資産と、攻撃に晒される可能性の高い部分 (公開されたアプリケーション、ネットワークのイングレス ポイントとエグレス ポイント、ユーザーと管理者のエンドポイントなど) を優先します。

- [Azure セキュリティ ベンチマーク - 体制と脆弱性の管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 組織の役割、責任、責務を整合させる

**ガイダンス**:セキュリティ組織における役割と責任に関する明確な戦略が文書化されて伝えられるようにします。 セキュリティに関する決定についてわかりやすく説明すること、共有される責任モデルについて全員に教育すること、クラウドをセキュリティで保護するテクノロジについて技術チームに教育することを優先とします。

- [Azure のセキュリティのベスト プラクティス 1 – 人: クラウド セキュリティに関する取り組みについてチームを教育する](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure のセキュリティのベスト プラクティス 2 - 人: クラウド セキュリティ テクノロジについてチームを教育する](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure のセキュリティのベスト プラクティス 3 - プロセス: クラウドのセキュリティに関する意思決定のアカウンタビリティを割り当てる](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: ネットワーク セキュリティ戦略を定義する

**ガイダンス**:組織全体のセキュリティ アクセス制御戦略の一環として、Azure ネットワーク セキュリティ アプローチを確立します。  

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   一元的なネットワーク管理とセキュリティ責任

-   エンタープライズ セグメント化戦略と一致する仮想ネットワーク セグメント化モデル

-   さまざまな脅威と攻撃のシナリオにおける修復戦略

-   インターネット エッジとイングレスおよびエグレス戦略

-   クラウドとオンプレミスのハイブリッド相互依存戦略

-   最新のネットワーク セキュリティ成果物 (例: ネットワーク図、参照ネットワーク アーキテクチャ)

詳細については、次のリファレンスを参照してください。
- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure セキュリティ ベンチマーク - ネットワーク セキュリティ](../security/benchmarks/security-controls-v2-network-security.md)

- [Azure のネットワーク セキュリティの概要](../security/fundamentals/network-overview.md)

- [エンタープライズ ネットワーク アーキテクチャ戦略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID と特権アクセス戦略を定義する

**ガイダンス**:組織全体のセキュリティ アクセス制御戦略の一環として、Azure の ID と特権アクセス アプローチを確立します。  

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   一元化された ID と認証システム、および他の内部および外部 ID システムとのその相互接続

-   さまざまなユース ケースおよび条件における強力な認証方法

-   高い特権を持つユーザーの保護

-   異常なユーザー アクティビティの監視と処理  

-   ユーザー ID とアクセスの確認と調整のプロセス

詳細については、次のリファレンスを参照してください。

- [Azure セキュリティ ベンチマーク - ID 管理](../automation/update-management/overview.md)

- [Azure セキュリティ ベンチマーク - 特権アクセス](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 管理のセキュリティの概要](../security/fundamentals/identity-management-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: ログ記録と脅威対応戦略を定義する

**ガイダンス**:コンプライアンス要件を満たしながら脅威を迅速に検出して修復するための、ログ記録と脅威対応戦略を確立します。 アナリストが、統合や手動による手順ではなく、脅威の対応に集中できるように、質の高いアラートとシームレスなエクスペリエンスを提供することを優先してください。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   セキュリティ運用 (SecOps) 組織の役割と責任 

-   NIST または他の業界フレームワークと一致する、明確に定義されたインシデント対応プロセス 

-   脅威の検出、インシデント対応、コンプライアンスのニーズに対応するためのログのキャプチャと保持

-   SIEM、Azure のネイティブ機能、その他のソースを使用した、脅威に関する情報の一元的な可視化と関連付け 

-   顧客、サプライヤー、および関心を持つパブリック パーティに関するコミュニケーションと通知の計画

-   ログ記録と脅威の検出、フォレンジクス、攻撃の修復と根絶など、インシデント処理に対する Azure ネイティブおよびサードパーティのプラットフォームの使用

-   インシデントとインシデント発生後のアクティビティを処理するためのプロセス (教訓や証拠の保持など)

詳細については、次のリファレンスを参照してください。

- [Azure セキュリティ ベンチマーク - ログと脅威検出](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure セキュリティ ベンチマーク - インシデント対応](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure のセキュリティのベスト プラクティス 4 - プロセス: クラウドのインシデント対応プロセスを更新する](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 導入フレームワーク、ログ、およびレポートの決定ガイド](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure のエンタープライズ スケーリング、管理、監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](../security/benchmarks/overview.md)」を参照してください。
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する
