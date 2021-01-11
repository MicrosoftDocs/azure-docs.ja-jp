---
title: インクルード ファイル
titleSuffix: Azure
description: インクルード ファイル
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687040"
---
1. Azure Peering Service で有効にするピアリング接続を選択します。 次に、 **[...]** 、 **[接続の編集]** の順に選択します。
    > [!div class="mx-imgBorder"]
    > ![ピアリング接続の [接続の編集]](../media/setup-direct-modify-editconnection.png)
1. **[Use for Peering Service]\(Peering Service に使用\)** の下で、 **[有効]** を選択し、 **[保存]** を選択します。
    > [!div class="mx-imgBorder"]
    > ![ピアリング接続で Peering Service を有効にする](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. **[概要]** 画面にデプロイの詳細が表示されます。 デプロイが完了したら、 **[リソースに移動]** を選択します。
    > [!div class="mx-imgBorder"]
    > ![デプロイが完了しました](../media/setup-direct-modify-overview-deployment-complete.png)

1. **[Registered Prefixes]\(登録されたプレフィックス\)** ウィンドウで **[Add registered prefix]\(登録されたプレフィックスの追加\)** を選択します。
    > [!div class="mx-imgBorder"]
    > ![[Add Registered Prefix]\(登録されたプレフィックスの追加\)](../media/setup-direct-modify-add-registered-prefix.png)
1. **[名前]** と **[プレフィックス]** を選択し、 **[保存]** を選択してプレフィックスを登録します。
    > [!div class="mx-imgBorder"]
    >  ![プレフィックスを登録する](../media/setup-direct-modify-register-a-prefix.png) 

1. プレフィックスが作成されると、 **[Registered Prefixes]\(登録されたプレフィックス\)** の一覧に表示されます。 プレフィックスの **[名前]** を選択すると、詳細が表示されます。
    > [!div class="mx-imgBorder"]
    > ![登録されたプレフィックスと接続](../media/setup-direct-modify-registered-prefixes.png)
1. 登録されたプレフィックスのページで、プレフィックスごとの **[Prefix key]\(プレフィックス キー\)** を含む完全な詳細が表示されます。 このキーは、このプレフィックスが割り当てられた顧客に、そのプロバイダー ISP から提供する必要があります。 顧客は、このキーを使用してサブスクリプション内にプレフィックスを登録できます。
    > [!div class="mx-imgBorder"]
    > ![プレフィックス キーを持つプレフィックス](../media/setup-direct-modify-registered-prefix-detail.png)
