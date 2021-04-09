---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 6e42fef2aa4415373ed0bac39284f36ba330a4d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730649"
---
VM の作成時に渡した IP 経由でリモート デスクトップ プロトコル (RDP) を使用して、Windows VM に接続します。

1. お使いのクライアントで、RDP を開きます。 
1. **[スタート]** に移動し、「**mstsc**」と入力します。
1. **[リモート デスクトップ接続]** ウィンドウで、VM テンプレート パラメーター ファイルで使用した VM の IP アドレスとアクセス資格情報を入力し、 **[接続]** を選択します。

   ![RDP 経由で Windows VM に接続するための [リモート デスクトップ接続] ウィンドウのスクリーンショット。](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > 信頼されていないコンピューターへの接続の承認が必要となる場合があります。 

これで、アプライアンスで実行されている VM にサインインしました。 
