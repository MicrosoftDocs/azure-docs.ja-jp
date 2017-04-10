---
title: "Azure CLI 1.0 を使用したカスタム Linux イメージのアップロード | Microsoft Docs"
description: "Resource Manager デプロイメント モデルと Azure CLI 1.0 を使用して、仮想ハード ディスク (VHD) とカスタム Linux イメージを作成し、Azure にアップロードします。"
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
ms.date: 10/10/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 97b5e4c3bda8d66297a43b4e430fec92d9588686
ms.lasthandoff: 03/21/2017


---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-10"></a>Azure CLI 1.0 を使用してカスタム ディスク イメージをアップロードし、Linux VM を作成する
この記事では、Resource Manager デプロイメント モデルを使用して仮想ハード ディスク (VHD) を Azure にアップロードし、そのカスタム イメージから Linux VM を作成する方法について説明します。 この機能によって、要件に合った Linux ディストリビューションをインストールして構成し、その VHD を使用して Azure 仮想マシン (VM) をすばやく作成することができます。


## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](#quick-commands) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイ モデル用の次世代 CLI


## <a name="quick-commands"></a>クイック コマンド
タスクをすばやく実行する必要がある場合のために、次のセクションでは、VM を Azure にアップロードするための基本的なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](#requirements)。

[Azure CLI 1.0](../cli-install-nodejs.md) でログインし、Resource Manager モードを使用していることを確認します。

```azurecli
azure config mode arm
```

次の例では、パラメーター名を独自の値を置き換えます。 パラメーター名の例には、`myResourceGroup`、`mystorageaccount`、および `myimages` が含まれています。

まず、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `WestUs` の場所に作成します。

```azurecli
azure group create myResourceGroup --location "WestUS"
```

仮想ディスクを保持するストレージ アカウントを作成します。 次の例では、`mystorageaccount` という名前のストレージ アカウントを作成します。

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

ストレージ アカウントのアクセス キーを一覧表示します。 `key1` を書き留めておきます。

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

取得したストレージ キーを使用してストレージ アカウント内にコンテナーを作成します。 次の例では、`key1` からのストレージ キーの値を使用して `myimages` という名前のコンテナーを作成します。

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

最後に、作成したコンテナーに VHD をアップロードします。 `/path/to/disk/mydisk.vhd` の下で VHD のローカル パスを指定します。

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

これで、 [Resource Manager テンプレートを使用](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)して、アップロードされた仮想ディスクから VM を作成できます。 次のように、ディスク (`--image-urn`) への URI を指定することによって、CLI を使用することもできます。 次の例では、以前にアップロードした仮想ディスクを使用して、`myVM` という名前の VM を作成します。

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

目標のストレージ アカウントは、仮想ディスクのアップロード先と同じである必要があります。 また、 `azure vm create` コマンドで求められるその他のパラメーターすべて (仮想ネットワーク、パブリック IP アドレス、ユーザー名、SSH キー) を指定するか、プロンプトで回答する必要もあります。 詳細については、 [Resource Manager の CLI から利用できるパラメーター](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)に関するトピックをご覧ください。

## <a name="requirements"></a>必要条件
次の手順を完了するには、以下が必要です。

* **.vhd ファイルにインストールされている Linux オペレーティング システム** - [動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (または[動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を参照してください) を VHD 形式で仮想ディスクにインストールします。 VM と VHD を作成するツールはいくつかあります。
  * [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) または [KVM](http://www.linux-kvm.org/page/RunningKVM) をインストールして構成します。その際、イメージ形式として VHD を使用します。 必要であれば `qemu-img convert` を使用して[イメージを変換](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)できます。
  * [Windows 10 上](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install)または [Windows Server 2012/2012 R2 上](https://technet.microsoft.com/library/hh846766.aspx)の Hyper-V を使用することもできます。

> [!NOTE]
> 新しい VHDX 形式は、Azure ではサポートされていません。 VM を作成するときに、形式として VHD を指定します。 必要であれば、[`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) または [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) の PowerShell コマンドレットを使用して VHDX ディスクを VHD に変換できます。 Azure では動的 VHD のアップロードはサポートしないため、そのようなディスクは、アップロードする前に静的 VHD に変換する必要があります。 Azure へのアップロード プロセス中、動的ディスクの変換には、 [Azure VHD Utilities for GO (GO 用の Azure VHD Utilities)](https://github.com/Microsoft/azure-vhd-utils-for-go) などのツールを使用できます。
> 
> 

* カスタム イメージから作成する VM は、イメージ自体と同じストレージ アカウント内に存在する必要があります。
  * ストレージ アカウントと、カスタム イメージおよび作成した VM の両方を保持するコンテナーを作成します。
  * VM をすべて作成したら、イメージを安全に削除することができます。

[Azure CLI 1.0](../cli-install-nodejs.md) でログインし、Resource Manager モードを使用していることを確認します。

```azurecli
azure config mode arm
```

次の例では、パラメーター名を独自の値を置き換えます。 パラメーター名の例には、`myResourceGroup`、`mystorageaccount`、および `myimages` が含まれています。

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>アップロードするイメージを準備する
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
> [Azure プラットフォームの SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) は、動作保証済みディストリビューションのいずれか&1; つを、「[Azure での動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」の "サポートされているバージョン" で指定されている構成で使用した場合にのみ、Linux を実行する VM に適用されます。


## <a name="create-a-resource-group"></a>リソース グループの作成
リソース グループは、仮想ネットワークやストレージ記憶域など、仮想マシンをサポートするすべての Azure リソースを論理的にまとめます。 Azure リソース グループの詳細については [こちら](../azure-resource-manager/resource-group-overview.md)をご覧ください。 カスタム ディスク イメージをアップロードして VM を作成する前に、まずリソース グループを作成する必要があります。 

次の例では、`myResourceGroup` という名前のリソース グループを `WestUS` の場所に作成します。

```azurecli
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
VM は、ストレージ アカウント内にページ BLOB として保存されます。 Azure BLOB ストレージの詳細については [こちら](../storage/storage-introduction.md#blob-storage)をご覧ください。 カスタム ディスク イメージと VM 用に、ストレージ アカウントを作成します。 カスタム ディスク イメージから作成する VM は、必ずそのイメージと同じストレージ アカウント内に存在する必要があります。

次の例では、`mystorageaccount` という名前のストレージ アカウントを以前に作成したリソース グループに作成します。

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>ストレージ アカウント キーの一覧表示
Azure では、ストレージ アカウントごとに 2 つの 512 ビット アクセス キーが生成されます。 これらのアクセス キーは、書き込み操作を実行する場合など、ストレージ アカウントを認証するときに使用します。 ストレージへのアクセス管理の詳細については [こちら](../storage/storage-create-storage-account.md#manage-your-storage-account)をご覧ください。 `azure storage account keys list` コマンドを実行すると、アクセス キーを表示できます。

作成したストレージ アカウントのアクセス キーは次のようにして表示します。

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
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
ローカル ファイル システムを論理的に整理するためにさまざまなディレクトリを作成するのと同様に、ストレージ アカウント内にコンテナーを作成して仮想ディスクとイメージを整理します。 ストレージ アカウントには、任意の数のコンテナーを含めることができます。 

次の例では、前の手順で取得したアクセス キー (`key1`) を指定して、`myimages` という名前のコンテナーを作成します。

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>VHD をアップロードする
これで、カスタム ディスク イメージを実際にアップロードできるようになりました。 VM で使用するすべての仮想ディスクと同様に、カスタム ディスク イメージをアップロードし、ページ BLOB として保存します。

アクセス キー、前の手順で作成したコンテナー、ローカル コンピューター上のカスタム ディスク イメージへのパスの順に指定します。

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>カスタム イメージから VM を作成する
カスタム ディスク イメージから VM を作成する場合は、ディスク イメージの URI を指定します。 宛先のストレージ アカウントは、カスタム ディスク イメージが保存されているストレージ アカウントと必ず一致させます。 Azure CLI または Resource Manager JSON テンプレートを使用して VM を作成することができます。

### <a name="create-a-vm-using-the-azure-cli"></a>Azure CLI を使用して VM を作成する
`--image-urn` パラメーターを `azure vm create` コマンドで指定して、カスタム ディスク イメージをポイントします。 `--storage-account-name` は、カスタム ディスク イメージが保存されているストレージ アカウントと必ず一致させます。 カスタム ディスク イメージと同じコンテナーを VM の格納先として使用する必要はありません。 カスタム ディスク イメージをアップロードする前に、前の手順と同じ方法で追加のコンテナーを必要なだけ作成しておいてください。

次の例では、`myVM` という名前の VM をカスタム ディスク イメージから作成します。

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

また、 `azure vm create` コマンドで求められるその他のパラメーターすべて (仮想ネットワーク、パブリック IP アドレス、ユーザー名、SSH キー) を指定するか、プロンプトで回答する必要もあります。 詳細については、 [リソース マネージャーの CLI から利用できるパラメーター](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)に関するトピックをご覧ください。

### <a name="create-a-vm-using-a-json-template"></a>JSON テンプレートを使用して VM を作成する
Azure Resource Manager のテンプレートは、ビルドする環境を定義する JavaScript Object Notation (JSON) ファイルです。 このテンプレートは、コンピューティングやネットワークなどのさまざまなリソース プロバイダーに分類されます。 既存のテンプレートを使用することも独自に作成することもできます。 詳細については、 [Resource Manager とテンプレートの使用方法](../azure-resource-manager/resource-group-overview.md)に関する記事をご覧ください。

テンプレートの `Microsoft.Compute/virtualMachines` プロバイダー内に、VM の構成の詳細を格納する `storageProfile` ノードが作成されます。 編集が必要な&2; つの主なパラメーターが `image` URI と `vhd` URI です。これらでそれぞれカスタム ディスク イメージと新しい VM の仮想ディスクをポイントします。 以下に、カスタム ディスク イメージを使用する場合の JSON の例を示します。

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

[この既存のテンプレートを使用してカスタム イメージから VM を作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)するか、[独自の Azure Resource Manager テンプレートの作成](../resource-group-authoring-templates.md)に関するページを確認できます。 

テンプレートを構成したら、 `azure group deployment create` コマンドを使用して VM を作成します。 `--template-uri` パラメーターを使用して JSON テンプレートの URI を指定します。

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

JSON ファイルをコンピューターにローカルでに保存している場合は、代わりに `--template-file` パラメーターを使用することができます。

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>次のステップ
カスタム仮想ディスクを準備してアップロードしたら、 [Resource Manager とテンプレートの使用](../azure-resource-manager/resource-group-overview.md)について学習しましょう。 必要であれば、新しい VM に [データ ディスクを追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) することもできます。 VM 上で実行するアプリケーションがあり、これにアクセスする必要がある場合は、必ず [ポートとエンドポイント](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を開放してください。


