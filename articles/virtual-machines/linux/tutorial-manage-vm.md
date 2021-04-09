---
title: チュートリアル - Azure CLI を使用した Linux VM の作成と管理
description: このチュートリアルでは、Azure CLI を使用して、Azure 内に Linux VM を作成して管理する方法について説明します
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 77213fe83a56a33f4d10658f85465349949be792
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555603"
---
# <a name="tutorial-create-and-manage-linux-vms-with-the-azure-cli"></a>チュートリアル:Azure CLI を使用した Linux VM の作成と管理

Azure 仮想マシンは、完全に構成可能で柔軟なコンピューティング環境を提供します。 このチュートリアルでは、VM サイズや VM イメージの選択、VM のデプロイなどの Azure 仮想マシンの展開に関する基本事項について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * VM を作成し接続する
> * VM イメージを選択して使用する
> * 特定の VM サイズを確認して使用する
> * VM のサイズを変更する
> * VM の状態を表示して理解する

このチュートリアルでは、[Azure Cloud Shell](../../cloud-shell/overview.md) で CLI を使用します。このバージョンは常に更新され最新になっています。 Cloud Shell を開くには、コード ブロックの上部にある **[使ってみる]** を選択します。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシンの前にリソース グループを作成する必要があります。 この例では、*myResourceGroupVM* という名前のリソース グループが *eastus* リージョンに作成されます。 

```azurecli-interactive
az group create --name myResourceGroupVM --location eastus
```

このチュートリアル全体で示しているように、VM の作成時または変更時にリソース グループを指定します。

## <a name="create-virtual-machine"></a>仮想マシンの作成

仮想マシンを作成するには、[az vm create](/cli/azure/vm) コマンドを使用します。 

仮想マシンを作成するときに、オペレーティング システム イメージ、ディスクのサイズ、管理者資格情報など、いくつかの選択肢があります。 次の例では、Ubuntu Server を実行する *myVM* という名前の VM を作成します。 VM には、*azureuser* という名前のユーザー アカウントが作成されます。また、既定のキーの場所 ( *~/.ssh*) に SSH キーが存在しない場合は、SSH キーが生成されます。

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM の作成には数分かかることがあります。 VM が作成されると、Azure CLI で VM に関する以下の情報が出力されます。 `publicIpAddress` を記録します。このアドレスは仮想マシンへのアクセスに使用します。 

```output
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>VM への接続

Azure Cloud Shell またはローカル コンピューターで、SSH を使用して VM に接続できるようになりました。 サンプルの IP アドレスは、前の手順で記録した `publicIpAddress` に置き換えてください。

```bash
ssh azureuser@52.174.34.95
```

VM にログインしたら、アプリケーションをインストールして構成できます。 作業が終了したら、通常どおり SSH セッションを閉じます。

```bash
exit
```

## <a name="understand-vm-images"></a>VM イメージについて

Azure Marketplace には、VM の作成に使用できる多くのイメージが用意されています。 前の手順では、Ubuntu のイメージを使用して仮想マシンを作成しました。 この手順では、Azure CLI を使用して Marketplace で CentOS のイメージを検索し、このイメージを使用して 2 台目の仮想マシンをデプロイします。 

[az vm image list](/cli/azure/vm/image) コマンドを使用して、よく使用されるイメージのリストを表示します。

```azurecli-interactive 
az vm image list --output table
```

このコマンドの出力では、Azure にあるよく使用される VM イメージが返されます。

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

`--all` 引数を追加すると、リスト全体を確認できます。 また、`--publisher` か `–-offer` を使用してリストをフィルタリングすることも可能です。 この例では、*CentOS* に一致するプランがあるすべてのイメージを表示するように、リストをフィルター処理します。 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

出力の一部を次に示します。

```output
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

特定のイメージを使用して VM をデプロイするために、 *[Urn]* 列の値をメモに記録します。この値は、イメージを [識別](cli-ps-findimage.md#terminology)するための、発行元、プラン、SKU、およびオプションのバージョン番号で構成されます。 イメージを指定するときにイメージのバージョン数を "latest" で置き換えることもできます。このようにすると、ディストリビューションの最新バージョンが選択されます。 この例では、`--image` 引数を使用して、CentOS 6.5 イメージの最新バージョンを指定します。  

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>VM のサイズについて

仮想マシンのサイズにより、CPU、GPU、メモリなど、仮想マシンで利用できるコンピューティング リソースの量が決定されます。 仮想マシンのサイズは、予定のワーク ロードに合ったものにする必要があります。 ワークロードが増えた場合は既存の仮想マシンのサイズを変更できます。

### <a name="vm-sizes"></a>VM サイズ

次の表は、ユース ケース別にサイズを分類したものです。  

| Type                     | 一般的なサイズ           |    説明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [汎用](../sizes-general.md)         |B、Dsv3、Dv3、DSv2、Dv2、Av2、DC| CPU とメモリのバランスがとれています。 開発/テスト環境や、小中規模のアプリケーションとデータ ソリューションに最適です。  |
| [コンピューティングの最適化](../sizes-compute.md)   | Fsv2          | メモリに対する CPU の比が大きくなっています。 トラフィックが中程度のアプリケーション、ネットワーク アプライアンス、バッチ処理に適しています。        |
| [メモリの最適化](../sizes-memory.md)    | Esv3、Ev3、M、DSv2、Dv2  | コアに対するメモリの比が大きくなっています。 リレーショナル データベース、中から大規模のキャッシュ、およびインメモリ分析に適しています。                 |
| [ストレージの最適化](../sizes-storage.md)      | Lsv2、Ls              | 高いディスク スループットと IO。 ビッグ データ、SQL、および NoSQL のデータベースに最適です。                                                         |
| [GPU](../sizes-gpu.md)          | NV、NVv2、NC、NCv2、NCv3、ND            | 負荷の高いグラフィック レンダリングやビデオ編集に特化した VM です。       |
| [高性能](../sizes-hpc.md) | H        | オプションで高スループットのネットワーク インターフェイス (RDMA) を備えた、最も強力な CPU VM です。 |


### <a name="find-available-vm-sizes"></a>使用可能な VM サイズを確認する

特定の地域で利用可能な VM サイズのリストを確認するには、[az vm list-sizes](/cli/azure/vm) コマンドを使用します。 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

出力の一部を次に示します。

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>サイズを指定して VM を作成する

上記の VM 作成の例ではサイズを指定しなかったため、サイズは既定のものになっています。 VM のサイズは、作成時に `--size` 引数を付けて [az vm create](/cli/azure/vm) を使用することで指定できます。 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>VM のサイズを変更する

デプロイ後に VM のサイズを変更して、リソースの割り当てを増減できます。 VM の現在のサイズを表示するには、[az vm show](/cli/azure/vm) を使用します。

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

VM のサイズを変更する前に、現在の Azure クラスターで目的のサイズを利用可能であるか確認します。 [az vm list-vm-resize-options](/cli/azure/vm) コマンドでは、サイズのリストが返されます。 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```

目的のサイズが使用可能な場合は電源を入れた状態で VM のサイズを変更できます。ただし、この操作中に再起動が行われます。 [az vm resize]( /cli/azure/vm) コマンドを使用してサイズ変更を実行します。

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

目的のサイズが現在のクラスターにない場合、サイズ変更を行うには VM の割り当てを解除する必要があります。 [az vm deallocate]( /cli/azure/vm) コマンドを使用して、VM を停止し割り当てを解除します。 VM の電源を入れ直すと、一時ディスクのデータがすべて削除される可能性があることに注意してください。 また、静的な IP アドレスを使用している場合を除き、パブリック IP アドレスが変更されます。 

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

割り当ての解除後、サイズ変更を行うことができます。 

```azurecli-interactive
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

サイズの変更後、VM を起動できます。

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>VM の電源状態

Azure VM は、次のいずれかの電源状態になります。 この状態は、ハイパーバイザーから見た VM の現在の電源状態を表しています。 

### <a name="power-states"></a>電源状態

| 電源状態 | 説明
|----|----|
| 開始中 | 仮想マシンが起動中であることを示します。 |
| 実行中 | 仮想マシンが実行中であることを示します。 |
| 停止中 | 仮想マシンが停止中であることを示します。 | 
| 停止済み | 仮想マシンが停止されていることを示します。 仮想マシンが停止済みの状態でも、コンピューティング料金は発生します。  |
| 割り当て解除中 | 仮想マシンの割り当てが解除中であることを示します。 |
| 割り当て解除済み | 仮想マシンがハイパーバイザーから削除されているものの、コントロール プレーンでは使用可能であることを示します。 割り当て解除済み状態の仮想マシンでは、コンピューティング料金は発生しません。 |
| - | 仮想マシンの電源状態が不明であることを示します。 |

### <a name="find-the-power-state"></a>電源の状態を確認する

特定の VM の状態を取得するには、[az vm get-instance-view](/cli/azure/vm) コマンドを使用します。 必ず仮想マシンとリソース グループの有効な名前を指定してください。 

```azurecli-interactive
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

出力:

```output
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

サブスクリプション内のすべての VM の電源状態を取得するには、**statusOnly** パラメーターを *true* に設定して [Virtual Machines - List All API](/rest/api/compute/virtualmachines/listall) を使用します。

## <a name="management-tasks"></a>管理タスク

仮想マシンのライフサイクルでは、各種の管理タスクを実行する必要がある場合があります (仮想マシンの起動、停止、削除など)。 また、何度も行う作業や複雑な作業は、スクリプトを作成して自動化したい場合もあるでしょう。 日常的な管理タスクの多くは、Azure CLI を使ってコマンド ラインやスクリプトから実行できます。 

### <a name="get-ip-address"></a>IP アドレスを取得する

仮想マシンのプライベート IP アドレスとパブリック IP アドレスを取得するには、次のコマンドを使用します。  

```azurecli-interactive
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>仮想マシンの停止

```azurecli-interactive
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>仮想マシンの起動

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>リソース グループの削除

リソース グループを削除すると、グループに含まれているリソース (VM、仮想ネットワーク、ディスクなど) もすべて削除されます。 `--no-wait` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。 `--yes` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。

```azurecli-interactive
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のような基本的な VM の作成と管理を実行する方法について説明しました。

> [!div class="checklist"]
> * VM を作成し接続する
> * VM イメージを選択して使用する
> * 特定の VM サイズを確認して使用する
> * VM のサイズを変更する
> * VM の状態を表示して理解する

次のチュートリアルに進み、VM ディスクについて確認してください。  

> [!div class="nextstepaction"]
> [VM ディスクの作成と管理](./tutorial-manage-disks.md)