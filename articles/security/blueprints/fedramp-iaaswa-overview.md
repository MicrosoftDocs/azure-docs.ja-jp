---
title: Azure のセキュリティとコンプライアンスのブループリント - FedRAMP のための IaaS Web アプリケーション
description: Azure のセキュリティとコンプライアンスのブループリント - FedRAMP のための IaaS Web アプリケーション
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: 1ba5b813843ce2f5d31f337ab4d3d94e521b0e0c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864475"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Azure のセキュリティとコンプライアンスのブループリント:FedRAMP のための IaaS Web アプリケーション

## <a name="overview"></a>概要

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) は、米国政府全体で実行されるプログラムであり、クラウド製品とサービスに対するセキュリティの評価、承認、および継続的な監視に対する標準化された手法を提供します。 この Azure のセキュリティとコンプライアンスのブループリント自動化では、インターネットに接続する単純な Web アプリケーションに適している FedRAMP 準拠 IaaS (サービスとしてのインフラストラクチャ) 環境をデプロイするためのガイダンスを提供します。 このソリューションは、一般的なリファレンス アーキテクチャでの Azure リソースのデプロイと構成を自動化して、お客様が特定のセキュリティ要件と準拠要件をどのように満たすことができるかを示し、お客様が Azure 上に独自のソリューションをビルドして構成するための基礎として機能します。 このソリューションは、NIST SP 800-53 に基づく FedRAMP High ベースラインのコントロールのサブセットを実装します。 FedRAMP 要件とこのソリューションの詳細については、[コンプライアンス ドキュメント](#compliance-documentation)を参照してください。
> [!NOTE]
> このソリューションは Azure Government にデプロイされます。

この Azure のセキュリティとコンプライアンスのブループリント自動化は、IaaS Web アプリケーション リファレンス アーキテクチャと事前構成済みのセキュリティ コントロールを自動的にデプロイして、お客様が FedRAMP 要件への準拠を実現することを支援します。 このソリューションは、リソースのデプロイと構成をガイドする Azure Resource Manager テンプレートと PowerShell スクリプトで構成されています。

このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。 変更せずにこの環境にアプリケーションをデプロイすることは、FedRAMP High ベースラインの要件を完全に満たすためには不十分です。 以下の点に注意してください。
- このアーキテクチャは、お客様が FedRAMP に準拠した方法で Azure を使用するためのベースラインを提供します。
- お客様は、このアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスの評価を実施する責任を負います。要件は、お客様の実装によって変化する可能性があるからです。

このソリューションの機能の概要を簡単に把握するために、ソリューションのデプロイの説明とデモンストレーションを行っている[このビデオ](https://aka.ms/fedrampblueprintvideo)をご覧ください。

デプロイ手順については、[ここ](https://aka.ms/fedrampblueprintrepo)をクリックしてください。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント
このソリューションは、バックエンドに SQL Server を使用する IaaS Web アプリケーション向けのリファレンス アーキテクチャをデプロイします。 アーキテクチャには、Web 層、データ層、Active Directory インフラストラクチャ、Application Gateway、および Azure Load Balancer が含まれています。 Web 層とデータ層にデプロイされる仮想マシンは可用性セット内に構成され、SQL Server インスタンスは高可用性のために AlwaysOn 可用性グループ内に構成されます。 仮想マシンはドメインに参加し、Active Directory グループ ポリシーを使用して、オペレーティング システム レベルでセキュリティとコンプライアンスの構成が適用されます。 要塞ホストは、デプロイされたリソースにアクセスするためのセキュリティで保護された接続を管理者に提供します。 **Azure では参照アーキテクチャのサブネットに対する管理とデータ インポートのために、VPN または Azure ExpressRoute 接続を構成することを推奨しています。**

![FedRAMP のための IaaS Web アプリケーションの参照アーキテクチャ図](images/fedramp-iaaswa-architecture.png?raw=true "FedRAMP のための IaaS Web アプリケーションの参照アーキテクチャ図")

このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャについて詳しくは、「[デプロイ アーキテクチャ](#deployment-architecture)」セクションをご覧ください。

- Azure Virtual Machines
    - (1) 要塞ホスト (Microsoft Windows Server 2016 Datacenter)
    - (2) Active Directory ドメイン コントローラー (Windows Server 2016 Datacenter)
    - (2) SQL Server クラスター ノード (Windows Server 2016 上の SQL Server 2017)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- 可用性セット
    - (1) Active Directory ドメイン コントローラー
    - (1) SQL クラスター ノード
    - (1) Web/IIS
- Azure Virtual Network
    - (1) /16 仮想ネットワーク
    - (5) /24 サブネット
    - 両方のドメイン コントローラーに DNS 設定が設定されます
- Azure Load Balancer
- Azure Application Gateway
    - (1) WAF Application Gateway が有効
        - ファイアウォール モード: 防止
        - ルール セット:OWASP 3.0
        - リスナー: ポート 443
- Azure Storage
    - (7) Geo 冗長ストレージ アカウント
- Azure クラウド監視
- Recovery Services コンテナー
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Azure Monitor (ログ)

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ

以下のセクションでは、開発と実装の要素について詳しく説明します。

**要塞ホスト**:要塞ホストは、デプロイされたリソースにアクセスするためのセキュリティで保護された接続を管理者に提供する、単一のエントリ ポイントです。 要塞ホストの NSG は、TCP ポート 3389 のみで RDP 接続を許可します。 お客様は、組織のシステム セキュリティ強化要件を満たすために要塞ホストをさらに詳細に構成できます。

### <a name="virtual-network"></a>仮想ネットワーク
このアーキテクチャは、10.200.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

**ネットワーク セキュリティ グループ**:このソリューションは、分離された Web サブネット、データベース サブネット、Active Directory サブネット、管理サブネットを持つアーキテクチャの仮想ネットワーク内にリソースをデプロイします。 サブネット間のトラフィックをシステムと管理機能に必要なものだけに制限するために、個々のサブネットに適用されるネットワーク セキュリティ グループの規則に従って、サブネットが論理的に分離されます。

このソリューションにデプロイされている[ネットワーク セキュリティ グループ](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json)の構成を参照してください。 組織は、[こちらのドキュメント](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)をガイドとして使用して上記のファイルを編集することで、ネットワーク セキュリティ グループを構成できます。

各サブネットには、専用のネットワーク セキュリティ グループ (NSG) があります。
- Application Gateway 用の 1 つの NSG (LBNSG)
- 要塞ホスト (MGTNSG) 用の 1 つの NSG
- プライマリおよびバックアップ ドメイン コントローラー用の 1 つの NSG (ADNSG)
- SQL Server (SQLNSG) 用の 1 つの NSG
- Web 層用の 1 つの NSG (WEBNSG)

**サブネット**:各サブネットは、対応する NSG に関連付けられています。

### <a name="data-at-rest"></a>保存データ

このアーキテクチャは、さまざまな暗号化手段を使用して、保存データを保護します。

**Azure Storage**:保存データの暗号化要件を満たすために、すべてのストレージ アカウントは、[ストレージ サービスの暗号化](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)を使用します。

**SQL Server**:SQL Server は、[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) を使用するように構成されます。これは、データとログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 TDE は、保存されているデータが未承認のアクセスの対象になっていないことを保証します。

お客様は、次の SQL Server セキュリティ対策を構成することもできます。
-   [AD 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)を使用すると、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
-   [SQL Database の監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
-   [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
-   [SQL の脅威検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)は、不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンに対するセキュリティの警告を提供することで、潜在的な脅威の検出とそれらの脅威への対応を可能にします。
-   [Always Encrypted の列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)により、データベース システム内で機密データがプレーンテキストとして表示されることはありません。 データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
-   [SQL Database 動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)は、参照アーキテクチャがデプロイされた後に実行できます。 お客様は、お使いのデータベース スキーマに準拠するように動的データ マスクの設定を調整する必要があります。

**Azure Disk Encryption**:暗号化された Windows IaaS 仮想マシン ディスクに対して Azure Disk Encryption が使用されます。 [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、Windows の BitLocker 機能を活用して、OS とデータ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Azure Key Vault と統合されています。

### <a name="identity-management"></a>ID 管理

次のテクノロジによって、Azure 環境に ID 管理機能が提供されます。
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID の管理サービスです。
- Azure AD を使用して、お客様がデプロイした Web アプリケーションを認証できます。 詳細については、「[Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。  
- [Azure のロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) は、Azure の厳密に対象を絞ったアクセス管理を実現します。 サブスクリプションのアクセスはサブスクリプション管理者に制限され、リソースへのアクセスはユーザー ロールに基づいて制限できます。
- デプロイされた IaaS Active Directory インスタンスは、デプロイされた IaaS 仮想マシンの ID を OS レベルで管理します。

### <a name="security"></a>セキュリティ
**シークレットの管理**:ソリューションでは、キーとシークレットの管理に [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) が使用されます。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 Azure Key Vault は、この参照アーキテクチャの IaaS 仮想マシンのディスク暗号化キーとシークレットの管理に役立ちます。

**更新プログラムの管理**:この Azure のセキュリティとコンプライアンスのブループリント自動化によってデプロイされる Windows 仮想マシンは、Windows Update サービスから自動的に更新プログラムを受け取るように、既定で構成されます。 また、このソリューションによってデプロイされる Azure Automation ソリューションにより、更新プログラムのデプロイを作成し、必要に応じて Windows サーバーに修正プログラムをデプロイできます。

**マルウェア対策**:仮想マシン向けの [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) は、ウイルスやスパイウェアなどの悪意のあるソフトウェアを識別して削除するリアルタイム保護機能を提供し、既知のマルウェアや不要なソフトウェアが保護されている仮想マシンへのインストールまたは実行を試みた場合に警告する構成可能なアラートを備えています。

**Application Gateway**:このアーキテクチャでは、OWASP ルールセットを有効にした、Web アプリケーション ファイアウォール (WAF) がある Application Gateway を使用してセキュリティの脆弱性のリスクを軽減します。 その他の機能には次のものがあります。

- [エンド ツー エンド SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL オフロード](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)の有効化
- [TLS v1.0 および v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) の無効化
- [Web アプリケーション ファイアウォール](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF モード)
- OWASP 3.0 ルールセットを使用した[防止モード](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)

### <a name="business-continuity"></a>ビジネス継続性

**高可用性**:Azure SLA を 99.95% 満たすように、計画または計画外メンテナンス イベントの間に、少なくとも 1 つの仮想マシンが利用可能です。 このソリューションは、[可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)に配置されているすべての Web 層およびデータ層の仮想マシンを展開します。 可用性セットは、可用性を向上させるために仮想マシンが複数の分離されたハードウェア クラスターに分散されることを保証します。 また、このソリューションは、SQL Server の仮想マシンを、[AlwaysOn 可用性グループ](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)として可用性セットに展開します。 Always On 可用性グループ機能は、高可用性とディザスター リカバリ用の機能を提供します。

**Recovery Services コンテナー**:[Recovery Services コンテナー](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)にはバックアップ データが保管され、このアーキテクチャ内の Azure 仮想マシンのすべての構成が保護されます。 Recovery Services コンテナーを使用すると、VM 全体を復元せずに IaaS VM からファイルとフォルダーを復元できるため、復元時間を短縮できます。

**クラウド監視**: [クラウド監視](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness)は、Windows Server 2016 内のフェールオーバー クラスター クォーラム監視の一種であり、Azure をアービトレーション ポイントとして活用します。 他のクォーラム監視と同様、クラウド監視は、投票権を取得し、クォーラムの計算に参加することができますが、パブリックで使用可能な標準の Azure Blob Storage を使用します。 これにより、パブリック クラウドでホストされている VM のメンテナンスの余分なオーバーヘッドを排除できます。

### <a name="logging-and-auditing"></a>ログ記録と監査

Azure Monitor ログは、システムの正常性だけではなく、システムとユーザーのアクティビティを詳細に記録します。 [Azure Monitor ログ](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) ソリューションは、Azure やオンプレミス環境のリソースによって生成されるデータを収集して分析します。

- **アクティビティ ログ:**[アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、状態の判断に役立ちます。
- **診断ログ:**[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)は、リソースによって出力されるすべてのログです。 これらのログには、Windows イベント システム ログ、Azure ストレージ ログ、Key Vault 監査ログ、および Application Gateway のアクセス ログとファイアウォール ログが含まれます。
- **ログのアーカイブ**:すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 リテンション期間には、組織固有の保有要件を満たすために最長 730 日までの日数をユーザーが設定できます。 これらのログは、処理、格納、およびダッシュボードでのレポート表示を行うために、Azure Monitor ログに接続されます。

さらに、このアーキテクチャの一部として、次の監視ソリューションがインストールされます。 これらのソリューションは、FedRAMP セキュリティ統制に適合するよう、お客様の責任で設定してください。
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment):この Active Directory 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware):このマルウェア対策ソリューションでは、マルウェア、脅威、および保護の状態に関する報告を行います。
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker):Azure Automation ソリューションは、Runbook の格納、実行、および管理を行います。
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started):Security and Audit ダッシュボードでは、セキュリティ ドメイン、注意が必要な問題、検出、脅威に関する知識、および一般的なセキュリティの照会に関するメトリックを提供することで、リソースのセキュリティ ステータスに対する高度な分析情報を提供します。
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment):この SQL 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧をお客様に提供します。
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management):Update Management ソリューションでは、利用可能な更新プログラムのステータスや必要な更新プログラムのインストール プロセスなど、オペレーティング システムのセキュリティ更新プログラムをお客様が管理できるようにします。
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth):Agent Health ソリューションは、デプロイされたエージェント数とその地理的配置に加え、応答しないエージェント数やオペレーショナル データを送信しているエージェント数を報告します。
-   [Azure Activity Logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity):Activity Log Analytics ソリューションは、顧客向けのすべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。
-   [Change Tracking](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity):Change Tracking ソリューションでは、お客様が環境の変更を簡単に識別できるようにします。

**Azure Monitor**
: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) は、監査、アラートの作成、データのアーカイブ、および Azure リソース内の API 呼び出しの追跡を実行できるようにすることで、パフォーマンスの追跡、セキュリティの維持、および傾向の識別を実行できるようにします。

## <a name="threat-model"></a>脅威モデル
この参照アーキテクチャのデータ フロー ダイアグラムを[ダウンロード](https://aka.ms/fedrampWAdfd)するか、下記を参照してください。 このモデルは、修正を行う際に、お客様がシステム インフラストラクチャの潜在的なリスクを理解するために役立ちます。

![FedRAMP 脅威モデルのための IaaS Web アプリケーション](images/fedramp-iaaswa-threat-model.png?raw=true "FedRAMP 脅威モデルのための IaaS Web アプリケーション")

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント

[Azure Security and Compliance Blueprint – FedRAMP High Customer Responsibility Matrix](https://aka.ms/blueprinthighcrm) (Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP の高度なベースラインでのお客様の責任に関するマトリックス) では、FedRAMP の高度なベースラインで必要とされるすべてのセキュリティ統制の一覧を示しています。 マトリックスには、各統制の実装が、マイクロソフト、お客様、または両者間での共有の責任のどれに該当するかが、詳細に示されています。

[Azure のセキュリティとコンプライアンスのブルー プリントの FedRAMP IaaS Web アプリケーション マトリックス](https://aka.ms/blueprintwacim)は、FedRAMP の高度なベースラインで必要とされるすべてのセキュリティ統制の一覧を示しています。 マトリックスでは、実装がどのように管理対象の各統制の要件を満たすかについての詳細な説明を含め、IaaS Web アプリケーションのアーキテクチャで対応されている統制に関する情報を提供します。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

この Azure のセキュリティとコンプライアンスのブループリント自動化は、Azure Resource Manager の API サービスが Azure 内にリソースをデプロイするために処理する JSON 構成ファイルと PowerShell スクリプトで構成されています。 詳しいデプロイ手順については、[こちら](https://aka.ms/fedrampblueprintrepo)をご覧ください。
> [!NOTE]
> このソリューションは Azure Government にデプロイされます。

#### <a name="quickstart"></a>クイック スタート
1. [この](https://aka.ms/fedrampblueprintrepo) GitHub リポジトリをローカル ワークステーションに複製するかダウンロードします。

2. 配置前 PowerShell スクリプト (azure-blueprint/predeploy/Orchestration_InitialSetup.ps1) を実行します。

3. 下のボタンをクリックし、Azure ポータルにサインインします。必要な ARM テンプレート パラメーターを入力し、**[購入]**  をクリックします。

    [![Azure へのデプロイ](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項
### <a name="vpn-and-expressroute"></a>VPN と ExpressRoute
この IaaS Web アプリケーションの参照アーキテクチャの一部としてデプロイされるリソースへの接続を安全に確立するために、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) またはセキュリティ保護された VPN トンネルを構成する必要があります。 ExpressRoute または VPN を適切に設定することで、転送中のデータに対して保護のレイヤーを追加できます。

Azure を使用してセキュリティ保護された VPN トンネルを実装することにより、オンプレミス ネットワークと Azure 仮想ネットワークの間で仮想プライベート接続を作成できます。 この接続はインターネットをまたいでいるため、お客様は自社ネットワークと Azure の間の暗号化されたリンクの "トンネル" を通して情報を送信できます。 サイト間 VPN は、数十年にわたってあらゆる規模の企業に導入されている安全で成熟したテクノロジです。 このオプションでは、暗号化メカニズムとして [IPsec トンネル モード](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))が使用されています。

VPN トンネル内のトラフィックはサイト間 VPN を使用してインターネット上を転送されるため、Microsoft は、さらに高いセキュリティで保護されたもう 1 つの接続オプションを提供します。 Azure ExpressRoute は、オンプレミスの場所または Exchange ホスティング プロバイダーと Azure の間に確立される専用 WAN リンクです。 ExpressRoute 接続はインターネットを経由しないため、これらの接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 さらに、これはお客様の通信プロバイダーの直接接続であり、データがインターネット上を移動しないため、インターネットに公開されることはありません。

オンプレミス ネットワークを Azure に拡張するセキュアなハイブリッド ネットワークを実装するためのベスト プラクティスが[提供されています](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="disclaimer"></a>免責事項

- このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。  
- このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。  
- お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。  
- このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。  
- このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
- このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
