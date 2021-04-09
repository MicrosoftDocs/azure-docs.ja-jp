---
title: OpenBSD イメージの作成とアップロード
description: OpenBSD オペレーティング システムを格納した仮想ハード ディスク (VHD) を作成およびアップロードして、Azure CLI で Azure 仮想マシンを作成する方法について説明します
author: gbowerman
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 05/24/2017
ms.author: guybo
ms.openlocfilehash: 3437fcd2d1d521aad237ecf6d3f7937b4835d376
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554600"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>OpenBSD ディスクイメージの作成と Azure へのアップロード
この記事では、OpenBSD オペレーティング システムを格納した仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。 アップロードした VHD を独自のイメージとして使用し、Azure CLI で Azure の仮想マシン (VM) を作成することができます。


## <a name="prerequisites"></a>前提条件
この記事では、次の項目があることを前提としています。

* **Azure サブスクリプション**- アカウントをお持ちでない場合でも、数分でアカウントを作成できます。 MSDN サブスクリプションをお持ちの場合は、「[Visual Studio サブスクライバー向けの月単位の Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」をご覧ください。 それ以外の場合は、 [無料試用版のアカウントの作成](https://azure.microsoft.com/pricing/free-trial/)方法に関するページをご覧ください。  
* **Azure CLI** - [Azure CLI](/cli/azure/install-azure-cli) の最新版がインストールされ、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインしていることを確認します。
* **.vhd ファイルにインストールされている OpenBSD オペレーティング システム** - サポートされている OpenBSD オペレーティング システム ([6.6 バージョン AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)) を仮想ハード ディスクにインストールしておきます。 .vhd ファイルを作成するツールはいくつかあります。 たとえば Hyper-V などの仮想化ソリューションを使用して .vhd ファイルを作成し、オペレーティング システムをインストールすることができます。 Hyper-V をインストールして使用する手順については、「 [Hyper-V をインストールして仮想マシンを作成する](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))」を参照してください。


## <a name="prepare-openbsd-image-for-azure"></a>Azure の OpenBSD イメージを準備する
Hyper-V のサポートが追加された OpenBSD オペレーティング システム 6.1 をインストールしたVMで、次の手順を実施します。

1. インストール中に DHCP が有効でない場合は、次のようにサービスを有効にします。

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. シリアル コンソールを次のようにセットアップします。

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. パッケージのインストールを次のように構成します。

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. 既定では、`root` ユーザーは Azure 上の仮想マシンで無効になっています。 ユーザーはOpenBSD VM で `doas` コマンドを使用して、昇格された特権でコマンドを実行できます。 doas は、既定では有効になっています。 詳細については、[doas.conf](https://man.openbsd.org/doas.conf.5) をご覧ください。 

5. 次のように Azure エージェントの前提条件をインストールし、構成します。

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Azure エージェントの最新版は常に [GitHub](https://github.com/Azure/WALinuxAgent/releases) にあります。 次のように、エージェントをインストールします。

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Azure エージェントをインストール後、次のように実行されていることを確認するようお勧めします。
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. システムのプロビジョニングを解除してクリーンアップし、再プロビジョニングに適した状態にします。 以下のコマンドは前回プロビジョニングされたユーザー アカウントおよび関連付けられたデータも削除します。

    ```sh
    waagent -deprovision+user -force
    ```

これで VM をシャットダウンできます。


## <a name="prepare-the-vhd"></a>VHD の準備
VHDX 形式は Azure ではサポートされていません。サポートされるのは **固定 VHD** のみです。 Hyper-V マネージャーまたは PowerShellの [convert-vhd](/powershell/module/hyper-v/convert-vhd) コマンドレットを使用して、ディスクを固定 VHD 形式に変換できます。 次が例となります。

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>ストレージ リソースの作成とアップロード
最初に、[az group create](/cli/azure/group) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

VHS をアップロードするには、ストレージ アカウントを、[az storage account create](/cli/azure/storage/account) を使用して作成します。 ストレージ アカウント名は一意である必要があるため、独自の名前を入力してください。 次の例では、*mystorageaccount* という名前のストレージ アカウントを作成します。

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

ストレージ アカウントへのアクセスを制御するには、次のように [az storage account keys list](/cli/azure/storage/account/keys) を使用してストレージ キーを取得します。

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

アップロードする VHD を論理的に分離するには、[az storage container create](/cli/azure/storage/container) を使用してストレージ アカウント内のコンテナーを作成します。

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

最後に、次のように [az storage blob upload](/cli/azure/storage/blob) を使用して VHD をアップロードします。

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>VHD から VM を作成
[サンプル スクリプト](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-vhd)を使用して、または直接 [az vm create](/cli/azure/vm) を使用して、VM を作成できます。 アップロードした OpenBSD VHD を指定するには、次のように `--image` パラメーターを使用します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

次のように [az vm list-ip-addresses](/cli/azure/vm) を使用して OpenBSD VM の IP アドレスを取得します。

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

これで正常に OpenBSD VM に SSH できます。
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>次のステップ
OpenBSD 6.1 の Hyper-V の対応に関して詳細をお知りになりたい場合は、[OpenBSD 6.1](https://www.openbsd.org/61.html) および [hyperv.4](https://man.openbsd.org/hyperv.4) をお読みください。

マネージド ディスクから VM を作成する場合は、[az disk](/cli/azure/disk) をお読みください。