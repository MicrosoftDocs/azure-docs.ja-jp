---
title: 'Azure のセキュリティとコンプライアンスのブループリント: NIST SP 800-171 のための IaaS Web アプリケーション'
description: 'Azure のセキュリティとコンプライアンスのブループリント: NIST SP 800-171 のための IaaS Web アプリケーション'
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: b30094e264086f018acbf84144300df46c60ac4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610271"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Azure のセキュリティとコンプライアンスのブループリント: NIST SP 800-171 のための IaaS Web アプリケーション

## <a name="overview"></a>概要
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) では、非連邦情報システムおよび組織に存在する管理対象の非機密情報 (CUI) を保護するためのガイドラインが提供されます。 NIST SP 800-171 では、CUI の機密性を保護するための 14 個のセキュリティ要件ファミリが確立されています。

Azure のセキュリティとコンプライアンスのブルー プリントでは、NIST SP 800-171 制御のサブセットを実装する、Azure の Web アプリケーション アーキテクチャをお客様がデプロイするのに役立つガイダンスが提供されます。 このソリューションでは、お客様が特定のセキュリティおよびコンプライアンス要件を紹介しています｡ これはまた､お客様が Azure で独自のデータ ウェアハウス ソリューションを構築､構成する土台にもなります｡

この参照アーキテクチャ、関連する実装ガイド、および脅威モデルは、お客様固有の要件に適合させるための土台として使用されることを目的としており、 運用環境でそのまま使用することはできません。 変更を加えずにこのアーキテクチャをデプロイすることは、NIST SP 800-171 の要件を完全に満たすには不十分です。 このアーキテクチャを使用して構築されたソリューションのセキュリティとコンプライアンスの適切なアセスメントは、お客様が実施する必要があります。 要件は、お客様の実装の細部によって異なる場合があります。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント
この Azure のセキュリティとコンプライアンスのブループリントは、バックエンドに SQL Server を使用する IaaS Web アプリケーション向けのリファレンス アーキテクチャをデプロイします。 アーキテクチャには、Web 層、データ層、Active Directory インフラストラクチャ、Azure Application Gateway、および Azure Load Balancer で構成されています｡ Web およびデータ層にデプロイされた仮想マシン (VM) は、可用性セットで構成されます。 SQL Server インスタンスは、高可用性の Always On 可用性グループで構成されます。 VMはドメイン参加済みです。 Active Directory グループ ポリシーでは、オペレーティング システム レベルのセキュリティとコンプライアンスの構成が適用されます。

Azure Portal からお客様が構成する Azure Storage 上にソリューション全体がビルドされます。 Storage は Storage Service Encryption を使用してすべてのデータを暗号化し、保存データの機密性を維持します。 Geo 冗長ストレージは、、お客様のプライマリ データ ロケーションで問題が発生してもデータが失われないようにします｡ 2 つ目のコピーは、数百マイル離れた別の場所に格納されます。

セキュリティ強化のために、このソリューションのすべてのリソースは、Azure Resource Manager を通じてリソース グループとして管理されます。 デプロイされたリソースと Azure Key Vault のキーへのアクセスは､Azure の Active Directory (Azure AD) ロールベースのアクセス制御 (RBAC) を使って制御されます｡ システムの正常性は、Azure Monitor によって監視されます。 どちらの監視サービスについても､お客様はログをキャプチャするよう構成できます。 システム正常性は、使いやすい 1 つのダッシュ ボードに表示されます。

管理踏み台ホストは、デプロイされたリソースにアクセスするためのセキュリティで保護された接続を管理者に提供します。 *Microsoft では､参照アーキテクチャのサブネットに対する管理とデータ インポート用に VPN または Azure ExpressRoute 接続を構成することを推奨しています。*


![NIST SP 800-171 のための IaaS Web アプリケーションの参照アーキテクチャ ダイアグラム](images/nist171-iaaswa-architecture.png "NIST SP 800-171 のための IaaS Web アプリケーションの参照アーキテクチャ ダイアグラム")

このソリューションでは、次の Azure サービスを使用します。 詳しくは、[deployment architecture](#deployment-architecture)セクションをご覧ください。

- Azure Virtual Machines
    - (1) 管理/踏み台 (Windows Server 2016 Datacenter)
    - (2) Active Directory ドメイン コントローラー (Windows Server 2016 Datacenter)
    - (2) SQL Server クラスター ノード (Windows Server 2016 上の SQL Server 2017)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1) /16 ネットワーク
    - (5) /24 ネットワーク
    - (5) ネットワーク セキュリティ グループ
- 可用性セット
    - (1) Active Directory ドメイン コントローラー
    - (1) SQL クラスター ノード
    - (1) Web/IIS
- Azure Application Gateway
    - (1) Web アプリケーション ファイアウォール
        - ファイアウォール モード: 防止
        - ルール セット:OWASP 3.0
        - リスナー ポート:443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (ログ)
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure Automation
- クラウド監視
- Recovery Services コンテナー

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ
次のセクションで、デプロイと実装の要素について詳しく説明します。

**踏み台ホスト**:要塞ホストは、この環境にデプロイされているリソースへのアクセスに利用できる単一エントリ ポイントです。 踏み台ホストは、セーフ リスト上のパブリック IP アドレスからのリモート トラフィックのみを許可することで、デプロイ済みのリソースへのセキュリティで保護された接続を提供します。 リモート デスクトップ トラフィックを許可するには、トラフィックのソースがネットワーク セキュリティ グループに定義されている必要があります。

このソリューションでは、次の構成を持つドメイン参加済み踏み台ホストとして仮想マシンを作成します。
-   [マルウェア対策拡張機能](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Diagnostics 拡張機能](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault を使用した [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   未使用時の仮想マシン リソースの消費を抑えるための[自動シャットダウン ポリシー](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)。
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) の有効化。実行中のオペレーティング システムから分離されている保護された環境で、資格情報とその他のシークレットが実行されるようにします。

### <a name="virtual-network"></a>仮想ネットワーク
このアーキテクチャは、10.200.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

**ネットワーク セキュリティ グループ**:このソリューションは、仮想ネットワーク内で web やデータベース、Active Directory､管理それぞれのサブネットからなるアーキテクチャにリソースをデプロイします。 サブネットは、個々 のサブネットに適用される NSG ルールによって論理的に分離されています。 ルールは、サブネット間のトラフィックをシステムと管理機能に必要なもののみに制限します。

このソリューションを使ってデプロイされた [NSG](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) ついては､構成を参照してください｡ 組織は、[このドキュメント](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)を目安として､以前のファイルを編集することで NSG を構成することができます｡

あらゆるサブネットのそれぞれに専用の NSG が 1 つあります。
- Application Gateway 用 NSG (LBNSG)
- 踏み台ホスト (MGTNSG) 用 NSG (MGTNSG)
- プライマリおよびバックアップ ドメイン コントローラー用 NSG (ADNSG)
- SQL Server とクラウド監視用 NSG (SQLNSG)
- Web 層用 NSG (WEBNSG)

### <a name="data-in-transit"></a>転送中のデータ
Azure の既定では､Azure データセンターとの間のあらゆる通信は暗号化されます。 また、Azure Portal を介した Storage へのあらゆるトランザクションは HTTPS を経由します｡

### <a name="data-at-rest"></a>保存データ
このアーキテクチャでは、複数の方法で保存データが保護されます。 それらの方法としては、暗号化やデータベース監査があります｡

**Azure Storage**:暗号化された保存データの要件を満たすために、すべての [Storage](https://azure.microsoft.com/services/storage/) で [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption) が使用されます。 これは、組織のセキュリティ コミットメントと NIST SP 800-171 によって定義されているコンプライアンス要件のサポートにおいてデータを保護するのに役立ちます。

**Azure Disk Encryption**:Disk Encryption は、Windows IaaS VM ディスクの暗号化に使用されます。 [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、Windows の BitLocker 機能を活用して、オペレーティング システムとデータ ディスクのボリュームを暗号化します。 このソリューションは Key Vault と連携することで、ディスクの暗号化キーの制御と管理を実現しています｡

**SQL Server**:SQL Server インスタンスは、次のデータベース セキュリティ対策を使用します。
-   [SQL Server の監査](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017)では、データベース イベントを追跡し、監査ログにイベントを書き込みます。
-   [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) (TDE) は、データベース、関連付けられたバックアップ、トランザクション ログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 透過的なデータ暗号化により、保存されているデータが未承認のアクセスに晒されないようになります｡
-   [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
-   [暗号化された列](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017)により、データベース システムにおいて機密データがプレーンテキストとして表示されないようにしています｡ データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
- [動的データ マスク](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017)では、特権のないユーザーやアプリケーションに対してデータをマスクすることで､機密データの露出を制限されます。 DDM はまた潜在的に機密のデータを自動的に検出し、適用する適切なマスクを提案することもできます。 動的データ マスクは、機密データが未承認のアクセスによってデータベースが終了されるようなアクセスを制限するのに役立ちます。 *お客様は、お使いのデータベース スキーマに準拠するように設定を調整する必要があります。*

### <a name="identity-management"></a>ID 管理
次のテクノロジによって、Azure 環境でのデータへのアクセスを管理する機能が提供されます。
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 このソリューションのすべてのユーザーは Azure AD で作成し､それらユーザーには SQL Server にアクセスするユーザーが含まれます。
-   アプリケーションに対する認証は Azure AD を使用して行われます。 詳細は、「[Azure AD とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。
-   管理者は､[Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)を使用してきめ細かなアクセス許可を定義することができます｡ すべてのユーザーに Azure リソースへの無制限のアクセス許可を付与する代わりに、管理者は、データにアクセスするための特定のアクションのみを許可できます。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) を利用して、特定の情報にアクセスできるユーザーの数を最小限に抑えることができます。 管理者は、Azure AD Privileged Identity Management を使用して、特権 ID と特権 ID によるリソースへのアクセスを検出、制限、および監視できます。 この機能を使用して、必要に応じて、オンデマンドの Just-In-Time 管理アクセスを適用することもできます。
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) は組織の ID に影響する潜在的な脆弱性を検出します。 組織の ID に関連する検出された不審なアクションに対する自動応答を構成することができます。 また､不審なインシデントを調査し、適切なアクションを実行して解決することもできます。

### <a name="security"></a>セキュリティ
**シークレットの管理**:ソリューションでは、キーとシークレットの管理に [Key Vault](https://azure.microsoft.com/services/key-vault/) が使用されます。 Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティ保護するのに役立ちます。 次の Key Vault 機能は、お客様がデータを保護するのに役立ちます。
- 必要に応じて、高度なアクセス ポリシーが構成されます。
- Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されます。
- Key Vault のすべてのキーとシークレットに有効期限があります。
- Key Vault 内のすべてのキーは、特殊なハードウェア セキュリティ モジュールによって保護されます。 キーの種類は、ハードウェア セキュリティ モジュールによって保護された 2048 ビット RSA キーです。
- RBAC を使用することで、すべてのユーザーと ID に最低限必要なアクセス許可が付与されます。
- Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
- キーに対して許可される暗号化操作は必要なものに制限されます。
- このソリューションは Key Vault と連携して､Iaas VM ディスク暗号化キーとシークレットを管理します｡

**更新プログラムの管理**: この参照アーキテクチャの一部としてデプロイされる Windows VM は既定で、Windows Update サービスから自動的に更新プログラムを受信するように構成されています。 このソリューションには、[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)サービスも含まれます。このサービスによって、更新されたデプロイを作成し､必要に応じて仮想マシンにパッチを適用することができます｡

**マルウェア対策**: VM 用の [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) は、ウイルスやスパイウェアなどの悪意のあるソフトウェアの特定や削除に役立つリアルタイムの保護機能を提供します。 お客様は、既知の悪意のあるまたは望ましくないソフトウェアによって仮想マシンにインストールや実行が試みられたときに生成するアラートを構成できます。

**Azure Security Center**:[Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) を使用すると、お客様はワークロード全体へのセキュリティ ポリシーの一元的な適用と管理、脅威にさらされる状態の制限、攻撃の検出とその対応を行うことができます。 Security Center はまた、Azure サービスの既存の構成にアクセスして、セキュリティ状況の改善とデータの保護に役立つ、構成とサービスに関する推奨事項を提供します。

Security Center では、さまざまな検出機能を使用して、お客様の環境が標的の可能性がある攻撃を通知します。 これらのアラートには、アラートをトリガーした要因、対象となったリソース、攻撃元に関する重要な情報が含まれています。 Security Center には、一連の[セキュリティ アラート](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)が事前に定義されています。これらは、脅威または不審なアクティビティが発生した際にトリガーされます。 お客様は[カスタム アラート ルール](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)を使用して、お客様の環境から既に収集されているデータに基づく新しいセキュリティ アラートを定義することができます。

Security Center では、優先順位の付いたセキュリティ アラートとインシデントを提供します。 Security Center では、お客様が潜在的なセキュリティ問題を簡単に検出､対処できるようにします。 検出された脅威ごとに、[脅威インテリジェンス レポート](https://docs.microsoft.com/azure/security-center/security-center-threat-report)が生成されます。 インシデント対応チームは、脅威を調査し､修復するときに､このレポートを利用します。

この参照アーキテクチャではまた、Security Center の [脆弱性評価](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)を利用します｡ 構成後、パートナー エージェント (Qualys など) はパートナーの管理プラットフォームに脆弱性データを報告します。 次に、パートナーの管理プラットフォームは、脆弱性と正常性の監視データを Security Center に送り返します。 お客様はこの情報を利用して、脆弱な VM をすぐに特定することができます｡

**Azure Application Gateway**:このアーキテクチャでは、Web アプリケーション ファイアウォールが構成され、OWASP ルールセットが有効なアプリケーション ゲートウェイを使用して、セキュリティの脆弱性のリスクを軽減します。 その他の機能には次のものがあります。

- [エンド ツー エンド SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL オフロード](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)の有効化
- [TLS v1.0 および v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) の無効化
- [Web アプリケーション ファイアウォール](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (防止モード)
- OWASP 3.0 ルールセットを使用した[防止モード](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)
- [診断ログ](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)の有効化
- [カスタム正常性プローブ](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) と [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) による、その他の保護と通知の提供。 Azure Security Center は評価システムも提供します。

### <a name="business-continuity"></a>ビジネス継続性

**高可用性**: このソリューションは、すべての仮想マシンを[可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)にデプロイします。 可用性セットは、可用性を向上させるために仮想マシンが複数の分離されたハードウェア クラスターに分散されるようにします｡ Azure SLA を 99.95% 満たすように、計画または計画外メンテナンス イベントの間、少なくとも 1 つの仮想マシンを利用できます｡

**Recovery Services コンテナー**:[Recovery Services コンテナー](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)にはバックアップ データが保管され、このアーキテクチャ内の Azure 仮想マシンのすべての構成が保護されます。 Recovery Services コンテナーを使用すると、VM 全体を復元せずに IaaS VM からファイルとフォルダーを復元できます｡ このプロセスによって、復元時間は短縮されます｡

**クラウド監視**: [クラウド監視](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness)は、Windows Server 2016 内のフェールオーバー クラスター クォーラム監視の一種であり、Azure をアービトレーション ポイントとして使用します。 クォーラム監視などのクラウド監視は、投票を取得し、クォーラムの計算に参加できます。 標準の公開されている Azure Blob storage を使用します。 これにより、パブリック クラウドでホストされている VM のメンテナンスの余分なオーバーヘッドを排除できます。

### <a name="logging-and-auditing"></a>ログ記録と監査

Azure サービスは、システムの正常性だけではなく、システムとユーザーのアクティビティも詳細に記録します。
- **アクティビティ ログ**:[アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、状態の判断に役立ちます。
- **診断ログ**:[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログ、Storage ログ、Key Vault 監査ログ、および Application Gateway のアクセス ログとファイアウォール ログが含まれます。 すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 ユーザーは個々の要件に応じて最大 730 日間の保有期間を設定できます。

**Azure Monitor ログ**: これらのログは、処理、格納、およびダッシュボードでのレポート表示を行うために、[Azure Monitor ログ](https://azure.microsoft.com/services/log-analytics/)に統合されます。 データの収集後は、Log Analytics ワークスペース内にデータ型ごとに別にテーブルに編成されます｡ これにより、元のソースに関係なくにすべてのデータをまとめて分析できます。 Security Center は、Azure Monitor ログに統合されています。 お客様は Kusto クエリを使用してセキュリティ イベント データにアクセスし、それを他のサービスからのデータと組み合わせることができます。

このアーキテクチャの一部として、次の Azure [監視ソリューション](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)が含まれます。
-   [Active Directory 評価](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment):Active Directory 正常性チェック ソリューションはサーバー環境のリスクと正常性を定期的に評価します。 このソリューションは、デプロイされているサーバー インフラストラクチャに固有の推奨事項を優先順位付きの一覧で提供します。
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment):SQL 正常性チェック ソリューションはサーバー環境のリスクと正常性を定期的に評価します。 このソリューションは、デプロイされているサーバー インフラストラクチャに固有の推奨事項を優先順位付きの一覧で提供します。
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth):Agent Health ソリューションは、デプロイされているエージェント数とその地理的分散を報告します。 また、応答のないエージェント数と運用データを送信するエージェント数の報告もします。
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity):Activity Log Analytics ソリューションは、顧客向けのすべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。

**Azure Automation**:[Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) は、Runbook の格納、実行、管理を行います。 このソリューションでは、Runbook は SQL Server からログを収集できます。 お客様は､Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) ソリューションを利用して､環境の変更を簡単に把握できます。

**Azure Monitor**:[Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ではパフォーマンスを追跡し、セキュリティ維持し、傾向を把握することができます。 データの監査やアラートの作成、データのアーカイブを行うことができます。 Azure リソースにおける API 呼び出し追跡することもできます。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラムは[ダウンロード](https://aka.ms/nist171-iaaswa-tm)することができます｡また､こちらにも提供されます。 このモデルは、お客様が修正を加える際にシステム インフラストラクチャの潜在的なリスクを理解するのに役立ちます。

![NIST SP 800-171 のための IaaS Web アプリケーションの脅威モデル](images/nist171-iaaswa-threat-model.png "NIST SP 800-171 のための IaaS Web アプリケーションの脅威モデル")

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント

[Azure のセキュリティとコンプライアンスのブループリント - NIST SP 800-171 顧客責任マトリックス](https://aka.ms/nist171-crm)には、NIST SP 800-171 で必要とされるすべてのセキュリティ制御が一覧表示されています。 このマトリックスには、各制御の実装が、Microsoft、お客様、または両者間での共有の責任のどれに該当するかが、詳細に示されています。

[Azure のセキュリティとコンプライアンスのブループリント -　NIST SP 800-171 PaaS Web Application Control Implementation Matrix](https://aka.ms/nist171-iaaswa-cim) は､PaaS Web アプリケーション アーキテクチャで取り上げられている NIST SP 800-171 コントロールに関する情報を提供します。 これには、取り上げられている各コントロール要件に対する実装の準拠状況に関する詳細な説明が含まれています。

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項
### <a name="vpn-and-expressroute"></a>VPN と ExpressRoute
この PaaS Web アプリケーションの参照アーキテクチャの一部としてデプロイされるリソースへの接続を安全に確立するために、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) またはセキュリティ保護された VPN トンネルを構成する必要があります。 ExpressRoute または VPN を適切に設定することで、お客様は転送中のデータに対して保護のレイヤーを追加できます。

Azure を使用してセキュリティ保護された VPN トンネルを実装することにより、オンプレミス ネットワークと Azure 仮想ネットワークの間で仮想プライベート接続を作成できます。 この接続は、インターネット経由で行われます。 お客様は、この接続を利用して､そのネットワークと Azure 間の暗号化されたリンク内で情報を安全に「トンネリング」できます｡ サイト間 VPN は、数十年にわたってあらゆる規模の企業に導入されている安全で成熟したテクノロジです。 このオプションでは、暗号化メカニズムとして [IPsec トンネル モード](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))が使用されています。

VPN トンネル内のトラフィックはサイト間 VPN を使用してインターネット上を転送されるため、Microsoft は、もう 1 つの､さらに高いセキュリティで保護された接続オプションを提供しています。 ExpressRoute は、オンプレミスの場所または Exchange ホスティング プロバイダーと Azure との間の専用の WAN リンクです。 ExpressRoute 接続は、お客様の通信プロバイダーと直接接続します。 その結果、データはインターネットを経由することはなく､インターネットに公開されることもありません。 ExpressRoute 接続は一般的な接続よりも信頼性が高く、高速で、待ち時間が短く、セキュリティの高い接続を提供します。 

オンプレミス ネットワークを Azure に拡張するセキュアなハイブリッド ネットワークを実装するためのベスト プラクティスが[提供されています](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="disclaimer"></a>免責事項

- このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。 
- このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。 
- お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。 
- このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。 
- このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
- このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
