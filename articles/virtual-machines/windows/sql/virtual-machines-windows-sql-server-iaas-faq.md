---
title: Azure での Windows 仮想マシン上の SQL Server に関する FAQ | Microsoft Docs
description: この記事では、Azure VM で SQL Server を実行することに関してよく寄せられる質問に対する回答を示します。
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.openlocfilehash: 7f6ec1ee65727fb8c3c7d98f696c288e95ec880a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876192"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Azure の Windows 仮想マシン上で実行されている SQL Server についてよく寄せられる質問

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

この記事では、[Azure の Windows 仮想マシン上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) に関して特に多く寄せられる質問への回答を示します。

> [!NOTE]
> この記事では、Windows VM 上の SQL Server に固有の問題について説明します。 Linux VM で SQL Server を実行している場合は、[Linux での FAQ](../../linux/sql/sql-server-linux-faq.md) に関する記事をご覧ください。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>イメージ

1. **どのような SQL Server 仮想マシン ギャラリーのイメージを使用できますか?** 

   Azure では、Windows と Linux の両方のすべてのエディションで、SQL Server のサポートされているすべてのメジャー リリース用の仮想マシン イメージを保持します。 詳細については、[Windows VM イメージ](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)と [Linux VM イメージ](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create) の完全な一覧を参照してください。

1. **既存の SQL Server 仮想マシン ギャラリー イメージは更新されますか?**

   仮想マシン ギャラリー内の SQL Server イメージは、2 か月ごとに最新の Windows と Linux の更新プログラムによって更新されます。 Windows イメージの場合は、重要な SQL Server のセキュリティ更新プログラムや Service Pack など、Windows Update で重要とマークされている更新プログラムが含まれます。 Linux イメージの場合は、システムの最新の更新プログラムが含まれます。 SQL Server の累積更新プログラムは、Linux と Windows では異なる方法で処理されます。 Linux では、SQL Server の累積更新プログラムも更新に含まれます。 ただし、現時点では、Windows VM は、SQL Server または Windows Server の累積更新プログラムでは更新されません。

1. **SQL Server 仮想マシン イメージはギャラリーから削除される可能性がありますか?**

   はい。 Azure では、メジャー バージョンとエディションごとに 1 つのイメージのみを保持します。 たとえば、新しい SQL Server Service Pack がリリースされると、Azure はその Service Pack 用の新しいイメージをギャラリーに追加します。 前の Service Pack 用の SQL Server イメージは、Azure ポータルからただちに削除されます。 ただし、PowerShell からプロビジョニングする場合は、次の 3 か月間は引き続き使用できます。 3 か月が経過すると、前の Service Pack イメージは使用できなくします。 この削除ポリシーは、SQL Server のバージョンがそのライフサイクルの終わりに達した時点でサポートされなくなった場合にも適用されます。


1. **Azure portal に表示されない SQL Server の古いイメージをデプロイすることはできますか?**

   はい、PowerShell を使用します。 PowerShell を使用して SQL Server VM をデプロイする方法の詳細については、「[Azure PowerShell を使用して SQL Server 仮想マシンをプロビジョニングする方法](virtual-machines-windows-ps-sql-create.md)」をご覧ください。

1. **SQL Server VM から VHD イメージを作成できますか。**

   はい。ただし、考慮事項がいくつかあります。 この VHD を Azure の新しい VM にデプロイした場合は、ポータルに SQL Server の構成セクションが表示されません。 その場合は、PowerShell を使用して SQL Server の構成オプションを管理する必要があります。 また、そのイメージの基になった SQL VM のレートで課金されます。 これは、デプロイの前に VHD から SQL Server を削除した場合にも当てはまります。 

1. **仮想マシン ギャラリーに表示されていない構成 (Windows 2008 R2  + SQL Server 2012 など) をセットアップすることはできますか?**

   いいえ。 SQL Server を含む仮想マシン ギャラリー イメージでは、Azure portal または [PowerShell](virtual-machines-windows-ps-sql-create.md) を使用して、提供されているイメージのいずれかを選択する必要があります。 


## <a name="creation"></a>作成

1. **SQL Server で Azure 仮想マシンを作成するにはどうするのですか?**

   最も簡単な解決方法は、SQL Server を含む仮想マシンを作成することです。 Azure にサインアップして、ポータルから SQL VM を作成するチュートリアルについては、「[Azure ポータルでの SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」をご覧ください。 秒単位の SQL Server ライセンスを使用する仮想マシン イメージを選択するか、または独自の SQL Server ライセンスを適用できるイメージを使用できます。 無料ライセンス版 (Developer または Express) を利用するかオンプレミスのライセンスを再利用して、SQL Server を VM に手動でインストールすることもできます。 現在所有しているライセンスを使用する場合は、[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)が必要です。 詳細については、「[Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md)」(SQL Server Azure VM の料金ガイダンス) を参照してください。

1. **オンプレミス SQL Server データベースをクラウドに移行するにはどうすればよいですか?**

   まず SQL Server インスタンスで、Azure 仮想マシンを作成します。 次に、オンプレミス データベースをそのインスタンスに移行します。 データ移行の方法については、「[Azure VM の SQL Server への SQL Server データベースの移行](virtual-machines-windows-migrate-sql.md)」をご覧ください。

## <a name="licensing"></a>ライセンス

1. **Azure VM に SQL Server のライセンス版をインストールするにはどうすればよいですか?**

   2 つの方法があります。 [ライセンスをサポートする仮想マシン イメージ](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)の 1 つをプロビジョニングできます。これは、ライセンス持ち込み (BYOL) とも呼ばれます。 SQL Server インストール メディアを Windows Server VM にコピーしてから、SQL Server を VM にインストールするオプションもあります。 ただし、SQL Server を手動でインストールする場合、ポータルは統合されず、SQL Server IaaS Agent 拡張機能はサポートされません。そのため、このシナリオでは、自動バックアップや自動修正などの機能は動作しません。 こうした理由から、BYOL ギャラリー イメージのいずれかを使用することをお勧めします。 Azure VM 上で BYOL または独自の SQL Server メディアを使用するには、[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)が必要です。 詳細については、「[Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md)」(SQL Server Azure VM の料金ガイダンス) を参照してください。

1. **スタンバイ/フェールオーバーのみ使用するために作成した Azure VM 上の SQL Server のライセンスに料金を支払う必要がありますか。**

   「[Virtual Machines のライセンス FAQ](https://azure.microsoft.com/pricing/licensing-faq/)」に説明されているように、ソフトウェア アシュアランスによるライセンス モビリティを使用している場合は、HA デプロイにパッシブなセカンダリ レプリカとして参加している SQL Server のライセンスに料金を支払う必要はありません。 それ以外の場合は、ライセンスの料金を支払う必要があります。

1. **従量課金制のギャラリー イメージから作成した VM を、現在所有している SQL Server ライセンスを使用するように変更できますか。**

   はい。 従量課金ギャラリー イメージを最初に使用した場合、2 つのライセンス モデルを簡単に切り替えることができます。 ただし、BYOL イメージを最初に使用した場合、ライセンスを PAYG に切り替えることはできません。 詳細については、[SQL Server VM のライセンス モデルを変更する方法](virtual-machines-windows-sql-ahb.md)に関するページを参照してください。

   > [!Note]
   > 現時点では、この機能は、パブリック クラウドのお客様だけが利用できます。

1. **新しい SQL VM を作成するには、BYOL イメージまたは SQL VM RP を使用する必要がありますか。**

   ライセンス持ち込み (BYOL) イメージを使用できるのは、EA のお客様だけです。 ソフトウェア アシュアランスをお持ちのその他のお客様は、SQL VM リソース プロバイダーを使用して [Azure ハイブリッド特典 (AHB)](https://azure.microsoft.com/pricing/licensing-faq/) を利用した SQL VM を作成する必要があります。 

1. **ライセンス モデルの切り替えには、SQL Server のダウンタイムが必要ですか。**

   いいえ。 [ライセンス モデルの変更](virtual-machines-windows-sql-ahb.md)は、即座に有効になり、VM を再起動する必要はないため、SQL Server のダウンタイムは必要ありません。 ただし、SQL Server VM を SQL VM リソース プロバイダーに登録するには、[SQL IaaS 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)が前提条件です。SQL IaaS 拡張機能をインストールすると、SQL Server サービスが再起動します。 このため、SQL IaaS 拡張機能をインストールする必要がある場合、メンテナンス期間中に行う必要があります。 

1. **CSP サブスクリプションで Azure ハイブリッド特典をアクティブ化できますか。**

   はい。CSP サブスクリプションで Azure ハイブリッド特典を利用できます。 CSP のお客様は、最初に従量課金イメージをデプロイし、次にライセンス持ち込みに[ライセンス モデルを変更する](virtual-machines-windows-sql-ahb.md)必要があります。  

1. **新しい SQL VM リソース プロバイダーへの VM の登録には、追加のコストがかかりますか。**

   いいえ。 SQL VM リソース プロバイダーは、Azure VM 上で SQL Server に追加の管理機能を有効にするだけなので、追加料金は発生しません。 

1. **SQL VM リソース プロバイダーは、すべての顧客が使用できますか。**
 
   はい。 すべてのお客様は、新しい SQL VM リソース プロバイダーに登録できます。 ただし、SQL Server VM で [Azure ハイブリッド特典 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (または BYOL) をアクティブ化できるのは、ソフトウェア アシュアランスの特典をお持ちのお客様だけです。 

1. **VM リソースが移動または破棄されると、_Microsoft.SqlVirtualMachine_ リソースはどうなりますか?** 

   Microsoft.Compute/VirtualMachine リソースが破棄または移動されると、関連付けられた Microsoft.SqlVirtualMachine リソースに、操作が非同期的にレプリケートされることが通知されます。

1. **_Microsoft.SqlVirtualMachine_ リソースが破棄されると、VM はどうなりますか?**

    Microsoft.SqlVirtualMachine リソースが破棄されても、Microsoft.Compute/VirtualMachine リソースには影響しません。 ただし、ライセンスの変更により、元の既定のイメージ ソースに戻ります。 

1. **SQL VM リソース プロバイダーに自己デプロイ済みの SQL Server VM を登録することはできますか。**

    はい。 独自のメディアから SQL Server をデプロイし、SQL IaaS 拡張機能をインストールした場合は、SQL IaaS 拡張機能によって提供される管理の容易性の利点を得るため、リソース プロバイダーに SQL Server VM を登録できます。 ただし、自己デプロイ済みの SQL VM を従量課金に変換することはできません。

## <a name="administration"></a>管理

1. **同じ VM に SQL Server の 2 つ目のインスタンスをインストールできますか? 既定のインスタンスのインストール済みの機能を変更することはできますか?**

   はい。 SQL Server インストール メディアは、 **C** ドライブ上のフォルダーにあります。 その場所から、 **Setup.exe** を実行して、新しい SQL Server インスタンスを追加するか、またはコンピューター上の SQL Server の他のインストールされている機能を変更します。 一部の機能 (自動バックアップ、自動修正、Azure Key Vault の統合など) は、既定のインスタンスまたは正しく構成された名前付きインスタンス (質問 3 を参照) でしか動作しないので注意が必要です。 

1. **SQL Server の既定のインスタンスをアンインストールできますか?**

   はい。ただし、考慮事項がいくつかあります。 前出の回答で述べたように、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に依存する機能があります。  IaaS 拡張機能を削除せずに既定のインスタンスをアンインストールすると、拡張機能がそのインスタンスを探し続けるために、イベント ログ エラーが生成される可能性があります。 これらのエラーは次の 2 つのソースから発生します:**Microsoft SQL Server Credential Management**、**Microsoft SQL Server IaaS Agent**。 エラーの例を次に示します。

      SQL Server への接続を確立しているときにネットワーク関連またはインスタンス固有のエラーが発生しました。 サーバーが見つからないかアクセスできません。

   あえて既定のインスタンスをアンインストールする場合は、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)もアンインストールしてください。

1. **IaaS 拡張機能で SQL Server の名前付きインスタンスを使用できますか?**
   
   はい。ただし、その名前付きインスタンスが SQL Server の唯一のインスタンスであることと、元の既定のインスタンスが[正しくアンインストールされている](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md#installation)ことが条件となります。 既定のインスタンスがなく、1 つの SQL Server VM 上に複数の名前付きインスタンスがある場合は、IaaS 拡張機能はインストールに失敗します。 

1. **SQL VM から SQL Server を完全に削除できますか。**

   はい。ただし、[SQL Server Azure VM の価格に関するガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)のページで説明されているように、SQL VM に対しては引き続き課金されます。 SQL Server が不要になった場合は、新しい仮想マシンをデプロイし、データとアプリケーションを新しい仮想マシンに移行してから、 SQL Server 仮想マシンを削除してください。
   
## <a name="updating-and-patching"></a>更新プログラムと修正プログラムの適用

1. **Azure VM で SQL Server の異なるバージョン/エディションに変更するにはどうすればよいですか?**

   お客様は、SQL Server の目的のバージョンまたはエディションを含むセットアップ メディアを使用して、SQL Server のバージョン/エディションを変更できます。 エディションが変更されたら、Azure portal を使用して、VM の課金を正確に反映するように VM のエディション プロパティを変更してください。 詳細については、[SQL Server VM のエディションの変更](virtual-machines-windows-sql-change-edition.md)に関するページを参照してください。 


1. **SQL Server VM に更新プログラムと Service Pack を適用するにはどうすればよいですか?**

   仮想マシンでは、更新プログラムを適用するタイミングと方法を含めて、ホスト マシンを制御できます。 オペレーティング システムの場合、Windows 更新プログラムを手動で適用するか、または[自動修正](virtual-machines-windows-sql-automated-patching.md)と呼ばれるスケジュール サービスを有効にすることができます。 自動修正では、そのカテゴリ内の SQL Server の更新を含む、重要とマークされている更新プログラムがインストールされます。 SQL Server へのその他のオプションの更新プログラムは、手動でインストールする必要があります。

## <a name="general"></a>全般

1. **SQL Server フェールオーバー クラスター インスタンス (FCI) は Azure VM でサポートされますか?**

   はい。 [Windows フェールオーバー クラスターを Windows Server 2016 上で作成](virtual-machines-windows-portal-sql-create-failover-cluster.md)し、クラスター ストレージとして Storage Spaces Direct (S2D) を使用できます。 または、[Azure 仮想マシンでの SQL Server の高可用性とディザスター リカバリー](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)に関する記事の説明に従って、サードパーティのクラスタリング ソリューションまたは記憶域ソリューションを使用できます。

   > [!IMPORTANT]
   > 現時点で、Azure 上の SQL Server FCI では [SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)がサポートされていません。 FCI に参加している VM からこの拡張機能をアンインストールすることをお勧めします。 この拡張機能では、自動バックアップや自動修正などの機能のほか、ポータルの SQL 用の機能の一部がサポートされます。 エージェントをアンインストールすると、これらの機能が SQL VM で動作しなくなります。

1. **SQL VM と SQL Database サービスの違いは何ですか?**

   概念上、Azure 仮想マシンで SQL Server を実行することは、リモート データ センターで SQL Server を実行することと違いません。 これに対し、 [SQL Database](../../../sql-database/sql-database-technical-overview.md) はサービスとしてデータベースを提供します。 SQL Database では、データベースをホストするマシンにアクセスできません。 詳細な比較については、[クラウド SQL Server オプションであるAzure SQL (PaaS) Database および Azure VM (IaaS) の SQL Server の選択](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)に関するページを参照してください。

1. **Azure VM に SQL データ ツールをインストールするにはどうすればよいですか?**

    「[Microsoft SQL Server Data Tools - Business Intelligence for Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)」から、SQL データ ツールをダウンロードしてインストールします。

1. **SQL Server VM では MSDTC を使用した分散トランザクションはサポートされていますか?**
   
    はい。 ローカルの DTC は、SQL Server 2016 SP2 以降でサポートされています。 ただし、Always On 可用性グループを利用する場合は、フェールオーバー時に実行中のトランザクションが失敗し、再試行する必要があるため、アプリケーションをテストする必要があります。 クラスター化された DTC は、Windows Server 2019 以降で使用できます。 

## <a name="resources"></a>リソース

**Windows VM**:

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM のプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)
* [Azure VM の SQL Server へのデータベースの移行](virtual-machines-windows-migrate-sql.md)
* [Azure 仮想マシンにおける SQL Server の高可用性と障害復旧](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)
* [Azure Virtual Machines における SQL Server のアプリケーション パターンと開発計画](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**:

* [Linux VM における SQL Server の概要](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux VM のプロビジョニング](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [よく寄せられる質問 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server on Linux のドキュメント](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
