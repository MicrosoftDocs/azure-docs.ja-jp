---
title: 料金ガイダンスおよびとコストの管理
description: 適切な SQL Server 仮想マシンの料金モデルを選択するためのベスト プラクティスについて説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d980b92bc4effc58ef84ef6ec70c3908f575e484
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615603"
---
# <a name="pricing-guidance-for-sql-server-on-azure-vms"></a>Azure VM 上の SQL Server の料金ガイダンス
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、[Azure Virtual Machines における SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md) の料金ガイダンスを示します。 コストに影響するいくつかのオプションがあるため、コストとビジネス要件が釣り合うように適切なイメージを選択することが重要です。

> [!TIP]
> SQL Server のエディションと仮想マシン (VM) のサイズの特定の組み合わせのコスト見積もりのみを確認したい場合は、[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) または [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux) の料金ページを参照してください。 **OS / ソフトウェア** の一覧から、お使いのプラットフォームと SQL Server エディションを選択します。
>
> ![VM 料金ページの UI](./media/pricing-guidance/virtual-machines-pricing-ui.png)
>
> または、[料金計算ツール](https://azure.microsoft.com/pricing/#explore-cost)を使用して、仮想マシンを追加して構成します。 

## <a name="free-licensed-sql-server-editions"></a>無料ライセンスの SQL Server エディション

概念実証の開発、テスト、または構築を行う場合は、無料ライセンスの **SQL Server Developer Edition** をご使用ください。 このエディションには SQL Server Enterprise エディションの全機能が備わっており､あらゆる種類のアプリケーションを構築､テストすることができます｡ ただし､運用環境で Developer エディションを実行することはできません｡ SQL Server Developer エディションの VM は､その VM のコスト分の料金のみ発生します｡これは､関係する SQL Server ライセンス コストがないためです｡

運用環境で軽量ワークロード (コア 4 未満、メモリ 1 GB 未満、データベース < 10 GB 未満) を実行する場合は、無料ライセンスの **SQL Server Express Edition** をご利用ください。 SQL Server Express エディションの VM もまた､その VM のコスト分の料金のみ発生します｡

このような開発/テストまたは軽量運用ワークロードについては、そのワークロードに応じて小さい VM サイズを選択することで費用を節約することもできます。 シナリオによっては､DS1v2 が賢明な選択肢であることもあります｡

これらのイメージのいずれかを使用して SQL Server 2017 を実行する Azure VM を作成するには、次のリンクを参照してください。

| プラットフォーム | 自由にライセンスされたイメージ |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>有料の SQL Server エディション

運用ワークロードが軽量でない場合は、次のいずれかの SQL Server エディションを使用します。

| SQL Server のエディション | ワークロード |
|-----|-----|
| Web | 小規模 Web サイト |
| Standard | 少量から中量のワークロード |
| Enterprise | 大量のワークロードまたはミッションクリティカルなワークロード|

これらのエディションの SQL Server ライセンス料金には 2 つのオプションがあります。"*使用した分を支払う*" 方法と、"*自分のライセンスを持ち込む (BYOL)* " 方法です。

## <a name="pay-per-usage"></a>使用した分を支払う

**使用した分の SQL Server ライセンス料金を支払う** 方法 (**従量課金制** とも呼ばれている) は、Azure VM を実行する秒単位のコストに SQL Server ライセンスのコストが含まれることを意味します。 SQL Server の各種エディション (Web、Standard、Enterprise) の料金は、[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) または [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux) の Azure Virtual Machines の料金ページで確認できます。

コストはすべてのバージョンの SQL Server (2012 SP3 から 2019 まで) で同じです。 1 秒あたりのライセンス コストは、VM vCPU の数によって異なります。

使用した分だけ SQL Server ライセンス料金を支払う方法は、次の場合にお勧めします。

- **一時的なワークロードまたは定期的なワークロード**。 たとえば、毎年 2 か月間行われるイベントや毎週月曜日に行われるビジネス分析をサポートする必要があるアプリです。

- **有効期間または規模が不明なワークロード**。 たとえば、数か月で不要になるアプリ、または要求によって必要な処理能力が増減するアプリです。

これらの従量課金制イメージのいずれかを使用して SQL Server 2017 を実行する Azure VM を作成するには、次のリンクを参照してください。

| プラットフォーム | ライセンスされたイメージ |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Azure portal で SQL Server 仮想マシンを作成する場合は、 **[サイズの選択]** ウィンドウに見積もりコストが表示されます。 この見積もりに含まれるのは、VM を実行するためのコンピューティング コストと、OS (Windows または他社製 Linux オペレーティング システム) のライセンス コストのみであることに注意してください。
>
> ![VM サイズを選択するブレード](./media/pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Web、Standard、および Enterprise エディションに対する追加の SQL Server ライセンス コストは含まれません。 正確な価格の見積もりを得るには、[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) または [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) の価格ページで、ご自身のオペレーティング システムと SQL Server エディションを選択します

> [!NOTE]
> ライセンス モデルを従量課金制のライセンスとライセンス持ち込み (BYOL) の間で切り替えられるようになりました。 詳細については、[SQL Server VM のライセンス モデルを変更する方法](licensing-model-azure-hybrid-benefit-ahb-change.md)に関するページを参照してください。 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a> ライセンスを持ち込む (BYOL)

**ライセンス モビリティを使用した自分の SQL Server ライセンスの持ち込み** (**BYOL**) は、Azure VM のソフトウェア アシュアランスで既存の SQL Server ボリューム ライセンスを使用することです。 BYOL を使用した SQL Server VM の料金は、SQL Server のライセンスではなく、VM を実行するコストに対してのみ発生します。これは、ボリューム ライセンス プログラムを介して、またはクラウド ソリューション パートナーを通して、既にライセンスとソフトウェア アシュアランスを取得している場合が対象になります。

> [!NOTE]
> BYOL イメージは、現在、Windows 仮想マシンでのみご利用いただけます。 ただし、SQL Server は Linux 専用の VM に手動でインストールできます。 [Linux VM 上の SQL Server の FAQ](../linux/frequently-asked-questions-faq.md) にあるガイドラインを参照してください。

ライセンス モビリティを使用した SQL Server ライセンスの持ち込みは、次の場合に推奨されます。

- **継続的なワークロード**。 たとえば、年中無休の業務をサポートする必要があるアプリ。

- **有効期間と規模がわかっているワークロード**。 たとえば、1 年にわたって必要で、要求が予測されているアプリ。

BYOL を SQL Server VM で利用するには、SQL Server Standard または Enterprise と[ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1) のライセンス (ボリューム ライセンス プログラムの必須オプション、他の製品では必要に応じて購入可能) が必要です。 ボリューム ライセンス プログラムで提供される料金レベルは、契約のタイプ、SQL Server の数やコミットメントによってさまざまです。 ただし、経験則として、継続的な運用環境のワークロードに自分のライセンスを持ち込むメリットは次のとおりです。

| BYOL のメリット | 説明 |
|-----|-----|
| **コスト削減** | [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)により、最大 55% の節約を実現できます。 詳細については、「[Azure での SQL Server 仮想マシンのライセンス モデルを変更する](licensing-model-azure-hybrid-benefit-ahb-change.md)」を参照してください。 |
| **無料のパッシブ セカンダリ レプリカ** | 自分のライセンスを持ち込むもう 1 つのメリットは、高可用性を実現するために SQL Server 1 つにつき [1 つのパッシブ セカンダリ レプリカの無料ライセンス](https://azure.microsoft.com/pricing/licensing-faq/)を得られることです。 これによって、高可用性 SQL Server デプロイ (Always On 可用性グループの使用など) のライセンス コストを半減できます。 パッシブ セカンダリを実行する権限は、フェールオーバー サーバーのソフトウェア アシュアランス特典で提供されます。 |

これらのライセンス持ち込みイメージのいずれかを使用して SQL Server 2017 を実行する Azure VM を作成するには、先頭に "{BYOL}" が付いた VM を参照してください。

- [SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Azure で使用する SQL Server ライセンス数を 10 日以内にお知らせください。 前のイメージのリンクに、この方法が記載されています。

> [!NOTE]
> ライセンス モデルを従量課金制のライセンスとライセンス持ち込み (BYOL) の間で切り替えられるようになりました。 詳細については、[SQL Server VM のライセンス モデルを変更する方法](licensing-model-azure-hybrid-benefit-ahb-change.md)に関するページを参照してください。 



## <a name="reduce-costs"></a>コストの削減

不要なコストがかからないようにするには、最適な仮想マシンのサイズを選択し、ワークロードが継続して実行されないよう断続的にシャットダウンすることを検討してください。

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a> VM の適切なサイズ設定

SQL Server のライセンス コストは vCPU の数に直接関連します。 CPU、メモリ、記憶域、および I/O 帯域幅の予想されるニーズに合った VM サイズを選択してください。 マシンのサイズ オプションの一覧については、[Windows VM のサイズ](../../../virtual-machines/sizes.md)と [Linux VM のサイズ](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをそれぞれご覧ください。

特定の種類の SQL Server ワークロードで適切に動作する新しいマシン サイズがあります。 こうしたマシン サイズでは、メモリ、記憶域、および I/O 帯域幅が高いレベルで維持されますが、仮想化されたコア数は少なくなります。 たとえば、次の例を考えてみましょう。

| VM サイズ | vCPU 数 | メモリ | ディスクの最大数 | 最大 I/O スループット | SQL Server のライセンス コスト | 合計コスト (コンピューティング + ライセンス) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51,200 IOPS または 768 MB/秒 | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51,200 IOPS または 768 MB/秒 | 75% 削減 | 57% 削減 |

> [!IMPORTANT]
> これは、特定の時点の例です。 最新の仕様については、マシンのサイズに関する記事と、[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) および [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) それぞれの Azure 価格ページをご覧ください。

前の例で、**Standard_DS14v2** と **Standard_DS14-4v2** の仕様は、vCPU を除いて同じであることがわかります。 **Standard_DS14-4v2** マシン サイズの末尾のサフィックス **-4v2** は、アクティブな vCPU の番号を示します。 SQL Server のライセンス コストは vCPU の数と関連するため、追加 vCPU が不要なシナリオでは、これにより VM のコストが大幅に削減されます。 これは 1 つの例であり、このサフィックス パターンで特定される、vCPU が制約されたマシン サイズは多数あります。 詳細については、[データベース作業のコスト効率を高めるための新しい Azure VM サイズについての発表](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)に関するページをご覧ください。

### <a name="shut-down-your-vm-when-possible"></a>可能な場合は VM をシャットダウン

使用しているワークロードが継続して実行されない場合は、活動しない期間に仮想マシンを停止することを検討してください。 料金は使用した分だけになります。

たとえば、Azure VM で SQL Server を試しているだけの場合、誤って実行したままにして数週間分の料金が発生するのは望ましくありません。 1 つの解決方法としては、[自動シャットダウン機能](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)を使用します。

![SQL Server VM の自動シャットダウン](./media/pricing-guidance/sql-vm-auto-shutdown.png)

自動シャットダウンは [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab) で提供されます。他にも多数の類似した機能があります。

他のワークロードでは、[Azure Automation](https://azure.microsoft.com/services/automation/) など、スクリプト ソリューションによる Azure VM の自動シャットダウンと再起動を検討してください。

> [!IMPORTANT]
> 課金を避けるためには、VM をシャットダウンして割り当て解除するしかありません。 停止するだけ、または電源オプションを使用した VM のシャットダウンでは、使用料金は発生します。

## <a name="next-steps"></a>次のステップ

Azure の一般的な料金ガイダンスについては、「[Azure の課金とコスト管理で予想外のコストを防ぐ](../../../cost-management-billing/cost-management-billing-overview.md)」をご覧ください。 最新の Azure Virtual Machines の料金 (SQL Server を含む) については、[Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) および [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) の Azure Virtual Machines の料金ページを参照してください。

Azure Virtual Machines における SQL Server の概要については、次の記事を参照してください。

- [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Linux VM における SQL Server の概要](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
