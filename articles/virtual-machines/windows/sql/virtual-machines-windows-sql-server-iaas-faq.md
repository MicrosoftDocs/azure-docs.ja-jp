---
title: Azure での Windows 仮想マシン上の SQL Server に関する FAQ | Microsoft Docs
description: この記事では、Azure VM で SQL Server を実行することに関してよく寄せられる質問に対する回答を示します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: eeda78c69c21fafcbe64071422bf7d73a4737249
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208322"
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

1. **SQL Server VM の Azure SQL Server マーケットプレース イメージを汎用化し、それを使用して VM をデプロイできますか?**

   はい。ただし、ポータルで SQL Server VM を管理したり、修正プログラムの自動適用や自動バックアップなどの機能を利用したりするには、[各 SQL Server VM を SQL Server VM リソース プロバイダーに登録する](virtual-machines-windows-sql-register-with-resource-provider.md)必要があります。 リソース プロバイダーに登録するとき、各 SQL Server VM のライセンスの種類も指定する必要があります。 

1. **独自の VHD を使用して SQL Server VM をデプロイできますか?**

   はい。ただし、ポータルで SQL Server VM を管理したり、修正プログラムの自動適用や自動バックアップなどの機能を利用したりするには、[各 SQL Server VM を SQL Server VM リソース プロバイダーに登録する](virtual-machines-windows-sql-register-with-resource-provider.md)必要があります。

1. **仮想マシン ギャラリーに表示されていない構成 (Windows 2008 R2  + SQL Server 2012 など) をセットアップすることはできますか?**

   いいえ。 SQL Server を含む仮想マシン ギャラリー イメージでは、Azure portal または [PowerShell](virtual-machines-windows-ps-sql-create.md) を使用して、提供されているイメージのいずれかを選択する必要があります。 ただし、Windows VM をデプロイしたり、それに SQL Server を自己インストールしたりできます。 その後、ポータルで SQL Server VM を管理したり、修正プログラムの自動適用や自動バックアップなどの機能を利用したりするには、[各 SQL Server VM を SQL Server VM リソース プロバイダーに登録する](virtual-machines-windows-sql-register-with-resource-provider.md)必要があります。 


## <a name="creation"></a>作成

1. **SQL Server で Azure 仮想マシンを作成するにはどうするのですか?**

   最も簡単な方法は、SQL Server を含む仮想マシンを作成することです。 Azure にサインアップして、ポータルから SQL Server VM を作成するチュートリアルについては、[Azure portal で SQL Server 仮想マシンをプロビジョニングする方法](virtual-machines-windows-portal-sql-server-provision.md)に関するページをご覧ください。 秒単位の SQL Server ライセンスを使用する仮想マシン イメージを選択するか、または独自の SQL Server ライセンスを適用できるイメージを使用できます。 無料ライセンス版 (Developer または Express) を利用するかオンプレミスのライセンスを再利用して、SQL Server を VM に手動でインストールすることもできます。 ポータルで SQL Server VM を管理したり、修正プログラムの自動適用や自動バックアップなどの機能を利用したりするには、[各 SQL Server VM を SQL Server VM リソース プロバイダーに登録](virtual-machines-windows-sql-register-with-resource-provider.md)してください。 現在所有しているライセンスを使用する場合は、[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)が必要です。 詳細については、「[Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md)」(SQL Server Azure VM の料金ガイダンス) を参照してください。

1. **オンプレミス SQL Server データベースをクラウドに移行するにはどうすればよいですか?**

   まず SQL Server インスタンスで、Azure 仮想マシンを作成します。 次に、オンプレミス データベースをそのインスタンスに移行します。 データ移行の方法については、「[Azure VM の SQL Server への SQL Server データベースの移行](virtual-machines-windows-migrate-sql.md)」をご覧ください。

## <a name="licensing"></a>ライセンス

1. **Azure VM に SQL Server のライセンス版をインストールするにはどうすればよいですか?**

   これには 3 とおりの方法があります。 Enterprise Agreement (EA) のお客様の場合、[ライセンスがサポートされる仮想マシン イメージ](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)の 1 つをプロビジョニングできます。これは、ライセンス持ち込み (BYOL) とも呼ばれます。 [ソフトウェア アシュアランス](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)をお持ちの場合、既存の従量課金制 (PAYG) イメージで [Azure ハイブリッド特典](virtual-machines-windows-sql-ahb.md)を有効にできます。 あるいは、SQL Server インストール メディアを Windows Server VM にコピーしてから、SQL Server を VM にインストールできます。 自動バックアップや修正プログラムの自動適用などの機能を利用するには、[SQL Server VM を SQL Server VM リソース プロバイダーに登録](virtual-machines-windows-sql-register-with-resource-provider.md)してください。 

1. **スタンバイ/フェールオーバーのみ使用するために作成した Azure VM 上の SQL Server のライセンスに料金を支払う必要がありますか。**

   スタンバイ セカンダリ可用性グループまたはフェールオーバー クラスター化されたインスタンスの無料パッシブ ライセンスを得るには、[ライセンス ガイド PDF](https://download.microsoft.com/download/7/8/C/78CDF005-97C1-4129-926B-CE4A6FE92CF5/SQL_Server_2017_Licensing_guide.pdf) に概要が記載されている次の条件をすべて満たす必要があります。

   1. [ライセンス モビリティ](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2)は[ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)から与えられます。 
   1. パッシブ SQL Server インスタンスでは、クライアントに SQL Server データを提供することや、アクティブな SQL Server ワークロードを実行することがありません。 プライマリ サーバーと同期を取ること、それ以外では、パッシブ データベースをウォーム スタンバイの状態で維持することにのみ使用されます。 アクティブ SQL Server ワークロードを実行している、またはセカンダリ サーバーから追加でバックアップするなど、何らかの "作業" をしているクライアントへの報告としてデータを提供している場合、有料ライセンスの SQL Server インスタンスにする必要があります。 
   1. アクティブ SQL Server ライセンスはソフトウェア アシュアランスの対象となり、パッシブ セカンダリ SQL Server インスタンスが **1 つ**だけ許可され、その計算処理量はライセンスのあるアクティブ サーバーの計算処理量までになります。 
   1. セカンダリ SQL Server VM では、ライセンス持ち込み (BYOL) または Azure ハイブリッド特典 (AHB) [ライセンスモデル](virtual-machines-windows-sql-ahb.md)が利用されます。 

1. **従量課金制のギャラリー イメージから作成した VM を、現在所有している SQL Server ライセンスを使用するように変更できますか。**

   はい。 [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)を有効にすることによって、従量課金制 (PAYG) のギャラリー イメージをライセンス持ち込み (BYOL) に簡単に切り替えることができます。  詳細については、[SQL Server VM のライセンス モデルを変更する方法](virtual-machines-windows-sql-ahb.md)に関するページを参照してください。 現時点では、この機能は、パブリック クラウドのお客様だけが利用できます。

1. **ライセンス モデルの切り替えには、SQL Server のダウンタイムが必要ですか。**

   いいえ。 [ライセンス モデルの変更](virtual-machines-windows-sql-ahb.md)は、即座に有効になり、VM を再起動する必要はないため、SQL Server のダウンタイムは必要ありません。 ただし、SQL Server VM を SQL Server VM リソース プロバイダーに登録するには、[SQL IaaS 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)が前提条件です。SQL IaaS 拡張機能を_完全_モードでインストールすると、SQL Server サービスが再起動します。 そのため、SQL IaaS 拡張機能をインストールする必要がある場合、機能が制限される_軽量_モードでインストールするか、メンテナンス期間中であれば_完全_モードでインストールしてください。 _軽量_モードでインストールされた SQL IaaS 拡張機能はいつでも_完全_モードにアップグレードできますが、SQL Server サービスを再起動する必要があります。 

1. **Azure portal を利用し、同じ VM で複数のインスタンスを管理できますか?**

   いいえ。 ポータル管理は、SQL Server IaaS Agent 拡張機能に依存する SQL Server VM リソース プロバイダーによって提供される機能です。 そのため、拡張機能と同じ制限がリソース プロバイダーに適用されます。 ポータルでは、正しく構成されている場合に限り、既定のインスタンスまたは名前付きインスタンスを 1 つだけ管理できます。 このような制限に関する詳細は、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に関するページをご覧ください。 

1. **CSP サブスクリプションで Azure ハイブリッド特典をアクティブ化できますか。**

   はい。CSP サブスクリプションで Azure ハイブリッド特典を利用できます。 CSP のお客様は、最初に従量課金イメージをデプロイし、次にライセンス持ち込みに[ライセンス モデルを変更する](virtual-machines-windows-sql-ahb.md)必要があります。

1. **新しい SQL Server VM リソース プロバイダーに VM を登録すると、追加のコストがかかりますか?**

   いいえ。 SQL Server VM リソース プロバイダーは、Azure VM 上で SQL Server に追加の管理機能を有効にするだけなので、追加料金は発生しません。 

1. **SQL Server VM リソース プロバイダーは、すべての顧客が使用できますか?**
 
   はい。SQL Server VM がクラシック モデルではなく Resource Manager モデルを使用してパブリック クラウドにデプロイされた場合に限ります。 他のお客様はすべて、新しい SQL Server VM リソース プロバイダーに登録できます。 ただし、SQL Server VM で [Azure ハイブリッド特典 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) をアクティブ化し、独自のライセンスを使用できるのは、[ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)の特典をお持ちのお客様だけです。 

1. **VM リソースが移動または破棄されると、リソース プロバイダー (_Microsoft.SqlVirtualMachine_) リソースはどうなりますか?** 

   Microsoft.Compute/VirtualMachine リソースが破棄または移動されると、関連付けられた Microsoft.SqlVirtualMachine リソースに、操作が非同期的にレプリケートされることが通知されます。

1. **リソース プロバイダー (_Microsoft.SqlVirtualMachine_) リソースが破棄されると、VM はどうなりますか?**

    Microsoft.SqlVirtualMachine リソースが破棄されても、Microsoft.Compute/VirtualMachine リソースには影響しません。 ただし、ライセンスの変更により、元の既定のイメージ ソースに戻ります。 

1. **自己デプロイされた SQL Server VM を SQL Server VM リソース プロバイダーに登録することはできますか?**

    はい。 独自のメディアから SQL Server をデプロイし、SQL IaaS 拡張機能をインストールした場合は、SQL IaaS 拡張機能によって提供される管理の容易性の利点を得るため、リソース プロバイダーに SQL Server VM を登録できます。 ただし、自己デプロイされた SQL Server VM を従量課金制に変換することはできません。

1. **クラシック モデルを使用してデプロイされた SQL Server VM でライセンス モデルを切り替えることはできますか。**

   いいえ。 クラシック VM では、ライセンス モデルの変更はサポートされていません。 VM を Azure Resource Manager モデルに移行し、SQL Server VM リソース プロバイダーに登録できます。 VM が SQL Server VM リソース プロバイダーに登録されたら、VM でライセンス モデルを変更できます。 
   


## <a name="administration"></a>管理

1. **同じ VM に SQL Server の 2 つ目のインスタンスをインストールできますか? 既定のインスタンスのインストール済みの機能を変更することはできますか?**

   はい。 SQL Server インストール メディアは、 **C** ドライブ上のフォルダーにあります。 その場所から、 **Setup.exe** を実行して、新しい SQL Server インスタンスを追加するか、またはコンピューター上の SQL Server の他のインストールされている機能を変更します。 一部の機能 (自動バックアップ、自動修正、Azure Key Vault の統合など) は、既定のインスタンスまたは正しく構成された名前付きインスタンス (質問 3 を参照) でしか動作しないので注意が必要です。 

1. **SQL Server の既定のインスタンスをアンインストールできますか?**

   はい。ただし、考慮事項がいくつかあります。 まず、VM のライセンス モデルによっては、SQL Server 関連の請求が引き続き発生することがあります。 2 つ目に、前出の回答で述べたように、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に依存する機能があります。 IaaS 拡張機能を削除せずに既定のインスタンスをアンインストールすると、拡張機能が既定のインスタンスを探し続けるために、イベント ログ エラーが生成される可能性があります。 これらのエラーは次の 2 つのソースから発生します:**Microsoft SQL Server Credential Management**、**Microsoft SQL Server IaaS Agent**。 エラーの例を次に示します。

      SQL Server への接続を確立しているときにネットワーク関連またはインスタンス固有のエラーが発生しました。 サーバーが見つからないかアクセスできません。

   あえて既定のインスタンスをアンインストールする場合は、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)もアンインストールしてください。 

1. **IaaS 拡張機能で SQL Server の名前付きインスタンスを使用できますか?**
   
   はい。ただし、その名前付きインスタンスが SQL Server の唯一のインスタンスであることと、元の既定のインスタンスが[正しくアンインストールされている](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance)ことが条件となります。 既定のインスタンスがなく、1 つの SQL Server VM 上に複数の名前付きインスタンスがある場合は、SQL Server IaaS エージェント拡張機能はインストールに失敗します。 

1. **SQL Server VM から SQL Server を完全に削除できますか?**

   はい。ただし、[SQL Server Azure VM の価格に関するガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)のページで説明されているように、SQL Server VM に対して引き続き課金されます。 SQL Server が不要になった場合は、新しい仮想マシンをデプロイし、データとアプリケーションを新しい仮想マシンに移行してから、 SQL Server 仮想マシンを削除してください。
   
## <a name="updating-and-patching"></a>更新プログラムと修正プログラムの適用

1. **Azure VM で SQL Server の異なるバージョン/エディションに変更するにはどうすればよいですか?**

   お客様は、SQL Server の目的のバージョンまたはエディションを含むセットアップ メディアを使用して、SQL Server のバージョン/エディションを変更できます。 エディションが変更されたら、Azure portal を使用して、VM の課金を正確に反映するように VM のエディション プロパティを変更してください。 詳細については、[SQL Server VM のエディションの変更](virtual-machines-windows-sql-change-edition.md)に関するページを参照してください。 SQL Server のバージョンが異なっても、課金に違いはありません。そのため、SQL Server のバージョンが変更されても、必要な措置はありません。

1. **SQL Server のエディションやバージョンを変更するためのセットアップ メディアはどこで入手できますか?**

  [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)をお持ちのお客様は、[ボリューム ライセンス サービス センター](https://www.microsoft.com/Licensing/servicecenter/default.aspx)からインストール メディアを入手できます。 ソフトウェア アシュアランスをお持ちでないお客様は、目的のエディションを含むマーケットプレースの SQL Server VM イメージから、セットアップ メディアを使用できます。

1. **SQL Server VM に更新プログラムと Service Pack を適用するにはどうすればよいですか?**

   仮想マシンでは、更新プログラムを適用するタイミングと方法を含めて、ホスト マシンを制御できます。 オペレーティング システムの場合、Windows 更新プログラムを手動で適用するか、または[自動修正](virtual-machines-windows-sql-automated-patching.md)と呼ばれるスケジュール サービスを有効にすることができます。 自動修正では、そのカテゴリ内の SQL Server の更新を含む、重要とマークされている更新プログラムがインストールされます。 SQL Server へのその他のオプションの更新プログラムは、手動でインストールする必要があります。

1. **SQL Server 2008 / 2008 R2 インスタンスを SQL Server VM リソース プロバイダーに登録した後にアップグレードできますか?**

   はい。 セットアップ メディアを利用し、SQL Server のバージョンやエディションをアップグレードできます。その後、[SQL IaaS 拡張機能モード](virtual-machines-windows-sql-register-with-resource-provider.md#change-management-modes)を_エージェントなし_から_完全_にアップグレードできます。 アップグレードにより SQL IaaS 拡張機能のあらゆる特典を利用できるようになります。たとえば、ポータルが使いやすくなり、バックアップや修正プログラムの適用を自動化できます。 

## <a name="general"></a>全般

1. **SQL Server フェールオーバー クラスター インスタンス (FCI) は Azure VM でサポートされますか?**

   はい。 [Windows フェールオーバー クラスターを Windows Server 2016 上で作成](virtual-machines-windows-portal-sql-create-failover-cluster.md)し、クラスター ストレージとして Storage Spaces Direct (S2D) を使用できます。 または、[Azure 仮想マシンでの SQL Server の高可用性とディザスター リカバリー](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)に関する記事の説明に従って、サードパーティのクラスタリング ソリューションまたは記憶域ソリューションを使用できます。

   > [!IMPORTANT]
   > 現時点では、Azure 上の SQL Server FCI は、_完全な_ [SQL Server IaaS エージェント拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に対応していません。 FCI に参加している VM から_完全な_拡張機能をアンインストールし、代わりに_軽量_モードで拡張機能をインストールすることをお勧めします。 この拡張機能では、自動バックアップ、修正プログラムの自動適用、SQL Server のポータル機能の一部などの機能に対応しています。 このような機能は、_完全_なエージェントのアンインストール後、SQL Server VM では動作しません。

1. **SQL Server VM と SQL Database サービスの違いは何ですか?**

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
