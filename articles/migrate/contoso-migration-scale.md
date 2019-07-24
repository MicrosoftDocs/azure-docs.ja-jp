---
title: Contoso - Azure への移行のスケーリング | Microsoft Docs
description: Azure へのスケーリングされた移行を Contoso がどのように処理するかについて説明します。
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: raynew
ms.openlocfilehash: bd1761ecf16bbfb0d3fdc354ab1b9fa1f42f9c17
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328580"
---
# <a name="contoso---scale-a-migration-to-azure"></a>Contoso - Azure への移行のスケーリング

この記事では、Contoso が Azure への大規模な移行を実施します。 それぞれ 3,000、8,000、10,000 を超えるワークロード、データベース、VM の移行をどのように計画し、実行するかを検討します。


この記事は、架空の会社 Contoso がオンプレミスのリソースを Microsoft Azure クラウドに移行する方法について説明するシリーズ記事の 1 つです。 このシリーズでは、背景や計画に関する情報とデプロイ シナリオを示しながら、移行インフラストラクチャを設定する方法、移行のためのオンプレミス リソースの適合性を評価する方法、さまざまな種類の移行を実行する方法を説明します。 シナリオは複雑になってきています。 今後もこのシリーズの記事を追加していく予定です。


**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1:概要](contoso-migration-overview.md) | 記事シリーズ、Contoso の移行戦略およびシリーズで使用されているサンプル アプリの概要です。 | 使用可能
[記事 2: Azure インフラストラクチャをデプロイする](contoso-migration-infrastructure.md) | Contoso がオンプレミス インフラストラクチャと Azure インフラストラクチャを移行に向けて準備します。 このシリーズの移行に関するすべての記事で同じインフラストラクチャを使用します。 | 使用可能。
[記事 3:Azure への移行についてオンプレミスのリソースを評価する](contoso-migration-assessment.md)  | Contoso が、VMware で実行されているオンプレミスの SmartHotel360 アプリを評価します。 Contoso では、アプリの VM は Azure Migrate サービスを使用して評価し、アプリの SQL Server データベースは Data Migration Assistant を使用して評価します。 | 使用可能
[記事 4: Azure VM および SQL Database Managed Instance でのアプリのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が、オンプレミスの SmartHotel360 アプリの Azure へのリフトアンドシフト移行を実行します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行します。 アプリ データベースの Azure SQL Database Managed Instance への移行には、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用します。 | 使用可能   
[記事 5:Azure VM でのアプリのリホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery サービスを使用して SmartHotel360 アプリの VM を Azure VM に移行します。 | 使用可能
[記事 6:Azure VM および SQL Server AlwaysOn 可用性グループでのアプリのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso がアプリを移行します。Site Recovery を使用してアプリの VM を移行し、Database Migration Service を使用してアプリのデータベースを AlwaysOn 可用性グループで保護されている SQL Server クラスターに移行します。 | 使用可能
[記事 7:Linux アプリの Azure VM への再ホスト](contoso-migration-rehost-linux-vm.md) | Contoso が Site Recovery サービスを使用して、Azure VM への Linux osTicket アプリのリフトアンドシフト移行を完了します。 | 使用可能
[記事 8:Azure VM および Azure Database for MySQL での Linux アプリのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Site Recovery を使用して Linux osTicket アプリを Azure VM に移行します。 アプリ データベースを Azure Database for MySQL に移行するために MySQL Workbench を使用します。 | 使用可能
[記事 9:Azure Web App と Azure SQL Database でのアプリのリファクター](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel360 アプリを Azure Web アプリに移行します。また、Database Migration Assistant を使用して、アプリ データベースを Azure SQL Server インスタンスに移行します。 | 使用可能    
[記事 10:Azure Web アプリと Azure Database for MySQL での Linux アプリのリファクター](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Linux osTicket アプリを複数サイト上の Azure Web アプリに移行します。 Web アプリは継続的デリバリー用に GitHub と統合されます。 アプリ データベースを Azure Database for MySQL インスタンスに移行します。 | 使用可能
[記事 11:Azure DevOps サービスでの Team Foundation Server をリファクタリングします](contoso-migration-tfs-vsts.md) | Contoso がそのオンプレミスの Team Foundation Server の展開を Azure の Azure DevOps Services に移行します。 | 使用可能
[記事 12:Azure コンテナーと Azure SQL Database でのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行します。 その後、アプリの Web 階層を Azure Service Fabric 内で動作する Windows コンテナーとして再構築し、さらに、Azure SQL Database を使用してアプリ データベースを再構築します。 | 使用可能    
[記事 13:Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (Azure App Service、Azure Kubernetes Service (AKS)、Azure Functions、Azure Cognitive Services、Azure Cosmos DB など) を使用して SmartHotel アプリをリビルドします。 | 使用可能 
記事 14:Azure への移行のスケーリング | 移行の組み合わせを試した後、Contoso は Azure への完全移行に向けてスケーリングを準備します。 | この記事の内容は次のとおりです。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長を続けており、このためオンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **効率化**: Contoso では、不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門は IT に対して、時間やコストを無駄にせず、迅速に作業を行ってもらう必要があります。これは、例えば、顧客の要求に素早く対応するためです。
- **俊敏性の向上**: Contoso IT は、ビジネス部門のニーズに対する対応力を高める必要があります。 また、グローバル経済で成功を収めるために、市場の変化に対して、より迅速な対応ができる必要があります。 ビジネスの妨げになったり、ビジネスの機会を壊すようなことがあってはなりません。
- **スケール**:ビジネスが順調に成長していく中で、Contoso IT チームは、同じペースで拡張可能なシステムを提供する必要があります。
- **コスト モデルの改善**:Contoso は、IT 予算における資金要件を削減したいと考えています。  Contoso の希望は、クラウドの機能を使用してスケーリングし、高価なハードウェアの必要性を削減することです。
- **ライセンス コストの削減**:Contoso はクラウドのコストを最小限に抑えたいと考えています。


## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定しました。

**要件** | **詳細**
--- | --- 
**Azure への迅速な移行** | Contoso は、Azure へのアプリと VM の移行をできるだけ迅速に開始することを望んでいます。
**完全なインベントリの作成** | Contoso は、組織内にあるすべてのアプリ、データベース、VM の完全なインベントリを求めています。
**アプリの評価と分類** | Contoso は、クラウドを最大限に活用したいと考えています。 既定として Contoso は、すべてのサービスを PaaS として実行することを想定しています。 PaaS が適切でない場合は IaaS を使用します。 
**DevOps のトレーニングと DevOps への移行** | Contoso は DevOps モデルに移行したいと考えています。 Contoso は Azure と DevOps のトレーニングを提供し、必要に応じてチームを再編成します。 


目標と要件を確定した後、Contoso は IT のフットプリントを確認し、移行プロセスを識別します。

## <a name="current-deployment"></a>現在のデプロイ

[Azure のインフラストラクチャ](contoso-migration-infrastructure.md)を計画して設定し、上記の表に詳細を示したさまざまな概念実証 (POC) としての移行の組み合わせを試した後、Contoso は Azure への大規模な完全移行を開始する準備が整いました。 Contoso が移行しようとしている項目は次のとおりです。

**項目** | **数量** | **詳細**
--- | --- | ---
**ワークロード** | 3,000 個以上のアプリ | アプリは VM 上で稼働しています。<br/><br/>  SQL ベースで OSS LAMP の Windows アプリです。
**データベース** | 約 8,500 | データベースは SQL Server、MySQL、PostgreSQL です。
**VM** | 10,000 以上 | VM は VMware ホスト上で稼働しており、vCenter Server によって管理されています。


## <a name="migration-process"></a>移行プロセス

ビジネス ドライバーと移行の目標が明確になったところで、Contoso は移行プロセスとして 4 段階のアプローチを決定しました。

- **フェーズ 1 - 評価**:現在の資産を検出し、現在の資産が Azure への移行に適しているかどうかを確認します。
- **フェーズ 2 - 移行**:資産を Azure に移行します。 アプリとオブジェクトを Azure に移行する方法は、アプリによって、また何を実現したいかによって異なります。
- **フェーズ 3 - 最適化**:Contoso は、リソースを Azure に移行した後、パフォーマンスと効率を最大化するために、移行したリソースを改善し、合理化する必要があります。
- **フェーズ 4 - セキュリティ確保と管理**:すべてが準備できたら、Azure で運用するクラウド アプリの管理、セキュリティ確保、監視のため、Contoso は Azure に用意されているセキュリティと管理のためのリソースを活用します。


これらのフェーズは、組織全体でのシリアルなフェーズではありません。 Contoso の移行プロジェクトの各部分は、評価と移行のプロセスのさまざまな段階にあります。 最適化、セキュリティ、管理は今後も継続的に行われます。


## <a name="phase-1-assess"></a>フェーズ 1:評価

Contoso は、オンプレミスのアプリ、データ、インフラストラクチャを検出、評価して、Azure への移行を開始します。 Contoso が行うことは次のとおりです。

- Contoso はアプリを検出し、アプリ間の依存関係をマップして、移行の順序と優先度を決定する必要があります。
- Contoso は評価を実施しながら、アプリとリソースの包括的なインベントリを構築します。 新しいインベントリとともに、既存の構成管理データベース (CMDB) とサービス カタログを使用し、更新します。
    - CMDB には Contoso アプリの技術的な構成が保存されています。
    - サービス カタログは、関連するビジネス パートナーやサービス レベル アグリーメント (SLA) を含め、アプリの運用に関する詳細を記したドキュメントです。

### <a name="discover-apps"></a>アプリの検出

Contoso では、さまざまなサーバーで何千ものアプリを実行しています。 CMDB とサービス カタログに加えて、Contoso には検出と評価のためのツールが必要です。 

- ツールは、評価データを移行プロセスに取り込むことができるメカニズムを備えている必要があります。
- 評価ツールによって、Contoso の物理リソースと仮想リソースのインテリジェントなインベントリ構築に役立つデータが提供されます。 データにはプロファイル情報とパフォーマンスのメトリックが含まれている必要があります。
- 検出が完了すれば、資産と、資産に関連付けられたメタデータとの完全なインベントリが手に入ります。 このインベントリは、移行計画を定義するために使用されます。

### <a name="identify-classifications"></a>分類の識別

Contoso は、インベントリに含まれる資産を分類するために、いくつかの共通するカテゴリを識別します。 これらの分類は、移行に関する Contoso の意思決定に不可欠です。 分類の一覧は、移行の優先度を決定し、複雑な問題を識別するために役立ちます。

**カテゴリ** | **割り当てられた値** | **詳細**
--- | --- | ---
業務グループ | 業務グループ名の一覧 | インベントリ項目の担当はどのグループか?
POC 候補 | Y/N | アプリをクラウド移行に向けての POC または Early Adopter として使用できるか?
技術的負債 | なし/多少/重大 | インベントリ項目はサポート対象外の製品、プラットフォーム、オペレーティング システムを実行または使用しているか?
ファイアウォールの影響 | Y/N | アプリはインターネット/外部トラフィックと通信するか?  ファイアウォールに統合されているか?
セキュリティの問題 | Y/N | アプリに関する既知のセキュリティの問題はあるか?  アプリは暗号化されていないデータまたは最新でないプラットフォームを使用しているか?


### <a name="discover-app-dependencies"></a>アプリの以前関係の検出

評価プロセスの一環として、Contoso は、アプリがどこで実行されているかを識別し、アプリ サーバー間の依存関係や関係性を把握する必要があります。 Contoso は複数のステップで環境をマップします。

1. 最初のステップとして、サーバーとマシンが個々のアプリ、ネットワークの場所、グループにどのようにマップされているかを検出します。
2. この情報に基づいて、依存関係がほとんどない、したがって迅速な移行に適したアプリを明確に識別できます。
3. マッピングを使用して、アプリ サーバー間での、より複雑な依存関係と通信を簡単に識別できます。 その後それらのサーバーを、アプリを反映するように論理的にグループ化したうえで、グループに基づいて移行戦略を計画します。


マッピングを完了することで、移行計画を作成するときに、Contoso はすべてのアプリ コンポーネントを確実に識別し、考慮に入れることができます。 

![依存関係のマッピング](./media/contoso-migration-scale/dependency-map.png)


### <a name="evaluate-apps"></a>アプリの評価

検出と評価のプロセスの最後のステップとして、Contoso は評価とマッピングの結果を検討し、サービス カタログに含まれる各アプリを移行する方法を見つけることができます。 

この評価プロセスを把握するために、Contoso は追加の分類をいくつかインベントリに加えました。

**カテゴリ** | **割り当てられた値** | **詳細**
--- | --- | ---
業務グループ | 業務グループ名の一覧 | インベントリ項目の担当はどのグループか?
POC 候補 | Y/N | アプリをクラウド移行に向けての POC または Early Adopter として使用できるか?
技術的負債 | なし/多少/重大 | インベントリ項目はサポート対象外の製品、プラットフォーム、オペレーティング システムを実行または使用しているか?
ファイアウォールの影響 | Y/N | アプリはインターネット/外部トラフィックと通信するか?  ファイアウォールに統合されているか?
セキュリティの問題 | Y/N | アプリに関する既知のセキュリティの問題はあるか?  アプリは暗号化されていないデータまたは最新でないプラットフォームを使用しているか?
移行戦略 | リホスト/リファクター/リアーキテクト/リビルド | アプリについて、どのような種類の移行が必要か?  アプリは Azure にどのようにデプロイされるか? [詳細情報](contoso-migration-overview.md#migration-strategies)。
技術的な複雑さ | 1 - 5 | 移行はどの程度複雑か? この値は、Contoso DevOps および関連パートナーによって定義される必要があります。
ビジネス上の重要度 | 1 - 5 | アプリは業務上どのくらい重要か? たとえば、小規模なワークグループ アプリにはスコアとして 1 を割り当て、組織全体で使用する重要なアプリにはスコア 5 を割り当てることができます。 このスコアは、移行の優先度レベルに影響します。
移行の優先度 | 1/2/3 | アプリの移行の優先度はどれぐらいか?
移行のリスク  | 1 - 5 | アプリの移行に関するリスク レベルはどのくらいか? この値については、Contoso DevOps および関連パートナーの合意を得る必要があります。




### <a name="figure-out-costs"></a>コストの把握

Azure への移行のコストと潜在的なコスト削減を把握するために、Contoso は[総保有コスト (TCO) 計算ツール](https://azure.microsoft.com/pricing/tco/calculator/)を使用して Azure の TCO を計算し、同等なオンプレミス デプロイと比較することができます。

### <a name="identify-assessment-tools"></a>評価用ツールの識別

Contoso は、検出、評価、インベントリ作成に使用するツールを決定します。 Contoso が識別したツールには、Azure のツールとサービス、ネイティブ アプリのツールとスクリプト、パートナーのツールが混在しています。 特に、Contoso は大規模な評価に Azure Migrate をどのように使用できるかに関心があります。


#### <a name="azure-migrate"></a>Azure Migrate


Azure Migrate サービスは、Azure への移行準備としてオンプレミスの VMware VM を検出して評価するのに役立ちます。 Azure Migrate の機能は次のとおりです。

1. 検出:オンプレミスの VMware VM を検出します。
    - Azure Migrate は、複数の vCenter サーバーからの検出 (シリアルに) をサポートし、別々の Azure Migrate プロジェクトで検出を実行できます。
    - Azure Migrate は、Migrate Collector を実行している VMware VM を手段として、検出を実行します。 同一の Collector によって異なる複数の vCenter サーバー上の VM を検出し、さまざまなプロジェクトにデータを送信できます。
1. Azure 対応性を評価する:オンプレミスのマシンが Azure での実行に適しているかどうかを評価します。 評価の内容は次のとおりです。
    - 推奨サイズ:オンプレミス VM のパフォーマンス履歴から、Azure VM の推奨サイズが得られます。
    - 月額料金の見積もり:オンプレミスのマシンを Azure で実行するためのコストを見積もります。
2. 依存関係を識別する:評価と移行の対象としての最適なマシン グループを作成できるように、オンプレミスのマシンの依存関係を視覚化します。


![Azure Migrate](./media/contoso-migration-scale/azure-migrate.png)


##### <a name="migrate-at-scale"></a>大規模な移行

Contoso は Azure Migrate を使用して、この移行を正しくスケーリングする必要があります。 

- Contoso は Azure Migrate を使用してアプリごとの評価を行います。 これにより、Azure Migrate から Azure portal に適切なタイミングで確実にデータが返されます。
- Contoso 管理者は[Azure Migrate の大規模なデプロイ](how-to-scale-assessment.md)についての情報を参照します。
- Contoso は次の表に示した Azure Migrate の制限に留意します。


**アクション** | **制限**
--- | ---
Azure Migrate プロジェクトの作成 | 1,500 VM
探索 | 1,500 VM
評価 | 1,500 VM

Contoso は Azure Migrate を次のように使用します。

- vCenter で Contoso は VM をフォルダーに整理します。 これにより、特定のフォルダー内の VM に対して評価を実行するときに、焦点を絞りやすくなります。
- Azure Migrate では Azure Service Map を使用してマシン間の依存関係を評価します。 そのためには、評価する対象の VM 上にエージェントをインストールする必要があります。 
    - Contoso では、自動化されたスクリプトを使用して、必要な Windows または Linux エージェントをインストールします。
    - スクリプトを使用することで、Contoso は vCenter フォルダー内の VM にインストールをプッシュできます。


#### <a name="database-tools"></a>データベース ツール

Azure Migrate に加えて、Contoso はデータベース評価専用のツールを重点的に使用します。 [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) のようなツールは、移行対象の SQL Server データベースを評価するために役立ちます。

Data Migration Assistant (DMA) は、Azure SQL Database、Azure IaaS VM 上で実行されている SQL Server、Azure SQL Managed Instance などの幅広い Azure データベース ソリューションとの互換性がオンプレミス データベースにあるかどうかを把握するのに役立ちます。 

Contoso は DMS に加えて、SQL Server データベースの検出とドキュメント化にその他のスクリプトを使用します。 それらは GitHub リポジトリにあります。

#### <a name="partner-tools"></a>パートナーのツール

Contoso が Azure への移行対象のオンプレミス環境を評価する際に役立つ、その他のパートナー ツールがいくつかあります。 Azure 移行パートナーの詳細については、[こちらを参照してください](https://azure.microsoft.com/migration/partners/)。  

## <a name="phase-2-migrate"></a>フェーズ 2:移行

評価が完了すると、Contoso はアプリ、データ、インフラストラクチャを Azure に移行するためのツールを識別する必要があります。 




### <a name="migration-strategies"></a>移行方法

Contoso はおおまかに次の 4 つの移行方法を戦略として検討できます。 

**戦略** | **詳細** | **使用方法**
--- | --- | ---
**リホスト**  | これは "リフト アンド シフト" による移行と呼ばれることが多く、コードを編集することなく既存のアプリをすばやく Azure に移行できるオプションです。<br/><br/> アプリはそのままの状態で移行されるので、コード変更に伴うリスクやコストなしにクラウドのメリットを享受できます。 | Contoso は、コードの変更が不要な、あまり戦略的でないアプリをリホストすることができません。
**リファクター** |  "再パッケージ化" とも呼ばれるこの戦略は、アプリを Azure PaaS に接続し、クラウド機能を有効に活用するために、最小限のアプリ コードまたは構成の変更を必要とします。 | Contoso は戦略的なアプリをリファクターして同じ基本機能を維持しながら、Azure App Services などの Azure プラットフォーム上で実行するように移行できます。<br/><br/> これには最小限のコード変更が必要です。<br/><br/> 一方、これは Microsoft によって管理されないので、Contoso は仮想マシン プラットフォームを維持する必要があります。
**リアーキテクト** | この戦略では、クラウドの機能とスケールに合わせてアプリのアーキテクチャを最適化するために、アプリケーション コード ベースを変更または拡張します。<br/><br/> 既存のアプリが最先端のものとなり、回復性とスケーラビリティに優れ、単独でのデプロイが可能なアーキテクチャが実現します。<br/><br/> Azure サービスを利用すれば、このプロセスをスピードアップできるほか、自信を持ってアプリケーションをスケーリングすることや、アプリを簡単に管理することが可能となります。
**リビルド** | この戦略では、クラウドネイティブ テクノロジを使って、アプリケーションをゼロからリビルドします。<br/><br/> Azure の PaaS (サービスとしてのプラットフォーム) は、クラウド内に完全な開発およびデプロイ環境を提供します。 それにより、ソフトウェア ライセンスの費用と複雑さが削減され、基盤となるアプリのインフラストラクチャ、ミドルウェア、その他のリソースが不要になります。 | Contoso は、サーバーレスのコンピューターやマイクロサービスなどのクラウド テクノロジを活用するために、重要なアプリケーションを一から書き直すことができます。<br/><br/> Contoso は開発したアプリとサービスを管理し、その他すべては Azure によって管理されます。


また、特に Contoso の持つデータベースの規模を考えて、データについて考慮する必要があります。 Contoso の既定のアプローチでは、Azure SQL Database などの PaaS サービスを使用してクラウドの機能を最大限に活用します。 データベース用の PaaS サービスに移行することによって、Contoso は基盤となるプラットフォームを Microsoft に任せて、データを管理するだけでよくなります。

### <a name="evaluate-migration-tools"></a>移行ツールの評価

移行を行うために Contoso が主に使用する Azure のサービスとツールは次のとおりです。

- [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview):ディザスター リカバリーを調整し、オンプレミスの VM を Azure に移行します。
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview):SQL Server、MySQL、Oracle などのオンプレミス データベースを Azure に移行します。


#### <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery は、ディザスター リカバリーを調整し、Azure 内から、およびオンプレミス サイトから Azure に移行するための主要な Azure サービスです。

1. Site Recovery によってオンプレミス サイトから Azure へのレプリケーションの有効化と調整が行われます。
2. レプリケーションが設定されて実行中の状態になったら、オンプレミス マシンを Azure にフェールオーバーして移行を完了できます。

Contoso は既に [POC を完了](contoso-migration-rehost-vm.md)し、Site Recovery がクラウドへの移行にどのように役立つかを確認済みです。

##### <a name="using-site-recovery-at-scale"></a>Site Recovery の大規模な使用

Contoso は、複数のリフト アンド シフト移行を実行することを計画しています。 これを確実に行うには、Site Recovery によって一度におよそ 100 ずつ VM を一括レプリケートすることになります。 これがどのように機能するかを把握するために、Contoso は提案された Site Recovery による移行について、キャパシティ プランニングを実行する必要があります。

- Contoso は、自社のトラフィックの量に関する情報を収集する必要があります。 具体的には以下のとおりです。
    - Contoso は、レプリケートする対象である VM の変化率を確認する必要があります。
    - また、オンプレミス サイトから Azure へのネットワーク接続を考慮する必要があります。
- キャパシティと数量の要件に応じて、回復ポイントの目標 (RPO) を満たすために、必要な VM に対して、日次データ変化率に基づく十分な帯域幅を割り当てる必要があります。
- 最後に、デプロイに必要な Site Recovery コンポーネントを実行するために必要なサーバーの数を把握する必要があります。

###### <a name="gather-on-premises-information"></a>オンプレミス情報の収集
Contoso は、[Site Recovery Deployment Planner](https://docs.microsoft.com/azure/site-recovery/site-recovery-deployment-planner) ツールを使用して次の手順を完了することができます。

- Contoso はこのツールを使用して、運用環境に影響を与えることなくリモートから VM をプロファイリングすることができます。 これにより、レプリケーションとフェールオーバーの帯域幅とストレージの要件を特定できます。
- このツールは、Site Recovery のコンポーネントを一切オンプレミスにインストールせずに実行することができます。
- このツールによって、互換および非互換の VM、VM あたりのディスク数、およびディスクあたりのデータ チャーンに関する情報が収集されます。 また、ネットワーク帯域幅の要件に加えて、レプリケーションとフェールオーバーを正常に実行するために必要な Azure インフラストラクチャに関する情報も識別されます。
- Contoso は、Planner ツールを実行する Windows Server マシンが確実に Site Recovery 構成サーバーの最小要件に一致しているようにする必要があります。 構成サーバーは、オンプレミスの VMware VM をレプリケートするために必要な Site Recovery マシンです。


###### <a name="identify-site-recovery-requirements"></a>Site Recovery の要件の識別

Site Recovery には、レプリケート対象の VM 以外に、VMware 移行のためのさまざまなコンポーネントが必要です。

**コンポーネント** | **詳細**
--- | ---
**構成サーバー** | 通常は、OVF テンプレートを使用して設定された VMware VM です。<br/><br/> 構成サーバー コンポーネントは、オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理します。
**プロセス サーバー** | 構成サーバーに既定でインストールされます。<br/><br/> プロセス サーバー コンポーネントは、レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure Storage に送信します。<br/><br/> また、プロセス サーバーは、レプリケートする VM への Azure Site Recovery モビリティ サービスのインストールや、オンプレミスのマシンの自動検出も行います。<br/><br/> スケーリングされたデプロイには、大量のレプリケーション トラフィックを処理するために、追加のスタンドアロン プロセス サーバーが必要です。
**モビリティ サービス** | モビリティ サービス エージェントは、Site Recovery を使用して移行する各 VMware VM にインストールします。  

Contoso は、キャパシティに関する考慮事項に基づいて、これらのコンポーネントのデプロイ方法を把握する必要があります。

**コンポーネント** | **キャパシティの要件**
--- | ---
**日次変化率の最大値** | 1 台のプロセス サーバーでは、1 日あたり最大 2 TB の変化率を処理できます。 1 つの VM で使用できるプロセス サーバーは 1 台だけであるため、レプリケートされた VM ごとにサポートされる日次データ変化率の最大値は 2 TB です。
**最大スループット** | Standard の Azure ストレージ アカウントでは、1 秒間に最大 20,000 要求を処理できます。レプリケート元の VM 全体の 1 秒あたりの入出力操作 (IOPS) 数をこの制限内に抑えることをお勧めします。 たとえば、VM に 5 個のディスクが搭載されていて、その VM 上で 120 IOPS (8 K サイズ) が生成される場合、Azure のディスクごとの IOPS 制限である 500 以内に収まります。<br/><br/> 必要なストレージ アカウントの数は、ソース マシンの IOPS 合計を 20,000 で割ったものに等しいことに注意してください。 レプリケートされたマシンは、Azure の 1 つのストレージ アカウントにのみ属することができます。
**構成サーバー** | 100 から 200 の VM をまとめてレプリケートする場合の Contoso の見積もりと、[構成サーバーのサイズ要件](../site-recovery/site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)に基づいて、Contoso の見積もりには次のように構成サーバー マシンが必要です。<br/><br/> CPU:16 vCPU (2 ソケット * 8 コア @ 2.5 GHz)<br/><br/> メモリ:32 GB<br/><br/> キャッシュ ディスク:1 TB (テラバイト)<br/><br/> データ変化率:1 TB から 2 TB まで。<br/><br/> Contoso は、サイズ要件に加えて、構成サーバーが最適に配置されていること、すなわち移行される VM と同じネットワークの同じ LAN セグメントに配置されていることを確認する必要があります。
**プロセス サーバー** | Contoso は、100 から 200 の VM をレプリケートできるスタンドアロンの専用プロセス サーバーをデプロイします。<br/><br/> CPU:16 vCPU (2 ソケット * 8 コア @ 2.5 GHz)<br/><br/> メモリ:32 GB<br/><br/> キャッシュ ディスク:1 TB (テラバイト)<br/><br/> データ変化率:1 TB から 2 TB まで。<br/><br/> プロセス サーバーは負荷が高くなるため、レプリケーションに必要なディスク I/O、ネットワーク トラフィック、CPU 使用率を処理できる ESXi ホストに配置する必要があります。 Contoso は、この目的のために専用ホストを検討します。 
**ネットワーク** | Contoso は現在のサイト間 VPN インフラストラクチャを見直し、Azure ExpressRoute を実装することにしました。 その実装は、待機時間を削減し、Contoso の米国東部 2 プライマリ Azure リージョンの帯域幅を改善するために不可欠です。<br/><br/> **監視**:Contoso はプロセス サーバーからのデータの流れを注意深く監視する必要があります。 データによってネットワーク帯域幅が過負荷になる場合、Contoso は[プロセス サーバーの帯域幅を調整する](../site-recovery/site-recovery-plan-capacity-vmware.md#control-network-bandwidth)ことを検討します。
**Azure Storage** | 移行に際して、Contoso は適切な Azure ストレージ アカウントの種類と数を見極める必要があります。  Site Recovery は、VM のデータを Azure Storage にレプリケートします。<br/><br/> Site Recovery により、Standard または Premium (SSD) ストレージ アカウントにレプリケートできます。<br/><br/> ストレージについて決定するために、Contoso は[容量の上限](../virtual-machines/windows/disks-types.md)を見直し、時間の経過に伴い予想される増加率および使用量の増加を考慮に入れる必要があります。 移行の速度と優先度に従って、Contoso は Premium SSD を使用することを決定しました。<br/><br/>
Contoso は、Azure にデプロイされるすべての VM にマネージド ディスクを使用することに決定しました。  必要な IOPS は、ディスクが Standard HDD、Standard SSD、Premium (SSD) のいずれかによって決まります。<br/><br/>

#### <a name="data-migration-service"></a>データ移行サービス

Azure Database Migration Service (DMS) は、複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を最小限のダウンタイムで実現できる、フル マネージドのサービスです。

- DMS によって既存のツールとサービスの機能が統合されます。 Data Migration Assistant (DMA) を使用して、データベースの互換性に関する推奨事項と必要な変更事項を特定した評価レポートが生成されます。
- DMS では、シンプルなセルフガイド式の移行プロセスが使用されており、潜在的な問題に対する移行前の対処に役立つインテリジェントな評価が提供されます。
- DMS により、複数のソースからターゲットの Azure データベースへ大規模な移行を実施できます。
- DMS は SQL Server 2005 から SQL Server 2017 までのサポートを提供します。

DMS は、唯一の Microsoft データベース移行ツールではありません。 [ツールとサービスの比較](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/)を参照してください。

###### <a name="using-dms-at-scale"></a>DMS の大規模な使用

SQL Server からの移行時に、Contoso は DMS を使用します。  

- DMS をプロビジョニングするとき、Contoso は、DMS が確実に正しくサイズ設定され、データ移行のパフォーマンスを最適化するように設定されているようにする必要があります。 Contoso は "4 個の仮想コアを持つ Business Critical レベル" のオプションを選択します。これにより、そのサービスに複数の vCPU を活用してパラレル化と高速なデータ転送を実現できます。

    ![DMS のスケーリング](./media/contoso-migration-scale/dms.png)

- Contoso のもう 1 つのスケーリング方法は、Azure SQL または MySQL データベースのターゲット インスタンスを、データ移行中は Premium レベル SKU に一時的にスケールアップすることです。 これにより、下位レベルの SKU 使用時にデータ転送活動に影響を与える可能性があるデータベース調整を最小限に抑えることができます。



##### <a name="using-other-tools"></a>その他のツールの使用

DMS に加えて、Contoso は VM 情報を識別するために他のツールやサービスを使用できます。

- 手動での移行に役立つスクリプトがあります。 スクリプトは GitHub リポジトリで入手できます。
- さまざまな[パートナーのツール](https://azure.microsoft.com/migration/partners/)も移行に使用できます。


## <a name="phase-3-optimize"></a>フェーズ 3:最適化

Azure にリソースを移行した後、Contoso はパフォーマンス向上のためにリソースを合理化し、コスト管理ツールを使用して ROI を最大化する必要があります。 Azure は従量課金制のサービスであるため、Contoso にとって、システムのパフォーマンスを把握し、システムが確実に適切なサイズに設定されているようにすることが重要です。


### <a name="azure-cost-management"></a>Azure Cost Management

クラウドへの投資効果を最大化するため、Contoso は無料の Azure Cost Management ツールを活用します。

- ライセンスが付与されたこのソリューションは、Microsoft の子会社である Cloudyn によって開発されたものです。これにより、Contoso は透明性のある正確なクラウド支出管理を行うことができます。  クラウドのコストを監視、割り当て、削減するためのツールを提供します。
- Azure Cost Management では、コストの割り当て、ショーバック、チャージバックに役立つシンプルなダッシュボード レポートを利用できます。
- Cost Management を使うと、使用率が低いリソースを識別して管理、調整することにより、クラウドの支出を最適化できます。
- Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。

    
![コスト管理](./media/contoso-migration-scale/cost-management.png)  
    
 
### <a name="native-tools"></a>ネイティブ ツール

Contoso は、スクリプトを使った未使用のリソースの検出も行います。

- 大規模な移行時には、仮想ハード ドライブ (VHD) などのデータが後に残ることが多く、料金が発生しますが、会社には価値をもたらしません。 GitHub リポジトリでスクリプトを入手できます。
- Contoso は、Microsoft の IT 部門によって行われた作業を活用し、Azure Resource Optimization (ARO) ツールキットを実装することを検討します。
- Contoso は、Runbook とスケジュールがあらかじめ構成された Azure Automation アカウントを自社のサブスクリプションにデプロイし、コストの削減を始めることができます。 Azure リソースの最適化は、スケジュールが有効化または作成された後、サブスクリプションに対して自動的に行われます。新しいリソースに対しても最適化が適用されます。
- これにより、コストを削減する分散的なオートメーション機能が提供されます。 次のような機能が含まれます。
    - 低い CPU 使用率に基づいて Azure VM を自動的に一時停止します。
    - Azure VM の一時停止と一時停止解除のスケジュールを設定します。
    - Azure タグを使用して、Azure VM の一時停止または一時停止解除を昇順および降順に行うスケジュールを設定します。
    - リソース グループをオンデマンドで一括削除します。
- ARO ツールキットの概要については、こちらの [GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit)を参照してください。

### <a name="partner-tools"></a>パートナーのツール

[Hanu](https://hanu.com/insight/) や [Scalr]( https://www.scalr.com/cost-optimization/) などのパートナーのツールを活用することができます。


## <a name="phase-4-secure--manage"></a>フェーズ 4:セキュリティ確保と管理

このフェーズでは、Azure で運用するクラウド アプリの管理、セキュリティ確保、監視のため、Contoso は Azure に用意されているセキュリティと管理のためのリソースを活用します。 ここに挙げたリソースは、Azure portal で利用できる製品を使用して、適切に管理された安全な環境を運用するのに役立ちます。 Contoso は、ここで紹介したサービスを移行の時点から利用できます。また、Azure がハイブリッド環境に対応していることから、それらのサービスの多くはハイブリッド クラウドでも引き続き利用でき、変わらないエクスペリエンスが得られます。


### <a name="security"></a>セキュリティ
Contoso は Azure Security Center を使用して、ハイブリッド クラウド ワークロード全体で、統合されたセキュリティ管理と高度な脅威保護を実現可能です。

- Security Center により、Azure で運用するクラウド アプリのセキュリティを完全に可視化し、コントロールすることができます。
- 適応型脅威保護を有効にすることにより、Contoso は脅威をすばやく検出して対策を講じ、脅威にさらされる危険性を抑えることができます。

Security Center の詳細については、[こちらを参照してください](https://azure.microsoft.com/services/security-center/)。 


### <a name="monitoring"></a>監視

Contoso は、Azure で運用するようになった、新しく移行したアプリ、インフラストラクチャ、データの正常性とパフォーマンスを可視化する必要があります。 Contoso は、Azure Monitor、Log Analytics、Application Insights のような組み込みの Azure クラウド監視ツールを活用します。
 
- これらのツールを使用して、さまざまなソースから簡単にデータを収集し、豊富な分析情報を入手できます。 たとえば、Contoso は VM の CPU ディスク使用量やメモリ使用量を把握し、複数の VM 間のアプリケーションとネットワークの依存関係を可視化することができます。アプリケーションのパフォーマンスを追跡することもできます。
- これらのクラウド監視ツールは、問題の是正に使用することや、サービス ソリューションと統合することも可能です。
- Azure での監視の詳細については、[こちらを参照してください](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)。

### <a name="bcdr"></a>BCDR 

Contoso には、Azure リソースに関する事業継続とディザスター リカバリー (BCDR) 戦略が必要です。

- Azure には、データの安全とアプリやサービスの稼働状態を維持するため、[組み込みの BCDR 機能](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications)が用意されています。 
- 組み込みの機能に加えて、Contoso は、障害からの復旧、コストのかかる業務中断の回避、コンプライアンス目標の達成、ランサムウェアや人為的エラーからのデータの保護を確実にしたいと考えています。 目的
    - Contoso は、Azure のリソースをバックアップするための費用対効果の高いソリューションとして、Azure Backup をデプロイします。 これは組み込みのサービスであるため、Contoso はいくつかのシンプルな手順でクラウドのバックアップを設定できます。
    - Contoso は、指定した Azure リージョン間でのレプリケーション、フェールオーバー、フェールバックを行うために、Azure Site Recovery を使用して Azure VM のディザスター リカバリーを設定します。  これにより、Azure VM 上で実行されているアプリは、プライマリ リージョンに障害が発生した場合には、Contoso が選択したセカンダリ リージョンで引き続き確実に使用することができます。 [詳細情報](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)。

 
## <a name="conclusion"></a>まとめ

この記事では、Contoso が Azure への大規模な移行を計画しました。  移行プロセスを 4 つの段階に分けました。 評価と移行から、移行後の最適化、セキュリティ確保、管理までを完了しました。 ほとんどの場合、移行プロジェクトを全体的な 1 つのプロセスとして計画し、組織内の複数のシステムを業務に適した分類と数に分割して移行することが重要です。 データを評価し、分類を適用することによって、プロジェクトを一連の小規模な移行に分割でき、安全かつ迅速に実行できるようになります。  それらの小規模な移行を合わせた結果が、短期間で Azure への大規模な移行の成功につながります。
