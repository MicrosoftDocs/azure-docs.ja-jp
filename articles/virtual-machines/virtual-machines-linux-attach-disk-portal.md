---
title: "データ ディスクを Linux VM に接続する | Microsoft Docs"
description: "Azure ポータルでリソース マネージャー デプロイ モデルを使用して、新規または既存のデータ ディスクを Linux VM に接続する方法。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 9c36e1e463d5d40ba1152481705a5cee9af89460
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Azure ポータルで Linux VM にデータ ディスクを接続する方法
この記事では、Azure ポータルを使用して新しいディスクと既存のディスクの両方を Linux 仮想マシンに接続する方法について示します。 [Azure Portal で Windows VM にデータ ディスクを接続する](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)こともできます。 Azure Managed Disks または非管理対象ディスクのどちらを使用するかを選択できます。 Managed Disks は Azure プラットフォームによって処理されるため、ディスクを格納するための準備も場所も必要ありません。 非管理対象ディスクではストレージ アカウントが必要であり、いくつかの[クォータと制限が適用されます](../azure-subscription-service-limits.md#storage-limits)。 Azure Managed Disks の詳細については、「[Azure Managed Disks overview](../storage/storage-managed-disks-overview.md)」 (Azure Managed Disks の概要) をご覧ください。

VM にディスクを接続する前に、次のヒントを確認してください。

* 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
* Premium Storage を使用するには、DS シリーズまたは GS シリーズの仮想マシンが必要です。 これらの仮想マシンでは、Premium および Standard のどちらのディスクも使用できます。 Premium Storage は特定のリージョンで使用できます。 詳細については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
* 仮想マシンに接続されているディスクは、実際には Azure に保存されている .vhd ファイルです。 詳細については、 [仮想マシン用のディスクと VHD](../storage/storage-about-disks-and-vhds-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。


## <a name="find-the-virtual-machine"></a>仮想マシンの検索
1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. ハブ メニューで **[Virtual Machines]**をクリックします。
3. 一覧から仮想マシンを選択します。
4. [仮想マシン] ブレードにアクセスし、**[要点]** で、**[ディスク]** の順にクリックします。
   
    ![ディスク設定を開く](./media/virtual-machines-linux-attach-disk-portal/find-disk-settings.png)

次の手順に従って、[管理ディスク](#use-azure-managed-disks)または[非管理対象ディスク](#use-unmanaged-disks)のどちらかを接続します。

## <a name="use-azure-managed-disks"></a>Azure Managed Disks の使用

### <a name="attach-a-new-disk"></a>新しいディスクの接続

1. **[ディスク]** ブレードで、**[+ Add data disk]** (データ ディスクの追加) をクリックします。
2. **[名前]** のドロップダウン メニューをクリックして、**[ディスクの作成]** を選択します。

    ![Azure Managed Disks の作成](./media/virtual-machines-linux-attach-disk-portal/create-new-md.png)

3. 管理ディスクの名前を入力します。 既定の設定を確認し、必要に応じて更新して、**[作成]** をクリックします。
   
   ![ディスク設定を確認する](./media/virtual-machines-linux-attach-disk-portal/create-new-md-settings.png)

4. **[保存]** をクリックして管理ディスクを作成し、VM の構成を更新します。

   ![新しい Azure Managed Disk の保存](./media/virtual-machines-linux-attach-disk-portal/confirm-create-new-md.png)

5. Azure でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]**に新しいディスクが表示されます。 管理ディスクは最上位リソースであるため、リソース グループのルートに表示されます。

   ![リソース グループの Azure Managed Disk](./media/virtual-machines-linux-attach-disk-portal/view-md-resource-group.png)

### <a name="attach-an-existing-disk"></a>既存のディスクの接続
1. **[ディスク]** ブレードで、**[+ Add data disk]** (データ ディスクの追加) をクリックします。
2. **[名前]** のドロップダウン メニューをクリックして、Azure サブスクリプションにアクセスできる既存の管理ディスク一覧を確認します。 接続する管理ディスクを選択します。

   ![既存の Azure Managed Disk の接続](./media/virtual-machines-linux-attach-disk-portal/select-existing-md.png)

3. **[保存]** をクリックして既存の管理ディスクを接続し、VM の構成を更新します。
   
   ![Azure Managed Disk の更新の保存](./media/virtual-machines-linux-attach-disk-portal/confirm-attach-existing-md.png)

4. Azure で仮想マシンにディスクが接続されると、仮想マシンのディスク設定の **[データ ディスク]** にそのディスクが表示されます。

## <a name="use-unmanaged-disks"></a>非管理対象ディスクの使用

### <a name="attach-a-new-disk"></a>新しいディスクの接続

1. **[ディスク]** ブレードで、**[+ Add data disk]** (データ ディスクの追加) をクリックします。
2. 既定の設定を確認し、必要に応じて更新して、 **[OK]**をクリックします。
   
   ![ディスク設定を確認する](./media/virtual-machines-linux-attach-disk-portal/attach-new.png)
3. Azure でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]**に新しいディスクが表示されます。

### <a name="attach-an-existing-disk"></a>既存のディスクの接続
1. **[ディスク]** ブレードで、**[+ Add data disk]** (データ ディスクの追加) をクリックします。
2. **[既存のディスクの接続]** の **[VHD ファイル]** をクリックします。
   
   ![既存のディスクを接続する](./media/virtual-machines-linux-attach-disk-portal/attach-existing.png)
3. **[ストレージ アカウント]**で、アカウントと、.vhd ファイルが格納されているコンテナーを選択します。
   
   ![VHD の場所を検索する](./media/virtual-machines-linux-attach-disk-portal/find-storage-container.png)
4. .vhd ファイルを選択します。
5. **[既存のディスクの接続]** の **[VHD ファイル]** に、選択したファイルが表示されます。 **[OK]**をクリックします。
6. Azure で仮想マシンにディスクが接続されると、仮想マシンのディスク設定の **[データ ディスク]** にそのディスクが表示されます。


## <a name="next-steps"></a>次のステップ
ディスクを追加した後、使用できるように準備する必要があります。 詳細については、「[方法: Linux での新しいデータ ディスクの初期化](linux/classic/attach-disk.md#initialize-a-new-data-disk-in-linux)」を参照してください。

