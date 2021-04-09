---
title: Azure Storage 用の Azure セキュリティ ベースライン
description: Azure Storage セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 56f340a8d64346fd8934e9cfbae26079d4ee7f29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576561"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Azure Storage 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインでは、[Azure セキュリティ ベンチマーク バージョン 1.0](../../security/benchmarks/overview-v1.md) のガイダンスを Azure Storage に適用します。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Storage に適用できる関連ガイダンスによって定義されています。 Azure Storage に適用できない **制御** は、除外されています。

 
Azure Storage を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Storage セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:Azure Storage のファイアウォールを構成して、特定のパブリック IP アドレス範囲からクライアントへのアクセスを制限したり、仮想ネットワークまたは特定の Azure リソースを選択したりできます。  また、プライベート エンドポイントを構成して、トラフィックが、ご自身のエンタープライズからストレージ サービスに、プライベート ネットワーク経由で排他的に転送されるようにすることもできます。

注:従来のストレージ アカウントでは、ファイアウォールと仮想ネットワークはサポートされていません。

- [Azure Storage ファイアウォールを構成する方法](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Azure Storage のプライベート エンドポイントを構成する方法](storage-private-endpoints.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Storage では、多層型セキュリティ モデルが提供されています。 ストレージ アカウントへのアクセスを、Azure 仮想ネットワーク内の指定した IP アドレス、IP 範囲、またはサブネットのリストから発信された要求に制限できます。 Azure Security Center を使用し、ネットワークの保護に関する推奨事項に従って、Azure のネットワーク リソースを保護することができます。 また、ストレージ アカウント ファイアウォールを介してストレージ アカウント用に構成された仮想ネットワークまたはサブネットのネットワーク セキュリティ グループ フロー ログを有効にし、トラフィックの監査のためにログをストレージ アカウントに送信します。 

 
ストレージ アカウントにプライベート エンドポイントがアタッチされている場合は、サブネットに対してネットワーク セキュリティ グループ規則を構成できないことに注意してください。 
 

 
- [Azure Storage ファイアウォールおよび仮想ネットワークを構成する](storage-network-security.md) 
 

 
- [NSG フロー ログを有効にする方法](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 
 

 
- [Azure Security Center によって提供されるネットワークのセキュリティについて](../../security-center/security-center-network-recommendations.md) 
 

 
- [Azure Storage のプライベート エンドポイントについて](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: Azure Storage アカウントに対して Azure Defender for Storage を有効にします。 Azure Defender for Storage では、ストレージ アカウントに対する通常と異なる潜在的に有害なアクセスの試行すなわちストレージ アカウントの悪用を検出するセキュリティ インテリジェンス レイヤーが追加されます。  Azure Security Center 統合アラートは、IP アドレスが既知の危険な IP アドレス (例: 既知の Cryptominer) か、以前には危険として認識されていない IP アドレスかどうかにかかわらず、ネットワーク通信が正常に解決された IP アドレスに関連付けられているアクティビティに基づいています。 セキュリティ アラートは、アクティビティで異常が発生したときにトリガーされます。 

- [Azure Defender for Storage を構成する](azure-defender-storage-configure.md) 

- [Azure Security Center の統合された脅威インテリジェンスについて](../../security-center/azure-defender.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:Network Watcher のパケット キャプチャを使用すると、ストレージ アカウントと仮想マシンの間のトラフィックを追跡するキャプチャ セッションを作成できます。 必要なトラフィックのみを確実にキャプチャするためにキャプチャ セッション用のフィルターが用意されています。 パケット キャプチャは、事後と事前、どちらの場合でもネットワークの異常を診断するのに役立ちます。 その他の用途には、ネットワーク統計の収集や、ネットワークへの侵入に関する情報の取得などがあり、クライアントとサーバー間の通信のデバッグなどに役立ちます。 パケット キャプチャをリモートでトリガーできることで、目的の仮想マシンでパケット キャプチャを手動で実行する負担が軽減されて、貴重な時間の節約になります。 

- [ ポータルを使用して Azure Network Watcher でパケット キャプチャを管理する](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**: Azure Defender for Storage では、ストレージ アカウントに対する通常と異なる潜在的に有害なアクセスの試行すなわちストレージ アカウントの悪用を検出するセキュリティ インテリジェンス レイヤーが追加されます。 セキュリティ アラートは、アクティビティで異常が発生したときにトリガーされます。 これらのセキュリティ アラートは Azure Security Center と統合されます。さらに、不審なアクティビティの詳細と、脅威の調査や修復方法に関する推奨事項と共に、サブスクリプション管理者にメールで送信されます。 

- [Azure Defender for Storage を構成する](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: ストレージ アカウントへのアクセスが必要な仮想ネットワークのリソースについては、構成した仮想ネットワーク用の仮想ネットワーク サービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall に対するネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたはターゲット フィールドにサービス タグ名 (Storage など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。 

ネットワーク アクセスのスコープを特定のストレージ アカウントに設定する必要がある場合は、仮想ネットワーク サービス エンドポイント ポリシーを使用します。

- [サービス タグの使用に関する詳細について](../../virtual-network/service-tags-overview.md)

- [Azure Storage の仮想ネットワーク サービス エンドポイント ポリシーに関する詳細について](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Storage アカウントに関連付けられているネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 "Microsoft.Storage" と "Microsoft.Network" の名前空間で Azure Policy エイリアスを使用して、ストレージ アカウント リソースのネットワーク構成を監査または適用するためのカスタム ポリシーを作成します。 

次のように、ストレージ アカウントに関連する組み込みのポリシー定義を使用することもできます。ストレージ アカウントは仮想ネットワーク サービス エンドポイントを使用する必要がある 

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md) 

- [ストレージに関する Azure Policy のサンプル](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage) 

- [ネットワークに関する Azure Policy のサンプル](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Azure Blueprint を作成する方法](../../governance/blueprints/create-blueprint-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:ネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループやその他のリソースにタグを使用します。 タグ付けを使用すると、組み込みおよびカスタムの名前と値のペアを特定のネットワーク リソースに関連付けることができるため、ネットワーク リソースを整理し、Azure リソースをネットワーク設計に関連付けることができます。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。 

ネットワーク セキュリティ グループではタグがサポートされますが、個々のセキュリティ規則ではサポートされません。 セキュリティ規則には、通常タグに配置する情報の一部を格納するために使用できる **[説明]** フィールドがあります。

- [リソース タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

- [ネットワーク セキュリティ グループを使用してネットワーク トラフィックをフィルター処理する方法](../../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure Policy を使用して、ネットワーク リソースへの構成の変更をログに記録します。  重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。  

 
- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)
 
 
- [Azure Monitor でアラートを作成する方法](../../azure-monitor/alerts/alerts-activity-log.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージには Azure Storage アカウントを使用します。必要に応じて、不変ストレージや保有期間の保持の強制などのセキュリティ機能を使用できます。

 
- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../../azure-monitor/essentials/diagnostic-settings.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure Storage Analytics には、BLOB、キュー、およびテーブル用のログが用意されています。 Azure portal を使用して、アカウントに対してどのログが記録されるかを構成できます。   

 
- [Azure Storage アカウントの監視を構成する方法](manage-storage-analytics-logs.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Storage アカウントまたは Log Analytics ワークスペースにセキュリティ イベント ログを保存する場合は、組織の要件に従ってアイテム保持ポリシーを設定することができます。  

 
- [Azure Storage アカウント ログの保持ポリシーを構成する方法](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

 
- [Log Analytics でデータ保持期間を変更する](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure Storage ログを確認するには、Log Analytics オファリングを使用したクエリなどの通常のオプションと、ログ ファイルを直接表示する独自のオプションがあります。 Azure Storage では、ログは BLOB に格納され、`http://accountname.blob.core.windows.net/$logs` で直接アクセスする必要があります (ログ フォルダーは既定では非表示になっているため、直接移動する必要があります。 リスト コマンドには表示されません) 

 
また、ストレージ アカウントに対して Azure Defender for Storage を有効にします。 Azure Defender for Storage では、ストレージ アカウントに対する通常と異なる潜在的に有害なアクセスの試行すなわちストレージ アカウントの悪用を検出するセキュリティ インテリジェンス レイヤーが追加されます。 セキュリティ アラートは、アクティビティで異常が発生したときにトリガーされます。 これらのセキュリティ アラートは Azure Security Center と統合されます。さらに、不審なアクティビティの詳細と、脅威の調査や修復方法に関する推奨事項と共に、サブスクリプション管理者にメールで送信されます。 
 

 
- [データをログに記録して確認する](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored) 
 

 
- [Azure Defender for Storage を構成する](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Security Center で、ストレージ アカウントに対して Azure Defender を有効にします。 ストレージ アカウントの診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics ワークスペースを Azure Sentinel にオンボードします。これは、セキュリティ オーケストレーション自動応答 (SOAR) ソリューションが提供されるためです。 これにより、プレイブック (自動化されたソリューション) を作成して、セキュリティの問題を修復するために使用できます。  

 
- [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)  

 
- [Azure Security Center でアラートを管理する方法](../../security-center/security-center-managing-and-responding-alerts.md)  

 
- [Log Analytics のログ データに関するアラートを送信する方法](../../azure-monitor/alerts/tutorial-response.md)  

 
- [Azure Storage Analytics のログ](storage-analytics-logging.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**:Azure Security Center を使用し、ストレージに対して Azure Defender を有効にして、ハッシュ評価分析およびアクティブな Tor 出口ノード (匿名化プロキシ) からの疑わしいアクセスを使用して、Azure Storage へのマルウェアのアップロードを検出します。 

- [Azure Defender for Storage を構成する](azure-defender-storage-configure.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: Azure Monitor の Azure DNS Analytics (プレビュー) ソリューションを使用して、DNS インフラストラクチャのセキュリティ、パフォーマンス、操作に関する分析情報を収集します。  現時点では、Azure Storage アカウントはサポートされていませんが、サード パーティの DNS ログ ソリューションを使用することはできます。  

 
- [DNS Analytics プレビュー ソリューションを使用した DNS インフラストラクチャに関する分析情報の収集](../../azure-monitor/insights/dns-analytics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (Azure AD) には、明示的に割り当てる必要があるためにクエリ可能である組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Storage アカウントまたは Azure Active Directory (Azure AD) には、既定または空のパスワードという概念がありません。 Azure Storage では、Azure のロールベースのアクセス制御 (Azure RBAC) と、共有キーおよび Shared Access Signature (SAS) をサポートするアクセス制御モデルが実装されます。 共有キーと SAS 認証の特徴は、呼び出し元に ID が関連付けられていないため、セキュリティ プリンシパルのアクセス許可ベースの承認を実行できないことです。

- [Azure Storage 内のデータへのアクセスの承認](storage-auth.md)

- [Azure Storage アカウントのセキュリティ プリンシパルとアクセスの制御について](storage-introduction.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: ストレージ アカウントにアクセスできる専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

Microsoft サービスの Azure Active Directory (Azure AD) Privileged Identity Management の特権ロール、および Azure Resource Manager を使用して、Just-In-Time/Just-Enough-Access を有効にすることもできます。

- [Azure Security Center ID とアクセスについて](../../security-center/security-center-identity-access.md)

- [Privileged Identity Management の概要](/azure/active-directory/privileged-identity-management/)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**: 可能な限り、サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory (Azure AD) SSO を使用します。 Azure Security Center ID とアクセス管理の推奨事項を使用してください。
 

 
- [Azure AD を使用した SSO について](../../active-directory/manage-apps/what-is-single-sign-on.md)
 

 
- [Azure Storage 内のデータへのアクセスの承認](storage-auth.md)
 

 
- [Azure AD を使用して BLOB とキューへのアクセスを承認する](storage-auth-aad.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) 多要素認証を有効にし、ストレージ アカウント リソースを保護するための Azure Security Center ID とアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**: 多要素認証が構成された PAW (特権アクセス ワークステーション) を使用してストレージ アカウント リソースにログインし、そのリソースを構成します。  

 
- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

 
- [Azure で多要素認証を有効にする方法](../../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: Azure Security Center のリスク検出アラートを Azure Monitor に送信し、アクション グループを使用してカスタムのアラートまたは通知を構成します。 ストレージ アカウントに対して Azure Defender を有効にして、疑わしいアクティビティに関するアラートを生成します。 さらに、Azure Active Directory (Azure AD) のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

- [Azure Defender for Storage を構成する](azure-defender-storage-configure.md)
 

- [Azure AD のリスク検出について](../../active-directory/identity-protection/overview-identity-protection.md)
 

- [カスタムのアラートおよび通知用にアクション グループを構成する方法](../../azure-monitor/alerts/action-groups.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国、地域の特定の論理グループからのアクセスのみを許可します。 

- [Azure でネームド ロケーションを構成する方法](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure には、ストレージ アカウント内のリソースに対するクライアントのアクセスをきめ細かく制御する Azure ロールベースのアクセス制御 (Azure RBAC) が用意されています。   セキュリティのベスト プラクティスとして、より侵害されやすいアカウント キーを使用するのではなく、可能な限り Azure AD 資格情報を使用します。 アプリケーション設計において、BLOB ストレージへのアクセスのために Shared Access Signature が必要な場合は、セキュリティを強化するために、可能な限り、Azure AD 資格情報を使用してユーザー委任 Shared Access Signature (SAS) を作成してください。

- [Azure AD インスタンスを作成して構成する方法](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Azure Storage リソース プロバイダーを使用して管理リソースにアクセスする](authorization-resource-provider.md)

- [Azure portal で Azure RBAC を使用して Azure Blob とキューのデータへのアクセスを構成する方法](storage-auth-aad-rbac-portal.md)

- [Azure Storage 内のデータへのアクセスの承認](storage-auth.md)

- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](storage-sas-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure Active Directory (Azure AD) ログを確認して、古いアカウントを検出します。これには、ストレージ アカウントの管理者ロールを使用するアカウントが含まれる可能性があります。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、ストレージ アカウント リソースへのアクセスに使用される可能性のあるエンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 適切なユーザーのみが継続的なアクセス権を持っていることを確認するために、ユーザー アクセスを定期的に確認する必要があります。 
 

 
また、Shared Access Signature (SAS) を使用して、データのセキュリティを損なうことなく、ストレージ アカウント内のリソースへのセキュリティで保護された委任アクセスを提供することもできます。 パラメーターの中でも、クライアントがアクセスできるリソース、それらのリソースに対して持つアクセス許可、SAS の有効期間などを制御できます。
 

 
また、コンテナーと BLOB への匿名読み取りアクセスについても確認します。 既定では、コンテナーとその中のすべての BLOB には、適切なアクセス許可を与えられたユーザーのみがアクセスできます。 Azure Monitor を使用して、匿名認証の条件を使用してストレージ アカウントへの匿名アクセスに対するアラートを生成できます。
 

 
予想外のユーザー アカウント アクセスのリスクを軽減するための効果的な方法の 1 つは、ユーザーに付与するアクセスの期間を制限することです。 期間限定の SAS URI は、ストレージ アカウントへのユーザー アクセスを自動的に有効期限切れにするための 1 つの効果的な方法です。 さらに、ストレージ アカウント キーを頻繁に交換することで、ストレージ アカウント キーを使用した予期しないアクセスの期間が限定されることを保証できます。
 

 
- [Azure AD のレポートについて](/azure/active-directory/reports-monitoring/) 
 

 
- [Azure Storage アカウント レベルでアクセスを表示および変更する方法](storage-auth-aad-rbac-portal.md)
 

 
- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](storage-sas-overview.md)
 

 
- [コンテナーと BLOB への匿名読み取りアクセスを管理する](../blobs/anonymous-read-access-configure.md)
 

 
- [Azure Portal でのストレージ アカウントの監視](manage-storage-analytics-logs.md)
 

 
- [ストレージ アカウント アクセス キーを管理する](storage-account-keys-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Storage Analytics を使用して、ストレージ サービスに対する要求の成功と失敗についての詳細な情報をログに記録します。 すべてのログは、Storage Analytics をストレージ アカウントに対して有効にしたときに自動的に作成される $logs という名前のコンテナー内のブロック BLOB に格納されます。
 

Azure Active Directory (Azure AD) ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペースで必要なアラートを構成できます。

Azure Storage アカウントに対する認証エラーを監視するために、ストレージ リソースのメトリックが特定のしきい値に達したときに通知するアラートを作成できます。 さらに、Azure Monitor を使用して、匿名認証の条件を使用してストレージ アカウントへの匿名アクセスに対するアラートを生成できます。

- [Azure Storage Analytics のログ](storage-analytics-logging.md)
 

- [Azure アクティビティ ログを Azure Monitor に統合する方法](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)
 

- [Azure Storage アカウントのメトリック アラートを構成する方法](manage-storage-analytics-logs.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**: ストレージ アカウント リソースに関連して検出された疑わしいアクションへの自動応答を構成するには、Azure Active Directory (Azure AD) のリスクおよび ID 保護機能を使用します。 組織のセキュリティ対応を実装するには、Azure Sentinel によって自動応答を有効にする必要があります。

- [Azure AD の危険なサインインを表示する方法](../../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: Microsoft が顧客データにアクセスする必要のあるサポート シナリオでは、カスタマー ロックボックス (ストレージ アカウントのプレビュー) に、顧客が顧客データへのアクセス要求を確認し、承認または拒否するためのインターフェイスが用意されています。 Microsoft によって、ストレージ アカウント内に格納されている組織のシークレットへのアクセスが必要になったり、要求されたりすることはありません。

- [カスタマー ロックボックスについて](../../security/fundamentals/customer-lockbox-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理するストレージ アカウント リソースを追跡しやすくするには、タグを使用します。 

- [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: 環境やデータの機密度など、個々のセキュリティ ドメイン用の個別のサブスクリプション、管理グループ、ストレージ アカウントを使用して分離を実装します。  ストレージ アカウントを制限して、使用されるネットワークの種類とサブセットに基づいて、アプリケーションやエンタープライズ環境で求められるストレージ アカウントへのアクセス レベルを制御できます。 ネットワーク ルールを構成すると、指定したネットワークのセットを経由してデータを要求しているアプリケーションのみが、ストレージ アカウントにアクセスできます。 Azure RBAC (AzureRBAC) を使用して Azure Storage へのアクセスを制御できます。

また、プライベート エンドポイントを構成してセキュリティを強化することもできます。仮想ネットワークとサービスの間のトラフィックは Microsoft のバックボーン ネットワークを経由することで、パブリック インターネットからの露出が排除されます。

- [追加の Azure サブスクリプションを作成する方法](../../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

- [Azure Storage ファイアウォールおよび仮想ネットワークを構成する](storage-network-security.md)

- [仮想ネットワーク サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 機密情報を格納または処理するストレージ アカウント リソースの場合は、タグを使用してリソースを機密としてマークします。 窃盗によるデータ損失のリスクを軽減するには、Azure Firewall を使用して Azure Storage アカウントに対する送信ネットワーク トラフィックを制限します。  

さらに、仮想ネットワーク サービス エンドポイント ポリシーを使用すると、サービス エンドポイント経由の Azure Storage アカウントの仮想ネットワーク エグレス トラフィックをフィルター処理し、データ流出を特定の Azure Storage アカウントのみに許可することができます。

- [Azure Storage ファイアウォールおよび仮想ネットワークを構成する](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Azure Storage の仮想ネットワーク サービス エンドポイント ポリシー](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Azure での顧客データの保護について](../../security/fundamentals/protection-customer-data.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: ストレージ アカウントの [安全な転送が必須] を有効にすると、HTTPS の使用を強制することができます。 このオプションを有効にすると、HTTP を使った接続は拒否されます。  さらに、Azure Security Center と Azure Policy を使用して、ストレージ アカウントの安全な転送を強制します。

- [Azure Storage で安全な転送を要求する方法](storage-require-secure-transfer.md)

- [Security Center により監視される Azure セキュリティ ポリシー](../../security-center/policy-reference.md)

**責任**: 共有

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 4.4](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Storage アカウントと関連リソースでは、データ特定機能をまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。 

- [Azure での顧客データの保護について](../../security/fundamentals/protection-customer-data.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**:Azure Active Directory (Azure AD) では、Azure ロールベースのアクセス制御 (Azure RBAC) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 BLOB またはキュー データへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure 組み込み RBAC ロールは、Azure Storage によって定義されます。

- [Azure Storage アカウントに Azure RBAC ロールを割り当てる方法](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Azure Storage リソース プロバイダーを使用して管理リソースにアクセスする](authorization-resource-provider.md)

- [Azure portal で Azure RBAC を使用して Azure Blob とキューのデータへのアクセスを構成する方法](storage-auth-aad-rbac-portal.md)

- [Azure AD インスタンスを作成して構成する方法](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Storage 内のデータへのアクセスの承認](storage-auth.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**:Azure Storage 暗号化はすべてのストレージ アカウントに対して有効になり、無効にすることはできません。 Azure Storage では、データがクラウドに永続化されるときに自動的に暗号化されます。 Azure Storage からデータを読み取ると、Azure Storage によって復号化されてから返されます。 Azure Storage の暗号化を使用すると、コードを変更したり、アプリケーションにコードを追加したりする必要なしに、保存データをセキュリティで保護できます。 

- [Azure Storage の保存時の暗号化について](storage-service-encryption.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure アクティビティ ログで Azure Monitor を使用して、ストレージ アカウント リソースに変更が加えられたときのアラートを作成します。  また、Azure Storage のログ記録を有効にして、Azure Storage に対する各要求がどのように承認されたかを追跡することもできます。 このログでは、OAuth 2.0 トークン、共有キー、または共有アクセス署名 (SAS) を使用して、要求が匿名で行われたかどうかが示されます。  さらに、Azure Monitor を使用して、匿名認証の条件を使用してストレージ アカウントへの匿名アクセスに対するアラートを生成できます。 

 
- [Azure アクティビティ ログ イベントのアラートを作成する方法](../../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Azure Storage Analytics のログ](storage-analytics-logging.md) 

 
- [Azure Storage アカウントのメトリック アラートを構成する方法](manage-storage-analytics-logs.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:Azure Security Center からの推奨事項に従って、ストレージ アカウントの構成を継続的に監査および監視します。  

- [セキュリティの推奨事項 - リファレンス ガイド](../../security-center/recommendations-reference.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**:適用できません。Microsoft により、ストレージ アカウントをサポートしている基になるシステムに対して脆弱性の管理が実行されます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: Azure Security Center によって提供される既定のリスク評価 (セキュリティ スコア) を使用します。 

- [Azure Security Center のセキュリティ スコアについて](/azure/security-center/security-center-secure-score)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (ストレージ アカウントを含む) のクエリや検出を行います。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

- [Azure Graph を使用してクエリを作成する方法](../../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供するストレージ アカウント リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。 

- [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用し、ストレージ アカウントおよび関連リソースの整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。 

また、Azure Defender for Storage を使用して、承認されていない Azure リソースを検出します。 

- [追加の Azure サブスクリプションを作成する方法](../../cost-management-billing/manage/create-subscription.md) 

- [管理グループを作成する方法](../../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

- [Azure Defender for Storage を構成する](azure-defender-storage-configure.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースのインベントリを作成する必要があります。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。 

 
 - 許可されないリソースの種類 
 
 - 許可されるリソースの種類 
 

 
また、Azure Resource Graph を使用すると、サブスクリプション内のリソースのクエリまたは検出を行えます。 これは、高いセキュリティに基づいた環境 (ストレージ アカウントを使用するものなど) に役立ちます。 
 

 
- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md) 
 

 
- [Azure Graph を使用してクエリを作成する方法](../../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: お客様は、お客様の会社のポリシーで求められているように、Azure Policy を使用してリソースの作成や使用を防ぐことができます。 

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類
- 許可されるリソースの種類

追加情報については、参照先のリンクをご覧ください。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicStorage**:

[!INCLUDE [Resource Policy for Microsoft.ClassicStorage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classicstorage-6-9.md)]

**Azure Policy 組み込み定義 - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-6-9.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure Conditional Access を使用して Azure Resource Manager を操作するユーザーの権限を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。 これにより、ストレージ アカウントを使用するリソースなど、高セキュリティ環境内のリソースの作成と変更を防ぐことができます。 

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: ストレージ アカウント インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、**Microsoft.Storage** 名前空間で Azure Policy エイリアスを使用します。 Azure Storage アカウントに次のような組み込みの Azure Policy 定義を使用することもできます。

- ストレージ アカウントに対する制限のないネットワーク アクセスの監査
- Azure Defender for Storage のデプロイ
- ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある
- ストレージ アカウントへの安全な転送を有効にする必要がある

ストレージ アカウントの安全な構成基準として Azure Security Center の推奨事項を使用します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: ストレージ アカウント リソース全体にセキュリティで保護された設定を適用するには、Azure Policy [拒否] と [存在する場合はデプロイする] を使用します。 

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy の効果について](../../governance/policy/concepts/effects.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタム Azure ポリシー、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなど、ご利用のコードを安全に格納して管理するには、Azure Repos を使用します。Azure DevOps で管理するリソースにアクセスするには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Azure AD で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: Azure Policy を利用して、ストレージ アカウントのシステム構成をアラート、監査、および適用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。 

- [ Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: Azure Security Center を利用して、Azure Storage アカウント リソースのベースライン スキャンを実行します。 

- [Azure Security Center の推奨事項を修復する方法](../../security-center/security-center-remediate-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure Storage では、データはクラウドに永続化されるときに自動的に暗号化されます。 Microsoft によって管理されるキーを使用してストレージ アカウントを暗号化することも、独自のキーで暗号化を管理することもできます。 顧客が指定したキーを使用している場合は、Azure Key Vault を利用してキーを安全に格納することができます。 

さらに、ストレージ アカウント キーを頻繁に交換して、ストレージ アカウント キーの紛失や漏えいの影響を軽減します。

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)

- [ストレージ アカウント アクセス キーを管理する](storage-account-keys-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:Azure Active Directory (Azure AD) とマネージド ID を使用して、Azure Storage アカウント内の BLOB およびキューへのアクセスを承認します。 Azure Blob と Queue storage は、Azure リソースのマネージド ID を使用して Azure AD 認証をサポートします。 

Azure リソースのマネージド ID により、Azure 仮想マシンで実行されているアプリケーション、関数アプリ、仮想マシン スケール セット、およびその他のサービスから Azure AD 資格情報を使用して、BLOB およびキューのデータへのアクセスを認証することができます。 Azure リソースのマネージド ID を Azure AD 認証と一緒に使用することで、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。

- [マネージド ID を使用して Azure BLOB とキュー データへのアクセス権を付与する方法](storage-auth-aad-rbac-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [ 資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Azure Defender for Storage を使用して、ハッシュ評価分析およびアクティブな Tor 出口ノード (匿名化プロキシ) からの疑わしいアクセスを使用して、Azure Storage へのマルウェアのアップロードを検出します。 
 

 
また、App Service、Data Lake Storage、Blob Storage、その他などのコンピューティング以外の Azure リソースにアップロードする前に、マルウェアの内容を事前にスキャンして、組織の要件を満たすこともできます。
 

 
- [Azure Defender for Storage を構成する](azure-defender-storage-configure.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: Microsoft Azure Storage アカウント内のデータは、持続性と高可用性を保証するため、常に自動的にレプリケートされます。 Azure Storage では、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止または停電、大規模な自然災害など) から保護するためにデータがコピーされます。 同じデータ センター内、同じリージョン内の複数のゾーン データ センター間、または地理的に分離されたリージョン間でデータをレプリケートすることもできます。 

また、Azure Automation を有効にして、BLOB のスナップショットを定期的に取得することもできます。

- [Azure Storage の冗長性とサービス レベル アグリーメントについて](storage-redundancy.md)

- [BLOB のスナップショットを作成する](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure Automation の概要](../../automation/automation-intro.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: ストレージ アカウントでサポートされているサービスからデータをバックアップするには、azcopy やサードパーティ製のツールを使用するなど、複数の方法を使用できます。 Azure Blob Storage の不変ストレージを使用すると、ユーザーはビジネスに不可欠なデータ オブジェクトを WORM (Write Once Read Many) 状態で保存できます。 この状態では、ユーザーが指定した期間、データを消去および変更できなくなります。
 

顧客が管理または指定するキーは、Azure CLI または PowerShell を使用して Azure Key Vault 内でサポートできます。 

 
- [AzCopy を使ってみる](storage-use-azcopy-v10.md)  

- [BLOB ストレージの不変ポリシーを設定および管理する](../blobs/storage-blob-immutability-policies-manage.md)
 

 
- [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: 次の PowerShell コマンドを使用して、Key Vault の証明書、キー、マネージド ストレージ アカウント、およびシークレットのデータ復元を定期的に行います。

Restore-AzKeyVaultCertificate

Restore-AzKeyVaultKey

Restore-AzKeyVaultManagedStorageAccount

Restore-AzKeyVaultSecret

- [Key Vault の証明書を復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Key Vault のキーを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Key Vault のマネージド ストレージ アカウントを復元する方法](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault のシークレットを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [AzCopy は、ストレージ アカウント間での BLOB、ファイル、テーブル データのコピーに利用できるコマンドライン ユーティリティです](storage-use-azcopy-v10.md)

注:Azure Table Storage サービスとの間でデータをコピーする場合、AzCopy バージョン 7.3 をインストールしてください。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**:ストレージ アカウントでカスタマー マネージド キーを有効にするには、Azure Key Vault を使用してキーを格納する必要があります。 キー コンテナーで [論理的な削除] と [Do Not Purge]\(消去しない\) の両方のプロパティを有効にする必要があります。  キー コンテナーの [論理的な削除] 機能では、削除されたコンテナーと、キー、シークレット、証明書などのコンテナー オブジェクトを復元できます。  ストレージ アカウント データを Azure Storage BLOB にバックアップする場合、BLOB または BLOB のスナップショットが削除されたときに、論理的な削除機能でデータを保存および復旧することができます。  バックアップを機密データとして扱い、このベースラインの一部として関連するアクセスとデータ保護制御を適用する必要があります。  さらに、保護を強化するために、ビジネスに不可欠なデータ オブジェクトを WORM (Write Once, Read Many) 状態で格納することもできます。

- [Azure Key Vault の論理的な削除を使用する方法](../../key-vault/general/key-vault-recovery.md)

- [Azure Storage Blob の論理的な削除](../blobs/soft-delete-blob-overview.md)

- [不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](../blobs/storage-blob-immutable-storage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST のコンピューター セキュリティ インシデント処理ガイド](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。 

 
また、サブスクリプション ( 運用、非運用など) をタグを使用して明確にマークし、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。
 

 
- [Security alerts in Azure Security Center](../../security-center/security-center-alerts-overview.md)
 

 
- [タグを使用した Azure リソースの整理](../../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物 - IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して "Logic Apps" で自動的に応答をトリガーし、Azure リソースを保護できます。
    

- [ワークフローの自動化と Logic Apps を構成する方法](../../security-center/workflow-automation.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
