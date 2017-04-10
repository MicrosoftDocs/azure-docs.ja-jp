---
title: "Azure CLI 2.0 を使用したカスタム Linux ディスクのアップロード | Microsoft Docs"
description: "Resource Manager デプロイメント モデルと Azure CLI 2.0 を使用して、仮想ハード ディスク (VHD) を作成し、Azure にアップロードする"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 15a1605938941b056eda1a151ea018d05bffc246
ms.lasthandoff: 03/29/2017


---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Azure CLI 2.0 を使用してカスタム ディスクをアップロードし、Linux VM を作成する
この記事では、Azuer CLI 2.0 を使用して仮想ハード ディスク (VHD) を Azure にアップロードし、そのカスタム ディスクから Linux VM を作成する方法について説明します。 これらの手順は、[Azure CLI 1.0](virtual-machines-linux-upload-vhd-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。 この機能によって、要件に合った Linux ディストリビューションをインストールして構成し、その VHD を使用して Azure 仮想マシン (VM) をすばやく作成することができます。

## <a name="quick-commands"></a>クイック コマンド
タスクをすばやく実行する必要がある場合のために、次のセクションでは、VHD を Azure にアップロードするための基本的なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](#requirements)。

[Azure CLI 2.0](/cli/azure/install-az-cli2) の最新版がインストールされ、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしていることを確認します。

次の例では、パラメーター名を独自の値を置き換えます。 パラメーター名の例には、`myResourceGroup`、`mystorageaccount`、および `mydisks` が含まれています。

最初に、[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `WestUs` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

[az storage account create](/cli/azure/storage/account#create) を使用して、仮想ディスクを保持するストレージ アカウントを作成します。 [Azure Managed Disks](../storage/storage-managed-disks-overview.md) を使用する場合でも、管理ディスクに変換する前に、VHD のアップロード先のストレージ アカウントを作成する必要があります。 次の例では、`mystorageaccount` という名前のストレージ アカウントを作成します。

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

[az storage account keys list](/cli/azure/storage/account/keys#list) を使用して、ストレージ アカウントのアクセス キーの一覧を表示します。 `key1` を書き留めておきます。

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

[az storage container create](/cli/azure/storage/container#create) を使用して、取得したストレージ キーを使用してストレージ アカウント内にコンテナーを作成します。 次の例では、`key1` からのストレージ キーの値を使用して `mydisks` という名前のコンテナーを作成します。

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

最後に、[az storage blob upload](/cli/azure/storage/blob#upload) を使用して、作成したコンテナーに VHD をアップロードします。 `/path/to/disk/mydisk.vhd` の下で VHD のローカル パスを指定します。

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

### <a name="azure-managed-disks"></a>Azure Managed Disks
Azure Managed Disks または管理されていないディスクを使用して VM を作成することができます。 Managed Disks は Azure プラットフォームによって処理されるため、ディスクを格納するための準備も場所も必要ありません。 Azure Managed Disks の詳細については、「[Azure Managed Disks の概要](../storage/storage-managed-disks-overview.md)」をご覧ください。 VHD から VM を作成するには最初に、[az disk create](/cli/azure/disk/create) を使用して VHD を管理ディスクに変換する必要があります。

```azurecli
az disk create --resource-group myResourceGroup --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```

[az disk list](/cli/azure/disk/list) を使用して作成した管理ディスクの詳細を取得します。

```azurecli
az disk list --resource-group myResourceGroup \
  --query [].{Name:name,ID:id} --output table
```

出力は次の例のようになります。

```azurecli
Name               ID
-----------------  ----------------------------------------------------------------------------------------------------
myManagedDisk    /subscriptions/mySubscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/myManagedDisk
```

次に、[az vm create](/cli/azure/vm#create) を使用して VM を作成し、管理ディスク名を指定します (`--attach-os-disk`)。 次の例では、アップロードされた VHD から作成した管理ディスクを使用して `myVM` という VM を作成します。

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --attach-os-disk myManagedDisk
```

### <a name="unmanaged-disks"></a>非管理対象ディスク
非管理対象ディスクで VM を作成するには、[az vm create](/cli/azure/vm#create) を使用してディスクに URI を指定します (`--image`)。 次の例では、以前にアップロードした仮想ディスクを使用して、`myVM` という名前の VM を作成します。

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

目標のストレージ アカウントは、仮想ディスクのアップロード先と同じである必要があります。 また、**az vm create** コマンドで求められるその他のパラメーターすべて (仮想ネットワーク、パブリック IP アドレス、ユーザー名、SSH キー) を指定するか、プロンプトで回答する必要もあります。 詳細については、 [Resource Manager の CLI から利用できるパラメーター](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)に関するトピックをご覧ください。

## <a name="requirements"></a>必要条件
次の手順を完了するには、以下が必要です。

* **.vhd ファイルにインストールされている Linux オペレーティング システム** - [動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (または[動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を参照してください) を VHD 形式で仮想ディスクにインストールします。 VM と VHD を作成するツールはいくつかあります。
  * [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) または [KVM](http://www.linux-kvm.org/page/RunningKVM) をインストールして構成します。その際、イメージ形式として VHD を使用します。 必要であれば `qemu-img convert` を使用して[イメージを変換](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)できます。
  * [Windows 10 上](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install)または [Windows Server 2012/2012 R2 上](https://technet.microsoft.com/library/hh846766.aspx)の Hyper-V を使用することもできます。

> [!NOTE]
> 新しい VHDX 形式は、Azure ではサポートされていません。 VM を作成するときに、形式として VHD を指定します。 必要であれば、[`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) または [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) の PowerShell コマンドレットを使用して VHDX ディスクを VHD に変換できます。 Azure では動的 VHD のアップロードはサポートしないため、そのようなディスクは、アップロードする前に静的 VHD に変換する必要があります。 Azure へのアップロード プロセス中、動的ディスクの変換には、 [Azure VHD Utilities for GO (GO 用の Azure VHD Utilities)](https://github.com/Microsoft/azure-vhd-utils-for-go) などのツールを使用できます。
> 
> 

* カスタム ディスクから作成する VM は、ディスク自体と同じストレージ アカウント内に存在する必要があります。
  * ストレージ アカウントと、カスタム ディスクおよび作成した VM の両方を保持するコンテナーを作成します。
  * VM をすべて作成したら、ディスクを安全に削除することができます。

[Azure CLI 2.0](/cli/azure/install-az-cli2) の最新版がインストールされ、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしていることを確認します。

次の例では、パラメーター名を独自の値を置き換えます。 パラメーター名の例には、`myResourceGroup`、`mystorageaccount`、および `mydisks` が含まれています。

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>アップロードするディスクを準備する
Azure は、さまざまな Linux ディストリビューションをサポートしています (「 [Azure での動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください)。 次の記事では、Azure でサポートされる以下のさまざまな Linux ディストリビューションを準備する方法について説明します。

* **[CentOS ベースのディストリビューション](virtual-machines-linux-create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES と openSUSE](virtual-machines-linux-suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[その他 - 動作保証外のディストリビューション](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Azure で Linux イメージを準備する際のその他のヒントについては、**[Linux のインストールに関する注記](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**をご覧ください。

> [!NOTE]
> [Azure プラットフォームの SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) は、動作保証済みディストリビューションのいずれか 1 つを、「[Azure での動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」の "サポートされているバージョン" で指定されている構成で使用した場合にのみ、Linux を実行する VM に適用されます。
> 
> 

## <a name="create-a-resource-group"></a>リソース グループの作成
リソース グループは、仮想ネットワークやストレージ記憶域など、仮想マシンをサポートするすべての Azure リソースを論理的にまとめます。 リソース グループの詳細については、[リソース グループの概要](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。 カスタム ディスクをアップロードして VM を作成する前に、まず [az group create](/cli/azure/group#create) を使用してリソース グループを作成する必要があります。

次の例では、場所 `westus` に `myResourceGroup` という名前のリソース グループを作成します: [Azure Managed Disks の概要](../storage/storage-managed-disks-overview.md)
```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
VM を作成するには、Azure Managed Disks または非管理対象ディスクを使用できます。 Managed Disks は Azure プラットフォームによって処理されるため、ディスクを格納するための準備も場所も必要ありません。 非管理対象ディスクは、ストレージ アカウント内にページ BLOB として保存されます。 詳しくは、「[Azure Managed Disks の概要](../storage/storage-managed-disks-overview.md)」または[ Azure Blob Storage に関するセクション](../storage/storage-introduction.md#blob-storage)をご覧ください。 管理ディスクを使用する場合でも、管理ディスクに変換する前に、VHD のアップロード先のストレージ アカウントを作成する必要があります。

[az storage account create](/cli/azure/storage/account#create) を使用して、カスタム ディスクと VM 用に、ストレージ アカウントを作成します。 カスタム ディスクから作成する、非管理対象ディスクを備えた VM はすべて、そのイメージと同じストレージ アカウント内に存在する必要があります。 管理ディスクを備えた VM は、サブスクリプション内の任意のリソース グループで作成できます。

次の例では、`mystorageaccount` という名前のストレージ アカウントを以前に作成したリソース グループに作成します。

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>ストレージ アカウント キーの一覧表示
Azure では、ストレージ アカウントごとに 2 つの 512 ビット アクセス キーが生成されます。 これらのアクセス キーは、書き込み操作を実行する場合など、ストレージ アカウントを認証するときに使用します。 ストレージへのアクセス管理の詳細については [こちら](../storage/storage-create-storage-account.md#manage-your-storage-account)をご覧ください。 アクセス キーを表示するには、[az storage account keys list](/cli/azure/storage/account/keys#list) を使用します。

作成したストレージ アカウントのアクセス キーは次のようにして表示します。

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
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
以降の手順でストレージ アカウントとのやり取りに使用するため、 `key1` を書き留めます。

## <a name="create-a-storage-container"></a>ストレージ コンテナーを作成する
ローカル ファイル システムを論理的に整理するためにさまざまなディレクトリを作成するのと同様に、ストレージ アカウント内にコンテナーを作成してディスクを整理します。 ストレージ アカウントには、任意の数のコンテナーを含めることができます。 コンテナーを作成するには、[az storage container create](/cli/azure/storage/container#create) を使用します。

次の例では、前の手順で取得したアクセス キー (`key1`) を指定して、`mydisks` という名前のコンテナーを作成します。

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

## <a name="upload-vhd"></a>VHD をアップロードする
次に、[az storage blob upload](/cli/azure/storage/blob#upload) を使用してカスタム ディスクをアップロードします。 カスタム ディスクをアップロードし、ページ BLOB として保存します。

アクセス キー、前の手順で作成したコンテナー、ローカル コンピューター上のカスタム ディスクへのパスの順に指定します。

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-vm-from-custom-disk"></a>カスタム ディスクからの VM の作成
VM を作成するには、Azure Managed Disks または管理されていないディスクを使用します。 いずれのタイプでも、VM の作成時に、管理ディスクまたは管理されないディスクに URI を指定します。 管理されていないディスクの場合、宛先のストレージ アカウントが、カスタム ディスクが保存されているストレージ アカウントと必ず一致するようにします。 Azure 2.0 または Resource Manager JSON テンプレートを使用して VM を作成することができます。

### <a name="azure-cli-20---azure-managed-disks"></a>Azure CLI 2.0 - Azure Managed Disks
VHD から VM を作成するには最初に、[az disk create](/cli/azure/disk/create) を使用して VHD を管理ディスクに変換する必要があります。 次の例では、指定したストレージ アカウントとコンテナーにアップロードした VHD から `myManagedDisk` という名前の管理ディスクを作成します。

```azurecli
az disk create --resource-group myResourceGroup --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```

[az disk list](/cli/azure/disk/list) を使用して作成した管理ディスクの URI を取得します。

```azurecli
az disk list --resource-group myResourceGroup \
  --query '[].{Name:name,URI:creationData.sourceUri}' --output table
```

出力は次の例のようになります。

```azurecli
Name               URI
-----------------  ----------------------------------------------------------------------------------------------------
myUMDiskFromVHD    https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

次に、[az vm create](/cli/azure/vm#create) を使用して VM を作成し、管理ディスクの URI を指定します (`--image`)。 次の例では、アップロードされた VHD から作成した管理ディスクを使用して `myVM` という VM を作成します。

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --attach-os-disk https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

### <a name="azure-20---unmanaged-disks"></a>Azure 2.0 - 非管理対象ディスク
非管理対象ディスクで VM を作成するには、[az vm create](/cli/azure/vm#create) を使用してディスクに URI を指定します (`--image`)。 次の例では、以前にアップロードした仮想ディスクを使用して、`myVM` という名前の VM を作成します。

カスタム ディスクをポイントするには、[az vm create](/cli/azure/vm#create) を使用して `--image` パラメーターを指定します。 `--storage-account` は、カスタム ディスクが保存されているストレージ アカウントと必ず一致させます。 カスタム ディスクと同じコンテナーを VM の格納先として使用する必要はありません。 カスタム ディスクをアップロードする前に、前の手順と同じ方法で追加のコンテナーを必要なだけ作成しておいてください。

次の例では、`myVM` という名前の VM をカスタム ディスクから作成します。

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

また、**az vm create** コマンドで求められるその他のパラメーターすべて (ユーザー名、SSH キー) を指定するか、プロンプトで回答する必要もあります。


### <a name="resource-manager-template---unmanaged-disks"></a>Resource Manager テンプレート - 管理されていないディスク
Azure Resource Manager のテンプレートは、ビルドする環境を定義する JavaScript Object Notation (JSON) ファイルです。 このテンプレートは、コンピューティングやネットワークなどのさまざまなリソース プロバイダーに分類されます。 既存のテンプレートを使用することも独自に作成することもできます。 詳細については、 [Resource Manager とテンプレートの使用方法](../azure-resource-manager/resource-group-overview.md)に関する記事をご覧ください。

テンプレートの `Microsoft.Compute/virtualMachines` プロバイダー内に、VM の構成の詳細を格納する `storageProfile` ノードが作成されます。 編集が必要な 2 つの主なパラメーターが `image` URI と `vhd` URI です。これらでそれぞれカスタム ディスクと新しい VM の仮想ディスクをポイントします。 以下に、カスタム ディスクを使用する場合の JSON の例を示します。

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

[この既存のテンプレートを使用してカスタム イメージから VM を作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)するか、[独自の Azure Resource Manager テンプレートの作成](../resource-group-authoring-templates.md)に関するページを確認できます。 

テンプレートを構成したら、[az group deployment create](/cli/azure/group/deployment#create) を使用して VM を作成します。 `--template-uri` パラメーターを使用して JSON テンプレートの URI を指定します。

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

JSON ファイルをコンピューターにローカルでに保存している場合は、代わりに `--template-file` パラメーターを使用することができます。

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>次のステップ
カスタム仮想ディスクを準備してアップロードしたら、 [Resource Manager とテンプレートの使用](../azure-resource-manager/resource-group-overview.md)について学習しましょう。 必要であれば、新しい VM に [データ ディスクを追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) することもできます。 VM 上で実行するアプリケーションがあり、これにアクセスする必要がある場合は、必ず [ポートとエンドポイント](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を開放してください。


