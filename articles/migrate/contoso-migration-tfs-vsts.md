---
title: Azure の Azure DevOps Services に Team Foundation Server の展開をリファクターする | Microsoft Docs
description: Contoso がオンプレミスの TFS の展開を Azure の Azure DevOps Services に移行することでリファクターする方法について説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 21396a10543d388b6ac360f426272f1841b2f510
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314112"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-azure-devops-services"></a>Contoso の移行: Azure DevOps Services に Team Foundation Server の展開をリファクターする

この記事では、Contoso がオンプレミスの Team Foundation Server (TFS) の展開を Azure の Azure DevOps Services にリファクターする方法について説明します。 Contoso の開発チームは、過去 5 年間、チームのコラボレーションとソース管理に TFS を使用してきました。 現在は、開発やテストの作業とソース管理に対応するクラウドベースのソリューションに移行したいと考えています。 Azure DevOps モデルに移行し、新しいクラウドネイティブ アプリを開発する際に、Azure DevOps Services が役立ちます。

このドキュメントは、架空の会社 Contoso がオンプレミス リソースを Microsoft Azure クラウドに移行する方法を説明するシリーズ記事の 1 つです。 このシリーズには、背景情報とシナリオが含まれており、移行インフラストラクチャのセットアップ方法や、さまざまな種類の移行を実施する方法を具体的に説明しています。 シナリオは複雑になってきています。 今後、徐々に記事を追加する予定です。

**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1:概要](contoso-migration-overview.md) | Contoso の移行戦略、記事シリーズ、および使用するサンプル アプリの概要を示します。 | 使用可能
[記事 2: Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md) | Contoso が移行に備えて、オンプレミスと Azure のインフラストラクチャをどのように準備するかを説明します。 Contoso のすべての移行シナリオで、同じインフラストラクチャが使用されます。 | 使用可能
[記事 3:オンプレミスのリソースの評価](contoso-migration-assessment.md)  | Contoso が、VMware 上で実行されるオンプレミスの 2 階層アプリ、SmartHotel の評価をどのように実行するかを示します。 アプリの VM については [Azure Migrate](migrate-overview.md) サービスで、アプリの SQL Server データベースについては [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) で評価を行います。 | 使用可能
[記事 4: Azure VM および SQL Managed Instance へのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が SmartHotel アプリを Azure に移行する方法を説明します。 SQL Managed Instance に移行するために、アプリの Web VM は [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用して移行し、アプリのデータベースは [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) サービスを使用して移行します。 | 使用可能
[記事 5:Azure VM へのリホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery サービスを使用して、どのように SmartHotel アプリを Azure IaaS VM を移行するかを示します。
[記事 6:Azure VM および SQL Server 可用性グループへのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel アプリを移行する方法を示します。 Contoso は、Site Recovery を使用してアプリの VM を移行し、Database Migration サービスを使用してアプリのデータベースを SQL Server 可用性グループに移行します。 | 使用可能
[記事 7:Linux アプリを Azure VM にリホストする](contoso-migration-rehost-linux-vm.md) | Contoso が、Azure Site Recovery を使用して、どのように osTicket Linux アプリを Azure IaaS VM 移行するかを示します。
[記事 8:Linux アプリの Azure VM および Azure MySQL Server へのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso がどのように osTicket Linux アプリを移行するかを具体的に説明します。 VM の移行には Site Recovery を使用し、Azure MySQL Server インスタンスへの移行には MySQL Workbench を使用します。 | 使用可能
[記事 9:Azure Web App と Azure SQL Database にアプリをリファクターする](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel アプリを Azure コンテナー ベースの Web アプリに移行して、アプリ データベースを Azure SQL Server に移行する方法を示します。 | 使用可能
[記事 10:Azure App Service と Azure MySQL Server への Linux アプリのリファクター](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が osTicket Linux アプリを PHP 7.0 Docker コンテナーを使用する Azure App Service に移行する方法を示します。 デプロイするためのコード ベースが GitHub に移行されます。 アプリのデータベースが Azure MySQL に移行されます。 | 使用可能
記事 11:Azure DevOps Services での TFS 展開のリファクター | TFS の開発用アプリを Azure の Azure DevOps Services に移行します | この記事の内容は次のとおりです。
[記事 12:Azure Containers と Azure SQL Database でアプリを再構築する](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行して再構築する方法を示します。 アプリの Web 層を Windows コンテナーとして再構築し、Azure SQL データベースでアプリ データベースを再構築します。 | 使用可能
[記事 13:Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (App Services、Azure Kubernetes、Azure Functions、Cognitive Services、Cosmos DB など) を使用して SmartHotel アプリをリビルドする方法を示します。 | 使用可能
[記事 14:Azure への移行のスケーリング](contoso-migration-scale.md) | 移行の組み合わせを試した後、Contoso は Azure への完全移行に向けてスケーリングを準備します。 | 使用可能


## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に協力して将来の目標を明らかにしました。 パートナーは、開発ツールやテクノロジにはあまり関心がありませんが、次の点を把握しています。

- **ソフトウェア**: 中心の業務にかかわらず、Contoso を含め、すべての企業がソフトウェア企業になりました。 企業の上層部は、IT 部門がユーザーの新しい業務慣行や顧客のエクスペリエンスを利用して業務を改善する方法に関心を持っています。
- **効率化**: Contoso はプロセスを合理化し、開発者とユーザーにとって不要な手順を削除する必要があります。 こうすることで、顧客の要件により効率的に対応できるようになります。 時間やお金を無駄にすることなく、IT 部門の迅速化が必要です。
- **機敏性**: Contoso の IT 部門は、グローバル経済で成功を収めるために、ビジネス ニーズに対応し、市場よりも迅速に対応する必要があります。 IT 部門がビジネスのブロックする要素にならないようにします。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、Azure DevOps Services への移行の目標を設定しました。

- チームにはデータをクラウドに移行するツールが必要です。 手作業がほとんど不要になるようにする必要があります。
- 昨年の作業項目データと履歴を移行する必要があります。
- 新しいユーザー名とパスワードは設定したくありません。 現在のシステムの割り当てはすべて維持する必要があります。
- ソース管理については、Team Foundation バージョン管理 (TFVC) から Git に移行したいと考えています。
- Git への切り替えは、ソース コードの最新バージョンのみをインポートする "tip migration" (チップ移行) になります。 この移行は、コードベースの転換時にすべての作業が中止されるダウンタイム中に行われます。 Contoso は、移行後に最新のマスター ブランチ履歴しか利用できなくなることを理解しています。
- Contoso はこの変更を心配し、完全に移行する前にテストしたいと考えています。 Azure DevOps Services への移行後も TFS へのアクセスを維持したいと考えています。
- 複数のコレクションがあるので、プロセスをより理解できるように、プロジェクトがごく少数のコレクションから始めたいと考えています。
- TFS コレクションが Azure DevOps Services 組織と 1 対 1 の関係であることは理解しているので、複数の URL を持つことになります。 ただし、これはコード ベースとプロジェクトの分離の現在のモデルと一致します。


## <a name="proposed-architecture"></a>提案されたアーキテクチャ

- Contoso は、TFS プロジェクトをクラウドに移行し、プロジェクトやソース管理をオンプレミスでホストしなくなります。
- TFS は、Azure DevOps Services に移行されます。
- 現在、Contoso には **ContosoDev** という名前の TFS コレクションが 1 つあります。これは **contosodevmigration.visualstudio.com** という Azure DevOps Services 組織に移行されます。
- 昨年のプロジェクト、作業項目、バグ、およびイテレーションは Azure DevOps Services に移行されます。
- Contoso は Azure Active Directory を利用します。これは、移行計画の開始時に [Azure インフラストラクチャを展開](contoso-migration-infrastructure.md)したときに設定したものです。 


![シナリオのアーキテクチャ](./media/contoso-migration-tfs-vsts/architecture.png) 


## <a name="migration-process"></a>移行プロセス

Contoso は、次のようにして移行プロセスを完了します。

1. 多くの準備が必要です。 まず、Contoso は TFS の実装をサポートされるレベルまでアップグレードする必要があります。 現在は TFS 2017 Update 3 を実行していますが、データベースの移行を使用するには、最新の更新プログラムが適用された、サポートされている 2018 バージョンを実行する必要があります。
2. アップグレード後、TFS Migration Tool を実行し、コレクションを検証します。
3. 一連の準備ファイルを構築し、テストのために移行のドライ ランを実行します。
4. その後、もう一度移行を実行します。今度は、作業項目、バグ、スプリント、およびコードを含む完全な移行です。
5. 移行後、コードを TFVC から Git に移動します。

![移行プロセス](./media/contoso-migration-tfs-vsts/migration-process.png) 


## <a name="scenario-steps"></a>シナリオのステップ

Contoso は、次のようにして移行を完了します。

> [!div class="checklist"]
> * **手順 1:Azure ストレージ アカウントを作成する**: このストレージ アカウントは、移行プロセス中に使用されます。
> * **手順 2:TFS をアップグレードする**: 展開を TFS 2018 Update 2 にアップグレードします。 
> * **手順 3:コレクションを検証する**: 移行の準備の TFS コレクションを検証します。
> * **手順 4:準備ファイルを構築する**: TFS Migration Tool を使用して、移行ファイルを作成します。 


## <a name="step-1-create-a-storage-account"></a>手順 1:ストレージ アカウントの作成

1. Contoso の管理者は Azure portal でストレージ アカウントを作成します (**contosodevmigration**)。
2. フェールオーバーに使用するセカンダリ リージョン (米国中部) でアカウントを設定します。 ローカルの冗長ストレージには汎用の標準アカウントを使用します。

    ![ストレージ アカウント](./media/contoso-migration-tfs-vsts/storage1.png) 


**さらにサポートが必要な場合**

- [Azure Storage の概要](https://docs.microsoft.com/azure/storage/common/storage-introduction)。
- [ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。


## <a name="step-2-upgrade-tfs"></a>手順 2:TFS をアップグレードする

Contoso の管理者は、TFS サーバーを TFS 2018 Update 2 にアップグレードします。 開始前の作業:

- [TFS 2018 Update 2](https://visualstudio.microsoft.com/downloads/) をダウンロードします
- [ハードウェア要件](https://docs.microsoft.com/tfs/server/requirements)を検証し、[リリース ノート](https://docs.microsoft.com/visualstudio/releasenotes/tfs2018-relnotes)と[アップグレードの注意事項](https://docs.microsoft.com/tfs/server/upgrade/get-started#before-you-upgrade-to-tfs-2018)をすべて読みます。

次の手順でアップグレードします。

1. まず (VMware VM 上で実行されている) TFS サーバーをバックアップし、VMware のスナップショットを作成します。

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade1.png) 

2. TFS のインストーラーが起動し、インストール先を選択します。 インストーラーはインターネットにアクセスできる必要があります。

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade2.png) 

3. インストールが完了すると、サーバー構成ウィザードが起動します。

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade3.png) 

4. 検証後、ウィザードはアップグレードを完了します。

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade4.png) 

5. プロジェクト、作業項目、およびコードを確認して TFS のインストールを検証します。

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png) 

> [!NOTE]
> 一部の TFS アップグレードでは、アップグレードの完了後に機能の構成ウィザードを実行する必要があります。 [詳細情報](https://docs.microsoft.com/azure/devops/reference/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts)。

**さらにサポートが必要な場合**

TFS のアップグレードの詳細については、[こちら](https://docs.microsoft.com/tfs/server/upgrade/get-started)を参照してください。

## <a name="step-3-validate-the-tfs-collection"></a>手順 3:TFS コレクションを検証する

Contoso の管理者は、移行前に ContosoDev コレクション データベースに対して TFS Migration Tool を実行して検証します。

1. 管理者は [TFS Migration Tool](https://www.microsoft.com/download/details.aspx?id=54274) をダウンロードして解凍します。 実行している TFS 更新プログラム用のバージョンをダウンロードすることが重要です。 バージョンは管理コンソールで確認できます。

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. プロジェクト コレクションの URL を指定して、検証を実行するツールを実行します。

   **TfsMigrator validate /collection:http:\//contosotfs:8080/tfs/ContosoDev**


3. ツールにエラーが表示されます。

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. ログ ファイルは、ツールの場所の直前の **Logs** フォルダーにあります。 主要な検証ごとにログ ファイルが生成されます。 **TfsMigration.log** は主要な情報を保持しています。

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. 管理者は、ID に関連するこのエントリを見つけます。

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. 管理者はコマンド ラインで **TfsMigration validate /help** を実行し、ID の検証にコマンドの **/tenantDomainName** が必要と思われることを確認します。

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. 検証コマンドをもう一度実行し、この値を Azure AD 名と共に含めます **TfsMigrator validate /collection:http:\//contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com**。

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. Azure AD のサインイン画面が表示され、全体管理者ユーザーの資格情報を入力します。

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. 検証に合格します。これはツールで確認できます。

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>手順 4:移行ファイルを作成する

検証が完了すると、Contoso の管理者は TFS Migration Tool を使用して移行ファイルを構築できます。

1. ツールで準備手順を実行します。

    **TfsMigrator prepare /collection:http:\//contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus**

     ![準備](./media/contoso-migration-tfs-vsts/prep1.png)

    Prepare で次の処理を実行します。
    - コレクションをスキャンしてすべてのユーザー一覧を検索し、識別マップ ログを作成します (**IdentityMapLog.csv**])。
    - Azure Active Directory への接続を準備し、各 ID に一致するものを検索します。
    - Prepare が一致する ID を検出して Active とマークできるように、Contoso は Azure AD をあらかじめデプロイし、AD Connect を使用して同期しています。

2. Azure AD のサインイン画面が表示され、全体管理者の資格情報を入力します。

    ![準備](./media/contoso-migration-tfs-vsts/prep2.png)

3. Prepare が完了すると、インポート ファイルが正常に生成されたことがレポートされます。

    ![準備](./media/contoso-migration-tfs-vsts/prep3.png)

4. 管理者は、IdentityMapLog.csv ファイルと import.json ファイルの両方が新しいフォルダーに作成されていることを確認できます。

    ![準備](./media/contoso-migration-tfs-vsts/prep4.png)

5. import.json ファイルにはインポート設定が指定されています。 このファイルには、目的の組織名やストレージ アカウント情報などの情報が含まれています。 ほとんどのフィールドは自動的に設定されます。 一部のフィールドにはユーザー入力が必要です。 Contoso はファイルを開き、作成する Azure DevOps Services 組織名 **contosodevmigration** を追加します。 この名前の場合、Azure DevOps Services の URL は **contosodevmigration.visualstudio.com** になります。

    ![準備](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > 組織は移行前に作成する必要があります。移行後に変更することもできます。

6. インポート中に Azure DevOps Services に取り込まれるアカウントを示す ID ログ マップ ファイルを確認します。 

   - アクティブな ID とは、インポート後に Azure DevOps Services 内のユーザーになる ID のことです。
   - Azure DevOps Services で、これらの ID にライセンスが付与され、移行後に組織のユーザーとして表示されます。
   - これらの ID は、ファイルの **Expected Import Status** 列で **Active** とマークされています。

     ![準備](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-azure-devops-services"></a>手順 5:Azure DevOps Services に移行する

準備が完了したので、Contoso の管理者は移行に集中できるようになりました。 移行を実行すると、バージョン管理には TFVC ではなく Git が使用されるようになります。

移行に備えてコレクションをオフラインにするために、管理者はまず開発チームとダウンタイムのスケジュールを立てます。 移行プロセスの手順は次のとおりです。

1. **コレクションをデタッチする**: コレクションの ID データは、コレクションがアタッチされ、オンラインのときは TFS サーバー構成データベース内にあります。 コレクションが TFS サーバーからデタッチされると、その ID データのコピーが作成され、転送のためにコレクションと共にパッケージ化されます。 インポートの ID 部分を実行するには、このデータが必要です。 インポート中に発生した変更をインポートする方法がないので、インポートが完了するまでコレクションをデタッチしたままにすることをお勧めします。
2. **バックアップを生成する**: 移行プロセスの次の手順は、Azure DevOps Services にインポートできるバックアップを生成することです。 データ層アプリケーション コンポーネント パッケージ (DACPAC) は、データベースの変更を単一のファイルにパッケージ化し、SQL の他のインスタンスに展開することができる SQL Server の機能です。 また、Azure DevOps Services に直接復元することもできるので、コレクション データをクラウドに取り込むためのパッケージ化方法として使用されます。 Contoso は SqlPackage.exe ツールを使用して DACPAC を生成します。 このツールは、SQL Server Data Tools に含まれています。
3. **ストレージにアップロードする**: DACPAC が作成されたら、Azure Storage にアップロードします。 アップロードが完了したら、Shared Access Signature (SAS) を取得し、TFS Migration Tool からストレージにアクセスできるようにします。
4. **インポート ファイルに入力する**: Contoso は、DACPAC 設定など、インポート ファイルに足りないフィールドに入力します。 完全な移行の前に、まず **DryRun** インポートを実行して、すべてが正しく動作していることを確認するように指定します。
5. **ドライ ランを実行する**: ドライ ランのインポートで、コレクションの移行をテストできます。 ドライ ランの保存期間は限られており、運用環境の移行が実行される前に削除されます。 設定した期間が経過すると、自動的に削除されます。 ドライ ランが削除される時期についての注意は、インポートが完了した後に送信される成功メールに記載されています。 時期を書き留め、それに応じて計画を立てます。
6. **運用環境の移行を完了する**: ドライ ランの移行が完了したら、Contoso の管理者は import.json を更新し、インポートを再実行して、最終的な移行を実行します。



### <a name="detach-the-collection"></a>コレクションをデタッチする

まず Contoso の管理者はローカル SQL Server のバックアップと TFS サーバーの VMware スナップショットを作成してから、デタッチします。

1.  TFS 管理コンソールで、デタッチするコレクションを選択します (**ContosoDev**)。

    ![移行](./media/contoso-migration-tfs-vsts/migrate1.png)

2. **[全般]** で、**[コレクションのデタッチ]** を選択します

    ![移行](./media/contoso-migration-tfs-vsts/migrate2.png)

3. チーム プロジェクト コレクションのデタッチ ウィザードの **[サービス メッセージ]** で、コレクション内のプロジェクトに接続しようとするユーザー向けのメッセージを入力します。

    ![移行](./media/contoso-migration-tfs-vsts/migrate3.png)

4. **[デタッチの進行状況]** で進行状況を監視し、プロセスが完了したら **[次へ]** をクリックします。

    ![移行](./media/contoso-migration-tfs-vsts/migrate4.png)

5. **[準備チェック]** でチェックが完了したら、**[デタッチ]** をクリックします。

    ![移行](./media/contoso-migration-tfs-vsts/migrate5.png)

6. **[閉じる]** をクリックして完了します。

    ![移行](./media/contoso-migration-tfs-vsts/migrate6.png)

6. コレクションは TFS 管理コンソールで参照されなくなります。

    ![移行](./media/contoso-migration-tfs-vsts/migrate7.png)


### <a name="generate-a-dacpac"></a>DACPAC を生成する

Contoso は Azure DevOps Services へのインポートのためにバックアップ (DACPAC) を作成します。

- DACPAC の作成には SQL Server Data Tools の SqlPackage.exe が使用されます。 SQL Server Data Tools と共に、120、130、140 などの名前のフォルダー以下に複数バージョンの SqlPackage.exe がインストールされます。 適切なバージョンを使用して DACPAC を準備することが重要です。
- TFS 2018 のインポートでは、140 以降のフォルダーの SqlPackage.exe を使用する必要があります。  CONTOSOTFS の場合、このファイルは **C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140** フォルダーにあります。


Contoso の管理者は次のように DACPAC を生成します。

1. コマンド プロンプトを開き、SQLPackage.exe の場所に移動します。 次のコマンドを入力して DACPAC を生成します。

    **SqlPackage.exe /sourceconnectionstring:"Data Source=SQLSERVERNAME\INSTANCENAME;Initial Catalog=Tfs_ContosoDev;Integrated Security=True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData=true /p:IgnoreUserLoginMappings=true /p:IgnorePermissions=true /p:Storage=Memory** 

    ![バックアップ](./media/contoso-migration-tfs-vsts/backup1.png)

2. コマンドの実行後、次のメッセージが表示されます。

    ![バックアップ](./media/contoso-migration-tfs-vsts/backup2.png)

3. DACPAC ファイルのプロパティを確認します。

    ![バックアップ](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>ストレージに対してファイルを更新する

DACPAC が作成されたら、Contoso は Azure Storage にアップロードします。

1. Azure Storage Explorer を[ダウンロードしてインストール](https://azure.microsoft.com/features/storage-explorer/)します。

    ![アップロード](./media/contoso-migration-tfs-vsts/backup5.png)

4. サブスクリプションに接続し、移行のために作成したストレージ アカウントを探します (**contosodevmigration**)。 新しい BLOB コンテナー **azuredevopsmigration** を作成します。

    ![アップロード](./media/contoso-migration-tfs-vsts/backup6.png)

5. アップロード対象の DACPAC ファイルをブロック BLOB として指定します。

    ![アップロード](./media/contoso-migration-tfs-vsts/backup7.png)
    
7. ファイルがアップロードされたら、ファイル名、**[SAS の生成]** の順にクリックします。 ストレージ アカウント以下の BLOB コンテナーを展開し、インポート ファイルを含むコンテナーを選択し、**[Shared Access Signature の取得]** をクリックします。

    ![アップロード](./media/contoso-migration-tfs-vsts/backup8.png)

8. 既定値をそのまま使用し、**[作成]** をクリックします。 これで、24 時間のアクセスが可能になります。

    ![アップロード](./media/contoso-migration-tfs-vsts/backup9.png)

9. Shared Access Signature の URL をコピーし、TFS Migration Tool で使用できるようにします。

    ![アップロード](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> 許可された期間内に移行が完了する必要があります。完了しない場合、アクセス許可は期限切れになります。
> Azure portal からは SAS キーを生成しないでください。 この方法で生成されたキーは、アカウント スコープであり、インポートには使用できません。

### <a name="fill-in-the-import-settings"></a>インポート設定を入力する

以前、Contoso の管理者はインポート仕様ファイル (import.json) の一部を入力していました。 今回は、残りの設定を追加する必要があります。

import.json ファイルを開き、次のフィールドに入力します。•   Location: 前述の手順で生成された SAS キーの場所。
•   Dacpac: ストレージ アカウントにアップロードした DACPAC ファイルに名前を設定します。 拡張子 ".dacpac" を含めます。
•   ImportType: ここでは DryRun に設定します。


![設定のインポート](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>ドライ ラン 移行を実行する

Contoso の管理者はドライ ラン移行を開始し、すべてが想定どおりに動作していることを確認します。

1. コマンド プロンプトを開き、TfsMigration の場所 (C:\TFSMigrator) に移動します。
2. 最初の手順として、インポート ファイルを検証します。 Contoso は、ファイルの形式が正しいこと、SAS キーが動作していることを確認したいと考えています。

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly**

3. この検証で、SAS キーの有効期間を長くする必要があるというエラーが返されます。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test1.png)

3. Azure Storage Explorer を使用して有効期間を 7 日間に設定した新しい SAS キーを作成します。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test2.png)

3. import.json ファイルを更新し、検証をもう一度実行します。 今回は検証が正常に完了します。

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly**

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test3.png)
    
7. ドライ ランを開始します。

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json**

8. 移行を確認するメッセージが発行されます。 ドライ ラン後にステージング データが保持される時間の長さに注意してください。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test4.png)

9. Azure AD の [サインイン] が表示されます。Contoso 管理者のサインインで完了する必要があります。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test5.png)

10. メッセージにはインポートに関する情報が表示されます。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test6.png)

11. 15 分ほど経過した後、管理者は URL にアクセスし、次の情報を確認します。

     ![ドライ ラン](./media/contoso-migration-tfs-vsts/test7.png)

12. 移行が完了したら、Contoso の開発リーダーは Azure DevOps Services にサインインし、ドライ ランが正常に動作していることを確認します。 認証後、Azure DevOps Services は、組織を確認するいくつかの詳細情報が必要です。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test8.png)

13. Azure DevOps Services で、開発リーダーはプロジェクトが Azure DevOps Services に移行されたことを確認できます。 15 日後に組織が削除されるという通知が表示されます。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test9.png)

14. 開発リーダーはいずれかのプロジェクトを開き、**[作業項目]** > **[自分に割り当て済み]** を開きます。 作業項目データが ID と共に移行されたことが表示されます。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test10.png)

15. また、他のプロジェクトとコードをチェックして、ソース コードと履歴が移行されたことを確認します。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>運用環境の移行を実行する

ドライ ランが完了したら、Contoso の管理者は運用環境の移行に進みます。 ドライ ランを削除し、インポート設定を更新し、インポートをもう一度実行します。

1. Azure DevOps Services ポータルのドライ ランの組織を削除します。
2. **ImportType** を **ProductionRun** に設定するように import.json ファイルを更新します。

    ![Production](./media/contoso-migration-tfs-vsts/full1.png)

3. ドライ ランの場合と同様に、移行を開始します (**TfsMigrator import /importFile:C:\TFSMigrator\import.json**)。
4. 移行を確認するメッセージが表示され、セキュリティで保護されている場所に、最大 7 日間、ステージング領域としてデータを保持できるという警告が表示されます。

    ![Production](./media/contoso-migration-tfs-vsts/full2.png)

5. Azure AD の [サインイン] で、リーダーは Contoso 管理者のサインインを指定します。

    ![Production](./media/contoso-migration-tfs-vsts/full3.png)

6. メッセージにはインポートに関する情報が表示されます。

    ![Production](./media/contoso-migration-tfs-vsts/full4.png)

7. 15 分ほど経過した後、リーダーは URL にアクセスし、次の情報を確認します。

    ![Production](./media/contoso-migration-tfs-vsts/full5.png)

8. 移行が完了したら、Contoso の開発リーダーは Azure DevOps Services にログインし、移行が正常に動作していることを確認します。 ログインすると、プロジェクトが移行されたことを確認できます。

    ![Production](./media/contoso-migration-tfs-vsts/full6.png)

8. 開発リーダーはいずれかのプロジェクトを開き、**[作業項目]** > **[自分に割り当て済み]** を開きます。 作業項目データが ID と共に移行されたことが表示されます。

    ![Production](./media/contoso-migration-tfs-vsts/full7.png)

9. リーダーは他の作業項目データを見て確認します。

    ![Production](./media/contoso-migration-tfs-vsts/full8.png)

15. また、他のプロジェクトとコードをチェックして、ソース コードと履歴が移行されたことを確認します。

    ![Production](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>ソース管理を TFVC から Git に移行する

Contoso は、移行が完了したら、ソース コード管理については TFVC から Git に移行したいと考えています。 同一組織での Git リポジトリとして Azure DevOps Services 組織における現在のソース コードをインポートする必要があります。

1. Azure DevOps Services ポータルで、TFVC リポジトリ (**$/PolicyConnect**) のいずれかを開いて確認します。

    ![Git](./media/contoso-migration-tfs-vsts/git1.png)

2. **[ソース]** ドロップ ダウン、**[インポート]** の順にクリックします。

    ![Git](./media/contoso-migration-tfs-vsts/git2.png)

3. **[ソースの種類]** で **[TFVC]** を選択し、リポジトリのパスを指定します。 履歴は移行しないことを決めました。

    ![Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > TFVC と Git のバージョン管理情報の保存方法は異なるので、履歴は移行しないことをお勧めします。 これは、Windows や他の製品を一元管理されたバージョン管理から Git に移行したときに Microsoft が採用したアプローチです。

4. インポートの後、管理者はコードを確認します。

    ![Git](./media/contoso-migration-tfs-vsts/git4.png)

5. 2 番目のリポジトリ (**$/SmartHotelContainer**) について、同じプロセスを繰り返します。

    ![Git](./media/contoso-migration-tfs-vsts/git5.png)

6. ソースを確認した後、開発リーダーたちは Azure DevOps Services への移行が完了したことに同意します。 Azure DevOps Services は、移行に関わるチーム内のすべての開発のソースになりました。

    ![Git](./media/contoso-migration-tfs-vsts/git6.png)



**さらにサポートが必要な場合**

TFVC からのインポートの詳細については、[こちら](https://docs.microsoft.com/azure/devops/repos/git/import-from-TFVC?view=vsts)を参照してください。

##  <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了したら、Contoso は次の操作を行う必要があります。

- 追加のインポート アクティビティについては、[インポート後](https://docs.microsoft.com/azure/devops/articles/migration-post-import?view=vsts)に関する記事を参照してください。
- TFVC リポジトリを削除するか、読み取り専用モードに設定します。 コード ベースは使用しないでください。ただし、履歴のために参照することはできます。

## <a name="next-steps"></a>次の手順

Contoso は関連するチーム メンバーに Azure DevOps Services と Git のトレーニングを用意する必要があります。



