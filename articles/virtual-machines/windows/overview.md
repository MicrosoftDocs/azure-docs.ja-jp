---
title: Windows 仮想マシンの概要 - Azure | Microsoft Docs
description: Azure における Windows 仮想マシンの作成と管理について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 24914cc3ff673d04e8c181082e13c087aeabe709
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765750"
---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Azure における Windows 仮想マシンの概要

Azure Virtual Machines (VM) は、Azure が提供する[スケーラブルなオンデマンド コンピューティング リソース](../../app-service/overview-compare.md)の 1 つです。 通常、コンピューティング環境を他の手段より細かく管理する必要がある場合に、VM を選択します。 この記事では、VM を作成する前に検討する必要のある事項、VM の作成方法、VM の管理方法に関する情報を提供します。

Azure VM は、VM を実行する物理的なハードウェアを購入して維持する手間を省き、仮想化がもたらす柔軟性を提供します。 ただし、VM のメンテナンス、つまり VM 上で動作するソフトウェアの構成、その修正プログラムの適用、インストールは必要です。

Azure の仮想マシンは、さまざまな方法で利用できます。 次に例をいくつか示します。

* **開発とテスト** – Azure VM は、アプリケーションのコーディングとテストに必要な特定の構成でコンピューターをすばやく簡単に作成する手段を提供します。
* **クラウドのアプリケーション** – アプリケーションの需要は変動する可能性があるため、Azure の VM でアプリケーションを実行することは経済的に理に適っています。 VM が必要になったら追加分の料金を支払い、不要になったらシャットダウンすることができます。
* **データセンターの拡張** – Azure 仮想ネットワーク内の仮想マシンは、組織のネットワークに簡単に接続できます。

アプリケーションで使用する VM の数は、ニーズに応じてスケールアップおよびスケールアウトできます。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM の作成前に検討する必要のある事項
Azure でアプリケーション インフラストラクチャを構築する際には、多数の[設計上の考慮事項](/azure/architecture/reference-architectures/virtual-machines-windows?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)が必ず存在します。 開始する前に、VM の次の側面を考慮することが重要です。

* アプリケーション リソースの名前
* リソースが格納される場所
* VM のサイズ
* 作成できる VM の最大数
* VM で実行されるオペレーティング システム
* 開始した後の VM の構成
* VM で必要な関連リソース

### <a name="naming"></a>名前を付ける
仮想マシンには[名前](/azure/architecture/best-practices/naming-conventions)が割り当てられ、コンピューター名がオペレーティング システムの一部として構成されます。 VM の名前は最大で 15 文字です。

Azure を使用してオペレーティング システム ディスクを作成する場合、コンピューター名と仮想マシン名は同じになります。 以前構成されたオペレーティング システムが含まれる[独自のイメージをアップロードして使用](upload-generalized-managed.md)し、それを基に仮想マシンを作成する場合は、別の名前にすることができます。 独自のイメージ ファイルをアップロードするときは、オペレーティング システムのコンピューター名と仮想マシン名を同じにすることをお勧めします。

### <a name="locations"></a>場所
Azure で作成されるすべてのリソースは、世界各地の複数の[地理的リージョン](https://azure.microsoft.com/regions/)に分散されます。 通常、このリージョンは VM の作成時には**場所**と呼ばれます。 VM の場合、この場所によって仮想ハード ディスクの格納場所を指定します。

次の表に、利用可能な場所の一覧を取得する方法の一部を示します。

| 方法 | 説明 |
| --- | --- |
| Azure ポータル |VM を作成するときに一覧から場所を選択します。 |
| Azure PowerShell |[Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) コマンドを使用します。 |
| REST API |[場所の一覧表示](https://docs.microsoft.com/rest/api/resources/subscriptions)操作を使用します。 |
| Azure CLI |[az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest) 操作を使用します。 |

### <a name="vm-size"></a>VM サイズ
使用する VM の[サイズ](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)は、実行するワークロードによって決まります。 さらに、選択したサイズによって、処理能力、メモリ、ストレージの容量などの要素が決まります。 Azure では、さまざまな種類の使用をサポートするために、さまざまなサイズを用意しています。

Azure では、VM のサイズおよびオペレーティング システムに基づいて[時間単位の料金](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)が請求されます。 時間単位を満たさない場合は、分単位でのみ請求されます。 ストレージは別料金で、別個に請求されます。

### <a name="vm-limits"></a>VM の制限
サブスクリプションにはそれぞれ既定の[クォータ制限](../../azure-subscription-service-limits.md)が設けられており、プロジェクトで多数の VM をデプロイする場合に、その点が影響する可能性があります。 現在は、リージョンあたり 20 VM の制限がサブスクリプションごとに設けられています。 制限は、[サポート チケットで引き上げを依頼する](../../azure-supportability/resource-manager-core-quotas-request.md)ことによって引き上げることができます。

### <a name="operating-system-disks-and-images"></a>オペレーティング システム ディスクおよびイメージ
仮想マシンは、[仮想ハード ディスク (VHD)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用して、オペレーティング システム (OS) およびデータを格納します。 VHD は、OS をインストールするために選択できるイメージの保存にも使用できます。 

Azure には、Windows Server オペレーティング システムのさまざまなバージョンと種類で使用できる [Marketplace イメージ](https://azure.microsoft.com/marketplace/virtual-machines/)が多数用意されています。 Marketplace イメージは、イメージの発行元、プラン、SKU、およびバージョン (通常は最新バージョンとして指定) によって識別されます。 64 ビットのオペレーティング システムのみがサポートされています。 サポートされているゲストのオペレーティング システム、ロール、機能の詳細については、「[Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)」を参照してください。

次の表に、イメージに関する情報を見つける方法をいくつか示します。

| 方法 | 説明 |
| --- | --- |
| Azure ポータル |値は、使用するイメージを選択する際に自動的に指定されます。 |
| Azure PowerShell |[Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher) -Location *location*<BR>[Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer) -Location *location* -Publisher *publisherName*<BR>[Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) -Location *location* -Publisher *publisherName* -Offer *offerName* |
| REST API |[イメージ発行元の一覧表示](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[イメージ プランの一覧表示](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[イメージ SKU の一覧表示](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[az vm image list-publishers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --location *場所*<BR>[az vm image list-offers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --location *場所* --publisher *発行元名*<BR>[az vm image list-skus](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest) --location *場所* --publisher *発行元名* --offer *プラン名*|

[独自のイメージをアップロードして使用](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)することができますが、そのとき、発行元名、プラン、SKU は使用されません。

### <a name="extensions"></a>拡張機能
VM の[拡張機能](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)は、デプロイ後の構成と自動化タスクを通じて VM に追加の機能を提供します。

拡張機能を使用して、次のような一般的なタスクを実行できます。

* **カスタム スクリプトの実行** – [カスタム スクリプト拡張機能](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を利用すれば、VM のプロビジョニングの際にスクリプトを実行して、VM でワークロードを構成できます。
* **構成のデプロイと管理** – [PowerShell Desired State Configuration (DSC) 拡張機能](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を利用すれば、VM で DSC をセットアップして構成と環境を管理できます。
* **診断データの収集** – [Azure 診断拡張機能](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を利用すれば、アプリケーションの正常性を監視するために使用できる診断データを収集するように VM を構成できます。

### <a name="related-resources"></a>関連リソース
次の表のリソースは VM によって使用されるため、VM の作成時に存在するか、作成する必要があります。

| リソース | 必須 | 説明 |
| --- | --- | --- |
| [[リソース グループ]](../../azure-resource-manager/resource-group-overview.md) |はい |VM は、リソース グループに含まれる必要があります。 |
| [ストレージ アカウント](../../storage/common/storage-create-storage-account.md) |はい |VM には、その仮想ハード ディスクを格納するストレージ アカウントが必要です。 |
| [Virtual Network](../../virtual-network/virtual-networks-overview.md) |はい |VM は、仮想ネットワークのメンバーである必要があります。 |
| [パブリック IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |いいえ  |VM には、リモートでアクセスするためのパブリック IP アドレスを割り当てることができます。 |
| [ネットワーク インターフェイス](../../virtual-network/virtual-network-network-interface.md) |はい |VM には、ネットワークで通信するためのネットワーク インターフェイスが必要です。 |
| [データ ディスク](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |いいえ  |VM には、ストレージ容量を拡張するためのデータ ディスクを含めることができます。 |

## <a name="how-do-i-create-my-first-vm"></a>最初の VM の作成方法
VM を作成する際、いくつかの選択肢があります。 どの選択肢を利用するかは、環境によって異なります。 

次の表は、VM の作成を開始するうえでの情報を提供します。

| 方法 | 記事 |
| --- | --- |
| Azure ポータル |[ポータルを使用して Windows を実行する仮想マシンを作成する](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| テンプレート |[リソース マネージャー テンプレートで Windows 仮想マシンを作成する](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[PowerShell を使用して Windows VM を作成する](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| クライアント SDK |[C# を使用した Azure リソースのデプロイ](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| REST API |[VM の作成または更新](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |
| Azure CLI |[Azure CLI を使用した VM の作成](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-cli-sample-create-vm) |

起こってほしくはないものの、問題が発生することもあります。 そのような場合は、[Azure での Windows 仮想マシンの作成に伴う Resource Manager デプロイメントの問題のトラブルシューティング](../troubleshooting/troubleshoot-deployment-new-vm-windows.md)に関するページを参照してください。

## <a name="how-do-i-manage-the-vm-that-i-created"></a>作成した VM の管理方法
VM は、ブラウザーベースのポータル、スクリプトがサポートされるコマンドライン ツール、または直接 API を使用して管理できます。 実施する一般的な管理タスクは、VM に関する情報の収集、VM へのログオン、可用性の管理、バックアップの作成などです。

### <a name="get-information-about-a-vm"></a>VM に関する情報の取得
次の表に、VM に関する情報の取得方法の一部を示します。

| 方法 | 説明 |
| --- | --- |
| Azure ポータル |ハブ メニューの **[仮想マシン]** をクリックし、一覧から VM を選択します。 その VM のブレードで、概要情報を確認したり、値の設定やメトリックの監視を実行したりできます。 |
| Azure PowerShell |PowerShell を使用して VM を管理する方法については、「[Azure PowerShell モジュールを使用して Windows VM を作成および管理する](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 |
| REST API |[VM 情報の取得](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get)操作を使用して、VM に関する情報を取得します。 |
| クライアント SDK |C# を使用して VM を管理する方法については、「[Azure Resource Manager と C# を使用した Azure 仮想マシンの管理](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 |
| Azure CLI |Azure CLI を使用して VM を管理する方法については、[Azure CLI リファレンス](https://docs.microsoft.com/cli/azure/vm)を参照してください。 |

### <a name="log-on-to-the-vm"></a>VM へのログオン
Azure Portal の [接続] ボタンを使用して、[リモート デスクトップ (RDP) セッション](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を開始します。 リモート接続の使用を試みているときに、問題が発生することがあります。 その場合は、[Windows を実行する Azure 仮想マシンへの Remote Desktop 接続のトラブルシューティング](../troubleshooting/troubleshoot-rdp-connection.md)に関するページを参照してください。

### <a name="manage-availability"></a>可用性の管理
アプリケーションの[高可用性を確保](manage-availability.md)する方法を把握しておくことが重要です。 この構成では、少なくとも 1 つの VM が実行され続けるように、複数の VM を作成します。

Microsoft が VM に設けている 99.95% というサービス レベル アグリーメントの要件をカスタム デプロイで満たすためには、複数の VM をデプロイし、[可用性セット](tutorial-availability-sets.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)内でワークロードを実行する必要があります。 この構成により、複数の障害ドメインに VM を分散すると共に、メンテナンス期間の異なるホストにデプロイすることができます。 完全な [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) では、全体としての Azure の可用性の確保について説明します。

### <a name="back-up-the-vm"></a>VM のバックアップ
[Recovery Services コンテナー](../../backup/backup-introduction-to-azure-backup.md)は、Azure Backup サービスと Azure Site Recovery サービスの両方でデータと資産を保護するために使用されます。 Recovery Services コンテナーを使用すれば、[Resource Manager でデプロイされた VM のバックアップを PowerShell を使用してデプロイおよび管理できます](../../backup/backup-azure-vms-automation.md)。 

## <a name="next-steps"></a>次の手順
* Linux VM を使用する場合は、「[Azure と Linux](../linux/overview.md)」を参照してください。
* インフラストラクチャのセットアップに関するガイドラインの詳細については、「[サンプルの Azure インフラストラクチャによるチュートリアル](infrastructure-example.md)」を参照してください。
