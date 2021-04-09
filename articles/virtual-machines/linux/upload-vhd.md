---
title: Azure CLI を使用してカスタム Linux VM をアップロードまたはコピーする
description: Resource Manager デプロイ モデルと Azure CLI を使用して、カスタマイズされた仮想マシンをアップロードまたはコピーする
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 9d549d77b4a60f7543f69a9fd89e8b538c95d010
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564613"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Azure CLI を使用してカスタム ディスクから Linux VM を作成する

<!-- rename to create-vm-specialized -->

この記事では、Azure でカスタマイズされた仮想ハード ディスク (VHD) をアップロードする方法、および既存の VHD をコピーする方法について説明します。 その後、新しく作成された VHD を使用して新しい Linux 仮想マシン (VM) を作成します。 Linux ディストリビューションを要件に応じてインストールして構成した後、その VHD を使用して新しい Azure 仮想マシンを作成できます。

カスタマイズされたディスクから複数の VM を作成するには、まず VM または VHD からイメージを作成します。 詳細については、「[CLI を使用した Azure VM のカスタム イメージの作成](tutorial-custom-images.md)」を参照してください。

カスタム ディスクを作成するには、次の 2 つのオプションがあります。
* VHD のアップロード
* 既存の Azure VM をコピーする


## <a name="requirements"></a>必要条件
次の手順を完了するには、次のものが必要です。

- Azure で使用するために準備された Linux 仮想マシン。 この記事の「[VM を準備する](#prepare-the-vm)」のセクションでは、SSH で VM に接続するために必要な Azure Linux エージェント (waagent) のインストールに関するディストリビューション固有の情報を見つける方法について説明します。
- 既存の [Azure で動作保証済みの Linux ディストリビューション](endorsed-distros.md) (または「[動作保証外のディストリビューションに関する情報](create-upload-generic.md)」をご覧ください) から入手し VHD 形式の仮想ディスクにした VHD ファイル。 VM と VHD を作成するツールはいくつかあります。
  - [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) または [KVM](https://www.linux-kvm.org/page/RunningKVM) をインストールして構成します。その際、イメージ形式として VHD を使用します。 必要に応じて、`qemu-img convert` を使用して[イメージを変換](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)できます。
  - [Windows 10 上](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)または [Windows Server 2012/2012 R2 上](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))の Hyper-V を使用することもできます。

> [!NOTE]
> 新しい VHDX 形式は、Azure ではサポートされていません。 VM を作成するときに、形式として VHD を指定します。 必要に応じて、[qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) または [Convert-VHD](/powershell/module/hyper-v/convert-vhd) PowerShell コマンドレットを使用して VHDX ディスクを VHD に変換できます。 Azure は動的 VHD のアップロードをサポートしていないため、このようなディスクは、アップロードの前に静的 VHD に変換する必要があります。 [Azure VHD Utilities for GO (GO 用の Azure VHD Utilities)](https://github.com/Microsoft/azure-vhd-utils-for-go) などのツールを使用すると、ダイナミック ディスクを Azure へのアップロード中に変換できます。
> 
> 


- 最新の [Azure CLI](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/reference-index#az-login) を使用して Azure アカウントにサインインしていることを確認します。

以下の例では、パラメーター名を `myResourceGroup`、`mystorageaccount`、`mydisks` などの独自の値に置き換えてください。

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>VM を準備する

Azure は、さまざまな Linux ディストリビューションをサポートしています (「 [Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md)」を参照してください)。 次の記事では、Azure でサポートされているさまざまな Linux ディストリビューションを準備する方法について説明しています。

* [CentOS ベースのディストリビューション](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES と openSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [その他:動作保証外のディストリビューション](create-upload-generic.md)

Azure で Linux イメージを準備する際のその他の一般的なヒントについては、[Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes)に関するページをご覧ください。

> [!NOTE]
> [Azure プラットフォームの SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) は、いずれかの動作保証済みディストリビューションが、「[Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md)」の "サポートされているバージョン" で指定されている構成の詳細で使用されている場合にのみ、Linux を実行している VM に適用されます。
> 
> 

## <a name="option-1-upload-a-vhd"></a>オプション 1: VHD のアップロード

VHD をマネージド ディスクに直接アップロードできるようになりました。 手順については、「[Azure CLI を使用して Azure に VHD をアップロードする](disks-upload-vhd-to-managed-disk-cli.md)」を参照してください。

## <a name="option-2-copy-an-existing-vm"></a>オプション 2:既存の VM をコピーする

Azure でカスタマイズされた VM を作成してから OS ディスクをコピーし、それを新しい VM にアタッチして別のコピーを作成することもできます。 これはテストとしては有効ですが、複数の新しい VM のモデルとして既存の Azure VM を使用する場合は、代わりに *イメージ* を作成します。 既存の Azure VM からのイメージの作成の詳細については、「[CLI を使用した Azure VM のカスタム イメージの作成](tutorial-custom-images.md)」を参照してください。

既存の VM を別のリージョンにコピーする場合は、azcopy を使用して、[ディスクのコピーを別のリージョンに作成する](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)ことができます。 

それ以外の場合は、VM のスナップショットを取得し、スナップショットから新しい OS VHD を作成する必要があります。

### <a name="create-a-snapshot"></a>スナップショットの作成

この例では、リソース グループ *myResourceGroup* に *myVM* という VM のスナップショットを作成し、*osDiskSnapshot* というスナップショットを作成します。

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>マネージド ディスクを作成する

スナップショットから新しいマネージド ディスクを作成します。

スナップショットの ID を取得します。 この例では、スナップショットの名前は *osDiskSnapshot* であり、*myResourceGroup* リソース グループに含まれています。

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

マネージド ディスクを作成します。 この例では、スナップショットから、標準ストレージでサイズが 128 GB の *myManagedDisk* という名前のマネージド ディスクを作成します。

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>VM の作成

[az vm create](/cli/azure/vm#az-vm-create) を使用して VM を作成し、マネージド ディスクを OS ディスクとしてアタッチ (--attach-os-disk) します。 次の例では、アップロードされた VHD から作成したマネージド ディスクを使用して *myNewVM* という名前の VM を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

ソース VM の資格情報を使用して、SSH でその VM に接続できる必要があります。 

## <a name="next-steps"></a>次のステップ
カスタム仮想ディスクを準備してアップロードしたら、 [Resource Manager とテンプレートの使用](../../azure-resource-manager/management/overview.md)について学習しましょう。 必要であれば、新しい VM に [データ ディスクを追加](add-disk.md) することもできます。 VM 上で実行するアプリケーションがあり、これにアクセスする必要がある場合は、必ず [ポートとエンドポイント](nsg-quickstart.md)を開放してください。
