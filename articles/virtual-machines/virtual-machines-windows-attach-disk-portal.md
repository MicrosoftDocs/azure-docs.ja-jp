---
title: "データ ディスクを Windows VM に接続する | Microsoft Docs"
description: "Azure ポータルでリソース マネージャー デプロイ モデルを使用して、新規または既存のデータ ディスクを Windows VM に接続する方法。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3790fc59-7264-41df-b7a3-8d1226799885
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 88956ec33009bfd6ce5047085f6d7512d951edee
ms.lasthandoff: 03/25/2017


---
# <a name="how-to-attach-a-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Azure ポータルで Windows VM にデータ ディスクを接続する方法
この記事では、Azure ポータルを使用して新しいディスクと既存のディスクの両方を Windows 仮想マシンに接続する方法について示します。 [Azure ポータルで Linux VM にデータ ディスクを接続する](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)こともできます。 接続する前に、次のヒントを確認してください。

* 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
* Premium Storage を使用するには、DS シリーズまたは GS シリーズの仮想マシンが必要です。 これらの仮想マシンでは、Premium および Standard のストレージ アカウントのディスクを使用できます。 Premium Storage は特定のリージョンで使用できます。 詳細については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
* 新しいディスクの場合、接続時に Azure で自動的に作成されるので、最初に作成する必要はありません。
* 既存のディスクの場合、Azure ストレージ アカウントで .vhd ファイルを使用できる必要があります。 別の仮想マシンに接続されていない場合は既存の .vhd を使用できます。そうでなければ、独自の .vhd ファイルをストレージ アカウントにアップロードできます。

[Powershell を使用してデータ ディスクを接続する](virtual-machines-windows-attach-disk-ps.md)こともできます。



## <a name="find-the-virtual-machine"></a>仮想マシンの検索
1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. ハブ メニューで **[Virtual Machines]**をクリックします。
3. 一覧から仮想マシンを選択します。
4. [仮想マシン] ブレードにアクセスし、**[要点]** で、**[ディスク]** の順にクリックします。
   
    ![ディスク設定を開く](./media/virtual-machines-windows-attach-disk-portal/find-disk-settings.png)

次の手順に従って、[新しいディスク](#option-1-attach-a-new-disk)または[既存のディスク](#option-2-attach-an-existing-disk)を接続します。

## <a name="option-1-attach-and-initialize-a-new-disk"></a>オプション 1: 新しいディスクを接続し、初期化する
1. **[ディスク]** ブレードの **[新しいディスクの接続]** をクリックします。
2. 既定の設定を確認し、必要に応じて更新して、 **[OK]**をクリックします。
   
   ![ディスク設定を確認する](./media/virtual-machines-windows-attach-disk-portal/attach-new.png)
3. Azure でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]**に新しいディスクが表示されます。

### <a name="initialize-a-new-data-disk"></a>新しいデータ ディスクの初期化

1. 仮想マシンへの接続 手順については、「 [Windows が実行されている Azure 仮想マシンに接続してログオンする方法](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。
2. 仮想マシンにログオンした後、 **サーバー マネージャー**を開きます。 左側のウィンドウで、 **[ファイル サービスと記憶域サービス]**を選択します。
   
    ![サーバー マネージャーを開く](./windows/classic/media/attach-disk/fileandstorageservices.png)
3. メニューを展開し、 **[ディスク]**を選択します。
4. **[ディスク]** セクションにディスクが一覧表示されます。 普通、disk 0、disk 1、disk 2 といった名前です。 disk 0 はオペレーティング システム ディスク、disk 1 は一時ディスク、disk 2 は VM にアタッチしたデータ ディスクです。 新しいデータ ディスクのパーティションは **[不明]**と表示されます。 ディスクを右クリックし、 **[初期化]**を選択します。
5. ディスクの初期化時にすべてのデータが消去されることが通知されます。 **[はい]** をクリックして警告を了解し、ディスクを初期化します。 完了すると、パーティションは **[GPT]**と表示されます。 もう一度ディスクを右クリックし、 **[新しいボリューム]**を選択します。
6. 既定の値を使用してウィザードを完了します。 ウィザードが終了すると、 **[ボリューム]** セクションに新しいボリュームが表示されます。 ディスクがオンラインになり、データを格納できるようになります。

    ![ボリュームの初期化に成功](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)


## <a name="option-2-attach-an-existing-disk"></a>オプション 2: 既存のディスクを接続する
1. **[ディスク]** ブレードの **[既存のディスクの接続]** をクリックします。
2. **[既存のディスクの接続]** の **[VHD ファイル]** をクリックします。
   
   ![既存のディスクを接続する](./media/virtual-machines-windows-attach-disk-portal/attach-existing.png)
3. **[ストレージ アカウント]**で、アカウントと、.vhd ファイルが格納されているコンテナーを選択します。
   
   ![VHD の場所を検索する](./media/virtual-machines-windows-attach-disk-portal/find-storage-container.png)
4. .vhd ファイルを選択します。
5. **[既存のディスクの接続]** の **[VHD ファイル]** に、選択したファイルが表示されます。 **[OK]**をクリックします。
6. Azure で仮想マシンにディスクが接続されると、仮想マシンのディスク設定の **[データ ディスク]**にそのディスクが表示されます。



## <a name="use-trim-with-standard-storage"></a>Standard Storage での TRIM の使用

Standard Storage (HDD) を使用する場合は、TRIM を有効にする必要があります。 TRIM はディスク上の未使用のブロックを破棄するため、実際に使用しているストレージにのみ課金されます。 これにより、サイズの大きいファイルを作成した後に削除した場合、コストを節約できます。 

次のコマンドを実行すると、TRIM の設定を確認できます。 Windows VM 上でコマンド プロンプトを開いて、次のように入力します。

```
fsutil behavior query DisableDeleteNotify
```

このコマンドが 0 を返す場合、TRIM は適切に有効化されています。 1 が返される場合は、次のコマンドを実行して TRIM を有効にします。
```
fsutil behavior set DisableDeleteNotify 0
```
                
ディスクからデータを削除した後は、TRIM でデフラグを実行して、TRIM 操作が正しくフラッシュすることを確認できます。

```
defrag.exe <volume:> -l
```

## <a name="next-steps"></a>次のステップ
アプリケーションで D: ドライブを使用してデータを保存する必要がある場合は、 [Windows 一時ディスクのドライブ文字を変更](virtual-machines-windows-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)できます。


