---
title: Azure Data Box 用の Azure セキュリティ ベースライン
description: Azure Data Box 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d8a1b2192784c26ed17b31be262b22efbf935d84
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89225606"
---
# <a name="azure-security-baseline-for-azure-data-box"></a>Azure Data Box 用の Azure セキュリティ ベースライン

Azure Data Box 用の Azure セキュリティ ベースラインには、ご自身のデプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) に基づいて作成されています。

詳細については、「[Azure セキュリティ ベースラインの概要](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)」を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: 適用できません。Azure Data Box を仮想ネットワークに関連付けることはできません。 Azure portal を介して、Data Box から Azure でホストされるストレージへのトラフィックを制御します。 Data Box を利用すると、Azure のバックボーンを介してデータが転送されます。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:仮想ネットワーク、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: 適用できません。Azure Data Box を仮想ネットワークに関連付けることはできません。 Azure portal を介して、Data Box から Azure でホストされるストレージへのトラフィックを制御します。 Data Box を利用すると、Azure のバックボーンを介してデータが転送されます。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: 適用できません。Azure Data Box を仮想ネットワークに関連付けることはできません。 Azure portal を介して、Data Box から Azure でホストされるストレージへのトラフィックを制御します。 Data Box を利用すると、Azure のバックボーンを介してデータが転送されます。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: 適用できません。Azure Data Box を仮想ネットワークに関連付けることはできません。 Azure portal を介して、Data Box から Azure でホストされるストレージへのトラフィックを制御します。 Data Box を利用すると、Azure のバックボーンを介してデータが転送されます。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: ガイダンス: Azure Data Box によって使用されるエンドポイントはすべて、Microsoft によって管理されます。 オンプレミスのシステムにデプロイする追加のコントロールについては、お客様が責任を負うものとします。

* [Azure Data Box のセキュリティについて](https://docs.microsoft.com/azure/databox/data-box-security)

* [Azure Data Box のポート情報](https://docs.microsoft.com/azure/databox/data-box-system-requirements#port-requirements)

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: 適用できません。Azure Data Box を仮想ネットワークに関連付けることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: 適用できません。Azure Data Box を仮想ネットワークに関連付けることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: 適用できません。Azure Data Box を仮想ネットワークに関連付けることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: 適用できません。Azure Data Box を仮想ネットワークに関連付けることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Microsoft では、Azure リソースに使用するタイム ソースを保持しています (たとえば、ログ内のタイムスタンプ用に)。 Azure Data Box の時間は、それが、顧客のサイトの NTP サーバーにアクセスできるネットワークに接続されていない場合、ずれる場合があります。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Data Box の注文の各段階に対応して、注文へのアクセスの制御、イベントの監査、注文の追跡、生成されるさまざまなログの解釈のための、さまざまなアクションを実行できます。

* [Azure Data Box の追跡とイベントのログ記録について](https://docs.microsoft.com/azure/databox/data-box-logs)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Data Box の注文の各段階に対応して、注文へのアクセスの制御、イベントの監査、注文の追跡、生成されるさまざまなログの解釈のための、さまざまなアクションを実行できます。

* [Azure Data Box の追跡とイベントのログ記録について](https://docs.microsoft.com/azure/databox/data-box-logs)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: この推奨事項は、コンピューティング リソースを対象にしています。 Data Box には、セキュリティ ログを含む監査ログおよびサポート パッケージが用意されています。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: 適用なし

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Data Box の注文の各段階に対応して、注文へのアクセスの制御、イベントの監査、注文の追跡、生成されるさまざまなログの解釈のための、さまざまなアクションを実行できます。

* [Azure Data Box の追跡とイベントのログ記録について](https://docs.microsoft.com/azure/databox/data-box-logs)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:  Data Box の注文の各段階に対応して、注文へのアクセスの制御、イベントの監査、注文の追跡、生成されるさまざまなログの解釈のための、さまざまなアクションを実行できます。

* [Azure Data Box の追跡とイベントのログ記録について](https://docs.microsoft.com/azure/databox/data-box-logs)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用できません。Azure Data Box では、マルウェア対策関連のログの処理や生成を行いません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません。Azure Data Box では、DNS 関連のログの処理や生成を行いません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: ご利用の Azure Data Box への管理アクセス権を持つユーザー アカウントのインベントリを維持します。 サブスクリプションの Azure portal にある ID およびアクセス管理 (IAM) ウィンドウを使用して、Azure ロールベースのアクセス制御 (Azure RBAC) を構成できます。 ロールは、Active Directory 内のユーザー、グループ、サービス プリンシパル、マネージド ID に適用されます。注文が最初に作成されたときに、注文にアクセスできるユーザーを制御できます。 さまざまな範囲の Azure ロールを設定して、Data Box の注文へのアクセスを制御します。 Azure ロールは、アクセスの種類 (読み取り/書き込み、読み取り専用、操作のサブセットへの読み取り/書き込み) を決定します。

* [カスタム ロールについて](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)

* [ブック用に Azure RBAC を構成する方法](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)

* [注文に対するアクセス制御のセットアップ方法を理解する](https://docs.microsoft.com/azure/databox/data-box-logs#set-up-access-control-on-the-order)

**Azure Security Center の監視**: いいえ

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure AD には既定のパスワードという概念がありません。 パスワードを必要とする他の Azure リソースでは、パスワードが強制的に作成されます。これには複雑な要件と、サービスによって異なるパスワードの最小文字数が適用されます。 既定のパスワードが使用される可能性があるサードパーティ製のアプリケーションとマーケットプレース サービスについては、お客様が責任を負うものとします。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。
- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

* [Azure Security Center を使用して ID およびアクセスを監視する方法 (プレビュー)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Azure Policy を使用する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**: 適用できません。ご利用の Data Box へのアクセスは、Azure portal から行い、所有者または共同作成者のテナント ロールを持つアカウント用に予約されています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: 適用なし

**Azure Security Center の監視**: いいえ

**責任**: 適用なし

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: Azure Multi-Factor Authentication (MFA) 対応の特権アクセス ワークステーション (PAW) を使用して、ログインし、Azure Data Box の注文を構成します。

* [Privileged Access Workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) (Privileged Access Workstation)

* [クラウド ベースの Azure Multi-Factor Authentication のデプロイの計画](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: 適用できません。Azure Data Box には固有の管理者アカウントがありません。 アクセスするには、Azure portal を使用します。 ただし、Azure Active Directory (AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいまたは安全でないアクティビティが発生したときにログとアラートを生成するように、ご利用の Azure サブスクリプションを構成できます。

また、Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

* [Privileged Identity Management (PIM) をデプロイする方法](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Azure AD のリスク検出の概要](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレスの範囲、国、またはリージョンの特定の論理グループからのみ Azure portal へのアクセスを許可します。

* [Azure でネームド ロケーションを構成する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (AD) を中央認証および承認システムとして使用します (該当する場合)。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

* [Azure AD インスタンスを作成して構成する方法](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

Data Box アプライアンスの場合、これはリアルタイムではサポートされません。 ジョブの終了時にログを確認できます。

* [Azure AD のレポートの概要](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Azure ID アクセス レビューの使用方法](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory (AD) を中央認証および承認システムとして使用します (該当する場合)。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

Azure AD サインイン アクティビティ、監査、リスク イベント ログのソースにアクセスできるため、Azure Sentinel またはサードパーティの SIEM と統合することができます。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics 内で必要なログ アラートを構成できます。

Azure Data Box サービス ログは Log Analytics ワークスペースに書き込まれません。

* [Azure アクティビティ ログを Azure Monitor に統合する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: コントロール プレーン (Azure portal など) でのアカウント ログイン動作の逸脱については、Azure AD Identity Protection とリスク検出機能を使用して、ユーザー ID に関連して検出された疑わしいアクションへの自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

* [Azure AD のリスクの高いサインインを表示する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Identity Protection のリスク ポリシーを構成して有効にする方法](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: カスタマー ロックボックスは、現在 Azure Data Box ではサポートされていません。

* [カスタマー ロックボックスでサポートされているサービスの一覧](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: Azure Data Box には適用されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: Azure Data Box は、アクセス権を付与しているリソースが存在するサブスクリプションでプロビジョニングされます。 保護または分離するパブリック エンドポイントがありません。 Data Box アクセスを利用できるのは、サブスクリプションに対する所有者または共同作成者のアクセス権を持つユーザーです。

Azure へのデータのアップロード中に、Data Box のアプライアンスと、データのアップロードに使用されるサービスが分離されます。

* [Azure Data Box の使用を開始する方法](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: Microsoft では、Azure Data Box の基盤となるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。 Data Box が顧客サイトにある場合は、ベスト プラクティスに従って、転送される機密データが確実に保護されるようにしてください。

* [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Microsoft では、Azure Data Box の基盤となるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。 Data Box が顧客サイトにある場合は、ベスト プラクティスに従って、転送される機密データが確実に保護されるようにしてください。

* [Azure Data Box でのデータ移行について](https://docs.microsoft.com/azure/databox/data-box-faq)

* [Data Box セキュリティの概要](https://docs.microsoft.com/azure/databox/data-box-security)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: 現在使用できません。データの識別、分類、損失防止機能は、Azure Data Box ではまだご利用いただけません。Microsoft は、Azure Data Box の基盤となるインフラストラクチャを管理し、顧客データの損失や漏えいを防ぐための厳格な管理を実施してきました。

* [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 適用なし

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Data Box の注文を作成するサブスクリプションに対して、所有者または共同作成者アクセス権があることを確認します。 また、Data Box 閲覧者ロールと Data Box 共同作成者ロールをリソース レベルで定義することもできます。

* [Azure Data Box の使用を開始する方法を理解する](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)

* [アクセス制御の設定方法を理解する](https://docs.microsoft.com/azure/databox/data-box-logs#set-up-access-control-on-the-order)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。 Microsoft では、Azure Data Box の基盤となるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

* [Azure での顧客データの保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Data Box では、保存データの AES 256 ビット暗号化が実装されています。

Azure Data Box では、保存データの AES 256 ビット暗号化が実装されています。さらに、Azure Data Box は、デバイスのロックに使用されたデバイス ロック解除キー (デバイス パスワードとも呼ばれる) を、暗号化キーにより保護します。 既定では、Data Box の注文のデバイス ロック解除キーは、Microsoft マネージド キーを使って暗号化されます。 デバイス ロック解除キーをさらに制御するために、カスタマー マネージド キーを提供することもできます。 カスタマー マネージド キーは、Azure Key Vault で作成して格納する必要があります。

* [Data Box データ保護について](https://docs.microsoft.com/azure/databox/data-box-security)

* [Azure Key Vault のカスタマー マネージド キーを Azure Data Box に使用する](https://docs.microsoft.com/azure/databox/data-box-customer-managed-encryption-key-portal)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Data Box およびその他の重要なリソースまたは関連リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

* [Azure アクティビティ ログ イベントのアラートを作成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Microsoft では、Azure Data Box をサポートしている基になるシステムで脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: Data Box は出荷されると、最新の更新プログラムを適用してインストールされます。 フィールドの更新は実行しません。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**: Data Box は出荷されると、最新の更新プログラムを適用してインストールされます。 フィールドの更新は実行しません。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**:  Microsoft では、Azure Data Box をサポートしている基になるシステムで脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: Microsoft では、Azure Data Box をサポートしている基になるシステムで脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された Asset Discovery ソリューションを使用する

**ガイダンス**: 適用できません。検出する Data Box 資産がありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: 適用なし。Data Box の資産メタデータがありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 適用なし。Data Box サービスでは、承認されていない Azure リソースが使用されることは決してありません。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 適用できません。Data Box サービス レベルにはありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 適用なし。Data Box サービス レベルにはありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用なし。Data Box サービス レベルにはありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: 適用なし。Data Box サービス レベルにはありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用なし。Data Box サービス レベルにはありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 適用できません。Data Box サービスでは、承認された Azure サービスのみが使用されます。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 適用できません。Data Box サービスでは、承認されたソフトウェア タイトルのみが使用されます。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

* [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。Data Box サービスで、スクリプトの実行はサポートされていません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用できません。Data Box サービスには、Azure App Service 上で実行される Web アプリケーションはありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Data Box には、事前構成されたベスト プラクティス セキュリティ設定が付属しています。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: Azure Data Box には、事前構成されたベスト プラクティス セキュリティ設定が付属しています。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure Data Box には、リソース用に事前構成されたベスト プラクティス セキュリティ設定が付属していて、変更することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: Azure Data Box には、リソース用に事前構成されたベスト プラクティス セキュリティ設定が付属していて、変更することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: Data Box 構成はすべて、安全に格納されます。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: Data Box オペレーティング システム イメージはすべて、安全に格納されます。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: 適用できません。Azure Data Box 構成を変更することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**: 適用できません。Azure Data Box 構成を変更することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: 適用できません。Azure Data Box 構成を変更することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。Azure Data Box 構成を変更することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: カスタマー マネージド キーは、Azure Key Vault で作成して格納する必要があります。

* [Azure Key Vault のカスタマー マネージド キーを Azure Data Box に使用する方法](https://docs.microsoft.com/azure/databox/data-box-customer-managed-encryption-key-portal)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: 適用できません。Azure Data Box ではマネージド ID は利用されません。

* [マネージド ID がサポートされる Azure サービス](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: Microsoft は Data Box コードに対して資格情報スキャナーを実行します。 さらに、Microsoft は資格情報も安全に保護します。 コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

* [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 該当なし

**責任**: 共有

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。 Microsoft マルウェア対策は、Azure サービス (Azure App Service など) をサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Azure カスタマー ロックボックスなど) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

非コンピューティング Azure リソースにアップロードされている任意のコンテンツを事前にスキャンするのは、お客様の責任となります。 Microsoft は、お客様のデータにアクセスできないため、お客様に代わってお客様のコンテンツのマルウェア対策スキャンを実行することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。 Microsoft Antimalware は、Azure サービスをサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: 適用できません。Data Box サービスではバックアップは必要ありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: データとカスタマー マネージド キーは必ずバックアップしてください。 Data Box では、バックアップは作成されません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: ご利用のデータをオンプレミスから削除する場合は、事前にそれらがすべて Azure Storage アカウント内にあることを確認してください。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: ご利用のバックアップまたはカスタマー マネージド キーが、ベスト プラクティスに従って保護されていることを確認します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

* [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [独自のインシデント対応計画の作成のための NIST の「コンピューター セキュリティ インシデント対応ガイド」](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 運用、非運用など) をタグを使用して明確にマークし、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。 インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

* [Security alerts in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [タグを使用した Azure リソースの整理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。

* [NIST の出版物 - IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

* [Azure Security Center のセキュリティ連絡先を設定する方法](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

* [連続エクスポートを構成する方法](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Azure Sentinel にアラートをストリーミングする方法](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して "Logic Apps" で自動的に応答をトリガーし、Azure リソースを保護できます。

* [ワークフローの自動化と Logic Apps を構成する方法](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: Microsoft は Data Box デバイスに対して侵入テストと脆弱性のスキャンを行います。 お客様は独自の侵入テストと脆弱性のスキャンを行うことができます。 そうすることを選択した場合は、ご利用の侵入テストが Microsoft のポリシーに決して違反しないように、Microsoft の活動規則に従う必要があります。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

* [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](https://docs.microsoft.com/azure/security/benchmarks/overview)に関するページを参照する
- [Azure セキュリティ ベースライン](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
