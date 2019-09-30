---
title: PCI DSS の Web アプリをセキュリティで保護する | Microsoft Docs
description: このサンプル アプリでは、セキュリティのベスト プラクティスを実装して、Azure で開発を行う際のアプリケーションと組織のセキュリティ体制を向上させます。
keywords: NA
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992391"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>PCI アプリのセキュリティで保護されたインフラストラクチャを開発する

## <a name="overview"></a>概要

Payment Card Industry (PCI) アプリ用のこのセキュリティで保護されたインフラストラクチャでは、カード所有者データの収集、格納、および取得に適した、Payment Card Industry のサービスとしてのプラットフォーム (PaaS) 環境のデプロイに関するガイダンスを提供します。 このソリューションは、一般的なリファレンス アーキテクチャでの Azure リソースのデプロイと構成を自動化して、お客様が特定のセキュリティ要件と準拠要件をどのように満たすことができるかを示し、お客様が Azure 上に独自のソリューションをビルドして構成するための基礎として機能します。 このソリューションでは、Payment Card Industry Data Security Standards (PCI DSS 3.2) と同様の要件のサブセットを実装します。

このサンプルでは、構成済みのセキュリティ コントロールを含む PaaS Web アプリケーション リファレンス アーキテクチャを自動的にデプロイし、お客様が PCI DSS 3.2 要件と同様のコンプライアンスを実現するのを支援します。 このソリューションは、リソースのデプロイと構成をガイドする Azure Resource Manager テンプレートと PowerShell スクリプトで構成されています。

この記事で説明されている手順に順次従って、アプリケーション コンポーネントが適切に構成されていることを確認してください。 データベース、Azure App Service、Azure Key Vault インスタンス、および Azure Application Gateway インスタンスは互いに依存しています。

デプロイ スクリプトによって、インフラストラクチャがセットアップされます。 デプロイ スクリプトを実行した後は、Azure portal でいくつかの手動構成を実行し、コンポーネントとサービスを互いにリンクする必要があります。

このサンプルは、セキュリティで保護された Azure インフラストラクチャで小売アプリを構築する必要がある、小売業界で働く Azure の経験豊富な開発者を対象としています。

> [!NOTE]
> このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。

変更を加えずにこの環境にアプリケーションをデプロイすることは、PCI DSS 3.2 の要件を完全に満たすには不十分です。 以下の点に注意してください。

- このアーキテクチャは、お客様が PCI DSS 3.2 に準拠した方法で Azure を使用するためのベースラインを提供します。
- お客様は、このアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスの評価を実施する責任を負います。要件は、お客様の実装によって変化する可能性があるからです。

このアプリの開発とデプロイでは、次の方法を学習します。

- Azure Key Vault インスタンスを作成して格納し、そこからシークレットを取得する。
- Azure Database for Azure SQL をデプロイし、セキュリティで保護されたデータを設定して、そこへのアクセスを承認する。
- フロントエンド ファイアウォール アクセスで分離された専用の App Service 環境で、Azure Web アプリをデプロイする。
- [OWASP 上位 10 のルール セット](https://coreruleset.org/)を使用するファイアウォールを使用して、Azure Application Gateway インスタンスを作成して構成する。
- Azure サービスを使用して、転送中および保存時のデータの暗号化を有効にする。
- Azure ポリシーとブルー プリントを設定してコンプライアンスを評価する

このアプリを開発してデプロイすると、次のサンプル Web アプリと、説明されている構成およびセキュリティ対策が設定されます。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント
このアプリは、3 つの階層を持つ典型的な n 層アプリケーションです。 次に示すように、フロントエンド、バックエンド、データベースの各レイヤーに、監視コンポーネントとシークレット管理コンポーネントが統合されています。

![アプリのアーキテクチャ](./media/secure-pci-web-app/architecture.png)

このアーキテクチャは、次のコンポーネントで構成されています。

- [App Service Environment v2](https://docs.microsoft.com/azure/app-service/environment/intro/)。 アプリケーション アーキテクチャの App Service Environment とロード バランサーを提供します。
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/)。 アプリケーション アーキテクチャにゲートウェイとファイアウォールを提供します。
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。 複数のプラットフォームで拡張可能なアプリケーション パフォーマンス管理 (APM) サービスを提供します。
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)。 アプリのシークレットを格納して暗号化し、それらに関連するアクセス ポリシーの作成を管理します。
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/)。クラウドベースの ID とアクセス管理サービス、サインイン、およびアクセス リソースを提供します。
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)。 ドメインをホストするサービスを提供します。
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/)。 アプリケーションをスケーリングし、サービスを高可用性にします
- [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction/)。 最新のデータ ストレージ シナリオのソリューションを提供します。
- [Azure Web アプリ](https://docs.microsoft.com/azure/app-service/overview/)。  Web アプリケーションをホストするための HTTP ベースのサービスを提供します。
- [Azure Database for AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/)。 アプリのデータを安全に格納します。
- [Azure Blueprints](https://docs.microsoft.com/azure/governance/blueprints/overview/) 特定の標準および要件に関する仕様とコンプライアンスを提供します。
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) 割り当てられているポリシーへの非準拠がないか、リソースを評価します。

## <a name="threat-model"></a>脅威モデル
脅威モデリングは、ビジネスとアプリケーションに対する潜在的なセキュリティ上の脅威を特定して、適切な軽減計画を確保するプロセスです。

このサンプルでは、[Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) を使用して、セキュリティで保護されたサンプル アプリに対する脅威モデリングを実装しています。 コンポーネントとデータ フローを図式化することで、開発プロセスの早い段階で問題と脅威を特定できます。 これは後々、時間やコストに節約にもつながります。

次に示すのは、サンプル アプリの脅威モデルです。

![脅威モデル](./media/secure-pci-web-app/threat-model.png)

Threat Modeling Tool によって生成される脅威と潜在的脆弱性の例を、次のスクリーンショットに示します。 脅威モデルでは、公開される攻撃対象領域の概要が示され、問題を軽減する方法を検討するよう開発者に促します。

![脅威モデルの出力](./media/secure-web-app/threat-model-output.png)

たとえば、上の脅威モデルの出力に示されている SQL インジェクションは、ユーザー入力をサニタイズしたり、Azure Database for PostgreSQL でストアド関数を使用したりすることによって軽減できます。 この軽減策により、データの読み書き中に任意にクエリを実行することはできなくなります。

開発者は、脅威モデルの出力に示されている各脅威を軽減することによって、システムの全体的なセキュリティを向上させることができます。

## <a name="deployment"></a>Deployment
### <a name="prerequisites"></a>前提条件
アプリケーションを起動して稼働させるには、次のツールをインストールする必要があります。

- アプリケーション コードを変更および表示するためのコード エディター。 [Visual Studio Code](https://code.visualstudio.com/) はオープンソースのオプションです。
- 開発コンピューター上の [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest)。
- システム上の [Git](https://git-scm.com/)。 Git を使用してソース コードをローカルに複製します。
- [jq](https://stedolan.github.io/jq/)。わかりやすい方法で JSON にクエリを実行するための UNIX ツールです。

このサンプルは、Azure 内にリソースをデプロイするために Azure Resource Manager の API サービスで処理される、JSON 構成ファイルと PowerShell スクリプトで構成されています。 詳しいデプロイ手順については、[こちら](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM)をご覧ください。

#### <a name="quickstart"></a>クイック スタート

1.  [この](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) GitHub リポジトリをローカル ワークステーションに複製するかダウンロードします。
2.  0-Setup-AdministrativeAccountAndPermission.md を確認し、指定されたコマンドを実行します。
3.  Contoso のサンプル データでテスト ソリューションをデプロイするか、最初の運用環境を試験します。

    このスクリプトでは Contoso サンプル データを使用して、Web ストアのデモンストレーションのための Azure リソースをデプロイします。

この例では、デプロイ スクリプトを実行し、Web アプリを App Service にデプロイしてリソースを作成します。

Azure にアプリをデプロイする方法は、次のように多数あります。

- Azure Resource Manager のテンプレート
- PowerShell
- Azure CLI
- Azure ポータル
- Azure DevOps

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項

### <a name="network"></a>ネットワーク
このアーキテクチャは、10.200.0.0/16 のアドレス空間を使用してプライベート仮想ネットワークを定義します。
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
ネットワーク セキュリティ グループ (https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) には、仮想ネットワーク内のトラフィックを許可または拒否するアクセス制御リスト (ACL) が含まれています。 ネットワーク セキュリティ グループを使用して、サブネットまたは個々の VM レベルでトラフィックを保護できます。 次のネットワーク セキュリティ グループが存在します。

- Application Gateway 用の 1 つのネットワーク セキュリティ グループ
- App Service Environment 用の 1 つのネットワーク セキュリティ グループ
- Azure SQL Database 用の 1 つのネットワーク セキュリティ グループ
- 踏み台ホスト用の 1 つのネットワーク セキュリティ グループ

各ネットワーク セキュリティ グループでは、ソリューションが安全かつ適切に動作できるように、特定のポートとプロトコルが開かれています。 さらに、各ネットワーク セキュリティ グループで次の構成を使用できます。

- 診断ログとイベント (https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) が有効化され、ストレージ アカウントに格納される
- Azure Monitor ログがネットワーク セキュリティ グループの診断 (https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) に接続される

### <a name="nsg-config"></a>NSG の構成
App Service Environment の NSG 構成を次の図に示すように構成する必要があります。

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

各サブネットは、対応するネットワーク セキュリティ グループに関連付けられています。

### <a name="config"></a>Config
サブネットは次の図のように構成されます。
 ![構成](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
ドメイン ネーム システム (DNS) は、Web サイトまたはサービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) は、DNS ドメインのホスティング サービスであり、Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 Azure DNS は、プライベート DNS ドメインもサポートします。

### <a name="protect-data"></a>データの保護
クラウド内のデータを保護するには、データが発生する可能性がある特定の状態と、その状態に対してどのような制御を利用できるのかを把握する必要があります。 Azure のデータ セキュリティと暗号化のベスト プラクティスは、次のデータの状態に関連しています。

- 保存時:ストレージ オブジェクト、コンテナー、データなど、物理メディア (磁気ディスクまたは光学ディスク) に静的な状態で存在しているすべての情報が含まれます。
- 転送中:コンポーネント、場所、またはプログラムの間でデータが転送されるとき、データは転送中になります。 例として、ネットワーク上の転送、サービス バス経由の転送 (オンプレミスからクラウドへ、クラウドからオンプレミスへ。ExpressRoute などのハイブリッド接続を含みます)、入力/出力プロセス中の転送があります。

### <a name="azure-storage"></a>Azure Storage
暗号化された保存データの要件を満たすために、すべての [Azure Storage](https://azure.microsoft.com/services/storage/) では Azure Key Vault を使用して、データのアクセスと暗号化を行うキーの制御が維持されます。 これは、PCI DSS 3.2 によって定義されている組織のセキュリティ コミットメントとコンプライアンス要件のサポートにおいてカード所有者のデータを保護するために役立ちます。

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption では、Windows の BitLocker 機能を利用して、データ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Azure Key Vault と統合されています。

### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL データベース インスタンスは、次のデータベース セキュリティ対策を使用します。

- [Active Directory 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)を使用して、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
- [SQL Database の監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
- Azure SQL Database は、[Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用するように構成されます。Transparent Data Encryption は、データベース、関連付けられたバックアップ、トランザクション ログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 Transparent Data Encryption は、保存されているデータが未承認のアクセスの対象になっていないことを保証します。
- [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
- [SQL の脅威検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)は、不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンに対するセキュリティの警告を提供することで、潜在的な脅威の検出とそれらの脅威への対応を可能にします。
- [暗号化された列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)により、データベース システム内で機微なデータがプレーンテキストとして表示されないことが確保されます。 データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
- [SQL Database 動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)は、特権のないユーザーまたはアプリケーションに対してデータをマスクすることで、機微なデータの露出を制限します。 動的データ マスクは、潜在的な機微なデータを自動的に検出し、適用する適切なマスクを提案できます。 これは、不正アクセスによるデータベースの終了が発生しないように、データへのアクセスを識別して削減するのに役立ちます。 お客様は、お使いのデータベース スキーマに準拠するように動的データ マスクの設定を調整する必要があります。

### <a name="identity-management"></a>ID 管理
次のテクノロジによって、Azure 環境でのカードホルダー データへのアクセスを管理する機能が提供されます。

- Azure Active Directory は、Microsoft のマルチテナント クラウドベースのディレクトリおよび ID 管理サービスです。 このソリューションのすべてのユーザー (Azure SQL Database にアクセスするユーザーを含む) は、Azure Active Directory で作成されています。
- アプリケーションに対する認証は Azure Active Directory を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration)」をご覧ください。 さらに、データベース列の暗号化では、Azure SQL Database に対してアプリケーションを認証するために Azure Active Directory が使用されます。 詳細については、[Azure SQL Database で機微なデータを保護する方法](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)に関するページを参照してください。
- Azure ロールベースのアクセス制御によって、管理者は、業務の遂行に必要なアクセスのみをユーザーに付与するきめの細かいアクセス許可を定義できます。 すべてのユーザーに Azure リソースへの無制限のアクセス許可を付与する代わりに、管理者は、カードホルダー データにアクセスするための特定のアクションのみを許可できます。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。
- Azure Active Directory Privileged Identity Management では、お客様は、カード所有者データなどの特定の情報にアクセスできるユーザーの数を最小限に抑えることができます。 管理者は、Azure Active Directory Privileged Identity Management を使用して、特権 ID と特権 ID によるリソースへのアクセスを検出、制限、監視できます。 この機能を使用して、必要に応じて、オンデマンドの Just-In-Time 管理アクセスを適用することもできます。
- Azure Active Directory Identity Protection では、組織の ID に影響する可能性のある脆弱性を検出し、組織の ID に関連する検出された疑わしいアクションに対する自動応答を構成します。さらに、疑わしいインシデントを調査し、適切なアクションを実行してそれらを解決します。

### <a name="secrets-management"></a>シークレットの管理
ソリューションでは、キーとシークレットの管理に Azure Key Vault が使用されます。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 次の Azure Key Vault 機能は、そのようなデータの保護とアクセスに役立ちます。

- 必要に応じて、高度なアクセス ポリシーが構成されます。
- Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されます。
- Key Vault のすべてのキーとシークレットに有効期限があります。
- Key Vault 内のすべてのキーは、特殊なハードウェア セキュリティ モジュールによって保護されます。 キーの種類は、HSM 保護された 2048 ビット RSA キーです。
- Key Vault を使用すると、キーとシークレット (認証キー、ストレージ アカウント キー、データ暗号化キー、.PFX ファイル、パスワードなど) をハードウェア セキュリティ モジュール (HSM) で保護されたキーを使用して暗号化できます。
- RBAC を使用して、特定のスコープのユーザー、グループ、アプリケーションにアクセス許可を割り当てます。
- Key Vault を使用して、自動更新で TLS 証明書を管理します。
- Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
- キーに対して許可される暗号化操作は必要なものに制限されます。

### <a name="azure-security-center"></a>Azure Security Center
Azure Security Center を使用すると、お客様はワークロード全体へのセキュリティ ポリシーの一元的な適用と管理、脅威にさらされる状態の軽減、攻撃の検出とその対応を行うことができます。 さらに、Azure Security Center は、Azure サービスの既存の構成にアクセスして、セキュリティ状況の改善とデータの保護に役立つ、構成とサービスに関する推奨事項を提供します。

Azure Security Center では、さまざまな検出機能を使用して、お客様の環境を対象とする攻撃の可能性を通知します。 これらのアラートには、アラートをトリガーした要因、対象となったリソース、攻撃元に関する重要な情報が含まれています。 Azure Security Center には、事前に定義された一連のセキュリティ アラートが用意されています。これらは、脅威または疑わしいアクティビティが発生した際にトリガーされます。 Azure Security Center のカスタム アラート ルールを使用すると、お客様が環境から既に収集されているデータに基づいて、新しいセキュリティ アラートを定義できます。

Azure Security Center では、お客様の潜在的なセキュリティの問題の検出と対処が簡単になるように、優先度付けしたセキュリティの警告とインシデントを提供しています。 インシデント対応チームによる脅威の調査と修復を支援するために、検出された脅威ごとに脅威インテリジェンス レポートが生成されます。

### <a name="azure-application-gateway"></a>Azure Application Gateway
このアーキテクチャでは、Web アプリケーション ファイアウォールが構成され、OWASP ルールセットが有効な Azure Application Gateway を使用してセキュリティの脆弱性のリスクを軽減します。 その他の機能には次のものがあります。

- エンドツーエンド SSL
- TLS v1.0 および v1.1 の無効化
- TLSv1.2 の有効化
- Web アプリケーション ファイアウォール (防止モード)
- OWASP 3.0 ルールセットを使用した防止モード
- 診断ログの有効化
- カスタム正常性プローブ
- Azure Security Center と Azure Advisor による、その他の保護と通知の提供。 Azure Security Center は評価システムも提供します。

### <a name="logging-and-auditing"></a>ログ記録と監査
Azure サービスは、システムの正常性だけではなく、システムとユーザーのアクティビティも詳細に記録します。

- [アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、状態の判断に役立ちます。
- [診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログ、Azure Storage ログ、Key Vault 監査ログ、および Application Gateway のアクセス ログとファイアウォール ログが含まれます。 すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 リテンション期間には、組織固有の保有要件を満たすために最長 730 日までの日数をユーザーが設定できます。

### <a name="azure-monitor-logs"></a>Azure Monitor ログ
これらのログは、処理、格納、およびダッシュボードでのレポート表示を行うために、[Azure Monitor ログ](https://azure.microsoft.com/services/log-analytics/)に統合されます。 収集されたデータは、その型ごとに Log Analytics ワークスペース内の別個のテーブルにまとめられ、すべてのデータがその収集元にかかわらず一斉に分析できる状態になります。 さらに、Azure Security Center を Azure Monitor ログと統合することで、お客様は Kusto クエリを使用してセキュリティ イベント データにアクセスし、それを他のサービスからのデータと組み合わせることができます。

このアーキテクチャの一部として、次の Azure [監視ソリューション](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)が含まれます。

- [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment):Active Directory 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment):SQL 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧をお客様に提供します。
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth):Agent Health ソリューションでは、デプロイされたエージェント数とその地理的配置に加え、応答しないエージェント数とオペレーショナル データを送信しているエージェント数を報告します。
- [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity):Activity Log Analytics ソリューションは、顧客向けのすべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) は、組織で監査、アラートの作成、データのアーカイブ、Azure リソース内の API 呼び出しの追跡を実行できるようにすることで、ユーザーがパフォーマンスの追跡、セキュリティの維持、傾向の識別を実行できるようにします。

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理サービスです。 Application Insights は、パフォーマンスの異常を検出します。それを使用して、実行中の Web アプリケーションを監視できます。 組み込まれている強力な分析ツールを使えば、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。 それは、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。

### <a name="azure-key-vault"></a>Azure Key Vault
キーを格納する組織のコンテナーを作成し、以下のような運用タスクのアカウンタビリティを維持します。

- Key Vault に格納されるデータは次のとおりです。

   - Application Insight キー
   - データ ストレージ アクセス キー
   - 接続文字列
   - データ テーブル名
   - ユーザーの資格情報

- 必要に応じて、高度なアクセス ポリシーが構成されます。
- Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されています。
- Key Vault のすべてのキーとシークレットに有効期限があります。
- Key Vault のすべてのキーは、HSM [キーの種類 = HSM で保護された 2048 ビットの RSA キー] で保護されています。
- ロールベースのアクセス制御 (RBAC) を使用して、すべてのユーザー/ID に最低限必要なアクセス許可が付与されます。
- アプリケーションは、相互に信頼し、実行時に同じシークレットへのアクセスを必要とする場合を除き、Key Vault を共有しません。
- Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
- キーの許可される暗号化操作は必要なものに制限されます。

### <a name="vpn-and-expressroute"></a>VPN と ExpressRoute
この PaaS Web アプリケーションの参照アーキテクチャの一部としてデプロイされるリソースへの接続を安全に確立するために、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) またはセキュリティ保護された VPN トンネルを構成する必要があります。 ExpressRoute または VPN を適切に設定することで、転送中のデータに対して保護のレイヤーを追加できます。

Azure を使用してセキュリティ保護された VPN トンネルを実装することにより、オンプレミス ネットワークと Azure 仮想ネットワークの間で仮想プライベート接続を作成できます。 この接続はインターネットを介して行われ、お客様はご自分のネットワークと Azure の間の暗号化されたリンク内で情報を安全に "トンネリング" することができます。 サイト間 VPN は、数十年にわたってあらゆる規模の企業に導入されている安全で成熟したテクノロジです。 このオプションでは、暗号化メカニズムとして IPsec トンネル モードが使用されます。

VPN トンネル内のトラフィックはサイト間 VPN を使用してインターネット上を転送されるため、Microsoft は、さらに高いセキュリティで保護されたもう 1 つの接続オプションを提供します。 Azure ExpressRoute は、オンプレミスの場所または Exchange ホスティング プロバイダーと Azure の間に確立される専用 WAN リンクです。 ExpressRoute 接続はインターネットを経由しないため、これらの接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 さらに、これはお客様の通信プロバイダーの直接接続であり、データがインターネット上を移動しないため、インターネットに公開されることはありません。

オンプレミス ネットワークを Azure に拡張するセキュアなハイブリッド ネットワークを実装するためのベスト プラクティスが[提供されています](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="cost-considerations"></a>コストに関する考慮事項
まだ Azure アカウントを持っていない場合は、無料で作成できます。 開始するには、[無料アカウントのページ](https://azure.microsoft.com/free/)に移動し、無料の Azure アカウントでできることと、12 か月間無料で使用できる製品をご確認ください。

セキュリティ機能を備えたサンプル アプリのリソースをデプロイするには、いくつかの Premium 機能に対して料金を支払う必要があります。 アプリケーションの規模が拡大し、Azure によって提供される無料レベルと試用版をアップグレードしてアプリケーションの要件を満たす必要が生じるにつれ、コストが増加する可能性があります。 コストの見積もりには、Azure [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)をご利用ください。

## <a name="next-steps"></a>次の手順
次の記事は、セキュリティで保護されたアプリケーションを設計、開発、およびデプロイする際に役立ちます。

- [Design (デザイン)](secure-design.md)
- [開発](secure-develop.md)
- [デプロイする](secure-deploy.md)
