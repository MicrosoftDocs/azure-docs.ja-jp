---
title: "Microsoft Azure Linux VM 用のディスクと VHD について | Microsoft Docs"
description: "Azure での Linux 仮想マシン用のディスクと VHD の基本について説明します。"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 7be8dd52-98f7-4187-9b78-55197915bc9b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 94ed8a0a1e47f6cb05095f8fe192a9da2a42fc2d
ms.lasthandoff: 03/27/2017


---
# <a name="about-disks-and-vhds-for-azure-linux-vms"></a>Azure Linux VM 用のディスクと VHD について
Azure の仮想マシンでは、その他のコンピューターとまったく同じように、オペレーティング システム、アプリケーション、およびデータを格納するための場所としてディスクを使用します。 Azure のすべての仮想マシンには、Linux オペレーティング システム ディスクと一時ディスクの少なくとも 2 つのディスクがあります。 オペレーティング システム ディスクはイメージから作成されます。オペレーティング システム ディスクとイメージの両方は実際に仮想ハード ディスク (VHD) であり、Azure のストレージ アカウントに格納されます。 仮想マシンでは 1 つ以上のデータ ディスクも保持することができ、これらも VHD として格納されます。 

この記事では、ディスクのさまざまな仕様法王について説明し、作成および使用できるディスクの種類を示します。 この記事は、 [Windows 仮想マシン](storage-about-disks-and-vhds-windows.md)にも利用できます。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>VM で使用されるディスク

ディスクは VM でどのように使用されるのでしょうか。

## <a name="operating-system-disk"></a>オペレーティング システム ディスク
どの仮想マシンにも 1 つのオペレーティング システム ディスクが取り付けられています。 これは SATA ドライブとして登録され、既定で /dev/sda のラベルが付けられます。 このディスクの最大容量は 1023 ギガバイトです。 

## <a name="temporary-disk"></a>一時ディスク
各 VM には、一時ディスクが含まれています。 一時ディスクは、アプリケーションとプロセスのための一時的なストレージを提供し、ページ ファイルやスワップ ファイルなどのデータのみを格納するためのものです。 一時ディスクのデータは、[メンテナンス イベント](../virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-planned-vs-unplanned-maintenance)中、または [VM の再デプロイ](../virtual-machines/virtual-machines-linux-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)時に失われる可能性があります。 VM の標準的な再起動中は、一時ドライブのデータは保持されます。

Linux 仮想マシンでは、ディスクは通常 **/dev/sdb** であり、Azure Linux エージェントにより書式設定され、**/mnt** にマウントされます。 仮想マシンのサイズに基づいて、一時ディスクのサイズは異なります。 詳細については、「[Linux 仮想マシンのサイズ](../virtual-machines/virtual-machines-linux-sizes.md)」をご覧ください。

Azure による一時ディスクの使用方法については、「 [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>データ ディスク
データ ディスクは仮想マシンに取り付けられる VHD であり、ユーザーが保存しておく必要があるアプリケーションなどのデータを格納するためのものです。 データ ディスクは SCSI ドライブとして登録され、ユーザーが選択した文字のラベルが付けられます。 各ディスクの最大容量は 1023 ギガバイトです。 仮想マシンのサイズにより、そこに取り付けできるデータ ディスクの数と、ディスクをホストするために使用できるストレージの種類が決まります。

> [!NOTE]
> 仮想マシンの容量の詳細については、「[Linux 仮想マシンのサイズ](../virtual-machines/virtual-machines-linux-sizes.md)」をご覧ください。
> 

ユーザーがイメージから仮想マシンを作成するときに、Azure はオペレーティング システム ディスクを作成します。 ユーザーがデータ ディスクを含むイメージを使用する場合、Azure は仮想マシンの作成時にデータ ディスクも作成します。 それ以外の場合は、仮想マシンを作成した後にデータ ディスクを追加してください。

仮想マシンにディスクを**取り付ける**ことで、ユーザーは仮想マシンにデータ ディスクをいつでも追加できます。 ユーザーがアップロードした VHD やストレージ アカウントにコピーした VHD を使用できます。または Azure が作成する VHD も使用できます。 データ ディスクを取り付けると、VHD ファイルが VM に関連付けられます。VHD で「リース」を設定しておけば、仮想マシンに取り付けている間にストレージから削除することはできなくなります。

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>トラブルシューティング
[!INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>次のステップ
* [ディスクのアタッチ](../virtual-machines/virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) による VM 用のストレージの追加。
* 冗長性を持たせるための[ソフトウェア RAID の構成](../virtual-machines/virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 追加の VM を短時間でデプロイできるようにするための [Linux 仮想マシンのキャプチャ](../virtual-machines/linux/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。


