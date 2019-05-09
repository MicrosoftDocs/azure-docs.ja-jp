---
title: SQL Server と Azure Site Recovery を使用して SQL Server のディザスター リカバリーを設定する | Microsoft Docs
description: この記事では、SQL Server と Azure Site Recovery を使用して、SQL Server のディザスター リカバリーを設定する方法について説明します。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 67526eddd19c5869aa54432f963d9b80396f878d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270984"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>SQL Server のためにディザスター リカバリーを設定する

この記事では、SQL Server のビジネス継続性とディザスター リカバリー (BCDR) テクノロジおよび [Azure Site Recovery](site-recovery-overview.md) の組み合わせを使用してアプリケーションの SQL Server バックエンドを保護する方法について説明します。

開始する前に、フェールオーバー クラスタリング、Always On 可用性グループ、データベース ミラーリング、ログ配布など、SQL Server ディザスター リカバリー機能についてよく理解してください。


## <a name="sql-server-deployments"></a>SQL Server のデプロイメント

多くのワークロードは SQL Server を基盤として使用します。これは SharePoint、Dynamics、SAP などのアプリと統合して、データ サービスを実装できます。  SQL Server は次のさまざまな方法でデプロイできます。

* **スタンドアロンの SQL Server**: SQL Server とすべてのデータベースは、1 つのマシン (物理または仮想) 上でホストされます。 仮想化する場合、ローカルの高可用性のためにホストのクラスタリングを使用します。 ゲストレベルの高可用性は実装されません。
* **SQL Server フェールオーバー クラスタリング インスタンス (Always On FCI)**: Windows フェールオーバー クラスター内には、共有ディスクを使用してインスタンス化された SQL Server を実行する 2 つ以上のノードが構成されます。 ノードが停止した場合、クラスターは SQL Server を別のインスタンスにフェールオーバーできます。 通常、この設定はプライマリ サイトに高可用性を実装するために使用されます。 このデプロイメントでは、共有ストレージ層の障害や停止は保護されません。 共有ディスクは、iSCSI、ファイバー チャネル、または共有 VHDX を使用して実装できます。
* **SQL Always On 可用性グループ**: 2 つ以上のノードがシェアード ナッシング クラスター内に設定されます。この場合、SQL Server データベースは、同期レプリケーションと自動フェールオーバーを使用して可用性グループ内に構成されます。

  この記事では、次に示すネイティブの SQL ディザスター リカバリー テクノロジを活用して、データベースを リモート サイトに復旧します。

* SQL Always On 可用性グループ: SQL Server 2012 または 2014 Enterprise の各エディションのディザスター リカバリーを提供します。
* 高い安全性モードでの SQL Database ミラーリング: SQL Server Standard エディション (全バージョン) または SQL Server 2008 R2 用です。

## <a name="site-recovery-support"></a>Site Recovery のサポート

### <a name="supported-scenarios"></a>サポートされるシナリオ
Site Recovery は、次の表のように SQL Server を保護できます。

**シナリオ** | **セカンダリ サイトへ** | **Azure へ**
--- | --- | ---
**Hyper-V** | はい | はい
**VMware** | はい | はい
**物理サーバー** | はい | はい
**Azure** |NA| はい

### <a name="supported-sql-server-versions"></a>サポートされる SQL Server のバージョン
これらの SQL Server バージョンは、サポートされるシナリオに対応しています。

* SQL Server 2016 Enterprise および Standard
* SQL Server 2014 Enterprise および Standard
* SQL Server 2012 Enterprise および Standard
* SQL Server 2008 R2 Enterprise および Standard

### <a name="supported-sql-server-integration"></a>サポートされる SQL Server の統合

Site Recovery は、次の表に要約したネイティブの SQL Server の BCDR テクノロジと統合して、ディザスター リカバリー ソリューションを提供できます。

**機能** | **詳細** | **SQL Server** |
--- | --- | ---
**Always On 可用性グループ** | SQL Server の複数のスタンドアロン インスタンスであり、複数のノードを持つフェールオーバー クラスターでそれぞれが実行されます。<br/><br/>データベースは、SQL Server インスタンス上でコピー (ミラー化) が可能なフェールオーバー グループにグループ化できるため、共有記憶域は必要ありません<br/><br/>プライマリ サイトと 1 つまたは複数のセカンダリ サイトの間でディザスター リカバリーを実現します。 2 つのノードをシェアード ナッシング クラスターに設定できます。このクラスターでは、同期レプリケーションと自動フェールオーバーを設定した可用性グループに SQL Server データベースを構成しておきます。 | SQL Server 2016、SQL Server 2014 および SQL Server 2012 Enterprise Edition
**フェールオーバー クラスタリング (Always On FCI)** | SQL Server は、Windows フェールオーバー クラスタリングを利用して、オンプレミスの SQL Server ワークロードの高可用性を実現しています。<br/><br/>共有ディスクを備えた、SQL Server のインスタンスを実行しているノードは、フェールオーバー クラスター内に構成されます。 インスタンスがダウンした場合、クラスターは別のクラスターにフェールオーバーします。<br/><br/>クラスターは、共有ストレージのエラーまたは障害からは保護しません。 共有ディスクは、iSCSI、ファイバー チャネル、または共有 VHDX を使用して実装できます。 | SQL Server Enterprise エディション<br/><br/>SQL Server Standard エディション (2 つのノードのみに制限)
**データベース ミラーリング (高い安全性モード)** | 1 つのセカンダリ コピーで 1 つのデータベースを保護します。 高い安全性 (同期) と高パフォーマンス (非同期) の両方のレプリケーション モードで使用できます。 フェールオーバー クラスターは必要はありません。 | SQL Server 2008 R2<br/><br/>SQL Server Enterprise のすべてのエディション
**スタンドアロンの SQL Server** | SQL Server とデータベースは、単一のサーバー (物理または仮想) でホストされます。 サーバーが仮想である場合、ホスト クラスタリングが高可用性のために使用されます。 ゲストレベルの高可用性はありません。 | Enterprise または Standard エディション

## <a name="deployment-recommendations"></a>デプロイメントに関する推奨事項

次の表は、Site Recovery に SQL Server の BCDR テクノロジを統合するための推奨事項を示しています。

| **バージョン** | **エディション** | **Deployment** | **オンプレミスからオンプレミス** | **オンプレミスから Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2016、2014、2012 のいずれか |Enterprise |フェールオーバー クラスター インスタンス |Always On 可用性グループ |Always On 可用性グループ |
|| Enterprise |高可用性のための Always On 可用性グループ |Always On 可用性グループ |Always On 可用性グループ |
|| Standard |フェールオーバー クラスター インスタンス (FCI) |ローカルのミラーを使用した Site Recovery レプリケーション |ローカルのミラーを使用した Site Recovery レプリケーション |
|| Enterprise または Standard |スタンドアロン |Site Recovery レプリケーション |Site Recovery レプリケーション |
| SQL Server 2008 R2 または 2008 |Enterprise または Standard |フェールオーバー クラスター インスタンス (FCI) |ローカルのミラーを使用した Site Recovery レプリケーション |ローカルのミラーを使用した Site Recovery レプリケーション |
|| Enterprise または Standard |スタンドアロン |Site Recovery レプリケーション |Site Recovery レプリケーション |
| SQL Server (全バージョン) |Enterprise または Standard |フェールオーバー クラスター インスタンス - DTC アプリケーション |Site Recovery レプリケーション |サポートされていません |

## <a name="deployment-prerequisites"></a>デプロイの前提条件

* サポートされている SQL Server のバージョンを実行しているオンプレミスの SQL Server デプロイメント。 通常、SQL Server には Active Directory も必要です。
* デプロイするシナリオの要件。 [Azure](site-recovery-support-matrix-to-azure.md) や [オンプレミス](site-recovery-support-matrix.md)へのレプリケーションのサポート要件と、[デプロイメントの前提条件](site-recovery-prereq.md)をよく理解します。

## <a name="set-up-active-directory"></a>Active Directory のセットアップ

SQL Server を正常に実行するために、セカンダリ復旧サイトに Active Directory をセットアップします。

* **小規模企業** - アプリケーションの数が少なく、オンプレミスのサイト用のドメイン コントローラーが 1 つしかない場合にサイト全体をフェールオーバーするには、Site Recovery レプリケーションを使用して、セカンダリ データセンターまたは Azure にドメイン コント ローラーをレプリケートすることをお勧めします。
* **中規模および大企業** - アプリケーションの数が多く、Active Directory フォレストがある場合に、アプリケーションまたはワークロード単位でフェールオーバーするには、追加のドメイン コントローラーをセカンダリ データセンターまたは Azure にセットアップすることをお勧めします。 Always On 可用性グループを使用してリモート サイトに復旧する場合は、もう 1 つ追加のドメイン コントローラーをセカンダリ サイトまたは Azure にセットアップして、復旧した SQL Server インスタンスに対して使用することをお勧めします。

この記事に記載された手順は、2 番目の拠点でドメイン コントローラーが使用できることを前提としています。 [こちら](site-recovery-active-directory.md) を参照してください。


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>SQL Server Always On との統合: Azure へのレプリケーション

次の手順を実行する必要があります。

1. Azure Automation アカウントにスクリプトをインポートします。 これは SQL 可用性グループをフェールオーバーするスクリプトを [Resource Manager 仮想マシン](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1)と[クラシック仮想マシン](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)に含んでいます。

    [![DAzure に配置する(https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. 復旧計画の最初のグループの事前アクションとして、ASR SQL-FailoverAG を追加します。

1. 可用性グループの名前を指定するオートメーション変数を作成するには、スクリプトで使用できる指示に従います。

### <a name="steps-to-do-a-test-failover"></a>テスト フェールオーバーを実行する手順

SQL Always On は、テスト フェールオーバーをネイティブでサポートしていません。 そこで、次の方法をお勧めします。

1. Azure で可用性グループ レプリカをホストする仮想マシンに [Azure Backup](../backup/backup-azure-arm-vms.md) をセットアップします。

1. 復旧計画のテスト フェールオーバーをトリガーする前に、前の手順で作成したバックアップから仮想マシンを復元します。

    ![Azure Backup からの復元](./media/site-recovery-sql/restore-from-backup.png)

1. バックアップから復元された仮想マシンで[クォーラムを強制](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)します。

1. リスナーの IP アドレスをテスト フェールオーバーのネットワークで利用できる IP アドレスに更新します。

    ![リスナー IP を更新する](./media/site-recovery-sql/update-listener-ip.png)

1. リスナーをオンラインにします。

    ![リスナーをオンラインにする](./media/site-recovery-sql/bring-listener-online.png)

1. 1 つの IP が各可用性グループ リスナーに対応するフロントエンド IP プールの下に作成された状態、そしてバックエンド プールに SQL 仮想マシンが追加された状態で、ロード バランサーを作成します。

     ![ロード バランサーを作成する - フロントエンド IP プール](./media/site-recovery-sql/create-load-balancer1.png)

    ![ロード バランサーを作成する - バックエンド プール](./media/site-recovery-sql/create-load-balancer2.png)

1. 復旧計画のテスト フェールオーバーを行います。

### <a name="steps-to-do-a-failover"></a>フェールオーバーを実行する手順

復旧計画でスクリプトを追加し、テスト フェールオーバーを実行することで復旧計画を検証したら、復旧計画のフェールオーバーを行うことができます。


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>SQL Server Always On との統合: セカンダリ オンプレミス サイトへのレプリケーション

SQL Server が可用性グループ (または FCI) を使用して高可用性を実現している場合は、復旧サイトでも可用性グループを使用することをお勧めします。 これは、分散トランザクションを使用しないアプリ向けであることに注意してください。

1. [データベースを構成](https://msdn.microsoft.com/library/hh213078.aspx) します。
1. セカンダリ サイトに、仮想ネットワークを作成します。
1. 仮想ネットワークとプライマリ サイトの間に、サイト間 VPN 接続を設定します。
1. 復旧サイトに仮想マシンを作成し、SQL Server を仮想マシンにインストールします。
1. 新しい SQL Server VM に、既存の Always On 可用性グループを拡張します。 この SQL Server インスタンスは、非同期レプリカ コピーとして構成します。
1. 可用性グループ リスナーを作成するか、または既存のリスナーを更新して、非同期のレプリカ仮想マシンを含めます。
1. アプリケーション ファームがリスナーを使用してセットアップされていることを確認します。 データベース サーバー名を使用してセットアップされている場合は、リスナーを使用するように更新して、フェールオーバー後に再構成する必要がないようにしてください。

分散トランザクションを使用するアプリケーションの場合は、[VMware/物理サーバー サイト間レプリケーション](site-recovery-vmware-to-vmware.md)と共に Site Recovery をデプロイすることをお勧めします。

### <a name="recovery-plan-considerations"></a>復旧計画に関する考慮事項
1. このサンプル スクリプトは、プライマリ サイトおよびセカンダリ サイト上の VMM ライブラリに追加します。

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. アプリケーションの復旧計画を作成するときに、可用性グループをフェールオーバーするスクリプトを呼び出す「pre action to Group-1」というスクリプト化した手順を追加します。

## <a name="protect-a-standalone-sql-server"></a>スタンドアロンの SQL Server を保護する

このシナリオでは、Site Recovery レプリケーションを使用して、SQL Server マシンを保護することをお勧めします。 正確な手順は、SQL Server が VM か物理サーバーか、および Azure またはセカンダリ オンプレミス サイトのどちらにレプリケートするかによって異なります。 [Site Recovery のシナリオ](site-recovery-overview.md)に関するページをご覧ください。

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>SQL Server クラスターの保護 (Standard エディション/Windows Server 2008 R2)

SQL Server Standard エディション または SQL Server 2008 R2 を実行するクラスターでは、Site Recovery レプリケーションを使用して SQL Server を保護することをお勧めします。

### <a name="on-premises-to-on-premises"></a>オンプレミス間

* アプリが分散トランザクションを使用している場合は、Hyper-V 環境には [SAN レプリケーションを使用した Site Recovery](site-recovery-vmm-san.md) を、VMware 環境には [VMware/物理サーバーから VMware へ](site-recovery-vmware-to-vmware.md)をデプロイすることをお勧めします。
* DTC 以外のアプリケーションの場合、前述のアプローチで、ローカルの安全性の高い DB ミラーを使用し、スタンドアロン サーバーとしてクラスターを復旧します。

### <a name="on-premises-to-azure"></a>オンプレミスと Azure 間

Site Recovery は、Azure にレプリケートするときに、ゲスト クラスター サポートを提供していません。 SQL Server も Standard エディション用に低コストのディザスター リカバリー ソリューションを提供しません。 このシナリオでは、スタンドアロンの SQL Server でオンプレミスの SQL Server クラスターを保護し、Azure で復旧することをお勧めします。

1. オンプレミスのサイトで、追加のスタンドアロン SQL Server インスタンスを構成します。
1. このインスタンスを、保護が必要なデータベースのミラーとして機能するように構成します。 高い安全性モードでミラーリングを構成します。
1. [Hyper-V](site-recovery-hyper-v-site-to-azure.md) または [VMware VM/物理サーバー](site-recovery-vmware-to-azure-classic.md)に対して、オンプレミスのサイトで Site Recovery を構成します。
1. Azure に新しい SQL Server インスタンスをレプリケートするには、Site Recovery レプリケーションを使用します。 これは高い安全性のミラー コピーなので、プライマリ クラスターと同期されますが、Site Recovery レプリケーションを使用して Azure にレプリケートされます。


![Standard クラスター](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>フェールバックに関する考慮事項

SQL Server Standard のクラスターの場合、計画されていないフェールオーバー後のフェールバックでは、ミラーを再確立して、SQL Server のバックアップと復元 (ミラー インスタンスから元のクラスターへ) が必要になります。

## <a name="next-steps"></a>次の手順
Site Recovery のアーキテクチャについて[こちら](site-recovery-components.md)をご覧ください。
