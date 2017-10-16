---
title: "Azure Stack 仮想マシンの概要"
description: "Azure Stack 仮想マシンについての詳細"
services: azure-stack
author: anjayajodha
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: 68da653052d0e3dfd66d6b65958046e42cefce73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Azure Stack 仮想マシンの概要

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

## <a name="overview"></a>概要
Azure Stack 仮想マシン (VM) は、Azure Stack で提供されるオンデマンドでスケーラブルなコンピューティング リソースの一種です。 通常、コンピューティング環境を他の手段より細かく管理する必要がある場合に、VM を選択します。 この記事では、VM を作成する前に検討する必要のある事項、VM の作成方法、VM の管理方法に関する情報を提供します。

Azure Stack VM により、個々 のクラスターやコンピューターを管理する必要なしに柔軟な仮想化が可能になります。 ただし、VM のメンテナンス、つまり VM 上で動作するソフトウェアの構成、その修正プログラムの適用、インストールは必要です。

Azure Stack 仮想マシンは、さまざまな方法で利用できます。 For example:

* **開発とテスト** – Azure Stack VM は、アプリケーションのコーディングとテストに必要な特定の構成でコンピューターをすばやく簡単に作成する手段を提供します。

* **クラウドのアプリケーション** – アプリケーションの需要は変動する可能性があるため、Azure Stack 内の VM でアプリケーションを実行することは経済的に理に適っています。 VM が必要になったら追加分の料金を支払い、不要になったらシャットダウンすることができます。

* **データセンターの拡張** – Azure Stack 仮想ネットワーク内の仮想マシンは、組織のネットワークや Azure に簡単に接続できます。

アプリケーションで使用する VM の数は、ニーズに応じてスケールアップおよびスケールアウトできます。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM の作成前に検討する必要のある事項

Azure Stack でアプリケーション インフラストラクチャを構築する際には、多数の設計上の考慮事項が必ず存在します。 開始する前に、VM の次の側面を考慮することが重要です。

- アプリケーション リソースの名前
- VM のサイズ
- 作成できる VM の最大数
- VM で実行されるオペレーティング システム
- 開始した後の VM の構成 
- VM で必要な関連リソース

### <a name="naming"></a>名前を付ける

仮想マシンには名前が割り当てられ、コンピューター名がオペレーティング システムの一部として構成されます。 VM の名前は最大で 15 文字です。

Azure Stack を使用してオペレーティング システム ディスクを作成する場合、コンピューター名と仮想マシン名は同じになります。 以前構成されたオペレーティング システムが含まれる独自のイメージをアップロードして使用し、それを基に仮想マシンを作成する場合は、別の名前にすることができます。 独自のイメージ ファイルをアップロードするときは、ベスト プラクティスとして、オペレーティング システムのコンピューター名と仮想マシン名を同じにします。

### <a name="vm-size"></a>VM サイズ

使用する VM のサイズは、実行するワークロードによって決まります。 さらに、選択したサイズによって、処理能力、メモリ、ストレージの容量などの要素が決まります。 Azure Stack では、さまざまな種類の使用をサポートするために、さまざまなサイズを用意しています。

### <a name="vm-limits"></a>VM の制限

サブスクリプションにはそれぞれ既定のクォータ制限が設けられており、プロジェクトで多数の VM をデプロイする場合に、その点が影響する可能性があります。 現在は、リージョンあたり 20 VM の制限がサブスクリプションごとに設けられています。

### <a name="operating-system-disks-and-images"></a>オペレーティング システム ディスクおよびイメージ

仮想マシンは、仮想ハード ディスク (VHD) を使用して、オペレーティング システム (OS) およびデータを保存します。 VHD は、OS をインストールするために選択できるイメージの保存にも使用できます。
Azure Stack には、オペレーティング システムのさまざまなバージョンと種類で使用できるマーケットプレースが用意されています。 Marketplace イメージは、イメージの発行元、プラン、SKU、およびバージョン (通常は最新バージョンとして指定) によって識別されます。

次の表に、イメージに関する情報を見つける方法をいくつか示します。


|メソッド|Description|
|---------|---------|
|Azure Stack ポータル|値は、使用するイメージを選択する際に自動的に指定されます。|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API     |[イメージ発行元の一覧表示](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[イメージ プランの一覧表示](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[イメージ SKU の一覧表示](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

独自のイメージをアップロードして使用することもできます。 その場合、発行元の名前、プラン、および SKU は使用されません。

### <a name="extensions"></a>拡張機能

VM の拡張機能は、デプロイ後の構成と自動化タスクを通じて VM に追加の機能を提供します。
拡張機能を使用して、次のような一般的なタスクを実行できます。

* カスタム スクリプトの実行 – カスタム スクリプト拡張機能を利用すれば、VM のプロビジョニングの際にスクリプトを実行して、VM でワークロードを構成できます。
* 構成のデプロイと管理 – PowerShell Desired State Configuration (DSC) 拡張機能を利用すれば、VM で DSC をセットアップして構成と環境を管理できます。
* 診断データの収集 – Azure 診断拡張機能を利用すれば、アプリケーションの正常性を監視するために使用できる診断データを収集するように VM を構成できます。

### <a name="related-resources"></a>関連リソース

次の表のリソースは VM によって使用されるため、VM の作成時に存在するか、作成する必要があります。


|リソース|必須|Description|
|---------|---------|---------|
|リソース グループ|あり|VM は、リソース グループに含まれる必要があります。|
|ストレージ アカウント|あり|VM には、その仮想ハード ディスクを格納するストレージ アカウントが必要です。|
|Virtual Network|あり|VM は、仮想ネットワークのメンバーである必要があります。|
|パブリック IP アドレス|いいえ|VM には、リモートでアクセスするためのパブリック IP アドレスを割り当てることができます。|
|ネットワーク インターフェイス|あり|VM には、ネットワークで通信するためのネットワーク インターフェイスが必要です。|
|データ ディスク|いいえ|VM には、ストレージ容量を拡張するためのデータ ディスクを含めることができます。|

## <a name="how-do-i-create-my-first-vm"></a>最初の VM の作成方法

VM の作成方法にはいくつかの選択肢があります。 どれを選ぶかは環境によって異なります。
次の表は、VM の作成を開始するうえでの情報を提供します。


|メソッド|記事|
|---------|---------|
|Azure Stack ポータル|Azure Stack ポータルで Windows 仮想マシンを作成する<br>[Azure Stack ポータルで Linux 仮想マシンを作成する](azure-stack-quick-linux-portal.md)|
|テンプレート|Azure Stack のクイック スタート テンプレートは次の場所にあります。<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Azure Stack で PowerShell を使用して Windows 仮想マシンを作成する](azure-stack-quick-create-vm-windows-powershell.md)<br>[Azure Stack で PowerShell を使用して Linux 仮想マシンを作成する](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Azure Stack で CLI を使用して Windows 仮想マシンを作成する](azure-stack-quick-create-vm-windows-cli.md)<br>[Azure Stack で CLI を使用して Linux 仮想マシンを作成する](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="how-do-i-manage-the-vm-that-i-created"></a>作成した VM の管理方法

VM は、ブラウザーベースのポータル、スクリプトがサポートされるコマンドライン ツール、または直接 API を使用して管理できます。 実施する一般的な管理タスクは、VM に関する情報の収集、VM へのログオン、可用性の管理、バックアップの作成などです。

### <a name="get-information-about-a-vm"></a>VM に関する情報の取得

次の表に、VM に関する情報の取得方法の一部を示します。


|メソッド|Description|
|---------|---------|
|Azure Stack ポータル|ハブ メニューの [Virtual Machines (仮想マシン)] をクリックし、一覧から VM を選択します。 その VM のページで、概要情報を確認したり、値の設定やメトリックの監視を実行したりできます。|
|Azure PowerShell|VM の管理は、Azure と Azure Stack とで似ています。 PowerShell の使用について詳しくは、次の Azure トピックをご覧ください。<br>[Azure PowerShell モジュールを使用して Windows VM を作成および管理する](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|クライアント SDK|C# による VM の管理は、Azure と Azure Stack とで似ています。 詳細については、次の Azure トピックを参照してください。<br>[C# を使用して Azure で Windows VM を作成および管理する](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-the-vm"></a>VM に接続します

Azure Stack ポータルの **[接続]** ボタンを使用して、VM に接続できます。

## <a name="next-steps"></a>次のステップ
* [Azure Stack の仮想マシンに関する考慮事項](azure-stack-vm-considerations.md)

