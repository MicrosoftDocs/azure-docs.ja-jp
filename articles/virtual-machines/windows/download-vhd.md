---
title: Azure から Windows VHD をダウンロードする | Microsoft Docs
description: Azure Portal を使用して Windows VHD をダウンロードします。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
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
ms.openlocfilehash: 7128413e48fdeef9b9284bc6db11649016a06153
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722809"
---
# <a name="download-a-windows-vhd-from-azure"></a>Azure から Windows VHD をダウンロードする

この記事では、Azure portal を使用して Azure から Windows 仮想ハード ディスク (VHD) ファイルをダウンロードする方法を説明します。

## <a name="stop-the-vm"></a>VM を停止する

VHD は、実行中の VM に接続されている場合、Azure からダウンロードできません。 VHD をダウンロードするには、VM を停止する必要があります。 VHD を[イメージ](tutorial-custom-images.md)として使用して、新しいディスクで他の VM を作成する場合は、[Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) を使用して、ファイルに含まれているオペレーティング システムを一般化し、VM を停止する必要があります。 VHD を既存の VM の新しいインスタンス用のディスクまたはデータ ディスクとして使用する場合は、実行する必要がある操作は VM の停止と割り当て解除だけです。

VHD をイメージとして使用して他の VM を作成するには、次の手順を実行します。

1.  まだサインインしていない場合は、 [Azure ポータル](https://portal.azure.com/)にサインインします。
2.  [VM に接続します](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
3.  VM で、管理者としてコマンド プロンプト ウィンドウを開きます。
4.  ディレクトリを *%windir%\system32\sysprep* に変更し、sysprep.exe を実行します。
5.  [システム準備ツール] ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、 **[一般化する]** チェック ボックスがオンになっていることを確認します。
6.  [シャットダウン オプション] の **[シャットダウン]** を選択し、 **[OK]** をクリックします。 

VHD を既存の VM の新しいインスタンス用のディスクまたはデータ ディスクとして使用するには、次の手順を実行します。

1.  Azure Portal のハブ メニューで、 **[仮想マシン]** をクリックします。
2.  一覧から VM を選択します。
3.  VM のブレードで、 **[停止]** をクリックします。

    ![VM の停止](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS URL の生成

VHD ファイルをダウンロードするには、[Shared Access Signature (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL を生成する必要があります。 URL が生成されると、その URL に有効期限が割り当てられます。

1.  VM のブレードのメニューで、 **[ディスク]** をクリックします。
2.  VM のオペレーティング システム ディスクを選択し、 **[エクスポート]** をクリックします。
3.  URL の有効期限を *36000* に設定します。
4.  **[URL の生成]** をクリックします。

    ![URL の生成](./media/download-vhd/export-generate.png)

> [!NOTE]
> Windows Server オペレーティング システム用の大きな VHD ファイルをダウンロードするのに十分な時間を確保するために、有効期限を既定値から増やしています。 Windows Server オペレーティング システムを含む VHD ファイルのダウンロードには、接続によっては数時間かかることがあります。 データ ディスク用の VHD をダウンロードする場合は、既定の時間で十分です。 
> 
> 

## <a name="download-vhd"></a>VHD のダウンロード

1.  生成された URL の下にある [VHD ファイルのダウンロード] をクリックします。

    ![VHD のダウンロード](./media/download-vhd/export-download.png)

2.  ブラウザーで **[保存]** をクリックしないと、ダウンロードが開始されない場合があります。 VHD ファイルの既定の名前は *abcd* です。

    ![ブラウザーの [保存] のクリック](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>次の手順

- [VHD ファイルを Azure にアップロードする](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法を学習します。 
- [ストレージ アカウント内の非管理対象ディスクからマネージド ディスクを作成する](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
- [PowerShell を使用した Azure ディスクの管理](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

