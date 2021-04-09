---
title: Azure Cloud Services の Azure セキュリティ ベースライン
description: Azure Cloud Services セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 105fd185900692669fdc40e0c47a03474524e250
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731104"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Azure Cloud Services の Azure セキュリティ ベースライン

このセキュリティ ベースラインによって、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Microsoft Azure Cloud Services に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、Cloud Services に適用される Azure セキュリティ ベンチマークと関連ガイダンスで定義されている **セキュリティ コントロール** によってグループ化されています。 Cloud Services に適用できない **コントロール** は、除外されています。

 
Cloud Services を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Cloud Services セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:個別のパブリック サブネットとプライベート サブネットを持つクラシック Azure Virtual Network を作成して、信頼されたポートと IP 範囲に基づいて分離を施行します。 これらの仮想ネットワークとサブネットは、現在の Azure Resource Manager のリソースではなく、クラシック Virtual Network (クラシック デプロイ) ベースのリソースである必要があります。  

ネットワーク セキュリティ グループを使用してトラフィックを許可または拒否します。ネットワーク セキュリティ グループには、トラフィックの方向、プロトコル、ソース アドレスとポート、および送信先アドレスとポートに基づくアクセス制御ルールが含まれています。 ネットワーク セキュリティ グループのルールはいつでも変更可能で、変更は関連付けられているすべてのインスタンスに適用されます。

Microsoft Azure Cloud Services (クラシック) を Azure Resource Manager 仮想ネットワークに配置することはできません。 ただし、Resource Manager ベースの仮想ネットワークとクラシック デプロイベースの仮想ネットワークは、ピアリングによって接続できます。 

- [ネットワーク セキュリティ グループの概要](../virtual-network/network-security-groups-overview.md)

- [仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/cloud-services/cloud-services-connectivity-and-networking-faq#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:仮想ネットワーク、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**:Azure Cloud Services の構成を文書化し、変更を監視します。 サービスの構成ファイルを使用して、サービスのロールごとにデプロイするロール インスタンスの数、すべての構成設定の値、およびロールに関連付けられているすべての証明書の拇印を指定します。 

サービスが仮想ネットワークの一部である場合は、仮想ネットワーク構成ファイルだけでなく、サービス構成ファイルでネットワークの構成情報を指定する必要があります。 サービス構成ファイルの既定の拡張子は .cscfg です。 Azure Policy は、クラシック デプロイでの構成の施行ではサポートされないことに注意してください。

サービス構成ファイル (.cscfg) のクラウド サービスの構成値と、サービス定義 (.csdef) ファイルの定義を設定します。 サービス定義ファイルを使用して、アプリケーションのサービス モデルを定義します。 クラウド サービスで使用できるロールを定義し、サービス エンドポイントも指定します。 サービス構成ファイルを使用して Azure Cloud Services の構成をログに記録します。 再構成は、ServiceConfig.cscfg ファイルを使用して行うことができます。 

指定した内部エンドポイントと通信できるロールを制限する、オプションの NetworkTrafficRules 要素のサービス定義を監視します。 ロールが相互に通信する方法を指定するには、サービス定義ファイル内の省略可能な要素である NetworkTrafficRules ノードを構成します。 特定のロールの内部エンドポイントにアクセスできるロールを制限します。  サービス定義は変更できないことに注意してください。 

ネットワーク セキュリティ グループ フロー ログを有効にし、監査のためにログを Azure Storage アカウントに送信します。 フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure テナント内のトラフィック フローに関する分析情報を提供します。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化したり、ホット スポットやセキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [Azure Resource Manager とクラシック デプロイ - デプロイ モデルとリソースの状態について](../azure-resource-manager/management/deployment-models.md)

- [Cloud Services 構成ファイル](schema-cscfg-file.md)

- [Azure Policy によってサポートされるサービスの一覧](/cli/azure/azure-services-the-azure-cli-can-manage)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**:Microsoft では、トランスポート層セキュリティ (TLS) プロトコル v1.2 を使用して、Azure Cloud Services と顧客の間で転送されているデータを保護します。 Microsoft のデータ センターは、Azure サービスに接続するクライアント システムとの TLS 接続をネゴシエートします。 TLS には、強力な認証、メッセージの機密性、整合性 (メッセージの改ざん、傍受、偽造の検出が有効)、相互運用性、アルゴリズムの柔軟性、デプロイと使用のしやすさといったメリットがあります。

- [暗号化の基礎](../security/fundamentals/encryption-overview.md)

- [TLS/SSL 証明書の構成](cloud-services-configure-ssl-certificate-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:Azure Cloud では、分散型サービス拒否 (DDoS) 攻撃からプラットフォーム サービスを保護するために複数層のネットワーク セキュリティを実装しています。 Azure DDoS Protection は、Azure Cloud の継続的な監視プロセスの一部であり、侵入テストを通して継続的に強化されています。 この DDoS Protection は、外部からの攻撃だけではなく、他の Azure テナントからの攻撃にも耐えられるように設計されています。 

Azure Cloud Services 内のプラットフォーム レベルの保護に加えて、通信をブロックまたは拒否するいくつかの方法があります。 次のとおりです。 

-  いくつかの特定の IP アドレスを選択的にブロックするスタートアップ タスクを作成します
-  IIS の web.config ファイルを修正することで、一連の特定の IP アドレスに対する Azure Web ロールのアクセスを制限します

Cloud Services の既定の URL または名前 (例: *.cloudapp.net) への着信トラフィックを防ぎます。Cloud Services 定義 (* .csdef) ファイル内のサイト バインド構成でホスト ヘッダーをカスタム DNS 名に設定します。

従来のサブスクリプション管理者の割り当てに対して、拒否の適用ルールを構成します。 既定では、内部エンドポイントを定義すると、任意のロールから別のロールの内部エンドポイントに、無制限で通信を行うことができます。 通信を制限するには、サービス定義ファイル内で、NetworkTrafficRules 要素を ServiceDefinition 要素に追加する必要があります。

- [自分のクラウド サービスの既定の URL への着信トラフィックをブロックまたは無効にするにはどうすればよいですか](https://docs.microsoft.com/azure/cloud-services/cloud-services-connectivity-and-networking-faq#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Azure DDOS protection](https://docs.microsoft.com/azure/cloud-services/cloud-services-connectivity-and-networking-faq#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [特定の IP アドレスをブロックする](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common#block-a-specific-ip-address)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:Azure ネットワークの監視に使用できる、ネットワーク パフォーマンスの監視、診断、および分析サービスである Azure Network Watcher を使用します。 Network Watcher Agent 仮想マシン拡張機能は、オンデマンドでネットワーク トラフィックをキャプチャするためと、Azure Virtual Machines に関するその他の高度な機能を使用するための要件です。 Network Watcher Agent 仮想マシン拡張機能をインストールし、ネットワーク セキュリティ グループのフロー ログを有効にします。

ネットワーク セキュリティ グループでフローのログ記録を構成します。 クラシック デプロイ モデルを使用してデプロイされた既存の仮想マシンに Network Watcher 仮想マシン拡張機能をデプロイする方法の詳細を確認します。

- [ネットワーク セキュリティ グループでフローのログ記録を構成する](../virtual-machines/extensions/network-watcher-linux.md)

- [フロー ログの構成の詳細については、こちらを参照してください](/cli/azure/azure-services-the-azure-cli-can-manage)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**:Azure Cloud Services には、組み込みの IDS または IPS 機能はありません。 お客様は、組織の要件に基づいて、Azure Marketplace から追加のネットワークベースの IDS または IPS ソリューションを選択してデプロイできます。 サードパーティのソリューションを使用する場合は、選択した IDS または IPS ソリューションを Azure Cloud Services で十分にテストして、適切に稼動および機能するようにしてください。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**:サービス証明書は Azure Cloud Services に付属して、サービスとの間のセキュリティで保護された通信を有効にします。 サービス定義で定義されているこれらの証明書は、Web ロールのインスタンスを実行している仮想マシンに自動的にデプロイされます。 たとえば、Web ロールの場合は、公開されている HTTPS エンドポイントを認証できるサービス証明書を使用できます。 

証明書を更新するには、新しい証明書をアップロードし、サービス構成ファイルの拇印値を変更するだけです。

TLS 1.2 プロトコルを使用します。これは、データをセキュリティで保護するために最も一般的に使用される方法であり、機密性と整合性の保護が提供されます。 

一般に、Web アプリケーションを保護し、OWASP Top 10 などの攻撃から保護するには、Azure Web Application Firewall が有効な Azure Application Gateway をデプロイして、Web アプリケーションを保護します。 

- [サービス証明書](cloud-services-certs-create.md)

- [Azure でアプリケーション用に TLS を構成する](cloud-services-configure-ssl-certificate-portal.md)

- [Application Gateway をデプロイする方法](../application-gateway/quick-create-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**:Azure Cloud Services の構成を強化し、変更を監視します。 サービス構成ファイルは、サービスのロールごとに配置するロール インスタンスの数、すべての構成設定の値、ロールに関連付けられているすべての証明書のサムプリントを指定します。 

サービスが仮想ネットワークの一部である場合は、仮想ネットワーク構成ファイルだけでなく、サービス構成ファイルでネットワークの構成情報を指定する必要があります。 サービス構成ファイルの既定の拡張子は .cscfg です。

Azure Policy は、Azure Cloud Services での構成の施行ではサポートされないことに注意してください。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:Azure Virtual Network 内の Azure リソースが送受信するネットワーク トラフィックは、Azure ネットワーク セキュリティ グループを使ってフィルター処理できます。 ネットワーク セキュリティ グループには、何種類かの Azure リソースとの送受信ネットワーク トラフィックを許可または拒否するセキュリティ規則が含まれています。 各規則で、送信元と送信先、ポート、およびプロトコルを指定することができます。

Azure Cloud Services 内の個々のネットワーク セキュリティ グループの規則の "説明" フィールドを使用して、ネットワークとの間のトラフィックを許可する規則を文書化します。

- [ネットワーク セキュリティ グループの規則を使用してネットワーク トラフィックをフィルター処理する方法](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure Traffic Manager の組み込みのエンドポイント監視と自動フェールオーバーの機能を使用します。 これらを使用して、エンドポイントと Azure リージョンの障害に対する回復性を備えた高可用性アプリケーションを提供できます。 エンドポイント監視を構成するには、Traffic Manager プロファイルに特定の設定を指定する必要があります。

Azure のプラットフォーム ログであるアクティビティ ログから、サブスクリプション レベルのイベントに関する分析情報を収集します。 これには、リソースが変更されたときや仮想マシンが起動されたときなどの情報が含まれます。 Azure portal でアクティビティ ログを表示するか、PowerShell と CLI を使用してエントリを取得します。 

診断設定を作成して、Azure Monitor、Azure Event Hubs (Azure の外部に転送するため)、または Azure Storage (アーカイブのため) にアクティビティ ログを送信します。 Azure Cloud Services の重要なリソースが変更されたときに通知アラートが送信されるように、Azure Monitor を構成します。 

- [Azure アクティビティ ログ](/azure/azure-monitor/platform/activity-log)

- [Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する](/azure/azure-monitor/platform/alerts-activity-log)

- [Traffic Manager の監視](../traffic-manager/traffic-manager-monitoring.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**:Microsoft は、Azure Cloud Services 用の Azure リソースのタイム ソースを保持しています。 お客様は、環境内で使用されているタイム サーバーへのアクセス (UDP プロトコルでポート 123 を使用) を許可するネットワーク ルールを作成することが必要になる場合があります。

- [NTP サーバー アクセス](https://docs.microsoft.com/azure/firewall/protect-windows-virtual-desktop#additional-considerations)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:Azure Event Hubs を使用して、クラウド サービスのストリーミング データをプログラムで使用します。 このデータをすべて統合して Azure Sentinel に送信し、ログを監視して確認するか、サードパーティの SIEM を使用します。 セキュリティ ログを一元的に管理する場合は、選択した Azure Security Center データを Azure Event Hubs に連続エクスポートするように構成し、SIEM に適したコネクタを設定します。 Azure Sentinel のオプションをいくつか次に示します (サードパーティ製ツールを含む)。

- Azure Sentinel - ネイティブの Security Center アラート データ コネクタを使用する
- Splunk - Splunk 向けの Azure Monitor アドオンを使用する
- IBM QRadar - 手動で構成されたログ ソースを使用する
- ArcSight – SmartConnector を使用する

Azure sentinel で使用可能なコネクタの詳細については、Azure Sentinel のドキュメントを参照してください。 

- [データ ソースの接続](../sentinel/connect-data-sources.md)

- [SIEM との統合](../security-center/continuous-export.md)

- [診断データの保存](diagnostics-extension-to-storage.md)

- [Azure Event Hubs を使用した SIEM 統合の構成](../security-center/continuous-export.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:Azure Cloud Services のトラブルシューティングを行うために、Visual Studio を構成して Azure Diagnostics をセットアップします。Azure Diagnostics では、仮想マシン (Azure Cloud Services を実行している仮想マシン インスタンスを含む) 上のシステム データとログ データがキャプチャされます。 診断データは、選択したストレージ アカウントに転送されます。 デプロイする前に、Azure Cloud Services プロジェクトで診断を有効にします。

 
Azure Monitor 内のアクティビティ ログでいくつかのイベントの変更履歴を表示します。 イベントの発生期間中に行われた変更を監査します。 [変更履歴 (プレビュー)] タブで詳細な調査を行うために、アクティビティ ログからイベントを選択します。Visual Studio から Azure Cloud Services を発行するときに、診断データを Application Insights に送信します。 その時点で Application Insights の Azure リソースを作成するか、データを既存の Azure リソースに送信します。 

Azure Cloud Services の可用性、パフォーマンス、障害、および使用状況は、Application Insights で監視できます。 カスタム グラフを Application Insights に追加して、最も重要なデータを確認できます。 ロール インスタンスのデータは、Azure Cloud Services プロジェクトで Application Insights SDK を使用して収集できます。 

- [デプロイ前に Visual Studio で診断を有効にする](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [変更履歴を表示する](/azure/azure-monitor/platform/activity-log#view-change-history)

- [Azure クラウド サービス (クラシック) 向けの Application Insights](../azure-monitor/app/cloudservices.md)

- [Azure クラウド サービス (クラシック) と仮想マシンに対する診断を設定する](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Cloud Services で高度な監視機能を使用すると、5 分、1 時間、12 時間の間隔で追加のメトリックをサンプリングして収集することができます。 集計されたデータはストレージ アカウントのテーブルに格納され、10 日後に消去されます。 ただし、使用されるストレージ アカウントはロールごとに構成されます。ロールによって異なるストレージ アカウントを使用できます。 これは、.csdef ファイルと .cscfg ファイルで接続文字列を使用して構成されます。

高度な監視では、監視対象のロールで Azure Diagnostics 拡張機能 (Application Insights SDK はオプションです) が使用されることに注意してください。 診断拡張機能は、(ロールごとに) 構成ファイル diagnostics.wadcfgx を使用して、監視する診断メトリックを構成します。 Azure 診断拡張機能では、データを収集して Azure Storage アカウントに格納します。 これらの設定は、.wadcfgx ファイル、.csdef ファイル、および.cscfg ファイルで構成されています。

- [クラウド サービスの監視の概要](cloud-services-how-to-monitor.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**:Azure Cloud Services では、基本または拡張の監視モードを使用できます。 Azure Cloud Services は、ホスト仮想マシンから基本的な監視データ (CPU 使用率、ネットワークの受信/送信、およびディスクの読み取り/書き込み) を自動的に収集します。 Azure portal のクラウド サービスの概要ページとメトリック ページで、収集された監視データを表示します。 

Azure Cloud Services で診断を有効にすると、Azure Diagnostics 拡張機能を使用して、アプリケーション ログやパフォーマンス カウンターなどの診断データを収集できます。 Set-azureservicediagnosticsextension コマンドレットを使用して既に実行されているクラウド サービスの診断構成を有効化または更新するか、診断拡張機能を使用してクラウド サービスを自動的にデプロイします。 必要に応じて、Application Insights SDK をインストールします。 パフォーマンス カウンターを Azure Monitor に送信します。

Azure 診断拡張機能では、データを収集して Azure Storage アカウントに格納します。 診断データは永続的に保存されないため、Microsoft Azure ストレージ エミュレーターまたは Azure Storage に転送します。 ストレージに保持されたら、Visual Studio のサーバー エクスプローラー、Microsoft Azure Storage Explorer、Azure Management Studio などの利用可能なツールのいずれかを使用して表示できます。 診断拡張機能で、diagnostics.wadcfgx という名前の構成ファイル (ロールごと) を使用して、診断メトリックを監視するように構成します。 

- [クラウド サービスの監視の概要](cloud-services-how-to-monitor.md)

- [Worker ロールの診断を有効にする方法 - SIEM との統合](../security-center/continuous-export.md)

- [PowerShell を使用した Azure Cloud Services での診断の有効化](cloud-services-diagnostics-powershell.md)

- [Azure Storage への診断データの保存と表示](https://docs.microsoft.com/azure/cloud-services/diagnostics-extension-to-storage?&amp;preserve-view=true)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Azure Sentinel と統合するか、サードパーティの SIEM と統合するか、異常なアクティビティのアラートを有効にして、Azure Cloud Services のログ データを監視します。

- [SIEM との統合](../security-center/continuous-export.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**:Azure 向けの Microsoft Antimalware によって、Azure Cloud Services および仮想マシンが保護されます。 お客様は、Web アプリケーション ファイアウォール、ネットワーク ファイアウォール、マルウェア対策、侵入検出/防御システム (IDS または IPS) などのサード パーティ製のセキュリティ ソリューションを必要に応じてデプロイすることができます。

- [Azure の基本的な IPS/IDS と DDoS が提供する機能には何がありますか](https://docs.microsoft.com/azure/cloud-services/cloud-services-configuration-and-management-faq#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセスを管理することをお勧めします。 ただし、Azure Cloud Services では、Azure RBAC モデルはサポートされません。Azure RBAC は Azure Resource Manager ベースのサービスではなく、お客様はクラシック サブスクリプションを使用する必要があるためです

既定では、Azure の従来のサブスクリプション管理者ロールには、アカウント管理者、サービス管理者、および共同管理者の 3 つがあります。 

従来のサブスクリプション管理者には、Azure サブスクリプションへのフル アクセス権があります。 Azure portal、Azure Resource Manager API、およびクラシック デプロイ モデル API を使用して、リソースを管理することができます。 Azure へのサインアップに使用されたアカウントは、自動的にアカウント管理者とサービス管理者の両方として設定されます。 後で共同管理者を追加できます。 

サービス管理者および共同管理者は、サブスクリプション スコープで所有者ロール (Azure ロール) が割り当てられているユーザーと同等のアクセス権を持ちます。 共同管理者を管理したり、サービス管理者を表示したりするには、Azure portal の [従来の管理者] タブを使用します。 

従来のサービス管理者と共同管理者のロールの割り当てを一覧表示するには、PowerShell で次のコマンドを使用します。

Get-AzRoleAssignment -IncludeClassicAdministrators

従来のサブスクリプション管理ロールの違いを確認します。 

- [3 つの従来のサブスクリプション管理ロールの違い](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#classic-subscription-administrator-roles)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**:使用可能なロールと、Azure Cloud Services リソースの操作と管理に必要なアクセス許可に基づいて、専用管理アカウントの使用に関する標準的な操作手順を作成することをお勧めします。

- [従来のサブスクリプション管理ロールの違い](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#classic-subscription-administrator-roles)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:Azure Cloud Services で実行されている複数のアプリケーションの ID を個別に管理することは避けてください。 シングル サインオンを実装して、複数の ID と資格情報をユーザーが管理しなくて済むようにします。

- [シングル サインオン (SSO) とは](../active-directory/manage-apps/what-is-single-sign-on.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:昇格された特権を必要とする管理タスクには、セキュリティで保護された Azure マネージド ワークステーション (特権アクセス ワークステーションとも呼ばれます) を使用することをお勧めします。

- [セキュリティで保護された Azure マネージド ワークステーションを理解する](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure Active Directory (Azure AD) 多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**:Azure クラウド サービスの REST API を使用して、Azure クラウド サービスのリソースの機密情報のインベントリを作成します。 デプロイされたクラウド サービスのリソースをポーリングして、構成と .pkg のリソースを取得します。

 例として、いくつかの API を以下に示します。

- デプロイの取得 - デプロイの取得操作では、デプロイの構成情報、状態、およびシステム プロパティが返されます。
- パッケージの取得 - パッケージの取得操作では、デプロイのクラウド サービス パッケージが取得され、Microsoft Azure BLOB ストレージにパッケージ ファイルが保存されます
- クラウド サービスのプロパティの取得 - クラウド サービスのプロパティの取得操作では、指定されたクラウド サービスのプロパティが取得されます

Azure Cloud Services の REST API のドキュメントを確認し、組織の要件に基づいて、機密情報をデータ保護するためのプロセスを作成します。

- [デプロイの取得](/rest/api/compute/cloudservices/rest-get-deployment)

- [Get Cloud Service Properties](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [配送パッケージの取得](/rest/api/compute/cloudservices/rest-get-package)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:Azure Cloud Services の環境の種類やデータの機密度レベルなど、個々のセキュリティ ドメイン用の個別のサブスクリプションと管理グループを使用して分離を実装します。

Azure Cloud Services の証明書要素の "permissionLevel" を編集して、ロール プロセスに付与されるアクセス許可を指定することもできます。 管理者特権プロセスのみが秘密キーにアクセスできるようにするには、管理者特権を指定します。 limitedOrElevated 権限では、すべてのロール プロセスが秘密キーにアクセスできます。 指定できる値は、limitedOrElevated または elevated です。 既定値は limitedOrElevated です。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [WebRole スキーマ](https://docs.microsoft.com/azure/cloud-services/schema-csdef-webrole#Certificate)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**:Azure Marketplace のサードパーティ ソリューションをネットワーク境界で使用して、機密情報の認可されていない転送を監視し、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックすることをお勧めします。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:Azure Cloud Services に TLS v2 を構成します。 Azure portal を使用して、ステージングされた Azure Cloud Services のデプロイに証明書を追加し、サービスの CSDEF ファイルと CSCFG ファイルに証明書情報を追加します。 アプリケーションを再パッケージ化し、ステージングされたデプロイを更新して、新しいパッケージが使用されるようにします。 

Azure Cloud Services に付属しているサービス証明書を Azure で使用して、サービスとの間のセキュリティで保護された通信を有効にします。 公開されている HTTPS エンドポイントを認証できる証明書を指定します。 クラウド サービスのサービス定義でサービス証明書を定義し、ロールのインスタンスを実行している仮想マシンに自動的にデプロイされるようにします。

管理証明書を使用して管理 API で認証します。管理証明書を使用すると、クラシック デプロイ モデルで認証することができます。 多くのプログラムとツール (Visual Studio や Azure SDK など) では、これらの証明書を使用して、さまざまな Azure サービスの構成とデプロイを自動化します。 

その他の参照情報として、クラシック デプロイ モデル API では、Azure portal を通じて利用できるクラシック デプロイ モデルの機能に、プログラムを使用してアクセスできます。 Azure SDK for Python を使用すると、Azure Cloud Services と Azure Storage のアカウントを管理できます。 Azure SDK for Python では、クラシック デプロイ モデル API である REST API をラップします。 すべての API 操作は TLS 経由で実行され、X.509 v3 証明書を使用して相互認証されます。 管理サービスへのアクセスは、Azure で実行されているサービス内から行うことができます。 また、HTTPS 要求の送信と HTTPS 応答の受信の機能を持つ任意のアプリケーションからインターネット経由で直接行うこともできます。

- [Azure でアプリケーション用に TLS を構成する](cloud-services-configure-ssl-certificate-portal.md)

- [Python からクラシック デプロイ モデルを使用する](cloud-services-python-how-to-use-service-management.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:サードパーティのアクティブ検出ツールを使用して、組織のテクノロジ システムによって格納、処理、または送信されるすべての機密情報 (オンサイトにあるもの、またはリモート サービス プロバイダーにあるものなど) を特定し、組織の機密情報インベントリを更新することをお勧めします。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**:クラウド サービス (クラシック) には適用されません。 データ損失は防止されません。

ホストベースの自動データ損失防止ソリューションなどのサードパーティ ツールを実装して、データがシステムからコピーされた場合でもデータへのアクセス制御を実施することをお勧めします。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**:Azure Cloud Services では、保存時の暗号化はサポートされません。 これは、Azure Cloud Services がステートレスになるように設計されているためです。 Azure Cloud Services では、外部ストレージをサポートします (既定では保存時に暗号化される Azure Storage など)。  

一時ディスクに格納されているアプリケーション データは暗号化されません。 必要に応じて、このデータを管理および暗号化する場合は、お客様が責任を負います。  

- [Azure での保存時の暗号化の概要](../security/fundamentals/encryption-atrest.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:Azure Monitor のクラシック メトリック アラートを使用すると、重要なリソースに適用されたメトリックのいずれかがしきい値を超えたときに通知を受け取ることができます。 クラシック メトリック アラートは、ディメンションを持たないメトリックでのみアラートを生成できる古い機能です。 クラシック メトリック アラートよりも機能が向上したメトリック アラートと呼ばれる新しい機能が既にあります。 

さらに、Application Insights を使用すると、Azure Cloud Services アプリの可用性、パフォーマンス、障害、および使用状況を監視できます。 この場合は、Application Insights SDK からのデータと、Azure Cloud Services からの Azure Diagnostics のデータが組み合わせて使用されます。

- [Azure Monitor を使用してクラシック メトリック アラートを作成、表示、および管理する](/azure/azure-monitor/platform/alerts-classic-portal)

- [メトリック アラートの概要](/azure/azure-monitor/platform/alerts-metric-overview) 

- [Azure クラウド サービス (クラシック) 向けの Application Insights](../azure-monitor/app/cloudservices.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**:この情報は、Azure Cloud Services worker の Azure ゲストのオペレーティング システムと、サービスとしてのプラットフォーム (PaaS) を使用した Web ロールに関連することに注意してください。 ただし、サービスとしてのインフラストラクチャ (IaaS) を使用した Virtual Machines には適用されません。

既定では、お客様のゲスト オペレーティング・システムは、サービス構成 (.cscfg) で指定した Windows Server 2016 などのオペレーティング・システム ファミリー内でサポートされている最新のイメージに定期的に更新されます。

お客様が Azure Cloud Services のデプロイで特定のオペレーティング システムのバージョンを選択した場合は、オペレーティング システムの自動更新が無効になり、パッチ適用はユーザーの責任となります。 お客様はロール インスタンスが更新プログラムを受け取るようにする必要があります。そうしないと、アプリケーションのセキュリティが脆弱になる可能性があります。

- [Azure ゲスト OS](cloud-services-guestos-msrc-releases.md)

- [Azure ゲスト OS のサポートと提供終了のポリシー](cloud-services-guestos-retirement-policy.md)

- [Cloud Services (クラシック) の構成方法](cloud-services-how-to-configure-portal.md)

- [ゲスト OS バージョンの管理](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal#manage-guest-os-version)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**:サードパーティの修正プログラム管理ソリューションを使用します。 環境内で既に Configuration Manager を使用しているお客様は、System Center Updates Publisher を使用して、カスタム更新プログラムを Windows Server Update Service に発行することもできます。 

これにより、Update Management では、サード パーティ ソフトウェアを使用して、Configuration Manager を更新リポジトリとして使用するマシンに修正プログラムを適用できます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**:DDoS 攻撃のリスクの範囲を継続的に把握することをお勧めします。 

次のシナリオを検討することをお勧めします。

- パブリックに使用可能な新しい Azure リソースのうち、保護が必要なものはあるか。
- サービスに単一障害点はあるか。
- どのようにすれば、サービスを分離して攻撃の影響を制限しながら、有効な顧客がサービスを利用できるようにすることができるか。
- DDoS Protection Standard を有効にする必要があるのに有効になっていない仮想ネットワークがあるか。
- サービスは複数のリージョンをまたがるフェールオーバーについてアクティブ/アクティブになっているか。

サポート ドキュメント:

- [Azure リソースのリスク評価](https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices#risk-evaluation-of-your-azure-resources)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Cloud Services には適用されません。 この推奨事項は、IaaS コンピューティング リソースに適用できます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:インベントリを定期的に調整し、認可されていないリソースがサブスクリプションから適切なタイミングで削除されるようにすることをお勧めします。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:お客様は、コンピューティング リソース用に承認された Azure リソースと承認されたソフトウェアを定義する必要があります。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**:Azure Security Center で使用できる適応型アプリケーション制御機能を使用します。 お使いの Windows および Linux の Azure および Azure 以外のマシン上でどのアプリケーションを実行できるかを制御できる、Security Center によるインテリジェントかつ自動化されたエンドツーエンドのソリューションです。 また、これは、マルウェアに対してマシンを強化するためにも役立ちます。 

この機能は、Azure と Azure 以外の Windows (すべてのバージョン、クラシック、または Azure Resource Manager) コンピューターおよび Linux コンピューターで使用できます。

Security Center では、機械学習を使用して、お使いのコンピューター上で実行されているアプリケーションを分析し、このインテリジェンスから許可リストを作成します。 この機能を使用すると、アプリケーション許可リスト ポリシーを構成および保守するプロセスが非常にシンプルになり、以下のことが実現できます。
- 悪意のあるアプリケーション (マルウェア対策ソリューションでは見逃される可能性のあるものを含む) の実行が試行されるのをブロックまたは警告する。

- ライセンスのあるソフトウェアのみを使用するという組織のセキュリティ ポリシーに準拠する。
- 環境内で使用されることが望ましくないソフトウェアを避ける。
- 古くてサポートされていないアプリが実行されることを避ける。
- 組織で許可されていない特定のソフトウェア ツールが実行されないようにする。
- アプリの使用状況を通じて、機密データへのアクセスを IT 部門が制御できるようにする。

詳細については、以下のリンクを参照してください。

- [アダプティブ アプリケーション制御](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**:Azure Security Center で使用できる適応型アプリケーション制御機能を使用します。 お使いの Windows および Linux の Azure および Azure 以外のマシン上でどのアプリケーションを実行できるかを制御できる、Security Center によるインテリジェントかつ自動化されたエンドツーエンドのソリューションです。 また、これは、マルウェアに対してマシンを強化するためにも役立ちます。 

この機能は、Azure と Azure 以外の Windows (すべてのバージョン、クラシック、または Azure Resource Manager) コンピューターおよび Linux コンピューターで使用できます。

Security Center では、機械学習を使用して、お使いのコンピューター上で実行されているアプリケーションを分析し、このインテリジェンスから許可リストを作成します。 この機能を使用すると、アプリケーション許可リスト ポリシーを構成および保守するプロセスが非常にシンプルになり、以下のことが実現できます。

- 悪意のあるアプリケーション (マルウェア対策ソリューションでは見逃される可能性のあるものを含む) の実行が試行されるのをブロックまたは警告する。

- ライセンスのあるソフトウェアのみを使用するという組織のセキュリティ ポリシーに準拠する。

- 環境内で使用されることが望ましくないソフトウェアを避ける。

- 古くてサポートされていないアプリが実行されることを避ける。

- 組織で許可されていない特定のソフトウェア ツールが実行されないようにする。

- アプリの使用状況を通じて、機密データへのアクセスを IT 部門が制御できるようにする。

詳細については、以下のリンクを参照してください。

- [アダプティブ アプリケーション制御](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:Azure Security Center で使用できる適応型アプリケーション制御機能を使用します。 お使いの Windows および Linux の Azure および Azure 以外のマシン上でどのアプリケーションを実行できるかを制御できる、Security Center によるインテリジェントかつ自動化されたエンドツーエンドのソリューションです。 また、これは、マルウェアに対してマシンを強化するためにも役立ちます。 

この機能は、Azure と Azure 以外の Windows (すべてのバージョン、クラシック、または Azure Resource Manager) コンピューターおよび Linux コンピューターで使用できます。

Security Center では、機械学習を使用して、お使いのコンピューター上で実行されているアプリケーションを分析し、このインテリジェンスから許可リストを作成します。 この機能を使用すると、アプリケーション許可リスト ポリシーを構成および保守するプロセスが非常にシンプルになり、以下のことが実現できます。

- 悪意のあるアプリケーション (マルウェア対策ソリューションでは見逃される可能性のあるものを含む) の実行が試行されるのをブロックまたは警告する。

- ライセンスのあるソフトウェアのみを使用するという組織のセキュリティ ポリシーに準拠する。

- 環境内で使用されることが望ましくないソフトウェアを避ける。

- 古くてサポートされていないアプリが実行されることを避ける。

- 組織で許可されていない特定のソフトウェア ツールが実行されないようにする。

- アプリの使用状況を通じて、機密データへのアクセスを IT 部門が制御できるようにする。

詳細については、以下のリンクを参照してください。

- [アダプティブ アプリケーション制御](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**:Azure Security Center で使用できる適応型アプリケーション制御機能を使用します。 お使いの Windows および Linux の Azure および Azure 以外のマシン上でどのアプリケーションを実行できるかを制御できる、Security Center によるインテリジェントかつ自動化されたエンドツーエンドのソリューションです。 また、これは、マルウェアに対してマシンを強化するためにも役立ちます。 

この機能は、Azure と Azure 以外の Windows (すべてのバージョン、クラシック、または Azure Resource Manager) コンピューターおよび Linux コンピューターで使用できます。

Security Center では、機械学習を使用して、お使いのコンピューター上で実行されているアプリケーションを分析し、このインテリジェンスから許可リストを作成します。 この機能を使用すると、アプリケーション許可リスト ポリシーを構成および保守するプロセスが非常にシンプルになり、以下のことが実現できます。

- 悪意のあるアプリケーション (マルウェア対策ソリューションでは見逃される可能性のあるものを含む) の実行が試行されるのをブロックまたは警告する。

- ライセンスのあるソフトウェアのみを使用するという組織のセキュリティ ポリシーに準拠する。

- 環境内で使用されることが望ましくないソフトウェアを避ける。

- 古くてサポートされていないアプリが実行されることを避ける。

- 組織で許可されていない特定のソフトウェア ツールが実行されないようにする。

- アプリの使用状況を通じて、機密データへのアクセスを IT 部門が制御できるようにする。

詳細については、以下のリンクを参照してください。

- [アダプティブ アプリケーション制御](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**:Azure Security Center で使用できる適応型アプリケーション制御機能を使用します。 お使いの Windows および Linux の Azure および Azure 以外のマシン上でどのアプリケーションを実行できるかを制御できる、Security Center によるインテリジェントかつ自動化されたエンドツーエンドのソリューションです。 また、これは、マルウェアに対してマシンを強化するためにも役立ちます。 

この機能は、Azure と Azure 以外の Windows (すべてのバージョン、クラシック、または Azure Resource Manager) コンピューターおよび Linux コンピューターで使用できます。

Security Center では、機械学習を使用して、お使いのコンピューター上で実行されているアプリケーションを分析し、このインテリジェンスから許可リストを作成します。 この機能を使用すると、アプリケーション許可リスト ポリシーを構成および保守するプロセスが非常にシンプルになり、以下のことが実現できます。
- 悪意のあるアプリケーション (マルウェア対策ソリューションでは見逃される可能性のあるものを含む) の実行が試行されるのをブロックまたは警告する。

- ライセンスのあるソフトウェアのみを使用するという組織のセキュリティ ポリシーに準拠する。
- 環境内で使用されることが望ましくないソフトウェアを避ける。
- 古くてサポートされていないアプリが実行されることを避ける。
- 組織で許可されていない特定のソフトウェア ツールが実行されないようにする。
- アプリの使用状況を通じて、機密データへのアクセスを IT 部門が制御できるようにする。

詳細については、以下のリンクを参照してください。

- [アダプティブ アプリケーション制御](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12:コンピューティング リソースでスクリプトを実行するユーザーの機能を制限する

**ガイダンス**:Azure Security Center で使用できる適応型アプリケーション制御機能を使用します。 お使いの Windows および Linux の Azure および Azure 以外のマシン上でどのアプリケーションを実行できるかを制御できる、Security Center によるインテリジェントかつ自動化されたエンドツーエンドのソリューションです。 また、これは、マルウェアに対してマシンを強化するためにも役立ちます。 

この機能は、Azure と Azure 以外の Windows (すべてのバージョン、クラシック、または Azure Resource Manager) コンピューターおよび Linux コンピューターで使用できます。

Security Center では、機械学習を使用して、お使いのコンピューター上で実行されているアプリケーションを分析し、このインテリジェンスから許可リストを作成します。 この機能を使用すると、アプリケーション許可リスト ポリシーを構成および保守するプロセスが非常にシンプルになり、以下のことが実現できます。

- 悪意のあるアプリケーション (マルウェア対策ソリューションでは見逃される可能性のあるものを含む) の実行が試行されるのをブロックまたは警告する。

- ライセンスのあるソフトウェアのみを使用するという組織のセキュリティ ポリシーに準拠する。

- 環境内で使用されることが望ましくないソフトウェアを避ける。

- 古くてサポートされていないアプリが実行されることを避ける。

- 組織で許可されていない特定のソフトウェア ツールが実行されないようにする。

- アプリの使用状況を通じて、機密データへのアクセスを IT 部門が制御できるようにする。

詳細については、以下のリンクを参照してください。

- [アダプティブ アプリケーション制御](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**:Azure Cloud Services を使用した機密性の高いまたはリスクの高いアプリケーションの場合は、個別のサブスクリプションや管理グループを実装して分離を実現します。

ネットワーク セキュリティ グループを使用して、受信セキュリティ規則を作成し、http などのサービスを選択して、カスタム ポートを選択し、優先度と名前を指定します。 優先度は、ルールが適用される順序に影響します。数値が小さいほど、ルールが早く適用されます。 ビジネス ニーズに基づいてネットワーク トラフィックを分離またはセグメント化するには、ネットワーク セキュリティ グループをサブネットまたは特定のネットワーク インターフェイスに関連付ける必要があります。

詳細については、以下のリンクを参照してください。

- [チュートリアル - Azure portal を使用してネットワーク セキュリティ グループでネットワーク トラフィックをフィルター処理する](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Cloud Services リソースの安全な構成基準として、Azure Security Center からの推奨事項を使用します。 

Azure portal で、[Security Center]、[計算 &amp; アプリ]、[Azure Cloud Services] の順に選択して、サービス リソースに適用できる推奨事項を確認します。

- [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure Cloud Services には適用されません。 これは、クラシック デプロイ モデルに基づいています。 サードパーティのソリューションを使用して、セキュリティで保護された Azure リソースの構成を維持することをお勧めします

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:Azure Cloud Services の構成ファイルには、リソースの操作属性が格納されます。 構成ファイルのコピーをセキュリティで保護されたストレージ アカウントに格納できます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:Azure Cloud Services には適用されません。 これはクラシック デプロイ モデルに基づいており、Azure Resource Manager のデプロイ ベースの構成ツールで管理することはできません。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**:Azure Cloud Services には適用されません。 この推奨事項は、サービスとしてのインフラストラクチャ (IaaS) ベースのコンピューティング リソースに適用できます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: Azure Security Center を使用して、ご利用の Azure リソースのベースライン スキャンを実行します。  

- [Azure Security Center の推奨事項を修復する方法](../security-center/security-center-remediate-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**:Azure Security Center で、[計算 &amp; アプリ] 機能を選択し、仮想マシン、サーバー、およびコンテナーに関する推奨事項に従います。

- [Azure Security Center のコンテナーの推奨事項を理解する](../security-center/container-security.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:Azure Cloud Services はクラシック デプロイ モデルに基づいており、Azure Key Vault とは統合されません。

Azure Cloud Services で使用される資格情報などのシークレットをセキュリティで保護することができます。これにより、パスワードを毎回入力する必要がなくなります。 最初に、プレーンテキストのパスワードを指定し、PowerShell コマンドの ConvertTo-SecureString を使用してそのパスワードをセキュリティで保護された文字列に変換します。 次に、ConvertFrom-SecureString を使用して、セキュリティで保護されたこの文字列を、暗号化された標準文字列に変換します。  これで、この暗号化された標準文字列を、Set-Content を使用してファイルに保存することができます。

また、Azure Cloud Services で使用される証明書の秘密キーをセキュリティで保護されたストレージに格納することをお勧めします。

- [PowerShell からリモート デスクトップを構成する](https://docs.microsoft.com/azure/cloud-services/cloud-services-role-enable-remote-desktop-powershell#configure-remote-desktop-from-powershell)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**:Azure Cloud Services で使用される資格情報などのシークレットをセキュリティで保護します。これにより、パスワードを毎回入力する必要がなくなります。 
 

最初に、プレーンテキストのパスワードを指定し、PowerShell コマンドの ConvertTo-SecureString を使用してそのパスワードをセキュリティで保護された文字列に変更します。 次に、ConvertFrom-SecureString を使用して、セキュリティで保護されたこの文字列を、暗号化された標準文字列に変換します。 この暗号化された標準文字列を、Set-Content コマンドを使用してファイルに保存します。

Azure Cloud Services で使用される証明書の秘密キーをセキュリティで保護されたストレージの場所に格納します。

- [PowerShell からリモート デスクトップを構成する](https://docs.microsoft.com/azure/cloud-services/cloud-services-role-enable-remote-desktop-powershell#configure-remote-desktop-from-powershell)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**:Azure 向けの Microsoft Antimalware は、Azure Cloud Services および Virtual Machines で使用できます。 これは、ウイルス、スパイウェアなどの悪意のあるソフトウェアの特定や駆除に役立つ無料のリアルタイム保護です。 これにより、既知の悪意あるソフトウェアや望ましくないソフトウェアが Azure システム上にソフトウェア自体をインストールまたは実行しようとしたときに、アラートが生成されます。 

マルウェア対策の構成を取得するには、PowerShell ベースのマルウェア対策コマンドレット (Get-AzureServiceAntimalwareConfig) を使用します。

Azure Cloud Services のスタートアップ タスクで PowerShell スクリプトを使用して、マルウェア対策拡張機能を有効にします。

自動化されたインテリジェントなエンドツーエンドのソリューションである Azure Security Center で、適応型アプリケーション制御機能を選択します。 マルウェアからコンピューターを保護し、悪意のあるアプリケーション (マルウェア対策ソリューションでは見逃される可能性のあるものを含む) の実行が試行されるのをブロックまたは警告することができます。

- [Azure Cloud Services にマルウェア対策拡張機能を自動的に追加する方法を教えてください](https://docs.microsoft.com/azure/cloud-services/cloud-services-configuration-and-management-faq#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [マルウェア対策のデプロイ シナリオ](https://docs.microsoft.com/azure/security/fundamentals/antimalware#antimalware-deployment-scenarios)

- [アダプティブ アプリケーション制御](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

- [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md)

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。 

サブスクリプション (運用、非運用など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。 

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 

- [連続エクスポートを構成する方法](../security-center/continuous-export.md) 

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

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

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
