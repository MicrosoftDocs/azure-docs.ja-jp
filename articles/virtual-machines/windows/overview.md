---
title: Azure での Windows VM の概要
description: Azure における Windows 仮想マシンの概要です。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4e955a51906f160264fc4e81f263fe7677be91de
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589624"
---
# <a name="windows-virtual-machines-in-azure"></a>Azure の Windows 仮想マシン

Azure Virtual Machines (VM) は、Azure が提供する[スケーラブルなオンデマンド コンピューティング リソース](/azure/architecture/guide/technology-choices/compute-decision-tree)の 1 つです。 通常、コンピューティング環境を他の手段より細かく管理する必要がある場合に、VM を選択します。 この記事では、VM を作成する前に検討する必要のある事項、VM の作成方法、VM の管理方法に関する情報を提供します。

Azure VM は、VM を実行する物理的なハードウェアを購入して維持する手間を省き、仮想化がもたらす柔軟性を提供します。 ただし、VM のメンテナンス、つまり VM 上で動作するソフトウェアの構成、その修正プログラムの適用、インストールは必要です。

Azure の仮想マシンは、さまざまな方法で利用できます。 いくつかの例を次に示します。

* **開発とテスト** – Azure VM は、アプリケーションのコーディングとテストに必要な特定の構成でコンピューターをすばやく簡単に作成する手段を提供します。
* **クラウドのアプリケーション** – アプリケーションの需要は変動する可能性があるため、Azure の VM でアプリケーションを実行することは経済的に理に適っています。 VM が必要になったら追加分の料金を支払い、不要になったらシャットダウンすることができます。
* **データセンターの拡張** – Azure 仮想ネットワーク内の仮想マシンは、組織のネットワークに簡単に接続できます。

アプリケーションで使用する VM の数は、ニーズに応じてスケールアップおよびスケールアウトできます。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM の作成前に検討する必要のある事項
Azure でアプリケーション インフラストラクチャを構築する際には、多数の[設計上の考慮事項](/azure/architecture/reference-architectures/n-tier/windows-vm)が必ず存在します。 開始する前に、VM の次の側面を考慮することが重要です。

* アプリケーション リソースの名前
* リソースが格納される場所
* VM のサイズ
* 作成できる VM の最大数
* VM で実行されるオペレーティング システム
* 開始した後の VM の構成
* VM で必要な関連リソース

### <a name="locations"></a>場所
Azure で作成されるすべてのリソースは、世界各地の複数の[地理的リージョン](https://azure.microsoft.com/regions/)に分散されます。 通常、このリージョンは VM の作成時には**場所**と呼ばれます。 VM の場合、この場所によって仮想ハード ディスクの格納場所を指定します。

次の表に、利用可能な場所の一覧を取得する方法の一部を示します。

| Method | 説明 |
| --- | --- |
| Azure portal |VM を作成するときに一覧から場所を選択します。 |
| Azure PowerShell |[Get-AzLocation](/powershell/module/az.resources/get-azlocation) コマンドを使用します。 |
| REST API |[場所の一覧表示](/rest/api/resources/subscriptions)操作を使用します。 |
| Azure CLI |[az account list-locations](/cli/azure/account?view=azure-cli-latest) 操作を使用します。 |

### <a name="singapore-data-residency"></a>シンガポールのデータ所在地

Azure では、顧客データを 1 つのリージョンに格納できるようにする機能は現在、アジア太平洋地域の東南アジア リージョン (シンガポール) でのみ使用できます。 その他のすべてのリージョンでは、顧客データは geo 内に格納されます。 詳細については、[セキュリティ センター](https://azuredatacentermap.azurewebsites.net/)に関するページを参照してください。

## <a name="availability"></a>可用性
Azure は、単一インスタンス仮想マシン向けに、業界をリードする 99.9% というサービス レベル アグリーメントを発表しました。ただし、すべてのディスクに Premium Storage を使用した VM をデプロイすることが条件となります。  デプロイが、VM に適用される 99.95% という標準のサービス レベル アグリーメントの要件を満たすためには、可用性セット内でワークロードを実行する複数の VM をデプロイする必要があります。 可用性セットにより、Azure データ センターにある複数の障害ドメインに VM を分散すると共に、メンテナンス期間の異なるホストにデプロイすることができます。 完全な [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) では、全体としての Azure の可用性の確保について説明します。


## <a name="vm-size"></a>VM サイズ
使用する VM の[サイズ](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)は、実行するワークロードによって決まります。 さらに、選択したサイズによって、処理能力、メモリ、ストレージの容量などの要素が決まります。 Azure では、さまざまな種類の使用をサポートするために、さまざまなサイズを用意しています。

Azure では、VM のサイズおよびオペレーティング システムに基づいて[時間単位の料金](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)が請求されます。 時間単位を満たさない場合は、分単位でのみ請求されます。 ストレージは別料金で、別個に請求されます。

## <a name="vm-limits"></a>VM の制限
サブスクリプションにはそれぞれ既定の[クォータ制限](../../azure-resource-manager/management/azure-subscription-service-limits.md)が設けられており、プロジェクトで多数の VM をデプロイする場合に、その点が影響する可能性があります。 現在は、リージョンあたり 20 VM の制限がサブスクリプションごとに設けられています。 制限は、[サポート チケットで引き上げを依頼する](../../azure-portal/supportability/resource-manager-core-quotas-request.md)ことによって引き上げることができます。

### <a name="operating-system-disks-and-images"></a>オペレーティング システム ディスクおよびイメージ
仮想マシンは、[仮想ハード ディスク (VHD)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用して、オペレーティング システム (OS) およびデータを格納します。 VHD は、OS をインストールするために選択できるイメージの保存にも使用できます。 

Azure には、Windows Server オペレーティング システムのさまざまなバージョンと種類で使用できる [Marketplace イメージ](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images%3Bwindows&page=1)が多数用意されています。 Marketplace イメージは、イメージの発行元、プラン、SKU、およびバージョン (通常は最新バージョンとして指定) によって識別されます。 64 ビットのオペレーティング システムのみがサポートされています。 サポートされているゲストのオペレーティング システム、ロール、機能の詳細については、「[Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)」を参照してください。

次の表に、イメージに関する情報を見つける方法をいくつか示します。

| Method | 説明 |
| --- | --- |
| Azure portal |値は、使用するイメージを選択する際に自動的に指定されます。 |
| Azure PowerShell |[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) -Location *location*<BR>[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) -Location *location* -Publisher *publisherName*<BR>[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) -Location *location* -Publisher *publisherName* -Offer *offerName* |
| REST API |[イメージ発行元の一覧表示](/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[イメージ プランの一覧表示](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[イメージ SKU の一覧表示](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[az vm image list-publishers](/cli/azure/vm/image?view=azure-cli-latest) --location *場所*<BR>[az vm image list-offers](/cli/azure/vm/image?view=azure-cli-latest) --location *場所* --publisher *発行元名*<BR>[az vm image list-skus](/cli/azure/vm?view=azure-cli-latest) --location *場所* --publisher *発行元名* --offer *プラン名*|

[独自のイメージをアップロードして使用](upload-generalized-managed.md)することができますが、そのとき、発行元名、プラン、SKU は使用されません。

### <a name="extensions"></a>拡張機能
VM の[拡張機能](../extensions/features-windows.md?toc=/azure/virtual-machines/windows/toc.json)は、デプロイ後の構成と自動化タスクを通じて VM に追加の機能を提供します。

拡張機能を使用して、次のような一般的なタスクを実行できます。

* **カスタム スクリプトの実行** – [カスタム スクリプト拡張機能](../extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json)を利用すれば、VM のプロビジョニングの際にスクリプトを実行して、VM でワークロードを構成できます。
* **構成のデプロイと管理** – [PowerShell Desired State Configuration (DSC) 拡張機能](../extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) を利用すれば、VM で DSC をセットアップして構成と環境を管理できます。
* **診断データの収集** – [Azure Diagnostics 拡張機能](../extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)を利用すれば、アプリケーションの正常性を監視するために使用できる診断データを収集するように VM を構成できます。

### <a name="related-resources"></a>関連リソース
次の表のリソースは VM によって使用されるため、VM の作成時に存在するか、作成する必要があります。

| リソース | 必須 | 説明 |
| --- | --- | --- |
| [リソース グループ](../../azure-resource-manager/management/overview.md) |はい |VM は、リソース グループに含まれる必要があります。 |
| [ストレージ アカウント](../../storage/common/storage-account-create.md) |はい |VM には、その仮想ハード ディスクを格納するストレージ アカウントが必要です。 |
| [Virtual Network](../../virtual-network/virtual-networks-overview.md) |はい |VM は、仮想ネットワークのメンバーである必要があります。 |
| [パブリック IP アドレス](../../virtual-network/public-ip-addresses.md) |いいえ |VM には、リモートでアクセスするためのパブリック IP アドレスを割り当てることができます。 |
| [ネットワーク インターフェイス](../../virtual-network/virtual-network-network-interface.md) |はい |VM には、ネットワークで通信するためのネットワーク インターフェイスが必要です。 |
| [データ ディスク](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |いいえ |VM には、ストレージ容量を拡張するためのデータ ディスクを含めることができます。 |

## <a name="next-steps"></a>次のステップ

最初の VM を作成する

- [ポータル](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [Azure CLI](quick-create-cli.md)
