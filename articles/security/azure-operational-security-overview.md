---
title: "Azure で運用可能なセキュリティの概要 | Microsoft Docs"
description: "この記事では、Azure で運用可能なセキュリティの概要について説明します。"
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: tomsh
ms.openlocfilehash: f2153e783adb955cf9055b09ba9aa2592f51e4b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-operational-security-overview"></a>Azure で運用可能なセキュリティの概要
Azure で運用可能なセキュリティとは、ユーザーのデータ、アプリケーション、および Microsoft Azure にあるその他の資産を保護するために使用できる、サービス、コントロール、機能を指します。 [Azure で運用可能なセキュリティ](https://docs.microsoft.com/azure/security/azure-operational-security)は、Microsoft セキュリティ開発ライフサイクル (Security Development Lifecycle: SDL)、Microsoft セキュリティ レスポンス センター プログラム、サイバー セキュリティの脅威状況に対する深い認識など、Microsoft に固有のさまざまな機能の使用経験から得られた知識が組み込まれたフレームワークです。

この記事、「Azure で運用可能なセキュリティの概要」では、以下の領域を重点的に取り上げます。

- Azure Operations Management Suite
-   Azure Security Center
-   Azure Monitor
-   Azure Network Watcher
-   Azure Storage Analytics
-   Azure Active Directory

## <a name="azure-operations-management-suite"></a>Azure Operations Management Suite
IT 運用チームは、データセンター インフラストラクチャ、アプリケーション、データの管理を担当します。これには、こうしたシステムの安定性とセキュリティが含まれます。 ただし、多くの場合、複雑さを増す IT 環境のセキュリティを組織が把握するには、複数のセキュリティおよび管理システムのデータをまとめる必要があります。

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) は、Microsoft のクラウドベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。

OMS は、IT Automation、Security & Compliance、Log Analytics、Backup & Recovery など、多くのサービスを備えたクラウドベースの IT 管理ソリューションです。 このため、オンプレミスまたはクラウドの IT インフラストラクチャの管理と保護に最適なツールです。

OMS のコア機能は、Azure で実行される一連のサービスによって提供されます。 サービスごとに固有の管理機能があり、サービスを組み合わせてさまざまな管理シナリオを実現できます。 これには次のものが含まれます。

-   Log Analytics
-   Automation
-   Backup
-   Site Recovery

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) は、管理対象のリソースから中央リポジトリにデータを収集する OMS の監視サービスです。 このデータには、API 経由で提供されたイベント、パフォーマンス データ、カスタム データが含まれます。 一度収集されたデータは、アラート、分析、エクスポートに使用できます。 この方法を使用すると、さまざまなソースからのデータを統合できるため、Azure サービスから得たデータを既存のオンプレミス環境と組み合わせることが可能です。 さらに、データの収集とそのデータに対して実行される操作は明確に分離されているため、あらゆる種類のデータにすべての操作を実行できます。

### <a name="automation"></a>Automation
ユーザーは Microsoft [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) を使用すると、クラウド環境およびエンタープライズ環境で一般的に実行される、手動で実行時間が長く、エラーが起こりやすく、頻繁に繰り返されるタスクを自動化する手段を入手できます。 時間を節約し、普段の管理タスクの信頼性が向上し、一定の間隔で自動的に実行されるようにスケジュールを設定します。 Runbook を使用してプロセスを自動化したり、Desired State Configuration を使用して構成管理を自動化したりすることができます。

### <a name="backup"></a>バックアップ
[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) は、Microsoft Cloud のデータのバックアップ (または保護) と復元に使用できる、Azure ベースのサービスです。 Azure Backup では、既存のオンプレミスまたはオフサイトのバックアップ ソリューションを、信頼性の高い、セキュリティで保護された、コスト競争力のあるクラウド ベースのソリューションに置き換えます。 Azure Backup には複数のコンポーネントが用意されており、これを適切なコンピューター、サーバー、またはクラウドにダウンロードしてデプロイします。 デプロイするコンポーネント (エージェント) は、何を保護するかによって決まります。 Azure の Recovery Services コンテナーにデータをバックアップするときは、すべての Azure Backup コンポーネントを使用できます (保護対象がオンプレミス データかクラウドのデータかに関係なく)。 [Azure Backup コンポーネントの表](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use)をご覧ください。

### <a name="site-recovery"></a>Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) を使用すると、オンプレミスの仮想マシンと物理マシンを Azure (またはセカンダリ サイト) にレプリケートする際の調整を行って、ビジネス継続性を実現できます。 プライマリ サイトが使用できなくなった場合、ユーザーが作業を継続できるようにセカンダリの場所にフェールオーバーし、システムが使用できる状態に戻ったらフェールバックします。 インテリジェントで効果的な脅威の検出です。

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) は、マイクロソフトの包括的なサービスとしての ID (IDaaS) ソリューションであり、次のような特色があります。

-   クラウド サービスとして IAM を有効化
-   一元的なアクセス管理、シングル サインオン (SSO)、およびレポート機能を提供
-   Salesforce、Google Apps、Box、Concur などのアプリケーション ギャラリーの [数千のアプリケーション](https://azure.microsoft.com/marketplace/active-directory/) に対する統合アクセス管理のサポート

Azure AD には、[多要素認証](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)、[デバイスの登録]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview)、[セルフサービスのパスワード管理](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)、[セルフサービスのグループ管理](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)、[特権を持つアカウントの管理](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)、[ロールベースのアクセス制御](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)、[アプリケーション使用状況の監視](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)、[機能豊富な監査](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)、[セキュリティの監視とアラート](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)など、一連の [ID 管理機能](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports)も用意されています。

Azure Active Directory を使用すると、パートナーと顧客 (ビジネスまたはコンシューマー) 向けに発行したすべてのアプリケーションに同じ ID およびアクセス管理の機能が備わります。 これにより、運用コストを大幅に減らすことができます。

## <a name="azure-security-center"></a>Azure Security Center
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started) は、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応できるようにします。 これにより、サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) は、仮想マシンのセキュリティ設定を可視化し、脅威を監視することによって、Azure にある仮想マシンのデータを保護するのに役立ちます。 Security Center は、仮想マシンの次の項目を監視できます。

-   推奨される構成規則を使用したオペレーティング システム (OS) セキュリティ設定
-   不足しているシステムのセキュリティ更新プログラムと重要な更新プログラム
-   エンドポイント保護の推奨事項
-   ディスク暗号化の検証
-   ネットワークベースの攻撃

Azure Security Center では、[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) が使用されています。RBAC が提供する[組み込みのロール](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)は、Azure でユーザー、グループ、サービスに割り当てることができます。

Security Center は、リソースの構成を評価して、セキュリティの問題と脆弱性を特定します。 Security Center では、リソースが属するサブスクリプションまたはリソース グループの所有者、共同作業者、閲覧者のいずれかのロールが割り当てられているときにリソースに関連した情報のみが表示されます。

>[!Note]
>Security Center のロールと許可されているアクションの詳細については、「[Permissions in Azure Security Center (Azure Security Center のアクセス許可)](https://docs.microsoft.com/azure/security-center/security-center-permissions)」を参照してください。

Security Center は、Microsoft Monitoring Agent を使用します。これは Operations Management Suite と Log Analytics サービスで使用されるものと同じエージェントです。 このエージェントから収集されたデータは、VM の位置情報を考慮して、Azure サブスクリプションに関連付けられている既存の Log Analytics [ワークスペース](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)または新規のワークスペースのいずれかに格納されます。

## <a name="azure-monitor"></a>Azure Monitor
クラウド アプリのパフォーマンスの問題は、ビジネスに影響を及ぼす場合があります。 複数の相互接続されたコンポーネントや頻繁なリリースにより、いつでもパフォーマンスの低下が生じる可能性があります。 さらに、アプリを開発している場合、通常、テストで見つからなかった問題はユーザーによって発見されます。 このような問題について即座に把握し、問題を診断して修正するためのツールを用意しておく必要があります。

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) は、Azure 上で実行されているサービスを監視するための基本的なツールです。 サービスのスループットと周辺環境に関するインフラストラクチャ レベルのデータが示されます。 お使いのアプリすべてを Azure で管理している場合は、リソースをスケールアップするかスケールダウンするかを決定すると、Azure Monitor により、開始するために使用する情報が提示されます。

さらに、監視データを使用して、アプリケーションに関する深い洞察を得ることもできます。 そのような知識は、アプリケーションのパフォーマンスや保守容易性を向上させたり、手作業での介入が必要な操作を自動化したりするうえで役立ちます。 次の情報が含まれます。

-   [Azure Activity Log (Azure アクティビティ ログ)]
-   Azure 診断ログ
-   メトリック
-   Azure 診断

### <a name="azure-activity-log"></a>[Azure Activity Log (Azure アクティビティ ログ)]
サブスクリプションのリソースに対して実行された操作を調査できるログです。 [アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)ではサブスクリプションのコントロール プレーン イベントが報告されるため、以前は "監査ログ" または "操作ログ" と呼ばれていました。

### <a name="azure-diagnostic-logs"></a>Azure 診断ログ
[Azure 診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)はリソースによって出力され、そのリソースの操作に関する豊富なデータを提供します。 これらのログの内容は、リソースの種類によって異なります。

たとえば、Windows イベント システム ログは、VM、BLOB、テーブルの診断ログのカテゴリの 1 つであり、キューのログは、ストレージ アカウントの診断ログのカテゴリです。

診断ログは、[アクティビティ ログ (以前は監査ログまたは操作ログと呼ばれていたもの)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) とは異なります。 アクティビティ ログでは、サブスクリプションのリソースに対して実行された操作を調査できます。 診断ログでは、リソース自体が実行した操作を調査できます。

### <a name="metrics"></a>メトリック
Azure Monitor では、テレメトリを使用して、Azure のワークロードのパフォーマンスと正常性を視覚的に確認できます。 Azure テレメトリ データの種類の中でも最も重要なのは、Azure リソースのほとんどから出力されるメトリックであり、これはパフォーマンス カウンターとも呼ばれます。 Azure Monitor では、この[メトリック](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)を複数の方法で構成して使用することで、監視やトラブルシューティングを実行できます。

### <a name="azure-diagnostics"></a>Azure 診断
デプロイされたアプリケーションで診断データを収集できるようにする Azure 内の機能です。 さまざまなソースで診断拡張機能を使用することができます。 現時点でのサポート対象は、[Azure クラウド サービスの Web ロールおよび Worker ロール](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service)、Microsoft Windows を実行している [Azure Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service)、および [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) となっています。


## <a name="network-watcher"></a>Network Watcher
Azure では、VNet、ExpressRoute、Application Gateway、ロード バランサーなどのさまざまなネットワーク リソースを調整および作成することで、エンドツーエンド ネットワークを作成できます。 各ネットワーク リソースは監視することができます。

エンドツーエンド ネットワークの構成やリソース間の相互作用は複雑になることがあり、Network Watcher を使用してシナリオベースの監視を行う必要のある複雑なシナリオが生じる場合があります。

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) は、Azure ネットワークの監視と診断を簡素化します。 Network Watcher で使用可能な診断ツールと視覚化ツールを使用すると、Azure の仮想マシンでリモート パケットをキャプチャし、フロー ログを使用してネットワーク トラフィックを把握し、VPN Gateway と接続を診断できます。

現在、Network Watcher が備える機能は次のとおりです。

- [トポロジ](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) - リソース グループ内のネットワーク リソース間のさまざまな相互接続および関係をネットワーク レベルで確認できます。
-   [可変パケット キャプチャ](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) - 仮想マシンで送受信されるパケット データをキャプチャします。 時間やサイズの制限などを設定できる詳細なフィルター オプションときめ細やかなコントロールにより、多様なキャプチャを行うことができます。 パケット データは、.cap 形式で BLOB ストアまたはローカル ディスクに保管できます。
-   [IP フロー検証](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) - フロー情報の 5 タプル パケット パラメーター (宛先 IP、発信元 IP、宛先ポート、発信元ポート、プロトコル) に基づいてパケットが許可されたか拒否されたかを確認します。 パケットがセキュリティ グループにより拒否された場合、そのパケットを拒否した規則とグループが返されます。
-   [次のホップ](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) - Azure Network Fabric におけるルーティング対象パケットの次のホップを特定します。これにより、誤って構成されたユーザー定義のルーティングがあるかどうかを診断できます。
-   [セキュリティ グループ ビュー](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) - VM に適用された有効な適用セキュリティ規則を確認できます。
-   [NSG フロー ログ](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) - ネットワーク セキュリティ グループのフロー ログにより、そのグループのセキュリティ規則で許可または拒否されるトラフィックに関係するログを記録できます。 フローは 5 タプル情報 (送信元 IP、宛先 IP送信元ポート、宛先ポート、プロトコル) で定義されます。
-   [仮想ネットワーク ゲートウェイと接続のトラブルシューティング](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) - 仮想ネットワーク ゲートウェイと接続に関する問題をトラブルシューティングできます。
-   [ネットワーク サブスクリプションの制限](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) - ネットワーク リソースの使用状況を制限と照らし合わせて確認できます。
-   [診断ログの構成](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – 1 つのウィンドウで、リソース グループ内のネットワーク リソースの診断ログを有効化または無効化することができます。

Network Watcher の構成方法について詳しくは、[Network Watcher の構成](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)に関する記事をご覧ください。

## <a name="developer-operations-devops"></a>開発者の操作 (DevOps)
DevOps アプリケーション開発の前に、チームは、ソフトウェア プログラムのビジネス要件の収集やコードの記述を担当しました。 次に、別の QA チームがプログラムを分離開発環境でテストし、要件が満たされた場合は、運用のためのコードをリリースしてデプロイします。 デプロイ チームは、ネットワークやデータベースなどの孤立したグループにさらに細分化されます。 ソフトウェア プログラムが独立したチームに "丸投げ" されるたびに、ボトルネックが追加されます。

[DevOps](https://www.visualstudio.com/learn/what-is-devops/) を使用すると、チームはより安全で高品質のソリューションをより高速かつ安価に提供できます。 お客様は、ソフトウェアとサービスを使用するときに、動的で信頼性の高いエクスペリエンスを期待します。  チームは、ソフトウェアの更新をすばやく反復し、更新の影響を測定し、新しい開発の繰り返しにすばやく対応して、問題に対処したり、より多くの価値を提供したりする必要があります。  Microsoft Azure などのクラウド プラットフォームでは、従来のボトルネックが除去されるため、インフラストラクチャの商品化に役に立ちます。 ソフトウェアは、ビジネスの成果における重要な差別化要因および要素としてすべてのビジネスに君臨します。 どの組織、開発者、IT worker も、DevOps の動きを回避できず、回避する必要もありません。

成熟した DevOps 実践者は、次のプラクティスのいくつかを採用します。 これらのプラクティスには、ビジネス シナリオに基づいて戦略を形作る[ユーザーが関係します](https://www.visualstudio.com/learn/what-is-devops-culture/)。  ツールは、さまざまなプラクティスの自動化に役立ちます。

-   [アジャイル計画およびプロジェクト管理](https://www.visualstudio.com/learn/what-is-agile/)手法を使用して、作業の計画とスプリントへの分離、チームのキャパシティの管理、チームがビジネス ニーズの変化にすばやく適応するための支援を行います。
-   [通常は Git を使用したバージョン コントロール](https://www.visualstudio.com/learn/what-is-git/)により、チームは世界中のどこにいてもソースを共有でき、ソフトウェア開発ツールと統合してリリース パイプラインを自動化できます。
-   [継続的インテグレーション](https://www.visualstudio.com/learn/what-is-continuous-integration/)は、実行中のコードのマージとテストを推進します。これにより、障害を早期に検出できるようになります。  その他のメリットとして、マージの問題への取り組みや開発チームへの迅速なフィードバックに浪費される時間が短縮されます。
-   環境の保護とテストのためのソフトウェア ソリューションの[継続的配信](https://www.visualstudio.com/learn/what-is-continuous-delivery/)により、組織はバグを迅速に修正し、絶えず変化するビジネス要件に対応できます。
-   アプリケーションの正常性と顧客の使用状況に関する、運用環境を含む実行中のアプリケーションの[監視](https://www.visualstudio.com/learn/what-is-monitoring/)は、組織が仮説を形成し、戦略を迅速に検証または誤りを証明するのに役立ちます。  豊富なデータがキャプチャされ、さまざまなログ形式で格納されます。
-   [コードとしてのインフラストラクチャ (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) は、ネットワークや仮想マシンの作成と切断の自動化と妥当性確認を有効にして、セキュリティで保護された、安定したアプリケーション ホスティング プラットフォームの提供を支援するプラクティスです。
-   [マイクロサービス](https://www.visualstudio.com/learn/what-are-microservices/) アーキテクチャを活用して、ビジネス ユース ケースを小規模の再利用可能なサービスに分離します。  このアーキテクチャでは、スケーラビリティと効率性が実現されます。

## <a name="next-steps"></a>次のステップ
OMS の Security and Audit ソリューションについて詳しくは、次の記事をご覧ください。

- [Operations Management Suite | Security & Compliance](https://www.microsoft.com/cloud-platform/security-and-compliance)。
- [Operations Management Suite の Security and Audit ソリューションでのセキュリティの警告に対する監視と対応](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-responding-alerts)。
- [Operations Management Suite の Security and Audit ソリューションでのリソースの監視](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-monitoring-resources)。
