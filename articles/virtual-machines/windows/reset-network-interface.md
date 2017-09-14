---
title: "Azure Windows VM のネットワーク インターフェイスをリセットする方法 | Microsoft Docs"
description: "Azure Windows VM のネットワーク インターフェイスをリセットする方法を示します"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 220e426be20086841854d89831f6c9d67529867f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017


---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Azure Windows VM のネットワーク インターフェイスをリセットする方法 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

既定のネットワーク インターフェイス (NIC) を無効にするか、NIC の静的 IP を手動で設定すると、Microsoft Azure Windows 仮想マシン (VM) に接続できなくなります。 この記事では、Azure Windows VM のネットワーク インターフェイスをリセットする方法を示します。リセットすることで、リモート接続の問題が解決します。

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>ネットワーク インターフェイスをリセットする

### <a name="for-classic-vms"></a>クラシック VM の場合

ネットワーク インターフェイスをリセットするには、次の手順に従います。

1.  [Azure ポータル]( https://ms.portal.azure.com)にアクセスします。
2.  **[仮想マシン (クラシック)]** を選択します。
3.  影響を受ける仮想マシンを選択します。
4.  **[IP アドレス]** を選択します。
5.  **[プライベート IP の割り当て]** が **[静的]** になっていない場合は、**[静的]** に変更します。
6.  **[IP アドレス]** を、サブネットで使用できる別の IP アドレスに変更します。
7.  [保存] を選択します。
8.  仮想マシンが再起動して、新しい NIC をシステムに初期化します。
9.  マシンへの RDP を試します。 成功した場合は、いつでも元のプライベート IP アドレスに戻すことができます。 それ以外の場合は、そのまま保持できます。 

### <a name="for-vms-deployed-in-resource-group-model"></a>リソース グループ モデルでデプロイされた VM の場合

1.  [Azure ポータル]( https://ms.portal.azure.com)にアクセスします。
2.  影響を受ける仮想マシンを選択します。
3.  **[ネットワーク インターフェイス]** を選択します。
4.  コンピューターに関連付けられているネットワーク インターフェイスを選択します。
5.  **[IP 構成]** を選択します。
6.  IP を選択します。 
7.  **[プライベート IP の割り当て]** が **[静的]** になっていない場合は、**[静的]** に変更します。
8.  **[IP アドレス]** を、サブネットで使用できる別の IP アドレスに変更します。
9. 仮想マシンが再起動して、新しい NIC をシステムに初期化します。
10. マシンへの RDP を試します。 成功した場合は、いつでも元のプライベート IP アドレスに戻すことができます。 それ以外の場合は、そのまま保持できます。 

## <a name="delete-the-unavailable-nics"></a>使用できない NIC を削除する
コンピューターにリモート デスクトップ接続できたら、潜在的な問題を回避するために古い NIC を削除する必要があります。

1.  デバイス マネージャーを開きます。
2.  **[表示]** > **[非表示のデバイスの表示]** を選択します。
3.  **[ネットワーク アダプター]** を選択します。 
4.  "Microsoft Hyper-V Network Adapter" という名前のアダプターを確認します。
5.  使用できないアダプターは淡色表示されることがあります。 アダプターを右クリックし、[アンインストール] を選択します。

    ![NIC の図](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > 名前が "Microsoft Hyper-V Network Adapter" である使用できないアダプターのみをアンインストールしてください。 その他の非表示のアダプターをアンインストールすると、別の問題が発生する可能性があります。
    >
    >

6.  これで、すべての使用できないアダプターがシステムから取り除かれます。
