---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 91dc6ba0bcd455aefe9de2efdff2feb20938152d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376389"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>ステップ 1: 仮想ネットワーク ゲートウェイへの移動

1. [Azure Portal](https://portal.azure.com) で **[すべてのリソース]** に移動します。 
2. 仮想ネットワーク ゲートウェイ ページを開くには、仮想ネットワーク ゲートウェイに移動し、クリックして選択します。 

### <a name="step-2-delete-connections"></a>ステップ 2: 接続の削除

1. 仮想ネットワーク ゲートウェイのページで、 **[接続]** をクリックしてゲートウェイへのすべての接続を表示します。
2. 接続名の行で **[...]** をクリックして、ドロップダウンから **[削除]** を選択します。
3. 確認画面で **[はい]** をクリックして、接続を削除します。 複数の接続がある場合は、接続を一つずつ削除します。

### <a name="step-3-delete-the-virtual-network-gateway"></a>ステップ 3: 仮想ネットワーク ゲートウェイの削除

S2S 構成に加えてこの VNet に対する P2S 構成がある場合は、仮想ネットワーク ゲートウェイを削除すると、警告なしにすべての P2S クライアントが自動的に切断されることに注意してください。

1. 仮想ネットワーク ゲートウェイのページで、 **[概要]** をクリックします。
2. **[概要]** ページで、 **[削除]** をクリックしてゲートウェイを削除します。
