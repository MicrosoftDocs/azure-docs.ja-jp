---
title: Microsoft Azure 用カスタマー ロックボックスの Azure セキュリティ ベースライン
description: Microsoft Azure 用カスタマー ロックボックスの Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: fb0b976c8759eb77aa2240f95fadbd41e5d9596a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504918"
---
# <a name="azure-security-baseline-for-customer-lockbox-for-microsoft-azure"></a>Microsoft Azure 用カスタマー ロックボックスの Azure セキュリティ ベースライン

Microsoft Azure 用カスタマー ロックボックスの Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つレコメンデーションが含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../benchmarks/overview.md) に基づいて作成されています。

詳細については、「[Azure セキュリティ ベースラインの概要](../benchmarks/security-baselines-overview.md)」を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](../benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: 適用できません。仮想ネットワーク、サブネット、またはネットワーク セキュリティ グループをカスタマー ロックボックスに関連付けることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:VNet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: 適用できません。仮想ネットワーク、サブネット、またはネットワーク セキュリティ グループをカスタマー ロックボックスに関連付けることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:適用できません。仮想ネットワーク、サブネット、またはネットワーク セキュリティ グループをカスタマー ロックボックスに関連付けることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**:適用できません。仮想ネットワーク、サブネット、またはネットワーク セキュリティ グループをカスタマー ロックボックスに関連付けることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: 適用できません。仮想ネットワーク、サブネット、またはネットワーク セキュリティ グループをカスタマー ロックボックスに関連付けることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: 適用できません。仮想ネットワーク、サブネット、またはネットワーク セキュリティ グループをカスタマー ロックボックスに関連付けることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: 適用できません。仮想ネットワーク、サブネット、またはネットワーク セキュリティ グループを Azure カスタマー ロックボックスに関連付けることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: 適用できません。仮想ネットワーク、サブネット、またはネットワーク セキュリティ グループをカスタマー ロックボックスに関連付けることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: 適用できません。仮想ネットワーク、サブネット、またはネットワーク セキュリティ グループをカスタマー ロックボックスに関連付けることはできません。 作成または監視するネットワーク構成がありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](../benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: 適用できません。Microsoft では、カスタマー ロックボックスなどのリソースに使用されるタイム ソースを管理しています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: カスタマー ロックボックスの監査ログは、Azure アクティビティ ログで自動的に有効にされ、管理されます。 このデータを表示するには、Azure アクティビティ ログから Log Analytic ワークスペースにそれをストリーミングし、そこでそれに関する調査と分析を実行できます。

カスタマー ロックボックスによって生成されたアクティビティ ログを Azure Sentinel または別の SIEM にオンボードして、中央のログの集計と管理を有効にします。

* [カスタマー ロックボックスの監査ログ](./customer-lockbox-overview.md#auditing-logs)

* [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: カスタマー ロックボックスの監査ログは、Azure アクティビティ ログで自動的に有効にされ、管理されます。 このデータを表示するには、Azure アクティビティ ログから Log Analytic ワークスペースにそれをストリーミングし、そこでそれに関する調査と分析を実行できます。

* [カスタマー ロックボックスの監査ログ](./customer-lockbox-overview.md#auditing-logs)

* [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor で、組織のコンプライアンス規則に従って、カスタマー ロックボックスに関連付けられている Log Analytics ワークスペースのログの保持期間を設定します。

* [ログ保持期間のパラメーターを設定する方法](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: カスタマー ロックボックスの監査ログは、Azure アクティビティ ログで自動的に有効にされ、管理されます。 このデータを表示するには、Azure アクティビティ ログから Log Analytic ワークスペースにそれをストリーミングし、そこでそれに関する調査と分析を実行できます。 異常な動作について、カスタマー ロックボックス要求からのログを分析し、監視します。 Azure Sentinel ワークスペースの "ログ" セクションを使用して、クエリを実行したり、カスタマー ロックボックス ログに基づいてアラートを作成したりします。

* [カスタマー ロックボックスの監査ログ](./customer-lockbox-overview.md#auditing-logs)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: カスタマー ロックボックスの監査ログは、Azure アクティビティ ログで自動的に有効にされ、管理されます。 このデータを表示するには、Azure アクティビティ ログから Log Analytic ワークスペースにそれをストリーミングし、そこでそれに関する調査と分析を実行できます。 異常な動作について、カスタマー ロックボックス要求からのログを分析し、監視します。 Azure Sentinel ワークスペースの "ログ" セクションを使用して、クエリを実行したり、カスタマー ロックボックス ログに基づいてアラートを作成したりします。

* [カスタマー ロックボックスの監査ログ](./customer-lockbox-overview.md#auditing-logs)

* [Log Analytics のログ データに関するアラートを送信する方法](../../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用できません。カスタマー ロックボックスでは、マルウェア対策関連のログの処理や生成を行いません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません。カスタマー ロックボックスでは、DNS 関連のログの処理や生成を行いません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](../benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: カスタマー ロックボックス要求への管理アクセス権を持つユーザー アカウントのインベントリを保持します。 サブスクリプションの Azure portal にある ID およびアクセス管理 (IAM) ウィンドウを使用して、Azure ロールベースのアクセス制御 (Azure RBAC) を構成できます。 ロールは、Azure Active Directory 内のユーザー、グループ、サービス プリンシパル、およびマネージド ID に適用されます。

お客様の組織で、Azure サブスクリプションの所有者ロールを持つユーザーに、保留中のアクセス要求について通知するメールが Microsoft から送信されます。 カスタマー ロックボックス要求では、このユーザーが承認者に指定されます。

* [カスタム ロールについて](../../role-based-access-control/custom-roles.md)

* [ブック用に Azure RBAC を構成する方法](../../sentinel/quickstart-get-visibility.md)

* [カスタマー ロックボックスでのアクセス要求のアクセス許可について](./customer-lockbox-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory には、既定のパスワードの概念がありません。 パスワードを必要とする他の Azure リソースでは、パスワードが強制的に作成されます。これには複雑な要件と、サービスによって異なるパスワードの最小文字数が適用されます。 既定のパスワードが使用される可能性があるサードパーティ製のアプリケーションとマーケットプレース サービスについては、お客様が責任を負うものとします。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のようなレコメンデーションを使用することもできます。
- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

* [Azure Security Center を使用して ID およびアクセスを監視する方法 (プレビュー)](../../security-center/security-center-identity-access.md)

* [Azure Policy を使用する方法](../../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**: 適用できません。カスタマー ロックボックスへのアクセスは、Azure portal から行い、所有者のテナント ロールを持つアカウント用に予約されています。 シングル サインオンはサポートされていません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure Active Directory Multi-Factor Authentication を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

* [Azure で MFA を有効にする方法](../../active-directory/authentication/howto-mfa-getstarted.md)

* [Azure Security Center で ID とアクセスを監視する方法](../../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:Azure AD Multi-Factor Authentication (MFA) 対応の特権アクセス ワークステーション (PAW) を使用して、ログインし、カスタマー ロックボックス要求を構成します。

* [Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) (Privileged Access Workstation)

* [クラウドベースの Azure AD Multi-Factor Authentication のデプロイの計画](../../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全ではないアクティビティが発生したときのログとアラートの生成のために、Azure Active Directory Privileged Identity Management (PIM) を使用します。

また、Azure Active Directory のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

* [Privileged Identity Management (PIM) をデプロイする方法](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure AD のリスク検出の概要](../../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**:条件付きアクセスのネームド ロケーションを使用して、IP アドレスの範囲、国、またはリージョンの特定の論理グループからのみ Azure portal へのアクセスを許可します。

* [Azure でネームド ロケーションを構成する方法](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory を中央認証および承認システムとして使用します (該当する場合)。 Azure Active Directory でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure Active Directory では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

* [Azure Active Directory インスタンスを作成して構成する方法](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure Active Directory では、古いアカウントの検出に役立つログが提供されます。 また、Azure Active Directory アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

* [Azure Active Directory レポートについて](../../active-directory/reports-monitoring/index.yml)

* [Azure Active Directory のアクセス レビューを使用する方法](../../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

**ガイダンス**: Azure Active Directory を中央認証および承認システムとして使用します (該当する場合)。 Azure Active Directory でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure Active Directory では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

Azure Active Directory サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、Azure Sentinel またはサードパーティの SIEM との統合が可能です。

このプロセスを効率化するには、Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics 内で必要なログ アラートを構成できます。

* [Azure アクティビティ ログを Azure Monitor に統合する方法](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**:コントロール プレーン (Azure portal など) でのアカウント ログイン動作の偏差について、Azure Active Directory Identity Protection とリスク検出機能を使用して、ユーザー ID に関連して検出された疑わしいアクションへの自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

* [Azure Active Directory の危険なサインインを表示する方法](../../active-directory/identity-protection/overview-identity-protection.md)

* [Identity Protection のリスク ポリシーを構成して有効にする方法](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: このレコメンデーションはカスタマー ロックボックスには適用されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](../benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: このレコメンデーションは適用されません。タグはカスタマー ロックボックスでサポートされていません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: 適用できません。カスタマー ロックボックスは、アクセスを許可しているリソースと同じサブスクリプションにプロビジョニングされます。 保護または分離するパブリック エンドポイントがありません。 カスタマー ロックボックス要求のアクセス権は、テナント レベルで所有者ロールを保持しているユーザーに付与されます。

* [カスタマー ロックボックス ワークフローについて](./customer-lockbox-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: Microsoft では、カスタマー ロックボックス用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

* [Azure での顧客データの保護について](./protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: 既定で、Microsoft では、クラウド サービスとユーザーとの間でデータをやりとりする際に、トランスポート層セキュリティ (TLS) プロトコルを使用してデータを保護します。 Microsoft のデータ センターは、Azure サービスに接続するクライアント システムとの TLS 接続をネゴシエートします。 TLS には、強力な認証、メッセージの機密性、整合性 (メッセージの改ざん、傍受、偽造の検出が有効)、相互運用性、アルゴリズムの柔軟性、デプロイと使用のしやすさといったメリットがあります。

* [Azure での転送中の暗号化の概要](./encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: 適用できません。カスタマー ロックボックス自体は顧客データを保持しません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**:カスタマー ロックボックス要求の承認は、テナント レベルで所有者ロールを保持しているユーザーに付与されます。

* [カスタマー ロックボックス ワークフローについて](./customer-lockbox-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。 Microsoft では、カスタマー ロックボックス用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

* [Azure での顧客データの保護](./protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: 適用できません。カスタマー ロックボックス自体は顧客データを保持しません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: カスタマー ロックボックスの監査ログは、Azure アクティビティ ログで自動的に有効にされ、管理されます。 Azure アクティビティ ログを使用して、Azure カスタマー ロックボックス リソースへの変更を監視し、検出します。 重要なリソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

* [カスタマー ロックボックスで監査を有効にする方法](./customer-lockbox-overview.md)

* [Azure アクティビティ ログ イベントを表示して取得する方法](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Azure Monitor でアラートを作成する方法](../../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](../benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: 適用できません。Microsoft では、カスタマー ロックボックスをサポートしている基になるシステムに対して脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:サードパーティの自動化されたソフトウェア修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: 適用できません。Microsoft では、カスタマー ロックボックスをサポートしている基になるシステムに対して脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**:適用できません。Microsoft では、カスタマー ロックボックスをサポートしている基になるシステムに対して脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](../benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Azure Resource Graph で検出できますが、Azure Resource Manager リソースを作成して使用することを強くお勧めします。

* [Azure Resource Graph を使用してクエリを作成する方法](../../governance/resource-graph/first-query-portal.md)

* [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

* [Azure ロールベースのアクセス制御 (Azure RBAC) を理解する](../../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:タグはカスタマー ロックボックスではサポートされていません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: Azure リソースを整理および追跡するには、必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

さらに、Azure Policy を使用し、次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類を制限します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [追加の Azure サブスクリプションを作成する方法](../../cost-management-billing/manage/create-subscription.md)

* [管理グループを作成する方法](../../governance/management-groups/create-management-group-portal.md)

* [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 環境に存在するすべての Azure リソースが承認されていることを確認します。

* [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

* [Azure Resource Graph を使用してクエリを作成する方法](../../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、サブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy を使用して特定のリソースの種類を拒否する方法](../../governance/policy/samples/index.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11:スクリプトを使用して Azure Resource Manager を操作するユーザーの権限を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

* [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](../benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: 適用できません。カスタマー ロックボックスには、構成可能なセキュリティ設定はありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: 適用できません。カスタマー ロックボックスには、構成可能なセキュリティ設定はありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: 適用できません。カスタマー ロックボックスには、構成可能なセキュリティ設定はありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: 適用できません。カスタマー ロックボックスには、構成可能なセキュリティ設定はありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**: 適用できません。カスタマー ロックボックスには、構成可能なセキュリティ設定はありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: 適用できません。カスタマー ロックボックス要求へのアクセスは、リソースを格納する Azure サブスクリプションの所有者に制限されます。 テナント所有者としてログインすること以外に、カスタマー ロックボックスにアクセスするために必要なパスワード、シークレット、キーはありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: 適用できません。カスタマー ロックボックスではマネージド ID を使用しません。

* [マネージド ID をサポートする Azure サービス](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

* [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](../benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。 カスタマー ロックボックス ソリューションをサポートする基になるホストで、Microsoft Antimalware が有効にされています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: カスタマー ロックボックスなどの Azure サービスをサポートする基になるホストで、Microsoft Antimalware が有効にされています。

非コンピューティング Azure リソースにアップロードされている任意のコンテンツを事前にスキャンするのは、お客様の責任となります。 Microsoft は、お客様のデータにアクセスできないため、お客様に代わってお客様のコンテンツのマルウェア対策スキャンを実行することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。 Microsoft Antimalware は、Azure サービスをサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](../benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: 適用できません。カスタマー ロックボックス自体には、顧客データは格納されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**: 適用できません。カスタマー ロックボックス自体には、顧客データは格納されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:適用できません。カスタマー ロックボックス自体には、顧客データは格納されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**:適用できません。カスタマーロックボックス自体には顧客データが格納されません。また、アクセスにキーやパスワードを使用することもありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](../benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

* [Azure Security Center 内でワークフロー自動化を構成する方法](../../security-center/security-center-planning-and-operations-guide.md)

* [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

* [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

* [Azure Security Center のセキュリティ連絡先を設定する方法](../../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

* [連続エクスポートを構成する方法](../../security-center/continuous-export.md)

* [Azure Sentinel にアラートをストリーミングする方法](../../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

* [ワークフローの自動化と Logic Apps を構成する方法](../../security-center/workflow-automation.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](../benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、60 日以内に確実に修復されるようにします

**ガイダンス**: 

* [お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらを参照してください。](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../benchmarks/security-baselines-overview.md)の詳細について学習する