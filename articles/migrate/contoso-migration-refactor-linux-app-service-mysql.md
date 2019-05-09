---
title: Contoso Linux サービス デスク アプリを Azure App Service と Azure MySQL にリファクタリングする | Microsoft Docs
description: Contoso が Web 層向けの GitHub を使用する Azure App Service と Azure SQL Database にオンプレミスの Linux アプリを移行することによって、それをリファクタリングする方法を説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 4ff3f129838a43bd7684dc10e1653dab969e9c1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087008"
---
# <a name="contoso-migration-refactor-a-contoso-linux-service-desk-app-to-multiple-regions-with-azure-app-service-traffic-manager-and-azure-mysql"></a>Contoso の移行: Azure App Service、Traffic Manager、Azure MySQL を使用して複数のリージョンに Contoso Linux サービス デスク アプリをリファクターする

この記事では、Contoso が GitHub と統合された Azure App Service と Azure MySQL にオンプレミスの 2 階層 Linux サービス デスク アプリ (osTicket) を移行することによって、それをリファクタリングする方法を示します。

このドキュメントは、架空の会社 Contoso がオンプレミス リソースを Microsoft Azure クラウドに移行する方法を説明するシリーズ記事の 1 つです。 このシリーズには、背景情報とシナリオが含まれており、移行インフラストラクチャのセットアップ方法や、さまざまな種類の移行を実施する方法を具体的に説明しています。 シナリオは複雑になってきています。 今後、徐々に記事を追加する予定です。

**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1:概要](contoso-migration-overview.md) | 記事シリーズ、Contoso の移行戦略およびシリーズで使用されているサンプル アプリの概要です。 | 使用可能
[記事 2: Azure インフラストラクチャをデプロイする](contoso-migration-infrastructure.md) | Contoso がオンプレミス インフラストラクチャと Azure インフラストラクチャを移行に向けて準備します。 このシリーズの移行に関するすべての記事で同じインフラストラクチャを使用します。 | 使用可能
[記事 3: Azure への移行についてオンプレミスのリソースを評価する](contoso-migration-assessment.md)  | Contoso が、VMware で実行されているオンプレミスの SmartHotel360 アプリを評価します。 Contoso では、アプリの VM は Azure Migrate サービスを使用して評価し、アプリの SQL Server データベースは Data Migration Assistant を使用して評価します。 | 使用可能
[記事 4: Azure VM および SQL Database Managed Instance でのアプリのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が、オンプレミスの SmartHotel360 アプリの Azure へのリフトアンドシフト移行を実行します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行します。 アプリ データベースの Azure SQL Database Managed Instance への移行には、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用します。 | 使用可能   
[記事 5:Azure VM でのアプリのリホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery サービスを使用して SmartHotel360 アプリの VM を Azure VM に移行します。 | 使用可能
[記事 6:Azure VM 上および SQL Server AlwaysOn 可用性グループ内でアプリをリホストする](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel360 アプリを移行します。 Contoso は、Site Recovery を使用してアプリの VM を移行します。 Contoso は、Database Migration Service を使用して、AlwaysOn 可用性グループで保護されている SQL Server クラスターにアプリのデータベースを移行します。 | 使用可能 
[記事 7:Azure VM での Linux アプリのリホスト](contoso-migration-rehost-linux-vm.md) | Contoso が Azure Site Recovery を使用して Azure VM への Linux osTicket アプリのリフトアンドシフト移行を行います | 使用可能
[記事 8:Linux アプリを Azure VM 上と Azure MySQL 上でリホストする](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Azure Site Recovery を使用して Linux osTicket アプリを Azure VM に移行する方法、および MySQL Workbench を使用してアプリのデータベースを Azure MySQL Server インスタンスに移行します。 | 使用可能
[記事 9: Azure Web Apps と Azure SQL Database でアプリをリファクタリングする](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel360 アプリを Azure Web アプリに移行します。また、Database Migration Assistant を使用して、アプリ データベースを Azure SQL Server インスタンスに移行します。 | 使用可能
記事 10:Azure Web Apps と Azure MySQL で Linux アプリをリファクタリングする | Contoso が Azure Traffic Manager を使用し、その Linux osTicket アプリを、複数の Azure リージョンの Azure Web アプリに移行します。この Azure Web アプリは、継続的デリバリーを目的として GitHub と統合されます。 Contoso は、アプリ データベースを Azure Database for MySQL インスタンスに移行します。 | この記事の内容は次のとおりです。
[記事 11:Azure DevOps Services で TFS をリファクタリングする](contoso-migration-tfs-vsts.md) | Contoso がそのオンプレミスの Team Foundation Server の展開を Azure の Azure DevOps Services に移行します。 | 使用可能
[記事 12: Azure Containers と Azure SQL Database でアプリを再構築する](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行します。 その後、アプリの Web 階層を Azure Service Fabric 内で動作する Windows コンテナーとして再構築し、さらに、Azure SQL Database を使用してデータベースを再構築します。 | 使用可能
[記事 13:Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (Azure App Service、Azure Kubernetes Service (AKS)、Azure Functions、Azure Cognitive Services、Azure Cosmos DB など) を使用して SmartHotel360 アプリをリビルドします。 | 使用可能
[記事 14:Azure への移行のスケーリング](contoso-migration-scale.md) | 移行の組み合わせを試した後、Contoso は Azure への完全移行に向けてスケーリングを準備します。 | 使用可能

この記事で Contoso は、2 階層の Linux Apache MySQL PHP (LAMP) サービスデスク アプリ (osTicket) を Azure に移行します。 このオープンソース アプリを使用したい場合は、[GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。


## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らが何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長し続けており、新しい市場に進出しつつあります。 そのため顧客サービス担当者を追加する必要があります。 
- **スケール**:Contoso は、事業規模が拡大したときに、顧客サービス担当者を追加できるように、ソリューションを構築する必要があります。
- **回復性の向上**:過去にシステムで発生した問題は、内部ユーザーのみに影響していました。 新しいビジネス モデルでは、外部ユーザーも影響を受けるため、Contoso は、アプリを常時稼働させる必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、最適な移行方法を決定するために、この移行の目標を明確にしました。

- アプリケーションは、現在のオンプレミスのキャパシティとパフォーマンスを越えるようにスケーリングする必要があります。  Contoso は、Azure のオンデマンド スケーリングを活用するためにアプリケーションを移行します。
- Contoso は、アプリのコード ベースを継続的デリバリー パイプラインに移したいと考えています。  アプリの変更が GitHub にプッシュされたときに、運用スタッフのタスクなしでその変更をデプロイしたいと考えています。
- アプリケーションは、成長とフェールオーバーに対応するための機能を備えた回復力のあるものにする必要があります。 Contoso は 2 つの異なる Azure リージョンにアプリをデプロイし、自動的にスケーリングするように設定したいと考えています。
- Contoso は、アプリケーションがクラウドに移された後、データベース管理タスクを最小限にしたいと考えています。

## <a name="solution-design"></a>ソリューション設計
Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。


## <a name="current-architecture"></a>現在のアーキテクチャ

- アプリは 2 つの VM (OSTICKETWEB および OSTICKETMYSQL) にわたって階層化されます。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上に配置されます。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。

![現在のアーキテクチャ](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png) 


## <a name="proposed-architecture"></a>提案されたアーキテクチャ

次のアーキテクチャが提案されます。

- OSTICKETWEB 上の Web 層のアプリは、Azure App Service を 2 つの Azure リージョン内にビルドすることで移行されます。 Azure App Service for Linux は、PHP 7.0 Docker コンテナーを使用して実装されます。
- アプリ コードは GitHub に移動され、Azure Web アプリは GitHub を使用した継続的デリバリー用に構成されます。
- Azure アプリ サーバーは、プライマリ リージョン (米国東部 2) とセカンダリ リージョン (米国中部) の両方にデプロイされます。
- 両方のリージョンで、2 つの Azure Web Apps の前に Traffic Manager が設定されます。
- Traffic Manager は、トラフィックを強制的に米国東部 2 を通過させるために、優先モードで構成されます。
- 米国東部 2 リージョン内の Azure アプリ サーバーがオフラインになった場合でも、ユーザーは、米国中部リージョンにフェールオーバーされたアプリにアクセスできます。
- アプリのデータベースは、MySQL Workbench ツールを使用して Azure MySQL PaaS サービスに移行されます。 オンプレミスのデータベースはローカルでバックアップされ、Azure MySQL に直接復元されます。
- このデータベースは、プライマリ リージョンである米国東部 2 で、運用ネットワーク (VNET-PROD-EUS2) のデータベース サブネット (PROD-DB-EUS2) に存在します。
- 運用ワークロードを移行しようとしているため、アプリの Azure リソースは、運用リソース グループ **ContosoRG** 内に存在することになります。
- Traffic Manager リソースは、Contoso のインフラストラクチャ リソース グループ **ContosoInfraRG** にデプロイされます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。


![シナリオのアーキテクチャ](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png) 


## <a name="migration-process"></a>移行プロセス

Contoso は、次のようにして移行プロセスを完了します。

1. 最初のステップとして、Contoso 管理者は、Azure インフラストラクチャをセットアップします。これには、Azure App Services のプロビジョニング、Traffic Manager の設定、および Azure MySQL インスタンスのプロビジョニングが含まれます。
2. Azure の準備ができたら、MySQL Workbench を使用してデータベースを移行します。 
3. Azure でデータベースが実行されたら、継続的デリバリーで使用する Azure App Service 用の GitHub プライベート リポジトリを設定し、それを osTicket アプリで読み込みます。
4. Azure Portal で、Azure App Service を実行する Docker コンテナーに GitHub からアプリを読み込みます。 
5. DNS の設定を調整し、アプリの自動スケーリングを構成します。

![移行プロセス](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png) 


### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Mobile Apps](https://azure.microsoft.com/services/app-service/) | このサービスでは、Web サイト向けの Azure PaaS サービスを使用してアプリケーションを実行およびスケーリングします。  | 価格は、インスタンスのサイズと必要な機能に基づきます。 [詳細情報](https://azure.microsoft.com/pricing/details/app-service/windows/)。
[Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) | DNS を使用して、Azure、外部 Web サイト、またはサービスにユーザーを送るロード バランサー。 | 価格は、受信した DNS クエリの数と監視対象のエンドポイントの数に基づきます。 [詳細情報](https://azure.microsoft.com/pricing/details/traffic-manager/)。
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | データベースは、オープン ソースの MySQL Server エンジンに基づいています。 これは、アプリの開発とデプロイに向けたサービスとしての、フルマネージドのエンタープライズ対応コミュニティ MySQL データベースです。 | 価格は、コンピューティング、ストレージ、およびバックアップ要件に基づきます。 [詳細情報](https://azure.microsoft.com/pricing/details/mysql/)。

 
## <a name="prerequisites"></a>前提条件

このシナリオを実行するために Contoso に必要なものを以下に示します。

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | Contoso は、この記事シリーズの前の記事でサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。 
**Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。



## <a name="scenario-steps"></a>シナリオのステップ

Contoso は、次のようにして移行を完了します。

> [!div class="checklist"]
> * **手順 1:Azure App Services をプロビジョニングする**:Contoso 管理者は、プライマリ リージョンとセカンダリ リージョンに Web Apps をプロビジョニングします。
> * **手順 2:Traffic Manager を設定する**:トラフィックのルーティングと負荷分散を行う Traffic Manager を Web アプリの前に設定します。
> * **手順 3:MySQL をプロビジョニングする**:Azure で、Azure MySQL データベースのインスタンスをプロビジョニングします。
> * **手順 4:データベースを移行する**:MySQL Workbench を使用してデータベースを移行します。 
> * **手順 5:GitHub を設定する**:アプリの Web サイト/コード用のローカル GitHub リポジトリを設定します。
> * **手順 6:Web アプリをデプロイする**:GitHub から Web アプリをデプロイします。




## <a name="step-1-provision-azure-app-services"></a>手順 1:Azure App Services をプロビジョニングする

Contoso 管理者は、Azure App Services を使用する 2 つの Web アプリを (各リージョンに 1 つずつ) プロビジョニングします。

1. Azure Marketplace からプライマリ リージョンである米国東部 2 内に Web App リソース (**osticket eus2**) を作成します。
2. 運用リソース グループ **ContosoRG** にリソースを配置します。

    ![Azure アプリ](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png) 

3. プライマリ リージョンで、標準サイズを使用する新しい App Service プラン (**APP-SVP-EUS2**) を作成します。

     ![Azure アプリ](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png) 
    
4. Contoso は、Docker コンテナーである PHP 7.0 ランタイム スタックを使用する Linux OS を選択します。

    ![Azure アプリ](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png) 

5. 米国中部リージョンで、2 つ目の Web アプリ (**osticket-cus**) と App Service プランを作成します。

    ![Azure アプリ](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png) 


**さらにサポートが必要な場合**

- 「[Web Apps の概要](https://docs.microsoft.com/azure/app-service/overview)」を参照します。
- 「[Azure App Service on Linux の概要](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)」を参照します。


## <a name="step-2-set-up-traffic-manager"></a>手順 2:Traffic Manager を設定する

Contoso 管理者は、受信 Web 要求を osTicket Web 層で実行中の Web Apps に送る Traffic Manager を設定します。

1. Azure Marketplace から Traffic Manager リソース (**osticket.trafficmanager.net**) を作成します。 米国東部 2 がプライマリ サイトになるように、優先順位によるルーティングを使用します。 そのリソースを、Contoso のインフラストラクチャ リソース グループ (**ContosoInfraRG**) 内に配置します。 Traffic Manager はグローバルであり、特定の場所にバインドされないことに注意してください。

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png) 

2. 次は Traffic Manager のエンドポイントを構成します。 米国東部 2 の Web アプリをプライマリ サイト (**osticket eus2**) として追加し、米国中部のアプリをセカンダリ (**osticket cu**) として追加します。

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png) 

3. エンドポイントを追加した後、それらを監視できます。

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**さらにサポートが必要な場合**

- 「[Traffic Manager の概要](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)」を参照します。
- [優先順位の高いエンドポイントへのトラフィックのルーティング](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method)に関する記事を参照します。
 
## <a name="step-3-provision-azure-database-for-mysql"></a>手順 3:Azure Database for MySQL をプロビジョニングする

Contoso 管理者は、MySQL データベース インスタンスを、プライマリの米国東部 2 リージョン内にプロビジョニングします。

1. Azure Portal で、Azure Database for MySQL のリソースを作成します。 

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. Azure データベースの名前 **contosoosticket** を追加します。 運用リソース グループ **ContosoRG** にデータベースを追加し、そのための資格情報を指定します。
3. オンプレミスの MySQL データベースはバージョン 5.7 なので、互換性のためにこのバージョンが選択されています。 既定のサイズを使用しますが、それらはデータベースの要件に適合しています。

     ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. **バックアップ冗長オプション**については、**Geo 冗長**を使用することを選択しています。 このオプションでは、停止が発生した場合、セカンダリの米国中部リージョンでデータベースを復元できます。 このオプションは、データベースをプロビジョニングするときにのみ構成できます。

    ![冗長性](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

4. 接続のセキュリティを設定します。 データベースの **[接続のセキュリティ]** で、データベースによる Azure サービスへのアクセスを許可するファイアウォール ルールを設定します。
5. 開始 IP アドレスと終了 IP アドレスに、ローカル ワークステーションのクライアント IP アドレスを追加します。 これにより、Web アプリが MySQL データベースと、移行を実行するデータベース クライアントにアクセスできるようになります。

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)



## <a name="step-4-migrate-the-database"></a>手順 4:データベースを移行する

Contoso 管理者は、MySQL ツールでバックアップと復元を使用して、データベースを移行します。 MySQL Workbench をインストールし、OSTICKETMYSQL からデータベースをバックアップして、Azure Database for MySQL Server に復元します。

### <a name="install-mysql-workbench"></a>MySQL Workbench のインストール

1. [MySQL Workbench の前提条件を確認してダウンロード](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool)します。
2. [インストールの指示](https://dev.mysql.com/doc/workbench/en/wb-installing.html)に従って、MySQL Workbench for Windows をインストールします。 インストール先のマシンは、OSTICKETMYSQL VM と、インターネット経由で Azure にアクセス可能である必要があります。
3. MySQL Workbench で、OSTICKETMYSQL への MySQL 接続を作成します。 

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. データベースを **osticket** として、ローカルの自己完結型ファイルにエクスポートします。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. データベースがローカルにバックアップされたら、Azure Database for MySQL インスタンスへの接続を作成します。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. これで、自己完結型ファイルから、Azure MySQL インスタンス内にあるデータベースをインポート (復元) できます。 このインスタンスのために新しいスキーマ (osticket) が作成されます。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. データが復元されると Workbench を使用してクエリを実行でき、Azure Portal に表示されます。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. 最後に、Web アプリ上のデータベース情報を更新する必要があります。 MySQL インスタンスで、**[接続文字列]** を開きます。 

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. 文字列の一覧で、Web アプリの設定を見つけ、クリックしてそれらをコピーします。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. メモ帳ウィンドウを開き、新しいファイルに文字列を貼り付け、osticket データベース、MySQL インスタンス、および資格情報の設定と一致するように文字列を更新します。

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Azure portal で MySQL インスタンスの**概要**からサーバー名とのログインを確認できます。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)


## <a name="step-5-set-up-github"></a>手順 5:GitHub を設定する

Contoso 管理者は、新しいプライベート GitHub リポジトリを作成し、Azure MySQL の osTicket データベースへの接続を設定します。 次に、Azure Web Apps とアプリを読み込みます。  

1.  OsTicket ソフトウェアのパブリック GitHub リポジトリを参照し、Contoso GitHub アカウントにフォークします。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. フォークしたら、**include** フォルダーに移動し、**ost-config.php** ファイルを検索します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)


3. ブラウザーでファイルを開いて編集します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. エディターで、データベースの詳細 (具体的には **DBHOST** と **DBUSER**) を更新します。 

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. 次に、変更をコミットします。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. 各 Web アプリ (**osticket-eus2** と **osticket-cus**) に対して、Azure portal で **[アプリケーション設定]** を変更します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. **osticket** という名前で接続文字列を入力し、メモ帳から**領域の値**に文字列をコピーします。 文字列の横のドロップダウン リストから **[MySQL]** を選択し、設定を保存します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>手順 6:Web アプリを構成する

移行プロセスの最後のステップとして、Contoso 管理者は、Web アプリと osTicket Web サイトを構成します。



1. プライマリ Web アプリ (**osticket-eus2**) で **[デプロイ オプション]** を開き、ソースを **[GitHub]** に設定します。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. デプロイ オプションを選択します。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. オプションを設定すると、その構成が Azure Portal に保留中として表示されます。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. 構成が更新され、GitHub から Azure App Service を実行中の Docket コンテナーに osTicket Web アプリが読み込まれ、サイトがアクティブとして表示されます。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. セカンダリ Web アプリ (**osticket-cus**) に対して、上記のステップを繰り返します。
6. サイトが構成されたら、Traffic Manager プロファイルを使用してアクセスできます。 DNS 名は、osTicket アプリの新しい場所になります。 [詳細情報](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)
    
7. Contoso では、覚えやすい DNS 名を求めています。 Traffic Manager の名を指すエイリアス レコード (CNAME) **osticket.contoso.com** を、ドメイン コントローラーの DNS 内に作成します。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. カスタム ホスト名を許可するように **osticket-eus2** Web アプリと **osticket-cus** Web アプリの両方を構成します。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>自動スケーリングを設定する

最後に、アプリの自動スケーリングを設定します。 これにより、エージェントがアプリを使用するときに、ビジネス ニーズに応じてアプリのインスタンスが増減することが保証されます。 

1. App Service **APP-SRV-EUS2** で、**[スケール ユニット]** を開きます。
2. 現在のインスタンスの CPU の割合が 10 分間 70% を超える場合はインスタンス数を 1 つ増加させるという単一のルールを持つ新しい自動スケーリング設定を構成します。

    ![自動スケール](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. 同じ設定を **APP-SRV-CUS** でも構成して、アプリがセカンダリ リージョンにフェールオーバーした場合に、同じ動作が適用されるようにします。 唯一の違いは、これは目的がフェールオーバーのみであるため、インスタンス数の上限を 1 に設定することです。

   ![自動スケール](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

##  <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了すると、osTicket アプリは、プライベート GitHub リポジトリを使用して継続的デリバリーを行う Azure Web Apps で実行されるようにリファクタリングされます。 アプリが 2 つのリージョンで実行されることで、回復性が向上します。 PaaS プラットフォームへの移行後、OsTicket データベースは、MySQL 用の Azure データベースで実行されます。

クリーン アップのために、次の手順を実行する必要があります。 
- VMware VM を vCenter インベントリから削除します。
- ローカルのバックアップ ジョブからからオンプレミスの VM を削除します。
- 社内のドキュメントを更新して、新しい場所と IP アドレスを示します。 
- こうしたオンプレミスの VM と対話しているリソースがないか確認し、新しい構成が反映されるように、関連する設定やドキュメントをすべて更新します。
- アプリが起動していることを追跡するために、osticket-trafficmanager.net URL をポイントするように監視を再構成します。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリが実行されるようになり、Contoso は新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは、アプリを再調査して、セキュリティの問題を特定します。 Contoso は、OsTicket アプリと MySQL データベース インスタンス間の通信が SSL 用に構成されていないことを識別しました。 データベース トラフィックをハッキングできないようにするため、これを行う必要があります。 [詳細情報](https://docs.microsoft.com/azure/mysql/howto-configure-ssl)。

### <a name="backups"></a>バックアップ

- OsTicket Web アプリには状態データが含まれていないため、バックアップは必要ありません。
- データベースのバックアップを構成する必要はありません。 Azure Database for MySQL は、サーバーのバックアップを自動的に作成して保存します。 彼らはデータベースのために geo 冗長性を使用することを選択したため、このデータベースは耐障害性があり、運用準備ができています。 バックアップを使用すると、サーバーを特定の時点に復元できます。 [詳細情報](https://docs.microsoft.com/azure/mysql/concepts-backup)。


### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- PaaS のデプロイにライセンスの問題はありません。
- Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 これは、Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。  Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。



