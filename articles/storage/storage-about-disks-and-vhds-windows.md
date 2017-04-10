---
title: "Microsoft Azure Windows VM 用のディスクと VHD について | Microsoft Docs"
description: "Azure での Windows 仮想マシン用のディスクと VHD の基本について説明します。"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 0142c64d-5e8c-4d62-aa6f-06d6261f485a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: fced31b28b4b8e5835033243719e6eb87aa3f0d4
ms.lasthandoff: 03/24/2017


---
# <a name="about-disks-and-vhds-for-azure-windows-vms"></a>Azure Windows VM 用のディスクと VHD について
Azure の仮想マシンでは、その他のコンピューターとまったく同じように、オペレーティング システム、アプリケーション、およびデータを格納するための場所としてディスクを使用します。 Azure のすべての仮想マシンには、Windows オペレーティング システム ディスクと一時ディスクの少なくとも 2 つのディスクがあります。 オペレーティング システム ディスクはイメージから作成されます。オペレーティング システム ディスクとイメージの両方は仮想ハード ディスク (VHD) であり、Azure のストレージ アカウントに格納されます。 仮想マシンでは 1 つ以上のデータ ディスクも保持することができ、これらも VHD として格納されます。 

この記事では、ディスクのさまざまな仕様法王について説明し、作成および使用できるディスクの種類を示します。 この記事は、 [Linux 仮想マシン](storage-about-disks-and-vhds-linux.md)にも利用できます。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>VM で使用されるディスク

ディスクは VM でどのように使用されるのでしょうか。

### <a name="operating-system-disk"></a>オペレーティング システム ディスク
どの仮想マシンにも 1 つのオペレーティング システム ディスクが取り付けられています。 これは SATA ドライブとして登録され、C ドライブのラベルが付けられます。 このディスクの最大容量は 1023 ギガバイトです。 

### <a name="temporary-disk"></a>一時ディスク
各 VM には、一時ディスクが含まれています。 一時ディスクは、アプリケーションとプロセスのための一時的なストレージを提供し、ページ ファイルやスワップ ファイルなどのデータのみを格納するためのものです。 一時ディスクのデータは、[メンテナンス イベント](../virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-planned-vs-unplanned-maintenance)中、または [VM の再デプロイ](../virtual-machines/virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)時に失われる可能性があります。 VM の標準的な再起動中は、一時ドライブのデータは保持されます。

この一時ディスクには D ドライブのラベルが付けられ、pagefile.sys を保存するために使用されます。 このディスクを再マッピングするには、 [Windows 一時ディスクのドライブ文字を変更する方法](../virtual-machines/virtual-machines-windows-change-drive-letter.md)に関するページを参照してください。 仮想マシンのサイズに基づいて、一時ディスクのサイズは異なります。 詳細については、「 [Azure の仮想マシンのサイズ](../virtual-machines/virtual-machines-windows-sizes.md)」を参照してください。

Azure による一時ディスクの使用方法については、「 [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)


### <a name="data-disk"></a>データ ディスク
データ ディスクは仮想マシンに取り付けられる VHD であり、ユーザーが保存しておく必要があるアプリケーションなどのデータを格納するためのものです。 データ ディスクは SCSI ドライブとして登録され、ユーザーが選択した文字のラベルが付けられます。 各ディスクの最大容量は 1023 ギガバイトです。 仮想マシンのサイズにより、そこに取り付けできるデータ ディスクの数と、ディスクをホストするために使用できるストレージの種類が決まります。

> [!NOTE]
> 仮想マシンの容量については、「 [Azure の Windows 仮想マシンのサイズ](../virtual-machines/virtual-machines-windows-sizes.md)」を参照してください。
> 

ユーザーがイメージから仮想マシンを作成するときに、Azure はオペレーティング システム ディスクを作成します。 ユーザーがデータ ディスクを含むイメージを使用する場合、Azure は仮想マシンの作成時にデータ ディスクも作成します。 それ以外の場合は、仮想マシンを作成した後にデータ ディスクを追加してください。

仮想マシンにディスクを**取り付ける**ことで、ユーザーは仮想マシンにデータ ディスクをいつでも追加できます。 ユーザーがアップロードした VHD やストレージ アカウントにコピーした VHD を使用できます。または Azure が作成する VHD も使用できます。 データ ディスクを取り付けると、VHD ファイルが VM に関連付けられます。VHD で「リース」を設定しておけば、仮想マシンに取り付けている間にストレージから削除することはできなくなります。


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>最後の 1 つの推奨事項: Standard非管理ディスクでの TRIM の使用 

Standard 非管理ディスク (HDD) を使用する場合は、TRIM を有効にする必要があります。 TRIM はディスク上の未使用のブロックを破棄するため、実際に使用しているストレージにのみ課金されます。 これにより、サイズの大きいファイルを作成した後に削除した場合、コストを節約できます。 

次のコマンドを実行すると、TRIM の設定を確認できます。 Windows VM 上でコマンド プロンプトを開いて、次のように入力します。

```
fsutil behavior query DisableDeleteNotify
```

このコマンドが 0 を返す場合、TRIM は適切に有効化されています。 1 が返される場合は、次のコマンドを実行して TRIM を有効にします。

```
fsutil behavior set DisableDeleteNotify 0
```

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>次のステップ
* [ディスクのアタッチ](../virtual-machines/virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) による VM 用のストレージの追加。
* [Windows VM イメージを Azure にアップロード](../virtual-machines/virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) します。
* [Windows 一時ディスクのドライブ文字を変更する方法](../virtual-machines/virtual-machines-windows-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) します。


