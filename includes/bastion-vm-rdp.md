---
title: インクルード ファイル
description: インクルード ファイル
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 083ab61d5a20bfb8e38747ae0694b1176c0a0fd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521537"
---
1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** を選択します。 ドロップダウンから **[Bastion]** を選択します。

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="[Bastion] を選択する":::

1. ドロップダウンから [Bastion] を選択すると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされていたため、[Bastion] タブが既定でアクティブになっています。 **[Bastion を使用する]** を選択します。

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="[Bastion を使用する] を選択する":::

1. **[Azure Bastion を使用して接続する]** ページで、仮想マシン スケール セットのユーザー名とパスワードを入力し、 **[接続]** を選択します。

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="のインスタンスに接続するときには、":::

1. Bastion を使用したこの仮想マシンへの RDP 接続は、ポート 443 と Bastion サービスを使用して (HTML5 を介して) Azure portal で直接開きます。

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="ポート 443 を使用して接続する":::
