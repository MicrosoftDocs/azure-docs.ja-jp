---
title: Azure から Linux VHD をダウンロードする
description: Azure CLI と Azure Portal を使用して Linux VHD をダウンロードします。
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: b3435d1dabf604cf7a1394c14ee62d65b923714b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565939"
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

- [Azure CLI を使用してカスタム ディスクをアップロードし、Linux VM を作成する](upload-vhd.md)方法を学習します。 
- [Azure CLI を使用した Azure ディスクの管理](tutorial-manage-disks.md)。
