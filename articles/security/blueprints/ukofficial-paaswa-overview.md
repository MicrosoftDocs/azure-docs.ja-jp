---
title: Azure のセキュリティとコンプライアンスのブループリント - UK OFFICIAL ワークロード向け PaaS Web アプリケーション ホスティング
description: Azure のセキュリティとコンプライアンスのブループリント - UK OFFICIAL ワークロード向け PaaS Web アプリケーション ホスティング
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: 1f6eeea85a348bb8e88a387fa0fc6bed55e41a5e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262784"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Azure のセキュリティとコンプライアンスのブループリント:UK OFFICIAL ワークロード向け PaaS Web アプリケーション ホスティング

## <a name="azure-security-and-compliance-blueprints"></a>Azure のセキュリティとコンプライアンスのブループリント

Azure Blueprint は、認定またはコンプライアンスの要件があるシナリオにソリューションを提供するために、クラウド ベースのアーキテクチャをデプロイするガイダンス ドキュメントと自動化テンプレートで構成されています。 Azure Blueprints は、ガイダンスと自動化テンプレートのコレクションです。Microsoft Azure をご利用のお客様は、さらに高度な要件に合わせて拡張可能な基盤アーキテクチャをプロビジョニングすることで、ビジネス目標を早期に実現できるようになります。

## <a name="overview"></a>概要

この Azure のセキュリティとコンプライアンスのブループリントには、[UK OFFICIAL](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf) に分類されたワークロードを処理するために適した、Microsoft Azure [PaaS (サービスとしてのプラットフォーム)](https://azure.microsoft.com/overview/what-is-paas/) ホステッド Web アプリケーション アーキテクチャを提供するガイダンスと自動化スクリプトが用意されています。 このセキュリティ分類は、公的機関によって作成または処理される情報の大部分を網羅しています。 これには、紛失、盗難、またはメディアでの公表により、損害が発生する可能性のある日常的な業務とサービスが含まれます。 OFFICIAL 分類の一般的な脅威プロファイルは、重要な情報とサービスを提供する民間企業とほぼ同じです。 UK OFFICIAL は、能力とリソースが制限された攻撃者による脅威や侵害から英国政府機関のデータやサービスを保護する必要性を予測しています。攻撃者は、ハクティビスト、単一争点の圧力団体、調査ジャーナリスト、高度な能力を持つ個人ハッカー、そして多数の犯罪者やグループなどです。

このブループリントは、英国国立サイバー セキュリティ センター (NCSC) によってレビューされ、NCSC 14 クラウド セキュリティ原則に適合しています。

このアーキテクチャは、Azure [PaaS (サービスとしてのプラットフォーム)](https://azure.microsoft.com/overview/what-is-paas/) コンポーネントを使用しています。これによって、ソフトウェア ライセンスの購入、基礎となるアプリケーション インフラストラクチャの管理、ミドルウェアまたは開発ツールの管理、およびその他のリソースの費用と複雑さを回避することができます。 お客様は、自社で開発したアプリケーションとサービスを管理し、ビジネス価値の実現に集中できます。一方、Microsoft Azure が仮想マシン、ストレージ、ネットワークなどの Azure リソースを管理するため、インフラストラクチャ管理の[責任分担](../fundamentals/paas-deployments.md)は Azure プラットフォームの方が重くなります。 [Azure App Services](https://azure.microsoft.com/services/app-service/) では、自動スケール、高可用性が実現されるほか、Windows と Linux がサポートされています。さらに、既定のサービスとして GitHub、Azure DevOps、または任意の Git リポジトリからの自動デプロイが可能になります。 App Services を使用すると、インフラストラクチャ管理のオーバーヘッドがないので、開発者はビジネス価値の実現に集中できます。 画期的な Java、PHP、Node.js、Python、HTML、または C# Web アプリケーションを構築することも、既存のクラウドまたはオンプレミス Web アプリケーションを Azure App Services に移行することもできます (ただし、パフォーマンスを確認するための徹底的なデュー デリジェンスとテストは必要です)。

このブループリントは、公的機関およびバックオフィスのユーザー向けに、セキュリティで保護された基盤である[サービスとしてのプラットフォーム](https://azure.microsoft.com/overview/what-is-paas/)の Web ベース インターフェイスを提供することに焦点を当てています。 このブループリントの設計シナリオでは、パブリック ユーザーが機密データを安全に提出、表示、管理できる、また、バック オフィスや政府機関のオペレーターがパブリック ユーザーが提出した機密データを安全に処理できる、Azure ホステッド Web ベース サービスの使用を考慮しています。 このシナリオには、次のようなユース ケースがあります。

- 納税申告書を提出するユーザーと、提出を処理する政府機関のオペレーター。
- Web ベースのアプリケーションを介してサービスを要求するユーザーと、そのサービスを検証および提供するバックオフィス ユーザー。または
- 政府機関のサービスに関するパブリック ドメイン ヘルプ情報を探して閲覧するユーザー。

このブループリントは、[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) テンプレートと Azure Command Line Interface スクリプトを使用して、英国立サーバー セキュリティ センター (NCSC) の 14 の[クラウド セキュリティに関する原則](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)と Center for Internet Security (CIS) の[重要なセキュリティ コントロール](https://www.cisecurity.org/critical-controls.cfm)に準拠する環境をデプロイします。 NCSC は、顧客がそのクラウド セキュリティに関する原則を使用してサービスのセキュリティ プロパティを評価し、顧客とサプライヤー間での責任分担の理解に役立てるよう推奨しています。 Microsoft では、お客様が責任の分担をより理解できるように、これらの各原則に関する情報を提供しています。 このアーキテクチャおよび対応する Azure Resource Manager テンプレートは、Microsoft ホワイト ペーパー「[14 Cloud Security Controls for UK cloud Using Microsoft Azure (Microsoft Azure を使用した英国クラウド向けの 14 のクラウド セキュリティ コントロール)」](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1)でサポートされています。 このアーキテクチャは NCSC によってレビューされています。また、英国 NCSC の 14 のクラウド セキュリティに関する原則に準拠しています。そのため、公的機関は、グローバルに、また英国で Microsoft Azure クラウドのクラウドベース サービスを使用してコンプライアンス義務を果たす能力を迅速に実現できます。 このテンプレートは、ワークロードのインフラストラクチャをデプロイします。 お客様は、アプリケーション コードとビジネス層およびデータ層のサポート ソフトウェアをインストールし、構成する必要があります。 詳しいデプロイ手順については、[こちら](https://aka.ms/ukofficial-paaswa-repo/)をご覧ください。

このブループリントは基盤のアーキテクチャです。 お客様は、OFFICIAL 分類の Web ベースの ワークロードの基礎としてこのブループリントを使用し、それぞれの要件に合わせてテンプレートとリソースを拡張することができます。 このブループリントは、Web ベースのワークロードをホスティングする [IaaS (サービスとしてのインフラストラクチャ)](https://azure.microsoft.com/overview/what-is-iaas/) と PaaS 両方の実装の選択肢をお客様に提供するために、[UK-OFFICAL Three-Tier IaaS Web Applications ブループリント](https://aka.ms/ukofficial-iaaswa)の原則に基づいて構築されています。

このブループリントをデプロイするには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、無料ですばやく簡単にサインアップできます:Azure を使ってみる。 デプロイ手順については、[ここ](https://aka.ms/ukofficial-paaswa-repo/)をクリックしてください。

## <a name="architecture-and-components"></a>アーキテクチャとコンポーネント

このブループリントでは、UK OFFICIAL ワークロードをサポートする Web アプリケーション ホスティング ソリューションを Azure クラウド環境で提供しています。 このアーキテクチャは、サービス機能として Azure プラットフォームを活用するセキュリティで保護された環境を実現しています。 この環境内には、2 つの App Service Web アプリ (パブリック ユーザー用とバックオフィス ユーザー用) と、Web フロント エンド向けのビジネス サービスを提供する API アプリケーション層がデプロイされています。 Azure SQL Database は、アプリケーションのマネージド リレーショナル データ ストアとしてデプロイされています。 このプラットフォームの外部からこれらのコンポーネントへの接続と、これらすべてのコンポーネント間の接続は、TLS 1.2 で暗号化され、Azure Active Directory によって認証されたアクセス権を使用することで、送信時のデータの機密性を保護しています。

![UK OFFICIAL ワークロード向け PaaS Web アプリケーション ホスティングの参照アーキテクチャ図](images/ukofficial-paaswa-architecture.png?raw=true "UK OFFICIAL ワークロード向け PaaS Web アプリケーション ホスティングの参照アーキテクチャ図")

デプロイ アーキテクチャの一環として、セキュリティで保護されたストレージのプロビジョニング、監視およびログ記録、統一されたセキュリティ管理と高度な脅威防止、管理機能もデプロイされており、このソリューションの環境をセキュリティで保護し、監視するために必要なすべてのツールが揃っています。

このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャの詳細については、「[デプロイ アーキテクチャ](#deployment-architecture)」セクションを参照してください。

- Azure Active Directory
- App Service
- Web アプリ
- API アプリ
- Azure DNS
- Key Vault
- Azure Monitor (ログ)
- Application Insights
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ

次のセクションで、デプロイと実装の要素について詳しく説明します。

### <a name="security"></a>セキュリティ

#### <a name="identity-and-authentication"></a>ID と認証

このブループリントは、ディレクトリおよび ID 管理サービスを使用してリソースへのアクセスを確実に保護しています。 このアーキテクチャは、[セキュリティ境界としての ID](../fundamentals/paas-deployments.md) をフルに活用しています。 

次のテクノロジによって、Azure 環境に ID 管理機能が提供されます。

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID の管理サービスです。 ソリューションのすべてのユーザー (SQL Database にアクセスするユーザーを含む) は、Azure Active Directory で作成されています。
- Web アプリケーションを使用するオペレーターと Azure リソースの管理機能へのアクセスに対する認証には Azure AD が使用されます。 詳細については、「[Azure Active Directory とアプリケーションの統合](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)」をご覧ください。
- データベース列の暗号化では、アプリケーションを Azure SQL Database に対して認証するために Azure AD が使用されます。 詳細については、[Always Encrypted:SQL データベースの機密データの保護](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)に関するページを参照してください。
- パブリック アクセス用に、Web アプリケーションを使用する一般市民が構成されています。 Active Directory またはソーシャル ネットワーク ID プロバイダーを介してアカウントの作成と認証を実行する場合、必要に応じて [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) を統合できます。
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) は、脆弱性と危険なアカウントを検出し、組織の ID のセキュリティ体制を強化する推奨事項を提供し、組織の ID に関連する疑わしいアクションに対する自動応答を構成します。さらに、疑わしいインシデントを調査し、適切なアクションを実行してそれらを解決します。
- [Azure のロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) は、Azure の厳密に対象を絞ったアクセス管理を実現します。 サブスクリプションへのアクセスはサブスクリプション管理者に制限され、Azure Key Vault へのアクセスは、重要な管理アクセス権が必要なユーザーのみに制限されます。
- [Azure Active Directory Conditional Access](../../active-directory/active-directory-conditional-access-azure-portal.md) を利用することで、場所、デバイス、状態、サインインのリスクなどの特定の条件に基づいて、環境内のアプリまたはユーザーへのアクセスに対するセキュリティ管理を強化することができます。
- [Azure DDoS Protection](../fundamentals/paas-deployments.md#security-advantages-of-a-paas-cloud-service-model) とアプリケーション設計のベスト プラクティスを組み合わせることで、DDoS 攻撃から防御し、トラフィックの常時監視、一般的なネットワークレベル攻撃のリアルタイムの軽減を実現できます。 PaaS アーキテクチャの場合、プラットフォーム レベルの DDoS 保護はお客様からは見えない機能であり、プラットフォームに組み込まれていますが、アプリケーションのセキュリティ設計の責任はお客様にある点にご注意ください。

#### <a name="data-in-transit"></a>転送中のデータ

外部から転送されるデータと Azure コンポーネント間で転送されるデータは、[トランスポート層セキュリティ/Secure Sockets Layer (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption) を使用して保護されており、ネットワーク経由の通信は、共有シークレットに基づく対称暗号を使用して暗号化されます。 既定では、ネットワーク トラフィックは TLS 1.2 を使用してセキュリティで保護されています。

#### <a name="security-and-malware-protection"></a>セキュリティとマルウェア対策

[Azure Security Center](https://azure.microsoft.com/services/security-center/) では、すべての Azure リソースのセキュリティの状態を一元的に把握できます。 セキュリティの制御が適切かつ正しく構成されているかを一目で確認し、注意が必要なリソースを素早く特定できます。

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) は、ベスト プラクティスに従って Azure デプロイメントを最適化できるようにする、個人用に設定されたクラウド コンサルタントです。 Azure のリソースの構成と利用統計情報を分析し、Azure リソースの費用対効果、パフォーマンス、高可用性、およびセキュリティを向上させるために役立つソリューションを推奨します。

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) は、ウイルス、スパイウェアなどの悪意のあるソフトウェアの特定や駆除に役立つリアルタイムの保護機能です。 この機能は、既定で基礎となる PaaS 仮想マシン インフラストラクチャにインストールされます。Azure のファブリックによって管理されるので、ユーザーが意識することはありません。

### <a name="paas-services-in-this-blueprint"></a>このブループリントの PaaS サービス

#### <a name="azure-app-service"></a>Azure App Service

Azure App Service は、Java、PHP、Node.js Python、HTML、および C# で開発された Web アプリケーション向けにフル マネージド Web ホスティング環境を用意しています。インフラストラクチャを管理する必要はありません。 自動スケールと高可用性が実現されるほか、Windows と Linux の両方がサポートされています。さらに、[Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/)または任意の Git ベースのリポジトリからの自動デプロイが可能になります。

App Service は [ISO、SOC、および PCI](https://www.microsoft.com/TrustCenter/) に準拠しています。また、ユーザー認証には、[Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) またはソーシャル ログイン ([Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google)、[Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook)、[Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)、および [Microsoft 認証](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)) を使用できます。

Basic、Standard、および Premium プランは運用ワークロード向けで、専用の仮想マシン インスタンスで稼働します。 各インスタンスでは、複数のアプリケーションとドメインがサポートされます。 App Service は、必要に応じて信頼できる IP アドレスへのトラフィックをセキュリティで保護するために、[IP アドレス制限](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)もサポートしています。また、[Key Vault](https://azure.microsoft.com/services/key-vault/) や [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) などの PaaS サービスに安全に接続するために、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/app-service/overview-managed-identity) もサポートしています。 さらに高度なセキュリティが必要な場合は Isolated プランをご利用ください。このプランは、プライベートな専用の Azure 環境内のアプリをホストするため、オンプレミスのネットワークとのセキュリティ保護された接続や、追加のパフォーマンスおよびスケールが必要なアプリに最適です。

このテンプレートは、次の App Service 機能をデプロイします。

- [Standard](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) App Service プラン レベル
- 複数の Azure App Service [デプロイ スロット](https://docs.microsoft.com/azure/app-service/deploy-staging-slots):開発、プレビュー、QA、UAT、そして当然ながら運用 (既定スロット)。
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) に接続するための [Azure リソースのマネージド ID](https://docs.microsoft.com/azure/app-service/overview-managed-identity) ([Azure SQL Database](https://azure.microsoft.com/services/sql-database/) へのアクセスを提供する場合にも使用できます) 
- パフォーマンスを監視する [Azure Application Insights](../../azure-monitor/app/azure-web-apps.md) との統合
- [診断ログ](../../azure-monitor/platform/resource-logs-overview.md) 
- メトリック [アラート](../../azure-monitor/app/alerts.md) 
- [Azure API Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL Database

SQL Database は、リレーショナル データ、JSON、空間、XML などの構造をサポートする、Microsoft Azure における汎用リレーショナル データベース管理サービスです。 SQL Database には、マネージド シングル SQL データベース、[エラスティック プール](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)内のマネージド SQL データベース、および SQL [マネージド インスタンス](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) (パブリック プレビュー) が用意されています。 それは、[動的にスケーラブルなパフォーマンス](../../sql-database/sql-database-purchase-models.md)を提供し、徹底的な解析的分析とレポートを行うための[列ストア インデックス](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview)や、極度のトランザクション処理を行うための[インメモリ OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) などのオプションを備えています。 SQL コード ベースに対するパッチの適用と更新を Microsoft がすべてシームレスで処理するため、基になるインフラストラクチャの管理はすべて不要になります。

このブループリントの Azure SQL Database

Azure SQL データベース インスタンスは、次のデータベース セキュリティ対策を使用します。

- [サーバー レベルおよびデータベース レベルのファイアウォール ルール](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)、または[仮想ネットワーク規則](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)を使用した[仮想ネットワーク サービス エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
- [透過的なデータ暗号化](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)によって、悪意のあるアクティビティの脅威から Azure SQL Database と Azure Data Warehouse の保護を支援します。 データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、アプリケーションに変更を加えずに暗号化を行うことができます。
- [Azure AD 認証](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。 ID の一元管理では、1 か所でデータベース ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。
- データベース管理のための Azure Active Directory の使用
- ストレージ アカウントに対する[監査ログ](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)
- 失敗した DB 接続のメトリック [アラート](../../azure-monitor/app/alerts.md)
- [SQL の脅威の検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Always Encrypted 列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure Storage

Microsoft [Azure Storage](https://azure.microsoft.com/services/storage/) は、高い可用性とセキュリティ、耐久性、スケーラビリティ、冗長性を備えたストレージを提供する、Microsoft が管理するクラウド サービスです。 Azure Storage は、Blob Storage、File Storage、Queue Storage で構成されます。

#### <a name="azure-storage-in-this-blueprint"></a>このブループリントの Azure Storage

このテンプレートは、次の Azure Storage コンポーネントを使用しています。

- [ストレージ サービスの暗号化](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- HTTPS 接続のみ許可

#### <a name="data-at-rest"></a>保存データ

Azure Storage に書き込まれるすべてのデータは、[Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) により、現在利用できるブロック暗号化の中でも最強レベルの 256 ビット AES 暗号化によって暗号化されています。 SSE には Microsoft が管理する暗号化キーのほか、[ユーザー独自の暗号化キー](../../storage/common/storage-encryption-keys-portal.md)を使うことができます。

ストレージ アカウントは、[仮想ネットワーク規則](https://docs.microsoft.com/azure/storage/common/storage-network-security)を使用し、[仮想ネットワーク サービス エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)を介してセキュリティで保護することができます。

Azure Storage のセキュリティ保護の詳細については、[セキュリティ ガイド](https://docs.microsoft.com/azure/storage/common/storage-security-guide)に関するページを参照してください。


### <a name="secrets-management"></a>シークレットの管理

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) は、第三者がアクセスできないように、アプリケーション キーとシークレットをセキュリティで保護するために使用されます。 Key Vault は、ユーザー パスワードの保管場所として使用するためのものではありません。 Key Vault を使用すると、コンテナーと呼ばれるセキュリティで保護されたコンテナーを複数作成できます。 これらのコンテナーは、ハードウェア セキュリティ モジュール (HSM) に基づいています。 コンテナーでは、アプリケーション シークレットを一元的に保管することで、セキュリティ情報が過って失われる可能性は低くなります。 さらに、キー コンテナーでは、その中に格納されているすべての情報へのアクセスを制御し、記録します。 Azure Key Vault は、トランスポート層セキュリティ (TLS) 証明書の要求と更新を処理でき、堅牢な証明書ライフサイクル管理ソリューションに必要な機能を提供します。

#### <a name="azure-key-vault-in-this-blueprint"></a>このブループリントの Azure Key Vault

- ストレージ アクセス キーを保持し、Web アプリを使用するユーザーの[マネージド ID](https://docs.microsoft.com/azure/app-service/overview-managed-identity) に読み取りアクセス権を付与します。
- SQL Server DBA のパスワードを (別のコンテナー内に) 保持します。
- 診断ログ

### <a name="monitoring-logging-and-audit"></a>監視、ログ、監査

#### <a name="azure-monitor-logs"></a>Azure Monitor ログ

[Azure Monitor ログ](https://azure.microsoft.com/services/log-analytics/)は Azure のサービスであり、ご利用のクラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータを収集して分析するのに役立ちます。

#### <a name="azure-monitor-logs-in-this-blueprint"></a>このブループリントの Azure Monitor ログ

- SQL の評価
- Key Vault の診断
- Application Insights の接続
- [Azure Activity Log (Azure アクティビティ ログ)]

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 このサービスは、Web アプリケーションをリアルタイムで監視するために使用され、パフォーマンスの異常を自動的に検出する機能、パフォーマンスの分析機能、問題の診断機能があり、ユーザーのアプリの操作方法を理解できます。 Application Insights は、.NET、Node.js、Java EE など、オンプレミスまたはクラウドでホストされているプラットフォームにデプロイできます。 DevOps プロセスと統合され、さまざまなツールへの接続ポイントを備えています。

#### <a name="application-insights-in-this-blueprint"></a>このブループリントの Application Insights

このテンプレートは、次の Application Insights コンポーネントを使用します。

- サイトごとの Application Insights ダッシュボード (オペレーター、顧客、および API)

#### <a name="azure-activity-logs"></a>Azure アクティビティ ログ

[Azure Activity Log](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) は、サブスクリプションのコントロール プレーン イベントを監査します。 アクティビティ ログを使用すると、サブスクリプションのリソースに対して発生する書き込み操作 (PUT、POST、DELETE) すべてについて、"いつ誰が何を" 行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。

#### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../../azure-monitor/overview.md) は、Azure サービスのコアな監視用に、メトリック、アクティビティ ログ、および診断ログを収集します。 Azure Monitor では、Microsoft Azure のほとんどのサービスに対して、基礎レベルのインフラストラクチャのメトリックとログを提供します。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラムを[ダウンロード](https://aka.ms/ukofficial-paaswa-tm)するか、下記を参照してください。 このモデルは、修正を行う際に、お客様がシステム インフラストラクチャの潜在的なリスクを理解するために役立ちます。

![UK OFFICIAL ワークロード向け PaaS Web アプリケーション ホスティングの脅威モデル](images/ukofficial-paaswa-threat-model.png?raw=true "UK OFFICIAL ワークロード向け PaaS Web アプリケーション ホスティングの脅威モデル")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC クラウド セキュリティに関する原則のコンプライアンス ドキュメント

Crown Commercial Service (政府による商業および調達アクティビティの向上に努める機関) は、Microsoft の対象となるエンタープライズ クラウド サービスの分類を OFFICIAL レベルのすべてのサービスをカバーする G-Cloud v6 に更新しました。 Azure と G-Cloud について詳しくは、[Azure UK G-Cloud セキュリティ評価概要](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud)に関するページをご覧ください。

このブループリントは、NCSC [クラウド セキュリティに関する原則](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)で説明されている 14 のクラウド セキュリティに関する原則に準拠し、UK-OFFICIAL として分類されたワークロードをサポートする環境の実現を支援します。

[Azure のセキュリティとコンプライアンスのブループリント - お客様の責任マトリックス](https://aka.ms/ukofficial-crm) (Excel ブック) には 14 のクラウド セキュリティに関する原則がすべて示されており、各原則 (または原則のサブパート) について、原則の実装の責任を追うのが Microsoft、お客様、または双方の共同責任のいずれかであるかを示します。

[Azure のセキュリティとコンプライアンスのブループリント - 原則実装マトリックス](https://aka.ms/ukofficial-paaswa-pim) (Excel ブック) には、14 のクラウド セキュリティに関する原則がすべて示されており、お客様の責任マトリックスでお客様の責任と指定された各原則 (または原則のサブパート) について、1) ブループリントが原則を実行しているかどうか、および 2) 実装が原則要件に準拠する方法の説明を示します。  

さらに Cloud Security Alliance (CSA) は、クラウド プロバイダーの評価でお客様をサポートし、クラウド サービスへの移行前に回答する必要がある質問を識別するために、Cloud Control Matrix を発行しました。 これに対し、Microsoft Azure は CSA Consensus Assessment Initiative Questionnaire ([CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)) に回答し、Microsoft が提示された原則に対処する方法を説明しました。

## <a name="third-party-assessment"></a>サードパーティの評価

このブループリントは、英国国立サイバー セキュリティ センター (NCSC) によってレビューされ、NCSC 14 クラウド セキュリティ原則に適合しています。

自動化テンプレートは、英国の Customer Success Unit Azure Cloud Solution Architect チームと Microsoft パートナーの [Ampliphae](https://www.ampliphae.com/) によってテストされています。


## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

この Azure のセキュリティとコンプライアンスのブループリント自動化は、Azure Resource Manager の API サービスが Azure 内にリソースをデプロイするために処理する JSON 構成ファイルと PowerShell スクリプトで構成されています。 詳しいデプロイ手順については、[こちら](https://aka.ms/ukofficial-paaswa-repo)をご覧ください。

デプロイには 3 つのアプローチが用意されています。シンプルな "express (高速)" バージョンの [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) は、迅速にテスト環境を構築するために適しています。パラメーター化された [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) は、ワークロード環境に合わせてより大規模な構成を提供するアプローチです。Azure portal ベースのデプロイでは、オペレーターが Azure portal を使用してデプロイ パラメーターを指定できます。 

1.  [この](https://aka.ms/ukofficial-paaswa-repo) GitHub リポジトリをローカル ワークステーションに複製するかダウンロードします。
2.  [方法 1:Azure CLI 2 (Express バージョン)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) に関するページを確認し、用意されているコマンドを実行します。
3.  [方法 1a:Azure CLI 2 (スクリプト引数を使用したデプロイの構成)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) に関するページを確認し、用意されているコマンドを実行します
4.  [方法 2:Azure portal のデプロイ プロセス](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process)に関するページを確認し、記載されているコマンドを実行します

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項

### <a name="api-management"></a>API Management

API App Service の前面に [Azure API Management](https://azure.microsoft.com/services/api-management/) を使用すると、セキュリティ、スロットリング、およびコントロールの層が追加され、API の公開、プロキシ、および保護を行うことができます。

### <a name="azure-b2c"></a>Azure B2C

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) は、ユーザーがパブリック Web アプリケーションの登録、ID の作成、承認とアクセス制御を行うことができるコントロールとして実装することができます。

## <a name="disclaimer"></a>免責事項

- このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。
- このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。
- お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。
- このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。
- このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
- このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
