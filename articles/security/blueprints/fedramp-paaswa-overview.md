---
title: Azure のセキュリティとコンプライアンスのブループリント - FedRAMP のための PaaS Web アプリケーション
description: Azure のセキュリティとコンプライアンスのブループリント - FedRAMP のための PaaS Web アプリケーション
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: e8458d505575a5bf39bbd0a9970c5044b9cbd0f0
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257335"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Azure のセキュリティとコンプライアンスのブループリント:FedRAMP のための PaaS Web アプリケーション

## <a name="overview"></a>概要

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) は、米国政府全体で実行されるプログラムであり、クラウド製品とサービスに対するセキュリティの評価、承認、および継続的な監視に対する標準化された手法を提供します。 この「Azure のセキュリティとコンプライアンスのブループリント」では、FedRAMP の高度なコントロールのサブセットを実装する Microsoft Azure platform as a service (PaaS) のアーキテクチャを配信する方法についてガイダンスを示します。 このソリューションは、一般的なリファレンス アーキテクチャでの Azure リソースのデプロイと構成に関するガイダンスを示し、お客様が特定のセキュリティ要件とコンプライアンス要件をどのように満たすことができるかを示し、お客様が Azure 上に独自のソリューションをビルドして構成するための基礎として機能します。

このリファレンス アーキテクチャ、関連するコントロール実装ガイド、および脅威モデルは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。 変更せずにこの環境にアプリケーションをデプロイすることは、FedRAMP High ベースラインの要件を完全に満たすためには不十分です。 以下の点に注意してください。
- このアーキテクチャは、お客様が FedRAMP に準拠した方法で Azure にワークロードをデプロイするためのベースラインを提供します。
- お客様は、このアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスの評価を実施する責任を負います。要件は、お客様の実装によって変化する可能性があるからです。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント
このソリューションは、Azure SQL Database バックエンドがある PaaS Web アプリケーション向けのリファレンス アーキテクチャを用意します。 Web アプリケーションは、分離された Azure App Service Environment でホストされます。これは、Azure データセンター内のプライベートな専用環境です。 この環境では、Azure によって管理されている VM 間に Web アプリケーションのトラフィックを負荷分散します。 このアーキテクチャには、ネットワーク セキュリティ グループ、Application Gateway、Azure DNS、および Load Balancer も含まれています。 また、Azure Monitor ではシステム正常性のリアルタイム分析も提供されます。 **Azure では、参照アーキテクチャのサブネットに対する管理とデータ インポートのために、VPN または ExpressRoute 接続を構成することを推奨しています。**

![FedRAMP のための PaaS Web アプリケーションの参照アーキテクチャ図](images/fedramp-paaswa-architecture.png?raw=true "FedRAMP のための PaaS Web アプリケーションの参照アーキテクチャ図")

このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャについて詳しくは、「[デプロイ アーキテクチャ](#deployment-architecture)」セクションをご覧ください。

- Azure Active Directory
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) Web アプリケーション ファイアウォール
        - ファイアウォール モード: 防止
        - ルール セット:OWASP 3.0
        - リスナー: ポート 443
- Azure の仮想ネットワーク
- ネットワーク セキュリティ グループ
- Azure DNS
- Azure Storage
- Azure Monitor
- App Service Environment v2
- Azure Load Balancer
- Azure Web アプリ
- Azure Resource Manager

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ
次のセクションで、デプロイと実装の要素について詳しく説明します。

**Azure Resource Manager**:[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) を使って、ソリューション内の複数のリソースを 1 つのグループとして操作できます。 ソリューションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。 デプロイメントにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。 Resource Manager には、デプロイ後のリソースの管理に役立つ、セキュリティ、監査、タグ付けの機能が用意されています。

**App Service Environment v2**:[Azure App Service Environment (ASE)](https://docs.microsoft.com/azure/app-service/environment/intro) は、App Service アプリケーションを大規模かつ安全に実行するために完全に分離された専用の環境を提供する、App Service の機能です。

ASE は、単一の顧客のアプリケーションだけを実行するために分離され、常に仮想ネットワークにデプロイされます。 顧客は、受信および送信アプリケーション ネットワーク トラフィックをきめ細かく制御できます。また、アプリケーションは、オンプレミスの企業リソースへの仮想ネットワーク経由のセキュリティで保護された高速接続を確立できます。

このアーキテクチャに ASE を使用することで、以下のコントロール/構成が可能になりました。

- セキュリティ保護された Azure Virtual Network 内でのホストと、ネットワーク セキュリティ規則
- HTTPS 通信用の自己署名 ILB 証明書で構成された ASE
- [内部負荷分散モード](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- [TLS 1.0](../../app-service/environment/app-service-app-service-environment-custom-settings.md) の無効化
- [TLS 暗号](../../app-service/environment/app-service-app-service-environment-custom-settings.md)の変更
- [受信トラフィック N/W ポート](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)のコントロール
- [Web アプリケーション ファイアウォール - データの制限](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- [Azure SQL Database トラフィック](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)の許可

「[ガイダンスと推奨事項](#guidance-and-recommendations)」のセクションには ASE に関する追加情報が含まれています。

**Azure Web アプリ**:[Azure App Service](https://docs.microsoft.com/azure/app-service/) を使用すると、お客様はインフラストラクチャを管理することなく、任意のプログラミング言語で Web アプリケーションを構築し、ホストできます。 Azure Web Apps では、自動スケールと高可用性が実現されるほか、Windows と Linux の両方がサポートされています。さらに、GitHub、Azure DevOps、または任意の Git リポジトリからの自動デプロイが可能になります。

### <a name="virtual-network"></a>Virtual Network
このアーキテクチャは、10.200.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

**ネットワーク セキュリティ グループ**:[ネットワーク セキュリティ グループ (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) には、仮想ネットワーク内のトラフィックを許可または拒否するアクセス制御リストが含まれています。 NSG を使用して、サブネットまたは個々の VM レベルでトラフィックを保護できます。 次の NSG が存在します。
- Application Gateway 用の 1 つの NSG
- App Service Environment 用の 1 つの NSG
- Azure SQL Database 用の 1 つの NSG

各 NSG では、ソリューションが安全かつ適切に機能できるように、固有のポートとプロトコルが開かれます。 さらに、各 NSG で次の構成が有効になります。
  - [診断ログとイベント](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)が有効化され、ストレージ アカウントに格納される
  - Azure Monitor ログが [NSG の診断](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)に接続される

**サブネット**:各サブネットは、対応する NSG に関連付けられています。

**Azure DNS**:ドメイン ネーム システム (DNS) は、Web サイトまたはサービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) は、DNS ドメインのホスティング サービスであり、Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 Azure DNS は、プライベート DNS ドメインもサポートします。

**Azure Load Balancer**:[Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) を使って、アプリケーションを拡張し、サービスを高可用性にすることができます。 ロード バランサーは、受信と送信のどちらのシナリオもサポートし、低遅延と高スループットを実現できるだけでなく、あらゆる TCP アプリケーションと UDP アプリケーションの数百万ものフローにスケールアップできます。

### <a name="data-in-transit"></a>転送中のデータ
Azure では、Azure データセンターとの間のすべての通信を既定で暗号化します。 Azure Portal での Azure Storage に対するすべてのトランザクションは HTTPS 経由で行われます。

### <a name="data-at-rest"></a>保存データ
このアーキテクチャでは、暗号化、データベース監査などの手段によって保存データを保護します。

**Azure Storage**:暗号化された保存データの要件を満たすために、すべての [Azure Storage](https://azure.microsoft.com/services/storage/) で [Storage Service Encryption](../../storage/common/storage-service-encryption.md) が使用されます。

**Azure Disk Encryption**
: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) は、Windows の BitLocker 機能を活用して、OS とデータ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Azure Key Vault と統合されています。

**Azure SQL Database**:Azure SQL データベース インスタンスは、次のデータベース セキュリティ対策を使用します。
-   [AD 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)を使用して、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
-   [SQL Database の監査](../../sql-database/sql-database-auditing.md)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
-   Azure SQL Database は、[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用するように構成されます。TDE は、データベース、関連付けられたバックアップ、およびトランザクション ログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。
-   [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
-   [SQL の脅威検出](../../sql-database/sql-database-threat-detection.md)は、不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンに対するセキュリティの警告を提供することで、潜在的な脅威の検出とそれらの脅威への対応を可能にします。
-   [Always Encrypted 列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)により、データベース システム内で機密データがプレーンテキストとして表示されないことが保証されます。 データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
- [行レベルのセキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)によって、データへのアクセスを制限するポリシーを定義して、処理を中止できます。
- [SQL Database 動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)は、参照アーキテクチャがデプロイされた後に実行できます。 お客様は、お使いのデータベース スキーマに準拠するように動的データ マスクの設定を調整する必要があります。

### <a name="identity-management"></a>ID 管理
次のテクノロジによって、Azure 環境に ID 管理機能が提供されます。
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) は、Microsoft が提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 このソリューションのすべてのユーザーは、Azure SQL Database にアクセスするユーザーも含めて、AAD 内に作成されます。
-   アプリケーションに対する認証は AAD を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)」をご覧ください。 さらに、データベース列の暗号化では、Azure SQL Database に対してアプリケーションを認証するために Azure Active Directory が使用されます。 詳細については、[SQL Database で機微なデータを保護する](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)方法に関するページを参照してください。
-   [Azure のロールベースのアクセス制御](../../role-based-access-control/role-assignments-portal.md) は、Azure の厳密に対象を絞ったアクセス管理を実現します。 サブスクリプションのアクセスはサブスクリプション管理者に制限され、リソースへのアクセスはユーザー ロールに基づいて制限できます。
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) により、特定の情報にアクセスできるユーザーの数を最小限に抑えることができます。  管理者は、AAD Privileged Identity Management を使用して、特権 ID と特権 ID によるリソースへのアクセスを検出、制限、および監視できます。 この機能を使用して、必要に応じて、オンデマンドの Just-In-Time 管理アクセスを適用することもできます。
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) は、組織の ID に影響を及ぼす可能性のある脆弱性を検出し、組織の ID に関連する検出された不審なアクションに対する自動応答を構成します。さらに、不審なインシデントを調査し、適切なアクションを実行してそれらを解決します。

### <a name="security"></a>セキュリティ
**シークレットの管理**: ソリューションでは、キーとシークレットの管理に [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を使用します。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 次の Azure Key Vault 機能は、データとそのようなデータへのアクセスを保護するために役立ちます。
- 必要に応じて、高度なアクセス ポリシーが構成されます。
- Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されます。
- Key Vault のすべてのキーとシークレットに有効期限があります。
- Key Vault 内のすべてのキーは、特殊なハードウェア セキュリティ モジュール (HSM) によって保護されます。 キーの種類は、HSM 保護された 2048 ビット RSA キーです。
- ロールベースのアクセス制御を使用して、すべてのユーザーと ID に最低限必要なアクセス許可が付与されます。
- Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
- キーに対して許可される暗号化操作は必要なものに制限されます。

**Application Gateway**: このアーキテクチャでは、OWASP ルールセットを有効にした、Web アプリケーション ファイアウォールがある Application Gateway を使用してセキュリティの脆弱性のリスクを軽減します。 その他の機能には次のものがあります。
- [エンド ツー エンド SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL オフロード](../../application-gateway/create-ssl-portal.md)の有効化
- [TLS v1.0 および v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) の無効化
- [Web アプリケーション ファイアウォール](../../application-gateway/waf-overview.md)
- OWASP 3.0 ルールセットを使用した[防止モード](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)
- [診断ログ](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)の有効化
- [カスタム正常性プローブ](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) と [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) による、その他の保護と通知の提供。 Azure Security Center は評価システムも提供します。

### <a name="logging-and-auditing"></a>ログ記録と監査
Azure Monitor は、システムの正常性だけではなく、システムとユーザーのアクティビティを詳細に記録します。 これは Azure やオンプレミス環境のリソースによって生成されるデータを収集して分析します。
- **[アクティビティ ログ]** :[アクティビティ ログ](../../azure-monitor/platform/activity-logs-overview.md)は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、状態の判断に役立ちます。
- **診断ログ**:[診断ログ](../../azure-monitor/platform/resource-logs-overview.md)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログ、Azure Storage ログ、Key Vault 監査ログ、および Application Gateway のアクセス ログとファイアウォール ログが含まれます。
- **ログのアーカイブ**:すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 リテンション期間には、組織固有の保有要件を満たすために最長 730 日までの日数をユーザーが設定できます。 これらのログは、処理、格納、およびダッシュボードでのレポート表示を行うために、Azure Monitor ログに接続されます。

さらに、このアーキテクチャの一部として、次の監視ソリューションが含まれます。
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md):Active Directory 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md):このマルウェア対策ソリューションでは、マルウェア、脅威、および保護の状態に関する報告を行います。
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker):Azure Automation ソリューションでは、Runbook の格納、実行、および管理を行います。 このソリューションでは、Runbook を使用して、Application Insights と Azure SQL Database からログを収集できます。
-   [Security and Audit](../../security-center/security-center-intro.md):Security and Audit ダッシュボードでは、セキュリティ ドメイン、注意が必要な問題、検出、脅威に関する知識、および一般的なセキュリティの照会に関するメトリックを提供することで、リソースのセキュリティ ステータスに対する高度な分析情報を提供します。
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md):この SQL 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧をお客様に提供します。
-   [Update Management](../../automation/automation-update-management.md):Update Management ソリューションでは、利用可能な更新プログラムのステータスや必要な更新プログラムのインストール プロセスなど、オペレーティング システムのセキュリティ更新プログラムをお客様が管理できるようにします。
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md):Agent Health ソリューションは、デプロイされたエージェント数とその地理的配置に加え、応答しないエージェント数やオペレーショナル データを送信しているエージェント数を報告します。
-   [Azure Activity Logs](../../azure-monitor/platform/collect-activity-logs.md):Activity Log Analytics ソリューションは、顧客向けのすべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。
-   [Change Tracking](../../automation/change-tracking.md):Change Tracking ソリューションでは、お客様が環境の変更を簡単に識別できるようにします。

**Azure Monitor**
: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) は、監査、アラートの作成、データのアーカイブ、および Azure リソース内の API 呼び出しの追跡を実行できるようにすることで、パフォーマンスの追跡、セキュリティの維持、および傾向の識別を実行できるようにします。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラムを[ダウンロード](https://aka.ms/fedrampPaaSWebAppDFD)するか、下記を参照してください。 このモデルは、修正を行う際に、お客様がシステム インフラストラクチャの潜在的なリスクを理解するために役立ちます。

![FedRAMP 脅威モデルのための PaaS Web アプリケーション](images/fedramp-paaswa-threat-model.png?raw=true "FedRAMP 脅威モデルのための PaaS Web アプリケーション")

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント
[Azure Security and Compliance Blueprint – FedRAMP High Customer Responsibility Matrix](https://aka.ms/blueprinthighcrm) (Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP の高度なベースラインでのお客様の責任に関するマトリックス) では、FedRAMP の高度なベースラインで必要とされるすべてのセキュリティ統制の一覧を示しています。 マトリックスには、各統制の実装が、マイクロソフト、お客様、または両者間での共有の責任のどれに該当するかが、詳細に示されています。

[Azure Security and Compliance Blueprint - FedRAMP PaaS WebApp High Control Implementation Matrix](https://aka.ms/fedrampPaaSWebAppCIM) (Azure のセキュリティとコンプライアンスのブループリント - FedRAMP PaaS WebApp の高度なコントロールの実装のマトリックス) は、FedRAMP の高度なベースラインで必要とされるすべてのセキュリティ統制の一覧を示しています。 マトリックスでは、実装がどのように管理対象の各統制の要件を満たすかについての詳細な説明を含め、PaaS Web アプリケーションのアーキテクチャで対応されている統制に関する情報を提供します。

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項
### <a name="vpn-and-expressroute"></a>VPN と ExpressRoute
この PaaS Web アプリケーションの参照アーキテクチャの一部としてデプロイされるリソースへの接続を安全に確立するために、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) またはセキュリティ保護された VPN トンネルを構成する必要があります。 ExpressRoute または VPN を適切に設定することで、お客様は転送中のデータに対して保護のレイヤーを追加できます。

Azure を使用してセキュリティ保護された VPN トンネルを実装することにより、オンプレミス ネットワークと Azure Virtual Network の間で VPN 接続を作成できます。 この接続はインターネットをまたいでいるため、お客様は自社ネットワークと Azure の間の暗号化されたリンクの "トンネル" を通して情報を送信できます。 サイト間 VPN は、数十年にわたってあらゆる規模の企業に導入されている安全で成熟したテクノロジです。 このオプションでは、暗号化メカニズムとして [IPsec トンネル モード](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))が使用されています。

VPN トンネル内のトラフィックはサイト間 VPN を使用してインターネット上を転送されるため、Microsoft は、さらに高いセキュリティで保護されたもう 1 つの接続オプションを提供します。 Azure ExpressRoute は、オンプレミスの場所または Exchange ホスティング プロバイダーと Azure の間に確立される専用 WAN リンクです。 ExpressRoute 接続はインターネットを経由しないため、これらの接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 さらに、これはお客様の通信プロバイダーの直接接続であり、データがインターネット上を移動しないため、インターネットに公開されることはありません。

オンプレミス ネットワークを Azure に拡張するセキュアなハイブリッド ネットワークを実装するためのベスト プラクティスが[提供されています](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="disclaimer"></a>免責事項

 - このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。
 - このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。
 - お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。
 - このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。
 - このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
 - このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
