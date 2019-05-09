---
title: Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP Automation のためのデータ ウェアハウス
description: Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP Automation のためのデータ ウェアハウス
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 3c78aed2f30ea85f5bc16a8c0fb270bb1c761be8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539515"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure のセキュリティとコンプライアンスのブループリント:FedRAMP Automation のためのデータ ウェアハウス

## <a name="overview"></a>概要

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) は、米国政府全体で実行されるプログラムであり、クラウド製品とサービスに対するセキュリティの評価、承認、および継続的な監視に対する標準化された手法を提供します。 この「Azure のセキュリティとコンプライアンスのブループリント」では、FedRAMP の高度なコントロールのサブセットを実装する Microsoft Azure のデータ ウェアハウス アーキテクチャを配信する方法についてガイダンスを示します。 このソリューションは、一般的なリファレンス アーキテクチャでの Azure リソースのデプロイと構成に関するガイダンスを示し、お客様が特定のセキュリティ要件と準拠要件をどのように満たすことができるかを示し、お客様が Azure 上に独自のデータ ウェアハウス ソリューションをビルドして構成するための基礎として機能します。

このリファレンス アーキテクチャ、関連するコントロール実装ガイド、および脅威モデルは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。 変更せずにこの環境にアプリケーションをデプロイすることは、FedRAMP High ベースラインの要件を完全に満たすためには不十分です。 以下の点に注意してください。
- このアーキテクチャは、お客様が FedRAMP に準拠した方法で Azure にワークロードをデプロイするためのベースラインを提供します。
- お客様は、このアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスの評価を実施する責任を負います。要件は、お客様の実装によって変化する可能性があるからです。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント

このソリューションは、高パフォーマンスのセキュリティで保護されたクラウド データ ウェアハウスを実装するデータ ウェアハウス参照アーキテクチャを提供します。 このアーキテクチャには、2 つの分離されたデータ層があります。片方は、クラスター化された SQL 環境の中で、データのインポート、格納、およびステージングが行われる層であり、他方は、処理するためにデータが ETL ツール (例: [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) の T-SQL クエリ) を使用して読み込まれるAzure SQL Data Warehouse 用の層です。 データが SQL Data Warehouse に格納された後、大規模な分析を実行できます。

Microsoft Azure では、お客様向けのさまざまなレポートと分析サービスを提供しています。 このソリューションには、Azure SQL Data Warehouse からレポートをすばやく作成するための SQL Server Reporting Services (SSRS) が含まれています。 すべての SQL トラフィックは、自己署名証明書を含めることによって、SSL で暗号化されます。 ベスト プラクティスとして、Azure では、セキュリティ強化のために信頼された証明書機関を利用することをお勧めします。

Azure SQL Data Warehouse 内のデータは、単票形式ストレージを持つリレーショナル テーブルに格納されます。この形式は、クエリのパフォーマンスを向上させると同時に、データ ストレージ コストを大幅に削減します。  用途の要件に応じて、コンピューティング リソースを必要とするアクティブなプロセスが存在しない場合に、Azure SQL Data Warehouse のコンピューティング リソースのスケールアップ、スケールダウン、または完全なシャット ダウンを実行できます。

SQL ロード バランサーは、SQL トラフィックを管理することで高パフォーマンスを保証します。 この参照アーキテクチャ内のすべての仮想マシンは、高可用性とディザスター リカバリー機能のために AlwaysOn 可用性グループ内に構成された SQL Server インスタンスを使用して可用性セット内にデプロイされます。

このデータ ウェアハウスの参照アーキテクチャには、アーキテクチャ内のリソースを管理するための Active Directory (AD) 層も含まれています。 Active Directory サブネットによって、大規模な AD フォレスト構造への容易な導入が可能になり、大規模なフォレストへのアクセスが利用できない場合でも、環境を継続的に運用できます。 すべての仮想マシンは、Active Directory 層に対してドメイン参加済みであり、Active Directory グループ ポリシーを使用して、オペレーティング システム レベルでセキュリティとコンプライアンスの構成が適用されます。

仮想マシンは管理要塞ホストとして機能し、デプロイされたリソースにアクセスするためのセキュリティで保護された接続を管理者に提供します。 データは、この管理要塞ホストを通してステージング領域に読み込まれます。 **Azure では参照アーキテクチャのサブネットに対する管理とデータ インポートのために、VPN または Azure ExpressRoute 接続を構成することを推奨しています。**

![FedRAMP のためのデータ ウェアハウスの参照アーキテクチャの図](images/fedramp-datawarehouse-architecture.png?raw=true "FedRAMP のためのデータ ウェアハウスの参照アーキテクチャの図")

このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャの詳細については、「[デプロイメント アーキテクチャ](#deployment-architecture)」セクションを参照してください。

Azure Virtual Machines
-   (1) 要塞ホスト
-   (2) Active Directory ドメイン コントローラー
-   (2) SQL Server クラスター ノード
-   (1) SQL Server 監視

可用性セット
-   (1) Active Directory ドメイン コントローラー
-   (1) SQL クラスター ノードとミラーリング監視

Virtual Network
-   (4) サブネット
-   (4) ネットワーク セキュリティ グループ

SQL Data Warehouse

SQL Server Reporting Services

Azure SQL Load Balancer

Azure Active Directory

Recovery Services コンテナー

Azure Key Vault

Azure Monitor ログ

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ

以下のセクションでは、開発と実装の要素について詳しく説明します。

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) は、超並列処理 (MPP) を利用して、ペタバイト単位のデータに対して複雑なクエリを短時間で実行するエンタープライズ データ ウェアハウス (EDW) です。 簡単な PolyBase T-SQL クエリで SQL Data Warehouse にビッグ データをインポートしてから、MPP の機能を利用して高パフォーマンスの分析を実行します。

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) によって、Azure SQL Data Warehouse のテーブル、グラフ、マップ、ゲージ、マトリックスなどを含むレポートをすばやく作成できます。

**要塞ホスト**:要塞ホストは、この環境にデプロイされたリソースへのユーザーのアクセスを許可する単一エントリ ポイントです。 要塞ホストは、セーフ リスト上のパブリック IP アドレスからのリモート トラフィックのみを許可することで、デプロイ済みのリソースへのセキュリティで保護された接続を提供します。 リモート デスクトップ (RDP) トラフィックを許可するには、トラフィックのソースがネットワーク セキュリティ グループ (NSG) に定義されている必要があります。

仮想マシンは、次の構成を使用してドメイン参加済み要塞ホストとして作成されています。
-   [マルウェア対策拡張機能](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Monitor ログ拡張機能](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure 診断拡張機能](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault を使用した [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) (Azure Government、PCI DSS、HIPAA、およびその他の要件に準拠)
-   未使用時の仮想マシン リソースの消費を抑えるための[自動シャットダウン ポリシー](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) の有効化。実行中のオペレーティング システムから分離されている保護された環境で、資格情報とその他のシークレットが実行されるようにします。

### <a name="virtual-network"></a>仮想ネットワーク
この参照アーキテクチャは、10.0.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

**ネットワーク セキュリティ グループ**:[NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) には、VNet 内のトラフィックを許可または拒否するアクセス制御リスト (ACL) が含まれます。 NSG を使用して、サブネットまたは個々の VM レベルでトラフィックを保護できます。 次の NSG が存在します。
  - データ層 (SQL Server クラスター、SQL Server 監視、および SQL Load Balancer) 用の NSG
  - 管理要塞ホスト用の NSG
  - Active Directory 用の NSG
  - SQL Server Reporting Services 用の NSG

各 NSG では、ソリューションが安全かつ適切に機能できるように、固有のポートとプロトコルが開かれます。 さらに、各 NSG で次の構成が有効になります。
  - [診断ログとイベント](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)が有効化され、ストレージ アカウントに格納される
  - Azure Monitor ログが [NSG の診断](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)に接続される

**サブネット**:各サブネットは、対応する NSG に関連付けられています。

### <a name="data-at-rest"></a>保存データ
このアーキテクチャでは、暗号化、データベース監査などの手段によって保存データを保護します。

**Azure Storage**: 暗号化された保存データの要件を満たすために、すべての [Azure Storage](https://azure.microsoft.com/services/storage/) で [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption) が使用されます。

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、Windows の BitLocker 機能を活用して、OS とデータ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Azure Key Vault と統合されています。

**Azure SQL Database** Azure SQL データベース インスタンスは、次のデータベース セキュリティ対策を使用します。
-   [AD 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)を使用して、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
-   [SQL Database の監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
-   SQL Database は、[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用するように構成されます。TDE は、データとログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 TDE は、保存されているデータが未承認のアクセスの対象になっていないことを保証します。
-   [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
-   [SQL の脅威検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)は、不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンに対するセキュリティの警告を提供することで、潜在的な脅威の検出とそれらの脅威への対応を可能にします。
-   [Always Encrypted の列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)により、データベース システム内で機密データがプレーンテキストとして表示されることはありません。 データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
-   [SQL Database 動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)は、参照アーキテクチャがデプロイされた後に実行できます。 お客様は、お使いのデータベース スキーマに準拠するように動的データ マスクの設定を調整する必要があります。

### <a name="business-continuity"></a>ビジネス継続性
**高可用性**: サーバー ワークロードは、Azure の仮想マシンの高可用性を確保するために[可用性セット](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にグループ化されます。 Azure SLA を 99.95% 満たすように、計画または計画外メンテナンス イベントの間に、少なくとも 1 つの仮想マシンが利用可能です。

**Recovery Services コンテナー**: [Recovery Services コンテナー](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)にはバックアップ データが保管され、このアーキテクチャ内の Azure 仮想マシンのすべての構成が保護されます。 Recovery Services コンテナーを使用すると、VM 全体を復元せずに IaaS VM からファイルとフォルダーを復元できるため、復元時間を短縮できます。

### <a name="logging-and-audit"></a>ログ記録と監査
[Azure Monitor ログ](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)は、システムの正常性だけではなく、システムとユーザーのアクティビティを詳細に記録します。 [Azure Monitor ログ](https://azure.microsoft.com/services/log-analytics/) ソリューションは、Azure やオンプレミス環境のリソースによって生成されるデータを収集して分析します。
- **アクティビティ ログ**: [アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。
- **診断ログ**: [診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログと、Azure Blob Storage、テーブル、キューのログが含まれます。
- **ファイアウォール ログ**: Application Gateway は、完全な診断ログとアクセス ログを提供します。 ファイアウォール ログは、WAF が有効になっている Application Gateway リソースで使用できます。
- **ログ アーカイブ**: すべての診断ログは、暗号化された集中型の Azure ストレージ アカウントに書き込まれ、定義済みのリテンション期間である 2 日間にわたってアーカイブされるように構成されます。 これらのログは、処理、格納、およびダッシュボードでのレポート表示を行うために、Azure Monitor ログに接続されます。

さらに、このアーキテクチャの一部として、次の監視ソリューションが含まれます。
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment):この Active Directory 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware):このマルウェア対策ソリューションでは、マルウェア、脅威、および保護の状態に関する報告を行います。
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker):Azure Automation ソリューションは、Runbook の格納、実行、および管理を行います。
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started):Security and Audit ダッシュボードでは、セキュリティ ドメイン、注意が必要な問題、検出、脅威に関する知識、および一般的なセキュリティの照会に関するメトリックを提供することで、リソースのセキュリティ ステータスに対する高度な分析情報を提供します。
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment):この SQL 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧をお客様に提供します。
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management):Update Management ソリューションでは、利用可能な更新プログラムのステータスや必要な更新プログラムのインストール プロセスなど、オペレーティング システムのセキュリティ更新プログラムをお客様が管理できるようにします。
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth):Agent Health ソリューションは、デプロイされたエージェント数とその地理的配置に加え、応答しないエージェント数やオペレーショナル データを送信しているエージェント数を報告します。
-   [Azure Activity Logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity):Activity Log Analytics ソリューションは、顧客向けのすべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。
-   [Change Tracking](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity):Change Tracking ソリューションでは、お客様が環境の変更を簡単に識別できるようにします。

### <a name="identity-management"></a>ID 管理
次のテクノロジによって、Azure 環境に ID 管理機能が提供されます。
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) は、Microsoft が提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 ソリューションのすべてのユーザー (SQL Database にアクセスするユーザーを含む) は、Azure Active Directory で作成されています。
-   アプリケーションに対する認証は Azure AD を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。 さらに、データベース列の暗号化では、アプリケーションを Azure SQL Database に対して認証するために Azure AD が使用されます。 詳細については、[SQL Database で機密データを保護する](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)方法に関するページを参照してください。
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) は、組織の ID に影響を及ぼす可能性のある脆弱性を検出し、組織の ID に関連する検出された不審なアクションに対する自動応答を構成します。さらに、不審なインシデントを調査し、適切なアクションを実行してそれらを解決します。
-   [Azure のロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) は、Azure の対象を絞ったアクセス管理を実現します。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。

Azure SQL Database のセキュリティ機能の使用方法の詳細については、「[Contoso Clinic デモ アプリケーション](https://github.com/Microsoft/azure-sql-security-sample)」サンプルをご覧ください。

### <a name="security"></a>セキュリティ
**シークレットの管理**:ソリューションでは、キーとシークレットの管理に [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) が使用されます。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。

**マルウェア対策**: 仮想マシン向けの [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) は、ウイルスやスパイウェアなどの悪意のあるソフトウェアを識別して削除するリアルタイム保護機能を提供し、既知のマルウェアや不要なソフトウェアが保護されている仮想マシンへのインストールまたは実行を試みた場合に警告する構成可能なアラートを備えています。

**更新プログラムの管理**: この参照アーキテクチャの一部としてデプロイされる Windows 仮想マシンは、既定で Windows Update サービスから自動的に更新プログラムを受け取るように構成されます。 また、このソリューションには、[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) サービスが含まれます。このサービスによって、必要に応じて仮想マシンにパッチを適用するために、更新されたデプロイが作成される場合があります。


## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項
### <a name="expressroute-and-vpn"></a>ExpressRoute と VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) またはセキュリティ保護された VPN トンネルは、デプロイされるリソースへの接続を安全に確立するために、このデータ ウェアハウス参照アーキテクチャの一部として構成される必要があります。 ExpressRoute 接続はインターネットを経由しないため、これらの接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 ExpressRoute または VPN を適切に設定することで、お客様は転送中のデータに対して保護のレイヤーを追加できます。

### <a name="extract-transform-load-etl-process"></a>抽出、変換、読み込み (ETL) プロセス
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) は、別の ETL やインポート ツールなしで Azure SQL Data Warehouse にデータを読み込むことができます。 PolyBase では、T-SQL クエリを使用してデータにアクセスできます。 Microsoft のビジネス インテリジェンスと分析スタックに加え、SQL Server 対応のサードパーティ ツールを PolyBase で使用できます。

### <a name="azure-active-directory-setup"></a>Azure Active Directory の設定
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) は、デプロイの管理と環境を操作する担当者へのアクセスのプロビジョニングを行うために不可欠です。 既存の Windows Server Active Directory は、[4 回のクリック](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)で AAD と統合できます。 また、デプロイされた Active Directory インフラストラクチャを AAD フォレストのサブドメインにすることで、デプロイされた Active Directory インフラストラクチャ (ドメイン コントローラー) を既存の AAD に連結できます。

### <a name="additional-services"></a>その他のサービス
このデータ ウェアハウス アーキテクチャは[Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) 環境へのデプロイを意図していませんが、この参照アーキテクチャに示されたサービスと、Azure Commercial 環境だけで使用できる追加のサービスを利用して、ほぼ同じ目的を実現できます。 Azure Commercial では、中程度の影響レベルで FedRAMP JAB P-ATO を維持し、官庁やパートナーが Azure Commercial 環境を活用しているクラウドに中程度の機密情報をデプロイできるようにしています。

Azure Commercial では、書式設定されているデータ ストレージと書式設定されていないデータ ストレージ、およびデータ ウェアハウスに使用されるステージングを処理する、次のようなさまざまなサービスが提供されています。
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) は、ETL (抽出 - 変換 - 読み込み)、ELT (抽出 - 読み込み - 変換)、データ統合という複雑なハイブリッド プロジェクト用に構築された、管理クラウド サービスです。 Azure Data Factory を使用すると、さまざまなデータ ストアからデータを取り込むデータ駆動型ワークフロー (パイプラインと呼ばれます) を作成し、スケジュールを設定できます。 お客様は、Azure SQL Data Warehouse などのデータ ストアに出力できるように、データを処理して変換できます。
-   [Azure Data Lake Store ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) を使用すると、運用分析や調査分析を目的として任意のサイズ、種類、および取り込み速度のデータを 1 か所でキャプチャすることができます。 Azure Data Lake Store は Hadoop エコシステムのほとんどのオープン ソース コンポーネントと互換性があり、Azure SQL Data Warehouse などの他の Azure サービスと緊密に統合しています。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラム (DFD) を[ダウンロード](https://aka.ms/blueprintdwthreatmodel)できます。または下記を参照してください。

![FedRAMP 用データ ウェアハウス脅威モデル](images/fedramp-datawarehouse-threat-model.png?raw=true "FedRAMP 用データ ウェアハウス脅威モデル")

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント
[Azure Security and Compliance Blueprint – FedRAMP High Customer Responsibility Matrix](https://aka.ms/blueprinthighcrm) (Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP の高度なベースラインでのお客様の責任に関するマトリックス) では、FedRAMP の高度なベースラインで必要とされるすべてのセキュリティ コントロールの一覧を示しています。 同様に、[Azure Security and Compliance Blueprint – FedRAMP Moderate Customer Responsibility Matrix](https://aka.ms/blueprintcrmmod) (Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP の中程度のベースラインでのお客様の責任に関するマトリックス) では、FedRAMP の中程度のベースラインで必要なすべてのセキュリティ コントロールの一覧を示しています。 どちらのドキュメントにも、各コントロールの実装がマイクロソフト、お客様、または両者間での共有の責任のどれに該当するかが、詳細に示されています。

[Azure Security and Compliance Blueprint - FedRAMP High Control Implementation Matrix](https://aka.ms/blueprintdwcimhigh) (Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP の高度なコントロールでの実装に関するマトリックス) および [Azure Security and Compliance Blueprint - FedRAMP Moderate Control Implementation Matrix](https://aka.ms/blueprintdwcimmod) (Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP の中程度のコントロールでの実装に関するマトリックス) では、対象となる各コントロールの要件に実装がどの程度見合っているかの詳細な説明を含め、各 FedRAMP ベースラインのデータ ウェアハウス アーキテクチャの対象となるコントロールに関する情報を提供しています。

## <a name="disclaimer"></a>免責事項

 - このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。
 - このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。
 - お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。
 - このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。
 - このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
 - このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
