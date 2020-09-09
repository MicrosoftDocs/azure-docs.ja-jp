---
title: Azure での Windows 仮想マシン上の SQL Server に関する FAQ | Microsoft Docs
description: この記事では、Azure VM で SQL Server を実行することに関してよく寄せられる質問に対する回答を示します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: f382e3cf0f5d2d60c2868c6698b1ea901fbac023
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121444"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Azure VM における SQL Server についてよく寄せられる質問
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

この記事では、[Windows Azure Virtual Machines (VM) における SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) に関して特に多く寄せられる質問への回答を示します。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>イメージ

1. **どのような SQL Server 仮想マシン ギャラリーのイメージを使用できますか?** 

   Azure では、Windows と Linux の両方のすべてのエディションで、SQL Server のサポートされているすべてのメジャー リリース用の仮想マシン イメージを保持します。 詳細については、[Windows VM イメージ](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)と [Linux VM イメージ](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create) の完全な一覧を参照してください。

1. **既存の SQL Server 仮想マシン ギャラリー イメージは更新されますか?**

   仮想マシン ギャラリー内の SQL Server イメージは、2 か月ごとに最新の Windows と Linux の更新プログラムによって更新されます。 Windows イメージの場合は、重要な SQL Server のセキュリティ更新プログラムや Service Pack など、Windows Update で重要とマークされている更新プログラムが含まれます。 Linux イメージの場合は、システムの最新の更新プログラムが含まれます。 SQL Server の累積更新プログラムは、Linux と Windows では異なる方法で処理されます。 Linux では、SQL Server の累積更新プログラムも更新に含まれます。 ただし、現時点では、Windows VM は、SQL Server または Windows Server の累積更新プログラムでは更新されません。

1. **SQL Server 仮想マシン イメージはギャラリーから削除される可能性がありますか?**

   はい。 Azure では、メジャー バージョンとエディションごとに 1 つのイメージのみを保持します。 たとえば、新しい SQL Server Service Pack がリリースされると、Azure はその Service Pack 用の新しいイメージをギャラリーに追加します。 前の Service Pack 用の SQL Server イメージは、Azure ポータルからただちに削除されます。 ただし、PowerShell からプロビジョニングする場合は、次の 3 か月間は引き続き使用できます。 3 か月が経過すると、前の Service Pack イメージは使用できなくします。 この削除ポリシーは、SQL Server のバージョンがそのライフサイクルの終わりに達した時点でサポートされなくなった場合にも適用されます。


1. **Azure portal に表示されない SQL Server の古いイメージをデプロイすることはできますか?**

   はい、PowerShell を使用します。 PowerShell を使用して SQL Server VM をデプロイする方法の詳細については、「[Azure PowerShell を使用して SQL Server 仮想マシンをプロビジョニングする方法](create-sql-vm-powershell.md)」をご覧ください。
   
1. **SQL Server VM の一般化された Azure Marketplace SQL Server イメージを作成し、それを使用して VM をデプロイできますか?**

   はい。ただし、ポータルで SQL Server VM を管理したり、修正プログラムの自動適用や自動バックアップなどの機能を利用したりするには、[各 SQL Server VM を SQL Server VM リソース プロバイダーに登録する](sql-vm-resource-provider-register.md)必要があります。 リソース プロバイダーに登録するとき、各 SQL Server VM のライセンスの種類も指定する必要があります。

1. **Azure VM で SQL Server を一般化し、それを使用して新しい VM をデプロイするにはどうすればよいですか?**

   (SQL Server がインストールされていない) Windows Server VM をデプロイし、[SQL sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep?view=sql-server-ver15) プロセスを使用し、SQL Server インストール メディアを使用して Azure VM (Windows) 上の SQL Server を一般化できます。 [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3)をお持ちのお客様は、[ボリューム ライセンス サービス センター](https://www.microsoft.com/Licensing/servicecenter/default.aspx)からインストール メディアを入手できます。 ソフトウェア アシュアランスをお持ちでないお客様は、目的のエディションを含む Azure Marketplace の SQL Server VM イメージから、セットアップ メディアを使用できます。

   または、Azure Marketplace のいずれかの SQL Server イメージを使用して、Azure VM 上の SQL Server を一般化します。 独自のイメージを作成する前に、ソース イメージの次のレジストリ キーを削除する必要があることに注意してください。 そうしないと、SQL Server のセットアップ ブートストラップ フォルダーのサイズが大きくなったり、SQL IaaS 拡張機能が失敗した状態になる可能性があります。

   レジストリ キーのパス:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > カスタムの一般化されたイメージからデプロイされたものを含め、Azure VM 上の SQL Server を [SQL VM リソース プロバイダーに登録](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash)してコンプライアンス要件を満たし、自動パッチや自動バックアップなどのオプション機能を利用します。 リソースプロバイダーでは、各 SQL Server VM の[ライセンスの種類を指定する](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-ahb?tabs=azure-portal)こともできます。

1. **独自の VHD を使用して SQL Server VM をデプロイできますか?**

   はい。ただし、ポータルで SQL Server VM を管理したり、修正プログラムの自動適用や自動バックアップなどの機能を利用したりするには、[各 SQL Server VM を SQL Server VM リソース プロバイダーに登録する](sql-vm-resource-provider-register.md)必要があります。

1. **仮想マシン ギャラリーに表示されていない構成 (Windows 2008 R2 + SQL Server 2012 など) をセットアップすることはできますか?**

   いいえ。 SQL Server を含む仮想マシン ギャラリー イメージでは、Azure portal または [PowerShell](create-sql-vm-powershell.md) を使用して、提供されているイメージのいずれかを選択する必要があります。 ただし、Windows VM をデプロイしたり、それに SQL Server を自己インストールしたりできます。 その後、Azure portal で SQL Server VM を管理したり、修正プログラムの自動適用や自動バックアップなどの機能を利用したりするには、[各 SQL Server VM を SQL Server VM リソース プロバイダーに登録する](sql-vm-resource-provider-register.md)必要があります。 


## <a name="creation"></a>作成

1. **SQL Server で Azure 仮想マシンを作成するにはどうするのですか?**

   最も簡単な方法は、SQL Server を含む仮想マシンを作成することです。 Azure にサインアップして、ポータルから SQL Server VM を作成するチュートリアルについては、[Azure portal で SQL Server 仮想マシンをプロビジョニングする方法](create-sql-vm-portal.md)に関するページをご覧ください。 秒単位の SQL Server ライセンスを使用する仮想マシン イメージを選択するか、または独自の SQL Server ライセンスを適用できるイメージを使用できます。 無料ライセンス版 (Developer または Express) を利用するかオンプレミスのライセンスを再利用して、SQL Server を VM に手動でインストールすることもできます。 ポータルで SQL Server VM を管理したり、修正プログラムの自動適用や自動バックアップなどの機能を利用したりするには、[各 SQL Server VM を SQL Server VM リソース プロバイダーに登録](sql-vm-resource-provider-register.md)してください。 現在所有しているライセンスを使用する場合は、[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)が必要です。 詳細については、「[Pricing guidance for SQL Server Azure VMs](pricing-guidance.md)」(SQL Server Azure VM の料金ガイダンス) を参照してください。

1. **オンプレミス SQL Server データベースをクラウドに移行するにはどうすればよいですか?**

   まず SQL Server インスタンスで、Azure 仮想マシンを作成します。 次に、オンプレミス データベースをそのインスタンスに移行します。 データ移行の方法については、「[Azure VM の SQL Server への SQL Server データベースの移行](migrate-to-vm-from-sql-server.md)」をご覧ください。

## <a name="licensing"></a>ライセンス

1. **Azure VM に SQL Server のライセンス版をインストールするにはどうすればよいですか?**

   これには 3 とおりの方法があります。 Enterprise Agreement (EA) のお客様の場合、[ライセンスがサポートされる仮想マシン イメージ](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL)の 1 つをプロビジョニングできます。これは、ライセンス持ち込み (BYOL) とも呼ばれます。 [ソフトウェア アシュアランス](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)をお持ちの場合、既存の従量課金制 (PAYG) イメージで [Azure ハイブリッド特典](licensing-model-azure-hybrid-benefit-ahb-change.md)を有効にできます。 あるいは、SQL Server インストール メディアを Windows Server VM にコピーしてから、SQL Server を VM にインストールできます。 自動バックアップや修正プログラムの自動適用などの機能を利用するには、[SQL Server VM を SQL Server VM リソース プロバイダーに登録](sql-vm-resource-provider-register.md)してください。 

1. **従量課金制のギャラリー イメージから作成した VM を、現在所有している SQL Server ライセンスを使用するように変更できますか。**

   はい。 [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)を有効にすることによって、従量課金制 (PAYG) のギャラリー イメージをライセンス持ち込み (BYOL) に簡単に切り替えることができます。  詳細については、[SQL Server VM のライセンス モデルを変更する方法](licensing-model-azure-hybrid-benefit-ahb-change.md)に関するページを参照してください。 現在のところ、この機能はパブリック クラウドと Azure Government クラウドのお客様のみが利用できます。

1. **ライセンス モデルの切り替えには、SQL Server のダウンタイムが必要ですか。**

   いいえ。 [ライセンス モデルの変更](licensing-model-azure-hybrid-benefit-ahb-change.md)は、即座に有効になり、VM を再起動する必要はないため、SQL Server のダウンタイムは必要ありません。 ただし、SQL Server VM を SQL Server VM リソース プロバイダーに登録するには、[SQL IaaS 拡張機能](sql-server-iaas-agent-extension-automate-management.md)が前提条件です。SQL IaaS 拡張機能を_完全_モードでインストールすると、SQL Server サービスが再起動します。 そのため、SQL IaaS 拡張機能をインストールする必要がある場合、機能が制限される_軽量_モードでインストールするか、メンテナンス期間中であれば_完全_モードでインストールしてください。 _軽量_モードでインストールされた SQL IaaS 拡張機能はいつでも_完全_モードにアップグレードできますが、SQL Server サービスを再起動する必要があります。 
   
1. **クラシック モデルを使用してデプロイされた SQL Server VM でライセンス モデルを切り替えることはできますか?**

   いいえ。 クラシック VM では、ライセンス モデルの変更はサポートされていません。 VM を Azure Resource Manager モデルに移行し、SQL Server VM リソース プロバイダーに登録できます。 VM が SQL Server VM リソース プロバイダーに登録されたら、VM でライセンス モデルを変更できます。

1. **Azure portal を利用し、同じ VM で複数のインスタンスを管理できますか?**

   いいえ。 ポータル管理は、SQL Server IaaS Agent 拡張機能に依存する SQL Server VM リソース プロバイダーによって提供される機能です。 そのため、拡張機能と同じ制限がリソース プロバイダーに適用されます。 ポータルでは、正しく構成されている場合に限り、既定のインスタンスまたは名前付きインスタンスを 1 つだけ管理できます。 このような制限に関する詳細は、[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に関するページをご覧ください。 

1. **CSP サブスクリプションで Azure ハイブリッド特典をアクティブ化できますか。**

   はい。CSP サブスクリプションで Azure ハイブリッド特典を利用できます。 CSP のお客様は、最初に従量課金イメージをデプロイし、次にライセンス持ち込みに[ライセンス モデルを変更する](licensing-model-azure-hybrid-benefit-ahb-change.md)必要があります。
   
 
1. **スタンバイ/フェールオーバーのみ使用するために作成した Azure VM 上の SQL Server のライセンスに料金を支払う必要がありますか。**

   スタンバイ セカンダリ可用性グループまたはフェールオーバー クラスター化されたインスタンスの無料のパッシブ ライセンスを取得するには、[製品ライセンス条項](https://www.microsoft.com/licensing/product-licensing/products)に関するページに概要が記載されている次の条件をすべて満たす必要があります。

   1. [ライセンス モビリティ](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2)は[ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)を通して与えられます。 
   1. パッシブ SQL Server インスタンスでは、クライアントに SQL Server データを提供することや、アクティブな SQL Server ワークロードを実行することがありません。 プライマリ サーバーと同期を取ること、それ以外では、パッシブ データベースをウォーム スタンバイの状態で維持することにのみ使用されます。 アクティブな SQL Server ワークロードを実行しているか、または製品条項で指定されているもの以外の何らかの作業を行っているクライアントへのレポートなどのデータを提供している場合、これは有料のライセンス付与された SQL Server インスタンスである必要があります。 セカンダリ インスタンスでは、データベース整合性チェックまたは CheckDB、完全バックアップ、トランザクション ログ バックアップ、およびリソース使用状況データの監視のアクティビティが許可されます。 また、ディザスター リカバリー テストの短期間、プライマリおよび対応するディザスター リカバリー インスタンスを 90 日ごとに同時に実行することもできます。 
   1. アクティブ SQL Server ライセンスはソフトウェア アシュアランスの対象となり、パッシブ セカンダリ SQL Server インスタンスが **1 つ**だけ許可され、その計算処理量はライセンスのあるアクティブ サーバーの計算処理量までになります。 
   1. セカンダリ SQL Server VM は、Azure portal で[ディザスター リカバリー](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) ライセンスを利用します。
   
1. **どのようなものがパッシブ インスタンスと見なされますか?**

   パッシブ SQL Server インスタンスでは、クライアントに SQL Server データを提供することや、アクティブな SQL Server ワークロードを実行することがありません。 プライマリ サーバーと同期を取ること、それ以外では、パッシブ データベースをウォーム スタンバイの状態で維持することにのみ使用されます。 アクティブな SQL Server ワークロードを実行しているか、または製品条項で指定されているもの以外の何らかの作業を行っているクライアントへのレポートなどのデータを提供している場合、これは有料のライセンス付与された SQL Server インスタンスである必要があります。 セカンダリ インスタンスでは、データベース整合性チェックまたは CheckDB、完全バックアップ、トランザクション ログ バックアップ、およびリソース使用状況データの監視のアクティビティが許可されます。 また、ディザスター リカバリー テストの短期間、プライマリおよび対応するディザスター リカバリー インスタンスを 90 日ごとに同時に実行することもできます。
   

1. **どのようなシナリオでディザスター リカバリー (DR) 特典を利用できますか?**

   [ライセンス ガイド](https://aka.ms/sql2019licenseguide)に、ディザスター リカバリー特典を利用できるシナリオについて記載されています。 詳細については、製品の使用条件を参照し、ライセンスの担当者またはアカウント マネージャーにお問い合わせください。

1. **ディザスター リカバリー (DR) 特典はどのサブスクリプションでサポートされていますか?**

   ソフトウェア アシュアランスと同等のサブスクリプション権限を固定した特典として提供する包括的なプログラムでは、DR 特典がサポートされます。 これには、 オープン バリュー (OV)、オープン バリュー サブスクリプション (OVS)、Enterprise Agreement (EA)、エンタープライズ契約サブスクリプション (EAS)、およびサーバーおよびクラウド加入契約 (SCE) が含まれますが、これに限定されるものではありません。 詳細については、[製品の使用条件](https://www.microsoft.com/licensing/product-licensing/products)を参照し、ライセンスの担当者またはアカウント マネージャーにお問い合わせください。 

   
 ## <a name="resource-provider"></a>リソース プロバイダー

1. **新しい SQL Server VM リソース プロバイダーに VM を登録すると、追加のコストがかかりますか?**

   いいえ。 SQL Server VM リソース プロバイダーは、Azure VM 上で SQL Server に追加の管理機能を有効にするだけなので、追加料金は発生しません。 

1. **SQL Server VM リソース プロバイダーは、すべての顧客が使用できますか?**
 
   はい。SQL Server VM がクラシック モデルではなく Resource Manager モデルを使用してパブリック クラウドにデプロイされた場合に限ります。 他のお客様はすべて、新しい SQL Server VM リソース プロバイダーに登録できます。 ただし、SQL Server VM で [Azure ハイブリッド特典 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) をアクティブ化し、独自のライセンスを使用できるのは、[ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)の特典をお持ちのお客様だけです。 

1. **VM リソースが移動または破棄されると、リソース プロバイダー (_Microsoft.SqlVirtualMachine_) リソースはどうなりますか?** 

   Microsoft.Compute/VirtualMachine リソースが破棄または移動されると、関連付けられた Microsoft.SqlVirtualMachine リソースに、操作が非同期的にレプリケートされることが通知されます。

1. **リソース プロバイダー (_Microsoft.SqlVirtualMachine_) リソースが破棄されると、VM はどうなりますか?**

    Microsoft.SqlVirtualMachine リソースが破棄されても、Microsoft.Compute/VirtualMachine リソースには影響しません。 ただし、ライセンスの変更により、元の既定のイメージ ソースに戻ります。 

1. **自己デプロイされた SQL Server VM を SQL Server VM リソース プロバイダーに登録することはできますか?**

    はい。 独自のメディアから SQL Server をデプロイし、SQL IaaS 拡張機能をインストールした場合は、SQL IaaS 拡張機能によって提供される管理の容易性の利点を得るため、リソース プロバイダーに SQL Server VM を登録できます。    


## <a name="administration"></a>管理

1. **同じ VM に SQL Server の 2 つ目のインスタンスをインストールできますか? 既定のインスタンスのインストール済みの機能を変更することはできますか?**

   はい。 SQL Server インストール メディアは、 **C** ドライブ上のフォルダーにあります。 その場所から、 **Setup.exe** を実行して、新しい SQL Server インスタンスを追加するか、またはコンピューター上の SQL Server の他のインストールされている機能を変更します。 一部の機能 (自動バックアップ、自動修正、Azure Key Vault の統合など) は、既定のインスタンスまたは正しく構成された名前付きインスタンス (質問 3 を参照) でしか動作しないので注意が必要です。 [Azure ハイブリッド特典によるソフトウェア アシュアランス](licensing-model-azure-hybrid-benefit-ahb-change.md)、または**従量課金制**ライセンス モデルをご利用のお客様は、余分なライセンス コストを発生させることなく、仮想マシンに複数の SQL Server インスタンスをインストールすることができます。 追加の SQL Server インスタンスは、正しく構成されていなければ、システム リソースに負荷がかかる場合があります。 

1. **SQL Server の既定のインスタンスをアンインストールできますか?**

   はい。ただし、考慮事項がいくつかあります。 まず、VM のライセンス モデルによっては、SQL Server 関連の請求が引き続き発生することがあります。 2 つ目に、前出の回答で述べたように、[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に依存する機能があります。 IaaS 拡張機能を削除せずに既定のインスタンスをアンインストールすると、拡張機能が既定のインスタンスを探し続けるために、イベント ログ エラーが生成される可能性があります。 これらのエラーは次の 2 つのソースから発生します:**Microsoft SQL Server Credential Management**、**Microsoft SQL Server IaaS Agent**。 エラーの例を次に示します。

      SQL Server への接続を確立しているときにネットワーク関連またはインスタンス固有のエラーが発生しました。 サーバーが見つからないかアクセスできません。

   あえて既定のインスタンスをアンインストールする場合は、[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)もアンインストールしてください。 

1. **IaaS 拡張機能で SQL Server の名前付きインスタンスを使用できますか?**
   
   はい。ただし、その名前付きインスタンスが SQL Server の唯一のインスタンスであることと、元の既定のインスタンスが[正しくアンインストールされている](sql-server-iaas-agent-extension-automate-management.md#install-on-a-vm-with-a-single-named-sql-server-instance)ことが条件となります。 既定のインスタンスがなく、1 つの SQL Server VM 上に複数の名前付きインスタンスがある場合は、SQL Server IaaS エージェント拡張機能はインストールに失敗します。 

1. **SQL Server VM から SQL Server を完全に削除できますか?**

   はい。ただし、[SQL Server Azure VM の価格に関するガイダンス](pricing-guidance.md)のページで説明されているように、SQL Server VM に対して引き続き課金されます。 SQL Server が不要になった場合は、新しい仮想マシンをデプロイし、データとアプリケーションを新しい仮想マシンに移行してから、 SQL Server 仮想マシンを削除してください。
   
## <a name="updating-and-patching"></a>更新プログラムと修正プログラムの適用

1. **Azure VM で SQL Server の異なるバージョン/エディションに変更するにはどうすればよいですか?**

   お客様は、SQL Server の目的のバージョンまたはエディションを含むセットアップ メディアを使用して、SQL Server のバージョン/エディションを変更できます。 エディションが変更されたら、Azure portal を使用して、VM の課金を正確に反映するように VM のエディション プロパティを変更してください。 詳細については、[SQL Server VM のエディションの変更](change-sql-server-edition.md)に関するページを参照してください。 SQL Server のバージョンが異なっても、課金に違いはありません。そのため、SQL Server のバージョンが変更されても、必要な措置はありません。

1. **SQL Server のエディションやバージョンを変更するためのセットアップ メディアはどこで入手できますか?**

   [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)をお持ちのお客様は、[ボリューム ライセンス サービス センター](https://www.microsoft.com/Licensing/servicecenter/default.aspx)からインストール メディアを入手できます。 ソフトウェア アシュアランスをお持ちでないお客様は、目的のエディションを持つ Azure Marketplace SQL Server VM イメージからセットアップ メディアを使用できます。
   
1. **SQL Server VM に更新プログラムと Service Pack を適用するにはどうすればよいですか?**

   仮想マシンでは、更新プログラムを適用するタイミングと方法を含めて、ホスト マシンを制御できます。 オペレーティング システムの場合、Windows 更新プログラムを手動で適用するか、または[自動修正](automated-patching.md)と呼ばれるスケジュール サービスを有効にすることができます。 自動修正では、そのカテゴリ内の SQL Server の更新を含む、重要とマークされている更新プログラムがインストールされます。 SQL Server へのその他のオプションの更新プログラムは、手動でインストールする必要があります。

1. **SQL Server 2008 / 2008 R2 インスタンスを SQL Server VM リソース プロバイダーに登録した後にアップグレードできますか?**

   はい。 セットアップ メディアを利用し、SQL Server のバージョンやエディションをアップグレードできます。その後、[SQL IaaS 拡張機能モード](sql-vm-resource-provider-register.md#management-modes)を _エージェントなし_ から _完全_ にアップグレードできます。 アップグレードにより SQL IaaS 拡張機能のあらゆる特典を利用できるようになります。たとえば、ポータルが使いやすくなり、バックアップや修正プログラムの適用を自動化できます。 

1. **サポートが終了した SQL Server 2008 および SQL Server 2008 R2 インスタンスの無料の延長セキュリティ更新プログラムを取得するどうすればよいですか?**

   SQL Server をそのまま Azure 仮想マシンに移行することによって、[無料の延長セキュリティ更新プログラム](sql-server-2008-extend-end-of-support.md)を取得できます。 詳細については、[サポート終了オプション](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)に関するページを参照してください。 
  
   

## <a name="general"></a>全般

1. **SQL Server フェールオーバー クラスター インスタンス (FCI) は Azure VM でサポートされますか?**

   はい。 フェールオーバー クラスター インスタンスは、記憶域サブシステムに [Premium ファイル共有 (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) または[記憶域スペース ダイレクト (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) を使用してインストールできます。 Premium ファイル共有は、多くのワークロードのニーズを満たす IOPS とスループット容量を提供します。 IO 集中型ワークロードの場合は、マネージド Premium または Ultra ディスクに基づいて記憶域スペース ダイレクトを使用することを検討してください。 または、[Azure 仮想マシンでの SQL Server の高可用性とディザスター リカバリー](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions)に関する記事の説明に従って、サードパーティのクラスタリング ソリューションまたは記憶域ソリューションを使用できます。

   > [!IMPORTANT]
   > 現時点では、Azure 上の SQL Server FCI では _完全な_ [SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)がサポートされていません。 FCI に参加している VM から _完全な_ 拡張機能をアンインストールし、代わりに _軽量_ モードで拡張機能をインストールすることをお勧めします。 この拡張機能では、自動バックアップ、修正プログラムの自動適用、SQL Server のポータル機能の一部などの機能に対応しています。 このような機能は、_完全_ なエージェントのアンインストール後、SQL Server VM では動作しません。

1. **SQL Server VM と SQL Database サービスの違いは何ですか?**

   概念上、Azure 仮想マシンで SQL Server を実行することは、リモート データ センターで SQL Server を実行することと違いません。 これに対し、[Azure SQL Database](../../database/sql-database-paas-overview.md) ではサービスとしてのデータベースが提供されます。 SQL Database では、データベースをホストするマシンにアクセスできません。 詳細な比較については、[クラウド SQL Server オプションであるAzure SQL (PaaS) Database および Azure VM (IaaS) の SQL Server の選択](../../azure-sql-iaas-vs-paas-what-is-overview.md)に関するページを参照してください。

1. **Azure VM に SQL データ ツールをインストールするにはどうすればよいですか?**

    「[Microsoft SQL Server Data Tools - Business Intelligence for Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)」から、SQL データ ツールをダウンロードしてインストールします。

1. **SQL Server VM では MSDTC を使用した分散トランザクションはサポートされていますか?**
   
    はい。 ローカルの DTC は、SQL Server 2016 SP2 以降でサポートされています。 ただし、Always On 可用性グループを利用する場合は、フェールオーバー時に実行中のトランザクションが失敗し、再試行する必要があるため、アプリケーションをテストする必要があります。 クラスター化された DTC は、Windows Server 2019 以降で使用できます。 

## <a name="resources"></a>リソース

**Windows VM**:

* [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM での SQL Server のプロビジョニング](create-sql-vm-portal.md)
* [Azure VM の SQL Server へのデータベースの移行](migrate-to-vm-from-sql-server.md)
* [Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](performance-guidelines-best-practices.md)
* [Azure Virtual Machines における SQL Server のアプリケーション パターンと開発計画](application-patterns-development-strategies.md)

**Linux VM**:

* [Linux VM における SQL Server の概要](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Linux VM での SQL Server のプロビジョニング](../linux/sql-vm-create-portal-quickstart.md)
* [よく寄せられる質問 (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server on Linux のドキュメント](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
