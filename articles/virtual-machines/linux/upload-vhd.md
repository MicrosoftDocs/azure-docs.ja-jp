---
title: Azure CLI を使用したカスタム Linux VM のアップロードまたはコピー | Microsoft Docs
description: Resource Manager デプロイ モデルと Azure CLI を使用して、カスタマイズされた仮想マシンをアップロードまたはコピーする
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: 6483fa8737ee3de6a60c4e4646fefec30ae702b6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006653"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Azure CLI を使用してカスタム ディスクから Linux VM を作成する

<!-- rename to create-vm-specialized -->

この記事では、Azure でカスタマイズされた仮想ハード ディスク (VHD) をアップロードする方法、および既存の VHD をコピーする方法について説明します。 その後、新しく作成された VHD を使用して新しい Linux 仮想マシン (VM) を作成します。 Linux ディストリビューションを要件に応じてインストールして構成した後、その VHD を使用して新しい Azure 仮想マシンを作成できます。

カスタマイズされたディスクから複数の VM を作成するには、まず VM または VHD からイメージを作成します。 詳細については、「[CLI を使用した Azure VM のカスタム イメージの作成](tutorial-custom-images.md)」を参照してください。

カスタム ディスクを作成するには、次の 2 つのオプションがあります。
* VHD のアップロード
* 既存の Azure VM をコピーする

## <a name="quick-commands"></a>クイック コマンド

カスタマイズまたは特殊化されたディスクから [az vm create](/cli/azure/vm#az-vm-create) を使用して新しい VM を作成する場合は、カスタムまたはマーケットプレース イメージを指定 (--image) するのではなく、ディスクを**アタッチ** (--attach-os-disk) します。 次の例では、カスタマイズされた VHD から作成した *myManagedDisk* というマネージド ディスクを使用して *myVM* という VM を作成します。

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>必要条件
次の手順を完了するには、次のものが必要です。

* Azure で使用するために準備された Linux 仮想マシン。 この記事の「[VM を準備する](#prepare-the-vm)」のセクションでは、SSH で VM に接続するために必要な Azure Linux エージェント (waagent) のインストールに関するディストリビューション固有の情報を見つける方法について説明します。
* 既存の [Azure で動作保証済みの Linux ディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (または「[動作保証外のディストリビューションに関する情報](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください) から入手し VHD 形式の仮想ディスクにした VHD ファイル。 VM と VHD を作成するツールはいくつかあります。
  * [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) または [KVM](https://www.linux-kvm.org/page/RunningKVM) をインストールして構成します。その際、イメージ形式として VHD を使用します。 必要に応じて、`qemu-img convert` を使用して[イメージを変換](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)できます。
  * [Windows 10 上](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install)または [Windows Server 2012/2012 R2 上](https://technet.microsoft.com/library/hh846766.aspx)の Hyper-V を使用することもできます。

> [!NOTE]
> 新しい VHDX 形式は、Azure ではサポートされていません。 VM を作成するときに、形式として VHD を指定します。 必要に応じて、[qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) または [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell コマンドレットを使用して VHDX ディスクを VHD に変換できます。 Azure は動的 VHD のアップロードをサポートしていないため、このようなディスクは、アップロードの前に静的 VHD に変換する必要があります。 [Azure VHD Utilities for GO (GO 用の Azure VHD Utilities)](https://github.com/Microsoft/azure-vhd-utils-for-go) などのツールを使用すると、ダイナミック ディスクを Azure へのアップロード中に変換できます。
> 
> 


* 最新の [Azure CLI](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/reference-index#az-login) を使用して Azure アカウントにサインインしていることを確認します。

以降の例では、パラメーター名の例を *myResourceGroup*、*mystorageaccount*、*mydisks* などの独自の値に置き換えてください。

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>VM を準備する

Azure は、さまざまな Linux ディストリビューションをサポートしています (「 [Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください)。 次の記事では、Azure でサポートされているさまざまな Linux ディストリビューションを準備する方法について説明しています。

* [CentOS ベースのディストリビューション](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES と openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [その他:動作保証外のディストリビューション](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure で Linux イメージを準備する際のその他の一般的なヒントについては、[Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes)に関するページをご覧ください。

> [!NOTE]
> [Azure プラットフォームの SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) は、いずれかの動作保証済みディストリビューションが、「[Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」の "サポートされているバージョン" で指定されている構成の詳細で使用されている場合にのみ、Linux を実行している VM に適用されます。
> 
> 

## <a name="option-1-upload-a-vhd"></a>オプション 1:VHD のアップロード

ローカル コンピューターで実行されている VHD、または別のクラウドからエクスポートしたカスタマイズされた VHD をアップロードできます。 VHD を使用して新しい Azure VM を作成するには、ストレージ アカウントに VHD をアップロードし、その VHD からマネージド ディスクを作成する必要があります。 詳細については、「[Azure Managed Disks の概要](../windows/managed-disks-overview.md)」をご覧ください。

### <a name="create-a-resource-group"></a>リソース グループの作成

カスタム ディスクをアップロードし、VM を作成する前に、[az group create](/cli/azure/group#az-group-create) を使用してリソース グループを作成する必要があります。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[az storage account create](/cli/azure/storage/account) を使用して、カスタム ディスクと VM 用に、ストレージ アカウントを作成します。 次の例では、*mystorageaccount* というストレージ アカウントを以前に作成したリソース グループに作成します。

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>ストレージ アカウント キーの一覧表示
Azure では、ストレージ アカウントごとに 2 つの 512 ビット アクセス キーが生成されます。 これらのアクセス キーは、書き込み操作を実行する場合などに、ストレージ アカウントを認証するときに使用されます。 詳細については、[ストレージへのアクセスの管理](../../storage/common/storage-account-manage.md#access-keys)に関するページを参照してください。 

アクセス キーを表示するには、[az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) を使用します。 たとえば、作成したストレージ アカウントのアクセス キーを表示するには、次のようにします。

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

次のように出力されます。

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
以降の手順でストレージ アカウントとのやり取りに使用するため、 **key1** を書き留めます。

### <a name="create-a-storage-container"></a>ストレージ コンテナーを作成する
ローカル ファイル システムを論理的に整理するためにさまざまなディレクトリを作成するのと同様に、ディスクを整理するためにストレージ アカウント内にコンテナーを作成します。 ストレージ アカウントには、多数のコンテナーを含めることができます。 コンテナーを作成するには、[az storage container create](/cli/azure/storage/container#az-storage-container-create) を使用します。

次の例では、*mydisks* というコンテナーを作成します。

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>VHD をアップロードする
[az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload) を使用してカスタム ディスクをアップロードします。 カスタム ディスクをアップロードし、ページ BLOB として格納します。

アクセス キー、前の手順で作成したコンテナー、ローカル コンピューター上のカスタム ディスクへのパスの順に指定します。

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
VHD のアップロードにはしばらくかかることがあります。

### <a name="create-a-managed-disk"></a>マネージド ディスクを作成する


[az disk create](/cli/azure/disk#az-disk-create) を使用して VHD からマネージド ディスクを作成します。 次の例では、指定したストレージ アカウントとコンテナーにアップロードした VHD から *myManagedDisk* というマネージド ディスクを作成します。

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>オプション 2:既存の VM をコピーする

Azure でカスタマイズされた VM を作成してから OS ディスクをコピーし、それを新しい VM にアタッチして別のコピーを作成することもできます。 これはテストとしては有効ですが、複数の新しい VM のモデルとして既存の Azure VM を使用する場合は、代わりに*イメージ*を作成します。 既存の Azure VM からのイメージの作成の詳細については、「[CLI を使用した Azure VM のカスタム イメージの作成](tutorial-custom-images.md)」を参照してください。

### <a name="create-a-snapshot"></a>スナップショットの作成

この例では、リソース グループ *myResourceGroup* に *myVM* という VM のスナップショットを作成し、*osDiskSnapshot* というスナップショットを作成します。

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>マネージド ディスクを作成する

スナップショットから新しいマネージド ディスクを作成します。

スナップショットの ID を取得します。 この例では、スナップショットの名前は *osDiskSnapshot* であり、*myResourceGroup* リソース グループに含まれています。

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

マネージド ディスクを作成します。 この例では、スナップショットから、標準ストレージでサイズが 128 GB の *myManagedDisk* という名前のマネージド ディスクを作成します。

```azure-cli
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

## <a name="next-steps"></a>次の手順
カスタム仮想ディスクを準備してアップロードしたら、 [Resource Manager とテンプレートの使用](../../azure-resource-manager/resource-group-overview.md)について学習しましょう。 必要であれば、新しい VM に [データ ディスクを追加](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) することもできます。 VM 上で実行するアプリケーションがあり、これにアクセスする必要がある場合は、必ず [ポートとエンドポイント](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を開放してください。
