---
title: 'Azure のセキュリティとコンプライアンスのブループリント: PCI DSS のための IaaS Web アプリケーション'
description: 'Azure のセキュリティとコンプライアンスのブループリント: PCI DSS のための IaaS Web アプリケーション'
services: security
author: meladie
ms.assetid: 9c825380-2abe-4fdd-800c-59d1fed1780b
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: afe3ed510a4a6ff7804112755aea10c7d344db53
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259677"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-pci-dss"></a>Azure のセキュリティとコンプライアンスのブループリント:PCI DSS のための IaaS Web アプリケーション

## <a name="overview"></a>概要

この Azure のセキュリティとコンプライアンスのブループリントは、カード所有者のデータの収集、保存、取得に適した Payment Card Industry Data Security Standards (PCI DSS 3.2) に準拠したサービスとしてのインフラストラクチャ (IaaS) 環境をデプロイするためのガイダンスを提供します。 これは一般的な参照アーキテクチャを紹介し、安全な複数の階層からなる準拠環境でのクレジット カード データの適切な処理 (カード番号、有効期限、検証データを含む) を示します。 このブループリントは、デプロイの手間とコストを削減するためのクラウドベースのアプローチを求めている組織のニーズを満たす、エンド ツー エンドのソリューションを示しています。

この参照アーキテクチャ、実装ガイド、脅威モデルは、お客様が PCI DSS 3.2 要件に準拠するための基礎を提供します。 このソリューションは、お客様が PCI DSS 3.2 に準拠した方法でワークロードを Azure にデプロイするためのベースラインを提供します。ただし、追加の構成が必要なため、このソリューションをそのまま運用環境で使用しないでください。

PCI DSS への準拠を達成するには、お客様の実稼働ソリューションが認定セキュリティ評価機関から認証を受ける必要があります。 お客様は、このアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスの評価を実施する責任を負います。要件は、お客様の実装によって変化する可能性があるからです。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント

このソリューションは、バックエンドに SQL Server を使用する IaaS Web アプリケーション向けのリファレンス アーキテクチャをデプロイします。 アーキテクチャには、Web 層、データ層、Active Directory インフラストラクチャ、Application Gateway、および Load Balancer が含まれています。 Web 層とデータ層にデプロイされる仮想マシンは可用性セット内に構成され、SQL Server インスタンスは高可用性のために Always On 可用性グループ内に構成されます。 仮想マシンはドメインに参加し、Active Directory グループ ポリシーを使用して、オペレーティング システム レベルでセキュリティとコンプライアンスの構成が適用されます。 管理踏み台ホストは、デプロイされたリソースにアクセスするためのセキュリティで保護された接続を管理者に提供します。 **Azure では、参照アーキテクチャのサブネットに対する管理とデータ インポートのために、VPN または ExpressRoute 接続を構成することを推奨しています。**

![PCI DSS のための IaaS Web アプリケーションの参照アーキテクチャ ダイアグラム](images/pcidss-iaaswa-architecture.png "PCI DSS のための IaaS Web アプリケーションの参照アーキテクチャ ダイアグラム")

このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャについて詳しくは、「[デプロイ アーキテクチャ](#deployment-architecture)」セクションをご覧ください。

- 可用性セット
    - (1) Active Directory ドメイン コントローラー
    - (1) SQL クラスター ノード
    - (1) Web/IIS
- Azure Active Directory
- Azure Application Gateway
    - (1) Web アプリケーション ファイアウォール
        - ファイアウォール モード: 防止
        - ルール セット:OWASP 3.0
        - リスナー ポート:443
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Storage
    - (7) Geo 冗長ストレージ アカウント
- Azure Virtual Machines
    - (1) 管理/踏み台 (Windows Server 2016 Datacenter)
    - (2) Active Directory ドメイン コントローラー (Windows Server 2016 Datacenter)
    - (2) SQL Server クラスター ノード (Windows Server 2016 上の SQL Server 2017)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1) /16 ネットワーク
    - (5) /24 ネットワーク
    - (5) ネットワーク セキュリティ グループ
- クラウド監視
- Recovery Services コンテナー

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ

次のセクションで、デプロイと実装の要素について詳しく説明します。

**踏み台ホスト**:踏み台ホストは、この環境にデプロイされたリソースへのユーザーのアクセスを許可する単一エントリ ポイントです。 要塞ホストは、セーフ リスト上のパブリック IP アドレスからのリモート トラフィックのみを許可することで、デプロイ済みのリソースへのセキュリティで保護された接続を提供します。 リモート デスクトップ (RDP) トラフィックを許可するには、トラフィックのソースがネットワーク セキュリティ グループに定義されている必要があります。

このソリューションでは、次の構成を持つドメイン参加済み踏み台ホストとして仮想マシンを作成します。
-   [マルウェア対策拡張機能](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Diagnostics 拡張機能](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   Azure Key Vault を使用した [Azure ディスクの暗号化](../azure-security-disk-encryption-overview.md)
-   未使用時の仮想マシン リソースの消費を抑えるための[自動シャットダウン ポリシー](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) の有効化。実行中のオペレーティング システムから分離されている保護された環境で、資格情報とその他のシークレットが実行されるようにします。

### <a name="virtual-network"></a>仮想ネットワーク

このアーキテクチャは、10.200.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

**ネットワーク セキュリティ グループ**:このソリューションは、分離された Web サブネット、データベース サブネット、Active Directory サブネット、管理サブネットを持つアーキテクチャの仮想ネットワーク内にリソースをデプロイします。 サブネット間のトラフィックをシステムと管理機能に必要なものだけに制限するために、個々のサブネットに適用されるネットワーク セキュリティ グループの規則に従って、サブネットが論理的に分離されます。

ソリューションでデプロイされる[ネットワーク セキュリティ グループ](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json)の構成を参照してください。 組織は、[こちらのドキュメント](../../virtual-network/virtual-network-vnet-plan-design-arm.md)をガイドとして使用して上のファイルを編集することで、ネットワーク セキュリティ グループを構成できます。

各サブネットには、専用のネットワーク セキュリティ グループがあります。
- Application Gateway 用の 1 つのネットワーク セキュリティ グループ (LBNSG)
- 踏み台ホスト用の 1 つのネットワーク セキュリティ グループ (MGTNSG)
- プライマリおよびバックアップ ドメイン コントローラー用の 1 つのネットワーク セキュリティ グループ (ADNSG)
- SQL Server とクラウド監視用の 1 つのネットワーク セキュリティ グループ (SQLNSG)
- Web 層用の 1 つのネットワーク セキュリティ グループ (WEBNSG)

### <a name="data-in-transit"></a>転送中のデータ

Azure では、Azure データセンターとの間のすべての通信を既定で暗号化します。 さらに、Azure Portal での Azure Storage に対するすべてのトランザクションは HTTPS 経由で行われます。

### <a name="data-at-rest"></a>保存データ

このアーキテクチャでは、暗号化やデータベース監査などの複数の手段によって保存データを保護します。

**Azure Storage**:暗号化された保存データの要件を満たすために、すべての [Azure Storage](https://azure.microsoft.com/services/storage/) で [Storage Service Encryption](../../storage/common/storage-service-encryption.md) が使用されます。 これは、PCI DSS 3.2 によって定義されている組織のセキュリティ コミットメントとコンプライアンス要件のサポートにおいてカード所有者のデータを保護するために役立ちます。

**Azure Disk Encryption**:暗号化された Windows IaaS 仮想マシン ディスクに対して Azure Disk Encryption が使用されます。 [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) は、Windows の BitLocker 機能を活用して、OS とデータ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Azure Key Vault と統合されています。

**SQL Server**:SQL Server インスタンスは、次のデータベース セキュリティ対策を使用します。
- [Active Directory 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)を使用して、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
-   [SQL Database の監査](../../sql-database/sql-database-auditing.md)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
-   SQL Database は、[Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (TDE) を使用するように構成されます。TDE は、データベース、関連付けられたバックアップ、トランザクション ログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 Transparent Data Encryption は、保存されているカード所有者のデータが未承認のアクセスの対象になっていないことを保証します。
-   [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
-   [SQL の脅威検出](../../sql-database/sql-database-threat-detection.md)は、不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンに対するセキュリティの警告を提供することで、潜在的な脅威の検出とそれらの脅威への対応を可能にします。
-   [Always Encrypted 列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)により、データベース システム内でカード所有者の機微なデータがプレーンテキストとして表示されないことが確保されます。 データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
- [拡張プロパティ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql)機能を使用して、データ サブジェクトの処理を中止できます。この機能により、カスタム プロパティをデータベース オブジェクトに追加し、データを "中止" とタグ付けして、関連付けられたカード所有者のデータの処理を防ぐアプリケーション ロジックをサポートできます。
- [行レベルのセキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)によって、データへのアクセスを制限するポリシーを定義して、処理を中止できます。
- [SQL Database 動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)は、特権のないユーザーまたはアプリケーションに対してデータをマスクすることでカード所有者の機微なデータの露出を制限します。 動的データ マスクは、潜在的な機微なデータを自動的に検出し、適用する適切なマスクを提案できます。 これは、不正アクセスによるデータベースの終了が発生しないように、カード所有者のデータへのアクセスを識別して削減することに役立ちます。 お客様は、お使いのデータベース スキーマに準拠するように動的データ マスクの設定を調整する必要があります。

### <a name="identity-management"></a>ID 管理

次のテクノロジによって、Azure 環境でのデータへのアクセスを管理する機能が提供されます。

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) は、Microsoft が提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 このソリューションのすべてのユーザー (Azure SQL Database にアクセスするユーザーを含む) は、Azure Active Directory で作成されています。
- アプリケーションに対する認証は Azure Active Directory を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)」をご覧ください。 さらに、データベース列の暗号化では、Azure SQL Database に対してアプリケーションを認証するために Azure Active Directory が使用されます。 詳細については、[SQL Database で機微なデータを保護する](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)方法に関するページを参照してください。
- [Azure ロールベースのアクセス制御](../../role-based-access-control/role-assignments-portal.md)によって、管理者は、業務の遂行に必要なアクセスのみをユーザーに付与するきめの細かいアクセス許可を定義できます。 すべてのユーザーに Azure リソースへの無制限のアクセス許可を付与する代わりに、管理者は、データにアクセスするための特定のアクションのみを許可できます。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) により、特定の情報にアクセスできるユーザーの数を最小限に抑えることができます。 管理者は、Azure Active Directory Privileged Identity Management を使用して、特権 ID と特権 ID によるリソースへのアクセスを検出、制限、監視できます。 この機能を使用して、必要に応じて、オンデマンドの Just-In-Time 管理アクセスを適用することもできます。
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) は、組織の ID に影響を及ぼす可能性のある脆弱性を検出し、組織の ID に関連する検出された不審なアクションに対する自動応答を構成します。さらに、不審なインシデントを調査し、適切なアクションを実行してそれらを解決します。

### <a name="security"></a>セキュリティ

**シークレットの管理**:ソリューションでは、キーとシークレットの管理に [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) が使用されます。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 次の Azure Key Vault 機能は、そのようなデータの保護とアクセスに役立ちます。

- 必要に応じて、高度なアクセス ポリシーが構成されます。
- Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されます。
- Key Vault のすべてのキーとシークレットに有効期限があります。
- Key Vault 内のすべてのキーは、特殊なハードウェア セキュリティ モジュールによって保護されます。 キーの種類は、HSM 保護された 2048 ビット RSA キーです。
- ロールベースのアクセス制御を使用して、すべてのユーザーと ID に最低限必要なアクセス許可が付与されます。
- Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
- キーに対して許可される暗号化操作は必要なものに制限されます。
- このソリューションは、IaaS 仮想マシンのディスク暗号化キーとシークレットを管理できるように、Azure Key Vault と統合されています。

**更新プログラムの管理**: この参照アーキテクチャの一部としてデプロイされる Windows 仮想マシンは、既定で Windows Update サービスから自動的に更新プログラムを受け取るように構成されます。 また、このソリューションには、[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) サービスが含まれます。このサービスによって、必要に応じて仮想マシンにパッチを適用するために、更新されたデプロイが作成される場合があります。

**マルウェア対策**: 仮想マシン向けの [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) は、ウイルスやスパイウェアなどの悪意のあるソフトウェアを識別して削除するリアルタイム保護機能を提供し、既知のマルウェアや不要なソフトウェアが保護されている仮想マシンへのインストールまたは実行を試みた場合に警告する構成可能なアラートを備えています。

**Azure Security Center**:[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) を使用すると、お客様はワークロード全体へのセキュリティ ポリシーの一元的な適用と管理、脅威にさらされる状態の軽減、攻撃の検出とその対応を行うことができます。 さらに、Azure Security Center は、Azure サービスの既存の構成にアクセスして、セキュリティ状況の改善とデータの保護に役立つ、構成とサービスに関する推奨事項を提供します。

Azure Security Center では、さまざまな検出機能を使用して、お客様の環境を対象とする攻撃の可能性を通知します。 これらのアラートには、アラートをトリガーした要因、対象となったリソース、攻撃元に関する重要な情報が含まれています。 Azure Security Center には、事前定義された一連の[セキュリティ アラート](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)が用意されています。これらは、脅威または疑わしいアクティビティが発生した際にトリガーされます。 Azure Security Center の[カスタム アラート ルール](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)を使用すると、お客様が環境から既に収集されているデータに基づいて、新しいセキュリティ アラートを定義できます。

Azure Security Center では、お客様の潜在的なセキュリティの問題の検出と対処が簡単になるように、優先度付けしたセキュリティの警告とインシデントを提供しています。 インシデント対応チームによる脅威の調査と修復を支援するために、検出された脅威ごとに[脅威インテリジェンス レポート](https://docs.microsoft.com/azure/security-center/security-center-threat-report)が生成されます。

**Azure Application Gateway**:このアーキテクチャでは、Web アプリケーション ファイアウォールが構成され、OWASP ルールセットが有効な Azure Application Gateway を使用してセキュリティの脆弱性のリスクを軽減します。 その他の機能には次のものがあります。

- [エンド ツー エンド SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL オフロード](../../application-gateway/create-ssl-portal.md)の有効化
- [TLS v1.0 および v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) の無効化
- [Web アプリケーション ファイアウォール](../../application-gateway/waf-overview.md) (防止モード)
- OWASP 3.0 ルールセットを使用した[防止モード](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)
- [診断ログ](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)の有効化
- [カスタム正常性プローブ](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) と [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) による、その他の保護と通知の提供。 Azure Security Center は評価システムも提供します。

### <a name="business-continuity"></a>ビジネス継続性

**高可用性**: このソリューションは、すべての仮想マシンを[可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)にデプロイします。 可用性セットは、可用性を向上させるために仮想マシンが複数の分離されたハードウェア クラスターに分散されることを保証します。 Azure SLA を 99.95% 満たすように、計画または計画外メンテナンス イベントの間に、少なくとも 1 つの仮想マシンが利用可能です。

**Recovery Services コンテナー**: [Recovery Services コンテナー](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)にはバックアップ データが保管され、このアーキテクチャ内の Azure 仮想マシンのすべての構成が保護されます。 Recovery Services コンテナーを使用すると、仮想マシン全体を復元せずに IaaS 仮想マシンからファイルとフォルダーを復元できるため、復元時間を短縮できます。

**クラウド監視**: [クラウド監視](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness)は、Windows Server 2016 内のフェールオーバー クラスター クォーラム監視の一種であり、Azure をアービトレーション ポイントとして活用します。 他のクォーラム監視と同様、クラウド監視は、投票権を取得し、クォーラムの計算に参加することができますが、パブリックで使用可能な標準の Azure Blob Storage を使用します。 これにより、パブリック クラウドでホストされている仮想マシンのメンテナンスの余分なオーバーヘッドを排除できます。

### <a name="logging-and-auditing"></a>ログ記録と監査

Azure サービスは、システムの正常性だけではなく、システムとユーザーのアクティビティも詳細に記録します。
- **アクティビティ ログ**:[アクティビティ ログ](../../azure-monitor/platform/activity-logs-overview.md)は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、状態の判断に役立ちます。
- **診断ログ**:[診断ログ](../../azure-monitor/platform/resource-logs-overview.md)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログ、Azure Storage ログ、Key Vault 監査ログ、および Application Gateway のアクセス ログとファイアウォール ログが含まれます。 すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 リテンション期間には、組織固有の保有要件を満たすために最長 730 日までの日数をユーザーが設定できます。

**Azure Monitor ログ**: これらのログは、処理、格納、およびダッシュボードでのレポート表示を行うために、[Azure Monitor ログ](https://azure.microsoft.com/services/log-analytics/)に統合されます。 収集されたデータは、その型ごとに Log Analytics ワークスペース内の別個のテーブルにまとめられ、すべてのデータがその収集元にかかわらず一斉に分析できる状態になります。 さらに、Azure Security Center を Azure Monitor ログと統合することで、お客様は Kusto クエリを使用してセキュリティ イベント データにアクセスし、それを他のサービスからのデータと組み合わせることができます。

このアーキテクチャの一部として、次の Azure [監視ソリューション](../../monitoring/monitoring-solutions.md)が含まれます。
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md):Active Directory 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md):SQL 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧をお客様に提供します。
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md):Agent Health ソリューションは、デプロイされたエージェント数とその地理的配置に加え、応答しないエージェント数やオペレーショナル データを送信しているエージェント数を報告します。
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md):Activity Log Analytics ソリューションは、顧客向けのすべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。

**Azure Automation**:[Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) では、Runbook を格納、実行、および管理します。 このソリューションでは、Runbook を使用して、Azure SQL Database からログを収集できます。 Automation [Change Tracking](../../automation/change-tracking.md) ソリューションは、お客様が環境の変更を簡単に識別できるようにします。

**Azure Monitor**:[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) は、組織で監査、アラートの作成、データのアーカイブ、Azure リソース内の API 呼び出しの追跡を実行できるようにすることで、ユーザーがパフォーマンスの追跡、セキュリティの維持、傾向の識別を実行できるようにします。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラム (DFD) を[ダウンロード](https://aka.ms/pcidss-iaaswa-tm)できます。または下記を参照してください。 このモデルは、修正を行う際に、お客様がシステム インフラストラクチャの潜在的なリスクを理解するために役立ちます。

![PCI DSS のための IaaS Web アプリケーションの参照アーキテクチャ ダイアグラム](images/pcidss-iaaswa-threat-model.png "PCI DSS のための IaaS Web アプリケーションの参照アーキテクチャ ダイアグラム")

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント

[Azure のセキュリティとコンプライアンスのブループリント - PCI DSS の顧客責任マトリックス](https://aka.ms/pcidss-crm)には、PCI DSS 3.2 のすべての要件に対する責任の一覧が示されています。

[Azure のセキュリティとコンプライアンスのブループリント - PCI DSS IaaS Web アプリケーションの実装のマトリックス](https://aka.ms/pcidss-iaaswa-cim)は、IaaS Web アプリケーション アーキテクチャによって対応される PCI DSS 3.2 の要件に関する情報を示します。各条項で取り上げられている要件に適合する実装の詳細な説明も含まれています。

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
