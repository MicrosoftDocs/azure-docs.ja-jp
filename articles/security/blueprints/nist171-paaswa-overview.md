---
title: Azure のセキュリティとコンプライアンスのブループリント - NIST SP 800-171 のための PaaS Web アプリケーション
description: Azure のセキュリティとコンプライアンスのブループリント - PaaS Web アプリケーション NIST SP 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f9773c3b372ab22cbcd99828e147d23c185c4eb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127329"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Azure のセキュリティとコンプライアンスのブループリント - NIST Special Publication 800-171 のための PaaS Web アプリケーション

## <a name="overview"></a>概要
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) では、非連邦情報システムおよび組織に存在する管理対象の非機密情報 (CUI) を保護するためのガイドラインが提供されます。 NIST SP 800-171 では、CUI の機密性を保護するための 14 個のセキュリティ要件ファミリが確立されています。

この Azure のセキュリティとコンプライアンスのブルー プリントでは、NIST SP 800-171 制御のサブセットを実装する、Azure のサービスとしてのプラットフォーム (PaaS) Web アプリケーションをお客様がデプロイするのに役立つガイダンスが提供されます。 このソリューションでは、お客様が特定のセキュリティおよびコンプライアンス要件を紹介しています｡ これはさらに、Azure で独自の Web アプリケーションをビルドして構成するための基礎としても役立ちます。

この参照アーキテクチャ、関連する実装ガイド、および脅威モデルは、お客様固有の要件に適合させるための基礎として使用されることを目的としています。 運用環境でそのまま使用することはできません。 変更を加えずにこのアーキテクチャをデプロイすることは、NIST SP 800-171 の要件を完全に満たすには不十分です。 このアーキテクチャを使用して構築されたソリューションのセキュリティとコンプライアンスの適切なアセスメントは、お客様が実施する必要があります。 要件は、お客様の実装の細部によって異なる場合があります。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント

この Azure のセキュリティとコンプライアンスのブループリントでは、バックエンドに Azure SQL Database バックエンドを使用する PaaS Web アプリケーション向けの参照アーキテクチャが提供されます。 Web アプリケーションは、分離された App Service Environment でホストされます。これは、Azure データ センター内のプライベートな専用環境です。 この環境では、Azure によって管理されている仮想マシン (VM) 間に Web アプリケーションのトラフィックを負荷分散します。 このアーキテクチャには、ネットワーク セキュリティ グループ (NSG)、Azure アプリケーション ゲートウェイ、Azure DNS、および Azure Load Balancer も含まれています。

分析とレポートを強化するために、Azure SQL データベースを列ストア インデックスを使って構成できます。 Azure SQL データベースは、顧客の用途に対応するために、スケールアップ、スケールダウン、完全な切り離しが可能です。 すべての SQL トラフィックは、自己署名証明書を含めることによって、SSL で暗号化されます。 ベスト プラクティスとして、Azure では、セキュリティ強化のために信頼された証明書機関を利用することをお勧めします。

このソリューションでは、Azure Storage アカウントを使用します。お客様は、Storage Service Encryption を使用して保存データの機密性を保持するように、Azure Storage アカウントを構成できます。 Azure は、回復性のために、お客様が選択したデータ センター内にデータの 3 つのコピーを保存します。 Geo 冗長ストレージにより、データは数百マイル離れたセカンダリ データ センターにレプリケートされ、さらにそのデータ センター内で 3 つのコピーとして格納されます。 この配置により、お客様のプライマリ データ センターでトラブルが発生してもデータが失われることを避けることができます。

セキュリティ強化のために、このソリューションのすべてのリソースは、Azure Resource Manager を通じてリソース グループとして管理されます。 Azure Active Directory (Azure AD) のロールベース アクセス制御 (RBAC) は、デプロイされたリソースへのアクセスの制御に使用されます。 Azure Key Vault では､これらのリソースに顧客キーが含まれます。 システムの正常性は、Azure Monitor によって監視されます。 お客様はこの監視サービスを､ログをキャプチャするよう構成できます。 システムの正常性は、使いやすい単一のダッシュボードに表示されます。

SQL Database は SQL Server Management Studio により管理されます。 これはセキュリティで保護された VPN または Azure ExpressRoute 接続経由で SQL Database にアクセスするように構成されているローカル コンピューターから実行されます。

Application Insights は、Azure Monitor ログを通して、リアルタイムでアプリケーションのパフォーマンス管理と分析を行います。"*Microsoft では、管理用の VPN または ExpressRoute 接続を構成し、参照アーキテクチャ サブネットにデータをインポートすることをお勧めしています。* "

![NIST SP 800-171 のための PaaS Web アプリケーションの参照アーキテクチャ ダイアグラム](images/nist171-paaswa-architecture.png "NIST SP 800-171 のための PaaS Web アプリケーションの参照アーキテクチャ ダイアグラム")

このソリューションでは、次の Azure サービスを使用します。 詳しくは、[deployment architecture](#deployment-architecture)セクションをご覧ください。

- Azure Virtual Machines
    - (1) 管理/踏み台 (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1) /16 ネットワーク
    - (4) /24 ネットワーク
    - (4) ネットワーク セキュリティ グループ
- Azure Application Gateway
    - Web アプリケーション ファイアウォール
        - ファイアウォール モード: 防止
        - ルール セット:OWASP
        - リスナー ポート:443
- Application Insights
- Azure Active Directory
- App Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (ログ)
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Automation
- Azure Web Apps

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ
次のセクションで、デプロイと実装の要素について詳しく説明します。

**Azure Resource Manager**:[Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) を使用して、ソリューション内の複数のリソースを 1 つのグループとして操作できます。 ソリューションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。 お客様はデプロイ用のテンプレートを使用します。 テンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。 Resource Manager には、デプロイ後のリソースの管理に役立つ、セキュリティ、監査、タグ付けの機能が用意されています。

**踏み台ホスト**:踏み台ホストは、この環境にデプロイされているリソースへのアクセスに利用できる単一エントリ ポイントです。 踏み台ホストは、セーフ リスト上のパブリック IP アドレスからのリモート トラフィックのみを許可することで、デプロイ済みのリソースへのセキュリティで保護された接続を提供します。 リモート デスクトップ トラフィックを許可するには、トラフィックのソースが NSG で定義されている必要があります。

このソリューションでは、次の構成を持つドメイン参加済み踏み台ホストとして仮想マシンを作成します。
-   [マルウェア対策拡張機能](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Diagnostics 拡張機能](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault を使用した [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   未使用時の仮想マシン リソースの消費を抑えるための[自動シャットダウン ポリシー](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)。
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) の有効化。実行中のオペレーティング システムから分離されている保護された環境で、資格情報とその他のシークレットが実行されるようにします。

**Web Apps**: [Web Apps](https://docs.microsoft.com/azure/app-service/) は、Azure App Service の機能です。 これを使用して、インフラストラクチャを管理することなく、任意のプログラミング言語で Web アプリケーションを構築し、ホストすることができます。 これは自動スケールと高可用性を提供します。 Windows と Linux の両方をサポートしており、GitHub、Azure DevOps、または任意の Git リポジトリからの自動デプロイが可能になります。

**App Service Environment**:[App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) は、App Service の機能です。 高スケールで安全に App Service アプリケーションを実行するための、完全に分離された専用の環境を提供します。

App Service 環境は、単一のアプリケーションのみを実行するために分離されています。 これは常に、仮想ネットワークにデプロイします。 この分離機能により、参照アーキテクチャではテナントが完全に分離されており、Azure のマルチテナント環境から除外されます。 顧客は、受信と送信の両方のアプリケーション ネットワーク トラフィックをきめ細かく制御することができます。 アプリケーションは、仮想ネットワークを介した、オンプレミスの企業リソースへのセキュリティで保護された高速な接続を確立することができます。 お客様は、App Service Environment を使用して、負荷のメトリック、利用可能な予算、または定義されたスケジュールに基づいて "自動スケール" することができます。

このアーキテクチャに App Service Environment を使用することで、次のコントロール/構成が提供されます。

- セキュリティ保護された Azure 仮想ネットワーク内でのホストと、ネットワーク セキュリティ規則。
- HTTPS 通信用の自己署名内部ロード バランサー証明書。 ベスト プラクティスとして、Microsoft では、セキュリティ強化のために信頼された証明書機関を利用することをお勧めします。
- [内部負荷分散モード](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (モード 3)。
- [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings) の無効化。
- [TLS 暗号](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)の変更。
- [受信トラフィック N/W ポート](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)のコントロール。
- [Web アプリケーション ファイアウォール - データの制限](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)。
- [Azure SQL Database トラフィック](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)の許可。

### <a name="virtual-network"></a>仮想ネットワーク
このアーキテクチャは、10.200.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

**ネットワーク セキュリティ グループ**:[NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) には、仮想ネットワーク内のトラフィックを許可または拒否するアクセス制御リストが含まれます。 NSG を使用して、サブネットまたは個々の VM レベルでトラフィックを保護できます。 次の NSG が存在します。
- Application Gateway 用の 1 つの NSG
- App Service Environment 用の 1 つの NSG
- SQL Database 用の 1 つの NSG
- 踏み台ホスト用の 1 つの NSG

各 NSG では、ソリューションが安全かつ適切に機能できるように、固有のポートとプロトコルが開かれます。 さらに、各 NSG で次の構成が有効になります。
  - [診断ログとイベント](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)が有効化され、ストレージ アカウントに格納される。
  - Azure Monitor ログが [NSG の診断](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)に接続される。

**サブネット**:各サブネットは、対応する NSG に関連付けられています。

**Azure DNS**:ドメイン ネーム システム (DNS) は、Web サイトまたはサービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) は、DNS ドメインのホスティング サービスであり、Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 Azure DNS は、プライベート DNS ドメインもサポートします。

**Azure Load Balancer**:[Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) を使用して、お客様のアプリケーションを拡張し、サービスを高可用性にすることができます。 Load Balancer は、受信と送信のシナリオをサポートしています。 Load Balancer は、低遅延と高スループットを実現できるだけでなく、あらゆる TCP アプリケーションと UDP アプリケーションの数百万ものフローにスケールアップできます。

### <a name="data-in-transit"></a>転送中のデータ
Azure の既定では､Azure データセンターとの間のあらゆる通信は暗号化されます。 Azure Portal での Azure Storage に対するすべてのトランザクションは HTTPS 経由で行われます。

### <a name="data-at-rest"></a>保存データ

このアーキテクチャでは、暗号化、データベース監査などの手段によって保存データを保護します。

**Azure Storage**:暗号化された保存データの要件を満たすために、すべての [Storage](https://azure.microsoft.com/services/storage/) で [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption) が使用されます。 この機能は、NIST SP 800-171 によって定義されている組織のセキュリティ コミットメントとコンプライアンス要件のサポートにおいてデータを保護するために役立ちます。

**Azure Disk Encryption**:[Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、Windows の BitLocker 機能を使用して、データ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Key Vault と統合されています。

**Azure SQL Database**:SQL データベース インスタンスは、次のデータベース セキュリティ対策を使用します。
-   [Active Directory 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication)を使用して、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
-   [SQL Database の監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
-   SQL Database は、[Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用するように構成されます。 これは、データベース、関連付けられたバックアップ、トランザクション ログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 透過的なデータ暗号化により、保存されているデータが未承認のアクセスに晒されないようになります｡
-   [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
-   [SQL 脅威の検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)により、発生した潜在的な脅威を検出し、対応することができます。 不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンが見つかった場合に、セキュリティ アラートが提供されます。
-   [暗号化された列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)により、データベース システム内で機密データがプレーンテキストとして表示されないことが保証されます。 データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
- [動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)では、特権のないユーザーやアプリケーションに対してデータをマスクすることで､機密データの露出を制限されます。 DDM はまた潜在的に機密のデータを自動的に検出し、適用する適切なマスクを提案することもできます。 動的データ マスクは、機密データが未承認のアクセスによってデータベースが終了されるようなアクセスを制限するのに役立ちます。 *お客様は、お使いのデータベース スキーマに準拠するように設定を調整する必要があります。*

### <a name="identity-management"></a>ID 管理
次のテクノロジによって、Azure 環境でのデータへのアクセスを管理する機能が提供されます。
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 このソリューションのすべてのユーザーは Azure AD で作成し､それらユーザーには SQL Database にアクセスするユーザーが含まれます。
-   アプリケーションに対する認証は Azure AD を使用して行われます。 詳細は、「[Azure AD とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。 Azure AD は､データベース列の暗号化で SQL Database に対する認証にも使用されます。 詳細については、[SQL Database で機密データを保護する](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)方法に関するページを参照してください。
-   管理者は､[Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) を使用してきめ細かなアクセス許可を定義することができます｡ RBAC を使用すると、職務に必要な量のアクセス権だけをユーザーに付与することができます。 すべてのユーザーに Azure リソースへの無制限アクセスを許可する代わりに、管理者は、リソースやデータにアクセスするための特定のアクションのみを許可できます。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。
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

**Azure Security Center**:[Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) を使用すると、お客様はワークロード全体へのセキュリティ ポリシーの一元的な適用と管理、脅威にさらされる状態の制限、攻撃の検出とその対応を行うことができます。 Security Center はまた、Azure サービスの既存の構成にアクセスして、セキュリティ状況の改善とデータの保護に役立つ、構成とサービスに関する推奨事項を提供します。

Security Center では、さまざまな検出機能を使用して、お客様の環境が標的の可能性がある攻撃を通知します。 これらのアラートには、アラートをトリガーした要因、対象となったリソース、攻撃元に関する重要な情報が含まれています。 Security Center には、一連の[セキュリティ アラート](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)が事前に定義されています。これらは、脅威または不審なアクティビティが発生した際にトリガーされます。 お客様は[カスタム アラート ルール](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)を使用して、お客様の環境から既に収集されているデータに基づく新しいセキュリティ アラートを定義することができます。

Security Center では、優先順位の付いたセキュリティ アラートとインシデントを提供します。 Security Center では、お客様が潜在的なセキュリティ問題を簡単に検出､対処できるようにします。 検出された脅威ごとに、[脅威インテリジェンス レポート](https://docs.microsoft.com/azure/security-center/security-center-threat-report)が生成されます。 インシデント対応チームは、脅威を調査し､修復するときに､このレポートを利用します。

**Azure Application Gateway**:このアーキテクチャでは、Web アプリケーション ファイアウォールが構成され、OWASP ルールセットが有効なアプリケーション ゲートウェイを使用して、セキュリティの脆弱性のリスクを軽減します。 その他の機能には次のものがあります。

- [エンド ツー エンド SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL オフロード](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)の有効化
- [TLS v1.0 および v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) の無効化
- [Web アプリケーション ファイアウォール](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (防止モード)
- OWASP 3.0 ルールセットを使用した[防止モード](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)
- [診断ログ](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)の有効化
- [カスタム正常性プローブ](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) と [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) による、その他の保護と通知の提供。 Azure Security Center は評価システムも提供します。

### <a name="logging-and-auditing"></a>ログ記録と監査

Azure サービスは、システムの正常性だけではなく、システムとユーザーのアクティビティも詳細に記録します。
- **アクティビティ ログ**:[アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、状態の判断に役立ちます。
- **診断ログ**:[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログ、Storage ログ、Key Vault 監査ログ、および Application Gateway のアクセス ログとファイアウォール ログが含まれます。 すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 ユーザーは個々の要件に応じて最大 730 日間の保有期間を設定できます。

**Azure Monitor ログ**: ログは、処理、格納、ダッシュボードでのレポート表示を行うために、[Azure Monitor ログ](https://azure.microsoft.com/services/log-analytics/)に統合されます。 データの収集後は、Log Analytics ワークスペース内にデータ型ごとに別にテーブルに編成されます｡ これにより、元のソースに関係なくにすべてのデータをまとめて分析できます。 Security Center は、Azure Monitor ログに統合されています。 お客様は Kusto クエリを使用してセキュリティ イベント データにアクセスし、それを他のサービスからのデータと組み合わせることができます。

このアーキテクチャの一部として、次の Azure [監視ソリューション](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)が含まれます。
-   [Active Directory 評価](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment):Active Directory 正常性チェック ソリューションはサーバー環境のリスクと正常性を定期的に評価します。 このソリューションは、デプロイされているサーバー インフラストラクチャに固有の推奨事項を優先順位付きの一覧で提供します。
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment):SQL 正常性チェック ソリューションはサーバー環境のリスクと正常性を定期的に評価します。 このソリューションは、デプロイされているサーバー インフラストラクチャに固有の推奨事項を優先順位付きの一覧で提供します。
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth):Agent Health ソリューションは、デプロイされているエージェント数とその地理的分散を報告します。 また、応答のないエージェント数と運用データを送信するエージェント数の報告もします。
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity):Activity Log Analytics ソリューションは、顧客向けのすべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。

**Azure Automation**:[Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) は、Runbook の格納、実行、管理を行います。 このソリューションでは、Runbook は SQL Database からログを収集できます。 お客様は､Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) ソリューションを利用して､環境の変更を簡単に把握できます。

**Azure Monitor**:[Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ではパフォーマンスを追跡し、セキュリティ維持し、傾向を把握することができます。 データの監査やアラートの作成、データのアーカイブを行うことができます。 Azure リソースにおける API 呼び出し追跡することもできます。

**Application Insights**:[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理サービスです。 Application Insights は、パフォーマンスの異常を検出します。 これは実行中の Web アプリケーションを監視するために使用できます。 Application Insights には強力な分析ツールが組み込まれており、お客様が問題を診断する場合や、ユーザーがアプリを使用して実行している操作を把握するのに役立ちます。 それは、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラムは[ダウンロード](https://aka.ms/nist171-paaswa-tm)することができます｡また､こちらにも提供されます。 このモデルは、お客様が修正を加える際にシステム インフラストラクチャの潜在的なリスクを理解するのに役立ちます。

![NIST SP 800-171 のための PaaS Web アプリケーションの脅威モデル](images/nist171-paaswa-threat-model.png "NIST SP 800-171 のための PaaS Web アプリケーションの脅威モデル")

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント
[Azure のセキュリティとコンプライアンスのブループリント - NIST SP 800-171 顧客責任マトリックス](https://aka.ms/nist171-crm)には、NIST SP 800-171 で必要とされるすべてのセキュリティ制御が一覧表示されています。 このマトリックスには、各制御の実装が、Microsoft、お客様、または両者間での共有の責任のどれに該当するかが、詳細に示されています。

[Azure のセキュリティとコンプライアンスのブループリント -　NIST SP 800-171 PaaS Web Application Control Implementation Matrix](https://aka.ms/nist171-paaswa-cim) は､PaaS Web アプリケーション アーキテクチャで取り上げられている NIST SP 800-171 コントロールに関する情報を提供します。 これには、取り上げられている各コントロール要件に対する実装の準拠状況に関する詳細な説明が含まれています。

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項
### <a name="vpn-and-expressroute"></a>VPN と ExpressRoute
この PaaS Web アプリケーションの参照アーキテクチャの一部としてデプロイされるリソースへの接続を安全に確立するために、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) またはセキュリティ保護された VPN トンネルを構成する必要があります。 ExpressRoute または VPN を適切に設定することで、お客様は転送中のデータに対して保護のレイヤーを追加できます。

Azure を使用してセキュリティ保護された VPN トンネルを実装することにより、オンプレミス ネットワークと Azure 仮想ネットワークの間で仮想プライベート接続を作成できます。 この接続はインターネットをまたいでいるため、お客様は自社ネットワークと Azure の間の暗号化されたリンクの "トンネル" を通して情報を送信できます。 サイト間 VPN は、数十年にわたってあらゆる規模の企業に導入されている安全で成熟したテクノロジです。 このオプションでは、暗号化メカニズムとして [IPsec トンネル モード](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))が使用されています。

VPN トンネル内のトラフィックはサイト間 VPN を使用してインターネット上を転送されるため、Microsoft は、もう 1 つの､さらに高いセキュリティで保護された接続オプションを提供しています。 ExpressRoute は、オンプレミスの場所または Exchange ホスティング プロバイダーと Azure との間の専用の WAN リンクです。 ExpressRoute 接続は、お客様の通信プロバイダーと直接接続します。 その結果、データはインターネットを経由することはなく､インターネットに公開されることもありません。 ExpressRoute 接続は一般的な接続よりも信頼性が高く、高速で、待ち時間が短く、セキュリティの高い接続を提供します。

オンプレミス ネットワークを Azure に拡張するセキュアなハイブリッド ネットワークを実装するためのベスト プラクティスが[提供されています](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="disclaimer"></a>免責事項

 - このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。
 - このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。
 - お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。
 - このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。
 - このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
 - このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
