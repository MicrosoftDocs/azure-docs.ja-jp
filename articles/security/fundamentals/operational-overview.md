---
title: Azure で運用可能なセキュリティの概要 | Microsoft Docs
description: この概要では、Azure で運用可能なセキュリティについて説明します。 運用可能なセキュリティとは、資産保護サービス、コントロール、および機能を指します。
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: 4bc30fbf342a9bc85b52c9f88ce7ca1df3c36e23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595499"
---
# <a name="azure-operational-security-overview"></a>Azure で運用可能なセキュリティの概要

[Azure で運用可能なセキュリティ](./operational-security.md)とは、ユーザーのデータ、アプリケーション、および Microsoft Azure にあるその他の資産を保護するために使用できる、サービス、コントロール、機能を指します。 これは、Microsoft 独自のさまざまな機能を通じて得られた知識を統合化したフレームワークです。 これらの機能には、Microsoft Security Development Lifecycle (SDL)、Microsoft Security Response Center プログラム、およびサイバーセキュリティ上の脅威に関する高度なノウハウが含まれています。

## <a name="azure-management-services"></a>Azure 管理サービス

IT 運用チームは、データセンター インフラストラクチャ、アプリケーション、データの管理を担当します。これには、こうしたシステムの安定性とセキュリティが含まれます。 ただし、多くの場合、複雑さを増す IT 環境のセキュリティを組織が把握するには、複数のセキュリティおよび管理システムのデータをまとめる必要があります。

[Microsoft Azure Monitor ログ](../../azure-monitor/overview.md)は、オンプレミスのインフラストラクチャやクラウド インフラストラクチャの管理および保護に役立つ、クラウドベースの IT 管理ソリューションです。 このソリューションのコア機能は、Azure で実行される次のサービスによって提供されます。 Azure には、オンプレミスおよびクラウド インフラストラクチャを管理して保護できる複数のサービスがあります。 各サービスでは、固有の管理機能が提供されます。 お客様は、複数のサービスを組み合わせて、さまざまな管理シナリオを実現することができます。 

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../../azure-monitor/overview.md) では、マネージド ソースから中央のデータ ストアへのデータの収集が行われます。 このデータには、API によって提供されるイベント、パフォーマンス データ、またはカスタム データを含めることができます。 収集されたデータは、アラート、分析、エクスポートに使用できます。

さまざまなソースからのデータを統合し、Azure サービスから得たデータを既存のオンプレミス環境と組み合わせることが可能です。 さらに、Azure Monitor ログではデータの収集とそのデータに対して実行される操作が明確に分離されているため、あらゆる種類のデータにすべての操作を実行できます。

### <a name="automation"></a>Automation

[Azure Automation](../../automation/automation-intro.md) を使用すると、クラウド環境およびエンタープライズ環境で一般的に実行される、手動で実行時間が長く、エラーが起こりやすく、頻繁に繰り返されるタスクを自動化する手段を得られます。 これによりお客様は、管理タスクにかかる時間を短縮すると共に、タスクの信頼性を高めることができます。 また、これらのタスクは一定の頻度で自動的に実行されるようスケジュールされます。 お客様は、Runbook を使用してプロセスを自動化したり、Desired State Configuration を使用して構成管理を自動化したりすることができます。

### <a name="backup"></a>Backup

[Azure Backup](../../backup/backup-overview.md) は、Microsoft Cloud のデータのバックアップ (または保護) と復元に使用できる、Azure ベースのサービスです。 Azure Backup では、既存のオンプレミスまたはオフサイトのバックアップ ソリューションを、信頼性の高い、セキュリティで保護された、コスト競争力のあるクラウド ベースのソリューションに置き換えます。

Azure Backup には複数のコンポーネントが用意されており、お客様はそれらを適切なコンピューター、サーバー、またはクラウドにダウンロードしてデプロイします。 デプロイするコンポーネント (エージェント) は、何を保護するかによって決まります。 Azure の Azure Recovery Services コンテナーにデータをバックアップするときは、すべての Azure Backup コンポーネントを使用できます (保護対象がオンプレミス データかクラウドのデータかに関係なく)。

詳しくは、[Azure Backup コンポーネントの表](../../backup/backup-overview.md#what-can-i-back-up)をご覧ください。

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) を使用すると、オンプレミスの仮想マシンと物理マシンを Azure (またはセカンダリ サイト) にレプリケートする際の調整を行って、ビジネス継続性を実現できます。 プライマリ サイトが使用できなくなった場合には、ユーザーが作業を継続できるよう、セカンダリの場所にフェールオーバーできます。 システムが使用できる状態に戻ったら、プライマリにフェールバックできます。 さらにインテリジェントで効果的な脅威の検出を実行するには、Azure Security Center を使います。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](../../active-directory/manage-apps/what-is-application-management.md) は、次の機能を提供する包括的な ID サービスです。

-   ID とアクセスの管理 (IAM) をクラウド サービスとして提供します。
-   一元的なアクセス管理、シングル サインオン (SSO)、およびレポート機能を提供します。
-   Azure Marketplaceで提供される[数千のアプリケーション](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) (Salesforce、Google Apps、Box、Concur など) に対する、統合的なアクセス管理をサポートします。

Azure AD には、必要な [ID 管理機能](./identity-management-overview.md#security-monitoring-alerts-and-machine-learning-based-reports)がすべて備わっています。これには、次の機能が含まれます。

- [多要素認証](../../active-directory/authentication/concept-mfa-howitworks.md)
- [セルフサービスによるパスワード管理](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [セルフサービスのグループ管理](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
- [特権アカウント管理](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md)
- [アプリケーション使用状況の監視](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [高度な監査](../../active-directory/reports-monitoring/concept-audit-logs.md)
- [セキュリティ監視とアラート](../../security-center/security-center-managing-and-responding-alerts.md)

Azure Active Directory を使用すると、パートナーと顧客 (ビジネスまたはコンシューマー) 向けに発行したすべてのアプリケーションに同じ ID およびアクセス管理の機能が備わります。 これにより、運用コストを大幅に減らすことができます。

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) を使用すると、Azure リソースのセキュリティを高度に視覚化 (および制御) しながら、脅威を回避し、検出し、それらに対応することができます。 Security Center では、各サブスクリプションに対するセキュリティ監視機能とポリシー管理機能が総合的に提供されます。 Security Center は、見つけにくい脅威の検出を支援すると共に、さまざまなセキュリティ ソリューションをまとめた広範なエコシステムとして機能します。

Security Center では、仮想マシン (VM) のセキュリティ設定を可視化し、脅威を監視することによって、Azure 上の[仮想マシンのデータを保護](../../security-center/security-center-introduction.md)できます。 Security Center は、仮想マシンの次の項目を監視できます。

- 推奨される構成規則を使用したオペレーティング システムセキュリティ設定。
- 不足しているシステムのセキュリティ更新プログラムと重要な更新プログラム。
- エンドポイント保護の推奨事項。
- ディスク暗号化の検証。
- ネットワークベースの攻撃。

Security Center では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) が使用されます。 Azure RBAC では、Azure 内のユーザー、グループ、およびサービスに割り当てることができる、[組み込みロール](../../role-based-access-control/built-in-roles.md)が提供されます。

Security Center は、リソースの構成を評価して、セキュリティの問題と脆弱性を特定します。 Security Center では、リソースが属するサブスクリプションまたはリソース グループの所有者、共同作業者、閲覧者のいずれかのロールが割り当てられているときにリソースに関連した情報のみが表示されます。

>[!Note]
>Security Center のロールと許可されているアクションの詳細については、「[Azure Security Center におけるアクセス許可](../../security-center/security-center-permissions.md)」を参照してください。

Security Center では、Microsoft Monitoring Agent が使用されます。 これは、Azure Monitor サービスで使用されるのと同じエージェントです。 このエージェントから収集されたデータは、VM の位置情報を考慮して、Azure サブスクリプションに関連付けられている既存の Log Analytics [ワークスペース](../../azure-monitor/logs/manage-access.md)または新規のワークスペースのいずれかに格納されます。

## <a name="azure-monitor"></a>Azure Monitor

クラウド アプリのパフォーマンスの問題は、ビジネスに影響を及ぼす場合があります。 複数の相互接続されたコンポーネントや頻繁なリリースにより、いつでもパフォーマンスの低下が生じる可能性があります。 さらに、アプリを開発している場合、通常、テストで見つからなかった問題はユーザーによって発見されます。 このような問題について即座に把握し、問題を診断して修正するためのツールを用意しておく必要があります。

[Azure Monitor](../../azure-monitor/overview.md) は、Azure 上で実行されているサービスを監視するための基本的なツールです。 サービスのスループットと周辺環境に関するインフラストラクチャ レベルのデータが示されます。 アプリをすべて Azure で管理しているのであれば、リソースのスケールアップやスケールダウンを検討する際、Azure Monitor が役立ちます。

またこのツールでは、監視データを使用して、アプリケーションに関する詳細なインサイトを取得することもできます。 そのような知識は、アプリケーションのパフォーマンスや保守容易性を向上させたり、手作業での介入が必要な操作を自動化したりするうえで役立ちます。

Azure Monitor には、次のコンポーネントが含まれています。

### <a name="azure-activity-log"></a>[Azure Activity Log (Azure アクティビティ ログ)]

[Azure アクティビティ ログ](../../azure-monitor/essentials/platform-logs-overview.md)では、サブスクリプションのリソースに対して実行された操作を調査できます。 アクティビティ ログではサブスクリプションのコントロール プレーン イベントが報告されるため、以前は "監査ログ" または "操作ログ" と呼ばれていました。

### <a name="azure-diagnostic-logs"></a>Azure 診断ログ

[Azure 診断ログ](../../azure-monitor/essentials/platform-logs-overview.md)はリソースによって出力され、そのリソースの操作に関する豊富なデータを提供します。 これらのログの内容は、リソースの種類によって異なります。

Windows イベント システム ログは、VM 用診断ログの 1 カテゴリです。 Blob ログ、テーブル ログ、およびキュー ログは、ストレージ アカウント用の診断ログ カテゴリです。

診断ログは、[アクティビティ ログ](../../azure-monitor/essentials/platform-logs-overview.md)とは異なります。 アクティビティ ログでは、サブスクリプションのリソースに対して実行された操作を調査できます。 診断ログでは、リソース自体が実行した操作を調査できます。

### <a name="metrics"></a>メトリック

Azure Monitor では、テレメトリを使用して、Azure 上のワークロードのパフォーマンスと正常性を可視化できます。 Azure テレメトリ データの種類の中でも最も重要なのが、[メトリック](../../azure-monitor/data-platform.md)です (パフォーマンス カウンターとも呼ばれます)。これはほとんどの Azure リソースから出力されます。 Azure Monitor では、このメトリックを複数の方法で構成して使用し、監視やトラブルシューティングを行うことができます。

### <a name="azure-diagnostics"></a>Azure Diagnostics

Azure Diagnostics を使用すると、デプロイされたアプリケーションで診断データを収集できます。 さまざまなソースで診断拡張機能を使用することができます。 現時点でのサポート対象は、[Azure クラウド サービスのロール](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service)、Microsoft Windows を実行している [Azure 仮想マシン](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service)、および [Azure Service Fabric](../../azure-monitor/agents/diagnostics-extension-overview.md) となっています。

## <a name="azure-network-watcher"></a>Azure Network Watcher

Azure では、仮想ネットワーク、Azure ExpressRoute、Azure Application Gateway、ロード バランサーなどのネットワーク リソースを調整および作成することで、エンドツーエンド ネットワークを作成できます。 各ネットワーク リソースは監視することができます。

エンド ツー エンド ネットワークでは、構成のあり方や、リソース間での相互作用が複雑化することがあります。 そうなると、シナリオが複雑化し、[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) を通じたシナリオベースの監視が必要になります。

Network Watcher を使用すると、Azure ネットワークの監視と診断を簡素化できます。 Network Watcher では、診断ツールと視覚化ツールを使用して、次のことが行なえます。

- Azure 仮想マシンに対してリモート パケット キャプチャを実行する。
- フロー ログを使用して、ネットワーク トラフィックに関するインサイトを取得する。
- Azure VPN Gateway と接続を診断する。

現在、Network Watcher が備える機能は次のとおりです。

- [トポロジ](../../network-watcher/view-network-topology.md): リソース グループ内のネットワーク リソース間のさまざまな相互接続および関係を確認できます。
- [可変パケット キャプチャ](../../network-watcher/network-watcher-packet-capture-overview.md): 仮想マシンで送受信されるパケット データをキャプチャします。 時間やサイズの制限を設定する機能など、詳細なフィルター オプションときめ細やかなコントロールにより、多様なキャプチャを行えます。 パケット データは、.cap 形式で BLOB ストアまたはローカル ディスクに保管できます。
- [IP フロー検証](../../network-watcher/network-watcher-ip-flow-verify-overview.md): フロー情報の 5 タプル パケット パラメーター (宛先 IP、発信元 IP、宛先ポート、発信元ポート、プロトコル) に基づいてパケットが許可されたか拒否されたかを確認します。 パケットがセキュリティ グループによって拒否された場合は、そのパケットを拒否した規則とグループが返されます。
- [次のホップ](../../network-watcher/network-watcher-next-hop-overview.md): Azure ネットワーク ファブリックにおけるルーティング対象パケットの次のホップを特定します。これにより、誤って構成されたユーザー定義のルーティングがあるかどうかを診断できます。
- [セキュリティ グループ ビュー](../../network-watcher/network-watcher-security-group-view-overview.md) - VM に適用されている有効な適用セキュリティ規則を確認できます。
- [ネットワーク セキュリティ グループの NSG フロー ログ](../../network-watcher/network-watcher-nsg-flow-logging-overview.md): そのグループのセキュリティ規則で許可または拒否されるトラフィックに関係するログを記録できます。 フローは 5 タプル情報 (送信元 IP、宛先 IP送信元ポート、宛先ポート、プロトコル) で定義されます。
- [仮想ネットワーク ゲートウェイと接続のトラブルシューティング](../../network-watcher/network-watcher-troubleshoot-manage-rest.md): 仮想ネットワーク ゲートウェイと接続に関する問題をトラブルシューティングできます。
- [ネットワーク サブスクリプションの制限](../../network-watcher/network-watcher-monitoring-overview.md): ネットワーク リソースの使用状況を制限と照らし合わせて確認できます。
- [診断ログ](../../network-watcher/network-watcher-monitoring-overview.md): 1 つのウィンドウで、リソース グループ内のネットワーク リソースの診断ログを有効化または無効化することができます。

詳しくは、[Network Watcher の構成](../../network-watcher/network-watcher-create.md)に関する記事をご覧ください。

## <a name="cloud-service-provider-access-transparency"></a>クラウド サービス プロバイダーのアクセスの透過性

[Microsoft Azure 用のカスタマー ロックボックス](customer-lockbox-overview.md)は Azure portal に統合されたサービスです。このサービスを使用することでお客様は、Microsoft サポート エンジニアが問題解決のためにご利用のデータにアクセスするとこを必要とする可能性があるまれな事例で明示的な制御を行うことができます。
リモート アクセスの問題をデバッグするなどの事例は、非常にまれであり、Microsoft サポート エンジニアは問題解決のために管理者特権のアクセス許可を必要とします。 そのようなケースでは、Microsoft サポート エンジニアは、Just-In-Time アクセス サービスを使用し、それによってアクセスがそのサービスに制限された、期限付きの制限された承認を取得します。  
Microsoft ではアクセスするにあたり、常にお客様の同意を得てきましたが、カスタマー ロックボックスによってお客様は Azure portal からそのような要求を確認し、承認または拒否することができるようになりました。 お客様が要求を承認するまで、Microsoft サポート エンジニアにはアクセス権は付与されません。

## <a name="standardized-and-compliant-deployments"></a>標準化されたデプロイおよび準拠しているデプロイ

[Azure Blueprint](../../governance/blueprints/overview.md) によってクラウド アーキテクトや中央の情報技術部門は、組織の標準、パターン、要件を実装および順守した反復可能な一連の Azure リソースを定義できます。  
これにより、DevOps チームは新しい環境を迅速に構築して立ち上げると共に、自分達が組織のコンプライアンスを維持するインフラストラクチャを使用して新しい環境を構築していることを確信することができます。
ブループリントは、さまざまなリソース テンプレートやその他のアーティファクトのデプロイを宣言によって調整する手法です。

- ロールの割り当て
- ポリシーの割り当て
- Azure Resource Manager のテンプレート
- リソース グループ

## <a name="devops"></a>DevOps

[DevOps (Developer Operations)](https://azure.microsoft.com/overview/what-is-devops/) のアプリケーション開発手法が普及する以前、開発チームは、ソフトウェア プログラムのビジネス要件の収集や、コードの記述を担当していました。 その後、別の QA チームが、分離開発環境でプログラムをテストしていました。 QA チームは、要件が満たされていることを確認したうえで、デプロイ用の運用コードをリリースしていました。 デプロイ チームは、ネットワークやデータベースなど、さらに細かいグループに分かれていました。 そのため、分離されたチーム間でソフトウェア プログラムが引き渡されるたびに、ボトルネックが発生していました。

DevOps を導入すれば、各チームが連携し、より安全で高品質なソリューションを、より高速かつ安価に提供できるようになります。 お客様は、ソフトウェアとサービスを使用するときに、動的で信頼性の高いエクスペリエンスを期待します。 各担当チームは、ソフトウェア更新時の反復作業を迅速に実行し、更新による影響を測定する必要があります。 また、新たな開発作業にすばやく対応し、問題を解決して、より高度な価値を提供する必要があります。  

Microsoft Azure などのクラウド プラットフォームでは、従来のボトルネックが除去されるため、インフラストラクチャの商品化に役に立ちます。 ソフトウェアは、ビジネスの成果における重要な差別化要因および要素としてすべてのビジネスに君臨します。 どの組織、開発者、IT worker も、DevOps の動きを回避できず、回避する必要もありません。

成熟した DevOps 実践者は、次のプラクティスのいくつかを採用します。 これらのプラクティスには、ビジネス シナリオに基づいて戦略を形作る[ユーザーが関係します](/azure/devops/learn/what-is-devops-culture)。 ツールは、さまざまなプラクティスの自動化に役立ちます。

- [アジャイル計画およびプロジェクト管理](https://www.visualstudio.com/learn/what-is-agile/)手法を使用して、作業の計画とスプリントへの分離、チームのキャパシティの管理、チームがビジネス ニーズの変化にすばやく適応するための支援を行います。
- [通常は Git を使用したバージョン コントロール](/azure/devops/learn/git/what-is-git)により、チームは世界中のどこにいてもソースを共有でき、ソフトウェア開発ツールと統合してリリース パイプラインを自動化できます。
- [継続的インテグレーション](/azure/devops/learn/what-is-continuous-integration)は、実行中のコードのマージとテストを推進します。これにより、障害を早期に検出できるようになります。  その他のメリットとして、マージの問題への取り組みや開発チームへの迅速なフィードバックに浪費される時間が短縮されます。
- 環境の保護とテストのためのソフトウェア ソリューションの[継続的デリバリー](/azure/devops/learn/what-is-continuous-delivery)により、組織はバグを迅速に修正し、絶えず変化するビジネス要件に対応できます。
- 実行中のアプリケーションの[監視](/azure/devops/learn/what-is-monitoring) (運用環境でのアプリケーションの正常性や顧客の使用状況など) は、組織が仮説を形成し、戦略を迅速に検証または誤りを証明するのに役立ちます。  豊富なデータがキャプチャされ、さまざまなログ形式で格納されます。
- [コードとしてのインフラストラクチャ (IaC)](/azure/devops/learn/what-is-infrastructure-as-code) は、ネットワークや仮想マシンの作成と切断の自動化と妥当性確認を有効にして、セキュリティで保護された、安定したアプリケーション ホスティング プラットフォームの提供を支援するプラクティスです。
- [マイクロサービス](/azure/devops/learn/what-are-microservices) アーキテクチャを使用して、ビジネス ユース ケースを小規模の再利用可能なサービスに分離します。  このアーキテクチャでは、スケーラビリティと効率性が実現されます。

## <a name="next-steps"></a>次の手順

セキュリティおよび監査ソリューションの詳細については、次の記事をご覧ください。

- [セキュリティとコンプライアンス](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](../../security-center/security-center-introduction.md)
- [Azure Monitor](../../azure-monitor/overview.md)