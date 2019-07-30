---
title: Azure CLI を使用し、Azure で Linux VM のイメージをキャプチャする | Microsoft Docs
description: Azure CLI を使用し、Azure VM のイメージをキャプチャし、一括デプロイに利用します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 96169f8f52ea9d45d8804a7d4fc08827a4f1ea03
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668409"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>仮想マシンまたは VHD のイメージを作成する方法

<!-- generalize, image - extended version of the tutorial-->

仮想マシン (VM) のコピーを複数作成し、Azure で使用するには、VM または OS VHD のイメージをキャプチャします。 デプロイ用のイメージを作成するには、個人のアカウント情報を削除する必要があります。 次の手順で既存の VM のプロビジョニングと割り当てを解除し、イメージを作成します。 このイメージを使用して、サブスクリプション内の任意のリソース グループに VM を作成できます。

バックアップやデバッグのために既存の Linux VM のコピーを作成する方法、またはオンプレミス VM から特別な Linux VHD をアップロードする方法については、「[カスタム ディスク イメージをアップロードして Linux VM を作成する](upload-vhd.md)」を参照してください。  

**Azure VM Image Builder (パブリック プレビュー)** サービスを使用して、ご自身のカスタム イメージを作成することができます。ツールの学習やビルド パイプラインのセットアップは必要なく、イメージ構成を用意するだけで、Image Builder によってイメージが作成されます。 詳細については、[Azure VM Image Builder の概要](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview)に関する記事を参照してください。

さらに、**Packer** を使用してカスタム構成を作成することもできます。 詳細については、「[Packer を使用して Azure に Linux 仮想マシンのイメージを作成する方法](build-image-with-packer.md)」を参照してください。

イメージを作成する前に、次の項目が必要です。

* マネージド ディスクを使用する Resource Manager デプロイ モデルで作成された Azure VM。 Linux VM を作成していない場合は、[ポータル](quick-create-portal.md)、[Azure CLI](quick-create-cli.md)、または [Resource Manager](create-ssh-secured-vm-from-template.md) テンプレートを使うことができます。 必要に応じて VM を構成します。 たとえば、[データ ディスクを追加](add-disk.md)し、更新プログラムを適用し、アプリケーションをインストールします。 

* 最新の [Azure CLI](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/reference-index#az-login) を使用して Azure アカウントにログインしています。

## <a name="quick-commands"></a>クイック コマンド

この記事には簡易版があります。Azure VM の概要、テスト、評価については、「[CLI を使用した Azure VM のカスタム イメージの作成](tutorial-custom-images.md)」でも説明しています。


## <a name="step-1-deprovision-the-vm"></a>手順 1:VM のプロビジョニングを解除する
まず Azure VM エージェントで VM のプロビジョニングを解除し、マシン固有のファイルとデータを削除します。 ソース Linux VM で `-deprovision+user` パラメーターを指定して `waagent` コマンドを実行します。 詳細については、「[Azure Linux エージェント ユーザー ガイド](../extensions/agent-linux.md)」をご覧ください。

1. SSH クライアントを使って Linux VM に接続します。
2. SSH のウィンドウで、次のコマンドを入力します。
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > このコマンドはイメージとしてキャプチャする VM に対して実行します。 このコマンドでは、イメージからすべての機密情報が削除されることや、イメージが再配布に適した状態になることが保証されるわけではありません。 `+user` パラメーターにより、前回プロビジョニングされたユーザー アカウントも削除されます。 ユーザー アカウントの資格情報を VM に保持するには、`-deprovision` のみを使用します。
 
3. 「**y**」と入力して続行します。 `-force` パラメーターを追加すると、この確認手順を省略できます。
4. コマンドが完了したら、**exit** を入力して SSH クライアントを閉じます。

## <a name="step-2-create-vm-image"></a>手順 2:VM イメージを作成する
Azure CLI を使用し、一般化されたものとして VM を設定し、イメージをキャプチャします。 次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myVnet*、*myVM*といったパラメーター名にします。

1. [az vm deallocate](/cli/azure/vm) で、プロビジョニングを解除した VM の割り当てを解除します。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という VM の割り当てを解除します。
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. [az vm generalize](/cli/azure/vm) を使用して VM の状態を一般化に設定します。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という VM を一般化として設定します。
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. [az image create](/cli/azure/image#az-image-create) で VM リソースのイメージを作成します。 次の例では、*myVM* という名前の VM リソースを使用して *myResourceGroup* という名前のリソース グループに含まれる *myImage* という名前のイメージを作成します。
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > このイメージは、ソース VM と同じリソース グループに作成されます。 このイメージから、サブスクリプション内の任意のリソース グループに VM を作成できます。 管理の観点から、VM のリソースとイメージに専用のリソース グループを作成することをお勧めします。
   >
   > イメージをゾーン回復性のあるストレージに格納する場合は、[可用性ゾーン](../../availability-zones/az-overview.md)をサポートするリージョンにストレージを作成し、`--zone-resilient true` パラメーターを含める必要があります。

## <a name="step-3-create-a-vm-from-the-captured-image"></a>手順 3:キャプチャしたイメージから VM を作成する
[az vm create](/cli/azure/vm) で、作成したイメージを使用して VM を作成します。 次の例では、*myImage* という名前のイメージから *myVMDeployed* という名前の VM を作成します。

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>別のリソース グループで VM を作成する 

サブスクリプション内の任意のリソース グループでイメージから VM を作成できます。 イメージとは別のリソース グループに VM を作成するには、イメージに対する完全なリソース ID を指定します。 [az image list](/cli/azure/image#az-image-list) を実行すると、イメージの一覧が表示されます。 出力は次の例のようになります。

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

次の例では、[az vm create](/cli/azure/vm#az-vm-create) を実行し、イメージ リソース ID を指定してソース イメージとは別のリソース グループに VM を作成します。

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>手順 4:デプロイを検証する

作成した仮想マシンに SSH を使用して接続し、デプロイを検証し、新しい VM の使用を開始します。 SSH を介して接続するには、[az vm show](/cli/azure/vm#az-vm-show) を実行して、VM の IP アドレスか FQDN を見つけます。

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>次の手順
ソース VM イメージから複数の VM を作成できます。 イメージを変更するには: 

- イメージから VM を作成します。
- 必要な更新または構成の変更を行います。
- 再度本記事の手順に従って、イメージのプロビジョニングの解除、割り当ての解除、一般化、作成を行います。
- 今後のデプロイには、この新しいイメージを使用します。 元のイメージを削除することもできます。

CLI を使用して VM を管理する方法の詳細については、[Azure CLI](/cli/azure) に関するページをご覧ください。
