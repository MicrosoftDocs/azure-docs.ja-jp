---
title: "Azure Virtual Machines における SQL Server の効率的なコスト管理 | Microsoft Docs"
description: "適切な SQL Server 仮想マシンの料金モデルを選択するためのベスト プラクティスについて説明します。"
services: virtual-machines-windows
documentationcenter: na
author: luisherring
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/18/2017
ms.author: jroth
ms.openlocfilehash: 29a92f0c70bffedeb75c50b7fc3b687ee5ee227d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>SQL Server Azure VM の料金ガイダンス

このトピックでは、Azure における SQL Server 仮想マシンの料金ガイダンスを説明します。 コストに影響するいくつかのオプションがあるため、コストとビジネス要件が釣り合うように適切なイメージを選択することが重要です。

## <a name="free-licensed-sql-server-editions"></a>無料ライセンスの SQL Server エディション

概念実証の開発、テスト、または構築を行う場合は、無料ライセンスの **SQL Server Developer Edition** を使用してください。 このエディションには、SQL Server Enterprise Edition のすべての機能が含まれるため、これを使用してどのようなアプリケーションでも構築できます。 ただし、運用環境で実行することはできません。 SQL Server Developer VM では、SQL Server のライセンスではなく、VM のコストに対してのみ料金が発生します。

運用環境で軽量ワークロード (コア 4 未満、メモリ 1 GB 未満、データベース < 10 GB 未満) を実行しようとしている場合は、無料ライセンスの **SQL Server Express Edition** を使用してください。 SQL Express VM では、SQL のライセンスではなく、VM のコストに対してのみ料金が発生します。

このような開発/テストまたは軽量運用ワークロードの場合、そのワークロードに応じて小さい VM サイズを選択することでも費用を節約できます。 このようなワークロードでは DS1v2 をお勧めします。

このようなイメージの 1 つを使用して SQL Server 2016 Azure VM を作成するには、次のリンクを参照してください。

- [SQL Server 2016 Developer Azure VM](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1DeveloperWindowsServer2016-ARM)
- [SQL Server 2016 Express Azure VM](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1ExpressWindowsServer2016-ARM)

## <a name="paid-sql-server-editions"></a>有料の SQL Server エディション

運用ワークロードが軽量でない場合は、次のいずれかの SQL Server エディションを使用します。

| SQL Server のエディション | ワークロード |
|-----|-----|
| Web | 小規模 Web サイト |
| 標準 | 少量から中量のワークロード |
| Enterprise | 大量のワークロードまたはミッションクリティカルなワークロード|

これらのエディションの SQL Server ライセンス料金には 2 つのオプションがあります。"*使用した分を支払う*" 方法と、"*自分のライセンスを持ち込む*" 方法です。

### <a name="pay-per-usage"></a>使用した分を支払う

**使用した分の SQL Server ライセンス料金を支払う**方法では、Azure VM を実行する分単位のコストに SQL Server ライセンスのコストが含まれます。 さまざまな SQL Server エディション (Web、Standard、Enterprise) の料金は、[Azure VM の料金に関するページ](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard)で確認できます。 コストはすべてのバージョンの SQL Server (2008 R2 から 2016 まで) で同じです。 一般的な SQL Server ライセンスと同様に、分あたりのライセンス コストは VM コアの数によって変わります。

使用した分だけ SQL Server ライセンス料金を支払う方法は、次の場合にお勧めします。

- 一時的なワークロードまたは定期的なワークロード。 たとえば、毎年 2 か月間行われるイベントや毎週月曜日に行われるビジネス分析をサポートする必要があるアプリです。
- 有効期間または規模が不明なワークロード。 たとえば、数か月で不要になるアプリ、または要求によって必要な処理能力が増減するアプリです。

このような使用した分を支払うイメージの 1 つを使用して SQL Server 2016 Azure VM を作成するには、次のリンクを参照してください。

- [SQL Server 2016 Web Azure VM](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016)
- [SQL Server 2016 Standard Azure VM](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016)
- [SQL Server 2016 Enterprise Azure VM](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016)

> [!IMPORTANT]
> Azure Portal で SQL Server 仮想マシンを作成するとき、**[サイズの選択]** ブレードに表示される月額料金の見積もりには、SQL Server のライセンス費用は含まれていません。 この料金は VM 単体の費用です。
>
> ![VM サイズを選択するブレード](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>無料ライセンスの SQL Server Express エディションと SQL Server Developer エディションでは、この料金が概算費用の合計になります。 ただし、Web、Standard、および Enterprise については、追加される SQL ライセンス料金を「[Windows Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)」ページで調べてください。 この料金のページで、SQL Server の目的のエディションを選択してください。

### <a name="bring-your-own-license-byol"></a>ライセンスを持ち込む (BYOL)

**ライセンス モビリティを使用した自分の SQL Server ライセンスの持ち込み** (**BYOL**) は、Azure VM のソフトウェア アシュアランスで既存の SQL Server ボリューム ライセンスを使用することです。 BYOL を使用した SQL Server VM の料金は、SQL Server のライセンスではなく、VM を実行するコストに対してのみ発生します。これは、ボリューム ライセンス プログラムを介して既にライセンスとソフトウェア アシュアランスを取得している場合が対象になります。

ライセンス モビリティによる自分の SQL ライセンスの持ち込みは、次の場合にお勧めします。

- 継続的なワークロード。 たとえば、年中無休の業務をサポートする必要があるアプリ。
- 有効期間と規模がわかっているワークロード。 たとえば、1 年にわたって必要で、要求が予測されているアプリ。

BYOL を SQL Server VM で使用するためには、SQL Server Standard または Enterprise と[ソフトウェア アシュアランス](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1) ([ボリューム ライセンス](https://www.microsoft.com/en-us/download/details.aspx?id=10585) プログラムの必須オプション、他の製品では必要に応じて購入可能) が必要です。  ボリューム ライセンス プログラムで提供される料金レベルは、契約のタイプ、SQL Server の数やコミットメントによってさまざまです。 ただし、経験則として、継続的な運用環境のワークロードに自分のライセンスを持ち込むメリットは次のとおりです。

| BYOL のメリット | Description |
|-----|-----|
| **コスト削減** | 自分の SQL Server ライセンスの持ち込みが、使用した分だけ支払うよりもコスト効果が高くなるのは、ワークロードによって SQL Server Standard または Enterprise が *10 か月を超えて* 実行され続ける場合です。 |
| **長期的なコスト削減** | 平均すると、当初 3 年間は SQL Server ライセンスを購入または更新する方が "*1 年あたり 30% のコストダ ウン*" です。 3 年経過した後は、ライセンスを更新する必要がなくなり、ソフトウェア アシュアランスのみの支払いになります。 その時点で、"*200% のコスト ダウン*" になります。 |
| **無料のパッシブ セカンダリ レプリカ** | 自分のライセンスを持ち込むもう 1 つのメリットは、高可用性を実現するために SQL Server 1 つにつき [1 つのパッシブ セカンダリ レプリカの無料ライセンス](https://azure.microsoft.com/pricing/licensing-faq/)を得られることです。 これによって、高可用性 SQL Server デプロイ (Always On 可用性グループの使用など) のライセンス コストを半減できます。 パッシブ セカンダリを実行する権限は、フェールオーバー サーバーのソフトウェア アシュアランス特典で提供されます。 |

自分のライセンスを持ち込むイメージの 1 つを使用して SQL Server 2016 Azure VM を作成するには、プレフィックスに "{BYOL}" が付いた VM を参照してください。

- [SQL Server 2016 Enterprise Azure VM](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)
- [SQL Server 2016 Standard Azure VM](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)

> [!NOTE]
> Azure で使用する SQL Server ライセンス数を 10 日以内にお知らせください。 前のイメージのリンクに、この方法が記載されています。

## <a name="avoid-unecessary-costs"></a>不要なコストの回避

使用しているワークロードが継続して実行されない場合は、活動しない期間に仮想マシンを停止することを検討してください。 料金は使用した分だけになります。

たとえば、Azure VM で SQL Server を試しているだけの場合、誤って実行したままにして数週間分の料金が発生するのは望ましくありません。 1 つの解決方法としては、[自動シャットダウン機能](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)を使用します。

![SQL VM 自動シャットダウン](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

自動シャットダウンは [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab) で提供されます。他にも多数の類似した機能があります。

他のワークロードでは、[Azure Automation](https://azure.microsoft.com/services/automation/) などスクリプト ソリューションによる Azure VM の自動シャットダウンと再起動を検討してください。

> [!IMPORTANT]
> 課金を避けるためには、VM をシャットダウンして割り当て解除するしかありません。 停止するだけ、または電源オプションを使用した VM のシャットダウンでは、使用料金は発生します。

## <a name="next-steps"></a>次のステップ

Azure の一般的な料金ガイダンスについては、「[Azure の課金とコスト管理で予想外のコストを防ぐ](../../../billing/billing-getting-started.md)」をご覧ください。

SQL Server を含め、仮想マシンの最新の料金については、[Azure VM の料金に関するページ](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard)をご覧ください。

SQL Server Virtual Machines に関する他のトピックについては、[Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)に関するページをご覧ください。
