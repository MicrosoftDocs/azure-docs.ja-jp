---
title: Azure から Linux VHD をダウンロードする
description: Azure CLI と Azure Portal を使用して Linux VHD をダウンロードします。
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 897cae53e589f4058e5499c0e6e941d4f1d9bb2f
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87761062"
---
# <a name="download-a-linux-vhd-from-azure"></a>Azure から Linux VHD をダウンロードする

この記事では、Azure portal を使用して Azure から Linux 仮想ハード ディスク (VHD) ファイルをダウンロードする方法を説明します。 

## <a name="stop-the-vm"></a>VM を停止する

VHD は、実行中の VM に接続されている場合、Azure からダウンロードできません。 VHD をダウンロードするには、VM を停止する必要があります。 

1.  [Azure portal](https://portal.azure.com/) にサインインします。
2.  左側のメニューで **[Virtual Machines]** を選択します。
3.  一覧から VM を選択します。
4.  VM のページで、 **[停止]** を選択します。

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="VM を停止するためのメニュー ボタンを示す。":::

## <a name="generate-sas-url"></a>SAS URL の生成

VHD ファイルをダウンロードするには、[Shared Access Signature (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL を生成する必要があります。 URL が生成されると、その URL に有効期限が割り当てられます。

1. VM に対するページのメニューで、 **[ディスク]** を選択します。
2. VM 用のオペレーティング システム ディスクを選択して、 **[ディスクのエクスポート]** を選択します。
1. 必要に応じて、 **[URL の期限が切れるまでの秒数]** の値を更新して、ダウンロードを完了するのに十分な時間を設けます。 既定値は 3600 秒 (1 時間) です。
3. **[URL の生成]** を選択します。
 
      
## <a name="download-vhd"></a>VHD のダウンロード

1.  生成された URL の下にある **[VHD ファイルのダウンロード]** を選択します。

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="VHD をダウンロードするボタンを示す。":::

2.  ブラウザーで **[保存]** を選択しないと、ダウンロードが開始されない場合があります。 VHD ファイルの既定の名前は *abcd* です。

## <a name="next-steps"></a>次のステップ

- [Azure CLI を使用してカスタム ディスクをアップロードし、Linux VM を作成する](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)方法を学習します。 
- [Azure CLI を使用した Azure ディスクの管理](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
