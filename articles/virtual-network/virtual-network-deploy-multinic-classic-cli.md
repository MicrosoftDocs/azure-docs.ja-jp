---
title: 複数の NIC を持つ VM (クラシック) を作成する - Azure クラシック CLI | Microsoft Docs
description: Azure クラシック コマンドライン インターフェイス (CLI) を使用して、複数の NIC を持つ VM (クラシック) を作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dc437b15f73866f76361da529690eac7a10af1a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058758"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-classic-cli"></a>Azure クラシック CLI を使用して複数の NIC が設定された VM (クラシック) を作成する

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Azure に仮想マシン (VM) を作成し、複数のネットワーク インターフェイス (NIC) を各 VM にアタッチできます。 複数 NIC では、複数の NIC 全体にトラフィック タイプを分散できます。 たとえば、インターネットと通信する NIC もあれば、インターネットに接続されていない内部リソースとのみ通信する NIC もあるとします。 アプリケーションの配布や WAN の最適化ソリューションなど、多くのネットワーク仮想アプライアンスでは、複数の NIC 間にネットワーク トラフィックを分散できる必要があります。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager デプロイ モデル](../virtual-machines/linux/multiple-nics.md)を使用してこれらの手順を実行する方法について説明します。

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

次の手順では、*IaaSStory* という名前のリソース グループを WEB サーバーに、*IaaSStory-BackEnd* という名前のリソース グループを DB サーバーに使用します。

## <a name="prerequisites"></a>前提条件
DB サーバーを作成する前に、このシナリオで必要なすべてのリソースを備えた *IaaSStory* リソース グループを作成する必要があります。 これらのリソースを作成するには、次の手順に従います。 VNet を作成するには、「[仮想ネットワークを作成する](virtual-networks-create-vnet-classic-cli.md)」の記事の手順に従います。

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>バックエンド VM をデプロイする
バックエンド VM は、次のリソースの作成に依存します。

* **データ ディスクのストレージ アカウント**。 パフォーマンスを高めるために、データベース サーバー上のデータ ディスクはソリッド ステート ドライブ (SSD) テクノロジーを使用します。これには、Premium Storage アカウントが必要です。 デプロイする Azure の場所が Premium Storage をサポートすることを確認してください。
* **NIC**。 各 VM には 2 つの NIC があり、1 つはデータベース アクセス用で、もう 1 つは管理用です。
* **可用性セット**。 メンテナンス中に少なくとも 1 つの VM が稼働し、実行されているようにするためには、すべてのデータベース サーバーを単一の可用性セットに追加します。

### <a name="step-1---start-your-script"></a>手順 1 - スクリプトの開始
使用するすべての Bash スクリプトは、 [ここ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh)からダウンロードできます。 以下の手順に従って、ご使用の環境で機能するようにスクリプトを変更します。

1. 上記の [前提条件](#prerequisites)でデプロイした既存のリソース グループに基づいて、以下の変数の値を変更します。

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. バックエンド デプロイメントに使用する値に基づいて、以下の変数の値を変更します。

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>手順 2 - VM 用に必要なリソースの作成
1. すべてのバックエンド VM 向けに新しいクラウド サービスを作成します。 リソース グループ名のための `$backendCSName` 変数と Azure リージョンのための `$location` を使用していることに注意してください。

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. VM で使用するデータ ディスクと OS の Premium Storage アカウントを作成します。

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>手順 3 - 複数 NIC を持つ VM の作成
1. `numberOfVMs` 変数に基づいて、複数の VM を作成するループを開始します。

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. 各 VM について、2 つの NIC それぞれの名前と IP アドレスを指定します。

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. VM を作成します。 名前、サブネット、IP アドレスを持つすべての NIC の一覧を含む `--nic-config` パラメーターを使用していることに注目します。

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. 各 VM あたり 2 つのデータ ディスクを作成します。

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>手順 4 - スクリプトの実行
ニーズに合わせてスクリプトをダウンロードおよび変更したら、スクリプトを実行して複数の NIC を持つバックエンド データベース VM を作成してください。

1. スクリプトを保存し、 **Bash** ターミナルから実行します。 次のように、最初の出力が表示されます。

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. 数分後に実行が終了し、次のように、出力の残りの部分が表示されます。

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>手順 5 - VM オペレーティング システム内でのルーティングの構成

Azure DHCP では、既定のゲートウェイが、仮想マシンに接続された最初の (プライマリ) ネットワーク インターフェイスに割り当てられます。 Azure では、既定のゲートウェイは、仮想マシンに接続された追加の (セカンダリ) ネットワーク インターフェイスに割り当てられません。 したがって、既定では、セカンダリ ネットワーク インターフェイスのサブネット外のリソースと通信することはできません。 しかし、セカンダリ ネットワーク インターフェイスは、そのサブネット外のリソースと通信できます。 セカンダリ ネットワーク インターフェイス用のルーティング構成については、「[Routing within a virtual machine operating system with multiple network interfaces (複数のネットワーク インターフェイスを持つ仮想マシンのオペレーティング システム内でのルーティング)](virtual-network-network-interface-vm.md)」を参照してください。
