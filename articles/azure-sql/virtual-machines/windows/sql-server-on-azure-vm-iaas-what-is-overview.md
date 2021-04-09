---
title: Azure Windows 仮想マシンでの SQL Server の概要 | Microsoft Docs
description: クラウド内の Azure Virtual Machines 上の SQL Server の全エディションを、オンプレミスのハードウェアを一切管理することなく実行する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4b1234b88eafedbd03b78241baaa40231a6cbd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97356805"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Azure Virtual Machines 上の SQL Server とは何か (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

[Azure Virtual Machines 上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) では、オンプレミスのハードウェアを一切管理することなく全バージョンの SQL Server をクラウドで利用することができます。 また、SQL Server 仮想マシン (VM) によって従量課金制のライセンス コストが単純化されます。

Azure 仮想マシンは、世界中のさまざまな[リージョン](https://azure.microsoft.com/regions/)で実行されます。 また、[マシンのサイズ](../../../virtual-machines/sizes.md)も各種用意されています。 適切なバージョン、エディション、オペレーティング システムの SQL Server VM を仮想マシン イメージ ギャラリーで作成することができます。 これにより、仮想マシンは、多くの異なる SQL Server ワークロードに適した選択肢となります。

SQL Server on Azure VM を初めて使用する場合は、[Azure SQL ビデオ シリーズ](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)の "*SQL Server on Azure VM の概要*" に関するビデオをご覧ください。
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="automated-updates"></a>自動化された更新

Azure Virtual Machines 上の SQL Server では、[自動修正](automated-patching.md)を使用して、Windows と SQL Server の重要な更新プログラムを自動的にインストールするメンテナンス期間をスケジュール設定できます。

## <a name="automated-backups"></a>自動バックアップ

Azure Virtual Machines 上の SQL Server では、[自動バックアップ](automated-backup.md)を活用できます。これにより、データベースのバックアップが BLOB ストレージに定期的に作成されます。 また、この手法は手動で使用できます。 詳細については、「[Azure Storage を使用した SQL Server のバックアップと復元](azure-storage-sql-server-backup-restore-use.md)」を参照してください。

Azure では、Azure VM で実行されている SQL Server 向けのエンタープライズ クラスのバックアップ ソリューションも提供されています。 それはフル マネージド バックアップ ソリューションであり、Always On 可用性グループ、長期保有、特定の時点に復旧、一元的な管理と監視がサポートされています。 詳細については、[Azure VM での SQL Server に対する Azure Backup](../../../backup/backup-azure-sql-database.md) に関する記事をご覧ください。
  

## <a name="high-availability"></a>高可用性

高可用性が必要な場合は、SQL Server 高可用性グループの構成を検討してください。 これには、仮想ネットワーク内の Azure Virtual Machines 上の SQL Server の複数のインスタンスが含まれます。 高可用性ソリューションは手動で構成できるほか、Azure portal からテンプレートを使用して自動で構成することもできます。 高可用性を実現する各種方法の概要については、[Azure Virtual Machines 上の SQL Server の高可用性とディザスター リカバリー](business-continuity-high-availability-disaster-recovery-hadr-overview.md)に関する記事を参照してください。

## <a name="performance"></a>パフォーマンス

Azure 仮想マシンは、さまざまなワークロードのニーズを満たす複数のマシンのサイズを提供します。 また、SQL Server VM では、パフォーマンス要件に合わせて最適化される自動化されたストレージの構成も提供されます。 SQL Server VM のストレージの構成の詳細については、「[SQL Server VM のストレージの構成](storage-configuration.md)」を参照してください。 パフォーマンスを微調整するには、「[Azure Virtual Machines 上の SQL Server のパフォーマンスに関するベスト プラクティス](performance-guidelines-best-practices.md)」を参照してください。

## <a name="get-started-with-sql-server-vms"></a>SQL Server VM の使用を開始する

まず、必要なバージョン、エディション、オペレーティング システムの SQL Server 仮想マシン イメージを選んでください。 以下のセクションでは、SQL Server 仮想マシン ギャラリー イメージに関する、Azure Portal へのリンクを記載しています。

> [!TIP]
> SQL Server イメージの料金を理解する方法の詳細については、[Azure Virtual Machines 上の SQL Server の料金ガイダンス](pricing-guidance.md)に関する記事をご覧ください。 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> 従量課金制
次の表に示したのは、従量課金制で提供されている SQL Server イメージの一覧です。

| Version | オペレーティング システム | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise)、[Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard)、[Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web)、[Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

利用できる Linux SQL Server 仮想マシン イメージを確認するには、「[Azure Virtual Machines 上の SQL Server の概要 (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)」を参照してください。

> [!NOTE]
> SQL Server VM のライセンス モデルを従量課金制のライセンスから自分の所有ライセンスに変更できるようになりました。 詳細については、[SQL Server VM のライセンス モデルを変更する方法](licensing-model-azure-hybrid-benefit-ahb-change.md)に関するページを参照してください。 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> ライセンス持ち込み
ライセンス持ち込み (BYOL) を利用することもできます。 このシナリオでは、SQL Server ライセンスに対する追加料金はなく、VM のみに対して料金を支払います。  自分のライセンスを持ち込むと、継続的な運用環境のワークロードの費用を時間と共に削減できます。 この方法を利用するための要件については、「[SQL Server Azure VM の料金ガイダンス](pricing-guidance.md#byol)」を参照してください。

ライセンス持ち込みには、既存の従量課金制の SQL Server VM を変換する方法と、 **{BYOL}** というプレフィックスを使用してイメージをデプロイする方法があります。 従量制課金と BYOL との間でライセンス モデルを切り替える方法の詳細については、[SQL Server VM のライセンス モデルを変更する方法](licensing-model-azure-hybrid-benefit-ahb-change.md)に関するページを参照してください。 

| Version | オペレーティング システム | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise)、[Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

Azure portal で利用できない SQL Server の古いイメージを、PowerShell を使用してデプロイできます。 PowerShell を使用して使用可能なすべてのイメージを表示するには、次のコマンドを使用します。

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

PowerShell を使用して SQL Server VM をデプロイする方法の詳細については、「[Azure PowerShell を使用して SQL Server 仮想マシンをプロビジョニングする方法](create-sql-vm-powershell.md)」を参照してください。


### <a name="connect-to-the-vm"></a>VM に接続します
作成した SQL Server VM には、SQL Server Management Studio (SSMS) などのツールやアプリケーションから接続することができます。 手順については、「[Azure での SQL Server 仮想マシンへの接続](ways-to-connect-to-sql.md)」を参照してください。

### <a name="migrate-your-data"></a>データの移行
既存のデータベースがある場合は、新たにプロビジョニングした SQL Server VM にそれを移動することができます。 移行オプションの一覧とガイダンスについては、 [Azure VM の SQL Server へのデータベースの移行](migrate-to-vm-from-sql-server.md)に関するページを参照してください。

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Azure portal で Azure SQL リソースを作成して管理する

Azure portal には、SQL 仮想マシンを含む[すべての Azure SQL リソース](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql)を管理できる単一ページが用意されています。

**[Azure SQL リソース]** ページにアクセスするには、Azure portal のメニューの **[Azure SQL]** を選択するか、任意のページで **[Azure SQL]** を検索して選択します。

![Azure SQL を検索する](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> Azure SQL には、すべての Azure SQL データベース、エラスティック プール、論理サーバー、マネージド インスタンス、および仮想マシンにすばやく簡単にアクセスできる方法が用意されています。 Azure SQL はサービスでもリソースでもありません。 

既存のリソースを管理するには、一覧から目的の項目を選択します。 新しい Azure SQL リソースを作成するには、 **[+ 追加]** を選択します。 

![Azure SQL リソースを作成する](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

**[+ 追加]** を選択した後、任意のタイルで **[詳細の表示]** を選択して、さまざまなオプションに関する追加情報を表示します。

![データベース タイルの詳細](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

詳細については、次の情報を参照してください。

- [単一データベースを作成する](../../database/single-database-create-quickstart.md)
- [エラスティック プールの作成](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [マネージド インスタンスを作成する](../../managed-instance/instance-create-quickstart.md)
- [SQL Server 仮想マシンを作成する](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a> SQL Server VM イメージの更新ポリシー
Azure に保持される仮想マシン イメージは、サポートされるオペレーティング システム、バージョン、エディションの組み合わせごとに 1 つだけです。 つまりイメージは、いずれ更新され、古いイメージは削除されていくことになります。 詳細については、[SQL Server VM の FAQ](frequently-asked-questions-faq.md#images) に関するページの「**イメージ**」セクションを参照してください。

## <a name="customer-experience-improvement-program-ceip"></a>カスタマー エクスペリエンス向上プログラム (CEIP)
既定では、カスタマー エクスペリエンス向上プログラム (CEIP) が有効です。 これにより、レポートが定期的に Microsoft に送信され、SQL Server の改善に役立ちます。 プロビジョニング後に無効にする場合を除き、CEIP で必要な管理タスクはありません。 リモート デスクトップで VM に接続することで、CEIP をカスタマイズしたり無効にしたりすることができます。 その後、 **SQL Server エラーと使用状況レポート** ユーティリティを実行します。 レポートを無効にするには、指示に従います。 データの収集の詳細については、「[SQL Server のプライバシーに関する声明](/sql/sql-server/sql-server-privacy)」を参照してください。

## <a name="related-products-and-services"></a>関連製品およびサービス
### <a name="windows-virtual-machines"></a>Windows Virtual Machines
* [Azure Virtual Machines の概要](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>ストレージ
* [Microsoft Azure Storage の概要](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>ネットワーク
* [仮想ネットワークの概要](../../../virtual-network/virtual-networks-overview.md)
* [Azure 内の IP アドレス](../../../virtual-network/public-ip-addresses.md)
* [Azure Portal での完全修飾ドメイン名の作成](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL
* [SQL Server のドキュメント](/sql/index)
* [Azure SQL Database の比較](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>次のステップ

Azure Virtual Machines における SQL Server の概要:

* [Azure Portal での SQL Server VM の作成](sql-vm-create-portal-quickstart.md)

SQL Server VM についてよく寄せられる質問とその回答:

* [Azure Virtual Machines における SQL Server に関する FAQ](frequently-asked-questions-faq.md)

IaaS の SQL Server で N 層アプリケーションを実行するための参照アーキテクチャを表示する

* [SQL Server を使用した Azure の Windows N 層アプリケーション](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [高可用性を得るために複数の Azure リージョンで N 層アプリケーションを実行する](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)