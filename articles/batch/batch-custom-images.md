---
title: "カスタム イメージから Azure Batch プールをプロビジョニングする | Microsoft Docs"
description: "カスタム イメージから Batch プールを作成して、アプリケーションで必要なソフトウェアとデータを含むコンピューティング ノードをプロビジョニングできます。 カスタム イメージは、Batch ワークロードを実行するコンピューティング ノードを構成するための効率的な方法です。"
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.openlocfilehash: 3d655766b4f2a5efb0c8c29ffa81a89f84b3e17c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2017
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>カスタム イメージを使用して仮想マシンのプールを作成する

Azure Batch で仮想マシンのプールを作成する際は、プール内の各コンピューティング ノードのオペレーティング システムを提供する仮想マシン (VM) イメージを指定します。 仮想マシンのプールは、Azure Marketplace イメージを使用するか、自分で準備したカスタム VHD イメージを提供することで作成できます。 カスタム イメージを使用すると、各コンピューティング ノードのプロビジョニング時にオペレーティング システムをどのように構成するかを制御できます。 カスタム イメージには、コンピューティング ノードがプロビジョニングされたときにすぐに使用可能になるアプリケーションと参照データを含めることもできます。

カスタム イメージを使用すると、プールのコンピューティング ノードが Batch ワークロードを実行できるようになる時間を短縮できます。 Azure Marketplace イメージの使用と、プロビジョニング後の各コンピューティング ノードでのソフトウェアのインストールはいつでも実行できますが、この方法は、カスタム イメージの使用よりも効率が悪くなる場合があります。 

シナリオ用に構成されたカスタム イメージは、次のような操作を行う必要がある場合に使用します。

- **オペレーティング システム (OS) を構成する。**オペレーティング システムの特殊な構成をカスタム イメージで行うことができます。 
- **大規模なアプリケーションをインストールする。** プロビジョニングした各コンピューティング ノードにアプリケーションをインストールするよりも、カスタム イメージにアプリケーションをインストールする方が効率的です。
- **大量のデータをコピーする。** データを各コンピューティング ノードにコピーする代わりに、カスタム イメージにコピーすると、1 回のコピー操作で済むため、時間と帯域幅を節約できます。
- **セットアップ プロセス中に VM を再起動する。** 多数のコンピューティング ノードがある場合は特に、VM の再起動に時間がかかる場合があります。

## <a name="prerequisites"></a>前提条件

- **ユーザー サブスクリプション プール割り当てモードで作成された Batch アカウント** カスタム イメージを使用して仮想マシン プールをプロビジョニングするには、ユーザー サブスクリプション [プール割り当てモード](batch-api-basics.md#pool-allocation-mode)で Batch アカウントを作成します。 このモードでは、Batch プールはアカウントが存在するサブスクリプションに割り当てられます。 Batch アカウントを作成する際のプール割り当てモードの設定の詳細については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md)」の「[アカウント](batch-api-basics.md#account)」セクションを参照してください。

- **Azure ストレージ アカウント。** カスタム イメージを使用して仮想マシンのプールを作成するには、同じサブスクリプションおよびリージョンに標準の汎用 Azure Storage アカウントが必要です。 Azure VM からカスタム イメージを作成した場合は、そのイメージを VM の OS ディスクがあるストレージ アカウントにコピーします。別のストレージ アカウントを作成する必要はありません。 
    
## <a name="prepare-a-custom-image"></a>カスタム イメージを準備する

Batch で使用するカスタム イメージを準備するには、Linux または Windows の既存のインストールを汎用化する必要があります。 オペレーティング システムのインストールを汎用化すると、コンピュータに固有の情報が削除されます。 その結果、他のコンピューターまたは VM にインストールできるイメージになります。  

> [!IMPORTANT]
> Batch では、現在、Azure 管理対象イメージを使用したプールのプロビジョニングをサポートしていません。 プールのプロビジョニングに使用するカスタム イメージは、Azure Storage に格納する必要があります。 
>
> カスタム イメージを準備するときは、次の点に注意してください。
> - Batch プールのプロビジョニングに使用するベース OS イメージにカスタム スクリプト拡張機能などの Azure 拡張機能がプレインストールされていないことを確認します。 イメージにプレインストールされた拡張機能が含稀る場合、Azure で VM のデプロイ時に問題が発生する可能性があります。
> - Batch ノード エージェントでは現在、既定の一時ドライブが要求されるため、提供するベース OS イメージで既定の一時ドライブが使用されることを確認します。
>
>

既存の準備済みの Windows または Linux イメージなら、いずれもカスタム イメージとして使用できます。 たとえば、ローカルのイメージを使用する場合は、[AzCopy](../storage/storage-use-azcopy.md) または他のアップロード ツールを使用して、Batch アカウントと同じサブスクリプションおよびリージョンにある Azure Storage アカウントにイメージをアップロードします。

新規または既存の Azure VM からカスタム イメージを準備することもできます。 新しい VM を作成する場合は、Azure Marketplace イメージをカスタム イメージのベース イメージとして使用し、それをカスタマイズすることができます。 ベース イメージをカスタマイズするには、Azure VM を作成し、アプリケーションやデータを追加します。 その後、カスタム イメージとして使用できるように VM を汎用化し、Azure Storage に保存します。 

Azure VM からカスタム イメージを準備するには、次の手順に従います。

1. Azure Marketplace イメージから**管理対象外** Azure VM を作成します。 Azure Marketplace には、[Windows](../virtual-machines/windows/quick-create-portal.md) と [Linux](../virtual-machines/linux/quick-create-portal.md) の両方のイメージが含まれています。
    
    VM 作成プロセスの手順 3. で、**[ストレージ] の [Managed Disks を使用]** オプションで **[いいえ]** を選択していることを確認します。 また、VM の OS ディスクのストレージ アカウント名をメモしておいてください。このストレージ アカウントは、Azure によってカスタム イメージが保存される場所でもあります。

    ![管理対象外 VM を作成し、ストレージ アカウント名をメモする](media/batch-custom-images/vm-create-storage.png)
 
2. VM の作成プロセスを完了し、VM が Azure によって割り当てられるのを待ちます。 次の図は、Azure Portal に VM が実行中として表示されているようすを示しています。

    ![Marketplace イメージから VM を作成する](media/batch-custom-images/vm-status-running.png)

3. VM が実行状態になったら、RDP (Windows の場合) または SSH (Linux の場合) を使用して VM に接続します。 必要なソフトウェアをインストールするか、必要なデータをコピーしてから、VM を汎用化します。 「[VM の汎用化](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized.md#generalize-the-vm)」に記載されている手順に従って操作します。 
   
4. 手順に従って [Azure PowerShell にログイン](../virtual-machines/windows/sa-copy-generalized.md#log-in-to-azure-powershell)します。 Azure PowerShell のインストールについては、「[Azure PowerShell の概要](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.2.0)」を参照してください。 

5. 次に、手順に従って [VM の割り当てを解除して、汎用化状態に設定](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized#deallocate-the-vm-and-set-the-state-to-generalized)します。 

    Azure Portal で、VM の割り当てが解除されていることを確認します。

    ![VM の割り当てが解除されていることを確認する](media/batch-custom-images/vm-status-deallocated.png)

6.  [Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) PowerShell コマンドレットを使用して、VM イメージを作成して Azure Storage に保存します。 「[イメージの作成](../virtual-machines/windows/sa-copy-generalized.md#create-the-image)」に記載されている手順に従って操作します。
    
    VM イメージは、この説明の手順 1. に示すように、VM の作成時に作成された Azure Storage アカウントに保存されます。 Save-AzureRmVMImage コマンドレットを使用すると、イメージがそのストレージ アカウントの**システム** コンテナーに保存されます。 `-DestinationContainername` パラメーターは、**システム** コンテナー内の仮想ディレクトリを指定します。 `-VHDNamePrefix` パラメーターは、BLOB 名のプレフィックスを指定します。 このプレフィックスは、ハイフンと共に BLOB 名の先頭に付加されます。 

    たとえば、次のパラメーターを使用して Save-AzureRmVMImage を呼び出すとします。  

        Save-AzureRmVMImage -ResourceGroupName sample-resource-group -Name vm-custom-image -DestinationContainerName batchimages -VHDNamePrefix custom -Path C:\Temp\Images\vm-custom-image.json

    結果のイメージは、ここに表示されている位置と BLOB 名に保存されます。

    ![システム コンテナーに保存された VHD の場所](media/batch-custom-images/vhd-in-vm-storage-account.png)

    > [!NOTE]
    > Azure の管理対象外 VM では、さまざまな目的で複数のストレージ アカウントが作成されます。 VM の作成時に OS ディスクのストレージ コンテナーの名前を記録していない場合は、**システム** コンテナーを含む関連するストレージ アカウントを見つけてください。 **システム** コンテナー内を移動して、**Save-AzureRmVMImage** コマンドに指定した値が使用されているカスタム イメージを見つけます。

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>ポータルでカスタム イメージからプールを作成する

カスタム イメージを保存し、その場所を特定したら、そのイメージから Batch プールを作成できます。 次の手順に従って、Azure Portal でプールを作成します。

1. Azure Portal の Batch アカウントに移動します。 このアカウントは、カスタム イメージを含むストレージ アカウントと同じサブスクリプションおよびリージョン内にある必要があります。 
2. 左側の **[設定]** ウィンドウで、**[プール]** メニュー項目を選択します。
3. **[プール]** ウィンドウで、**[追加]** コマンドを選択します。
4. **[プールの追加]** ウィンドウで、**[イメージの種類]** ボックスの一覧の **[カスタム イメージ (Linux/Windows)]** を選択します。 ポータルに **[カスタム イメージ]** ピッカーが表示されます。 カスタム イメージが格納されているストレージ アカウントに移動し、コンテナーから目的の VHD を選択します。 
5. **[Publisher]\(パブリッシャー\)、[Offer]\(プラン\)、[SKU]** で、カスタム VHD に対して適切な値を選択します。
6. **[ノード サイズ]**、**[ターゲットの専用ノード数]**、**[低優先度ノード]** など、残りの必須の設定ほか、オプションの設定も必要に応じて指定します。

    たとえば、Microsoft Windows Server Datacenter 2016 のカスタム イメージの場合、**[プールの追加]** ウィンドウは次のように表示されます。

    ![カスタム Windows イメージからプールを追加する](media/batch-custom-images/add-pool-custom-image.png)
  
既存のプールがカスタム イメージに基づいているかどうかを調べるには、**[プール]** ウィンドウのリソースの概要セクションで **[オペレーティング システム]** プロパティを確認します。 プールがカスタム イメージから作成されている場合、値は **[カスタム VM イメージ]** に設定されます。

プールに関連付けられているすべてのカスタム VHD がプールの **[プロパティ]** ウィンドウに表示されます。
 
## <a name="next-steps"></a>次のステップ

- Batch の詳細な概要については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md)」を参照してください。
- Batch アカウントの作成の詳細については、「[Azure ポータルで Batch アカウントを作成する](batch-account-create-portal.md)」を参照してください。