---
title: "テンプレートとして使用する Azure Linux VM をキャプチャする | Microsoft Docs"
description: "Azure Resource Manager デプロイメント モデルで作成された Linux ベースの Azure 仮想マシン (VM) のイメージをキャプチャおよび汎用化する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 4505bb5f572add13c21df06fc7997358eaae6352
ms.lasthandoff: 03/21/2017


---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Azure で実行されている Linux 仮想マシンをキャプチャする
Resource Manager デプロイメント モデルの Azure Linux 仮想マシン (VM) を汎用化してキャプチャするには、この記事の手順に従います。 VM を汎用化すると、個人アカウント情報が削除されて、VM はイメージとして使われるように準備されます。 その後、OS の汎用化された仮想ハード ディスク (VHD) イメージ、接続されたデータ ディスクの VHD、および新しい VM デプロイの [Resource Manager テンプレート](../azure-resource-manager/resource-group-overview.md)をキャプチャします。 この記事では、非管理対象ディスクを使用する VM のために、Azure CLI 1.0 で VM イメージをキャプチャする方法について詳しく説明します。 また、[Azure CLI 2.0 で Azure Managed Disks を使用して VM をキャプチャ](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)することもできます。 Managed Disks は Azure プラットフォームによって処理されるため、ディスクを格納するための準備も場所も必要ありません。 詳しくは、「[Azure Managed Disks overview](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」(Azure Managed Disks の概要) をご覧ください。 

イメージを使って VM を作成するには、新しい各 VM のネットワーク リソースを設定し、テンプレート (JavaScript Object Notation または JSON ファイル) を使って、キャプチャされた VHD イメージからデプロイします。 この方法では、Azure Marketplace のイメージを使うときと同じように、現在のソフトウェア構成の VM をレプリケートできます。

> [!TIP]
> バックアップまたはデバッグ用に設定した特別な状態の既存の Linux VM のコピーを作成したい場合は、「[Azure で実行されている Linux 仮想マシンのコピーを作成する](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 また、オンプレミスの VM から Linux VHD をアップロードしたい場合は、「[カスタム ディスク イメージをアップロードして Linux VM を作成する](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。  

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](#before-you-begin) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイ モデル用の次世代 CLI

## <a name="before-you-begin"></a>開始する前に
次の前提条件が満たされていることを確認します。

* **Resource Manager デプロイメント モデルで作成された Azure VM** - Linux VM を作成していない場合は、[ポータル](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[Azure CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、または [Resource Manager テンプレート](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使うことができます。 
  
    必要に応じて VM を構成します。 たとえば、[データ ディスクを追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)し、更新プログラムを適用し、アプリケーションをインストールします。 
* **Azure CLI** - [Azure CLI](../cli-install-nodejs.md) をローカル コンピューターにインストールします。

## <a name="step-1-remove-the-azure-linux-agent"></a>手順 1: Azure Linux エージェントを削除する
最初に、Linux VM で **deprovision** パラメーターを指定して **waagent** コマンドを実行します。 このコマンドは、ファイルとデータを削除して、VM を汎用化できるようにします。 詳しくは、「[Azure Linux エージェント ユーザー ガイド](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

1. SSH クライアントを使って Linux VM に接続します。
2. SSH のウィンドウで、次のコマンドを入力します。
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > このコマンドはイメージとしてキャプチャする VM に対して実行するだけとします。 これにより、イメージからすべての機密情報が削除されることや、イメージが再配布に適した状態になることが保証されるわけではありません。
 
3. 「 **y** 」と入力して続行します。 **-force** パラメーターを追加すると、この確認手順を省略できます。
4. コマンドが完了した後、「**exit**」と入力します。 SSH クライアントが閉じられます。

## <a name="step-2-capture-the-vm"></a>手順 2: VM をキャプチャする
Azure CLI を使って、VM を汎用化してキャプチャします。 次の例では、パラメーター名を独自の値を置き換えます。 たとえば、**myResourceGroup**、**myVnet**、**myVM**といったパラメーター名にします。

1. ローカル コンピューターから Azure CLI を開き、[Azure サブスクリプションにログイン](../xplat-cli-connect.md)します。 
2. リソース マネージャー モードになっていることを確認します。
   
    ```azurecli
    azure config mode arm
    ```
3. 次のコマンドを使って、既にプロビジョニング解除されている VM をシャットダウンします。
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. 次のコマンドを使用して VM を汎用化します。
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. **azure vm capture** コマンドを実行して、VM をキャプチャします。 次の例では、イメージ VHD は **MyVHDNamePrefix**で始まる名前でキャプチャされ、**-t** オプションはテンプレート **MyTemplate.json** のパスを指定します。 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > イメージ VHD ファイルは、既定では、元の VM が使用していたのと同じストレージ アカウントに作成されます。 *同じストレージ アカウント*を使って、イメージから作成するすべての新しい VM の VHD を格納します。 

6. キャプチャされたイメージの場所を探すには、テキスト エディターで JSON テンプレートを開きます。 **storageProfile** で、**system** コンテナー内にある **image** の **uri** を見つけます。 たとえば、OS ディスク イメージの URI は `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd` のようになります。

## <a name="step-3-create-a-vm-from-the-captured-image"></a>手順 3: キャプチャしたイメージから VM を作成する
ここで、イメージとテンプレートを使用して、Linux VM を作成します。 次の手順では、Azure CLI と、キャプチャした JSON ファイル テンプレートを使って、新しい仮想ネットワークに VM を作成する方法を説明します。

### <a name="create-network-resources"></a>ネットワーク リソースを作成する
テンプレートを使用するには、まず、新しい VM に対して仮想ネットワークと NIC をセットアップする必要があります。 これらのリソースのリソース グループは、VM イメージが格納されている場所に作成することをお勧めします。 次に示すようなコマンドに、リソースの名前と適切な Azure の場所 (次のコマンドでは "centralus") を指定して実行します。

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>NIC の ID を取得する
キャプチャ中に保存した JSON を使って、イメージから VM をデプロイするには、NIC の ID が必要です。 これを取得するには、次のコマンドを実行します。

```azurecli
azure network nic show myResourceGroup1 myNIC
```

出力に含まれる **ID** は、`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic` のようになります。

### <a name="create-a-vm"></a>VM を作成します
ここで、次のコマンドを実行して、キャプチャした VM イメージから VM を作成します。 **-f** パラメーターを使って、保存したテンプレート JSON ファイルへのパスを指定します。

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

コマンドの出力では、新しい VM 名、管理者のユーザー名とパスワード、および前に作成した NIC の ID を指定するように求められます。

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

次に示すのは、デプロイが成功した場合の表示の例です。

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>デプロイを検証する
ここで、作成した仮想マシンに SSH を使用して接続し、デプロイを検証し、新しい VM の使用を開始します。 SSH を介して接続するには、次のコマンドを実行して、作成済みの VM の IP アドレスを見つけます。

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

コマンドの出力には、パブリック IP アドレスが一覧されます。 既定では、ポート 22 で SSH を使用して Linux VM に接続します。

## <a name="create-additional-vms"></a>追加の VM を作成する
前のセクションで説明した手順を使って追加の VM をデプロイするには、キャプチャしたイメージとテンプレートを使います。 イメージから VM を作成する他のオプションとしては、クイックスタート テンプレートを使う方法や、**azure vm create** コマンドを実行する方法があります。

### <a name="use-the-captured-template"></a>キャプチャしたテンプレートを使う
キャプチャしたイメージとテンプレートを使うには、次のようにします (前のセクションで詳しく説明)。

* VM イメージが、VM の VHD をホストするのと同じストレージ アカウントにあることを確認します。
* テンプレート JSON ファイルをコピーし、新しい VM の VHD の OS ディスクに対する一意の名前を指定します。 たとえば、**storageProfile** の **vhd** の **uri** で、**osDisk** VHD の一意の名前 (例: `https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`) を指定します。
* 同じ仮想ネットワークまたは別の仮想ネットワークに NIC を作成します。
* 変更したテンプレート JSON ファイルを使って、仮想ネットワークをセットアップするリソース グループ内にデプロイメントを作成します。

### <a name="use-a-quickstart-template"></a>クイックスタート テンプレートを使用する
イメージから VM を作成するときにネットワークが自動的にセットアップされるようにする場合は、テンプレートでリソースを指定できます。 たとえば、GitHub の [101-vm-from-user-image テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)を参考にしてください。 このテンプレートでは、カスタム イメージおよび必要な仮想ネットワークと、パブリック IP アドレスと、NIC リソースから仮想マシンを作成します。 Azure Portalでのテンプレートの使用に関する詳細なチュートリアルについては、「 [Resource Manager テンプレートを使用してカスタム イメージから仮想マシンを作成する方法](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/)」を参照してください。

### <a name="use-the-azure-vm-create-command"></a>azure vm create コマンドを使用する
通常は、Resource Manager テンプレートを使ってイメージから VM を作成するのが最も簡単です。 ただし、**azure vm create** コマンドと **-Q** (**--image-urn**) パラメーターを組み合わせて使って、VM を "*強制的に*" 作成することができます。 この方法を使う場合、新しい VM の OS .vhd ファイルの場所を指定するために、**-d** (**--os-disk-vhd**) パラメーターも渡します。 このファイルは、イメージ VHD ファイルが格納されているストレージ アカウントの vhds コンテナー内にある必要があります。 このコマンドは、新しい VM の VHD を自動的に **vhds** コンテナーにコピーします。

イメージで **azure vm create** を実行する前に、次の手順を済ませておきます。

1. リソース グループを作成するか、デプロイ用に既存のリソース グループを特定します。
2. 新しい VM 用のパブリック IP アドレス リソースと NIC リソースを作成します。 CLI を使って仮想ネットワーク、パブリック IP アドレス、および NIC を作成する手順については、この記事で前述した説明を参照してください (**azure vm create** では NIC も作成できますが、仮想ネットワークとサブネットの追加パラメーターを渡す必要があります)。

次に、新しい OS VHD ファイルと既存のイメージの両方に URI を渡すコマンドを実行します。 この例では、米国東部リージョンにサイズ Standard_A1 の VM を作成しています。

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

追加のコマンド オプションについては、 `azure help vm create`を実行してください。

## <a name="next-steps"></a>次のステップ
CLI を使用して VM を管理するには、「 [Azure リソース マネージャー テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」に記載のタスクを参照してください。


