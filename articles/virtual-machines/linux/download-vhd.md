---
title: Azure から Linux VHD をダウンロードする | Microsoft Docs
description: Azure CLI と Azure Portal を使用して Linux VHD をダウンロードします。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: f72d49a3ab204ce64eb89d0f05630b640c138e0a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329253"
---
# <a name="download-a-linux-vhd-from-azure"></a>Azure から Linux VHD をダウンロードする

この記事では、Azure CLI と Azure portal を使用して、Azure から Linux 仮想ハード ディスク (VHD) ファイルをダウンロードする方法を説明します。 

[Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) をまだインストールしていない場合はインストールします。

## <a name="stop-the-vm"></a>VM を停止する

VHD は、実行中の VM に接続されている場合、Azure からダウンロードできません。 VHD をダウンロードするには、VM を停止する必要があります。 VHD を[イメージ](tutorial-custom-images.md)として使用して、新しいディスクで他の VM を作成する場合は、ファイルに含まれているオペレーティング システムをプロビジョニング解除して一般化し、VM を停止する必要があります。 VHD を既存の VM の新しいインスタンス用のディスクまたはデータ ディスクとして使用する場合は、実行する必要がある操作は VM の停止と割り当て解除だけです。

VHD をイメージとして使用して他の VM を作成するには、次の手順を実行します。

1. SSH、アカウント名、および VM のパブリック IP アドレスを使用して VM に接続し、プロビジョニング解除します。 パブリック IP アドレスは、[az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show) で検索できます。 +user パラメーターにより、前回プロビジョニングされたユーザー アカウントも削除されます。 アカウントの資格情報を VM に組み込む場合は、この +user パラメーターを指定しないでください。 次の例では、前回プロビジョニングされたユーザー アカウントを削除します。

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. [az login](https://docs.microsoft.com/cli/azure/reference-index) で Azure アカウントにサインインします。
3. VM を停止し、割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. VM を一般化します。 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

VHD を既存の VM の新しいインスタンス用のディスクまたはデータ ディスクとして使用するには、次の手順を実行します。

1.  [Azure Portal](https://portal.azure.com/) にサインインします。
2.  ハブ メニューで **[Virtual Machines]** をクリックします。
3.  一覧から VM を選択します。
4.  VM のブレードで、**[停止]** をクリックします。

    ![VM の停止](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS URL の生成

VHD ファイルをダウンロードするには、[Shared Access Signature (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL を生成する必要があります。 URL が生成されると、その URL に有効期限が割り当てられます。

1.  VM のブレードのメニューで、**[ディスク]** をクリックします。
2.  VM のオペレーティング システム ディスクを選択し、**[エクスポート]** をクリックします。
3.  **[URL の生成]** をクリックします。

    ![URL の生成](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>VHD のダウンロード

1.  生成された URL の下にある [VHD ファイルのダウンロード] をクリックします。

    ![VHD のダウンロード](./media/download-vhd/export-download.png)

2.  ブラウザーで **[保存]** をクリックしないと、ダウンロードが開始されない場合があります。 VHD ファイルの既定の名前は *abcd* です。

    ![ブラウザーの [保存] のクリック](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>次の手順

- [Azure CLI を使用してカスタム ディスクをアップロードし、Linux VM を作成する](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)方法を学習します。 
- [Azure CLI を使用した Azure ディスクの管理](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

