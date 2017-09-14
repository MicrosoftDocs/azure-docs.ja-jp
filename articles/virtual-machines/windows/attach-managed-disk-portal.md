---
title: "管理対象のデータ ディスクを Windows VM に接続する - Azure | Microsoft Docs"
description: "Azure Portal でリソース マネージャー デプロイ モデルを使用して、新規の管理対象データ ディスクを Windows VM に接続する方法。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: f0cf88a06c5470ef173b22e7213419a6c8760723
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Azure Portal で Windows VM に管理対象のデータ ディスクを接続する方法

この記事では、Azure Portal を使用して新規の管理対象データ ディスクを Windows 仮想マシンに接続する方法について示します。 接続する前に、次のヒントを確認してください。

* 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](sizes.md)」を参照してください。
* 新しいディスクの場合、接続時に Azure で自動的に作成されるので、最初に作成する必要はありません。

[Powershell を使用してデータ ディスクを接続する](attach-disk-ps.md)こともできます。



## <a name="add-a-data-disk"></a>データ ディスクの追加
1. 左側のメニューの **[仮想マシン]** をクリックします。
2. 一覧から仮想マシンを選択します。
3. 仮想マシンのブレードで、**[ディスク]** をクリックします。
   4. **[ディスク]** ブレードで、**[+ Add data disk]** (データ ディスクの追加) をクリックします。
5. 新しいディスクのドロップダウン リストで **[空アイテムの作成]** を選択します。
6. **[管理ディスクの作成]** ブレードで、ディスクの名前を入力し、必要に応じてその他の設定を調整します。 完了したら、**[作成]** をクリックします。
7. **[ディスク]** ブレードで [保存] をクリックして、VM の新しいディスク構成を保存します。
6. Azure でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]**に新しいディスクが表示されます。


## <a name="initialize-a-new-data-disk"></a>新しいデータ ディスクの初期化

1. VM に接続します。
1. VM でスタート メニューをクリックし、「**diskmgmt.msc**」と入力して **Enter** キーを押します。 ディスクの管理スナップインが開始されます。
2. 新しい未初期化のディスクがディスクの管理によって認識され、[ディスクの初期化] ウィンドウが表示されます。
3. 新しいディスクが選択されていることを確認し、**[OK]** をクリックしてディスクを初期化します。
4. 新しいディスクが、**未割り当て**として表示されます。 ディスクの任意の場所を右クリックし、**[新しいシンプル ボリューム]** を選択します。 **新しいシンプル ボリューム ウィザード**が起動します。
5. すべての設定を既定値のままウィザードを進め、完了したら **[完了]** を選択します。
6. ディスクの管理を閉じます。
7. 新しいディスクを使用する前にフォーマットする必要があることを知らせるポップアップが表示されます。 **[ディスクのフォーマット]** をクリックします。
8. **[新しいディスクのフォーマット]** ダイアログで設定を確認し、**[開始]** をクリックします。
9. ディスクをフォーマットするとデータがすべて削除されることを警告するメッセージが表示されます。**[OK]** をクリックします。
10. フォーマットが完了したら、**[OK]** をクリックします。

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

ボリュームのフォーマットによって、ボリューム全体を確実に削除することもできます。

## <a name="next-steps"></a>次のステップ
アプリケーションで D: ドライブを使用してデータを保存する必要がある場合は、 [Windows 一時ディスクのドライブ文字を変更](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)できます。

