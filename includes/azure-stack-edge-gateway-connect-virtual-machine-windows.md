---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: e781e3c29a6a22648c6d0596f9fe0a3e425427d7
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500416"
---
VM の作成時に渡した IP 経由でリモート デスクトップ プロトコル (RDP) を使用して、Windows VM に接続します。

1. クライアントで RDP を起動します。 **[スタート]** に移動し、「**mstsc**」と入力します。
1. VM の IP アドレスと、VM テンプレートのパラメーター ファイルで使用したアクセス資格情報を入力します。

    ![RDP 経由で Windows VM に接続する方法を示すスクリーンショット。](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)
1. 信頼されていないコンピューターへの接続の承認が必要となる場合があります。 
1. これで、アプライアンスで実行されている VM にサインインしました。 