---
title: "Azure CLI 2.0 を使用したカスタム Linux VM のアップロードまたはコピー | Microsoft Docs"
description: "Resource Manager デプロイメント モデルと Azure CLI 2.0 を使用してカスタマイズされた仮想マシンをアップロードおよびコピーする"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/06/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 7c297725c26ea6c44403a10ecdcc3542f89f10b4
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Azure CLI 2.0 を使用してカスタム ディスクから Linux VM を作成しアップロードする

<!-- rename to create-vm-specialized -->

この記事は、カスタマイズされた仮想ハード ディスク (VHD) をアップロードする方法、または Azure で既存の VHD をコピーし、カスタム ディスクから新しい Linux 仮想マシン (VM) を作成する方法を示します。 要件に合った Linux ディストリビューションをインストールして構成し、その VHD を使用して新しい Azure 仮想マシンをすばやく作成することができます。

カスタマイズされたディスクから複数の VM を作成する場合は、VM または VHD からイメージを作成する必要があります。 詳細については、「[CLI を使用した Azure VM のカスタム イメージの作成](tutorial-custom-images.md)」をご覧ください。

2 つのオプションがあります。
* [VHD をアップロードする](#option-1-upload-a-specialized-vhd)
* [既存の Azure VM をコピーする](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>クイック コマンド

[az vm create](/cli/azure/vm#create) を使用してカスタマイズされたディスクまたは特殊化されたディスクから新しい VM を作成するときには、カスタムまたはマーケットプレース イメージを指定 (--image) するのではなく、ディスクを**アタッチ** (--attach-os-disk) します。 次の例では、カスタマイズされた VHD から作成した *myManagedDisk* という管理ディスクを使用して *myVM* という VM を作成します。

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>必要条件
次の手順を完了するには、以下が必要です。

* Azure で使用するために準備された Linux 仮想マシン。 この記事の「[アップロードするディスクを準備する](#prepare-the-vm)」セクションでは、Azure Linux エージェント (waagent) のインストールに関して、ディストリビューション固有の情報を見つける方法を説明します。Azure Linux エージェント (waagent) は、VM を Azure で正常に動作させるため、および SSH を使用して VM に接続できるようにするために必要です。
* 既存の [Azure で動作保証済みの Linux ディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (または「[動作保証外のディストリビューションに関する情報](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください) から入手し VHD 形式の仮想ディスクにした VHD ファイル。 VM と VHD を作成するツールはいくつかあります。
  * [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) または [KVM](http://www.linux-kvm.org/page/RunningKVM) をインストールして構成します。その際、イメージ形式として VHD を使用します。 必要であれば **qemu-img convert** を使用して[イメージを変換](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)できます。
  * [Windows 10 上](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install)または [Windows Server 2012/2012 R2 上](https://technet.microsoft.com/library/hh846766.aspx)の Hyper-V を使用することもできます。

> [!NOTE]
> 新しい VHDX 形式は、Azure ではサポートされていません。 VM を作成するときに、形式として VHD を指定します。 必要であれば、[qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) または [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell コマンドレットを使用して VHDX ディスクを VHD に変換できます。 Azure では動的 VHD のアップロードはサポートしないため、そのようなディスクは、アップロードする前に静的 VHD に変換する必要があります。 Azure へのアップロード プロセス中、動的ディスクの変換には、 [Azure VHD Utilities for GO (GO 用の Azure VHD Utilities)](https://github.com/Microsoft/azure-vhd-utils-for-go) などのツールを使用できます。
> 
> 


* [Azure CLI 2.0](/cli/azure/install-az-cli2) の最新版がインストールされ、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしていることを確認します。

次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*mystorageaccount*、*mydisks* といったパラメーター名にします。

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>VM を準備する

Azure は、さまざまな Linux ディストリビューションをサポートしています (「 [Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください)。 次の記事では、Azure でサポートされる以下のさまざまな Linux ディストリビューションを準備する方法について説明します。

* [CentOS ベースのディストリビューション](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES と openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [その他 - 動作保証外のディストリビューション](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure で Linux イメージを準備する際のその他の一般的なヒントについては、[Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes)に関するページをご覧ください。

> [!NOTE]
> [Azure プラットフォームの SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) は、動作保証済みディストリビューションのいずれか 1 つを、「[Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」の "サポートされているバージョン" で指定されている構成で使用した場合にのみ、Linux を実行する VM に適用されます。
> 
> 

## <a name="option-1-upload-a-vhd"></a>オプション 1: VHD をアップロードする

ローカル コンピューターで実行されている VHD、または別のクラウドからエクスポートしたカスタマイズされた VHD をアップロードできます。 VHD を使用して新しい Azure VM を作成するには、ストレージ アカウントに VHD をアップロードし、VHD から管理ディスクを作成する必要があります。 

### <a name="create-a-resource-group"></a>リソース グループの作成

カスタム ディスクをアップロードして VM を作成する前に、まず [az group create](/cli/azure/group#create) を使用してリソース グループを作成する必要があります。

次の例では、*myResourceGroup* というリソース グループを *eastus* に作成します。「[Azure Managed Disks の概要](../windows/managed-disks-overview.md)」
```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[az storage account create](/cli/azure/storage/account#create) を使用して、カスタム ディスクと VM 用に、ストレージ アカウントを作成します。 

次の例では、*mystorageaccount* というストレージ アカウントを以前に作成したリソース グループに作成します。

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>ストレージ アカウント キーの一覧表示
Azure では、ストレージ アカウントごとに 2 つの 512 ビット アクセス キーが生成されます。 これらのアクセス キーは、書き込み操作の実行のように、ストレージ アカウントを認証するときに使用します。 ストレージへのアクセス管理の詳細については [こちら](../../storage/common/storage-create-storage-account.md#manage-your-storage-account)をご覧ください。 アクセス キーを表示するには、[az storage account keys list](/cli/azure/storage/account/keys#list) を使用します。

作成したストレージ アカウントのアクセス キーは次のようにして表示します。

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
ローカル ファイル システムを論理的に整理するためにさまざまなディレクトリを作成するのと同様に、ストレージ アカウント内にコンテナーを作成してディスクを整理します。 ストレージ アカウントには、任意の数のコンテナーを含めることができます。 コンテナーを作成するには、[az storage container create](/cli/azure/storage/container#create) を使用します。

次の例では、*mydisks* というコンテナーを作成します。

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>VHD をアップロードする
次に、[az storage blob upload](/cli/azure/storage/blob#upload) を使用してカスタム ディスクをアップロードします。 カスタム ディスクをアップロードし、ページ BLOB として保存します。

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

### <a name="create-a-managed-disk"></a>管理ディスクを作成する


[az disk create](/cli/azure/disk#create) を使用して VHD から管理ディスクを作成します。 次の例では、指定したストレージ アカウントとコンテナーにアップロードした VHD から *myManagedDisk* という管理ディスクを作成します。

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>オプション 2: 既存の VM をコピーする

Azure でカスタマイズされた VM を作成してから OS ディスクをコピーし、新しい VM にアタッチして別のコピーを作成することもできます。 これは、テストでは問題ありませんが、複数の新しい VM のモデルとして既存の Azure VM を使用する場合は、代わりに**イメージ**を作成してください。 既存の Azure VM からイメージを作成する方法の詳細については、「[CLI を使用した Azure VM のカスタム イメージの作成](tutorial-custom-images.md)」をご覧ください。

### <a name="create-a-snapshot"></a>スナップショットの作成

この例では、リソース グループ *myResourceGroup* に *myVM* という VM のスナップショットを作成し、*osDiskSnapshot* というスナップショットを作成します。

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>管理ディスクを作成する

スナップショットから新しい管理ディスクを作成します。

スナップショットの ID を取得します。 この例では、スナップショットの名前は *osDiskSnapshot* であり、*myResourceGroup* リソース グループに含まれています。

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

管理ディスクを作成します。 この例では、スナップショットから標準ストレージのサイズが 128 GB の *myManagedDisk* という管理ディスクを作成します。

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>VM の作成

ここで、[az vm create](/cli/azure/vm#create) で VM を作成し、OS ディスクとして管理ディスクをアタッチ (--attach-os-disk) します。 次の例では、アップロードした VHD から作成した管理ディスクを使用して *myNewVM* という VM を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

資格情報を使用して、ソース VM からこの VM に SSH できる必要があります。 

## <a name="next-steps"></a>次のステップ
カスタム仮想ディスクを準備してアップロードしたら、 [Resource Manager とテンプレートの使用](../../azure-resource-manager/resource-group-overview.md)について学習しましょう。 必要であれば、新しい VM に [データ ディスクを追加](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) することもできます。 VM 上で実行するアプリケーションがあり、これにアクセスする必要がある場合は、必ず [ポートとエンドポイント](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を開放してください。


