---
title: SQL Server から SQL Managed Instance:移行の概要
description: SQL Server データベースを Azure SQL Managed Instance に移行するために使用できるさまざまなツールとオプションについて説明します。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: ac2b535b2e6b7a6b4169d08dd1768d69e685a216
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562012"
---
# <a name="migration-overview-sql-server-to-sql-managed-instance"></a>移行の概要: SQL Server から SQL Managed Instance
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

SQL Server を Azure SQL Managed Instance に移行するためのさまざまな移行オプションと考慮事項について説明します。 

オンプレミスまたは次で実行されている SQL Server を移行できます。 

- SQL Server on Virtual Machines  
- アマゾン ウェブ サービス (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform - GCP)  
- Cloud SQL for SQL Server (Google Cloud Platform – GCP) 

その他のシナリオについては、[データベース移行ガイド](https://datamigration.microsoft.com/)を参照してください。 

## <a name="overview"></a>概要

[Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md) は、フル マネージド サービスを必要とする SQL Server ワークロードの推奨されるターゲット オプションであり、仮想マシンやそのオペレーティング システムを管理する必要ありません。 SQL Managed Instance を使用すると、アプリケーションやデータベースの変更を最小限に抑えながらオンプレミスのアプリケーションを Azure にリフトアンドシフトできます。その一方で、ネイティブ仮想ネットワーク (VNet) サポートにより、ご使用のインスタンスが完全に分離されます。 

## <a name="considerations"></a>考慮事項 

移行オプションを評価する際に考慮する必要がある主な要因は、次のとおりです。 
- サーバーとデータベースの数
- データベースのサイズ
- 移行プロセス中の許容可能なビジネス ダウンタイム 

SQL Server を SQL Managed Instance に移行する主な利点の 1 つは、インスタンス全体を移行するか、個々のデータベースのサブセットのみを移行するかを選択できる点です。 移行プロセスに次のものを含めるか慎重に計画します。 
- 同じインスタンスに併置する必要があるすべてのデータベース。 
- ログイン、資格情報、SQL エージェント ジョブとオペレーター、サーバー レベルのトリガーなど、アプリケーションに必要なインスタンス レベルのオブジェクト。 

> [!NOTE]
> Azure SQL Managed Instance では、クリティカルなシナリオであっても 99.99% の可用性が保証されるため、SQL MI の一部の機能によるオーバーヘッドを無効にすることはできません。 詳しくは、[SQL Server と Azure SQL Managed Instance でパフォーマンスが異なる根本原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)に関するブログをご覧ください。 


## <a name="choose-appropriate-target"></a>適切なターゲットの選択

[パフォーマンス ベースライン](sql-server-to-managed-instance-performance-baseline.md)への適合のために、適切なサービス レベルと SQL Managed Instance の特性を選択する際に役立つ一般的なガイドラインをいくつか紹介します。 

- CPU 使用率ベースラインを使用して、SQL Server インスタンスが使用するコア数と一致する Managed Instance をプロビジョニングします。 [ハードウェアの世代の特性](../../managed-instance/resource-limits.md#hardware-generation-characteristics)に合わせて、リソースをスケーリングする必要がある場合があります。 
- メモリ使用量ベースラインを使用して、実際のメモリ割り当てに合った[仮想コア オプション](../../managed-instance/resource-limits.md#service-tier-characteristics)を選択します。 
- ファイル サブシステムのベースライン IO 待機時間を使用して、General Purpose (5 ミリ秒を超える待機時間) と Business Critical (3 ミリ秒未満の待機時間) のサービス レベルのいずれかを選択します。 
- ベースライン スループットを使用して、データ ファイルとログ ファイルのサイズを事前に割り当てて、必要な IO パフォーマンスを実現します。 

デプロイ時にコンピューティング リソースとストレージ リソースを選択できます。その後、[Azure portal](../../database/scale-resources.md) を使用して、アプリケーションのダウンタイムなしにそれらを変更できます。 

> [!IMPORTANT]
> [Managed Instance の仮想ネットワーク要件](../../managed-instance/connectivity-architecture-overview.md#network-requirements)の不一致があると、新しいインスタンスを作成することや、既存のインスタンスを使用することができない場合があります。  [新しいネットワークの作成](../../managed-instance/virtual-network-subnet-create-arm-template.md) および [既存のネットワークの構成](../../managed-instance/vnet-existing-add-subnet.md) についてご確認ください。 

Azure SQL Managed Instance でターゲットのサービス レベルを選択する際のもう 1 つの重要な考慮事項 (General Purpose または Business Critical) は、Business Critical レベルでのみ利用できるインメモリ OLTP などの特定の機能を使用できるかどうかという点です。 

### <a name="sql-server-vm-alternative"></a>代替手段 としての SQL Server VM

お客様のビジネスによっては、Azure SQL Managed Instance よりも Azure VM 上の SQL Server の方がより適切なターゲットである場合があります。 

実際のビジネスに次が当てはまる場合は、代わりに SQL Server VM に移行することを検討してください。 

- サードパーティ製のエージェントやカスタム エージェントを SQL Server と同じ仮想マシンにインストールするなど、オペレーティング システムやファイル システムへの直接アクセスが必要な場合。 
- FileStream/FileTable、PolyBase、クロス インスタンス トランザクションなど、まだサポートされていない機能に対する厳密な依存関係がある場合。 
- 特定バージョンの SQL Server (たとえば 2012 など) を確実に維持する必要がある場合。 
- コンピューティング要件がマネージド インスタンスによって提供されるものよりはるかに低く (1 つの仮想コアなど)、データベース統合が許容可能なオプションではない場合。 


## <a name="migration-tools"></a>移行ツール


移行のための推奨ツールは、Data Migration Assistant と Azure Database Migration Service です。 使用できる代替移行オプションが他にもあります。 

### <a name="recommended-tools"></a>推奨されるツール

次の表に、推奨される移行ツールを示します。 

|テクノロジ | 説明|
|---------|---------|
| [Azure Migrate](/azure/migrate/how-to-create-azure-sql-assessment) | VMware の場合、Azure SQL 用の Azure Migrate を使用すると、SQL データ資産を大規模に検出して評価することができます。これにより、Azure SQL デプロイに関する推奨事項、ターゲットのサイズ設定、月単位の見積もりが提供されます。 | 
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md)  | ファースト パーティの Azure サービス。移行プロセス時のダウンタイムを許容できるアプリケーションのオフライン モードでの移行をサポートします。 オンライン モードでの継続的な移行とは異なり、オフライン モードの移行では、ソースからターゲットへのデータベースの完全バックアップの 1 回限りの復元が実行されます。 | 
|[ネイティブ バックアップと復元](../../managed-instance/restore-sample-database-quickstart.md) | SQL Managed Instance では、ネイティブの SQL Server データベース バックアップ (.bak ファイル) の復元がサポートされています。このため、これが Azure Storage にデータベースの完全バックアップを提供できるお客様にとって最も簡単な移行オプションとなります。 完全バックアップと差分バックアップもサポートされており、この記事の後半の「[移行資産](#migration-assets)」セクションで説明しています。| 
|[ログ再生サービス (LRS)](../../managed-instance/log-replay-service-migrate.md) | これは SQL Server ログ配布テクノロジに基づくクラウド サービスであり、Managed Instance に対して有効にすることができます。このため、Azure ストレージに完全、差分、ログのデータベース バックアップを提供できる顧客にとって移行オプションとなります。 LRS は、バックアップ ファイルを Azure Blob Storage から SQL Managed Instance に復元するのに使用されます。| 
| | |

### <a name="alternative-tools"></a>代替ツール

次の表に、代替移行ツールを示します。 

|テクノロジ |説明  |
|---------|---------|
|[トランザクション レプリケーション](../../managed-instance/replication-transactional-overview.md) | トランザクションの一貫性を維持しながら、パブリッシャー/サブスクライバーの種類の移行オプションを指定することによって、ソース SQL Server データベース テーブルから SQL Managed Instance にデータをレプリケートします。 |  |
|[一括コピー](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [一括コピー プログラム (bcp) ユーティリティ](/sql/tools/bcp-utility)では、SQL Server のインスタンスからデータ ファイルにデータがコピーされます。 BCP ユーティリティを使用して、ソースからデータをエクスポートし、ターゲット SQL Managed Instance にデータ ファイルをインポートします。</br></br> Azure SQL Database にデータを移動する高速一括コピー操作の場合、[スマート一括コピー ツール](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/)を使用し、並列コピー タスクを利用して転送速度を最大化できます。 | 
|[インポートおよびエクスポート ウィザード/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) は `.bacpac` 拡張子を持つ Windows ファイルであり、データベースのスキーマとデータがカプセル化されています。 BACPAC を使用すると、ソース SQL Server からデータをエクスポートすることと、そのファイルを Azure SQL Managed Instance にインポートすることの両方ができます。  |  
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| Azure Data Factory の[コピー アクティビティ](../../../data-factory/copy-activity-overview.md)では、組み込みコネクタと [Integration Runtime](../../../data-factory/concepts-integration-runtime.md) を使用して、ソース SQL Server データベースから SQL Managed Instance にデータが移行されます。</br> </br> ADF では、SQL Server ソースから SQL Managed Instance にデータを移動するためのさまざまな[コネクタ](../../../data-factory/connector-overview.md)がサポートされています。 |
| | |

## <a name="compare-migration-options"></a>移行オプションの比較

移行オプションを比較して、ビジネス ニーズに合ったパスを選択します。 

### <a name="recommended-options"></a>推奨されるオプション

次の表では、推奨される移行オプションを比較しています。 

|移行オプション  |使用する場合  |考慮事項  |
|---------|---------|---------|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md) | - 単一のデータベースまたは複数のデータベースを大規模に移行する。 </br> - 移行プロセス中のダウンタイムを許容できる。 </br> </br> サポートされるソース: </br> - オンプレミスの SQL Server (2005 - 2019) または Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM |  - 大規模な移行を [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md) を使用して自動化できます。 </br> - 移行が完了するまでの時間は、データベースのサイズによって異なり、バックアップと復元の時間に左右されます。 </br> - 十分なダウンタイムが必要になる可能性があります。 |
|[ネイティブ バックアップと復元](../../managed-instance/restore-sample-database-quickstart.md) | - 個々の基幹業務アプリケーション データベースを移行する。  </br> - 個別の移行サービスまたはツールを使用せずに素早く簡単に移行する。  </br> </br> サポートされるソース: </br> - オンプレミスの SQL Server (2005 - 2019) または Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM | - データベース バックアップでは、Azure Blob Storage へのデータ転送を最適化するために複数のスレッドが使用されますが、ISV 帯域幅とデータベース サイズが転送速度に影響する可能性があります。 </br> - ダウンタイムには、完全バックアップと復元を実行するために必要な時間 (データ操作のサイズ) を考慮する必要があります。| 
|[ログ再生サービス (LRS)](../../managed-instance/log-replay-service-migrate.md) | - 個々の基幹業務アプリケーション データベースを移行する。  </br> - データベース移行には、さらに多くのコントロールが必要になります。  </br> </br> サポートされるソース: </br> - オンプレミスの SQL Server (2008 - 2019) または Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM | - 移行すると、SQL Server で完全なデータベース バックアップが行われ、Azure Blob Storage にバックアップ ファイルがコピーされます。 LRS は、バックアップ ファイルを Azure Blob Storage から SQL Managed Instance に復元するのに使用されます。 </br> - 移行プロセス中に復元されるデータベースは復元中モードに入り、プロセスが完了するまで読み取りや書き込みに使用することはできません。| 
| | | |

### <a name="alternative-options"></a>代替オプション

次の表では、代替移行オプションを比較しています。 

|方法/テクノロジ |使用する場合 |考慮事項  |
|---------|---------|---------|
|[トランザクション レプリケーション](../../managed-instance/replication-transactional-overview.md) | - ソース データベース テーブルからターゲット SQL Managed Instance データベース テーブルに変更を継続的にパブリッシュすることで移行する。 </br> - データベースを完全に、または選択したテーブル (データベースのサブセット) について部分的に移行する。  </br> </br> サポートされるソース: </br> - SQL Server (2012 - 2019) (一部制限あり) </br> - AWS EC2  </br> - GCP コンピューティングの SQL Server VM | </br> - セットアップは、他の移行オプションと比べて比較的複雑です。   </br> - (データベースをオフラインにせずに) データを移行するための継続的レプリケーション オプションが提供されます。</br> - トランザクション レプリケーションには、ソース SQL Server にパブリッシャーを設定するときに考慮べきいくつかの制限があります。 詳細については、「[オブジェクトのパブリッシュに関する制限事項](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects)」を参照してください。  </br> - [レプリケーション アクティビティを監視](/sql/relational-databases/replication/monitor/monitoring-replication)する機能を利用できます。    |
|[一括コピー](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - 完全または部分的にデータを移行する。 </br> - ダウンタイムを許容できる。 </br> </br> サポートされるソース: </br> - オンプレミスの SQL Server (2005 - 2019) または Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM   | - データをソースからエクスポートするため、およびターゲットにインポートするためのダウンタイムが必要です。 </br> - エクスポート/インポートで使用するファイル形式とデータ型は、テーブル スキーマと一致している必要があります。 |
|[インポートおよびエクスポート ウィザード/BACPAC](../../database/database-import.md)| - 個々の基幹業務アプリケーション データベースを移行する。 </br>- 比較的小規模なデータベースに適している。  </br>  個別の移行サービスやツールを必要としない。 </br> </br> サポートされるソース: </br> - オンプレミスの SQL Server (2005 - 2019) または Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM  |   </br> - データをソースでエクスポートし、転送先でインポートする必要があるため、ダウンタイムが必要です。   </br> - 切り捨てやデータ型不一致のエラーを回避するために、エクスポート/インポートで使用されるファイル形式とデータ型は、テーブル スキーマと一致している必要があります。 </br> - 多数のオブジェクトを含むデータベースをエクスポートする場合、エクスポート時間が大幅に長くなる可能性があります。 |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| - ソース SQL Server データベースからデータを移行/変換する。</br> - 複数のデータ ソースのデータを Azure SQL Managed Instance にマージする (通常はビジネス インテリジェンス (BI) ワークロードの目的で)。   </br> - ソースからターゲットにデータを移動するために、ADF でデータ移動パイプラインを作成する必要がある。   </br> - [コスト](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)が重要な考慮事項であり、パイプライン トリガー回数、アクティビティの実行数、データ移動の期間などに応じて変わる。 |
| | | |

## <a name="feature-interoperability"></a>機能の相互運用性 

他の SQL Server 機能に依存するワークロードを移行する場合は、追加の考慮事項があります。 

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services

[Azure Database Migration Service (DMS)](../../../dms/how-to-migrate-ssis-packages-managed-instance.md) を使用して、SSISDB の SQL Server Integration Services (SSIS) パッケージとプロジェクトを Azure SQL Managed Instance に移行します。 

移行でサポートされるのは、SQL Server 2012 以降の SSISDB の SSIS パッケージのみです。 移行前に従来の SSIS パッケージを変換します。 詳細については、[プロジェクト変換のチュートリアル](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model)に関する記事を参照してください。 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

SQL Server Reporting Services (SSRS) のレポートを、Power BI のページ分割されたレポートに移行できます。 レポートの準備と移行には、 [RDL Migration ツール](https://github.com/microsoft/RdlMigration) が役立ちます。 このツールは、ユーザーが SSRS サーバーから Power BI に RDL レポートを移行するのを助けるため、Microsoft によって開発されました。 これは GitHub で入手でき、移行シナリオのエンドツーエンドのチュートリアル文書も使用できます。 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server 2012 以降の SQL Server Analysis Services テーブル モデルは Azure Analysis Services に移行できます。これは、Azure の Analysis Services テーブル モデルの PaaS デプロイ モデルです。 Azure Analysis Services へのオンプレミス モデルの移行について詳しくは、こちらの[ビデオ チュートリアル](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/)を参照してください。

また、[新しい XMLA 読み取り/書き込みエンドポイントを使用して Power BI Premium ](/power-bi/admin/service-premium-connect-tools)にオンプレミスの Analysis Services テーブル モデルを移行することもできます。 
> [!NOTE]
> Power BI XMLA 読み取り/書き込みエンドポイント機能は現在パブリック プレビュー段階です。この機能が一般公開されるまでは、運用環境ワークロード向けに検討しないでください。

#### <a name="high-availability"></a>高可用性

高可用性アーキテクチャは [General Purpose (Standard 可用性モデル)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) と [Business Critical (Premium 可用性モデル)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) の SQL Managed Instance に既に組み込まれているため、SQL Server 高可用性機能の Always On フェールオーバー クラスター インスタンスと Always On 可用性グループは、ターゲット Azure SQL Managed Instance では非推奨となります。 また、Premium 可用性モデルでは、読み取り専用の目的で、セカンダリ ノードのいずれかに接続できる読み取りスケールアウトも提供されます。     

SQL Managed Instance に含まれる高可用性アーキテクチャに加えて、[自動フェールオーバー グループ](../../database/auto-failover-group-overview.md)機能もあります。これを使用すると、マネージド インスタンス内のデータベースの別リージョンへのレプリケーションとフェールオーバーを管理できます。 

#### <a name="sql-agent-jobs"></a>SQL エージェント ジョブ

オフライン Azure Database Migration Service (DMS) オプションを使用して [SQL エージェント ジョブ](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)を移行します。 これを行わない場合、SQL Server Management Studio を使用して Transact-SQL (T-SQL) でジョブをスクリプト化し、その後、ターゲット SQL Managed Instance でそれらを手動で再作成します。 

> [!IMPORTANT]
> 現時点では、Azure DMS では、T-SQL サブシステムのステップを使用したジョブのみサポートされています。 SSIS パッケージのステップを使用したジョブは、手動で移行する必要があります。 

#### <a name="logins-and-groups"></a>ログインとグループ

ソース SQL Server からの SQL ログインは、Database Migration Service (DMS) を使用してオフライン モードで Azure SQL Managed Instance に移動できます。  **移行ウィザード** で **[[ログインの選択]](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)** ブレードを使用して、ログインをターゲット SQL Managed Instance に移行します。 

既定では Azure Database Migration Service でサポートされているのは、SQL ログインの移行のみです。 ただし、次の方法により Windows ログインを移行する機能を有効にすることができます。

ターゲット SQL Managed Instance に Azure AD 読み取りアクセス権が付与されていることを確認します。これは、**全体管理者** のロールを持つユーザーが Azure portal を介して構成できます。
Azure portal ([構成] ページ) を介して設定された Windows ユーザーまたはグループのログイン移行を有効にするように、ご利用の Azure Database Migration Service インスタンスを構成します。 この設定を有効にしたら、サービスを再起動して変更を有効にします。

サービスを再起動すると、Windows ユーザーまたはグループのログインが、移行可能なログインの一覧に表示されます。 移行する Windows ユーザーまたはグループのログインについては、関連付けられているドメイン名を指定するように求められます。 サービス ユーザー アカウント (ドメイン名 NT AUTHORITY を含むアカウント) と仮想ユーザー アカウント (ドメイン名 NT SERVICE を含むアカウント名) はサポートされていません。

詳細については、[T-SQL を使用して SQL Server インスタンスの Windows ユーザーとグループを Azure SQL Managed Instance に移行する方法](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md)に関する記事を参照してください。

また、Microsoft データ移行アーキテクトが特別に設計した [PowerShell ユーティリティ ツール](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)も使用できます。 このユーティリティは、ログインを再作成し、ソースからターゲットへのデータベース ユーザーを選択するために、PowerShell を使用して T-SQL スクリプトを作成します。 このツールは、Windows AD アカウントを Azure AD アカウントに自動的にマップし、ソース Active Directory に対してログインごとに UPN 参照を実行できます。 このツールでは、ロール メンバーシップ、データベース ロール、およびユーザー アクセス許可に加えて、カスタム サーバー ロールとデータベース ロールのスクリプトを作成できます。 包含データベースは現在サポートされていません。使用可能な SQL Server アクセス許可のサブセットのみがスクリプト化されます。 

#### <a name="encryption"></a>暗号化

ネイティブ復元オプションを使用して、 [Transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md)  によって保護されているデータベースを Managed Instance に移行する場合、データベースの復元 "*前*" に、ソース SQL Server からターゲット SQL Managed Instance に [対応する証明書を移行します](../../managed-instance/tde-certificate-migrate.md)。 

#### <a name="system-databases"></a>システム データベース

システム データベースの復元はサポートされていません。 (マスターまたは msdb のデータベースに格納されている) インスタンス レベルのオブジェクトを移行するには、Transact-SQL (T-SQL) を使用してそれらをスクリプト化し、ターゲット Managed Instance でそれらを再作成します。 

#### <a name="in-memory-oltp-memory-optimized-tables"></a>インメモリ OLTP (メモリ最適化テーブル)

SQL Server にはインメモリ OLTP 機能があります。これを使用すると、メモリ最適化テーブル、メモリ最適化テーブル型、ネイティブにコンパイルされた SQL モジュールなどを利用して、高いスループットと短い待機時間というトランザクション処理要件を持つワークロードを実行することができます。 

> [!IMPORTANT]
> インメモリ OLTP は、Azure SQL Managed Instance の Business Critical レベルでのみサポートされています (General Purpose レベルではサポートされていません)。

オンプレミスの SQL Server にメモリ最適化テーブルまたはメモリ最適化テーブル型がある場合、Azure SQL Managed Instance に移行する際には、次のいずれかを行う必要があります。

- インメモリ OLTP がサポートされているターゲット Azure SQL Managed Instance の Business Critical レベルを選択します。または
- Azure SQL Managed Instance の General Purpose レベルに移行する場合は、データベースを移行する前に、メモリ最適化テーブル、メモリ最適化テーブル型、およびメモリ最適化オブジェクトと対話する、ネイティブにコンパイルされた SQL モジュールを削除します。 次の T-SQL クエリを使用すると、General Purpose レベルに移行する前に削除する必要があるすべてのオブジェクトを識別できます。

```tsql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

インメモリ テクノロジの詳細については、「[Azure SQL Database と Azure SQL Managed Instance でインメモリ テクノロジを使用してパフォーマンスを最適化する](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-overview)」を参照してください。

## <a name="leverage-advanced-features"></a>高度な機能を活用する 

SQL Managed Instance によって提供されるクラウド ベースの高度な機能を活用してください。 たとえば、サービスによってバックアップが自動的に行われるため、バックアップの管理について心配する必要はなくなります。 [保持期間内の特定の時点](../../database/recovery-using-backups.md#point-in-time-restore)に復元できます。 さらに、 [高可用性が組み込まれている](../../database/high-availability-sla.md)ため、高可用性の設定について心配する必要もありません。 

セキュリティを強化するために、 [Azure Active Directory 認証](../../database/authentication-aad-overview.md)、[監査](../../managed-instance/auditing-configure.md)、 [脅威の検出](../../database/azure-defender-for-sql.md)、 [行レベル セキュリティ](/sql/relational-databases/security/row-level-security)、 [動的データ マスキング](/sql/relational-databases/security/dynamic-data-masking)の使用を検討してください。

高度な管理とセキュリティの機能に加えて、SQL Managed Instance では[ワークロードの監視とチューニング](../../database/monitor-tune-overview.md)に役立つ高度なツールのセットが提供されます。 [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) を使用すると、多数のマネージド インスタンスを一元的に監視できます。マネージド インスタンスの [自動チューニング](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) では、SQL プランの実行統計のパフォーマンスが継続的に監視されて、識別されたパフォーマンスの問題が自動的に修正されます。 

一部の機能は、[データベース互換レベル](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)を最新の互換性レベル (150) に変更した場合にのみ使用できます。 

## <a name="migration-assets"></a>移行資産 

さらに支援が必要な場合は、実際の移行プロジェクトのために開発された次のリソースを参照してください。

|Asset  |説明  |
|---------|---------|
|[データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な不動産評価を加速させることができます。|
|[DBLoader ユーティリティ](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader を使用すると、区切りテキスト ファイルから SQL Server にデータを読み込むことができます。 この Windows コンソール ユーティリティは、SQL Server のネイティブ クライアント一括読み込みインターフェイスを使用します。これは、Azure SQL MI を含むすべてのバージョンの SQL Server で機能します。|
|[オンプレミスの SQL Server ログインを Azure SQL Managed Instance に移動するユーティリティ](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|ログインを再作成し、オンプレミス SQL Server から Azure SQL Managed Instance に移行するデータベース ユーザーを選択するための T-SQL コマンド スクリプトを作成する PowerShell スクリプト。 このツールを使用すると、Windows AD アカウントを Azure AD アカウントに自動的にマッピングできるだけでなく、必要に応じて SQL Server ネイティブ ログインを移行することもできます。|
|[Logman を使用した Perfmon データ収集の自動化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|ベースライン パフォーマンスを把握するための Perfmon データを収集して、移行ターゲットの推奨に役立てるツール。 このツールは、リモート SQL Server で設定されたパフォーマンス カウンターを作成、開始、停止、削除するコマンドを作成するために、logman.exe を使用します。|
|[ホワイトペーパー - 完全および差分のバックアップを復元して Azure SQL Managed Instance にデータベースを移行する](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|このホワイトペーパーが示すガイダンスと手順に従うと、完全および差分のバックアップのみ (ログ バックアップ機能なし) の場合、SQL Server から Azure SQL Managed Instance への移行を高速化できます。|

これらのリソースは、Azure Data Group エンジニアリング チームがスポンサーである Data SQL Ninja プログラムの一部として開発されました。 Data SQL Ninja プログラムの中核となるのは、複雑なモダン化のブロックを解除して加速し、データ プラットフォームを Microsoft の Azure Data プラットフォームに移行する機会を獲得することです。 組織が Data SQL Ninja プログラムへの参加に関心があると思われる場合は、アカウント チームに連絡し、申請を提出するよう依頼してください。


## <a name="next-steps"></a>次のステップ

Azure SQL Managed Instance への SQL Server の移行を開始するには、「[SQL Server から SQL Managed Instance へ - 移行ガイド](sql-server-to-managed-instance-guide.md)」を参照してください。

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールの一覧については、[データ移行のサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- Azure SQL Managed Instance の詳細については、次を参照してください。
   - [Azure SQL Managed Instance のサービス レベル](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server と Azure SQL Managed Instance の相違点](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 


- クラウド移行のためのフレームワークと導入サイクルの詳細については、以下を参照してください
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- アプリケーション アクセス層を評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください。
- データ アクセス レイヤーの A/B テストの実行方法について詳しくは、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) についてのページを参照してください。
