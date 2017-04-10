---
title: "Operations Management Suite (OMS) の概要 | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) は、Microsoft のクラウドベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。  この記事では、OMS の価値について説明して、OMS に含まれるさまざまなサービスとその内容を紹介するほか、詳細なコンテンツへのリンクを提示します。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: a0080a847764d538175d6b22d50d584383bd2054
ms.openlocfilehash: 3028da79b6a9bfae4c95a8139ecf8bbd7debc56a
ms.lasthandoff: 02/17/2017


---
# <a name="what-is-operations-management-suite-oms"></a>Operations Management Suite (OMS) とは
この記事では、Operations Management Suite (OMS) の概要を説明します。ここには、OMS によってもたらされるビジネス上の価値、OMS 内のサービスと管理ソリューション、多様なサービスとソリューションが&1; つにまとめられたサービス内容の簡単な概要が含まれます。  各サービスとソリューションのデプロイと使用に関する詳細なドキュメントへのリンクが記載されています。

## <a name="from-on-premise-to-the-cloud"></a>オンプレミスからクラウドへの移行
Microsoft は長年、エンタープライズ環境を管理するための製品を提供し続けてきました。  2007 年、管理製品の System Center スイートに多数の製品を統合しました。  この中には、ソフトウェア ディストリビューションやインベントリなどの機能を提供する Configuration Manager、システムとアプリケーションのプロアクティブな監視を行う Operations Manager、手動プロセスを自動化する Runbook が含まれた Orchestrator、重要なデータのバックアップと回復に使用する Data Protection Manager がありました。

コンピューティング リソースがクラウドに移行していくにつれ、Operations Manager と Orchestrator による Azure リソースの管理など、クラウド機能が System Center 製品に追加されていきました。  これらの製品は依然として基本的にオンプレミスのソリューションとして設計されていましたが、オンプレミスの管理環境のデプロイと保守にかなりの投資が必要です。  クラウドを十分に活用して将来のアプリケーションに対応するために、新しい管理手法が求められていました。

## <a name="introducing-operations-management-suite"></a>Operations Management Suite の概要
Operations Management Suite (OMS) は、最初からクラウド用に設計された一連の管理サービスです。  オンプレミスのリソースのデプロイと管理は行わず、OMS コンポーネント全体が Azure にホストされます。  構成が最小限で済むため、文字どおり数分で稼働させることができます。  

- **デプロイのコストと複雑さの最小化。**  OMS のすべてのコンポーネントとデータが Azure に格納されるため、オンプレミスのコンポーネントの難しい配置と投資を行うことなく、短期間で稼働して実行できます。
- **クラウド レベルへのスケール。**  不要なコンピューティング リソースに対する支払い、または記憶域スペースの不足を心配する必要はありません。これは、クラウドでは従量課金制に従って料金が決まり、必要な負荷に応じて簡単にスケールできるためです。  最初は少ないリソースの管理から始めて、その後環境全体にスケールアップできます。
- **最新機能の活用。**  OMS サービスの機能は継続的に追加、更新されています。  常に最新の機能を使用することができ、更新プログラムをデプロイする必要はありません。
- **統合されたサービス。**  各 OMS サービスは単独でも優れた価値を発揮しますが、組み合わせて使用して、複雑な管理シナリオに対応することもできます。  たとえば、Azure Automation の Runbook を使用すると、Azure Site Recovery でフェールオーバー プロセスを実行し、情報のログを Log Analytics に記録してアラートを生成できます。
- **グローバルな知識。**  OMS の管理ソリューションは最新情報に継続的にアクセスします。  たとえば、セキュリティと監査ソリューションは、世界中で検出されている最新の脅威を使って脅威分析を実行できます。
- **任意の場所からのアクセス。**  ブラウザーがあれば、どこからでも管理環境にアクセスできます。  OMS アプリをスマートフォンにインストールして、監視データにアクセス可能です。

### <a name="is-it-just-for-the-cloud"></a>クラウド以外での利用
OMS サービスがクラウドで実行されるからといって、オンプレミスの環境を効果的に管理できないわけではありません。  データ センター内の Windows コンピューターまたは Linux コンピューターにエージェントを配置すると、そのエージェントによって Log Analytics にデータが送信されます。Log Analytics で、このデータをクラウドまたはオンプレミスのサービスから収集されたその他すべてのデータと共に分析できます。  Azure Backup と Azure Site Recovery を使用すると、クラウドを活用して、オンプレミスのリソースのバックアップと高可用性を実現できます。  
クラウドの Runbook は通常オンプレミスのリソースにアクセスできませんが、エージェントは、データ センターで Runbook をホストする&1; つ以上のコンピューターにもインストールできます。  Runbook を開始するときは、単に、クラウドで実行するかローカル worker で実行するかを指定します。

## <a name="hybrid-management-with-system-center"></a>System Center によるハイブリッド管理
System Center を既にインストールしてある場合、これらのコンポーネントを OMS サービスと統合し、各製品の関連する専用機能を活用してオンプレミス環境とクラウド環境のどちらにも対応する、ハイブリッド ソリューションを実現できます。  既存の Operations Manager 管理グループを Log Analytics に接続して、クラウドで管理対象エージェントを分析します。  Data Protection Manager で既存のバックアップ プロセスを使用して、データをクラウドにバックアップします。  


## <a name="oms-services"></a>OMS サービス
OMS のコア機能は、Azure で実行される一連のサービスによって提供されます。  サービスごとに固有の管理機能があり、サービスを組み合わせてさまざまな管理シナリオを実現できます。

|| サービス | 説明 |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | 物理マシンや仮想マシンなど、さまざまなリソースの可用性とパフォーマンスを監視、分析します。 |
| ![Azure Automation](media/operations-management-suite-overview/icon-automation.png) | Automation | 手動プロセスを自動化し、物理マシンと仮想マシンの構成を適用します。 |
| ![Azure Backup](media/operations-management-suite-overview/icon-backup.png) | バックアップ | 重要なデータのバックアップと復元を行います。 |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | Site Recovery | 重要なアプリケーションの高可用性を実現します。 |

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) は、管理対象のリソースから中央リポジトリにデータを収集する OMS の監視サービスです。  このデータには、API 経由で提供されたイベント、パフォーマンス データ、カスタム データが含まれます。 一度収集されたデータは、アラート、分析、エクスポートに使用できます。  この方法を使用すると、さまざまなソースからのデータを統合できるため、Azure サービスから得たデータを既存のオンプレミス環境と組み合わせることが可能です。  さらに、データの収集とそのデータに対して実行される操作は明確に分離されているため、あらゆる種類のデータにすべての操作を実行できます。  

![Log Analytics の概要](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>データの収集
分析を行うためにデータを Log Analytics のリポジトリに収集する方法はさまざまです。

- **Windows または Linux のコンピューターと仮想マシン。**  データの収集元にする [Windows](../log-analytics/log-analytics-windows-agents.md) と [Linux](../log-analytics/log-analytics-linux-agents.md) のコンピューターまたは仮想マシンに Microsoft Monitoring Agent をインストールします。  エージェントによって、収集するイベントとパフォーマンス データを定義した構成が Log Analytics から自動的にダウンロードされます。  Azure で実行されている仮想マシンへのエージェントのインストールは、Azure Portal を使用して簡単に実行できます。  既存の Operations Manager 環境がある場合、管理グループを Log Analytics に接続して、既存の全エージェントからのデータ収集を自動的に開始することができます。
- **Azure サービス。**  Log Analytics によって [Azure 診断と Azure 監視](../log-analytics/log-analytics-azure-storage.md)からリポジトリにテレメトリが収集され、Azure リソースを監視できるようになります。
- **データ コレクター API。**  Log Analytics には、[任意のクライアントからデータを送信するための REST API](../log-analytics/log-analytics-data-collector-api.md) があります。  この API を使用すると、サード パーティ製のアプリケーションからデータを収集したり、カスタム管理シナリオを実装したりできます。  Azure Automation の Runbook を使用してデータを収集し、データ コレクター API を使用してそれをリポジトリに書き込む方法が一般的です。

#### <a name="reporting-and-analyzing-data"></a>データのレポートと分析
Log Analytics には、リポジトリに格納されたデータを抽出する強力なクエリ言語が備わっています。  すべてのソースのデータがレコードとして格納されるため、複数のソースのデータを&1; つのクエリで分析できます。
  
Log Analytics には、クエリからのデータをレポートして分析する方法がアドホック分析以外にも多数用意されています。

- **ビューとダッシュボード。**  [ビュー](../log-analytics/log-analytics-view-designer.md)と[ダッシュボード](../log-analytics/log-analytics-dashboards.md)では、ポータルでクエリの結果が視覚化されます。  管理ソリューションには通常、ソリューションからのデータを分析するビューが含まれます。  独自のカスタム ビューを作成してデータを分析し、カスタム ポータルでそれをすぐに使用可能にすることもできます。
- **エクスポート。**  Log Analytics の外で分析を行えるように、クエリの結果をエクスポートすることもできます。  優れた視覚化機能と分析機能を利用できる [Power BI](../log-analytics/log-analytics-powerbi.md) への定期的なエクスポートをスケジュールすることもできます。
- **ログ検索 API。**  Log Analytics には、[任意のクライアントからデータを収集するための REST API](../log-analytics/log-analytics-log-search-api.md) があります。  この API を使用すると、リポジトリに収集されたデータをプログラムで処理したり、別の監視ツールからアクセスしたりできます。

#### <a name="alerting"></a>アラート
問題を検出した場合、Log Analytics は[アラートで事前通知](../log-analytics/log-analytics-alerts.md)したり、是正措置を講じたりできます。  Log Analytics で行われるその他すべての分析と同様に、これはログ検索によって実行されます。  この検索は一定のスケジュールで実行され、結果が特定の基準に一致した場合にアラートが作成されます。

![Log Analytics alerts](media/operations-management-suite-overview/overview-alerts.png)

Log Analytics リポジトリでアラート レコードを作成できるほか、アラートでは次の操作を実行できます。

- **電子メール。**  検出された問題を事前に通知する電子メールを送信します。
- **Runbook。**  Log Analytics のアラートで Azure Automation の Runbook を開始できます。  これは通常、検出された問題の修正を試みるために実行されます。  Azure または別のクラウドで問題が発生している場合、Runbook はクラウドで開始できます。物理マシンまたは仮想マシンで問題が発生している場合、ローカル エージェントで Runbook を開始できます。
- **webhook。**  アラートを使って webhook を開始し、ログ検索の結果からデータを渡すことができます。  これを使用すると、代替アラート システムなどの外部サービスとの統合が可能になります。または、外部の Web サイトに対して是正措置を試行できます。

### <a name="azure-automation"></a>Azure Automation
[Azure Automation](http://azure.microsoft.com/documentation/services/automation) を使用すると、OMS でプロセスの自動化と構成管理を行えます。  手動プロセスを自動化でき、物理コンピューターと仮想コンピューターの構成を適用するのに役立ちます。  

#### <a name="process-automation"></a>プロセスの自動化
Azure Automation では、PowerShell スクリプトまたは PowerShell ワークフローに基づいた [Runbook](../automation/automation-runbook-types.md) を使用して手動プロセスを自動化できます。  ここには、複数の Runbook の間で共有できる変数や、Runbook の認証に必要な暗号化された情報を格納できるようにする資格情報と接続など、Runbook をサポートする資産も含まれます。
Runbook によるプロセスの自動化は、スイートの他のサービスで使用できます。  他の各サービスには PowerShell または REST API を使用してアクセスできるので、Runbook を作成して、Log Analytics での管理データの収集や Azure Backup によるバックアップの開始といった機能を実行できます。

##### <a name="accessing-resources"></a>リソースへのアクセス
Runbook は PowerShell に基づいているため、PowerShell コマンドレットでアクセスできるすべてのリソースを管理できます。  Automation アカウントに[モジュールを読み込む](../automation/automation-integration-modules.md)と、読み込んだモジュールが、そのアカウントのすべての Runbook で使用できるようになります。 
 
Runbook をクラウドで実行した場合、その Runbook はクラウドからアクセスできるすべてのリソースにアクセスできます。  たとえば、Azure サブスクリプション内のリソース、アマゾン ウェブ サービス (AWS) などの別のクラウド内のリソース、または REST API 経由でアクセスできるサービスなどです。  クラウドの Runbook は資格情報で実行されませんが、アクセスするリソースの認証に使用される資格情報、接続、証明書などの Automation 資産を活用できます。

クラウドで実行されている Runbook からデータ センターのリソースにアクセスすることはできませんが、  1 つ以上の [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md) をデータ センターにインストールすれば、ローカル リソースにアクセスする必要がある Runbook を実行できます。  Runbook を開始するときに、クラウドで実行するか特定の worker で実行するかを指定します。

![Azure Automation Runbook](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>Runbook の起動
Runbook は[複数の方法で開始](../automation/automation-starting-a-runbook.md)できるので、さまざまな管理シナリオで使用することができます。  

- **Azure Portal。**  Azure Automation は、他の Azure のサービスと同様に Azure Portal から管理できます。  Runbook を開始できるほか、Runbook をインポートしたり独自の Runbook を作成したりできます。
- **スケジュール設定。**  Runbook は定期的に開始されるようにスケジュールできます。  この機能を使えば、定期的な管理プロセスを自動で繰り返したり、Log Analytics にデータを自動で収集したりできます。
- **PowerShell と API。**  PowerShell コマンドレットまたは Azure Automation REST API を使用して Runbook を開始し、必要なパラメーター情報を渡すことができます。  
- **webhook。**  外部のアプリケーションまたは Web サイトから webhook を開始できる Runbook 向けに、webhook を作成できます。
- **Log Analytics のアラート。**  Log Analytics のアラートを使用すると、Runbook を自動で開始して、アラートによって特定された問題の修正を試行できます。

#### <a name="configuration-management"></a>構成管理
[PowerShell Desired State Configuration (DSC)](../automation/automation-dsc-overview.md) は、物理マシンと仮想マシンの構成をデプロイして適用できるようにする、Windows PowerShell の管理プラットフォームです。  Azure Automation は DSC 構成を管理し、必要な構成を取得するためにエージェントがアクセスできるプル サーバーをクラウド上に用意します。

![Azure Automation DSC](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Azure Backup と Azure Site Recovery
Azure Backup と Azure Site Recovery はビジネス継続性と障害復旧に役立ちます。  これらの各サービスには、障害が発生したときにアプリケーションの可用性を確保し、システムがオンライン状態に戻ったときに通常の運用状態に回復するのに役立つ機能が用意されています。  どちらのサービスも、組織で定義されている目標復旧時点 (RPO) と目標復旧時間 (RTO) を達成するのに役立ちます。 RPO では、障害発生時に使用できなくなっても許容できるデータの上限を定義します。また、RTO では、障害発生時にサービスまたはアプリが使用不可能になっても許容できる時間を制限します。

#### <a name="azure-backup"></a>Azure Backup
[Azure Backup](http://azure.microsoft.com/documentation/services/backup) を使用すると、OMS でデータのバックアップとサービスの復元を行えます。  アプリケーション データを保護し、長年にわたってデータを保管できます。設備投資は不要で、運用コストも最小限で済みます。  SQL Server や SharePoint などのアプリケーション ワークロードだけでなく、Windows の物理サーバーや仮想サーバーのデータもバックアップできます。  また、System Center Data Protection Manager (DPM) では、Azure Backup を使用して、保護データを Azure にレプリケートし、冗長性と長期保存を実現しています。

Azure Backup で保護されるデータは、特定の地域リージョンにあるバックアップ コンテナーに格納されます。 データは同じリージョン内でレプリケートされます。コンテナーの種類によっては、回復性を高めるために別のリージョンにレプリケートされることもあります。

Azure Backup には、3 つの基本的なシナリオがあります。

- **Azure Backup エージェントがインストールされた Windows マシン。** Windows サーバーまたは Windows クライアントから直接 Azure Backup コンテナーにファイルとフォルダーをバックアップします。<br><br>![Azure Backup エージェントがインストールされた Windows マシン](media/operations-management-suite-overview/overview-backup-01.png)
- **System Center Data Protection Manager (DPM) または Microsoft Azure Backup Server。** DPM または Microsoft Azure Backup Server を利用して、SQL や SharePoint などのアプリケーション ワークロードに加えてファイルとフォルダーをローカル ストレージにバックアップした後、Azure Backup コンテナーにレプリケートします。 Hyper-V または VMware の Windows 仮想マシンと Linux 仮想マシンをサポートします。<br><br>![System Center Data Protection Manager (DPM) または Microsoft Azure Backup Server](media/operations-management-suite-overview/overview-backup-02.png)
- **Azure 仮想マシン拡張機能。** Azure 内の Windows 仮想マシンまたは Linux 仮想マシンを、Azure Backup コンテナーにバックアップします。<br><br>![Azure 仮想マシン拡張機能](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) を使用すると、オンプレミスの仮想マシンと物理マシンを Azure (またはセカンダリ サイト) にレプリケートする際の調整を行って、ビジネス継続性を実現できます。 プライマリ サイトが使用できなくなった場合、ユーザーが作業を継続できるようにセカンダリの場所にフェールオーバーし、システムが使用できる状態に戻ったらフェールバックします。 

Azure Site Recovery は、サーバーとアプリケーションの高可用性を実現します。  また、オンプレミスの Hyper-V 仮想マシン、VMware 仮想マシン、物理 Windows/Linux サーバーのレプリケーション、フェールオーバー、回復を調整して、ビジネスの継続性と障害復旧 (BCDR) の戦略に貢献します。 マシンをセカンダリ データセンターにレプリケートしたり、Azure にレプリケートしてデータセンターを拡張したりすることができます。 Site Recovery で、ワークロードの単純なフェールオーバーと回復も実行できます。 SQL Server AlwaysOn などの障害復旧メカニズムと統合して、複数のマシンにわたって階層化されたワークロードの簡単なフェールオーバーを行うための復旧計画を提供します。

Azure Site Recovery には、3 つの基本的なレプリケーション シナリオがあります。

- **Hyper-V 仮想マシンのレプリケーション。**  Hyper-V 仮想マシンが VMM クラウドで管理されている場合、セカンダリ データセンターまたは Azure Storage にレプリケートできます。 Azure へのレプリケーションは、セキュリティで保護されたインターネット接続を経由して行われます。 セカンダリ データセンターへのレプリケーションは、LAN 経由で行われます。  Hyper-V 仮想マシンが VMM で管理されていない場合は、Azure Storage のみにレプリケートできます。 Azure へのレプリケーションは、セキュリティで保護されたインターネット接続を経由して行われます。<br><br>![Hyper-V 仮想マシンのレプリケーション](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **VMware 仮想マシンのレプリケーション。**  VMware を実行するセカンダリ データセンターまたは Azure Storage に VMware 仮想マシンをレプリケートできます。 Azure へのレプリケーションは、サイト間 VPN、Azure ExpressRoute、またはセキュリティで保護されたインターネット接続を経由して行われます。 セカンダリ データセンターへのレプリケーションは、InMage Scout データ チャネルを経由して行われます。<br><br>![VMware 仮想マシンのレプリケーション](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **Windows と Linux の物理サーバーのレプリケーション。**  セカンダリ データセンターまたは Azure Storage に物理サーバーをレプリケートできます。 Azure へのレプリケーションは、サイト間 VPN、Azure ExpressRoute、またはセキュリティで保護されたインターネット接続を経由して行われます。 セカンダリ データセンターへのレプリケーションは、InMage Scout データ チャネルを経由して行われます。 Azure Site Recovery には、複数の統計情報を表示する OMS ソリューションが用意されていますが、なんらかの操作を行うには Azure ポータルを使用する必要があります。<br><br>![Windows および Linux の物理サーバーのレプリケーション](media/operations-management-suite-overview/overview-siterecovery-physical.png)


Site Recovery では、特定の Azure リージョンにあるコンテナーにメタデータが格納されます。 レプリケートされたデータは Site Recovery サービスによって格納されません。

## <a name="management-solutions"></a>管理ソリューション
[管理ソリューション](operations-management-suite-solutions.md)は、1 つ以上の OMS サービスを活用する特定の管理シナリオが実装された、パッケージになった一連のロジックです。  さまざまなソリューションが Microsoft とパートナーによって提供されており、Azure サブスクリプションに簡単に追加して、OMS への投資の価値を高めることができます。  パートナーは、アプリケーションとサービスをサポートする独自のソリューションを作成し、Azure Marketplace またはクイック スタート テンプレートを使用してユーザーに提供できます。

複数のサービスを活用して追加の機能を提供するソリューションの良い例は、[更新管理ソリューション](oms-solution-update-management.md)です。  このソリューションでは、Windows と Linux 用の Log Analytics エージェントを使用して、各エージェントから必要な更新プログラムの情報を収集します。  このデータは、付属のダッシュボードを使ってデータを分析できる Log Analytics リポジトリに書き込まれます。  必要な更新プログラムをインストールするために、デプロイ作成時に Azure Automation の Runbook が使用されます。  このプロセスはすべてポータルで管理するため、根底にある詳細について配慮する必要はありません。

![解決策](media/operations-management-suite-overview/overview-solution.png)

ほとんどのソリューションは、次の機能のうち少なくとも&1; つを実行します。

- 追加情報の収集。  Log Analytics では、イベントやパフォーマンス データなど、さまざまなデータをクライアントとサービスから収集します。  管理ソリューションでは、他のデータ ソースからは入手できない追加の情報を収集できます。そのために、多くの場合、Azure Automation Runbook が使用されます。
- 収集された情報の追加の分析。  管理ソリューションには、データの分析と視覚化を行えるダッシュボードとビューが含まれています。  これらのリンクを使用すると、詳細なデータを掘り下げて調査できる、事前定義されたログ検索に戻ります。  リポジトリに既に収集されているデータの分析も実行できます。たとえば、セキュリティ イベントの間で、脅威を示唆するパターンを検索できます。
- 機能の追加。  Microsoft が提供する一部のソリューションはコア サービスの機能に基づいており、追加の機能があります。  たとえばサービス マップには、検出用の独自のコンソールが用意されており、サーバーとプロセスの依存関係がリアルタイムでマップされます。
OMS へのソリューションの追加は Microsoft とパートナーによって定期的に行われており、投資の価値を継続的に高めることができます。  Microsoft のソリューションは、OMS ポータルのソリューション カタログ経由で参照してインストールできます。または、Azure Portal の Azure Marketplace 経由で Microsoft とパートナー両方のソリューションを参照してインストールできます。  

![ソリューション ギャラリー](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>次のステップ
* [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)の詳細を確認する。
* [Azure Automation](../automation/automation-intro.md)の詳細を確認する。
* [Azure Backup](http://azure.microsoft.com/documentation/services/backup)の詳細を確認する。
* [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)の詳細を確認する。
* さまざまな OMS サービスで[使用できるソリューション](../log-analytics/log-analytics-add-solutions.md)を探す。 


