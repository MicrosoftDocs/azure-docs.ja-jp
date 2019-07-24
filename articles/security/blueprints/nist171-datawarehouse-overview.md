---
title: Azure のセキュリティとコンプライアンスのブループリント - NIST SP 800-171 のためのデータ ウェアハウス
description: Azure のセキュリティとコンプライアンスのブループリント - NIST SP 800-171 のためのデータ ウェアハウス
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: a1850ecfbb21eb9495bb0e6de362dc8dee3026a2
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242362"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Azure のセキュリティとコンプライアンスのブループリント - NIST SP 800-171 のためのデータ ウェアハウス

## <a name="overview"></a>概要
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) では、非連邦情報システムおよび組織に存在する管理対象の非機密情報 (CUI) を保護するためのガイドラインが提供されます。 NIST SP 800-171 では、CUI の機密性を保護するための 14 個のセキュリティ要件ファミリが確立されています。

Azure のセキュリティとコンプライアンスのブルー プリントでは、NIST SP 800-171 制御のサブセットを実装する、Azure のデータ ウェアハウス アーキテクチャをお客様がデプロイするのに役立つガイダンスが提供されます。 このソリューションでは、お客様が特定のセキュリティおよびコンプライアンス要件を紹介しています｡ これはまた､お客様が Azure で独自のデータ ウェアハウス ソリューションを構築､構成する土台にもなります｡

この参照アーキテクチャ、関連する実装ガイド、および脅威モデルは、お客様がその固有の要件に適応するための土台として利用されることを目的としており､ 運用環境でそのまま使用することはできません。 このアーキテクチャを使用して構築されたソリューションのセキュリティとコンプライアンスの適切なアセスメントは、お客様が実施する必要があります。 要件は、お客様の実装の細部によって異なる場合があります。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント
このソリューションは、高パフォーマンスのセキュリティで保護されたクラウド データ ウェアハウスを実装する参照アーキテクチャを提供します。 このアーキテクチャには、2 つの独立したデータ層があります。 1 つの層は､クラスター化された SQL 環境内にあってデータがインポート､格納､ステージングされる場所です｡ もう 1 つの層は、SQL データ ウェアハウス用の場所です。 この層のデータは､処理に抽出-変換-読み込み (ETL) ツール (たとえば [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) のT-SQL クエリ) を使用することで読み込まれます｡ データが SQL Data Warehouse に格納されると､分析を大規模に実行することが可能になります｡

Azure では、お客様向けのさまざまなレポートと分析サービスを提供しています。 このソリューションには、SQL Data Warehouse からレポートをすばやく作成するための SQL Server Reporting Services が含まれています。 すべての SQL トラフィックは、自己署名証明書を含めることによって、SSL で暗号化されます。 ベスト プラクティスとして、セキュリティ強化のために信頼された証明書機関を利用することをお勧めします。

SQL Data Warehouse は列指向のストレージを使用してリレーショナル テーブルにデータを格納します。 この形式にすると、データのストレージ コストが大幅に削減され、クエリのパフォーマンスが向上します｡ SQL Data Warehouse の計算リソースは､用途の要件に応じてスケールアップしたり､スケールダウンしたり､また計算リソースを必要とするアクティブなプロセスが存在しない場合､完全に停止したりできます｡

SQL Server のロード バランサーは、高いパフォーマンスを確保できるよう SQL トラフィックを管理します。 この参照アーキテクチャ内のすべての仮想マシンは、Always On 可用性グループ内に構成された SQL Server インスタンスを使用して可用性セット内にデプロイされます。 この構成では、高可用性とディザスター リカバリー機能が提供されます。

このデータ ウェアハウスの参照アーキテクチャには、アーキテクチャ内のリソースを管理するための Active Directory 層も含まれています。 Active Directory サブネットにより、大きな Active Directory フォレスト構造の下で適応が容易に行えるようになります｡ こうして、大きなフォレストへのアクセス不可の場合でも､環境の継続運用が可能になります。 各 VM は Active Directory 階層にドメイン参加されます｡ それら VM は Active Directory グループ ポリシーを使用して、オペレーティング システム レベルでセキュリティとコンプライアンスの構成設定を適用します。

このソリューションでは、Azure Storage アカウントを使用します。お客様は、Storage Service Encryption を使用して保存データの機密性を保持するように、Azure Storage アカウントを構成できます。 Azure は、回復性のために、お客様が選択したデータ センター内にデータの 3 つのコピーを保存します。 Geo 冗長ストレージによって、数百マイル離れたセカンダリ データ センターにデータのレプリカが作成され､ここでもまた､データ センター内にコピー 3 部が格納されます｡ この配置により、お客様のプライマリ データ センターでトラブルが発生してもデータが失われることを避けることができます。

セキュリティ強化のために、このソリューションのすべてのリソースは、Azure Resource Manager を通じてリソース グループとして管理されます。 Azure Active Directory (Azure AD) のロールベース アクセス制御 (RBAC) は、デプロイされたリソースへのアクセスの制御に使用されます。 Azure Key Vault では､これらのリソースに顧客キーが含まれます。 システムの正常性は、Azure Security Center と Azure Monitor によって監視されます。 どちらの監視サービスについても､お客様はログをキャプチャするよう構成できます。 システム正常性は、使いやすい 1 つのダッシュ ボードに表示されます。

VM の 1 つは管理要塞ホストとして機能します。 要塞ホストは、管理者がデプロイされているリソースにアクセスするためのセキュリティで保護された接続を提供します｡ データは、この管理要塞ホストを通してステージング領域に読み込まれます。 *Azure では､参照アーキテクチャのサブネットに対する管理とデータ インポート用に VPN または Azure ExpressRoute 接続を構成することを推奨しています。*

![NIST SP 800-171 のためのデータ ウェアハウスの参照アーキテクチャ ダイアグラム](images/nist171-dw-architecture.png "NIST SP 800-171 のためのデータ ウェアハウスの参照アーキテクチャ ダイアグラム")

このソリューションでは、次の Azure サービスを使用します。 詳しくは、[deployment architecture](#deployment-architecture)セクションをご覧ください。

- 可用性セット
    - Active Directory ドメイン コントローラー
    - SQL Server クラスター ノードと監視
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor (ログ)
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Virtual Machines
    - (1) 要塞ホスト
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

**Azure SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) は、超並列処理を利用して、ペタバイト単位のデータに対して複雑なクエリを短時間で実行するエンタープライズ データ ウェアハウスです。 ユーザーは、簡単な PolyBase T-SQL クエリを使用して、SQL Data Warehouse にビッグ データをインポートしたり、超並列処理の機能を利用して高パフォーマンスの分析を実行したりできます。

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) では、SQL Data Warehouse のテーブル、グラフ、マップ、ゲージ、マトリックスなどを含むレポートをすばやく作成できます。

**Azure Data Catalog**:[Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) は、データを管理しているユーザーが、データ ソースを容易に検出し、理解できるようにします。 一般的なデータ ソースの登録、タグ付け、およびデータの検索を実行できます。 データは元の場所に残りますが、メタデータのコピーは Data Catalog に追加されます。 データ ソースへの参照も含まれます。 このメタデータのインデックスが作成されるので、検索で簡単に各データ ソースを見つけられます。 また、インデックス作成により、データ ソースを検出するユーザーが理解しやすくなります。

**要塞ホスト**:要塞ホストは、この環境にデプロイされているリソースへのアクセスに利用できる単一エントリ ポイントです。 要塞ホストは、セーフ リスト上のパブリック IP アドレスからのリモート トラフィックのみを許可することで、デプロイ済みのリソースへのセキュリティで保護された接続を提供します。 リモート デスクトップ トラフィックを許可するには、トラフィックのソースがネットワーク セキュリティ グループに定義されている必要があります。

このソリューションでは、次の構成を使用して､1 つの仮想マシンがドメイン参加済み要塞ホストとして作成されます。
-   [マルウェア対策拡張機能](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure 診断拡張機能](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault を使用した [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   未使用時の仮想マシン リソースの消費を抑えるための[自動シャットダウン ポリシー](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)。
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) の有効化。実行中のオペレーティング システムから分離されている保護された環境で、資格情報とその他のシークレットが実行されるようにします。

### <a name="virtual-network"></a>仮想ネットワーク
この参照アーキテクチャは、10.0.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

**ネットワーク セキュリティ グループ**:[ネットワーク セキュリティ グループ (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) には、仮想ネットワーク内のトラフィックを許可または拒否するアクセス制御リストが含まれています。 NSG を使用して、サブネットまたは個々の VM レベルでトラフィックを保護できます。 次の NSG が存在します。
  - データ層 (SQL Server クラスター、SQL Server 監視、および SQL Load Balancer) 用の NSG
  - 管理要塞ホスト用の NSG
  - Active Directory 用の NSG
  - SQL Server Reporting Services 用の NSG

各 NSG では、ソリューションが安全かつ適切に機能できるように、固有のポートとプロトコルが開かれます。 さらに、各 NSG で次の構成が有効になります。
  - [診断ログとイベント](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)が有効化され、ストレージ アカウントに格納される。
  - Azure Monitor ログが [NSG の診断](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)に接続される。

**サブネット**:各サブネットは、対応する NSG に関連付けられています。

### <a name="data-at-rest"></a>保存データ
このアーキテクチャでは、複数の方法で保存データが保護されます。 それらの方法としては、暗号化やデータベース監査があります｡

**Azure Storage**:暗号化された保存データの要件を満たすために、すべての [Storage](https://azure.microsoft.com/services/storage/) で [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption) が使用されます。 これは、組織のセキュリティ コミットメントとコンプライアンス要件のサポートにおいてデータを保護するのに役立ちます。

**Azure Disk Encryption**:[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、Windows の BitLocker 機能を活用して、オペレーティング システムとデータ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Key Vault と統合されています。

**Azure SQL Database**:SQL データベース インスタンスは、次のデータベース セキュリティ対策を使用します。
-   [Active Directory 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)を使用して、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
-   [SQL Database の監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
-   SQL Database は、[Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用するように構成されます。 これは、データベース、関連付けられたバックアップ、トランザクション ログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 透過的なデータ暗号化により、保存されているデータが未承認のアクセスに晒されないようになります｡
-   [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
-   [SQL 脅威の検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)により、発生した潜在的な脅威を検出し、対応することができます。 不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンが見つかった場合に、セキュリティ アラートが提供されます。
-   [暗号化された列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)により、データベース システムにおいて機密データがプレーンテキストとして表示されないようにしています｡ データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
- [拡張プロパティ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql)を利用して､データ主体の処理を中止することができます｡ ユーザーは、データベース オブジェクトにカスタム プロパティを追加することができます。 また､データに "Discontinued" のタグを付けることで､関係する金融データの処理が行われないようにするアプリケーション ロジックをサポートすることもできます｡
- [行レベルのセキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)によって、データへのアクセスを制限するポリシーを定義して、処理を中止できます。
- [SQL Database 動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)では、特権のないユーザーやアプリケーションに対してデータをマスクすることで､機密データの露出を制限されます。 DDM はまた潜在的に機密のデータを自動的に検出し、適用する適切なマスクを提案することもできます。 動的データ マスクは、機密データが未承認のアクセスによってデータベースが終了されるようなアクセスを制限するのに役立ちます。 *お客様は、お使いのデータベース スキーマに準拠するように設定を調整する必要があります。*

### <a name="identity-management"></a>ID 管理
次のテクノロジによって、Azure 環境でのデータへのアクセスを管理する機能が提供されます。
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 このソリューションのすべてのユーザーは Azure AD で作成し､それらユーザーには SQL database にアクセスするユーザーが含まれます。
-   アプリケーションに対する認証は Azure AD を使用して行われます。 詳細は、「[Azure AD とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。 Azure AD は､データベース列の暗号化で SQL Database に対する認証にも使用されます。 詳細については、[SQL Database で機密データを保護する](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)方法に関するページを参照してください。
-   管理者は､[Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) を使用してきめ細かなアクセス許可を定義することができます｡ RBAC を使用すると、職務に必要な量のアクセス権だけをユーザーに付与することができます。 すべてのユーザーに Azure リソースへの無制限アクセスを許可する代わりに、管理者は、リソースやデータにアクセスするための特定のアクションのみを許可できます。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。
- お客様は､[Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) を利用して、データなどの特定の情報にアクセスできるユーザーの数を最小限に抑えることができます。 管理者は、Azure AD Privileged Identity Management を使用して、特権 ID と特権 ID によるリソースへのアクセスを検出、制限、および監視できます。 この機能を使用して、必要に応じて、オンデマンドの Just-In-Time 管理アクセスを適用することもできます。
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) は組織の ID に影響する潜在的な脆弱性を検出します。 組織の ID に関連する検出された不審なアクションに対する自動応答を構成することができます。 また､不審なインシデントを調査し、適切なアクションを実行して解決することもできます。

### <a name="security"></a>セキュリティ
**シークレットの管理**:ソリューションでは、キーとシークレットの管理に [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を使用します。 Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティ保護するのに役立ちます。 次の Key Vault 機能は、お客様がデータを保護するのに役立ちます。
- 必要に応じて、高度なアクセス ポリシーが構成されます。
- Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されます。
- Key Vault のすべてのキーとシークレットに有効期限があります。
- Key Vault 内のすべてのキーは、特殊なハードウェア セキュリティ モジュールによって保護されます。 キーの種類は、ハードウェア セキュリティ モジュールによって保護された 2048 ビット RSA キーです。
- RBAC を使用することで、すべてのユーザーと ID に最低限必要なアクセス許可が付与されます。
- Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
- キーに対して許可される暗号化操作は必要なものに制限されます。

**更新プログラムの管理**: この参照アーキテクチャの一部としてデプロイされる Windows VM は既定で、Windows Update サービスから自動的に更新プログラムを受信するように構成されています。 このソリューションには、[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)サービスも含まれます。このサービスによって、更新されたデプロイを作成し､必要に応じて仮想マシンにパッチを適用することができます｡

**マルウェア対策**: VM 用の [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) は、ウイルスやスパイウェアなどの悪意のあるソフトウェアの特定や削除に役立つリアルタイムの保護機能を提供します。 お客様は、既知の悪意のあるまたは望ましくないソフトウェアによって仮想マシンにインストールや実行が試みられたときに生成するアラートを構成できます。

**Azure Security Center**:[Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) を使用すると、お客様はワークロード全体へのセキュリティ ポリシーの一元的な適用と管理、脅威にさらされる状態の制限、攻撃の検出とその対応を行うことができます。 Security Center はまた、Azure サービスの既存の構成にアクセスして、セキュリティ状況の改善とデータの保護に役立つ、構成とサービスに関する推奨事項を提供します。

Security Center では、さまざまな検出機能を使用して、お客様の環境が標的の可能性がある攻撃を通知します。 これらのアラートには、アラートをトリガーした要因、対象となったリソース、攻撃元に関する重要な情報が含まれています。 Security Center には、一連の[セキュリティ アラート](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)が事前に定義されています。これらは、脅威または不審なアクティビティが発生した際にトリガーされます。 お客様は[カスタム アラート ルール](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)を使用して、お客様の環境から既に収集されているデータに基づく新しいセキュリティ アラートを定義することができます。

Security Center では、優先順位の付いたセキュリティ アラートとインシデントを提供します。 Security Center では、お客様が潜在的なセキュリティ問題を簡単に検出､対処できるようにします。 検出された脅威ごとに、[脅威インテリジェンス レポート](https://docs.microsoft.com/azure/security-center/security-center-threat-report)が生成されます。 インシデント対応チームは、脅威を調査し､修復するときさい､このレポートを利用します｡

この参照アーキテクチャではまた、Security Center の [脆弱性評価](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)を利用します｡ 構成後、パートナー エージェント (Qualys など) はパートナーの管理プラットフォームに脆弱性データを報告します。 次に、パートナーの管理プラットフォームは、脆弱性と正常性の監視データを Security Center に送り返します。 お客様はこの情報を利用して、脆弱な VM をすぐに特定することができます｡

### <a name="business-continuity"></a>ビジネス継続性
**高可用性**: サーバー ワークロードは、Azure での VM の高可用性の確保に役立つように [可用性セット](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にグループ化されます。 Azure SLA を 99.95% 満たすように、計画または計画外メンテナンス イベントの間、少なくとも 1 つの仮想マシンを利用できます｡

**Recovery Services コンテナー**: [Recovery Services コンテナー](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)はバックアップ データを格納して、このアーキテクチャ内のすべての VM の構成を保護します。 Recovery Services コンテナーを使用すると、VM 全体を復元せずに IaaS VM からファイルとフォルダーを復元できます｡ このプロセスによって、復元時間は短縮されます｡

### <a name="logging-and-auditing"></a>ログ記録と監査

Azure サービスは、システムの正常性だけではなく、システムとユーザーのアクティビティも詳細に記録します。
- **アクティビティ ログ**:[アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、状態の判断に役立ちます。
- **診断ログ**:[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログや Storage ログ、Key Vault 監査ログ、および Azure Application Gateway のアクセス ログとファイアウォール ログが含まれます。 すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 ユーザーは個々の要件に応じて最大 730 日間の保有期間を設定できます。

**Azure Monitor ログ**: これらのログは、処理、格納、およびダッシュボードでのレポート表示を行うために、[Azure Monitor ログ](https://azure.microsoft.com/services/log-analytics/)に統合されます。 データの収集後は、Log Analytics ワークスペース内にデータ型ごとに別にテーブルに編成されます｡ これにより、元のソースに関係なくにすべてのデータをまとめて分析できます。 Security Center は、Azure Monitor ログと統合されます。 お客様は Kusto クエリを使用してセキュリティ イベント データにアクセスし、それを他のサービスからのデータと組み合わせることができます。

このアーキテクチャの一部として、次の Azure [監視ソリューション](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)が含まれます。
-   [Active Directory 評価](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment):Active Directory 正常性チェック ソリューションはサーバー環境のリスクと正常性を定期的に評価します。 このソリューションは、デプロイされているサーバー インフラストラクチャに固有の推奨事項を優先順位付きの一覧で提供します。
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment):SQL 正常性チェック ソリューションはサーバー環境のリスクと正常性を定期的に評価します。 このソリューションは、デプロイされているサーバー インフラストラクチャに固有の推奨事項を優先順位付きの一覧で提供します。
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth):Agent Health ソリューションは、デプロイされているエージェント数とその地理的分散を報告します。 また、応答のないエージェント数と運用データを送信するエージェント数の報告もします。
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity):Activity Log Analytics ソリューションは、顧客向けのすべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。

**Azure Automation**:[Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) は、Runbook の格納、実行、管理を行います。 このソリューションでは、Runbook は SQL Database からログを収集できます。 お客様は､Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) ソリューションを利用して､環境の変更を簡単に把握できます。

**Azure Monitor**:[Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ではパフォーマンスを追跡し、セキュリティ維持し、傾向を把握することができます。 データの監査やアラートの作成、データのアーカイブを行うことができます。 Azure リソースにおける API 呼び出し追跡することもできます。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラムは[ダウンロード](https://aka.ms/nist171-dw-tm)することができます｡また､こちらにも提供されます。 このモデルは、お客様が修正を加える際にシステム インフラストラクチャの潜在的なリスクを理解するのに役立ちます。

![NIST SP 800-171 用データ ウェアハウス脅威モデル](images/nist171-dw-threat-model.png "NIST SP 800-171 用データ ウェアハウス脅威モデル")

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント
[Azure のセキュリティとコンプライアンスのブループリント - NIST SP 800-171 顧客責任マトリックス](https://aka.ms/nist171-crm)には、NIST SP 800-171 で必要とされるすべてのセキュリティ制御が一覧表示されています。 このマトリックスには、各制御の実装が、Microsoft、お客様、または両者間での共有の責任のどれに該当するかが、詳細に示されています。

[Azure のセキュリティとコンプライアンスのブループリント -　NIST SP 800-171 Data Warehouse Control Implementation Matrix](https://aka.ms/nist171-dw-cim)は､データ ウェアハウス アーキテクチャで取り上げられている NIST SP 800-171 コントロールに関する情報を提供します。 これには、取り上げられている各コントロール要件に対する実装の準拠状況に関する詳細な説明が含まれています。

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項

### <a name="vpn-and-expressroute"></a>VPN と ExpressRoute
セキュリティ保護された VPN トンネルまたは [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) は、このデータ ウェアハウス参照アーキテクチャの一部としてデプロイされているすべてのリソースへの接続を安全に確立されるように構成される必要があります。 ExpressRoute または VPN を適切に設定することで、お客様は転送中のデータに対して保護のレイヤーを追加できます。

Azure を使用してセキュリティ保護された VPN トンネルを実装することにより、オンプレミス ネットワークと Azure 仮想ネットワークの間で仮想プライベート接続を作成できます。 この接続は、インターネット経由で行われます。 お客様は、この接続を利用して､そのネットワークと Azure 間の暗号化されたリンク内で情報を安全に「トンネリング」できます｡ サイト間 VPN は、数十年にわたってあらゆる規模の企業に導入されている安全で成熟したテクノロジです。 このオプションでは、暗号化メカニズムとして [IPsec トンネル モード](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))が使用されています。

VPN トンネル内のトラフィックはサイト間 VPN を使用してインターネット上を転送されるため、Microsoft は、もう 1 つの､さらに高いセキュリティで保護された接続オプションを提供しています。 ExpressRoute は、オンプレミスの場所または Exchange ホスティング プロバイダーと Azure との間の専用の WAN リンクです。 ExpressRoute 接続は、お客様の通信プロバイダーと直接接続します。 その結果、データはインターネットを経由することはなく､インターネットに公開されることもありません。 ExpressRoute 接続は一般的な接続よりも信頼性が高く、高速で、待ち時間が短く、セキュリティの高い接続を提供します。

オンプレミス ネットワークを Azure に拡張するセキュアなハイブリッド ネットワークを実装するためのベスト プラクティスが[提供されています](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

### <a name="extract-transform-load-process"></a>抽出、変換、読み込みプロセス
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) は、別途､ETL やインポート ツールを用意しなくても SQL Data Warehouse にデータを読み込むことができます。 PolyBase では、T-SQL クエリを使用してデータにアクセスできます。 PolyBase は､Microsoft のビジネス インテリジェンスと分析スタックに加え、SQL Server 対応のサードパーティ ツールと組み合わせて利用することができます。

### <a name="azure-ad-setup"></a>Azure AD のセットアップ
[Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) は、デプロイの管理と､環境に関与する担当者へのアクセスのプロビジョニングに不可欠です。 オンプレミスの Active Directory は､[4 回のクリック](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)でAzure AD と連携させることができます｡ デプロイされた Active Directory インフラストラクチャ (ドメイン コント ローラー) を Azure AD に結びつけることもできます。 これを行うには、デプロイされた Active Directory インフラストラクチャを Azure AD フォレストのサブドメインにします。

### <a name="optional-services"></a>省略可能なサービス
Azure では、フォーマットされたデータとフォーマットされていないデータの格納とステージングを支援する多様なサービスを提供しています。 この参照アーキテクチャには､お客様の要件に応じて次のサービスを追加できます。
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) は、複雑な ハイブリット ETL (抽出-変換-読み込み) とデータ統合プロジェクト用に構築されたマネージド クラウド サービスです。 Data Factory には､データを追跡して､その場所を特定できる機能があります。 視覚化ツールと監視ツールは、データの到着日時と送信元を特定します。 お客様は各種データ ストアからデータを取り込むデータ主導型のワークフロー (パイプライン) を作成し、スケジュールを設定できます。 パイプラインを使用して､内部および外部ソースからデータを取り込むことができます｡ その後、データの処理と変換を行って、SQL Data Warehouse などのデータ ストアに出力できます。
- [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) では､データをステージングすることで､あらゆるサイズ、種類、および取り込み速度のデータをキャプチャし､運用および予備的分析を行うことができます。 Azure Data Lake には、データの抽出と変換を可能にする機能があります。 Data Lake Store は、Hadoop のエコシステムを構成するほとんどのオープン ソース コンポーネントと共存することができます。 また SQL Data Warehouse などの他の Azure サービスと優れた連携をすることができます｡

## <a name="disclaimer"></a>免責事項

 - このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。
 - このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。
 - お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。
 - このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。
 - このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
 - このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
