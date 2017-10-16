---
title: "データ ディスクを Linux VM に接続する | Microsoft Docs"
description: "ポータルを利用し、新しいデータ ディスクまたは既存のデータ ディスクを Linux VM に接続します。"
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
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 787f729732accd74c212b3be9520af50a2f04261
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>ポータルを利用し、データ ディスクを Linux VM に接続する 
この記事では、Azure ポータルを使用して新しいディスクと既存のディスクの両方を Linux 仮想マシンに接続する方法について示します。 [Azure Portal で Windows VM にデータ ディスクを接続する](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)こともできます。 

VM にディスクを接続する前に、次のヒントを確認してください。

* 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
* Premium Storage を使用するには、DS シリーズまたは GS シリーズの仮想マシンが必要です。 これらの仮想マシンでは、Premium および Standard のどちらのディスクも使用できます。 Premium Storage は特定のリージョンで使用できます。 詳細については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../../storage/common/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
* 仮想マシンに接続されているディスクは、実際には Azure に保存されている .vhd ファイルです。 詳細については、 [仮想マシン用のディスクと VHD](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。


## <a name="find-the-virtual-machine"></a>仮想マシンの検索
1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. 左側のメニューで **[仮想マシン]** をクリックします。
3. 一覧から仮想マシンを選択します。
4. [仮想マシン] ページにアクセスし、**[要点]** にある **[ディスク]** をクリックします。
   
    ![ディスク設定を開く](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>新しいディスクの接続

1. **[ディスク]** ウィンドウで、**[+ データ ディスクの追加]** をクリックします。
2. **[名前]** のドロップダウン メニューをクリックして、**[ディスクの作成]** を選択します。

    ![Azure Managed Disks の作成](./media/attach-disk-portal/create-new-md.png)

3. 管理ディスクの名前を入力します。 既定の設定を確認し、必要に応じて更新して、**[作成]** をクリックします。
   
   ![ディスク設定を確認する](./media/attach-disk-portal/create-new-md-settings.png)

4. **[保存]** をクリックして管理ディスクを作成し、VM の構成を更新します。

   ![新しい Azure Managed Disk の保存](./media/attach-disk-portal/confirm-create-new-md.png)

5. Azure でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]**に新しいディスクが表示されます。 管理ディスクは最上位リソースであるため、リソース グループのルートに表示されます。

   ![リソース グループの Azure Managed Disk](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>既存のディスクの接続
1. **[ディスク]** ウィンドウで、**[+ データ ディスクの追加]** をクリックします。
2. **[名前]** のドロップダウン メニューをクリックして、Azure サブスクリプションにアクセスできる既存の管理ディスク一覧を確認します。 接続する管理ディスクを選択します。

   ![既存の Azure Managed Disk の接続](./media/attach-disk-portal/select-existing-md.png)

3. **[保存]** をクリックして既存の管理ディスクを接続し、VM の構成を更新します。
   
   ![Azure Managed Disk の更新の保存](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Azure で仮想マシンにディスクが接続されると、仮想マシンのディスク設定の **[データ ディスク]** にそのディスクが表示されます。



## <a name="next-steps"></a>次のステップ
Azure CLI を利用し、[データ ディスクを接続する](add-disk.md)こともできます。
