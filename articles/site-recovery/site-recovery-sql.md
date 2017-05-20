---
title: "SQL Server および Azure Site Recovery を使用したアプリのレプリケート | Microsoft Docs"
description: "この記事では、SQL Server の災害機能の Azure Site Recovery を使用して、SQL Server をレプリケートする方法について説明します。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 9ea73dd91c9637692bbc3d6d2aa97fbed7ae500d
ms.openlocfilehash: 79c110031a47f1bdb78f4acfcadd7bff1e909807
ms.lasthandoff: 02/23/2017


---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>SQL Server 災害復旧および Azure Site Recovery を使用した SQL Server の保護

この記事では、SQL Server のビジネス継続性と災害復旧 (BCDR) テクノロジおよび [Azure Site Recovery](site-recovery-overview.md) の組み合わせを使用してアプリケーションの SQL Server バックエンドを保護する方法について説明します。

開始する前に、フェールオーバー クラスタリング、AlwaysOn 可用性グループ、データベース ミラーリング、ログ配布など、SQL Server 災害復旧機能についてよく理解してください。


## <a name="sql-server-deployments"></a>SQL Server のデプロイメント

多くのワークロードは SQL Server を基盤として使用します。これは SharePoint、Dynamics、SAP などのアプリと統合して、データ サービスを実装できます。  SQL Server は次のさまざまな方法でデプロイできます。

* **スタンドアロンの SQL Server**: SQL Server とすべてのデータベースは、1 つのマシン (物理または仮想) にホストされます。 仮想化する場合、ローカルの高可用性のためにホストのクラスタリングを使用します。 ゲストレベルの高可用性は実装されません。
* **SQL Server フェールオーバー クラスタリング インスタンス (AlwaysOn FCI)**: Windows フェールオーバー クラスターに、共有ディスクが使用された SQL Server インスタンスを実行する複数のノードを構成します。 ノードが停止した場合、クラスターは SQL Server を別のインスタンスにフェールオーバーできます。 通常、この設定はプライマリ サイトに高可用性を実装するために使用されます。 このデプロイメントでは、共有ストレージ層の障害や停止は保護されません。 共有ディスクは、iSCSI、ファイバー チャネル、または共有 VHDX を使用して実装できます。
* **SQL AlwaysOn 可用性グループ**: シェアード ナッシング クラスターに複数のノードを設定します。このクラスターでは、同期レプリケーションと自動フェールオーバーを設定した可用性グループに SQL Server データベースを構成します。

 この記事では、次に示すネイティブの SQL 災害復旧テクノロジを活用して、データベースを リモート サイトに復旧します。

* SQL AlwaysOn 可用性グループ: SQL Server 2012 または 2014 Enterprise の各エディションの災害復旧を提供します。
* 高い安全性モードでの SQL Database ミラーリング: SQL Server Standard エディション (全バージョン) または SQL Server 2008 R2 用です。

## <a name="site-recovery-support"></a>Site Recovery のサポート

### <a name="supported-scenarios"></a>サポートされるシナリオ
Site Recovery は、次の表のように SQL Server を保護できます。

**シナリオ** | **セカンダリ サイトへ** | **Azure へ**
--- | --- | ---
**Hyper-V** | はい | はい
**VMware** | はい | はい
**物理サーバー** | はい | はい

### <a name="supported-sql-server-versions"></a>サポートされる SQL Server のバージョン
これらの SQL Server バージョンは、サポートされるシナリオに対応しています。

* SQL Server 2014 Enterprise および Standard
* SQL Server 2012 Enterprise および Standard
* SQL Server 2008 R2 Enterprise および Standard

### <a name="supported-sql-server-integration"></a>サポートされる SQL Server の統合

Site Recovery は、次の表に要約したネイティブの SQL Server の BCDR テクノロジと統合して、災害復旧ソリューションを提供できます。

**機能** | **詳細** | **SQL Server** |
--- | --- | ---
**AlwaysOn 可用性グループ** | SQL Server の複数のスタンドアロン インスタンスであり、複数のノードを持つフェールオーバー クラスターでそれぞれが実行されます。<br/><br/>データベースは、SQL Server インスタンス上でコピー (ミラー化) が可能なフェールオーバー グループにグループ化できるため、共有記憶域は必要ありません<br/><br/>プライマリ サイトと 1 つまたは複数のセカンダリ サイトの間で災害復旧を実現します。 2 つのノードをシェアード ナッシング クラスターに設定できます。このクラスターでは、同期レプリケーションと自動フェールオーバーを設定した可用性グループに SQL Server データベースを構成しておきます。 | SQL Server 2014 および 2012 Enterprise エディション
**フェールオーバー クラスタリング (AlwaysOn FCI)** | SQL Server は、Windows フェールオーバー クラスタリングを利用して、オンプレミスの SQL Server ワークロードの高可用性を実現しています。<br/><br/>共有ディスクを備えた、SQL Server のインスタンスを実行しているノードは、フェールオーバー クラスター内に構成されます。 インスタンスがダウンした場合、クラスターは別のクラスターにフェールオーバーします。<br/><br/>クラスターは、共有ストレージのエラーまたは障害からは保護しません。 共有ディスクは、iSCSI、ファイバー チャネル、または共有 VHDX を使用して実装できます。 | SQL Server Enterprise エディション<br/><br/>SQL Server Standard エディション (2 つのノードのみに制限)
**データベース ミラーリング (高い安全性モード)** | 1 つのセカンダリ コピーで&1; つのデータベースを保護します。 高い安全性 (同期) と高パフォーマンス (非同期) の両方のレプリケーション モードで使用できます。 フェールオーバー クラスターは必要はありません。 | SQL Server 2008 R2<br/><br/>SQL Server Enterprise のすべてのエディション
**スタンドアロンの SQL Server** | SQL Server とデータベースは、単一のサーバー (物理または仮想) でホストされます。 サーバーが仮想である場合、ホスト クラスタリングが高可用性のために使用されます。 ゲストレベルの高可用性はありません。 | Enterprise または Standard エディション

## <a name="deployment-recommendations"></a>デプロイメントに関する推奨事項

次の表は、Site Recovery に SQL Server の BCDR テクノロジを統合するための推奨事項を示しています。

| **バージョン** | **エディション** | **デプロイ** | **オンプレミスからオンプレミス** | **オンプレミスから Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 または 2012 |Enterprise |フェールオーバー クラスター インスタンス |AlwaysOn 可用性グループ |AlwaysOn 可用性グループ |
|| Enterprise |高可用性のための AlwaysOn 可用性グループ |AlwaysOn 可用性グループ |AlwaysOn 可用性グループ | |
|| 標準 |フェールオーバー クラスター インスタンス (FCI) |ローカルのミラーを使用した Site Recovery レプリケーション |ローカルのミラーを使用した Site Recovery レプリケーション | |
|| Enterprise または Standard |スタンドアロン |Site Recovery レプリケーション |Site Recovery レプリケーション | |
| SQL Server 2008 R2 |Enterprise または Standard |フェールオーバー クラスター インスタンス (FCI) |ローカルのミラーを使用した Site Recovery レプリケーション |ローカルのミラーを使用した Site Recovery レプリケーション |
|| Enterprise または Standard |スタンドアロン |Site Recovery レプリケーション |Site Recovery レプリケーション | |
| SQL Server (全バージョン) |Enterprise または Standard |フェールオーバー クラスター インスタンス - DTC アプリケーション |Site Recovery レプリケーション |サポートされていません |

## <a name="deployment-prerequisites"></a>デプロイメントの前提条件

* サポートされている SQL Server のバージョンを実行しているオンプレミスの SQL Server デプロイメント。 通常、SQL Server には Active Directory も必要です。
* デプロイするシナリオの要件。 [Azure](site-recovery-support-matrix-to-azure.md) や [オンプレミス](site-recovery-support-matrix.md)へのレプリケーションのサポート要件と、[デプロイメントの前提条件](site-recovery-prereq.md)をよく理解します。
* Azure で復旧を設定するには、[Azure 仮想マシン準備状態評価](http://www.microsoft.com/download/details.aspx?id=40898)ツールを SQL Server 仮想マシン上で実行し、SQL Server 仮想マシンに Azure および Site Recovery との互換性があることを確認します。

## <a name="set-up-active-directory"></a>Active Directory のセットアップ

SQL Server を正常に実行するために、セカンダリ復旧サイトに Active Directory をセットアップします。

* **小規模企業** - アプリケーションの数が少なく、オンプレミスのサイト用のドメイン コントローラーが 1 つしかない場合にサイト全体をフェールオーバーするには、Site Recovery レプリケーションを使用して、セカンダリ データセンターまたは Azure にドメイン コント ローラーをレプリケートすることをお勧めします。
* **中規模および大企業** - アプリケーションの数が多く、Active Directory フォレストがある場合に、アプリケーションまたはワークロード単位でフェールオーバーするには、追加のドメイン コントローラーをセカンダリ データセンターまたは Azure にセットアップすることをお勧めします。 AlwaysOn 可用性グループを使用してリモート サイトに復旧する場合は、もう 1 つ追加のドメイン コント ローラーをセカンダリ サイトまたは Azure にセットアップして、復旧した SQL Server インスタンスに対して使用することをお勧めします。

この記事に記載された手順は、2 番目の拠点でドメイン コントローラーが使用できることを前提としています。 [こちら](site-recovery-active-directory.md) を参照してください。

## <a name="integrate-with-sql-server-alwayson-for-replication-to-azure-classic-portal-with-a-vmmconfiguration-server"></a>SQL Server AlwaysOn との統合: Azure (VMM/構成サーバーがあるクラシック ポータル) へのレプリケーション


Site Recovery は SQL AlwaysOn をネイティブでサポートします。 Azure の仮想マシンをセカンダリの場所として設定した状態で SQL 可用性グループを作成した場合、Site Recovery を使用して可用性グループのフェールオーバーを管理できます。

> [!NOTE]
> この機能は現在プレビューの段階です。 これを使用できるのは、プライマリ サイトの Hyper-V ホスト サーバーが System Center VMM クラウドで管理されているとき、または [VMware レプリケーション](site-recovery-vmware-to-azure.md)をセットアップしたときです。 現在、この機能は Azure Portal では使用できません。 新しい Azure ポータルを使用している場合は、[こちらのセクション](site-recovery-sql.md#integrate-with-sql-server-alwayson-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server)の手順に従ってください。
>
>


#### <a name="before-you-start"></a>開始する前に

SQL AlwaysOn を Site Recovery に統合するには次が必要です。

* オンプレミスの SQL Server (スタンドアロン サーバーまたはフェールオーバー クラスター)。
* SQL Server がインストールされている&1; つまたは複数の Azure Virtual Machines。
* オンプレミスの SQL Server と Azure で実行されている SQL Server 間の SQL Server 可用性グループ セットアップ。
* オンプレミスの SQL Server で有効化された PowerShell リモート処理。 VMM Server または構成サーバーから、SQL Server マシンへのリモート PowerShell 呼び出しが可能になっている必要があります。
* ユーザー アカウントをオンプレミスの SQL Server マシンに追加する必要があります。 少なくとも次のアクセス許可を持つ SQL Server グループを追加します。
  * ALTER AVAILABILITY GROUP: [こちら](https://msdn.microsoft.com/library/hh231018.aspx)と[こちらの](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)アクセス許可
  * ALTER DATABASE -  [こちらの](https://msdn.microsoft.com/library/ff877956.aspx#Security)アクセス許可
* VMM を実行している場合は、RunAs アカウントを VMM サーバーに作成する必要があります。
- VMware を実行している場合は、CSPSConfigtool.exe を使用して、アカウントを構成サーバーに作成する必要があります。
* SQL PS モジュールをオンプレミスで実行している SQL Servers と Azure VM 上にインストールする必要があります。
* VM エージェントを Azure VM にインストールする必要があります。
* Azure VM 上で実行している SQL Server で NTAUTHORITY\System に次のアクセス許可が付与されている必要があります。
  * ALTER AVAILABILITY GROUP: [こちら](https://msdn.microsoft.com/library/hh231018.aspx)と[こちらの](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)アクセス許可
  * ALTER DATABASE -  [こちらの](https://msdn.microsoft.com/library/ff877956.aspx#Security)アクセス許可

### <a name="add-a-sql-server"></a>SQL Server を追加する
1. **[SQL の追加]** をクリックして、新規の SQL Server を追加します。

    ![[SQL の追加]](./media/site-recovery-sql/add-sql.png)
2. **[Configure SQL Settings (SQL 設定の構成)]**  >  **[名前]** に、SQL Server のフレンドリ名を入力します。
3. **[SQL Server (FQDN)]** に、追加するソース SQL Server の FQDN を入力します。 SQL Server をフェールオーバー クラスターにインストールする場合、クラスター ノードではなく、クラスターの FQDN を指定してください。  
4. **[SQL Server インスタンス]** で、既定のインスタンスを選択するか、カスタム名を入力します。
5. **[管理サーバー]** で、コンテナーに登録されている VMM サーバーまたは構成サーバーを選択します。 Site Recovery は SQL Server との通信にこのサーバーを使用します。
6. **[アカウントとして実行]** に、VMM RunAs アカウントまたは構成サーバー アカウントの名前を指定します。 このアカウントは SQL Server へのアクセスに使用され、SQL Server マシンの可用性グループで読み取りおよびフェールオーバーのアクセス権を持っている必要があります。

    ![Add SQL Dialog](./media/site-recovery-sql/add-sql-dialog.png)

SQL Server は、追加後 **[SQL Server]** タブに表示されます。

![SQL Server List](./media/site-recovery-sql/sql-server-list.png)

### <a name="add-a-sql-availability-group"></a>SQL 可用性グループを追加する

1. 追加した SQL Server で、**[Add SQL Availability Group (SQL 可用性グループの追加)]** をクリックします。

    ![Add SQL AG](./media/site-recovery-sql/add-sqlag.png)
2. 可用性グループは Azure の&1; つまたは複数の Azure VM へのレプリケートが可能です。 グループを追加するとき、Site Recovery でグループをフェールオーバーする先の Azure VM の名前とサブスクリプションを指定する必要があります。

    ![Add SQL AG Dialog](./media/site-recovery-sql/add-sqlag-dialog.png)
3. この例では、Availability Group (可用性グループ) DB1-AG がフェールオーバーの際に、サブスクリプション DevTesting2 内で実行している仮想マシン SQLAGVM2 でプライマリとなります。

> [!NOTE]
> 追加された SQL Server 上でプライマリとなる可用性グループのみが Site Recovery に追加可能です。 SQL Server に可用性グループ プライマリを作成した場合、もしくは SQL Server を追加した後に SQL Server にさらに可用性グループを追加した場合は、SQL Server 上でグループを更新してください。
>
>

### <a name="create-a-recovery-plan"></a>復旧計画の作成

仮想マシンと可用性グループの両方を使用する復旧計画を作成します。 VMM サーバーまたは構成サーバーをソースとして、Azure をターゲットとして選択します。

![復旧計画の作成](./media/site-recovery-sql/create-rp1.png)

![復旧計画の作成](./media/site-recovery-sql/create-rp2.png)

例では、Sharepoint アプリはバックエンドに SQL 可用性グループを使用している3 つの仮想マシンで構成されています。 この復旧計画では、可用性グループとアプリ VM の両方を選択できます。 VM マシンを他のフェールオーバー グループに移動して、フェールオーバーの実行順序を指定することで復旧計画をカスタマイズすることもできます。 可用性グループは常に最初にフェールオーバーされます。アプリ バックエンドとして使用されるためです。

![復旧計画のカスタマイズ](./media/site-recovery-sql/customize-rp.png)

### <a name="failover"></a>フェールオーバー

可用性グループを復旧計画に追加した後は、他のフェールオーバー オプションも使用可能になります。

**フェールオーバー** | **詳細**
--- | ---
**計画フェールオーバー** | 計画フェールオーバーとはデータ損失のないフェールオーバーを意味します。 これを実現するには、SQL 可用性グループを "同期" モードに設定します。その後でフェールオーバーをトリガーすると、用意された仮想マシン上で可用性グループがプライマリになり、そのグループが Site Recovery に追加されます。 フェールオーバーが完了すると、可用性モードは計画されたフェールオーバーがトリガーされる前と同じ値に設定されます。
**計画外フェールオーバー** | 計画外フェールオーバーではデータの損失が生じることがあります。 計画外フェールオーバーをトリガーしたとき、グループの可用性モードは変更されません。 可用性グループが、用意された仮想マシン上のプライマリになり、Site Recovery に追加されます。 計画外フェールオーバーが完了すると、SQL Server で実行しているオンプレミスのサーバーが再び使用可能となり、レプリケーションの反転が可用性グループに対してトリガーされる必要があります。 このアクションは、復旧計画ではなく、**[SQL Server]** > **[SQL Availability Group (SQL 可用性グループ)]** で行うことができます。
**テスト フェールオーバー** |SQL 可用性グループのテスト フェールオーバーはサポートされていません。 テスト フェールオーバーをトリガーした場合、可用性グループについてフェールオーバーがスキップされます。

次のフェールオーバー オプションを検討してください。

**オプション** | **詳細**
--- | ---
**方法 1** | 1.アプリケーション層とフロントエンド層のテスト フェールオーバーを実行します。<br/><br/>2.読み取り専用モードでレプリカのコピーにアクセスするようアプリケーション層を更新し、アプリケーションの読み取り専用テストを実行します。
**方法 2** | 1.(サイト間の VMM の複製または Azure Backup を使用して) レプリカの SQL Server 仮想マシン インスタンスのコピーを作成し、テスト ネットワークで起動します。<br/><br/> 2.復旧計画を使用して、テスト フェールオーバーを実行します。

### <a name="fail-back"></a>フェールバック

可用性グループをオンプレミスの SQL Server で再度 "プライマリ" にする場合、復旧計画で計画フェールオーバーをトリガーし、方向を Microsoft Azure からオンプレミス サーバーに選択します。

> [!NOTE]
> 計画外フェールオーバーの後、可用性グループで反転レプリケーションをトリガーしてレプリケーションを再開させる必要があります。  これが行われるまでは、レプリケーションが中断されたままです。
>
>

## <a name="integrate-with-sql-server-alwayson-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server"></a>SQL Server AlwaysOn との統合: Azure (Azure ポータルまたは VMM/構成サーバーのないクラシック ポータル) へのレプリケーション

次の手順で説明するのは、新しい Azure ポータル、または VMM サーバーや構成サーバーを使用しないクラシック ポータルでの SQL Server 可用性グループとの統合です。 このシナリオでは、Azure Automation Runbook を使用して、SQL 可用性グループのスクリプト化されたフェールオーバーを構成できます。

次の手順を実行する必要があります。

1. 可用性グループをフェールオーバーするスクリプトのローカル ファイルを作成します。 このサンプル スクリプトでは、Azure レプリカ上の可用性グループへのパスを指定し、そのレプリカ インスタンスに可用性グループをフェールオーバーします。 このスクリプトは、カスタム スクリプト拡張機能を使用して可用性グループを渡すことによって、SQL Server レプリカ仮想マシン上で実行されます。

        ``Param(
           [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``

2. スクリプトを Azure Storage アカウント内の BLOB にアップロードします。 次の例を使用します。

        ``$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context``

3. Azure の SQL Server レプリカ仮想マシンでスクリプトを呼び出す Azure Automation Runbook を作成します。 これを行うには、次のサンプル スクリプトを使用します。 [詳細情報](site-recovery-runbook-automation.md) を参照してください。

4. アプリケーションの復旧計画を作成するときに、可用性グループをフェールオーバーする Automation Runbook を呼び出す「pre-Group 1 boot」というスクリプト化した手順を追加します。


5. SQL AlwaysOn は、テスト フェールオーバーをネイティブでサポートしていません。 そこで、次の方法をお勧めします。
    1. Azure で可用性グループ レプリカをホストする仮想マシンに [Azure Backup](../backup/backup-azure-vms.md) をセットアップします。
    1. 復旧計画のテスト フェールオーバーをトリガーする前に、前の手順で作成したバックアップから仮想マシンを復元します。
    1. 復旧計画のテスト フェールオーバーを行います。


> [!NOTE]
> 下のスクリプトは、SQL 可用性グループが従来の Azure VM でホストされていること、および手順&2; で復元した仮想マシンの名前が SQLAzureVM-Test であることを前提としています。 復元した仮想マシンの名前に応じてスクリプトを変更してください。
>
>


     ``workflow SQLAvailabilityGroupFailover
     {

         param (
             [Object]$RecoveryPlanContext
         )

         $Cred = Get-AutomationPSCredential -name 'AzureCredential'

         #Connect to Azure
         $AzureAccount = Add-AzureAccount -Credential $Cred
         $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
         Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

         InLineScript
         {
          #Update the script with name of your storage account, key and blob name
          $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
          $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;

          Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;

          if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                    Write-output "tfo"

                    Write-Output "Creating ILB"
                    Add-AzureInternalLoadBalancer -InternalLoadBalancerName SQLAGILB -SubnetName Subnet-1 -ServiceName SQLAzureVM-Test -StaticVNetIPAddress #IP
                    Write-Output "ILB Created"

                    #Update the script with name of the virtual machine recovered using Azure Backup
                    Write-Output "Adding SQL AG Endpoint"
                    Get-AzureVM -ServiceName "SQLAzureVM-Test" -Name "SQLAzureVM-Test"| Add-AzureEndpoint -Name sqlag -LBSetName sqlagset -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName SQLAGILB | Update-AzureVM

                    Write-Output "Added Endpoint"

                    $VM = Get-AzureVM -Name "SQLAzureVM-Test" -ServiceName "SQLAzureVM-Test"

                    Write-Output "UnInstalling custom script extension"
                    Set-AzureVMCustomScriptExtension -Uninstall -ReferenceName CustomScriptExtension -VM $VM |Update-AzureVM
                    Write-Output "Installing custom script extension"
                    Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $vm -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM   

                    Write-output "Starting AG Failover"
                    Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument "-Path sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag"  | Update-AzureVM
                    Write-output "Completed AG Failover"
                }
          else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     

                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM;

                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.

                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";

                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;

                Write-output "Completed AG Failover";

                }

         }
     }``

## <a name="integrate-with-sql-server-alwayson-for-replication-to-a-secondary-on-premises-site"></a>SQL Server AlwaysOn との統合: セカンダリ オンプレミス サイトへのレプリケーション

SQL Server が可用性グループ (または FCI) を使用して高可用性を実現している場合は、復旧サイトでも可用性グループを使用することをお勧めします。 これは、分散トランザクションを使用しないアプリ向けであることに注意してください。

1. [データベースを構成](https://msdn.microsoft.com/library/hh213078.aspx) します。
2. セカンダリ サイトに、仮想ネットワークを作成します。
3. 仮想ネットワークとプライマリ サイトの間に、サイト間 VPN 接続を設定します。
4. 復旧サイトに仮想マシンを作成し、SQL Server を仮想マシンにインストールします。
5. 新しい SQL Server VM に、既存の AlwaysOn 可用性グループを拡張します。 この SQL Server インスタンスは、非同期レプリカ コピーとして構成します。
6. 可用性グループ リスナーを作成するか、または既存のリスナーを更新して、非同期のレプリカ仮想マシンを含めます。
7. アプリケーション ファームがリスナーを使用してセットアップされていることを確認します。 データベース サーバー名を使用してセットアップされている場合は、リスナーを使用するように更新して、フェールオーバー後に再構成する必要がないようにしてください。

分散トランザクションを使用するアプリケーションの場合は、[SAN レプリケーション](site-recovery-vmm-san.md)または [VMware/物理サーバー サイト間レプリケーション](site-recovery-vmware-to-vmware.md)と共に Site Recovery をデプロイすることをお勧めします。

### <a name="recovery-plan-considerations"></a>復旧計画に関する考慮事項
1. このサンプル スクリプトは、プライマリ サイトおよびセカンダリ サイト上の VMM ライブラリに追加します。

        ``Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``
2. アプリケーションの復旧計画を作成するときに、可用性グループをフェールオーバーするスクリプトを呼び出す「pre-Group 1 boot」というスクリプト化した手順を追加します。

## <a name="protect-a-standalone-sql-server"></a>スタンドアロンの SQL Server を保護する

このシナリオでは、Site Recovery レプリケーションを使用して、SQL Server マシンを保護することをお勧めします。 正確な手順は、SQL Server が VM か物理サーバーか、および Azure またはセカンダリ オンプレミス サイトのどちらにレプリケートするかによって異なります。 [Site Recovery のシナリオ](site-recovery-overview.md)に関するページをご覧ください。

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>SQL Server クラスターの保護 (Standard エディション/Windows Server 2008 R2)

SQL Server Standard エディション または SQL Server 2008 R2 を実行するクラスターでは、Site Recovery レプリケーションを使用して SQL Server を保護することをお勧めします。

### <a name="on-premises-to-on-premises"></a>オンプレミス間

* アプリが分散トランザクションを使用している場合は、Hyper-V 環境には [SAN レプリケーションを使用した Site Recovery](site-recovery-vmm-san.md) を、VMware 環境には [VMware/物理サーバーから VMware へ](site-recovery-vmware-to-vmware.md)をデプロイすることをお勧めします。
* DTC 以外のアプリケーションの場合、前述のアプローチで、ローカルの安全性の高い DB ミラーを使用し、スタンドアロン サーバーとしてクラスターを復旧します。

### <a name="on-premises-to-azure"></a>オンプレミスと Azure 間

Site Recovery は、Azure にレプリケートするときに、ゲスト クラスター サポートを提供していません。 SQL Server も Standard エディション用に低コストの障害復旧ソリューションを提供しません。 このシナリオでは、スタンドアロンの SQL Server でオンプレミスの SQL Server クラスターを保護し、Azure で復旧することをお勧めします。

1. オンプレミスのサイトで、追加のスタンドアロン SQL Server インスタンスを構成します。
2. このインスタンスを、保護が必要なデータベースのミラーとして機能するように構成します。 高い安全性モードでミラーリングを構成します。
3. [Hyper-V](site-recovery-hyper-v-site-to-azure.md) または [VMware VM/物理サーバー](site-recovery-vmware-to-azure-classic.md)に対して、オンプレミスのサイトで Site Recovery を構成します。
4. Azure に新しい SQL Server インスタンスをレプリケートするには、Site Recovery レプリケーションを使用します。 これは高い安全性のミラー コピーなので、プライマリ クラスターと同期されますが、Site Recovery レプリケーションを使用して Azure にレプリケートされます。


![Standard クラスター](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)

### <a name="failback-considerations"></a>フェールバックに関する考慮事項

SQL Server Standard のクラスターの場合、計画外フェールオーバー後のフェールバックでは、ミラーを再確立して、SQL Server のバックアップと復元 (ミラー インスタンスから元のクラスターへ) が必要になります。

## <a name="next-steps"></a>次のステップ
Site Recovery のアーキテクチャについて[こちら](site-recovery-components.md)をご覧ください。

