---
title: Azure CLI を使用したカスタム Linux ディスクのアップロード | Microsoft Docs
description: Resource Manager デプロイ モデルと Azure CLI を使用して、仮想ハード ディスク (VHD) を作成し、Azure にアップロードする
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 9614614782179f9160aebdc4deca88f067778060
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708363"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Azure CLI を使用してカスタム ディスクをアップロードし、Linux VM を作成する

この記事では、Azure CLI を使用して仮想ハード ディスク (VHD) を Azure Storage アカウントにアップロードし、そのカスタム ディスクから Linux VM を作成する方法について説明します。 この機能によって、要件に合った Linux ディストリビューションをインストールして構成し、その VHD を使用して Azure 仮想マシン (VM) をすばやく作成することができます。

このトピックでは、最後の VHD のストレージ アカウントを使っていますが、これらの手順は[マネージド ディスク](upload-vhd.md)を使って実行することもできます。 

## <a name="quick-commands"></a>クイック コマンド
タスクをすばやく実行する必要がある場合のために、次のセクションでは、VHD を Azure にアップロードするための基本的なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](#requirements)。

[Azure CLI](/cli/azure/install-az-cli2) の最新版がインストールされ、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインしていることを確認します。

次の例では、パラメーター名を独自の値を置き換えます。 パラメーター名の例には、`myResourceGroup`、`mystorageaccount`、および `mydisks` が含まれています。

最初に、[az group create](/cli/azure/group) を使用して、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `WestUs` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

[az storage account create](/cli/azure/storage/account) を使用して、仮想ディスクを保持するストレージ アカウントを作成します。 次の例では、`mystorageaccount` という名前のストレージ アカウントを作成します。

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

[az storage account keys list](/cli/azure/storage/account/keys) を使用して、ストレージ アカウントのアクセス キーの一覧を表示します。 `key1` を書き留めておきます。

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

[az storage container create](/cli/azure/storage/container) を使用して、取得したストレージ キーを使用してストレージ アカウント内にコンテナーを作成します。 次の例では、`key1` からのストレージ キーの値を使用して `mydisks` という名前のコンテナーを作成します。

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

最後に、[az storage blob upload](/cli/azure/storage/blob) を使用して、作成したコンテナーに VHD をアップロードします。 `/path/to/disk/mydisk.vhd` の下で VHD のローカル パスを指定します。

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

[az vm create](/cli/azure/vm) を使用してディスクに URI を指定します (`--image`)。 次の例では、以前にアップロードした仮想ディスクを使用して、`myVM` という名前の VM を作成します。

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

目標のストレージ アカウントは、仮想ディスクのアップロード先と同じである必要があります。 また、**az vm create** コマンドで求められるその他のパラメーターすべて (仮想ネットワーク、パブリック IP アドレス、ユーザー名、SSH キー) を指定するか、プロンプトで回答する必要もあります。 詳細については、 [Resource Manager の CLI から利用できるパラメーター](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)に関するトピックをご覧ください。

## <a name="requirements"></a>必要条件
次の手順を完了するには、以下が必要です。

* **.vhd ファイルにインストールされている Linux オペレーティング システム** - [動作保証済み Linux ディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (または[動作保証外のディストリビューションに関する情報](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を参照してください) を VHD 形式で仮想ディスクにインストールします。 VM と VHD を作成するツールはいくつかあります。
  * [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) または [KVM](https://www.linux-kvm.org/page/RunningKVM) をインストールして構成します。その際、イメージ形式として VHD を使用します。 必要であれば `qemu-img convert` を使用して[イメージを変換](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)できます。
  * [Windows 10 上](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install)または [Windows Server 2012/2012 R2 上](https://technet.microsoft.com/library/hh846766.aspx)の Hyper-V を使用することもできます。

> [!NOTE]
> 新しい VHDX 形式は、Azure ではサポートされていません。 VM を作成するときに、形式として VHD を指定します。 必要であれば、[`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) または [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) の PowerShell コマンドレットを使用して VHDX ディスクを VHD に変換できます。 Azure では動的 VHD のアップロードはサポートしないため、そのようなディスクは、アップロードする前に静的 VHD に変換する必要があります。 Azure へのアップロード プロセス中、動的ディスクの変換には、 [Azure VHD Utilities for GO (GO 用の Azure VHD Utilities)](https://github.com/Microsoft/azure-vhd-utils-for-go) などのツールを使用できます。
> 
> 

* カスタム ディスクから作成する VM は、ディスク自体と同じストレージ アカウント内に存在する必要があります。
  * ストレージ アカウントと、カスタム ディスクおよび作成した VM の両方を保持するコンテナーを作成します。
  * VM をすべて作成したら、ディスクを安全に削除することができます。

[Azure CLI](/cli/azure/install-az-cli2) の最新版がインストールされ、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインしていることを確認します。

次の例では、パラメーター名を独自の値を置き換えます。 パラメーター名の例には、`myResourceGroup`、`mystorageaccount`、および `mydisks` が含まれています。

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>アップロードするディスクを準備する
Azure は、さまざまな Linux ディストリビューションをサポートしています (「 [Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください)。 次の記事では、Azure でサポートされる以下のさまざまな Linux ディストリビューションを準備する方法について説明します。

* **[CentOS ベースのディストリビューション](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES と openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[その他 - 動作保証外のディストリビューション](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Azure で Linux イメージを準備する際のその他のヒントについては、 **[Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes)** をご覧ください。

> [!NOTE]
> [Azure プラットフォームの SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) は、動作保証済みディストリビューションのいずれか 1 つを、「[Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」の "サポートされているバージョン" で指定されている構成で使用した場合にのみ、Linux を実行する VM に適用されます。
> 
> 

## <a name="create-a-resource-group"></a>リソース グループの作成
リソース グループは、仮想ネットワークやストレージ記憶域など、仮想マシンをサポートするすべての Azure リソースを論理的にまとめます。 リソース グループの詳細については、[リソース グループの概要](../../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。 カスタム ディスクをアップロードして VM を作成する前に、まず [az group create](/cli/azure/group) を使用してリソース グループを作成する必要があります。

次の例では、`myResourceGroup` という名前のリソース グループを `westus` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[az storage account create](/cli/azure/storage/account) を使用して、カスタム ディスクと VM 用に、ストレージ アカウントを作成します。 カスタム ディスクから作成する、非管理対象ディスクを備えた VM はすべて、そのイメージと同じストレージ アカウント内に存在する必要があります。 

次の例では、`mystorageaccount` という名前のストレージ アカウントを以前に作成したリソース グループに作成します。

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>ストレージ アカウント キーの一覧表示
Azure では、ストレージ アカウントごとに 2 つの 512 ビット アクセス キーが生成されます。 これらのアクセス キーは、書き込み操作を実行する場合など、ストレージ アカウントを認証するときに使用します。 ストレージへのアクセス管理の詳細については [こちら](../../storage/common/storage-account-manage.md#access-keys)をご覧ください。 アクセス キーを表示するには、[az storage account keys list](/cli/azure/storage/account/keys) を使用します。

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
ローカル ファイル システムを論理的に整理するためにさまざまなディレクトリを作成するのと同様に、ストレージ アカウント内にコンテナーを作成してディスクを整理します。 ストレージ アカウントには、任意の数のコンテナーを含めることができます。 コンテナーを作成するには、[az storage container create](/cli/azure/storage/container) を使用します。

次の例では、`mydisks` という名前のコンテナーを作成します。

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>VHD をアップロードする
次に、[az storage blob upload](/cli/azure/storage/blob) を使用してカスタム ディスクをアップロードします。 カスタム ディスクをアップロードし、ページ BLOB として保存します。

アクセス キー、前の手順で作成したコンテナー、ローカル コンピューター上のカスタム ディスクへのパスの順に指定します。

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>VM の作成
非管理対象ディスクで VM を作成するには、[az vm create](/cli/azure/vm) を使用してディスクに URI を指定します (`--image`)。 次の例では、以前にアップロードした仮想ディスクを使用して、`myVM` という名前の VM を作成します。

カスタム ディスクをポイントするには、[az vm create](/cli/azure/vm) を使用して `--image` パラメーターを指定します。 `--storage-account` は、カスタム ディスクが保存されているストレージ アカウントと必ず一致させます。 カスタム ディスクと同じコンテナーを VM の格納先として使用する必要はありません。 カスタム ディスクをアップロードする前に、前の手順と同じ方法で追加のコンテナーを必要なだけ作成しておいてください。

次の例では、`myVM` という名前の VM をカスタム ディスクから作成します。

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

また、**az vm create** コマンドで求められるその他のパラメーターすべて (ユーザー名、SSH キー) を指定するか、プロンプトで回答する必要もあります。


## <a name="resource-manager-template"></a>Resource Manager テンプレート
Azure Resource Manager のテンプレートは、ビルドする環境を定義する JavaScript Object Notation (JSON) ファイルです。 このテンプレートは、コンピューティングやネットワークなどのさまざまなリソース プロバイダーに分類されます。 既存のテンプレートを使用することも独自に作成することもできます。 詳細については、 [Resource Manager とテンプレートの使用方法](../../azure-resource-manager/resource-group-overview.md)に関する記事をご覧ください。

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

[この既存のテンプレートを使用してカスタム イメージから VM を作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)するか、[独自の Azure Resource Manager テンプレートの作成](../../azure-resource-manager/resource-group-authoring-templates.md)に関するページを確認できます。 

テンプレートを構成したら、[az group deployment create](/cli/azure/group/deployment) を使用して VM を作成します。 `--template-uri` パラメーターを使用して JSON テンプレートの URI を指定します。

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

JSON ファイルをコンピューターにローカルでに保存している場合は、代わりに `--template-file` パラメーターを使用することができます。

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>次の手順
カスタム仮想ディスクを準備してアップロードしたら、 [Resource Manager とテンプレートの使用](../../azure-resource-manager/resource-group-overview.md)について学習しましょう。 必要であれば、新しい VM に [データ ディスクを追加](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) することもできます。 VM 上で実行するアプリケーションがあり、これにアクセスする必要がある場合は、必ず [ポートとエンドポイント](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を開放してください。

