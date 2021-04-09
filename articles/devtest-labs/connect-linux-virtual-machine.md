---
title: Azure DevTest Labs で Linux 仮想マシンに接続する
description: ラボ (Azure DevTest Labs) で Linux 仮想マシンに接続する方法について説明します。
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86528268"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>ラボ (Azure DevTest Labs) で Linux VM に接続する
この記事では、ラボで Linux VM に接続する方法について説明します。 

## <a name="connect-to-a-linux-vm"></a>Linux VM に接続する
1. [Azure portal](https://portal.azure.com) にサインインします。
1. 検索バーで "**DevTest Labs**" を検索して選択します。 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="DevTest Labs の検索と選択":::    
1. ラボの一覧で、目的の **ラボ** を選択します。

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="目的のラボを選択する":::            
1. ラボのホーム ページで、 **[仮想マシン]** の一覧から目的の Linux VM を選択します。 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="目的の Linux VM を選択する":::        
5. **[概要]** ページで、VM の完全修飾ドメイン名 (FQDN) または IP アドレスを確認できます。 次の図に示すように、ポートも確認できます。

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="VM の完全修飾ドメイン名":::    

    VM が起動されているのに **[接続]** ボタンがグレー表示になっていることに留意してください。 これは仕様です。
6.  SSH を使用して Lunux VM に接続します。 次の例では、FQDN `mydtl07172452621450000.eastus.cloudapp.azure.com` の VM にユーザー名 `vmuser` とポート `51637` を使用して接続しています。 VM に接続するためのユーザーのパスワードを入力します。 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    [Putty](https://www.putty.org/) などのツールやその他の SSH クライアントを使用して VM に接続できます。 

    SSH を使用して接続した後、デスクトップ環境 ([xfce](https://www.xfce.org)) とリモート デスクトップ ([xrdp](http://xrdp.org)) をインストールして構成することができます。  詳しくは、「[リモート デスクトップをインストールして Azure の Linux VM に接続するように構成する](../virtual-machines/linux/use-remote-desktop.md)」を参照してください。 

## <a name="next-steps"></a>次の手順
[方法: Windows VM に接続する](connect-windows-virtual-machine.md)
