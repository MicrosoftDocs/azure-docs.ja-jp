---
title: Azure のセキュリティとコンプライアンスのブループリント - PCI DSS のためのデータ ウェアハウス
description: Azure のセキュリティとコンプライアンスのブループリント - PCI DSS のためのデータ ウェアハウス
services: security
author: meladie
ms.assetid: 6d29e5bf-6cd0-4cda-bbd8-59f283b0c86c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 7704d9124846c0c6c82728031327990f8f9b1cf6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60608243"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-pci-dss"></a>Azure のセキュリティとコンプライアンスのブループリント:PCI DSS のためのデータ ウェアハウス

## <a name="overview"></a>概要

この Azure のセキュリティとコンプライアンスのブループリントは、Payment Card Industry Data Security Standards (PCI DSS 3.2) の要件を支援するデータ ウェアハウス アーキテクチャを Azure にデプロイするためのガイダンスを提供します。 これは一般的な参照アーキテクチャを紹介し、安全な複数の階層からなる準拠環境でのクレジット カード データの適切な処理 (カード番号、有効期限、検証データを含む) を示します。 このブループリントは、特定のセキュリティとコンプライアンスの要件を満たすことができる方法を例示し、Azure で独自のデータ ウェアハウス ソリューションをビルドして構成するための基礎として機能します。

この参照アーキテクチャ、実装ガイド、脅威モデルは、お客様が PCI DSS 3.2 要件に準拠するための基礎を提供します。 このソリューションは、お客様が PCI DSS 3.2 に準拠した方法でワークロードを Azure にデプロイするためのベースラインを提供します。ただし、追加の構成が必要なため、このソリューションをそのまま運用環境で使用しないでください。

PCI DSS への準拠を達成するには、お客様の実稼働ソリューションが認定セキュリティ評価機関から認証を受ける必要があります。 お客様は、このアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスの評価を実施する責任を負います。要件は、お客様の実装によって変化する可能性があるからです。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント

このソリューションは、高パフォーマンスのセキュリティで保護されたクラウド データ ウェアハウスを実装する参照アーキテクチャを提供します。 このアーキテクチャには、2 つの分離されたデータ層があります。片方は、クラスター化された SQL 環境の中でデータのインポート、格納、ステージングが行われる層であり、他方は、抽出、変換、読み込みツール (例: [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) の T-SQL クエリ) を使用して処理するためにデータが読み込まれる Azure SQL Data Warehouse 用の層です。 データが Azure SQL Data Warehouse に格納された後、大規模な分析を実行できます。

Azure では、お客様向けのさまざまなレポートと分析サービスを提供しています。 このソリューションには、Azure SQL Data Warehouse からレポートをすばやく作成するための SQL Server Reporting Services (SSRS) が含まれています。 すべての SQL トラフィックは、自己署名証明書を含めることによって、SSL で暗号化されます。 ベスト プラクティスとして、Azure では、セキュリティ強化のために信頼された証明書機関を利用することをお勧めします。

Azure SQL Data Warehouse 内のデータは、単票形式ストレージを持つリレーショナル テーブルに格納されます。この形式は、クエリのパフォーマンスを向上させると同時に、データ ストレージ コストを大幅に削減します。 用途の要件に応じて、コンピューティング リソースを必要とするアクティブなプロセスが存在しない場合は、Azure SQL Data Warehouse のコンピューティング リソースのスケールアップ、スケールダウン、または完全なシャット ダウンを実行できます。

SQL Load Balancer は、SQL トラフィックを管理することで高パフォーマンスを保証します。 この参照アーキテクチャ内のすべての仮想マシンは、高可用性とディザスター リカバリー機能のために Always On 可用性グループ内に構成された SQL Server インスタンスを使用して可用性セット内にデプロイされます。

このデータ ウェアハウスの参照アーキテクチャには、アーキテクチャ内のリソースを管理するための Active Directory 層も含まれています。 Active Directory サブネットによって、大規模な Active Directory フォレスト構造への容易な導入が可能になり、大規模なフォレストへのアクセスが利用できない場合でも、環境を継続的に運用できます。 すべての仮想マシンは、Active Directory 層に対してドメイン参加済みであり、Active Directory グループ ポリシーを使用して、オペレーティング システム レベルでセキュリティとコンプライアンスの構成が適用されます。

このソリューションでは、Azure Storage アカウントを使用します。お客様は、Storage Service Encryption を使用して保存データの機密性を保持するように、Azure Storage アカウントを構成できます。 Azure は、回復性のために、お客様が選択したデータセンター内にデータの 3 つのコピーを保存します。 geo 冗長ストレージにより、データは数百マイル離れたセカンダリ データセンターにレプリケートされ、そのデータセンター内に 3 つのコピーとして再度保存されます。これにより、プライマリ データ センターでの有害事象によってデータが失われるのを防ぐことができます。

セキュリティ強化のために、このソリューションのすべてのリソースは、Azure Resource Manager を通じてリソース グループとして管理されます。 デプロイされたリソースへのアクセス (Azure Key Vault 内のキーへのアクセスを含む) を制御するために、Azure Active Directory のロールベースのアクセス制御が使用されます。 システムの正常性は、Azure Security Center と Azure Monitor によって監視されます。 お客様が両方の監視サービスを構成してログをキャプチャし、簡単にナビゲートできる単一のダッシュボードにシステムの正常性を表示できます。

仮想マシンは管理踏み台ホストとして機能し、デプロイされたリソースにアクセスするためのセキュリティで保護された接続を管理者に提供します。 データは、この管理踏み台ホストを通してステージング領域に読み込まれます。 **Microsoft では参照アーキテクチャのサブネットに対する管理とデータ インポートのために、VPN または Azure ExpressRoute 接続を構成することを推奨しています。**

![PCI DSS のためのデータ ウェアハウスの参照アーキテクチャ ダイアグラム](images/pcidss-dw-architecture.png "PCI DSS のためのデータ ウェアハウスの参照アーキテクチャ ダイアグラム")

このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャの詳細については、「[デプロイ アーキテクチャ](#deployment-architecture)」セクションを参照してください。

- 可用性セット
    - Active Directory ドメイン コントローラー
    - SQL クラスター ノードと監視
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Virtual Machines
    - (1) 踏み台ホスト
    - (2) Active Directory ドメイン コントローラー
    - (2) SQL Server クラスター ノード
    - (1) SQL Server 監視
- Azure Virtual Network
    - (1) /16 ネットワーク
    - (4) /24 ネットワーク
    - (4) ネットワーク セキュリティ グループ
- Recovery Services コンテナー
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ

次のセクションで、デプロイと実装の要素について詳しく説明します。

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) は、超並列処理 (MPP) を利用して、ペタバイト単位のデータ間で複雑なクエリを短時間で実行するエンタープライズ データ ウェアハウス (EDW) であり、ユーザーはこのウェアハウスによって、財務データを効率的に識別できます。 ユーザーは、簡単な PolyBase T-SQL クエリを使用して、SQL Data Warehouse にビッグ データをインポートし、MPP の機能を利用して高パフォーマンスの分析を実行できます。

**SQL Server Reporting Services (SSRS)** : [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) では、Azure SQL Data Warehouse のテーブル、グラフ、マップ、ゲージ、マトリックスなどを含むレポートをすばやく作成できます。

**Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) は、データを管理しているユーザーが、データ ソースを容易に検出し、把握できるようにします。 一般的なデータ ソースの登録、タグ付け、財務データの検索を実行できます。 データは元の場所に残りますが、メタデータのコピーと、データ ソースの場所に対する参照が Data Catalog に追加されます。 このメタデータのインデックスも作成されるので、検索で簡単に各データ ソースを見つけられるようになり、データ ソースを検出するユーザーが理解しやすくなります。

**踏み台ホスト**:踏み台ホストは、この環境にデプロイされたリソースへのユーザーのアクセスを許可する単一エントリ ポイントです。 要塞ホストは、セーフ リスト上のパブリック IP アドレスからのリモート トラフィックのみを許可することで、デプロイ済みのリソースへのセキュリティで保護された接続を提供します。 リモート デスクトップ (RDP) トラフィックを許可するには、トラフィックのソースがネットワーク セキュリティ グループに定義されている必要があります。

このソリューションでは、次の構成を持つドメイン参加済み踏み台ホストとして仮想マシンを作成します。
-   [マルウェア対策拡張機能](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Diagnostics 拡張機能](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault を使用した [Azure ディスクの暗号化](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   未使用時の仮想マシン リソースの消費を抑えるための[自動シャットダウン ポリシー](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) の有効化。実行中のオペレーティング システムから分離されている保護された環境で、資格情報とその他のシークレットが実行されるようにします。

### <a name="virtual-network"></a>仮想ネットワーク

この参照アーキテクチャは、10.0.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

**ネットワーク セキュリティ グループ**:[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)には、仮想ネットワーク内のトラフィックを許可または拒否するアクセス制御リストが含まれています。 ネットワーク セキュリティ グループを使用して、サブネットまたは個々の仮想マシン レベルでトラフィックを保護できます。 次のネットワーク セキュリティ グループが存在します。

  - データ層 (SQL Server クラスター、SQL Server 監視、SQL Load Balancer) 用のネットワーク セキュリティ グループ
  - 管理踏み台ホスト用のネットワーク セキュリティ グループ
  - Active Directory のネットワーク セキュリティ グループ
  - SQL Server Reporting Services 用のネットワーク セキュリティ グループ

各ネットワーク セキュリティ グループでは、ソリューションが安全かつ適切に機能できるように、固有のポートとプロトコルが開かれます。 さらに、各ネットワーク セキュリティ グループで次の構成を使用できます。

- [診断ログとイベント](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)が有効化され、ストレージ アカウントに格納される
- Azure Monitor ログが[ネットワーク セキュリティ グループの診断ログ](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)に接続される

**サブネット**:各サブネットは、対応するネットワーク セキュリティ グループに関連付けられています。

### <a name="data-at-rest"></a>保存データ

このアーキテクチャでは、暗号化やデータベース監査などの複数の手段によって保存データを保護します。

**Azure Storage**:暗号化された保存データの要件を満たすために、すべての [Azure Storage](https://azure.microsoft.com/services/storage/) で [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption) が使用されます。 これは、PCI DSS 3.2 によって定義されている組織のセキュリティ コミットメントとコンプライアンス要件のサポートにおいてカード所有者のデータを保護するために役立ちます。

**Azure Disk Encryption**:[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、Windows の BitLocker 機能を活用して、OS とデータ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Azure Key Vault と統合されています。

**Azure SQL Database**:Azure SQL データベース インスタンスは、次のデータベース セキュリティ対策を使用します。

- [Active Directory 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)を使用して、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
- [SQL Database の監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
- Azure SQL Database は、[Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用するように構成されます。Transparent Data Encryption は、データベース、関連付けられたバックアップ、トランザクション ログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 Transparent Data Encryption は、保存されているデータが未承認のアクセスの対象になっていないことを保証します。
- [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
- [SQL の脅威検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)は、不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンに対するセキュリティの警告を提供することで、潜在的な脅威の検出とそれらの脅威への対応を可能にします。
- [暗号化された列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)により、データベース システム内で機微なデータがプレーンテキストとして表示されないことが確保されます。 データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
- [拡張プロパティ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql)を使用して、データ サブジェクトの処理を中止できます。カスタム プロパティをデータベース オブジェクトに追加し、データを "中止" とタグ付けして、関連付けられた財務データの処理を防ぐアプリケーション ロジックをサポートできます。
- [行レベルのセキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)によって、データへのアクセスを制限するポリシーを定義して、処理を中止できます。
- [SQL Database 動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)は、特権のないユーザーまたはアプリケーションに対してデータをマスクすることで、機微なデータの露出を制限します。 動的データ マスクは、潜在的な機微なデータを自動的に検出し、適用する適切なマスクを提案できます。 これは、不正アクセスによるデータベースの終了が発生しないように、データへのアクセスを識別して削減するのに役立ちます。 お客様は、お使いのデータベース スキーマに準拠するように動的データ マスクの設定を調整する必要があります。

### <a name="identity-management"></a>ID 管理

次のテクノロジによって、Azure 環境でのデータへのアクセスを管理する機能が提供されます。

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) は、Microsoft が提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 このソリューションのすべてのユーザー (Azure SQL Database にアクセスするユーザーを含む) は、Azure Active Directory で作成されています。
- アプリケーションに対する認証は Azure Active Directory を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。 さらに、データベース列の暗号化では、Azure SQL Database に対してアプリケーションを認証するために Azure Active Directory が使用されます。 詳細については、[SQL Database で機微なデータを保護する](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)方法に関するページを参照してください。
- [Azure ロールベースのアクセス制御](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)によって、管理者は、業務の遂行に必要なアクセスのみをユーザーに付与するきめの細かいアクセス許可を定義できます。 すべてのユーザーに Azure リソースへの無制限のアクセス許可を付与する代わりに、管理者は、データにアクセスするための特定のアクションのみを許可できます。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) により、特定の情報にアクセスできるユーザーの数を最小限に抑えることができます。 管理者は、Azure Active Directory Privileged Identity Management を使用して、特権 ID と特権 ID によるリソースへのアクセスを検出、制限、監視できます。 この機能を使用して、必要に応じて、オンデマンドの Just-In-Time 管理アクセスを適用することもできます。
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) は、組織の ID に影響を及ぼす可能性のある脆弱性を検出し、組織の ID に関連する検出された不審なアクションに対する自動応答を構成します。さらに、不審なインシデントを調査し、適切なアクションを実行してそれらを解決します。

### <a name="security"></a>セキュリティ

**シークレットの管理**:ソリューションでは、キーとシークレットの管理に [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) が使用されます。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 次の Azure Key Vault 機能は、そのようなデータの保護とアクセスに役立ちます。

- 必要に応じて、高度なアクセス ポリシーが構成されます。
- Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されます。
- Key Vault のすべてのキーとシークレットに有効期限があります。
- Key Vault 内のすべてのキーは、特殊なハードウェア セキュリティ モジュールによって保護されます。 キーの種類は、HSM 保護された 2048 ビット RSA キーです。
- ロールベースのアクセス制御を使用して、すべてのユーザーと ID に最低限必要なアクセス許可が付与されます。
- Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
- キーに対して許可される暗号化操作は必要なものに制限されます。

**更新プログラムの管理**: この参照アーキテクチャの一部としてデプロイされる Windows 仮想マシンは、既定で Windows Update サービスから自動的に更新プログラムを受け取るように構成されます。 また、このソリューションには、[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) サービスが含まれます。このサービスによって、必要に応じて仮想マシンにパッチを適用するために、更新されたデプロイが作成される場合があります。

**マルウェア対策**: 仮想マシン向けの [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) は、ウイルスやスパイウェアなどの悪意のあるソフトウェアを識別して削除するリアルタイム保護機能を提供し、既知のマルウェアや不要なソフトウェアが保護されている仮想マシンへのインストールまたは実行を試みた場合に警告する構成可能なアラートを備えています。

**Azure Security Center**:[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) を使用すると、お客様はワークロード全体へのセキュリティ ポリシーの一元的な適用と管理、脅威にさらされる状態の軽減、攻撃の検出とその対応を行うことができます。 さらに、Azure Security Center は、Azure サービスの既存の構成にアクセスして、セキュリティ状況の改善とデータの保護に役立つ、構成とサービスに関する推奨事項を提供します。

Azure Security Center では、さまざまな検出機能を使用して、お客様の環境を対象とする攻撃の可能性を通知します。 これらのアラートには、アラートをトリガーした要因、対象となったリソース、攻撃元に関する重要な情報が含まれています。 Azure Security Center には、事前定義された一連の[セキュリティ アラート](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)が用意されています。これらは、脅威または疑わしいアクティビティが発生した際にトリガーされます。 Azure Security Center の[カスタム アラート ルール](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)を使用すると、お客様が環境から既に収集されているデータに基づいて、新しいセキュリティ アラートを定義できます。

Azure Security Center では、お客様の潜在的なセキュリティの問題の検出と対処が簡単になるように、優先度付けしたセキュリティの警告とインシデントを提供しています。 インシデント対応チームによる脅威の調査と修復を支援するために、検出された脅威ごとに[脅威インテリジェンス レポート](https://docs.microsoft.com/azure/security-center/security-center-threat-report)が生成されます。

### <a name="business-continuity"></a>ビジネス継続性

**高可用性**: サーバー ワークロードは、Azure の仮想マシンの高可用性を確保するために[可用性セット](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にグループ化されます。 Azure SLA を 99.95% 満たすように、計画または計画外メンテナンス イベントの間に、少なくとも 1 つの仮想マシンが利用可能です。

**Recovery Services コンテナー**: [Recovery Services コンテナー](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)にはバックアップ データが保管され、このアーキテクチャ内の Azure 仮想マシンのすべての構成が保護されます。 Recovery Services コンテナーを使用すると、仮想マシン全体を復元せずに IaaS 仮想マシンからファイルとフォルダーを復元できるため、復元時間を短縮できます。

### <a name="logging-and-auditing"></a>ログ記録と監査

Azure サービスは、システムの正常性だけではなく、システムとユーザーのアクティビティも詳細に記録します。
- **アクティビティ ログ**:[アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、状態の判断に役立ちます。
- **診断ログ**:[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログ、Azure Storage ログ、Key Vault 監査ログ、および Application Gateway のアクセス ログとファイアウォール ログが含まれます。 すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 リテンション期間には、組織固有の保有要件を満たすために最長 730 日までの日数をユーザーが設定できます。

**Azure Monitor ログ**: これらのログは、処理、格納、およびダッシュボードでのレポート表示を行うために、[Azure Monitor ログ](https://azure.microsoft.com/services/log-analytics/)に統合されます。 収集されたデータは、その型ごとに Log Analytics ワークスペース内の別個のテーブルにまとめられ、すべてのデータがその収集元にかかわらず一斉に分析できる状態になります。 さらに、Azure Security Center を Azure Monitor ログと統合することで、お客様は Kusto クエリを使用してセキュリティ イベント データにアクセスし、それを他のサービスからのデータと組み合わせることができます。

このアーキテクチャの一部として、次の Azure [監視ソリューション](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)が含まれます。
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment):Active Directory 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment):SQL 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧をお客様に提供します。
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth):Agent Health ソリューションは、デプロイされたエージェント数とその地理的配置に加え、応答しないエージェント数やオペレーショナル データを送信しているエージェント数を報告します。
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity):Activity Log Analytics ソリューションは、顧客向けのすべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。

**Azure Automation**:[Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) では、Runbook を格納、実行、および管理します。 このソリューションでは、Runbook を使用して、Azure SQL Database からログを収集できます。 Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) ソリューションは、お客様が環境の変更を簡単に識別できるようにします。

**Azure Monitor**:[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) は、組織で監査、アラートの作成、データのアーカイブ、Azure リソース内の API 呼び出しの追跡を実行できるようにすることで、ユーザーがパフォーマンスの追跡、セキュリティの維持、傾向の識別を実行できるようにします。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラムを[ダウンロード](https://aka.ms/pcidss-dw-tm)するか、下記を参照してください。 このモデルは、修正を行う際に、お客様がシステム インフラストラクチャの潜在的なリスクを理解するために役立ちます。

![PCI DSS のためのデータ ウェアハウスの参照アーキテクチャ ダイアグラム](images/pcidss-dw-threat-model.png "PCI DSS のための PaaS Web アプリケーションの脅威モデル")

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント

[Azure のセキュリティとコンプライアンスのブループリント - PCI DSS の顧客責任マトリックス](https://aka.ms/pcidss-crm)には、PCI DSS 3.2 のすべての要件に対する責任の一覧が示されています。

[Azure のセキュリティとコンプライアンスのブループリント - PCI DSS データ ウェアハウス実装マトリックス](https://aka.ms/pcidss-dw-cim)では、データ ウェアハウス アーキテクチャによって対応される PCI DSS 3.2 の要件に関する情報が示されています。カバーされている各制御の要件に適合する実装の詳細な説明も含まれています。

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項

### <a name="vpn-and-expressroute"></a>VPN と ExpressRoute

セキュリティ保護された VPN トンネルまたは [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) は、デプロイされるリソースへの接続を安全に確立するために、このデータ ウェアハウス参照アーキテクチャの一部として構成される必要があります。 ExpressRoute または VPN を適切に設定することで、転送中のデータに対して保護のレイヤーを追加できます。

Azure を使用してセキュリティ保護された VPN トンネルを実装することにより、オンプレミス ネットワークと Azure 仮想ネットワークの間で仮想プライベート接続を作成できます。 この接続はインターネットをまたいでいるため、お客様は自社ネットワークと Azure の間の暗号化されたリンクの "トンネル" を通して情報を送信できます。 サイト間 VPN は、数十年にわたってあらゆる規模の企業に導入されている安全で成熟したテクノロジです。 このオプションでは、暗号化メカニズムとして [IPsec トンネル モード](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))が使用されています。

VPN トンネル内のトラフィックはサイト間 VPN を使用してインターネット上を転送されるため、Microsoft は、さらに高いセキュリティで保護されたもう 1 つの接続オプションを提供します。 Azure ExpressRoute は、オンプレミスの場所または Exchange ホスティング プロバイダーと Azure の間に確立される専用 WAN リンクです。 ExpressRoute 接続はインターネットを経由しないため、これらの接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 さらに、これはお客様の通信プロバイダーの直接接続であり、データがインターネット上を移動しないため、インターネットに公開されることはありません。

オンプレミス ネットワークを Azure に拡張するセキュアなハイブリッド ネットワークを実装するためのベスト プラクティスが[提供されています](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

### <a name="extract-transform-load-process"></a>抽出、変換、読み込みプロセス

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) は、別の抽出、変換、読み込み、インポート ツールなしで Azure SQL Data Warehouse にデータを読み込むことができます。 PolyBase では、T-SQL クエリを使用してデータにアクセスできます。 Microsoft のビジネス インテリジェンスと分析スタックに加え、SQL Server 対応のサードパーティ ツールを PolyBase で使用できます。

### <a name="azure-active-directory-setup"></a>Azure Active Directory の設定

[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) は、デプロイの管理と環境を操作する担当者へのアクセスのプロビジョニングを行うために不可欠です。 既存の Windows Server Active Directory は、[4 回のクリック](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)で Azure Active Directory と統合できます。 また、デプロイされた Active Directory インフラストラクチャを Azure Active Directory フォレストのサブドメインにすることで、デプロイされた Active Directory インフラストラクチャ (ドメイン コントローラー) を既存の Azure Active Directory に連結できます。

### <a name="optional-services"></a>省略可能なサービス

Azure では、フォーマットされたデータとフォーマットされていないデータの格納とステージングを支援する多様なサービスを提供しています。 お客様の要件に応じて、次のサービスをこの参照アーキテクチャに追加できます。
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) は、ETL (抽出、変換、読み込み) とデータ統合という複雑なハイブリッド プロジェクト用に構築された、マネージド クラウド サービスです。 Azure Data Factory には、カードホルダー データのトレースと検索に役立つ機能があり、監視データの到着と送信元を識別する視覚化と監視ツールが含まれています。 Azure Data Factory を使用して、異なるデータ ストアからデータを取り込む、パイプラインと呼ばれるデータ駆動型ワークフローを作成してスケジュールを設定できます。 これらのパイプラインを使用して、内部ソースと外部ソースの両方からデータを取り込むことができます。 その後、データの処理と変換を行って、Azure SQL Data Warehouse などのデータ ストアに出力できます。
- 非構造化データを [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) にステージングできます。これにより、運用上の予備的な分析用の単一の場所で、サイズ、種類、および取り込み速度に関係なくデータをキャプチャできます。  Azure Data Lake には、データの抽出と変換を可能にする機能があります。 Azure Data Lake Store は Hadoop エコシステムのほとんどのオープン ソース コンポーネントと互換性があり、Azure SQL Data Warehouse などの他の Azure サービスと緊密に統合しています。

## <a name="disclaimer"></a>免責事項

 - このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。
 - このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。
 - お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。
 - このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。
 - このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
 - このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
