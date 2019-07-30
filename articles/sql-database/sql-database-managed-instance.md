---
title: Azure SQL Database Advanced Data Security の概要 | Microsoft Docs
description: このトピックでは、Azure SQL Database Advanced Data Security について、そのしくみと Azure SQL Database の単一データベースまたはプールされたデータベースとの相違点について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: b03f546b992bd9de6092dc0da8ef72aa69aa1da2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447784"
---
# <a name="use-sql-database-advanced-data-security-with-virtual-networks-and-near-100-compatibility"></a>SQL Database Advanced Data Security の仮想ネットワークでの使用と 100% に近い互換性

マネージド インスタンスは、Azure SQL Database の新しいデプロイ オプションです。最新の SQL Server オンプレミス (Enterprise Edition) Database Engine との 100% 近い互換性を備えています。また、セキュリティに関する一般的な懸念事項に対処するネイティブの[仮想ネットワーク](../virtual-network/virtual-networks-overview.md) (VNet) 実装を提供し、オンプレミスの SQL Server の顧客に有利な[ビジネス モデル](https://azure.microsoft.com/pricing/details/sql-database/)を提供します。 マネージド インスタンス デプロイ モデルにより、既存の SQL Server の顧客は最小限のアプリケーションおよびデータベース変更のみで、オンプレミスのアプリケーションをクラウドに移行 (リフト アンド シフト) することができます。 これと同時に、マネージド インスタンス デプロイ オプションでは、管理のオーバーヘッドと TCO を大幅に削減するすべての PaaS 機能 (自動的な修正プログラムの適用およびバージョン更新、[自動バックアップ](sql-database-automated-backups.md)、[高可用性](sql-database-high-availability.md)) が維持されます。

> [!IMPORTANT]
> 現在マネージド インスタンス デプロイ オプションが提供されているリージョンの一覧については、「[サポートされているリージョン](sql-database-managed-instance-resource-limits.md#supported-regions)」をご覧ください。

次の図では、マネージド インスタンスの主な機能について概説します。

![主な機能](./media/sql-database-managed-instance/key-features.png)

マネージド インスタンス デプロイ モデルは、多数のアプリをオンプレミスまたは IaaS、自作、あるいは ISV 提供の環境から完全なマネージド PaaS クラウド環境に、できるだけ手間をかけずに移行しようとしているユーザー用に設計されています。 Azure で完全に自動化された[データ移行サービス (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) を使用することにより、顧客はオンプレミスの SQL Server をマネージド インスタンスに移行 (リフト アンド シフト) することができます。この場合、VNet のネイティブ サポートにより、SQL Server オンプレミスとの互換性の維持、顧客のインスタンスの完全な分離が実現されます。  ソフトウェア アシュアランスに基づき、マネージド インスタンスでは [SQL Server 用の Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を利用して、顧客の既存のライセンスを割引料金のライセンスに交換することができます。  高度なセキュリティと豊富なプログラミング サーフェスを必要とする SQL Server インスタンスにとって、マネージド インスタンスはクラウド内の最適な移行先です。

マネージド インスタンス デプロイ オプションでは、段階的リリース計画に基づいて、最新のオンプレミスの SQL Server バージョンとの 100% 近いセキュリティ、外部からのアクセス互換性を実現することが目的とされています。

Azure SQL Database デプロイ オプションの単一データベース、プールされたデータベース、マネージド インスタンス、仮想マシンでホストされる SQL Server のどれにするかを決定するには、[Azure で適切なバージョンの SQL Server を選択する方法](sql-database-paas-vs-sql-server-iaas.md)に関するページをご覧ください。

## <a name="key-features-and-capabilities"></a>主な機能

マネージド インスタンスでは、Azure SQL Database と SQL Server Database Engine の両方で利用可能な最高の機能が組み合わされています。

> [!IMPORTANT]
> マネージド インスタンスでは、オンライン操作、計画の自動修正、その他のエンタープライズ パフォーマンス拡張機能など、最新バージョンの SQL Server のすべての機能が実行されます。 使用可能な機能の比較については、「[機能の比較:Azure SQL Database と SQL Server](sql-database-features.md)」をご覧ください。

| **PaaS の特典** | **ビジネス継続性** |
| --- | --- |
|ハードウェアの購入と管理が不要 <br>基になるインフラストラクチャを管理するための管理オーバーヘッドが不要 <br>迅速なプロビジョニングとサービスのスケーリング <br>自動的に行われる修正プログラムの適用とバージョンのアップグレード <br>その他の PaaS データ サービスとの統合 |99.99% アップタイム SLA  <br>組み込みの[高可用性](sql-database-high-availability.md) <br>[自動バックアップ](sql-database-automated-backups.md)によるデータの保護 <br>顧客が構成可能なバックアップの保有期間 <br>ユーザーによって開始される[バックアップ](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[データベースのポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)機能 |
|**セキュリティとコンプライアンス** | **管理**|
|分離環境 ([VNet 統合](sql-database-managed-instance-connectivity-architecture.md)、シングル テナント サービス、専用のコンピューティングおよびストレージ) <br>[透過的なデータ暗号化 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD 認証](sql-database-aad-authentication.md)、シングル サインオンのサポート <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD サーバー プリンシパル (ログイン)</a> (**パブリック プレビュー**) <br>Azure SQL データベースと同じコンプライアンス標準に準拠 <br>[SQL 監査](sql-database-managed-instance-auditing.md) <br>[脅威検出](sql-database-managed-instance-threat-detection.md) |サービスのプロビジョニングとスケーリングを自動化するための Azure Resource Manager API <br>手動でのサービスのプロビジョニングとスケーリングに対応する Azure Portal の機能 <br>データ移行サービス

> [!IMPORTANT]
> Azure SQL Database (すべてのデプロイ オプション) は、さまざまなコンプライアンス標準に対して認定されています。 詳細については、[Microsoft Azure セキュリティ センター](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)に関するページを参照してください。ここから最新の SQL Database コンプライアンス証明書の一覧を入手できます。

マネージド インスタンスの主な機能を次の表に示します。

|機能 | 説明|
|---|---|
| SQL Server のバージョン/ビルド | SQL Server Database Engine (最新の安定版) |
| 管理される自動バックアップ | はい |
| インスタンスおよびデータベースに対する組み込みの監視とメトリック | はい |
| 自動的に行われるソフトウェア修正プログラムの適用 | はい |
| 最新のデータベース エンジンの機能 | はい |
| データベースごとのデータ ファイル数 (ROWS) | 複数 |
| データベースごとのログ ファイル数 (LOG) | 1 |
| VNet - Azure Resource Manager デプロイ | はい |
| VNet - クラシック デプロイ モデル | いいえ |
| ポータルのサポート | はい|
| 組み込み統合サービス (SSIS) | いいえ - SSIS は [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) に含まれます。 |
| 組み込み Analysis Service (SSAS) | いいえ - SSAS は別の [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) です。 |
| 組み込みレポート サービス (SSRS) | いいえ - Power BI または SSRS IaaS を使用してください。 |
|||

## <a name="vcore-based-purchasing-model"></a>仮想コアベースの購入モデル

マネージド インスタンスの[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)では、柔軟性、管理性、透明性が実現されており、オンプレミスのワークロード要件をクラウドに容易に移行できます。 このモデルでは、ワークロードの必要性に基づいて、コンピューティング、メモリ、ストレージを変更できます。 また、仮想コア モデルは [SQL Server 用の Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)による最大 30% の割引の対象となります。

仮想コア モデルでは、ハードウェアの世代を選択できます。

- **Gen4** 論理 CPU は、Intel E5-2673 v3 (Haswell) 2.4-GHz プロセッサ、接続された SSD、物理コア、コアあたり 7 GB の RAM、8 - 24 仮想コアのコンピューティング サイズに基づいています。
- **Gen5** 論理 CPU は、Intel E5-2673 v4 (Broadwell) 2.3-GHz プロセッサ、高速 NVMe SSD、ハイパースレッド論理コア、4 - 80 コアのコンピューティング サイズに基づいています。

ハードウェアの世代の違いについて詳しくは、[マネージド インスタンスのリソース制限](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)に関する記事をご覧ください。

> [!IMPORTANT]
> 新しい Gen4 データベースは、AustraliaEast リージョンでサポートされなくなりました。

## <a name="managed-instance-service-tiers"></a>マネージド インスタンスのサービス レベル

マネージド インスタンスは 2 つのサービス レベルで利用できます。

- **General Purpose**:一般的なパフォーマンスと IO 待ち時間要件のアプリケーション用に設計されています。
- **Business Critical**:短い IO 待ち時間要件で、基盤となるメンテナンス操作がワークロードに与える影響が最小限のアプリケーション用に設計されています。

どちらのサービス階層も 99.99% の可用性を保証し、ストレージ サイズとコンピューティング能力を個別に選択することができます。 Azure SQL Database の高可用性アーキテクチャの詳細については、「[高可用性と Azure SQL Database](sql-database-high-availability.md)」を参照してください。

### <a name="general-purpose-service-tier"></a>General Purpose サービス レベル

次のリストで、汎用サービス階層の主な特徴を説明します。

- 標準的なパフォーマンス要件を持つ大多数のビジネス アプリケーション向けの設計
- 高パフォーマンスの Azure Blob Storage (8 TB)
- 信頼性の高い Azure Blob Storage と [Azure Service Fabric](../service-fabric/service-fabric-overview.md) に基づいた組み込みの[高可用性](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)

詳しくは、「[storage layer in general purpose tier (General Purpose レベルでのストレージ レイヤー)](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c)」および「[storage performance best practices and considerations for managed instances (general purpose) (マネージド インスタンス (General Purpose) のストレージ パフォーマンスのベスト プラクティスおよび考慮事項)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/)」 をご覧ください。

サービス レベルの違いについて詳しくは、[マネージド インスタンスのリソース制限](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)に関する記事をご覧ください。

### <a name="business-critical-service-tier"></a>Business Critical サービス レベル

Business Critical サービス レベルは、IO 要件の高いアプリケーション向けに構築されます。 分離された複数のレプリカを使用して、最高の耐障害性が提供されます。

次のリストで、Business Critical サービス レベルの主な特徴を説明します。

- 最高のパフォーマンスと HA の要件を持つビジネス アプリケーション向けに設計されています。
- 超高速のローカル SSD ストレージ (Gen4 で最大 1 TB、Gen5 で最大 4 TB) が付属します
- [Always On 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)と [Azure Service Fabric](../service-fabric/service-fabric-overview.md) に基づいた組み込みの[高可用性](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)。
- レポートやその他の読み取り専用ワークロードに使用できる、組み込みの追加の[読み取り専用データベース レプリカ](sql-database-read-scale-out.md)
- 高パフォーマンス要件のワークロードに使用することができる[インメモリ OLTP](sql-database-in-memory.md)  

サービス レベルの違いについて詳しくは、[マネージド インスタンスのリソース制限](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)に関する記事をご覧ください。

## <a name="advanced-security-and-compliance"></a>高度なセキュリティとコンプライアンス

マネージド インスタンス デプロイ オプションでは、Azure クラウドと SQL Server Database Engine によって提供される高度な機能が組み合わされています。

### <a name="managed-instance-security-isolation"></a>マネージド インスタンスのセキュリティ分離

マネージド インスタンスでは、Azure のクラウド内での他のテナントからのセキュリティ分離が追加されています。 セキュリティ分離:

- Azure Express Route または VPN Gateway を使った、オンプレミス環境への[ネイティブ仮想ネットの実装](sql-database-managed-instance-connectivity-architecture.md)と接続。
- 既定のデプロイでは、SQL エンドポイントはプライベート IP アドレスでのみ公開されるため、Azure またはハイブリッド ネットワークから安全に接続できます。
- 専用の基になるインフラストラクチャ (コンピューティング、ストレージ) を備えたシングル テナント。

次の図では、アプリケーションのさまざまな接続オプションについて概説します。

![高可用性](./media/sql-database-managed-instance/application-deployment-topologies.png)  

サブネット レベルでの VNet 統合およびネットワーク ポリシーの適用の詳細については、[マネージド インスタンスの VNet アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)に関する記事と、[マネージド インスタンスへのアプリケーションの接続](sql-database-managed-instance-connect-app.md)に関する記事をご覧ください。

> [!IMPORTANT]
> 複数のマネージド インスタンスを同じサブネットに配置すると追加のメリットが得られるので、セキュリティ要件で許可されている場合は常にそのように配置します。 同じサブネット内に複数のインスタンスを配置すると、ネットワーク インフラストラクチャのメンテナンスが大幅に簡素化され、インスタンスのプロビジョニング時間が短縮されます。これは、長いプロビジョニング時間が、サブネット内の最初のマネージド インスタンスをデプロイするコストと関連性があるためです。

### <a name="azure-sql-database-security-features"></a>Azure SQL Database のセキュリティ機能

Azure SQL Database は、データを保護するために使用できる一連の高度なセキュリティ機能を提供します。

- [マネージド インスタンスの監査](sql-database-managed-instance-auditing.md)では、データベース イベントが追跡されて、Azure ストレージ アカウント内の監査ログ ファイルにイベントが書き込まれます。 監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。
- 移動中のデータの暗号化 - マネージド インスタンスでは、トランスポート層セキュリティを使用して、移動中のデータの暗号化を実現することにより、データがセキュリティで保護されます。 トランスポート層セキュリティに加えて、マネージド インスタンス デプロイ オプションでは [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) を使用して、転送中、保存中、およびクエリの処理中に機密データが保護されます。 Always Encrypted は、重要なデータの盗難を含む侵害に対する、これまでにないデータ セキュリティを提供する業界初の機能です。 たとえば、クレジット カード番号は、Always Encrypted によって常に暗号化されてデータベースに格納されます。暗号化はクエリ処理中も行われます。復号化は、データを処理する必要がある承認されたスタッフまたはアプリケーションが使用するときに実行することができます。
- [脅威の検出](sql-database-managed-instance-threat-detection.md)では、サービスに組み込まれたセキュリティ インテリジェンスの追加レイヤーを提供することにより、[監査](sql-database-managed-instance-auditing.md)が補完されます。このレイヤーでは、データベースにアクセスしたりデータベースを悪用したりしようとする、異常で有害な可能性がある動作が検出されます。 不審なアクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセス パターンについて、アラートが送信されます。 脅威の検出のアラートは、[Azure Security Center](https://azure.microsoft.com/services/security-center/) で見ることができます。不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法が表示されます。  
- [動的データ マスク](/sql/relational-databases/security/dynamic-data-masking)では、特権のないユーザーに対して機微なデータをマスクすることでデータの公開を制限します。 動的データ マスクでは、公開する機微なデータの量を指定することで、機微なデータに対する未承認のアクセスを防ぐことができ、アプリケーション レイヤーへの影響は最小限に抑えられます。 これはポリシー ベースのセキュリティ機能であり、指定されたデータベース フィールドに対するクエリの結果セットに含まれるデリケートなデータが表示されないようにします。データベース内のデータは変更されません。
- [行レベルのセキュリティ](/sql/relational-databases/security/row-level-security)を使用して、クエリを実行しているユーザーの特性 (グループのメンバーシップや実行コンテキストなど) に基づいて、データベース テーブル内の行へのアクセスを制御できます。 行レベルのセキュリティ (RLS) により、アプリケーションでのセキュリティの設計やコーディングが簡略化されます。 RLS を使用すると、データ行のアクセスに対して制限を実装できます。 たとえば、ワーカーが自分の部署に関連するデータ行にのみアクセスできるようにしたり、データ アクセスを関連するデータにのみ制限したりできます。
- [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) では、マネージド インスタンスのデータ ファイルが暗号化されます。これは、保存データの暗号化として知られています。 TDE は、データとログ ファイルの I/O 暗号化と複合化をリアルタイムで実行します。 暗号化は、復旧中に、可用性のためのデータベース ブート レコードに格納されるデータベース暗号化キー (DEK) を使用します。 透過的なデータ暗号化でマネージド インスタンス内のすべてのデータベースを保護することができます。 TDE は、記憶域メディアの盗難を防ぐために多くのコンプライアンス基準で必要とされている、SQL Server の実証済みの保存データの暗号化テクノロジです。

Azure Database Migration Service (DMS) またはネイティブの復元を使用して、マネージド インスタンスへの暗号化されたデータベースの移行がサポートされています。 ネイティブ復元を使用して暗号化されたデータベースを移行する予定がある場合は、既存の TDE 証明書をオンプレミスの SQL Server または仮想マシン内の SQL Server からマネージド インスタンスに移行する手順が必要です。 移行オプションについて詳しくは、[マネージド インスタンスへの SQL Server インスタンスの移行](sql-database-managed-instance-migrate.md)に関する記事をご覧ください。

## <a name="azure-active-directory-integration"></a>Azure Active Directory の統合

マネージド インスタンス デプロイ オプションでは、従来の SQL サーバー データベース エンジン ログインと、Azure Active Directory (AAD) に統合されたログインがサポートされています。 Azure AD サーバー プリンシパル (ログイン) (**パブリック プレビュー**) は、オンプレミス環境で使用されているオンプレミス データベース ログインの Azure クラウド版です。 Azure AD サーバー プリンシパル (ログイン) を使用すると、まさにインスタンス スコープのプリンシパルとして、Azure Active Directory テナントからユーザーとグループを指定でき、同じマネージド インスタンス内の複数データベース間のクエリなど、インスタンス レベルの任意の操作を実行できます。

Azure AD サーバー プリンシパル (ログイン) (**パブリック プレビュー**) を作成するための新しい構文 **FROM EXTERNAL PROVIDER** が導入されます。 構文の詳細については、<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> を参照し、「[マネージド インスタンスの Azure Active Directory 管理者をプロビジョニングする](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)」の記事を確認してください。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory との統合と多要素認証

マネージド インスタンス デプロイ オプションでは、[Azure Active Directory 統合](sql-database-aad-authentication.md)によって、データベース ユーザーとその他の Microsoft サービスの ID を一元的に管理することができます。 この機能は、アクセス許可の管理を簡略化し、セキュリティを強化します。 Azure Active Directory は、[多要素認証](sql-database-ssms-mfa-authentication-configure.md) (MFA) をサポートしています。これにより、シングル サインオン プロセスをサポートすると同時に、データとアプリケーションのセキュリティが強化されます。

### <a name="authentication"></a>Authentication

マネージド インスタンス認証とは、データベースへの接続時にユーザーが自分の ID を証明する方法のことです。 SQL Database は、2 種類の認証をサポートしています。  

- **SQL 認証**:

  この認証方法では、ユーザー名とパスワードを使用します。
- **Azure Active Directory 認証**:

  この認証方法では、Azure Active Directory で管理されている ID を使用し、管理、統合されたドメインをサポートしています。 [可能であれば](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode)、Active Directory 認証 (統合セキュリティ) を使用します。

### <a name="authorization"></a>Authorization

承認とは、Azure SQL Database 内でユーザーにどのような操作が許可されるかを示すものであり、ユーザー アカウントのデータベース ロールのメンバーシップとオブジェクト レベルのアクセス許可によって制御されます。 マネージド インスタンスには、SQL Server 2017 と同じ承認機能があります。

## <a name="database-migration"></a>データベース移行

マネージド インスタンスのデプロイ オプションは、オンプレミスまたは IaaS データベース実装からの大量のデータベース移行を含むユーザー シナリオを対象にしています。 マネージド インスタンスでは、次に示すような複数のデータベース移行オプションがサポートされています。

### <a name="back-up-and-restore"></a>バックアップおよび復元  

移行のアプローチでは、Azure Blob Storage への SQL バックアップが活用されます。 Azure Storage Blob に格納されたバックアップは、[T-SQL RESTORE コマンド](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)を使用して、マネージド インスタンスに直接復元できます。

- Wide World Importers - Standard データベースのバックアップ ファイルを復元する方法を説明したクイック スタートについては、[バックアップ ファイルをマネージド インスタンスに復元する](sql-database-managed-instance-get-started-restore.md)方法に関するページを参照してください。 このクイック スタートでは、バックアップ ファイルを Azure ブログ ストレージにアップロードし、Secure Access Signature (SAS) キーを使用してセキュリティで保護する必要があることが説明されています。
- URL からの復元については、「[URL からのネイティブ復元](sql-database-managed-instance-migrate.md#native-restore-from-url)」を参照してください。

> [!IMPORTANT]
> マネージド インスタンスからのバックアップは、別のマネージド インスタンスにのみ復元できます。 オンプレミスの SQL Server に、または単一データベースまたはエラスティック プールに復元することはできません。

### <a name="data-migration-service"></a>データ移行サービス

Azure Database Migration Service は、複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を最小限のダウンタイムで実現できるように設計された、フル マネージドのサービスです。 このサービスでは、既存のサード パーティ製データベースと SQL Server データベースを、Azure VM 内の Azure SQL Database (単一データベース、エラスティック プール内のプールされたデータベース、およびマネージド インスタンス内のインスタンス データベース) と SQL Server に移動するために必要なタスクが合理化されます。 [DMS を使用してオンプレミスのデータベースをマネージド インスタンスに移行する方法](https://aka.ms/migratetoMIusingDMS)に関するページを参照してください。

## <a name="sql-features-supported"></a>サポートされている SQL 機能

マネージド インスタンス デプロイ オプションではサービスの一般提供までに、最新のオンプレミスの SQL Server との、100% 近くのセキュリティ、外部からのアクセス互換性を段階的に実現することを目的としています。 機能と比較の一覧については、[SQL Database 機能の比較](sql-database-features.md)に関するページを参照してください。マネージド インスタンスと SQL Server における T-SQL の相違点の一覧については、[マネージド インスタンスと SQL Server における T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関するページを参照してください。

マネージド インスタンス デプロイ オプションでは、SQL 2008 データベースに対する下位互換性がサポートされています。 SQL 2005 データベース サーバーからの直接の移行がサポートされており、移行される SQL 2005 データベースの互換性レベルは SQL 2008 に更新されます。
  
次の図では、マネージド インスタンスでのセキュリティ、外部からのアクセス互換性について概説します。  

![移行](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>SQL Server オンプレミスとマネージド インスタンスとの主な相違点

マネージド インスタンス デプロイ オプションでは、クラウド内で常に最新の状態が維持されることから利点が得られます。これは、オンプレミスの SQL Server の一部の機能が使用されなくなるか、削除されるか、代替機能が用意される可能性があることを意味します。 特定の機能の動作がやや異なること、または完全には制御できない環境でサービスが実行されていないことをツールで認識することが必要な特定のケースがあります。

- 高可用性は、[Always On 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)に似たテクノロジを使用して、組み込まれており、事前に定義されています。
- 自動バックアップおよびポイントイン タイム リストア。 顧客は自動バックアップ チェーンに影響を及ぼさない `copy-only` バックアップを開始できます。
- マネージド インスタンスでは完全な物理パスを指定できないため、すべての対応するシナリオを異なる方法でサポートする必要があります。たとえば、RESTORE DB は WITH MOVE をサポートせず、CREATE DB では物理パスを使用できず、BULK INSERT は Azure BLOB にしか機能しません。
- マネージド インスタンスでは、Windows 認証に代わるクラウドでの別の手段として、[Azure AD 認証](sql-database-aad-authentication.md)がサポートされています。
- マネージド インスタンスでは、インメモリ OLTP オブジェクトを含むデータベース用の XTP ファイル グループおよびファイルが自動的に管理されます
- マネージド インスタンスでは SQL Server Integration Services (SSIS) がサポートされており、SSIS パッケージを格納する SSIS カタログ (SSISDB) をホストできますが、パッケージは Azure Data Factory (ADF) 上のマネージド Azure-SSIS Integration Runtime (IR) で実行されます。[ADF での Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関するページをご覧ください。 SQL Database の SSIS 機能を比較するには、[Azure SQL Database の単一データベース/エラスティック プールおよびマネージド インスタンスの比較](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)に関するページを参照してください。

### <a name="managed-instance-administration-features"></a>マネージド インスタンスの管理機能

マネージ インスタンス デプロイ オプションでは、SQL Database サービスが自動化されているか、またはタスクが大幅に簡素化されているため、システム管理者が管理タスクに費やす時間が短くなります。 たとえば、[OS/RDBMS のインストールおよび修正プログラムの適用](sql-database-high-availability.md)、[動的なインスタンスのサイズ変更および構成](sql-database-single-database-scale.md)、[バックアップ](sql-database-automated-backups.md)、[データベース レプリケーション](replication-with-sql-database-managed-instance.md) (システム データベースを含む)、[高可用性の構成](sql-database-high-availability.md)、正常性および[パフォーマンス監視](../azure-monitor/insights/azure-sql.md)データ ストリームの構成などです。

> [!IMPORTANT]
> サポートされている機能、部分的にサポートされている機能、サポートされていない機能の一覧については、[SQL Database の機能](sql-database-features.md)に関するページを参照してください。 マネージド インスタンスと SQL Server における T-SQL の相違点の一覧については、[マネージド インスタンスと SQL Server における T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関するページを参照してください

### <a name="how-to-programmatically-identify-a-managed-instance"></a>マネージド インスタンスをプログラムで識別する方法

次の表には、Transact SQL を介してアクセスできるプロパティをいくつか示します。これらのプロパティを使用することで、目的のアプリケーションがマネージド インスタンスで動作していることを検出し、重要なプロパティを取得することができます。

|プロパティ|値|Comment (コメント)|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation|この値は SQL Database の値と同じです。|
|`SERVERPROPERTY ('Edition')`|SQL Azure|この値は SQL Database の値と同じです。|
|`SERVERPROPERTY('EngineEdition')`|8|この値では、マネージド インスタンスが一意に識別されます。|
|`@@SERVERNAME`、`SERVERPROPERTY ('ServerName')`|次の形式による完全なインスタンス DNS 名:`<instanceName>`.`<dnsPrefix>`.database.windows.net。ここで、`<instanceName>` は顧客が指定する名前です。`<dnsPrefix>` は名前の中で自動生成される部分であり、グローバルな DNS 名の一意性を保証します (例: "wcus17662feb9ce98")|例: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>次の手順

- 最初のマネージド インスタンスを作成する方法については、[クイック スタート ガイド](sql-database-managed-instance-get-started.md)を参照してください。
- 機能比較一覧については、[SQL 共通機能](sql-database-features.md)に関する記事をご覧ください。
- VNet の構成の詳細については、[マネージド インスタンス VNet の構成](sql-database-managed-instance-connectivity-architecture.md)に関するページを参照してください。
- マネージド インスタンスを作成し、バックアップ ファイルからデータベースを復元するためのクイック スタートについては、[マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- Azure Database Migration Service (DMS) を使用して移行する方法のチュートリアルについては、[DMS を使用したマネージド インスタンスの移行](../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。
- 組み込みのトラブルシューティング インテリジェンスを使用したマネージド インスタンス データベースの高度なパフォーマンス監視については、[Azure SQL Analytics を使用した Azure SQL Database の監視](../azure-monitor/insights/azure-sql.md)に関するページを参照してください。
- 料金情報については、[SQL Database マネージド インスタンスの価格](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。
