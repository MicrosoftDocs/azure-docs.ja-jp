---
title: Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP の分析
description: Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP の分析
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 9850c5f064815315db6f85a931e7e175d605dcc1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257586"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Azure のセキュリティとコンプライアンスのブループリント:FedRAMP 用の分析

## <a name="overview"></a>概要

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) は、米国政府全体で実行されるプログラムであり、クラウド製品とサービスに対するセキュリティの評価、承認、および継続的な監視に対する標準化された手法を提供します。 この「Azure のセキュリティとコンプライアンスのブループリント」では、FedRAMP の高度なコントロールのサブセットを実装する Microsoft Azure の分析アーキテクチャを配信する方法についてガイダンスを示します。 このソリューションは、一般的なリファレンス アーキテクチャでの Azure リソースのデプロイと構成に関するガイダンスを示し、お客様が特定のセキュリティ要件と準拠要件をどのように満たすことができるかを示し、お客様が Azure 上に独自の分析ソリューションをビルドして構成するための基礎として機能します。

このリファレンス アーキテクチャ、関連するコントロール実装ガイド、および脅威モデルは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。 変更せずにこの環境にアプリケーションをデプロイすることは、FedRAMP High ベースラインの要件を完全に満たすためには不十分です。 以下の点に注意してください。
- このアーキテクチャは、お客様が FedRAMP に準拠した方法で Azure にワークロードをデプロイするためのベースラインを提供します。
- お客様は、このアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスの評価を実施する責任を負います。要件は、お客様の実装によって変化する可能性があるからです。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント

このソリューションでは分析プラットフォームを提供します。お客様はそのプラットフォーム上に独自の分析ツールをビルドできます。 リファレンス アーキテクチャは、SQL/データの管理者による一括データのインポートを利用するか、または操作ユーザーによるオペレーショナル データの更新を利用して、お客様がデータを入力する汎用的なユース ケースの枠組みとなります。 どちらの作業ストリームでも、SQL Database にデータをインポートするために、[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) を組み込みます。 それぞれのお客様独自の分析要件に固有のインポート タスクを処理するために、Azure Functions は、Azure Portal 経由でお客様によって構成される必要があります。

Microsoft Azure では、多様なレポート サービスおよび分析サービスをお客様に提供していますが、このソリューションでは、迅速にデータを参照して、お客様のデータのより高度なモデリングによって迅速に結果を配信するために、Azure SQL Database と連動する Azure Analysis Services を組み込みます。 Azure Analytics Services は、データセット間の新しいリレーションシップを見つけることで、クエリ速度を高めることを意図した機械学習の 1 つの形態です。 複数の統計関数を使ってデータがトレーニングされた後は、クエリ ワークロードを分散させて応答時間を削減するために、同じ表形式モデルを使って最大 7 個の追加のクエリ プール (顧客のサーバーを含めた場合、合計 8 個) を同期できます。

強化された分析およびレポートでは、SQL Database は列ストア インデックスを使って構成できます。 Azure Analytics Services および SQL Database は両方とも、お客様の用途に対応するために、スケールアップ、スケールダウン、または完全な切り離しが可能です。 SQL トラフィックはすべて、自己署名証明書を含めることによって、SSL で暗号化されます。 ベスト プラクティスとして、Azure では、セキュリティ強化のために信頼済みの証明書機関を利用することをお勧めします。

データが Azure SQL Database にアップロードされ Azure Analysis Services によってトレーニングされた後、そのデータは Power BI を利用する操作ユーザーおよび SQL/データ管理者の両方から使用されます。 Power BI はデータを直感的に表示し、複数のデータセットに及ぶ情報を同時にプルして、より優れた分析情報を描画します。 高度な適応性と Azure SQL Database との容易な連携によって、業務ニーズに応じた幅広いシナリオを処理するように構成できます。

ソリューション全体は、アカウントの顧客が Azure Portal から構成する Azure Storage 上にビルドされます。 Azure Storage は Storage Service Encryption を使用してすべてのデータを暗号化し、保存データの機密性を保持します。  geo 冗長ストレージ (GRS) は、数百マイルは離れた別の場所にセカンダリ コピーが格納されることで、お客様のプライマリ データ センターでの有害事象によってデータを損失することがないようにします。

セキュリティ強化のために、このアーキテクチャでは、Azure Active Directory と Azure Key Vault を使ってリソースを管理します。 システムの正常性は、Azure Monitor によって監視されます。 お客様が両方の監視サービスを構成してログをキャプチャし、簡単にナビゲートできる単一のダッシュボードにシステムの正常性を表示できます。

Azure SQL Database は一般的に、SQL Server Management Studio (SSMS) 経由で管理されます。SSMS は、セキュアな VPN または ExpressRoute 接続経由で Azure SQL Database にアクセスするように構成されたローカル コンピューターから実行されます。 **Azure では、参照アーキテクチャのリソース グループに対する管理とデータ インポートのために、VPN または Azure ExpressRoute 接続を構成することを推奨しています。**

![FedRAMP に対する分析の参照アーキテクチャの図](images/fedramp-analytics-reference-architecture.png?raw=true "FedRAMP に対する分析の参照アーキテクチャの図")

### <a name="roles"></a>ロール
分析のブループリントは、操作ユーザー、SQL/データ管理者、およびシステム エンジニエアという 3 種類の汎用ユーザーによるシナリオの枠組みを示します。 Azure Role-based Access Control (RBAC) は、組み込みのカスタム ロールを利用した厳密なアクセス管理の実装を可能にします。 リソースは、[Role-based Access Control](../../role-based-access-control/role-assignments-portal.md) の構成および[事前定義済みロール](../../role-based-access-control/built-in-roles.md)の枠組みの提示と実装に利用できます。

#### <a name="systems-engineer"></a>システム エンジニア
システム エンジニアは、Azure のお客様のサブスクリプションを所有し、Azure Portal を使用してソリューションのデプロイを構成します。

#### <a name="sqldata-administrator"></a>SQL/データ管理者
SQL/データ管理者は、Azure SQL データベースにアップロードするための、一括データ インポート機能とオペレーショナル データ更新機能を確立します。 SQL/データ管理者は、データベースでのオペレーショナル データの更新を担当しませんが、Power BI を通してデータを表示できます。

#### <a name="operational-user"></a>操作ユーザー
操作ユーザーは、データを定期的に更新して、日次でのデータ生成を保持します。 また、操作ユーザーは Power BI を使って結果を解釈します。

### <a name="azure-services"></a>Azure サービス

このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャの詳細については、「[デプロイ アーキテクチャ](#deployment-architecture)」セクションを参照してください。
- Azure Functions
- Azure SQL Database
- Azure Analysis Service
- Azure Active Directory
- Azure Key Vault
- Azure Monitor (ログ)
- Azure Storage
- ExpressRoute/VPN Gateway
- Power BI ダッシュボード

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ
以下のセクションでは、開発と実装の要素について詳しく説明します。

![alt テキスト](images/fedramp-analytics-components.png?raw=true "FedRAMP に対する分析のコンポーネントの図")

**Azure Functions**:[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) は、ほとんどのプログラミング言語を介してクラウドで小規模なコード部分を実行するためのソリューションです。 このソリューションにおける Functions は、Azure Storage と連動してお客様のデータを自動的にクラウドにプルし、他の Azure サービスとの連携を推進します。 Functions は簡単に拡張可能でき、実行されたときだけ料金が発生します。

**Azure Analysis Service**:[Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) は、エンタープライズ データのモデリングと、Azure データ プラットフォーム サービスとの統合を提供します。 Azure Analysis Service は、複数のソースのデータを単一のデータ モデルに統合することで、大量のデータの参照を高速化します。

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) は、データ処理作業によってさらに優れた分析情報を導き出そうとするお客様に対して、分析とレポート作成の機能を提供します。

### <a name="networking"></a>ネットワーク
**ネットワーク セキュリティ グループ**:[NSG](../../virtual-network/virtual-network-vnet-plan-design-arm.md) は、デプロイされたリソースやサービスに送信されるトラフィックを管理するために設定されます。 ネットワーク セキュリティ グループは、既定で拒否のスキームに設定され、事前構成された Access Control List (ACL) に含まれるトラフィックのみを許可します。

各 NSG では、ソリューションが安全かつ適切に機能できるように、固有のポートとプロトコルが開かれます。 さらに、各 NSG で次の構成が有効になります。
  - [診断ログとイベント](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)が有効化され、ストレージ アカウントに格納される
  - Azure Monitor ログが [NSG の診断](../../azure-monitor/insights/azure-networking-analytics.md)ログに接続される。

### <a name="data-at-rest"></a>保存データ
このアーキテクチャでは、暗号化、データベース監査などの手段によって保存データを保護します。

**データ レプリケーション** Azure Government には、[データ レプリケーション](https://docs.microsoft.com/azure/storage/common/storage-redundancy)のための 2 つのオプションがあります。
 - データ レプリケーションの既定の設定は **geo 冗長ストレージ (GRS)** であり、プライマリ リージョン以外にある別のデータ センターにお客様のデータを非同期で格納します。 これにより、プライマリ データ センターが完全に失われた場合のデータの復旧を保証します。
 - もう 1 つの**ローカル冗長ストレージ (LRS)** は、Azure Storage アカウント経由で構成できます。 LRS は、ストレージ スケール ユニット内でデータをレプリケートし、お客様が自身のアカウントを作成する同じリージョンでホストされます。 プライマリ ストレージのスケール ユニットの障害時にバックアップ データが失われることがないように、すべてのデータが同時にレプリケートされます。

**Azure Storage** 暗号化された保存データの要件を満たすために、この参照アーキテクチャにデプロイされたすべてのサービスが [Azure Storage](https://azure.microsoft.com/services/storage/) を活用し、[Storage Service Encryption](../../storage/common/storage-service-encryption.md) を使ってデータを格納します。

**Azure Disk Encryption**
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) は、Windows の BitLocker 機能を活用して、OS とデータ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Azure Key Vault と統合されています。

**Azure SQL Database** Azure SQL データベース インスタンスは、次のデータベース セキュリティ対策を使用します。
-   [AD 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)を使用すると、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
-   [SQL Database の監査](../../sql-database/sql-database-auditing.md)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
-   SQL Database は、[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用するように構成されます。TDE は、データとログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 TDE は、保存されているデータが未承認のアクセスの対象になっていないことを保証します。
-   [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
-   [SQL の脅威検出](../../sql-database/sql-database-threat-detection.md)は、不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンに対するセキュリティの警告を提供することで、潜在的な脅威の検出とそれらの脅威への対応を可能にします。
-   [Always Encrypted の列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)により、データベース システム内で機密データがプレーンテキストとして表示されることはありません。 データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
-   [SQL Database 動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)は、参照アーキテクチャがデプロイされた後に実行できます。 お客様は、お使いのデータベース スキーマに準拠するように動的データ マスクの設定を調整する必要があります。

### <a name="logging-and-audit"></a>ログ記録と監査
[Azure Monitor](../../azure-monitor/overview.md) は、アクティビティ ログ、メトリック、および診断データなどのデータを監視する総合的な表示画面を生成し、お客様によるシステム正常性の全体像の作成を可能にします。  
[Azure Monitor ログ](../azure-security-disk-encryption-overview.md)は、システムの正常性だけではなく、システムとユーザーのアクティビティを詳細に記録します。 これは Azure やオンプレミス環境のリソースによって生成されるデータを収集して分析します。
- **アクティビティ ログ**: [アクティビティ ログ](../../azure-monitor/platform/activity-logs-overview.md)は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。
- **診断ログ**: [診断ログ](../../azure-monitor/platform/resource-logs-overview.md)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログと、Azure Blob Storage、テーブル、キューのログが含まれます。
- **ファイアウォール ログ**: Application Gateway は、完全な診断ログとアクセス ログを提供します。 ファイアウォール ログは、WAF が有効になっている Application Gateway リソースで使用できます。
- **ログ アーカイブ**: すべての診断ログは、暗号化された集中型の Azure ストレージ アカウントに書き込まれ、定義済みのリテンション期間である 2 日間にわたってアーカイブされるように構成されます。 これらのログは、処理、格納、およびダッシュボードでのレポート表示を行うために、Azure Monitor ログに接続されます。

さらに、このアーキテクチャの一部として、次の監視ソリューションが含まれます。
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker):Azure Automation ソリューションは、Runbook の格納、実行、および管理を行います。
-   [Security and Audit](../../security-center/security-center-intro.md):Security and Audit ダッシュボードでは、セキュリティ ドメイン、注意が必要な問題、検出、脅威に関する知識、および一般的なセキュリティの照会に関するメトリックを提供することで、リソースのセキュリティ ステータスに対する高度な分析情報を提供します。
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md):SQL 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧をお客様に提供します。
-   [Azure Activity Logs](../../azure-monitor/platform/collect-activity-logs.md):Activity Log Analytics ソリューションは、顧客向けのすべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。

### <a name="identity-management"></a>ID 管理
-   アプリケーションに対する認証は Azure AD を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)」をご覧ください。 さらに、データベース列の暗号化では、アプリケーションを Azure SQL Database に対して認証するために Azure AD が使用されます。 詳細については、[SQL Database で機密データを保護する](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)方法に関するページを参照してください。
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) は、組織の ID に影響を及ぼす可能性のある脆弱性を検出し、組織の ID に関連する検出された不審なアクションに対する自動応答を構成します。さらに、不審なインシデントを調査し、適切なアクションを実行してそれらを解決します。
-   [Azure のロールベースのアクセス制御 (RBAC)](../../role-based-access-control/role-assignments-portal.md) は、Azure の対象を絞ったアクセス管理を実現します。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。

Azure SQL Database のセキュリティ機能の使用方法の詳細については、「[Contoso Clinic デモ アプリケーション](https://github.com/Microsoft/azure-sql-security-sample)」サンプルをご覧ください。

### <a name="security"></a>セキュリティ
**シークレットの管理**:ソリューションでは、キーとシークレットの管理に [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) が使用されます。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項

### <a name="expressroute-and-vpn"></a>ExpressRoute と VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) またはセキュリティ保護された VPN トンネルは、デプロイされるリソースへの接続を安全に確立するために、この Data Analytics 参照アーキテクチャの一部として構成される必要があります。 ExpressRoute 接続はインターネットを経由しないため、これらの接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 ExpressRoute または VPN を適切に設定することで、お客様は転送中のデータに対して保護のレイヤーを追加できます。

### <a name="azure-active-directory-setup"></a>Azure Active Directory の設定
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) は、デプロイの管理と環境を操作する担当者へのアクセスのプロビジョニングを行うために不可欠です。 既存の Windows Server Active Directory は、[4 回のクリック](../../active-directory/hybrid/how-to-connect-install-express.md)で AAD と統合できます。 また、デプロイされた Active Directory インフラストラクチャを AAD フォレストのサブドメインにすることで、デプロイされた Active Directory インフラストラクチャ (ドメイン コントローラー) を既存の AAD に連結できます。

### <a name="additional-services"></a>その他のサービス
#### <a name="iaas---vm-considerations"></a>IaaS - VM に関する考慮事項
この PaaS ソリューションには、Azure IaaS VM は組み込まれません。 お客様は、これらの PaaS サービスの多くを実行する Azure VM を作成できます。 この場合、ビジネス継続性と Azure Monitor ログに対して、次のような特定の機能とサービスを活用できます。

##### <a name="business-continuity"></a>ビジネス継続性
- **高可用性**: サーバー ワークロードは、Azure の仮想マシンの高可用性を確保するために[可用性セット](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にグループ化されます。 Azure SLA を 99.95% 満たすように、計画または計画外メンテナンス イベントの間に、少なくとも 1 つの仮想マシンが利用可能です。

- **Recovery Services コンテナー**: [Recovery Services コンテナー](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)にはバックアップ データが保管され、このアーキテクチャ内の Azure 仮想マシンのすべての構成が保護されます。 Recovery Services コンテナーを使用すると、VM 全体を復元せずに IaaS VM からファイルとフォルダーを復元できるため、復元時間を短縮できます。

##### <a name="monitoring-solutions"></a>監視ソリューション
-   [AD Assessment](../../azure-monitor/insights/ad-assessment.md):この Active Directory 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md):このマルウェア対策ソリューションでは、マルウェア、脅威、および保護の状態に関する報告を行います。
-   [Update Management](../../automation/automation-update-management.md):Update Management ソリューションでは、利用可能な更新プログラムのステータスや必要な更新プログラムのインストール プロセスなど、オペレーティング システムのセキュリティ更新プログラムをお客様が管理できるようにします。
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md):Agent Health ソリューションは、デプロイされたエージェント数とその地理的配置に加え、応答しないエージェント数やオペレーショナル データを送信しているエージェント数を報告します。
-   [Change Tracking](../../automation/change-tracking.md):Change Tracking ソリューションでは、お客様が環境の変更を簡単に識別できるようにします。

##### <a name="security"></a>セキュリティ
- **マルウェア対策**: 仮想マシン向けの [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) は、ウイルスやスパイウェアなどの悪意のあるソフトウェアを識別して削除するリアルタイム保護機能を提供し、既知のマルウェアや不要なソフトウェアが保護されている仮想マシンへのインストールまたは実行を試みた場合に警告する構成可能なアラートを備えています。
- **更新プログラムの管理**: この参照アーキテクチャの一部としてデプロイされる Windows 仮想マシンは、既定で Windows Update サービスから自動的に更新プログラムを受け取るように構成されます。 また、このソリューションには、[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) サービスが含まれます。このサービスによって、必要に応じて仮想マシンにパッチを適用するために、更新されたデプロイが作成される場合があります。

#### <a name="azure-commercial"></a>Azure Commercial
この Data Analytics アーキテクチャは[Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) 環境へのデプロイを意図していませんが、この参照アーキテクチャに示されたサービスと、Azure Commercial 環境だけで使用できる追加のサービスを利用して、ほぼ同じ目的を実現できます。 Azure Commercial では、中程度の影響レベルで FedRAMP JAB P-ATO を維持し、官庁やパートナーが Azure Commercial 環境を活用しているクラウドに中程度の機密情報をデプロイできるようにしています。

Azure Commercial は、大量のデータから分析情報を導き出すために、幅広い分析サービスを提供しています。
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio)は [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/) のコンポーネントであり、1 つまたは複数のデータ ソースから予測分析モデルを開発します。 統計関数は、Power BI などのアプリケーションで使用できる効果的なモデルを生成するために、複数のイテレーションにわたって使用されます。
-   [Azure Data Lake Store ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) を使用すると、運用分析や調査分析を目的として任意のサイズ、種類、および取り込み速度のデータを 1 か所でキャプチャすることができます。 Azure Data Lake Store は、Hadoop のエコシステムのほとんどのオープン ソース コンポーネントと共存でき、他の Azure サービスと問題なく連携します。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラム (DFD) を[ダウンロード](https://aka.ms/blueprintanalyticsthreatmodel)できます。または下記を参照してください。

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント
[Azure Security and Compliance Blueprint – FedRAMP High Customer Responsibility Matrix](https://aka.ms/blueprinthighcrm) (Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP の高度なベースラインでのお客様の責任に関するマトリックス) では、FedRAMP の高度なベースラインで必要とされるすべてのセキュリティ コントロールの一覧を示しています。 同様に、[Azure Security and Compliance Blueprint – FedRAMP Moderate Customer Responsibility Matrix](https://aka.ms/blueprintanalyticscimmod) (Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP の中程度のベースラインでのお客様の責任に関するマトリックス) では、FedRAMP の中程度のベースラインで必要なすべてのセキュリティ コントロールの一覧を示しています。 どちらのドキュメントにも、各コントロールの実装がマイクロソフト、お客様、または両者間での共有の責任のどれに該当するかが、詳細に示されています。

[Azure Security and Compliance Blueprint - FedRAMP High Control Implementation Matrix](https://aka.ms/blueprintanalyticscimhigh) (Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP の高度なコントロールでの実装に関するマトリックス) および [Azure Security and Compliance Blueprint - FedRAMP Moderate Control Implementation Matrix](https://aka.ms/blueprintanalyticscimmod) (Azure のセキュリティとコンプライアンスのブルー プリント - FedRAMP の中程度のコントロールでの実装に関するマトリックス) では、対象となる各コントロールの要件に実装がどの程度見合っているかの詳細な説明を含め、各 FedRAMP ベースラインの分析アーキテクチャの対象となるコントロールに関する情報を提供しています。

## <a name="disclaimer"></a>免責事項

 - このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。
 - このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。
 - お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。
 - このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。
 - このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
 - このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
