---
title: "特殊化された VM のコピーを Azure で作成する | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで、Azure で実行されている特殊な Windows VM のコピーを作成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ce7e6cd3-6a4a-4fab-bf66-52f699b1398a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 9d3b8d0e9c13443cf29adb8c94cb817063110ae3
ms.lasthandoff: 03/23/2017


---
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Azure で実行されている特殊な Windows VM のコピーを作成する
この記事では、AZCopy ツールを使用して、Azure で実行されている特殊な Windows VM から VHD のコピーを作成する方法について説明します。 その後、VHD のコピーを使用して新しい VM を作成することができます。 

* 一般化された VM をコピーする場合は、[既存の一般化された Azure VM から VM イメージを作成する方法](virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事を参照してください。
* オンプレミスの VM から VHD をアップロードする場合は、「[Upload a Windows VHD from an on-premises VM to Azure (オンプレミスの VM から Azure への Windows VHD のアップロード)](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

## <a name="before-you-begin"></a>開始する前に
次のことを確認してください。

* **コピー元とコピー先のストレージ アカウント**に関する情報があること。 コピー元の VM については、ストレージ アカウント名とコンテナー名が必要です。 通常、コンテナー名は **vhds** になります。 また、コピー先のストレージ アカウントも持っている必要があります。 まだ持っていない場合は、ポータルを使用する (**[その他のサービス]**、[ストレージ アカウント]、[追加] の順に選択する) か [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) コマンドレットを使用して、作成できます。 
* Azure [PowerShell 1.0](/powershell/azureps-cmdlets-docs) (以降) がインストールされていること。
* [AzCopy ツール](../storage/storage-use-azcopy.md)をダウンロードしてインストール済みであること。 

## <a name="deallocate-the-vm"></a>VM の割り当てを解除する
VM の割り当てを解除して、コピーする VHD を解放します。 

* **Portal**: **[仮想マシン]** > **[myVM]** > [停止] の順にクリックします。
* **Powershell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` は、リソース グループ **myResourceGroup** 内にある **myVM** という名前の VM の割り当てを解除します。

Azure Portal で VM の **[状態]** が **[停止済み]** から **[停止済み (割り当て解除)]** に変わります。

## <a name="get-the-storage-account-urls"></a>ストレージ アカウントの URL を取得する
コピー元とコピー先のストレージ アカウントの URL が必要です。 URL は `https://<storageaccount>.blob.core.windows.net/<containerName>/` のようになります。 ストレージ アカウント名とコンテナー名が既にわかっている場合は、かっこで囲まれた情報を置き換えるだけで、URL を作成できます。 

Azure Portal または Azure PowerShell を使用して URL を取得できます。

* **Portal**: **[その他のサービス]** > **[ストレージ アカウント]** > <storage account> の **[BLOB]** の順にクリックすると、コピー元の VHD ファイルはおそらく **vhds** コンテナー内にあります。 コンテナーの **[プロパティ]** をクリックし、**URL** というラベルのテキストをコピーします。 コピー元およびコピー先の両方のコンテナーの URL が必要です。 
* **Powershell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` は、リソース グループ **myResourceGroup** 内にある **myVM** という名前の VM に関する情報を取得します。 その結果の **Storage profile** セクションで **Vhd Uri** を探します。 URI の最初の部分はコンテナーの URL、最後の部分は VM の OS VHD 名です。

## <a name="get-the-storage-access-keys"></a>ストレージ アクセス キーを取得する
コピー元とコピー先のストレージ アカウントのアクセス キーを探します。 アクセス キーの詳細については、「 [Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。

* **Portal**: **[その他のサービス]** > **[ストレージ アカウント]** > <storage account> の **[すべての設定]** > **[アクセス キー]** の順にクリックします。 **key1**としてラベル付されているキーをコピーします。
* **Powershell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` は、リソース グループ **myResourceGroup** 内にあるストレージ アカウント **mystorageaccount** のストレージ キーを取得します。 **key1**としてラベル付されているキーをコピーします。

## <a name="copy-the-vhd"></a>VHD をコピーする
AzCopy を使用して、ストレージ アカウント間でファイルをコピーすることができます。 コピー先のコンテナーについては、指定されたコンテナーが存在しない場合に作成されます。 

AzCopy を使用するには、ローカル コンピューターでコマンド ウィンドウを開き、AzCopy がインストールされているフォルダーに移動します。 これは、*C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy* のようになります。 

コンテナー内のすべてのファイルをコピーするには、**/S** スイッチを使用します。 OS VHD とすべてのデータ ディスクが同じコンテナーにある場合は、これを使用してそれらをコピーできます。 この例では、**mysourcestorageaccount** ストレージ アカウントのコンテナー **mysourcecontainer** 内にあるすべてのファイルを、**mydestinationstorageaccount** ストレージ アカウントのコンテナー **mydestinationcontainer** にコピーする方法を示します。 ストレージ アカウントとコンテナーの名前は、実際の名前に置き換えてください。 `<sourceStorageAccountKey1>` と `<destinationStorageAccountKey1>` は、実際のキーに置き換えてください。

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

複数のファイルが含まれているコンテナー内にある特定の VHD のみをコピーする場合は、/Pattern スイッチを使用してファイル名を指定することもできます。 この例では、**myFileName.vhd** という名前のファイルのみがコピーされます。

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


完了すると、次のようなメッセージが表示されます。

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>トラブルシューティング
* AZCopy の使用時に、[サーバーが要求の認証に失敗しました] というエラーが発生した場合は、Authorization ヘッダーの値が署名を含む正しい形式であることを確認します。 Key 2 (セカンダリ ストレージ キー) を使用している場合は、プライマリ ストレージ キー (最初のストレージ キー) を使ってみてください。

## <a name="next-steps"></a>次のステップ
* [VHD のコピーを OS ディスクとして VM に接続する](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ことで、新しい VM を作成できます。


