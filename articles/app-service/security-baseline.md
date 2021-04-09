---
title: App Service 用の Azure セキュリティ ベースライン
description: App Service セキュリティ ベースラインによって、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3193acf8ef19cdac97f6733a657610801d614f32
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952305"
---
# <a name="azure-security-baseline-for-app-service"></a>App Service 用の Azure セキュリティ ベースライン

App Service 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。 このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) に基づいて作成されています。 内容は、App Service に適用される Azure セキュリティ ベンチマークと関連ガイダンスで定義されている **セキュリティ コントロール** によってグループ化されています。 App Service に適用できない **コントロール** は、除外されています。

App Service を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な App Service セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:Isolated 価格レベル (App Service Environment (ASE) とも呼ばれます) で App Service を使用する場合、Azure Virtual Network 内のサブネットに直接デプロイできます。 ネットワーク セキュリティ グループを使用して、Azure App Service Environmen を保護 (仮想ネットワーク内のリソースに対する受信トラフィックと送信トラフィックをブロック) したり、App Service Environment 内のアプリへのアクセスを制限したりします。

既定では、ネットワーク セキュリティ グループには、優先順位が最も低い暗黙的な拒否規則が設定されているので、明示的な許可規則を追加する必要があります。 最小特権のネットワーク アプローチに基づいて、ネットワーク セキュリティ グループの許可規則を追加します。 App Service Environment をホストするために使用される基になる仮想マシンは、Microsoft が管理するサブスクリプション内にあるため、直接アクセスできません。

Web アプリケーション ファイアウォール (WAF) が有効になっている Azure Application Gateway 経由でトラフィックをルーティングすることによって、App Service Environment を保護します。 サービス エンドポイントを Application Gateway と共に使用して、アプリへの受信発行トラフィックを保護します。  

マルチテナント App Service (Isolated レベルではないアプリ) では、ネットワーク セキュリティ グループを使用して、アプリからの送信トラフィックをブロックします。 仮想ネットワーク統合機能を使用して、アプリが仮想ネットワーク内で、またはこれを経由してリソースにアクセスできるようにします。 この機能を使用して、アプリからパブリック アドレスへの送信トラフィックをブロックすることもできます。 仮想ネットワーク統合を使用して、アプリへの受信アクセスを提供することはできません。  

次を使用して、アプリへの受信トラフィックを保護します。
- アクセス制限 - 受信アクセスを制御する一連の許可または拒否の規則
- サービス エンドポイント - 指定した仮想ネットワークまたはサブネットの外部からの受信トラフィックを拒否できます。
- プライベート エンドポイント - プライベート IP アドレスを使用して仮想ネットワークにアプリを公開します。 アプリでプライベート エンドポイントが有効になっていると、そのアプリにはインターネットからアクセスできません。

同じリージョンの仮想ネットワークで仮想ネットワーク統合機能を使用する場合は、ネットワーク セキュリティ グループとユーザー定義のルートが含まれるルート テーブルを使用します。 ユーザー定義のルートを統合サブネットに配置して、意図したとおりに送信トラフィックを送信できます。  

サブスクリプションと仮想ネットワーク全体でアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、および記録するために、Azure Firewall を実装することを検討してください。 Azure Firewall では、仮想ネットワーク リソースに静的パブリック IP アドレスが使用されます。これにより、外部ファイアウォールは、仮想ネットワークからのトラフィックを識別できます。 

- [App Service 環境をロックする](environment/firewall-integration.md)

- [Open Web Application Security Project (OWASP) の上位 10 の脆弱性の保護](https://owasp.org/www-project-top-ten/)

- [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)

- [アプリを Azure 仮想ネットワークに統合する](web-sites-integrate-with-vnet.md)

- [App Service Environment のネットワークの考慮事項](environment/network-info.md)

- [外部 ASE を作成する方法](environment/create-external-ase.md)

- [内部 ASE を作成する方法](environment/create-ilb-ase.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

**Azure Policy 組み込み定義 - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**:Azure Security Center のネットワーク保護に関する推奨事項に従って、App Service アプリと API に関連するネットワーク リソースと構成を保護します。

Azure Firewall を使用して、トラフィックを送信し、サブスクリプションと仮想ネットワーク全体でアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、および記録します。 Azure Firewall では、仮想ネットワーク リソースに静的パブリック IP アドレスが使用されます。これにより、外部ファイアウォールは、仮想ネットワークからのトラフィックを識別できます。 また、Azure Firewall サービスは、ログ記録と分析のために Azure Monitor と完全統合されます。

- [Azure Firewall の概要](../firewall/overview.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

- [App Service の監視と保護を有効にする方法](../security-center/defender-for-app-service-introduction.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**:次の方法で、インターネットにアクセスできるアプリを App Service Environment (ASE) で保護します。
- インターネットに接続するアプリの前面に Azure Application Gateway を備えた Web アプリケーション ファイアウォール (WAF) を配置する
- アクセス制限を使用して Application Gateway への受信トラフィックを保護する
- Azure Active Directory (Azure AD) を使用して認証を確実に行うことでアプリを保護する
- TLS の最小バージョンを 1.2 に設定する
- アプリを HTTPS のみに設定する

すべてのアプリケーション トラフィックを Azure Firewall デバイス経由で外部に送信し、ログを監視します。 

マルチテナント App Service でインターネットにアクセス可能なアプリ (Isolated レベルではないもの) を保護するには、次のようにします。
- Web アプリケーション ファイアウォールが有効になったデバイスをアプリの前面に配置する
- アクセス制限またはサービス エンドポイントを使用して Web アプリケーション ファイアウォール (WAF) デバイスへの受信トラフィックを保護する
- Azure AD を使用して認証を確実に行うことでアプリを保護する
- TLS の最小バージョンを 1.2 に設定する
- アプリを HTTPS のみに設定する
- 仮想ネットワーク統合とアプリ設定 WEBSITE_VIRTUAL NETWORK_ROUTE_ALL を使用して、すべての送信トラフィックが、ネットワーク セキュリティ グループと統合サブネット上のユーザー定義ルートに従うようにします。

Application Service Environment アプリと同様に、すべてのアプリケーション トラフィックを Azure Firewall デバイス経由で外部に送信し、アプリのログを監視します。

さらに、「App Service 環境をロックする」というドキュメントで推奨事項を確認し、それに従ってください。

- [App Service 環境をロックする](environment/firewall-integration.md)

- [Azure Application Gateway 上の Azure Web Application Firewall](../web-application-firewall/ag/ag-overview.md)

- [Azure App Service のアクセス制限](app-service-ip-restrictions.md)

- [Azure Monitor を使用して WAF アラートを追跡し、傾向を簡単に監視する](../azure-monitor/overview.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:「App Service 環境をロックする」というドキュメントの説明に従って App Service Environment を保護します。 Azure Security Center の統合された脅威インテリジェンス機能を適用して、既知の悪意のあるパブリック IP アドレス、または未使用のパブリック IP アドレスとの通信を拒否します。 アクセス制限を使用して Application Gateway への受信トラフィックを保護します。 

インターネットに接続するパブリック エンドポイントを使用して、マルチテナント App Service (Isolated レベルではないアプリ) を保護します。 仮想ネットワーク内の特定のサブネットからのトラフィックのみを許可し、他のすべてのトラフィックをブロックします。 アクセス制限を使用して、許可される受信トラフィックをロック ダウンするようにネットワーク アクセス制御リスト (IP 制限) を構成します。

アプリへのネットワーク アクセスを管理するために、順序付けされた許可リストまたは拒否リストに対して優先順位を定義します。 このリストには、IP アドレスまたは仮想ネットワークのサブネットを含めることができます。 リストに 1 つ以上のエントリが含まれる場合、リストの末尾には暗黙的な "すべて拒否" の規則が存在します。 この機能は、App Service でホストされているすべてのワークロードに対して動作します。これには、Web アプリ、API アプリ、Linux アプリ、Linux コンテナー アプリ、関数が含まれます。 

サービス エンドポイントを使用して、Azure Virtual Network から Web アプリへのアクセスを制限します。 サービス エンドポイントを使用して、選択したサブネットからマルチテナント App Service (Isolated レベルではないアプリ) へのアクセスを制限します。 

- [Azure App Service 静的 IP 制限](app-service-ip-restrictions.md)

- [Azure Application Gateway 上の Azure Web Application Firewall](../web-application-firewall/ag/ag-overview.md)

- [App Service Environment 向けに Web アプリケーション ファイアウォール (WAF) を構成する方法](environment/app-service-app-service-environment-web-application-firewall.md)

- [「App Service 環境をロックする」の説明に従って ASE を保護する](environment/firewall-integration.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:Security Center を使用して App Service アプリとの間で送受信される要求と応答を監視します。 Web アプリケーションに対する攻撃を監視するには、Web アプリケーション ファイアウォールを備えたリアルタイム Application Gateway を使用します。Azure Monitor の統合ログを使用できるため、Web アプリケーション ファイアウォールのアラートを追跡し、傾向を簡単に監視できます。

- [Azure Application Gateway 上の Azure Web アプリケーション ファイアウォール](../web-application-firewall/ag/ag-overview.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**:App Service Environment でアプリのトラフィックを管理します。

- 「App Service 環境をロックする」の説明に従って App Service Environment を保護する
- インターネットに接続するアプリの前面に Azure Web Application Firewall を備えた Application Gateway を配置する
- HTTPS を介してのみアプリにアクセスできるように設定する

マルチテナント App Service でインターネットにアクセス可能なアプリ (Isolated レベルではないもの) のトラフィックを管理します。 

- インターネットに接続するアプリの前面に Azure Web Application Firewall が有効になっている Application Gateway を配置する
- アクセス制限またはサービス エンドポイントを使用して、Web Application Firewall への受信トラフィックを保護する。 アクセス制限機能は、App Service でホストされているすべてのワーク ロードに対して動作します。これには、Web アプリ、API アプリ、Linux アプリ、Linux コンテナー アプリ、関数が含まれます。

- HTTPS を介してのみアプリにアクセスできるように設定する
- 静的 IP 制限を使用して App Service アプリへのアクセスを制限して、アクセス権を持つ唯一のアドレスとしてアプリケーション ゲートウェイの VIP からのトラフィックのみを受信するようにする。

参照先のリンクで追加情報を確認してください。

- [Azure App Service 静的 IP 制限](app-service-ip-restrictions.md)

- [Azure Application Gateway 上の Azure Web Application Firewall](../web-application-firewall/ag/ag-overview.md)

- [ポータルで Application Gateway を使用してエンド ツー エンド TLS を構成する方法](../application-gateway/end-to-end-ssl-portal.md)

- [「App Service 環境をロックする」の説明に従って ASE を保護する](/azure/app-service/environment/firewall-integrationEnvironment:)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:App Service には、サービスの管理に使用されるエンドポイントがいくつも存在します。 これらのエンドポイント アドレスは、AppServiceManagement IP サービス タグにも含まれます。 AppServiceManagement タグは、こうしたトラフィックを許可するために App Service Environment でのみ使用されます。 

規則の適切なソースまたはターゲット フィールドにサービス タグ名を指定することで、対応するサービスのトラフィックを許可または拒否できます。 App Service の受信アドレスは、AppService IP サービス タグで追跡されます。 App Service によって使用される送信アドレスが含まれる IP サービス タグはありません。

サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

- [仮想ネットワーク サービス タグ](../virtual-network/service-tags-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**:App Service アプリに関連するネットワーク設定の標準的なセキュリティ構成を定義して実装します。 

"Microsoft.Web" および "Microsoft.Network" の名前空間で Azure Policy エイリアスを使用して、セキュリティ構成を維持します。 App Service アプリのネットワーク構成を監査または適用するために、カスタム ポリシーを作成します。 

次のような App Service 用の組み込みポリシー定義を使用します。
- アプリは仮想ネットワーク サービス エンドポイントを使用する必要がある
- アプリには HTTPS を介してのみアクセスできる
- TLS の最小バージョンを現在のバージョンに設定する

参照先のリンクで追加情報を確認してください。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [ポータルで Application Gateway を使用してエンド ツー エンド TLS を構成する方法](../application-gateway/end-to-end-ssl-portal.md)

- [「App Service 環境をロックする」の説明に従って ASE を保護する](/azure/app-service/environment/firewall-integrationEnvironment:)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:ネットワーク セキュリティ グループとその他の関連リソース (App Service のトラフィック フローなど) に対してタグを使用します。

個々のネットワーク セキュリティ グループ規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可するすべての規則のビジネス ニーズや期間などを指定します。

すべてのリソースがタグ付きで作成され、タグ付けされていない既存のリソースがユーザーに通知されるようにするために、タグ付け処理に関連したすべての組み込み Azure Policy 定義 ("タグとその値が必要" など) を適用します。 Azure PowerShell または Azure CLI を選択して、タグに基づいたリソースの検索やアクションを実行します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [Azure App Service のアクセス制限](/azure/app-service/app-service-ip-restriction)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、App Service に関連したネットワーク設定およびリソースへの変更を検出します。 

仮想ネットワーク エンドポイント サービスの使用についてアプリを監査するポリシーなど、App Service のいくつかの Azure Policy 組み込み定義のいずれかを適用します。 重要なネットワーク設定またはリソースが変更されるとトリガーされるアラートを Azure Monitor 内で作成します。 

Security Center、ポータル、またはプログラム ツールを使用して、セキュリティに関する詳細なアラートと推奨事項を確認します。 この情報をエクスポートするか、または環境内の他の監視ツールに送信します。 アラートと推奨事項を手動でエクスポートするため、または継続的かつ連続的にエクスポートするために、ツールを利用できます。 これらのツールを使用すると、次のことができます。
 
- Log Analytics ワークスペースへの連続エクスポート
- Azure Event Hubs への連続エクスポート (サードパーティ SIEM との統合のため)
- CSV ファイルへのエクスポート (1 回限り)

ネットワーク リソースの構成を監視し、変更を迅速に検出するために、自動ツールを備えたプロセスを作成することをお勧めします。

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

- [セキュリティ アラートと推奨事項のエクスポート](../security-center/continuous-export.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:App Service Environment (ASE) を Azure Monitor と統合して、ログを Azure Storage、Azure Event Hubs、または Log Analytics に送信します。 コントロール プレーンの監査ログのために Azure アクティビティ ログ診断設定を有効にします。 Security Center からのセキュリティ アラートは、Azure アクティビティ ログに発行されます。 Azure アクティビティ ログのデータを監査します。これにより、Azure App Service とその他の Azure リソースのコントロール プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定できます。 将来使用するためにクエリを保存し、クエリ結果を Azure ダッシュボードにピン留めし、ログ アラートを作成します。 また、Application Insights の Data Access REST API を使用して、プログラムで利用統計情報にアクセスします。

ビジネス要件に基づいて、さまざまなデータ ソースやコネクタに接続するために使用できる、スケーラブルなクラウド ネイティブのセキュリティ情報イベント管理 (SIEM) システムである Microsoft Azure Sentinel を使用します。 また、Azure Marketplace の Barracuda など、サードパーティのセキュリティ情報イベント管理 (SIEM) システムに対してデータを有効にしてオンボードできます。

- [ASE アクティビティのログ記録](./environment/using-an-ase.md#logging)

- [Azure App Service の診断設定を有効にする方法](troubleshoot-diagnostic-logs.md)

- [Application Insights を有効にする方法](../azure-monitor/app/app-insights-overview.md)

- [Application Insights からのテレメトリのエクスポート](../azure-monitor/app/export-telemetry.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:App Service のコントロール プレーン監査ログのために Azure アクティビティ ログ診断設定を有効にします。 Log Analytics ワークスペース、Azure Event Hub、または Azure Storage アカウントにログを送信します。

App Service とその他の Azure リソースの Azure アクティビティ ログ データを使用して、コントロール プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定できます。

さらに、Azure Key Vault により、アクセス ポリシーと監査履歴を使用した一元的なシークレット管理が提供されます。 

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure App Service の診断設定を有効にする方法](troubleshoot-diagnostic-logs.md)

- [Resource Manager の操作](../role-based-access-control/resource-provider-operations.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Monitor で、組織のコンプライアンス規則に従って、App Service リソースに関連付けられている Log Analytics ワークスペースのログの保持期間を設定します。
- [ログ保持期間のパラメーターを設定する方法](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**:Log Analytics ワークスペースに送信されるログを使用して、App Service リソースの Azure アクティビティ ログの診断設定を確認します。 Log Analytics でクエリを実行して、用語の検索、傾向の特定、パターンの分析を行い、収集されたデータに基づいて他の多くの分析情報を提供します。

App Service アプリに対して Application Insights を使用して、ログ、パフォーマンス、エラーのデータを収集します。 Azure portal 内で Application Insights によって収集された利用統計情報を表示します。

Web アプリケーション ファイアウォール (WAF) を配置している場合は、リアルタイムの Web アプリケーション ファイアウォール ログを使用して、App Service アプリに対する攻撃を監視できます。 このログは Azure Monitor と統合されているため、Web アプリケーション ファイアウォールのアラートを追跡し、傾向を簡単に監視できます。

スケーラブルなクラウド ネイティブのセキュリティ情報イベント管理 (SIEM) システムである Azure Sentinel を使用して、要件に従ってさまざまなデータ ソースおよびコネクタを統合します。 必要に応じて、Azure Marketplace のサードパーティのセキュリティ情報イベント管理ソリューションに対してデータを有効にしてオンボードできます。

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Application Insights を有効にする方法](../azure-monitor/app/app-insights-overview.md)

- [App Service Environment を Azure Application Gateway (WAF) と統合する方法](environment/integrate-with-application-gateway.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Azure サブスクリプションで Security Center を構成し、生成されたアラートを確認します。 Azure Monitor を使用して、アクティビティ ログ データをイベント ハブに送ります。これは、このイベント ハブで、Azure Sentinel などのセキュリティ情報イベント管理 (SIEM) ソリューションを使用して読み取ることができます。 

Azure Web Application Firewall (WAF) を配置している場合は、リアルタイムの Web アプリケーション ファイアウォール ログを使用して、App Service アプリに対する攻撃を監視します。 このログは Azure Monitor と統合されているため、Web アプリケーション ファイアウォール (WAF) のアラートを追跡し、傾向を簡単に監視できます。

- [App Service Environment を Azure Application Gateway (WAF) と統合する方法](environment/integrate-with-application-gateway.md)

- [セキュリティ アラートと推奨事項のエクスポート](../security-center/continuous-export.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:Azure Active Directory (Azure AD) の組み込みロールは、明示的に割り当てる必要があり、クエリ可能です。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [App Service と Azure Functions でマネージド ID を使用する方法](./overview-managed-identity.md?tabs=dotnet&context=azure%2factive-directory%2fmanaged-identities-azure-resources%2fcontext%2fmsi-context)

- [Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory (Azure AD) には、既定のパスワードの概念がありません。 App Service へのコントロール プレーン アクセスが提供されます。

一般的に、独自のアプリを構築するときは、ユーザー アクセスのための既定のパスワードを実装しないでください。 Azure AD、Microsoft アカウント、Facebook、Google、Twitter など、App Service に対して既定で使用できる ID プロバイダーのいずれかを使用します。

匿名アクセスは、サポートする必要がある場合を除いて、無効にします。 

- [Azure App Service で既定で使用できる ID プロバイダー](./overview-authentication-authorization.md#identity-providers)

- [Azure App Service および Azure Functions での認証と承認](overview-authentication-authorization.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Security Center の ID およびアクセス管理機能を使用して、管理アカウントの数を監視および追跡します。 

Security Center または組み込みの Azure ポリシーの推奨事項を使用します。次に例を示します。

- サブスクリプションには複数の所有者を割り当てる。 
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

ネットワーク リソースの構成を監視し、管理アカウントの変更を検出するプロセスを作成します。

- [Azure Security Center を使用して ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

- [Azure Policy を使用する方法](../governance/policy/tutorials/create-and-manage.md)

- [アプリケーションへのアクセス権をユーザーに付与することについて詳しく学習する](../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**:Azure Active Directory (Azure AD) を使用して App Service を認証します。 ID プロバイダーのために OAuth 2.0 サービスが提供され、モバイルと Web のアプリケーションへの承認されたアクセスが有効になります。 

App Service アプリが使用するフェデレーション ID では、サード パーティの ID プロバイダーが代わりにユーザー ID と認証フローを管理します。 これらの ID プロバイダーは既定で利用できます。

- Azure AD
- Microsoft アカウント

- Facebook

- Google

- Twitter

これらのプロバイダーのいずれかで認証と認可を有効にすると、そのプロバイダーのサインイン エンドポイントが、ユーザー認証と、プロバイダーからの認証トークンの検証に使用できるようになります。

- [Azure App Service での認証および認可について](./overview-authentication-authorization.md#identity-providers)

- [Azure App Service での認証および認可の詳細について](overview-authentication-authorization.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure Active Directory (Azure AD) で多要素認証機能を有効にし、Security Center の ID およびアクセス管理の推奨事項に従います。

Azure AD に対して多要素認証を実装します。 管理者は、ポータルのサブスクリプション アカウントが保護されていることを確認する必要があります。 作成したリソースはサブスクリプションで管理されているため、サブスクリプションは攻撃に対して脆弱です。 

- [Azure セキュリティ多要素認証](/previous-versions/azure/security/develop/secure-aad-app)

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**:多要素認証が構成された特権アクセス ワークステーション (PAW) を使用して Azure リソースにログインし、そのリソースを構成します。 

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**:環境内で疑わしいアクティビティまたは安全ではないアクティビティが発生したときにログとアラートを生成するために、Azure Active Directory (Azure AD) で Privileged Identity Management (PIM) を使用します。

また、Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

Security Center の脅威の防止により、環境に対して包括的な防御が提供されます。これには、Windows マシン、Linux マシン、App Service、Azure コンテナーなどの Azure コンピューティング リソースのための脅威の防止が含まれます。

- [Privileged Identity Management (PIM) をデプロイする方法](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD のリスク検出の概要](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure コンピューティング リソースのための脅威の防止](../security-center/azure-defender.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**:条件付きアクセスのネームド ロケーションを使用して、IP アドレスの範囲、国、またはリージョンの特定の論理グループからのみ Azure portal へのアクセスを許可します。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:Azure Active Directory (Azure AD) を App Service アプリの中央認証および承認システムとして使用します。 Azure AD では、データの保存時と転送時に強力な暗号化を使用してデータが保護され、さらに、ユーザーの資格情報がソルト化およびハッシュされ、安全に保存されます。

- [Azure AD ログインを使用するように Azure App Service アプリを構成する方法](configure-authentication-provider-aad.md)

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure Active Directory (Azure AD) によって提供されるログを使用して古いアカウントを検出します。 Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的に確認し、意図したユーザーのみが継続的アクセス権を持っていることを確認します。 

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**:Azure Active Directory (Azure AD) を App Service アプリの中央認証および承認システムとして使用します。 Azure AD では、データの保存時と転送時に強力な暗号化を使用してデータが保護され、ユーザーの資格情報がソルト化およびハッシュされ、安全に保存されます。

Azure AD サインイン アクティビティ、監査、およびリスク イベント ログのソースへのアクセスにより、Azure Sentinel またはサード パーティのセキュリティ情報イベント管理 (SIEM) ソリューションと統合できます。 Azure AD ユーザー アカウントの診断設定を作成し、監査とサインインのログを Log Analytics ワークスペースに送信することで、このプロセスを効率化します。 Log Analytics 内で必要なログ アラートを構成できます。

- [Azure AD ログインを使用するように Azure App Service アプリを構成する方法](configure-authentication-provider-aad.md)

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**:Azure Active Directory (Azure AD) を App Service アプリの中央認証および承認システムとして使用します。 

Azure AD Identity Protection を使用して、ユーザー ID に関連する疑わしいアクション (Azure portal のコントロール プレーンでのアカウント ログイン動作の異常など) を検出するように自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。 

- [Azure AD ログインを使用するように Azure App Service アプリを構成する方法](configure-authentication-provider-aad.md)

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: 使用できません。Azure App Service ではカスタマー ロックボックスがサポートされていません。

- [カスタマー ロックボックスでサポートされているサービスの一覧](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**:機密情報を格納または処理する App Service リソースの追跡を支援するために、タグを使用します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:App Service Environment では、開発、テスト、および運用環境用に個別のサブスクリプション、管理グループ、またはこれらの両方を実装します。 機密情報を処理するアプリを、同じ方法で他のアプリから分離できます。 App Service アプリを仮想ネットワークにデプロイします。 さらにアプリケーションを分離するために、ネットワーク セキュリティ グループとサブネットを使用します。 

App Service Environment (ASE) では、次の 2 つの種類のデプロイがあります。 どちらを使用しても、ビジネス要件に基づいてトラフィックを分離できます。

- 外部 Application Service Environment - App Service Environment でホストされているアプリをインターネットからアクセス可能な IP アドレスで公開します。

-  内部ロード バランサー (ILB) Application Service Environment - App Service Environment でホストされているアプリを仮想ネットワーク内の IP アドレスで公開します。 内部エンドポイントは内部ロード バランサー (ILB) であるため、ILB ASE と呼ばれています。 

マルチテナント App Service (Isolated レベルではないアプリ) の場合、仮想ネットワーク内のリソースにアプリがアクセスするために Virtual Network 統合を使用します。  プライベート サイト アクセスを使用して、Azure Virtual network 内など、プライベート ネットワークのみからアプリにアクセスできるようにします。 Virtual Network 統合は、アプリから仮想ネットワークへの送信呼び出しを行うためだけに使用されます。 Virtual Network 統合機能は、同じリージョン内の仮想ネットワークで使用する場合と、他のリージョン内の仮想ネットワークで使用する場合とで、動作が異なります。 
 
- [App Service Environment のネットワークの考慮事項](environment/network-info.md)

- [外部 ASE を作成する方法](environment/create-external-ase.md)

- [内部 ASE を作成する方法](environment/create-ilb-ase.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**:App Service では、データの識別、分類、および損失防止の機能はまだ使用できませんが、宛先でインターネットまたは Azure サービスに関する "タグ" が使用されているすべての規則を削除することで、仮想ネットワークからデータが流出するリスクを軽減できます。 

Microsoft では、App Service 用の基になるインフラストラクチャを管理し、データの損失や漏洩を防ぐための厳格な管理を実施してきました。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:送信時にすべての情報を暗号化するために、TLS/SSL の設定で構成された既定の最小バージョンの TLS 1.2 を使用します。 また、すべての HTTP 接続要求が HTTPS にリダイレクトされるようにします。

- [Azure App Service Web Apps での送信時の暗号化について](security-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:現在は使用できません。 App Service では、データの識別、分類、損失防止の機能はまだ使用できません。 

機密情報を処理する可能性のある App Service アプリにタグを付けます。 コンプライアンスのためにサードパーティ ソリューションが必要な場合は、そのソリューションを実装します。

Microsoft では、基になるプラットフォームを管理し、顧客のすべてのデータを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くしています。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する 

**ガイダンス**:Azure Active Directory (Azure AD) の Azure ロールベース アクセス制御 (Azure RBAC) を使用して、Azure portal の App Service コントロール プレーンへのアクセスを制御します。

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**:ファイルなどの App Service アプリの Web サイト コンテンツは Azure Storage に保存され、保存時に自動的に暗号化されます。 アプリケーション シークレットを Key Vault に格納し、それらを実行時に取得することを選択します。

お客様提供のシークレットは、App Service 構成データベースに保存され、保存時に暗号化されます。

ローカルに接続されたディスクは、必要に応じて Web サイトの一時ストレージ (たとえば、D:\local や %TMP%) として使用できますが、これらのディスクは保存時に暗号化されないので注意してください。

- [Azure App Service のデータ保護のコントロールについて](./security-recommendations.md#data-protection)

- [Azure Storage の保存時の暗号化について](../storage/common/storage-service-encryption.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:Azure Monitor と Azure アクティビティ ログを使用して、運用 App Service アプリとその他の重要または関連リソースが変更されたときにアラートを作成します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:DevSecOps プラクティスを導入して、App Service アプリをセキュリティで保護し、ライフサイクル全体でそのセキュリティで保護された状態が維持されるようにします。 DevSecOps によって、組織のセキュリティ チームとその機能を DevOps プラクティスに組み込み、チームの全員がセキュリティの責任を担うようにします。

App Service アプリをセキュリティで保護するための Security Center の推奨事項を確認し、それに従ってください。

- [CI/CD パイプラインに継続的なセキュリティ検証を追加する方法](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&preserve-view=true)

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../security-center/deploy-vulnerability-assessment-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**:Microsoft では、App Service をサポートする基になるシステムの脆弱性管理を行っています。 ただし、Security Center 内の推奨事項の重大度およセキュリティ スコアを使用して、お使いの環境内のリスクを測定できます。 ご自身のセキュア スコアは、軽減した Security Center の推奨事項の数に基づきます。 最初に解決すべき推奨事項が優先されるよう、それぞれどれが重要であるか考えてください。

- [セキュリティの推奨事項のリファレンス ガイド](../security-center/recommendations-reference.md)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 テナントに適切なアクセス許可が適用されていることを確認します。また、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&preserve-view=true)

- [Azure RBAC について](../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: メタデータを使用して Azure リソースにタグを適用し、それらを各分類に論理的に整理します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用して、Azure リソースを整理および追跡します。 定期的にインベントリを調整し、このプロセスの一部として、未承認のリソースがサブスクリプションから削除されていることを確認します。

Azure Policy を選択して、次の組み込みポリシー定義を使用して、お客様のサブスクリプションで作成できるリソースの種類が制限されるようにします。

- 許可されないリソースの種類
- 許可されるリソースの種類

参照先のリンクで追加情報を確認してください。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:組織のニーズに基づき、承認済み Azure リソースとコンピューティング リソース用の承認済みソフトウェアのインベントリを作成します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。  環境に存在するすべての Azure リソースが承認されていることを確認します。 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行い、検出された Azure リソースが組織のポリシーに基づいて承認されていることを確認します。

App Service の WebJobs を使用して、未承認のソフトウェア アプリケーションがコンピューティング リソース内にデプロイされていないか監視します。 WebJobs を使用して、Web アプリ、API アプリ、またはモバイル アプリと同じインスタンスでプログラムまたはスクリプトを実行します。 WebJob 構成を定義し、ログを使用して監視します。 [WebJob Run Details]\(Web ジョブの実行の詳細\) ページで、 [出力の切り替え] を選択すると、ログの内容のテキストが表示されます。 WebJobs は、App Service on Linux ではまだサポートされていないので注意してください。

- [Azure App Service で WebJobs を使用してバックグラウンド タスクを実行する](webjobs-create.md)

- [チュートリアル - コンプライアンスを適用するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [クイック スタート - Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:環境に存在するすべての Azure リソースが承認されていることを確認します。 Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。 組織のポリシーに従って、承認されていないデプロイ済みソフトウェア アプリケーションを削除します。

- [Azure App Service で WebJobs を使用してバックグラウンド タスクを実行する](webjobs-create.md)

- [チュートリアル - コンプライアンスを適用するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [クイック スタート - Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**:環境に存在するすべての Azure リソースが承認されていることを確認します。 Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。 組織のポリシーに従って、承認されていないデプロイ済みソフトウェア アプリケーションを削除します。 

- [Azure App Service で WebJobs を使用してバックグラウンド タスクを実行する](webjobs-create.md)

- [チュートリアル - コンプライアンスを適用するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [クイック スタート - Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**:承認された Azure サービスのみがサブスクリプションで使用されるように、承認されていない Azure サービスを定期的に確認するプロセスを作成します。

Azure Resource Graph を使用して、このプロセス内で、サブスクリプション内のリソースのクエリまたは検出を行います。 環境に存在するすべての Azure リソースが承認されていることを確認します。

Azure Policy を構成して、次の組み込みポリシー定義を使用して、サブスクリプションで作成できるリソースの種類が制限されるようにします。

- 許可されないリソースの種類

- 許可されるリソースの種類

App Service の WebJobs を使用して、未承認のソフトウェア アプリケーションがコンピューティング リソース内にデプロイされていないか監視します。 WebJobs を使用して、Web アプリ、API アプリ、またはモバイル アプリと同じインスタンスでプログラムまたはスクリプトを実行します。 WebJob 構成を定義し、ログを使用して監視します。 [WebJob Run Details]\(Web ジョブの実行の詳細\) ページで、 [出力の切り替え] を選択すると、ログの内容のテキストが表示されます。 WebJobs は、App Service on Linux ではまだサポートされていないので注意してください。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/built-in-policies.md#general)

- [Azure App Service で WebJobs を使用してバックグラウンド タスクを実行する](webjobs-create.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**:サブスクリプション内のソフトウェア タイトルのインベントリを定期的に作成し、それらのタイトルを確認するプロセスを実装して、承認された Azure サービスのみがサブスクリプションで使用されるようにします。

Azure Resource Graph を使用して、このプロセス内で、サブスクリプション内のリソースのクエリまたは検出を行います。 環境内で検出されたすべての Azure リソースが承認されていることを確認します。

Azure Policy を構成して、次の組み込みポリシー定義を使用して、お客様のサブスクリプションで作成できるリソースの種類が制限されるようにします。

- 許可されないリソースの種類

- 許可されるリソースの種類

同様に、App Service の WebJobs を使用してインベントリを作成し、未承認のソフトウェア アプリケーションがコンピューティング リソース内にデプロイされていないか調べます。 これらの構成を定義し、ログを使用して監視します。 [WebJob Run Details]\(Web ジョブの実行の詳細\) ページで、 [出力の切り替え] を選択すると、ログの内容のテキストが表示されます。 WebJobs は、App Service on Linux ではまだサポートされていないので注意してください。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/built-in-policies.md#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:Azure Conditional Access を構成して、"Microsoft Azure 管理" アプリに対して "アクセスのブロック" を構成することで、Azure Resource Manager とやりとりするユーザーの機能を制限します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**:App Service の WebJobs を使用すると、お客様は、Web アプリ、API アプリ、またはモバイル アプリと同じインスタンスでプログラムまたはスクリプトを実行できます。 構成を定義する際は、組織で許可されていないスクリプトを制限するようにする責任があります。 App Service には、スクリプトの実行を制限するネイティブ メカニズムがありません。 WebJobs は、App Service on Linux ではまだサポートされていないので注意してください。

- [Azure App Service で WebJobs を使用してバックグラウンド タスクを実行する](webjobs-create.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**:個別のサブスクリプションや管理グループを実装して、危険度の高い App Service アプリを分離できるようにします。 App Service の境界セキュリティは仮想ネットワークを使用して実現されるため、危険度が高いアプリは専用の仮想ネットワークにデプロイします。 App Service Environment は、Azure Virtual Network のサブネットへの App Service のデプロイです。 

外部 Application Service Environment および ILB (内部ロード バランサー) Application Service Environment という 2 種類の Application Service Environment があります。 要件に基づいて最適なアーキテクチャを選択してください。

- [App Service Environment のネットワークの考慮事項](environment/network-info.md)

- [外部 App Service Environment の作成](environment/create-external-ase.md)

- [App Service Environment で内部ロード バランサーを作成して使用する](environment/create-ilb-ase.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Policy を使用して、App Service でデプロイされるアプリの標準的なセキュリティ構成を定義して実装します。

"Microsoft.Web" 名前空間で Azure Policy エイリアスを使用して、App Service Web Apps の構成を監査または適用するカスタム ポリシーを作成します。

次のような組み込みポリシー定義を適用します。

- App Service は仮想ネットワーク サービス エンドポイントを使用する必要がある

- Web アプリケーションには、HTTPS を介してのみアクセスできるようにする

- アプリで最新の TLS バージョンを使用する

標準化された使用のため、組み込みポリシー定義を適用するプロセスを文書化することをお勧めします。   

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&preserve-view=true)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure App Service アプリ全体にセキュリティで保護された設定を適用するために、Azure Policy の [deny] と [deploy if not exist] 効果を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:カスタムの Azure Policy 定義を使用する場合は、コードを安全に保存して管理するために、Azure DevOps または Azure Repos を選択します。

既存の継続的インテグレーション (CI) および継続的デリバリー (CD) パイプラインを使用して、既知の安全な構成をデプロイします。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Azure Repos のドキュメント](/azure/devops/repos/?view=azure-devops&preserve-view=true)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.Web" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.Web" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 

Azure リソースの構成を自動的に適用するには、Azure Policy の [audit]、[deny]、[deploy if not exist] の効果を適用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を App Service アプリに提供します。 マネージド ID を使用すると、コード内に資格情報を記述しなくても、Azure AD 認証をサポートするすべてのサービス (Key Vault を含む) に対してアプリで認証を行うことができます。 Azure Key Vault で論理的な削除が有効になっていることを確認します。

- [Azure Key Vault で論理的な削除を有効にする方法](../key-vault/general/key-vault-recovery.md)

- [App Service に対しマネージド ID を使用する方法](overview-managed-identity.md)

- [マネージド ID で Key Vault の認証を提供する方法](../key-vault/general/assign-access-policy-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を、App Service でデプロイされたアプリに提供します。 マネージド ID を使用すると、コード内に資格情報を記述しなくても、Azure AD 認証をサポートするすべてのサービス (Key Vault を含む) に対してアプリで認証を行うことができます。

- [App Service に対しマネージド ID を使用する方法](overview-managed-identity.md)

- [マネージド ID で Key Vault の認証を提供する方法](../key-vault/general/assign-access-policy-portal.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**:App Service のバックアップと復元の機能により、アプリのバックアップを手動またはスケジュール設定により簡単に作成できます。 バックアップが無期限に保持されるように構成できます。 以前の状態のスナップショットにアプリを復元するには、既存のアプリを上書きするか、別のアプリに対して復元を行います。

App Service によって、アプリで使用するようにユーザーが構成した Azure ストレージ アカウントとコンテナーに、次の情報をバックアップできます。
- アプリの構成
- ファイルのコンテンツ
- アプリに接続されているデータベース

定期的および自動化されたバックアップが、組織のポリシーで定義されている頻度で実行されていることを確認します。

- [Azure App Service バックアップ機能について](manage-backup.md)

- [Azure Storage の暗号化のためのカスタマー マネージド キー](../storage/common/customer-managed-keys-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: App Service のバックアップと復元の機能を使用して、アプリケーションをバックアップします。 バックアップ機能を使用するには、アプリケーションのバックアップ情報を格納するために Azure Storage アカウントが必要です。

- Azure Storage では、保存時に暗号化が行われます。システムによって提供されるキー、または独自のカスタマー マネージド キーを使用します。 これは、Azure の Web アプリで実行されていないアプリケーション データが格納される場所です。
- デプロイ パッケージからの実行は、App Service のデプロイ機能です。 これにより、Shared Access Signature (SAS) URL を使用して、Azure Storage アカウントからサイト コンテンツをデプロイできます。

- Key Vault 参照は、App Service のセキュリティ機能です。 これにより、実行時にアプリケーション設定としてシークレットをインポートできます。 これを使用して、Azure Storage アカウントの SAS URL を暗号化します。

詳細については、参照リンクをご覧ください。

- [Azure でのアプリのバックアップ](manage-backup.md)

- [Azure App Service で実行されるアプリの復元](web-sites-restore.md)

- [Azure での保存時の暗号化の概要](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [暗号化モデルとキー管理テーブル](../security/fundamentals/encryption-atrest.md)

- [カスタマー マネージド キーを使用した保存時の暗号化](configure-encrypt-at-rest-using-cmk.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:App Service アプリケーションのバックアップの復元プロセスを定期的にテストします。

- [Azure でのアプリのバックアップ](manage-backup.md)

- [Azure App Service Web アプリを復元する方法](web-sites-restore.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**:App Service バックアップは Azure Storage アカウント内に格納されます。 Azure Storage 内のデータは、利用可能な最強のブロック暗号の 1 つである 256 ビット AES 暗号化を使って透過的に暗号化および暗号化解除され、FIPS 140-2 に準拠しています。 Azure Storage 暗号化は、Windows での BitLocker 暗号化に似ています。

Azure Storage 暗号化は、Resource Manager と従来のストレージ アカウントの両方を含む、すべてのストレージ アカウントで有効になります。 Azure Storage 暗号化を無効にすることはできません。 データは既定で保護されるので、Azure Storage 暗号化を利用するために、コードまたはアプリケーションを変更する必要はありません。

既定では、ストレージ アカウント内のデータは Microsoft マネージド キーで暗号化されます。 Microsoft マネージド キーを利用してデータを暗号化することも、独自のキーで暗号化を管理することもできます。 Azure Key Vault で論理的な削除が有効になっていることを確認します。

- [保存データに対する Azure Storage 暗号化の概要](../storage/common/storage-service-encryption.md)

- [Azure Key Vault で論理的な削除を有効にする方法](../key-vault/general/key-vault-recovery.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

- [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md)

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、サブスクリプション (運用、非運用など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物『IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド』をご覧ください](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。  事後にインシデントをレビューして、問題が解決されていることを確認します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 ビジネス ニーズに応じて、Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングします。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Security Center のワークフローの自動化機能を使用します。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。 

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](../security/benchmarks/overview.md)」を参照してください。
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する
