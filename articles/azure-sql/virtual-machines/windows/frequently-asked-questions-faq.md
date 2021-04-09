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
ms.openlocfilehash: 91f93faded7c18a1bc24f17053231f9011080c57
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036250"
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

   はい。ただし、ポータルで SQL Server VM を管理したり、修正プログラムの自動適用や自動バックアップなどの機能を利用したりするには、[各 SQL Server VM を SQL IaaS Agent 拡張機能に登録する](sql-agent-extension-manually-register-single-vm.md)必要があります。 拡張機能に登録するとき、各 SQL Server VM のライセンスの種類も指定する必要があります。

1. **Azure VM で SQL Server を一般化し、それを使用して新しい VM をデプロイするにはどうすればよいですか?**

   (SQL Server がインストールされていない) Windows Server VM をデプロイし、[SQL sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep) プロセスを使用し、SQL Server インストール メディアを使用して Azure VM (Windows) 上の SQL Server を一般化できます。 [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3)をお持ちのお客様は、[ボリューム ライセンス サービス センター](https://www.microsoft.com/Licensing/servicecenter/default.aspx)からインストール メディアを入手できます。 ソフトウェア アシュアランスをお持ちでないお客様は、目的のエディションを含む Azure Marketplace の SQL Server VM イメージから、セットアップ メディアを使用できます。

   または、Azure Marketplace のいずれかの SQL Server イメージを使用して、Azure VM 上の SQL Server を一般化します。 独自のイメージを作成する前に、ソース イメージの次のレジストリ キーを削除する必要があることに注意してください。 そうしないと、SQL Server のセットアップ ブートストラップ フォルダーのサイズが大きくなったり、SQL IaaS 拡張機能が失敗した状態になる可能性があります。

   レジストリ キーのパス:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > カスタムの一般化されたイメージからデプロイされたものを含め、Azure VM 上の SQL Server を [SQL IaaS Agent 拡張機能に登録](./sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%252cbash)してコンプライアンス要件を満たし、自動パッチや自動バックアップなどのオプション機能を利用します。 この拡張機能を使用すると、各 SQL Server VM の[ライセンスの種類を指定する](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal)こともできます。

1. **独自の VHD を使用して SQL Server VM をデプロイできますか?**

   はい。ただし、ポータルで SQL Server VM を管理したり、修正プログラムの自動適用や自動バックアップなどの機能を利用したりするには、[各 SQL Server VM を SQL IaaS Agent 拡張機能に登録する](sql-agent-extension-manually-register-single-vm.md)必要があります。

1. **仮想マシン ギャラリーに表示されていない構成 (Windows 2008 R2 + SQL Server 2012 など) をセットアップすることはできますか?**

   いいえ。 SQL Server を含む仮想マシン ギャラリー イメージでは、Azure portal または [PowerShell](create-sql-vm-powershell.md) を使用して、提供されているイメージのいずれかを選択する必要があります。 ただし、Windows VM をデプロイしたり、それに SQL Server を自己インストールしたりできます。 その後、Azure portal で SQL Server VM を管理したり、修正プログラムの自動適用や自動バックアップなどの機能を利用したりするには、[ご利用の SQL Server VM を SQL IaaS Agent 拡張機能に登録する](sql-agent-extension-manually-register-single-vm.md)必要があります。 


## <a name="creation"></a>作成

1. **SQL Server で Azure 仮想マシンを作成するにはどうするのですか?**

   最も簡単な方法は、SQL Server を含む仮想マシンを作成することです。 Azure にサインアップして、ポータルから SQL Server VM を作成するチュートリアルについては、[Azure portal で SQL Server 仮想マシンをプロビジョニングする方法](create-sql-vm-portal.md)に関するページをご覧ください。 秒単位の SQL Server ライセンスを使用する仮想マシン イメージを選択するか、または独自の SQL Server ライセンスを適用できるイメージを使用できます。 無料ライセンス版 (Developer または Express) を利用するかオンプレミスのライセンスを再利用して、SQL Server を VM に手動でインストールすることもできます。 ポータルで SQL Server VM を管理したり、修正プログラムの自動適用や自動バックアップなどの機能を利用したりするには、必ず[ご利用の SQL Server VM を SQL IaaS Agent 拡張機能に登録](sql-agent-extension-manually-register-single-vm.md)してください。 現在所有しているライセンスを使用する場合は、[Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)が必要です。 詳細については、「[Pricing guidance for SQL Server Azure VMs](pricing-guidance.md)」(SQL Server Azure VM の料金ガイダンス) を参照してください。

1. **オンプレミス SQL Server データベースをクラウドに移行するにはどうすればよいですか?**

   まず SQL Server インスタンスで、Azure 仮想マシンを作成します。 次に、オンプレミス データベースをそのインスタンスに移行します。 データ移行の方法については、「[Azure VM の SQL Server への SQL Server データベースの移行](migrate-to-vm-from-sql-server.md)」をご覧ください。

## <a name="licensing"></a>ライセンス

1. **Azure VM に SQL Server のライセンス版をインストールするにはどうすればよいですか?**

   これには 3 とおりの方法があります。 Enterprise Agreement (EA) のお客様の場合、[ライセンスがサポートされる仮想マシン イメージ](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL)の 1 つをプロビジョニングできます。これは、ライセンス持ち込み (BYOL) とも呼ばれます。 [ソフトウェア アシュアランス](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)をお持ちの場合、既存の従量課金制 (PAYG) イメージで [Azure ハイブリッド特典](licensing-model-azure-hybrid-benefit-ahb-change.md)を有効にできます。 あるいは、SQL Server インストール メディアを Windows Server VM にコピーしてから、SQL Server を VM にインストールできます。 自動バックアップや修正プログラムの自動適用などの機能を利用するには、使用する SQL Server VM を[拡張機能](sql-agent-extension-manually-register-single-vm.md)に必ず登録してください。 


1. **お客様が Azure Virtual Machines で実行されている SQL Server 従量課金制イメージに接続するには、SQL Server のクライアント アクセス ライセンス (CAL) が必要ですか。**

   いいえ。 お客様がライセンス持ち込みを利用しており、自分の SQL Server SA サーバーまたは CAL VM を Azure VM に移動させる場合、CAL が必要です。 

1. **従量課金制のギャラリー イメージから作成した VM を、現在所有している SQL Server ライセンスを使用するように変更できますか。**

   はい。 [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)を有効にすることによって、従量課金制 (PAYG) のギャラリー イメージをライセンス持ち込み (BYOL) に簡単に切り替えることができます。  詳細については、[SQL Server VM のライセンス モデルを変更する方法](licensing-model-azure-hybrid-benefit-ahb-change.md)に関するページを参照してください。 現在のところ、この機能はパブリック クラウドと Azure Government クラウドのお客様のみが利用できます。


1. **ライセンス モデルの切り替えには、SQL Server のダウンタイムが必要ですか。**

   いいえ。 [ライセンス モデルの変更](licensing-model-azure-hybrid-benefit-ahb-change.md)は、即座に有効になり、VM を再起動する必要はないため、SQL Server のダウンタイムは必要ありません。 ただし、ご利用の SQL Server VM を SQL IaaS Agent 拡張機能に登録するには、[SQL IaaS 拡張機能](sql-server-iaas-agent-extension-automate-management.md)が前提条件です。SQL IaaS 拡張機能を "_完全_" モードでインストールすると、SQL Server サービスが再起動します。 そのため、SQL IaaS 拡張機能をインストールする必要がある場合、機能が制限される _軽量_ モードでインストールするか、メンテナンス期間中であれば _完全_ モードでインストールしてください。 _軽量_ モードでインストールされた SQL IaaS 拡張機能はいつでも _完全_ モードにアップグレードできますが、SQL Server サービスを再起動する必要があります。 
   
1. **クラシック モデルを使用してデプロイされた SQL Server VM でライセンス モデルを切り替えることはできますか?**

   いいえ。 クラシック VM では、ライセンス モデルの変更はサポートされていません。 VM を Azure Resource Manager モデルに移行し、SQL IaaS Agent 拡張機能に登録できます。 VM を SQL IaaS Agent 拡張機能に登録すると、VM 上でライセンス モデルを変更できるようになります。

1. **Azure portal を利用し、同じ VM で複数のインスタンスを管理できますか?**

   いいえ。 ポータル管理は、SQL Server IaaS Agent 拡張機能に依存する SQL IaaS Agent 拡張機能によって提供される機能です。 そのため、その拡張機能と同じ制限がこの拡張機能に適用されます。 ポータルでは、正しく構成されている場合に限り、既定のインスタンスまたは名前付きインスタンスを 1 つだけ管理できます。 このような制限に関する詳細は、[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に関するページをご覧ください。 

1. **CSP サブスクリプションで Azure ハイブリッド特典をアクティブ化できますか。**

   はい。CSP サブスクリプションで Azure ハイブリッド特典を利用できます。 CSP のお客様は、最初に従量課金イメージをデプロイし、次にライセンス持ち込みに[ライセンス モデルを変更する](licensing-model-azure-hybrid-benefit-ahb-change.md)必要があります。
   
 
1. **スタンバイ/フェールオーバーのみ使用するために作成した Azure VM 上の SQL Server のライセンスに料金を支払う必要がありますか。**

   スタンバイ セカンダリ可用性グループまたはフェールオーバー クラスター化されたインスタンスの無料のパッシブ ライセンスを取得するには、[製品ライセンス条項](https://www.microsoft.com/licensing/product-licensing/products)に関するページに概要が記載されている次の条件をすべて満たす必要があります。

   1. [ライセンス モビリティ](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2)は[ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)を通して与えられます。 
   1. パッシブ SQL Server インスタンスでは、クライアントに SQL Server データを提供することや、アクティブな SQL Server ワークロードを実行することがありません。 プライマリ サーバーと同期を取ること、それ以外では、パッシブ データベースをウォーム スタンバイの状態で維持することにのみ使用されます。 アクティブな SQL Server ワークロードを実行しているか、または製品条項で指定されているもの以外の何らかの作業を行っているクライアントへのレポートなどのデータを提供している場合、これは有料のライセンス付与された SQL Server インスタンスである必要があります。 セカンダリ インスタンスでは、データベース整合性チェックまたは CheckDB、完全バックアップ、トランザクション ログ バックアップ、およびリソース使用状況データの監視のアクティビティが許可されます。 また、ディザスター リカバリー テストの短期間、プライマリおよび対応するディザスター リカバリー インスタンスを 90 日ごとに同時に実行することもできます。 
   1. アクティブ SQL Server ライセンスはソフトウェア アシュアランスの対象となり、パッシブ セカンダリ SQL Server インスタンスが **1 つ** だけ許可され、その計算処理量はライセンスのあるアクティブ サーバーの計算処理量までになります。 
   1. セカンダリ SQL Server VM は、Azure portal で[ディザスター リカバリー](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) ライセンスを利用します。
   
1. **どのようなものがパッシブ インスタンスと見なされますか?**

   パッシブ SQL Server インスタンスでは、クライアントに SQL Server データを提供することや、アクティブな SQL Server ワークロードを実行することがありません。 プライマリ サーバーと同期を取ること、それ以外では、パッシブ データベースをウォーム スタンバイの状態で維持することにのみ使用されます。 アクティブな SQL Server ワークロードを実行しているか、または製品条項で指定されているもの以外の何らかの作業を行っているクライアントへのレポートなどのデータを提供している場合、これは有料のライセンス付与された SQL Server インスタンスである必要があります。 セカンダリ インスタンスでは、データベース整合性チェックまたは CheckDB、完全バックアップ、トランザクション ログ バックアップ、およびリソース使用状況データの監視のアクティビティが許可されます。 また、ディザスター リカバリー テストの短期間、プライマリおよび対応するディザスター リカバリー インスタンスを 90 日ごとに同時に実行することもできます。
   

1. **どのようなシナリオでディザスター リカバリー (DR) 特典を利用できますか?**

   [ライセンス ガイド](https://aka.ms/sql2019licenseguide)に、ディザスター リカバリー特典を利用できるシナリオについて記載されています。 詳細については、製品の使用条件を参照し、ライセンスの担当者またはアカウント マネージャーにお問い合わせください。

1. **ディザスター リカバリー (DR) 特典はどのサブスクリプションでサポートされていますか?**

   ソフトウェア アシュアランスと同等のサブスクリプション権限を固定した特典として提供する包括的なプログラムでは、DR 特典がサポートされます。 これには、 オープン バリュー (OV)、オープン バリュー サブスクリプション (OVS)、Enterprise Agreement (EA)、エンタープライズ契約サブスクリプション (EAS)、およびサーバーおよびクラウド加入契約 (SCE) が含まれますが、これに限定されるものではありません。 詳細については、[製品の使用条件](https://www.microsoft.com/licensing/product-licensing/products)を参照し、ライセンスの担当者またはアカウント マネージャーにお問い合わせください。 

   
 ## <a name="extension"></a>拡張機能

1. **新しい SQL IaaS Agent 拡張機能への VM の登録には、追加のコストがかかりますか。**

   いいえ。 SQL IaaS Agent 拡張機能は、Azure VM 上で SQL Server 用の追加の管理機能を有効にするだけのものなので、追加料金は発生しません。 

1. **SQL IaaS Agent 拡張機能はすべての顧客が利用できますか。**
 
   はい。SQL Server VM がクラシック モデルではなく Resource Manager モデルを使用してパブリック クラウドにデプロイされた場合に限ります。 他の顧客はすべて、新しい SQL IaaS Agent 拡張機能に登録できます。 ただし、SQL Server VM で [Azure ハイブリッド特典 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) をアクティブ化し、独自のライセンスを使用できるのは、[ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)の特典をお持ちのお客様だけです。 

1. **VM リソースが移動または破棄されると、拡張機能 (_Microsoft.SqlVirtualMachine_) はどうなりますか?** 

   Microsoft.Compute/VirtualMachine リソースが破棄または移動されると、関連付けられた Microsoft.SqlVirtualMachine リソースに、操作が非同期的にレプリケートされることが通知されます。

1. **拡張機能 (_Microsoft.SqlVirtualMachine_) が破棄されると、VM はどうなりますか。**

    Microsoft.SqlVirtualMachine リソースが破棄されても、Microsoft.Compute/VirtualMachine リソースには影響しません。 ただし、ライセンスの変更により、元の既定のイメージ ソースに戻ります。 

1. **SQL IaaS Agent 拡張機能に自己デプロイ済みの SQL Server VM を登録することはできますか。**

    はい。 独自のメディアから SQL Server をデプロイし、SQL IaaS 拡張機能をインストールした場合は、SQL IaaS 拡張機能によって提供される管理の容易性の利点を得るため、拡張機能に SQL Server VM を登録できます。    


## <a name="administration"></a>管理

1. **同じ VM に SQL Server の 2 つ目のインスタンスをインストールできますか? 既定のインスタンスのインストール済みの機能を変更することはできますか?**

   はい。 SQL Server インストール メディアは、 **C** ドライブ上のフォルダーにあります。 その場所から、 **Setup.exe** を実行して、新しい SQL Server インスタンスを追加するか、またはコンピューター上の SQL Server の他のインストールされている機能を変更します。 一部の機能 (自動バックアップ、自動修正、Azure Key Vault の統合など) は、既定のインスタンスまたは正しく構成された名前付きインスタンス (質問 3 を参照) でしか動作しないので注意が必要です。 [Azure ハイブリッド特典によるソフトウェア アシュアランス](licensing-model-azure-hybrid-benefit-ahb-change.md)、または **従量課金制** ライセンス モデルをご利用のお客様は、余分なライセンス コストを発生させることなく、仮想マシンに複数の SQL Server インスタンスをインストールすることができます。 追加の SQL Server インスタンスは、正しく構成されていなければ、システム リソースに負荷がかかる場合があります。 

1. **1 つの VM 上のインスタンスの最大数とは**
   SQL Server 2012 から SQL Server 2019 では、スタンドアロン サーバー上で [50 のインスタンス](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP)をサポートできます。 これは、Azure でもオンプレミスでも同じ制限です。 環境をより適切に準備する方法については、[ベスト プラクティス](performance-guidelines-best-practices.md#multiple-instances)を参照してください。 

1. **SQL Server の既定のインスタンスをアンインストールできますか?**

   はい。ただし、考慮事項がいくつかあります。 まず、VM のライセンス モデルによっては、SQL Server 関連の請求が引き続き発生することがあります。 2 つ目に、前出の回答で述べたように、[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に依存する機能があります。 IaaS 拡張機能を削除せずに既定のインスタンスをアンインストールすると、拡張機能が既定のインスタンスを探し続けるために、イベント ログ エラーが生成される可能性があります。 これらのエラーは次の 2 つのソースから発生します:**Microsoft SQL Server Credential Management**、**Microsoft SQL Server IaaS Agent**。 エラーの例を次に示します。

      SQL Server への接続を確立しているときにネットワーク関連またはインスタンス固有のエラーが発生しました。 サーバーが見つからないかアクセスできません。

   あえて既定のインスタンスをアンインストールする場合は、[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)もアンインストールしてください。 

1. **IaaS 拡張機能で SQL Server の名前付きインスタンスを使用できますか?**
   
   はい。ただし、その名前付きインスタンスが SQL Server の唯一のインスタンスであることと、元の既定のインスタンスが[正しくアンインストールされている](sql-server-iaas-agent-extension-automate-management.md#named-instance-support)ことが条件となります。 既定のインスタンスがなく、1 つの SQL Server VM 上に複数の名前付きインスタンスがある場合は、SQL Server IaaS エージェント拡張機能はインストールに失敗します。  

1. **SQL Server VM から SQL Server と関連するライセンス課金を削除できますか?**

   はい。ただし、[価格ガイダンス](pricing-guidance.md)に記載されているように、お使いの SQL Server インスタンスに課金されないようにするために追加の手順を実行する必要があります。 SQL Server インスタンスを完全に削除する場合は、SQL Server がプレインストールされていない別の Azure VM に移行し、現在の SQL Server VM を削除できます。 その VM を残したまま SQL Server の課金を停止する場合は、次の手順に従ってください。 

   1. 必要に応じて、システム データベースを含むすべてのデータをバックアップします。 
   1. SQL IaaS 拡張機能 (存在する場合) を含めて、SQL Server を完全にアンインストールします。
   1. 無料の [SQL Express エディション](https://www.microsoft.com/sql-server/sql-server-downloads)をインストールします。
   1. [軽量モード](sql-agent-extension-manually-register-single-vm.md)で SQL IaaS Agent 拡張機能に登録します。
   1. (省略可能) サービスのスタートアップを無効にすることで、Express SQL Server サービスを無効にします。 

1. **Azure portal を利用し、同じ VM で複数のインスタンスを管理できますか?**

   いいえ。 ポータル管理は、SQL Server IaaS Agent 拡張機能に依存する SQL IaaS Agent 拡張機能によって提供されます。 そのため、その拡張機能と同じ制限がこの拡張機能に適用されます。 ポータルでは、正しく構成されている場合に限り、既定のインスタンスまたは名前付きインスタンスを 1 つだけ管理できます。 詳細については、[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に関するページをご覧ください。 


## <a name="updating-and-patching"></a>更新プログラムと修正プログラムの適用

1. **Azure VM で SQL Server の異なるバージョン/エディションに変更するにはどうすればよいですか?**

   お客様は、SQL Server の目的のバージョンまたはエディションを含むセットアップ メディアを使用して、SQL Server のバージョン/エディションを変更できます。 エディションが変更されたら、Azure portal を使用して、VM の課金を正確に反映するように VM のエディション プロパティを変更してください。 詳細については、[SQL Server VM のエディションの変更](change-sql-server-edition.md)に関するページを参照してください。 SQL Server のバージョンが異なっても、課金に違いはありません。そのため、SQL Server のバージョンが変更されても、必要な措置はありません。

1. **SQL Server のエディションやバージョンを変更するためのセットアップ メディアはどこで入手できますか?**

   [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)をお持ちのお客様は、[ボリューム ライセンス サービス センター](https://www.microsoft.com/Licensing/servicecenter/default.aspx)からインストール メディアを入手できます。 ソフトウェア アシュアランスをお持ちでないお客様は、目的のエディションを持つ Azure Marketplace SQL Server VM イメージからセットアップ メディアを使用できます。
   
1. **SQL Server VM に更新プログラムと Service Pack を適用するにはどうすればよいですか?**

   仮想マシンでは、更新プログラムを適用するタイミングと方法を含めて、ホスト マシンを制御できます。 オペレーティング システムの場合、Windows 更新プログラムを手動で適用するか、または[自動修正](automated-patching.md)と呼ばれるスケジュール サービスを有効にすることができます。 自動修正では、そのカテゴリ内の SQL Server の更新を含む、重要とマークされている更新プログラムがインストールされます。 SQL Server へのその他のオプションの更新プログラムは、手動でインストールする必要があります。

1. **SQL Server 2008/2008 R2 インスタンスは、SQL IaaS Agent 拡張機能に登録した後で、アップグレードできますか。**

   OS が Windows Server 2008 R2 以降の場合は、可能です。 セットアップ メディアを利用し、SQL Server のバージョンやエディションをアップグレードできます。その後、[SQL IaaS 拡張機能モード](sql-server-iaas-agent-extension-automate-management.md#management-modes)を _エージェントなし_ から _完全_ にアップグレードできます。 アップグレードにより SQL IaaS 拡張機能のあらゆる特典を利用できるようになります。たとえば、ポータルが使いやすくなり、バックアップや修正プログラムの適用を自動化できます。 OS のバージョンが Windows Server 2008 の場合は、NoAgent モードのみがサポートされます。 

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

## <a name="sql-server-iaas-agent-extension"></a>SQL Server IaaS Agent 拡張機能

1. **Azure Marketplace の SQL Server イメージからプロビジョニングされた SQL Server VM を登録する必要はありますか?**

   いいえ。 Azure Marketplace の SQL Server イメージからプロビジョニングされた VM は自動的に登録されます。 拡張機能への登録が必要になるのは、VM が Azure Marketplace の SQL Server イメージからプロビジョニングされて "*おらず*"、SQL Server を自分でインストールした場合のみです。

1. **SQL IaaS Agent 拡張機能はすべての顧客が利用できますか?** 

   はい。 お客様がご自身の SQL Server VM を拡張機能に登録する必要があるのは、Azure Marketplace の SQL Server イメージを使用せずに自分で SQL Server をインストールした場合やカスタム VHD を購入した場合です。 すべての種類のサブスクリプション (Direct、Enterprise Agreement、Cloud Solution Provider) で所有されているVM は、SQL IaaS Agent 拡張機能に登録できます。

1. **SQL IaaS Agent 拡張機能への登録時の既定の管理モードは何ですか?**

   SQL IaaS Agent 拡張機能に登録する際の既定の管理モードは、"*軽量*" です。 拡張機能への登録時に SQL Server 管理プロパティが設定されていない場合、モードは軽量として設定され、SQL Server サービスは再起動されません。 最初に軽量モードで SQL IaaS Agent 拡張機能に登録してから、予定メンテナンス期間中にフルにアップグレードすることをお勧めします。 同様に、[自動登録機能](sql-agent-extension-automatic-registration-all-vms.md)を使用する場合は、既定の管理も軽量になります。

1. **SQL IaaS Agent 拡張機能に登録するための前提条件は何ですか?**

   VM に SQL Server がインストールされている必要があるという点を除き、SQL IaaS Agent 拡張機能に登録する際の前提条件はありません。 SQL IaaS Agent 拡張機能が完全モードでインストールされている場合は、SQL Server サービスが再起動されるため、メンテナンス期間中に実行することをお勧めします。

1. **SQL IaaS Agent 拡張機能に登録すると、VM にエージェントがインストールされますか?**

   はい。完全管理モードで SQL IaaS Agent 拡張機能に登録すると、VM にエージェントが インストールされます。 軽量モードまたは NoAgent モードで登録する場合は、そうではありません。 

   軽量モードで SQL IaaS Agent 拡張機能に登録すると、SQL IaaS Agent 拡張機能の "*バイナリ*" のみが VM にコピーされます。この場合、エージェントはインストールされません。 これらのバイナリは、管理モードが完全にアップグレードされた際、エージェントのインストールに使用されます。


1. **SQL IaaS Agent 拡張機能に登録すると、自分の VM 上の SQL Server が再起動されますか?**

   これは、登録時に指定されたモードによって異なります。 軽量または NoAgent モードが指定されている場合、SQL Server サービスは再起動されません。 ただし、管理モードを完全に指定すると、SQL Server サービスが再起動されます。 自動登録機能では、SQL Server VM が軽量モードで登録されます。ただし、Windows Server のバージョンが 2008 の場合、SQL Server VM は NoAgent モードで登録されます。 

1. **SQL IaaS Agent 拡張機能に登録する場合、軽量と NoAgent の各管理モードではどのような違いがありますか?** 

   NoAgent 管理モードは、Windows Server 2008 上の SQL Server 2008 と SQL Server 2008 R2 で唯一使用可能な管理モードです。 以後のすべてのバージョンの Windows Server で使用可能な 2 つの管理モードとして軽量モードと完全モードがあります。 

   NoAgent モードでは、SQL Server のバージョンとエディションのプロパティをお客様が設定する必要があります。 軽量モードでは、VM が照会され、SQL Server インスタンスのバージョンとエディションが確認されます。

1. **SQL Server のライセンスの種類を指定せずに SQL IaaS Agent 拡張機能に登録できますか?**

   いいえ。 SQL IaaS Agent 拡張機能に登録している場合、SQL Server のライセンスの種類は省略可能なプロパティではありません。 すべての管理モード (NoAgent、軽量、フル) で SQL IaaS Agent 拡張機能に登録する際、SQL Server のライセンスの種類を従量課金制または Azure ハイブリッド特典として設定する必要があります。 Developer や Evaluation Edition など、無料版の SQL Server がインストールされている場合は、従量課金制ライセンスで登録する必要があります。 Azure ハイブリッド特典は、Enterprise Edition や Standard Edition などの有料版 SQL Server でのみ使用できます。

1. **SQL Server IaaS 拡張機能を NoAgent モードから完全モードにアップグレードできますか?**

   いいえ。 NoAgent モードでは、管理モードを完全または軽量にアップグレードすることはできません。 これは Windows Server 2008 の技術的な制限です。 最初に OS を Windows Server 2008 R2 以上にアップグレードする必要があります。その後、フル管理モードにアップグレードできます。 

1. **SQL Server IaaS 拡張機能を軽量モードから完全モードにアップグレードできますか?**

   はい。 管理モードを軽量から完全にアップグレードするには、Azure PowerShell または Azure portal を使用します。 実行すると、SQL Server サービスの再起動がトリガーされます。

1. **SQL Server IaaS 拡張機能を完全モードから NoAgent または軽量管理モードにダウングレードすることはできますか?**

   いいえ。 SQL Server IaaS 拡張機能の管理モードのダウングレードはサポートされていません。 管理モードを完全モードから軽量モードまたは NoAgent モードにダウングレードすることはできません。また、軽量モードから NoAgent モードにダウングレードすることもできません。 

   管理モードを完全管理から変更するには、SQL 仮想マシン "_リソース_" を削除して SQL IaaS Agent 拡張機能から SQL Server VM の [登録を解除](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)し、別の管理モードでその SQL Server VM を SQL IaaS Agent 拡張機能に再登録します。

1. **Azure portal から SQL IaaS Agent 拡張機能に登録できますか?**

   いいえ。 Azure portal では SQL IaaS Agent 拡張機能への登録を利用できません。 SQL IaaS Agent 拡張機能への登録は、Azure CLI または Azure PowerShell でのみサポートされます。 

1. **SQL Server をインストールする前に、VM を SQL IaaS Agent 拡張機能に登録できますか?**

   いいえ。 SQL IaaS Agent 拡張機能に正常に登録するには、VM に少なくとも 1 つの SQL Server (データベース エンジン) インスタンスが必要です。 VM 上に SQL Server インスタンスが存在しない場合、新しい Microsoft.SqlVirtualMachine リソースはエラー状態になります。

1. **複数の SQL Server インスタンスがある場合、VM を SQL IaaS Agent 拡張機能に登録できますか?**

   はい。VM 上に既定のインスタンスがあることが前提です。 SQL IaaS Agent 拡張機能では、1 つの SQL Server (データベース エンジン) インスタンスのみ登録されます。 複数のインスタンスがある場合、SQL IaaS Agent 拡張機能では、既定の SQL Server インスタンスが登録されます。

1. **SQL Server フェールオーバー クラスター インスタンスを SQL IaaS Agent 拡張機能に登録できますか?**

   はい。 Azure VM 上の SQL Server フェールオーバー クラスター インスタンスは SQL IaaS Agent 拡張機能に軽量モードで登録できます。 ただし、SQL Server フェールオーバー クラスター インスタンスを完全管理モードにアップグレードすることはできません。

1. **Always On 可用性グループが構成されている場合、自分の VM を SQL IaaS Agent 拡張機能に登録できますか?**

   はい。 Always On 可用性グループ構成に参加している場合、Azure VM 上の SQL Server インスタンスを SQL IaaS Agent 拡張機能に登録することに制限はありません。

1. **SQL IaaS Agent 拡張機能に登録する場合、または完全管理モードにアップグレードする場合、どのようなコストがかかりますか?**

   [なし] : SQL IaaS Agent 拡張機能への登録に関連する料金も、3 種類のいずれの管理モードの使用に関連する料金もかかりません。 ご利用の SQL Server VM を拡張機能を使用して管理する場合は、完全に無料です。 

1. **さまざまな管理モードを使用すると、パフォーマンスにどのような影響がありますか?**

   *NoAgent* および *軽量* 管理性モードを使用する場合、影響はありません。 OS にインストールされている 2 つのサービスから、*完全* 管理モードを使用する場合の影響は最小限に抑えられます。 これらは、タスク マネージャーを使用して監視し、組み込みの Windows サービス コンソールに表示することができます。 

   これらの 2 つのサービス名は次のとおりです。
   - `SqlIaaSExtensionQuery` (表示名 - `Microsoft SQL Server IaaS Query Service`)
   - `SQLIaaSExtension` (表示名 - `Microsoft SQL Server IaaS Agent`)

1. **拡張機能を削除する方法**

   拡張機能を削除するには、SQL IaaS Agent 拡張機能から SQL Server VM の[登録を解除](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)します。 

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
* [SQL Server on Linux のドキュメント](/sql/linux/sql-server-linux-overview)
