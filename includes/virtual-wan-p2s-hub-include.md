---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91812717"
---
1. 仮想 WAN で [ハブ] を選択し、 **[+ 新しいハブ]** を選択します。

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="新しいハブ":::

1. [仮想ハブを作成する] ページで、次のフィールドに入力します。

   * **[リージョン]** - 仮想ハブをデプロイするリージョンを選択します。
   * **[名前]** - 仮想ハブの呼び出しに使う名前を入力します。
   * **[ハブ プライベート アドレス空間]** - CIDR 表記のハブのアドレス範囲。

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="仮想ハブを作成する":::

1. [ポイント対サイト] タブで、次のフィールドを入力します。

   * **[ゲートウェイ スケール ユニット]** - ユーザー VPN ゲートウェイの合計容量を表します。
   * **[ポイント対サイト構成]** - 前の手順で作成した構成です。
   * **[クライアント アドレス プール]** - リモート ユーザーに使用されます。
   * **カスタム DNS サーバーの IP**

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="ポイント対サイトを持つハブ":::

1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **[検証に成功しました]** ページで **[作成]** を選択します。