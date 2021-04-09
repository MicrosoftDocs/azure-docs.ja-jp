---
title: Azure portal を使用して Azure Stack Edge Pro に VM をデプロイする方法
description: Azure portal を使用して Azure Stack Edge Pro に VM を作成して管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro device so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 6054e7e79acaa6abf304508221c63143b9d14a45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102436534"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Azure portal を使用して Azure Stack Edge Pro GPU デバイスに VM をデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure portal、テンプレート、Azure PowerShell コマンドレット、および Azure CLI/Python スクリプトを使用して、Azure Stack Edge デバイス上に仮想マシン (VM) を作成および管理できます。 この記事では、Azure portal を使用して、Azure Stack Edge デバイスに VM を作成し、それを管理する方法について説明します。 

この記事は、Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、および Azure Stack Edge Mini R デバイスに適用されます。 

> [!IMPORTANT] 
> クラウドからデバイスにデプロイされた VM を管理するユーザーに対して、多要素認証を有効にすることをお勧めします。
        
## <a name="vm-deployment-workflow"></a>VM デプロイのワークフロー

デプロイの大まかなワークフローは次のとおりです。

1. お使いの Azure Stack Edge デバイスでコンピューティング用のネットワーク インターフェイスを有効にします。 これにより、指定されたネットワーク インターフェイスに仮想スイッチが作成されます。
1. Azure portal からの仮想マシンのクラウド管理を有効にします。
1. Storage Explorer を使用して Azure Storage アカウントに VHD をアップロードします。 
1. アップロードした VHD を使用して、VHD をデバイスにダウンロードし、VHD から VM イメージを作成します。 
1. 前の手順で作成したリソースを使用します。
    1. 作成した VM イメージ。
    1. コンピューティングを有効にしたネットワーク インターフェイスに関連付けられている VSwitch。
    1. VSwitch に関連付けられたサブネット。

    次のリソースをインラインで作成または指定します。
    1. VM 名、サポートされている VM サイズ、VM のサインイン資格情報を選択します。 
    1. 新しいデータ ディスクを作成するか、既存のデータ ディスクを接続します。
    1. VM の静的 IP または動的 IP を構成します。 静的 IP を指定する場合は、コンピューティング用に有効化されているネットワーク インターフェイスのサブネット範囲内の空き IP アドレスから選択します。

    上のリソースを使用して、仮想マシンを作成します。


## <a name="prerequisites"></a>[前提条件]

Azure portal を使用してデバイスで VM の作成と管理を開始する前に、次のことを確認してください。

1. 次の説明に従って Azure Stack Edge Pro デバイスでネットワーク設定を完了していること。「[手順 1: Azure Stack Edge Pro デバイスを構成する](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device)」

    1. コンピューティング用のネットワーク インターフェイスを有効にしていること。 このネットワーク インターフェイスの IP を使用して、VM デプロイ用の仮想スイッチを作成します。 デバイスのローカル UI で、 **[Compute]\(コンピューティング\)** に移動します。 仮想スイッチの作成に使用するネットワーク インターフェイスを選択します。

        > [!IMPORTANT] 
        > コンピューティング用に構成できるポートは 1 つだけです。

    1. そのネットワーク インターフェイスでコンピューティングを有効にします。 そのネットワーク インターフェイスに対応する仮想スイッチが Azure Stack Edge Pro によって作成、管理されます。

1. 作成する仮想マシンの VM イメージを作成するために使用する Windows または Linux VHD にアクセスできること。

## <a name="deploy-a-vm"></a>VM をデプロイする

Azure Stack Edge デバイスに仮想マシンを作成するには、次の手順に従います。

### <a name="add-a-vm-image"></a>VM イメージを追加する

1. VHD を Azure Storage アカウントにアップロードします。 [Azure Storage Explorer を使用して VHD をアップロードする](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md)手順に従います。

1. Azure portal で、Azure Stack Edge デバイスの Azure Stack Edge リソースに移動します。 **[Edge コンピューティング] > [仮想マシン]** に移動します。

    ![VM イメージの追加 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. **[仮想マシン]** を選択して **[概要]** ページに移動します。 仮想マシンのクラウド管理を **有効** にします。
    ![VM イメージの追加 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. 最初の手順では、VM イメージを追加します。 前の手順で、既にストレージ アカウントに VHD をアップロードしています。 この VHD を使用して、VM イメージを作成します。

    **[イメージの追加]** を選択して、ストレージ アカウントから VHD をダウンロードしてデバイスに追加します。 VHD のサイズとダウンロードに使用できるインターネット帯域幅によっては、ダウンロード プロセスに数分かかります。 

    ![VM イメージの追加 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. **[イメージの追加]** ブレードで、次のパラメーターを入力します。 **[追加]** を選択します。


    |パラメーター  |説明  |
    |---------|---------|
    |ストレージ BLOB からダウンロード    |VHD をアップロードしたストレージ アカウントのストレージ BLOB の場所を参照します。         |
    |ダウンロード先    | 仮想マシンをデプロイしている現在のデバイスに自動的に設定されます。        |
    |イメージに名前を付けて保存      | ストレージ アカウントにアップロードした VHD から作成する VM イメージの名前。        |
    |OS の種類     |VM イメージの作成に使用する VHD のオペレーティング システムとして、Windows または Linux から選択します。         |
   

    ![VM イメージの追加 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. VHD がダウンロードされ、VM イメージが作成されます。 イメージの作成は、完了するまでに数分かかります。 VM イメージが正常に完了したことを知らせる通知が表示されます。

    ![VM イメージの追加 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. VM イメージが正常に作成されると、 **[イメージ]** ブレードのイメージの一覧に追加されます。
    ![VM イメージの追加 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    **[Deployments]\(デプロイ\)** ブレードが更新され、デプロイの状態が示されます。

    ![VM イメージの追加 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    新しく追加したイメージは、 **[概要]** ページにも表示されます。
    ![VM イメージの追加 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>VM を追加する

VM イメージを作成した後に VM を作成するには、次の手順に従います。

1. **[概要]** ページで、 **[仮想マシンの追加]** を選択します。

    ![VM の追加 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. **[基本]** タブで、次のパラメーターを入力します。


    |パラメーター |説明  |
    |---------|---------|
    |仮想マシン名     |         |
    |Image     | デバイスで使用可能な VM イメージから選択します。        |
    |サイズ     | [サポートされる VM のサイズ](azure-stack-edge-gpu-virtual-machine-sizes.md)から選択します。        |
    |ユーザー名     | 既定のユーザー名 *azureuser* を使用します。        |
    |認証の種類    | SSH 公開キーまたはユーザー定義のパスワードから選択します。       |
    |パスワード     | 仮想マシンにサインインするパスワードを入力します。 パスワードは 12 文字以上で、定義された[複雑さの要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。        |
    |[パスワードの確認入力]    | パスワードをもう一度入力します。        |


    ![VM の追加 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    **[Next:ディスク]** を選択します。

1. **[ディスク]** タブで、VM にディスクを接続します。 
    
    1. **[新しいディスクを作成し接続する]** または **[既存のディスクの接続]** を選択できます。

        ![VM の追加 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. **[新しいディスクを作成し接続する]** を選択します。 **[新しいディスクの作成]** ブレードで、ディスクの名前と GiB 単位のサイズを指定します。

        ![VM の追加 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  上記の手順を繰り返して、ディスクを追加します。 ディスクが作成されると、 **[ディスク]** タブに表示されます。

        ![VM の追加 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-3.png)

        **[次へ: ネットワーク]** を選択します。

1. **[ネットワーク]** タブで、VM のネットワーク接続を構成します。

    
    |パラメーター  |説明 |
    |---------|---------|
    |仮想ネットワーク    | ドロップダウン リストから、ネットワーク インターフェイス上でコンピューティングを有効にしたときに Azure Stack Edge デバイスに作成された仮想スイッチを選択します。    |
    |サブネット     | このフィールドには、コンピューティングを有効にしたネットワーク インターフェイスに関連付けられているサブネットが自動的に入力されます。         |
    |IP アドレス     | VM の静的または動的な IP を指定します。 静的 IP は、指定されたサブネットの範囲内の使用可能な空き IP アドレスである必要があります。        |

    ![VM の追加 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    **確認と作成** を選択します。

1. **[確認と作成]** タブで、VM の仕様を確認し、 **[作成]** を選択します。

    ![VM の追加 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. VM の作成が開始され、最大 20 分かかることがあります。 **[Deployments]\(デプロイ\)** にアクセスして、VM の作成を監視することができます。

    ![VM の追加 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. VM が正常に作成されると、 **[概要]** ページが更新されて新しい VM が表示されます。

    ![VM の追加 9](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. 新しく作成された VM を選択し、 **[仮想マシン]** にアクセスします。

    ![VM の追加 10](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    VM を選択すると、詳細が表示されます。 

    ![VM の追加 11](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>VM への接続

Windows と Linux のどちらの VM を作成したかによって、接続する手順が異なる場合があります。 Azure portal を経由して、デバイスにデプロイされている VM に接続することはできません。 Linux または Windows VM に接続するには、次の手順を実行する必要があります。

### <a name="connect-to-linux-vm"></a>Linux VM への接続

Linux VM に接続するには、これらの手順に従います。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Windows VM への接続

Windows VM に接続するには、これらの手順に従います。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>次の手順

Azure Stack Edge Pro デバイスを管理する方法については、[ローカル Web UI を使用して Azure Stack Edge Pro を管理する](azure-stack-edge-manage-access-power-connectivity-mode.md)方法に関する記事を参照してください。
