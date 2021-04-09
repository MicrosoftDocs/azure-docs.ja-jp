---
title: Azure から Windows VHD をダウンロードする
description: Azure Portal を使用して Windows VHD をダウンロードします。
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: a33b248c18bcbf322a1e2d911453a1c4c087e625
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550520"
---
# <a name="download-a-windows-vhd-from-azure"></a>Azure から Windows VHD をダウンロードする

この記事では、Azure portal を使用して Azure から Windows 仮想ハード ディスク (VHD) ファイルをダウンロードする方法を説明します。

## <a name="optional-generalize-the-vm"></a>省略可能:VM の汎用化

他の VM を作成するための[イメージ](tutorial-custom-images.md)として VHD を使用したい場合には、[Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) でオペレーティング システムを一般化する必要があります。 

他の VM を作成するためのイメージとして VHD を使用するには、VM を一般化してください。

1. まだサインインしていない場合は、 [Azure ポータル](https://portal.azure.com/)にサインインします。
2. [VM に接続します](connect-logon.md)。 
3. VM で、管理者としてコマンド プロンプト ウィンドウを開きます。
4. ディレクトリを *%windir%\system32\sysprep* に変更し、sysprep.exe を実行します。
5. [システム準備ツール] ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、 **[一般化する]** チェック ボックスがオンになっていることを確認します。
6. [シャットダウン オプション] の **[シャットダウン]** を選択し、 **[OK]** をクリックします。 


## <a name="stop-the-vm"></a>VM を停止する

VHD は、実行中の VM に接続されている場合、Azure からダウンロードできません。 VHD をダウンロードするには、VM を停止する必要があります。 

1. Azure Portal のハブ メニューで、 **[仮想マシン]** をクリックします。
1. 一覧から VM を選択します。
1. VM のブレードで、 **[停止]** をクリックします。


## <a name="generate-download-url"></a>ダウンロード URL の生成

VHD ファイルをダウンロードするには、[Shared Access Signature (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL を生成する必要があります。 URL が生成されると、その URL に有効期限が割り当てられます。

1. VM のページで、左側のメニューの **[ディスク]** をクリックします。
1. VM のオペレーティング システム ディスクを選択します。
1. ディスクのページで、左側のメニューから **[ディスクのエクスポート]** を選択します。
1. URL の有効期限の規定値は *3600* 秒です。 Windows OS ディスクの場合は、これを **36000** に増やします。
1. **[URL の生成]** をクリックします。

> [!NOTE]
> Windows Server オペレーティング システム用の大きな VHD ファイルをダウンロードするのに十分な時間を確保するために、有効期限を既定値から増やしています。 Windows Server オペレーティング システムを含む VHD ファイルのダウンロードには、接続によっては数時間かかることがあります。 データ ディスク用の VHD をダウンロードする場合は、既定の時間で十分です。 
> 
> 

## <a name="download-vhd"></a>VHD のダウンロード

1. 生成された URL の下にある [VHD ファイルのダウンロード] をクリックします。
1. ダウンロードを開始するために、ブラウザーで **[保存]** をクリックする必要がある場合もあります。 VHD ファイルの既定の名前は *abcd* です。

## <a name="next-steps"></a>次のステップ

- [VHD ファイルを Azure にアップロードする](upload-generalized-managed.md)方法を学習します。 
- [ストレージ アカウント内の非管理対象ディスクからマネージド ディスクを作成する](attach-disk-ps.md)。
- [PowerShell を使用した Azure ディスクの管理](tutorial-manage-data-disk.md)。
