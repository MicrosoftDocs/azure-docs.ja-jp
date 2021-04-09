---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "73491528"
---
1. 作成した仮想 WAN を探します。 [仮想 WAN] ページの **[接続]** セクションで、 **[ハブ]** を選択します。
2. [ハブ] ページで、 **[+ 新しいハブ]** を選択して **[仮想ハブを作成する]** ページを開きます。
3. **[仮想ハブを作成する]** ページの **[基本]** タブで、次のフィールドを入力します。

   ![基本操作](./media/virtual-wan-tutorial-er-hub-include/hub1.png "基本")

    **プロジェクトの詳細**

   * リージョン (旧称は場所)
   * 名前
   * ハブのプライベート アドレス空間。 ハブを作成するための最小アドレス空間は /24 です。これは、/25 から /32 のいずれの範囲でも、作成中にエラーが生成されることを意味します。
4. **[ExpressRoute]** タブを選択します。

5. **[ExpressRoute]** タブで、次のフィールドを入力します。

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * **[はい]** を選択すると、**ExpressRoute** ゲートウェイが作成されます。
   * ドロップダウンから **[ゲートウェイ スケール ユニット]** 値を選択します。
6. **[確認と作成]** を選択して検証します。
7. **[作成]** を選択してハブを作成します。 30 分後に **[更新]** を実行して、 **[ハブ]** ページでハブを確認します。 **[リソースに移動]** を選択してリソースに移動します。